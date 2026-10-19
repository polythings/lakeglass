---
number: 2
title: Architecture
area: Foundations
status: planned
updated: 2026-10-19
related: [3, 4, 5, 6, 7, 8, 10]
issues: [1, 4, 6]
---

# 2. Architecture

**Planned** · updated 2026-10-19 · related: [spec 3](0003-stores.md), [spec 4](0004-detection.md),
[spec 5](0005-file-formats.md), [spec 6](0006-table-formats.md), [spec 7](0007-engines.md),
[spec 8](0008-query-trace.md), [spec 10](0010-interface.md)

## Summary

A Rust core reads stores, detects formats, parses files and table metadata, runs checks, and drives query engines.
One binary, `lakeglass`, serves a web interface on the user's machine. Three traits (file format, table format,
engine) are the extension points; each implementation is its own crate. Everything is read-only, and every
request to the store goes through one counting layer.

## Design

### Layers

```
            ┌────────────── web UI (TypeScript, React) ──────────────┐
            │  store map · table · file anatomy · SQL · trace · checks │
            └───────────────────────────┬─────────────────────────────┘
                                        │ HTTP + Arrow IPC (localhost)
┌───────────────────────────────────────┴──────────────────────────────────────┐
│ lakeglass server: sessions, cache, settings, API                              │
│   detect ──→ table formats ──→ file formats        checks        engines      │
│      │            │                 │                 │              │        │
│      └────────────┴───── counting store (ranged reads, cache) ──────┘        │
└───────────────────────────────────────┬──────────────────────────────────────┘
                                        │ S3 / GCS / Azure / local
                                   object store
```

- **Counting store:** every LIST, HEAD, and GET (with its byte range) from any part of lakeglass, including embedded
  engines, is recorded and attributed to the view or query that caused it (spec 3, spec 8).
- **Arrow** is the common format between readers, engines, and the UI: rows travel to the browser as Arrow IPC.

### Extension points

| Trait | Does | Implemented by (planned) |
|-------|------|--------------------------|
| `FileFormat` | Recognize a file from its bytes and name; read its anatomy (spec 5); read rows as Arrow, with projection and a row limit | Parquet, Arrow IPC, ORC, Avro, Vortex, Lance, Puffin, SlateDB SST, RocksDB SST, CSV and JSON |
| `TableFormat` | Recognize a table from a listing (spec 4); list snapshots; read schema history, partitioning, and the metadata tree; list data and delete files with their statistics; plan which files a predicate needs (spec 8) | Iceberg, Delta Lake, Hudi, Paimon, Lance, DuckLake, Hive-style directories, Fluss remote storage, SlateDB |
| `Engine` | Say which formats it reads; register a table or files; run SQL and return Arrow; return its plan and metrics (spec 7) | DataFusion, DuckDB, Polars; remote: Flight SQL, Trino, Spark Connect |

A crate registers its implementation in a registry; the server and the UI list what is registered, so adding a
format or engine changes no other crate. Each trait has a conformance test suite every implementation runs against
fixtures.

### Crates

| Crate | Contents |
|-------|----------|
| `lakeglass` | Binary: CLI, server, embedded UI |
| `lakeglass-core` | Traits, registry, shared types (store map, anatomy, trace, finding) |
| `lakeglass-store` | Store connections, counting layer, cache (spec 3) |
| `lakeglass-detect` | Detection (spec 4) |
| `lakeglass-parquet`, `lakeglass-vortex`, … | One crate per file format (spec 5) |
| `lakeglass-iceberg`, `lakeglass-delta`, … | One crate per table format (spec 6) |
| `lakeglass-datafusion`, `lakeglass-duckdb`, … | One crate per engine (spec 7) |
| `lakeglass-checks` | Checks (spec 9) |
| `ui/` | The web UI (spec 10) |

Crates are added as they're built.

### Run modes

- **Local server** (first): `lakeglass` or `lakeglass s3://bucket/prefix` starts the server on 127.0.0.1 and opens
  the browser. Credentials come from the environment and the usual provider chains (spec 12).
- **Browser only** (later): the detect, file-format, and table-format crates compile to WebAssembly for public
  buckets, presigned URLs, and dropped local files. No server; engines limited to what runs in WASM.

[#1](https://github.com/polythings/lakeglass/issues/1) decides which mode comes first and whether a desktop app or a
shared team server follows.

## Decisions

| ID | Decision | Status |
|---|---|---|
| D4 | Read-only: nothing in lakeglass writes, deletes, or rewrites objects in a store. A check shows the fix (SQL or a command for the user's engine); it never runs it. | Proposed |
| D5 | Rust for the core, as libraries. The same detect and format crates compile to WebAssembly later. | Proposed |
| D6 | Runs on the user's machine: one binary serves the UI on 127.0.0.1. No hosted service holds credentials or data. | Proposed |
| D7 | Three traits, `FileFormat`, `TableFormat`, and `Engine`, with one crate per implementation and a registry. | Proposed |
| D8 | Arrow in memory and on the wire between the server and the UI. | Proposed |
| D9 | One counting store under every reader and embedded engine, so each view and query knows its requests and bytes. | Proposed |

## Open questions and issues

- [#1](https://github.com/polythings/lakeglass/issues/1) How lakeglass runs first.
- [#4](https://github.com/polythings/lakeglass/issues/4) Read-only for good?
- [#6](https://github.com/polythings/lakeglass/issues/6) `object_store` or OpenDAL.

## History

- 2026-10-19: proposed.
