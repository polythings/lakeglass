---
title: Architecture
updated: 2026-10-19
---

# Architecture

## Summary

One Next.js interface and one Rust core. The core runs in two places: compiled to WebAssembly in a browser worker, or
natively in the Mac app and the CLI. The interface talks to whichever backend it has through one TypeScript
interface, so every view works in all three apps. Three Rust traits (file format, table format, engine) are the
extension points, each implementation in its own crate, and every request to a store goes through one counting
layer.

## Design

### Pieces

```
                    Next.js interface (static export)
        ┌──────────────────────┼───────────────────────┐
   browser (spec 13)      Mac app (spec 14)        CLI (spec 15)
        │                      │ Tauri commands         │ HTTP on localhost
   Web Worker:                 └──────────┬─────────────┘
   lakeglass-wasm                  native lakeglass core
   + DuckDB-wasm                   + native engines
        │                                 │
        └──── counting store (object_store) ┘
                          │
        S3 · GCS · Azure · R2 · Tigris · MinIO · local files
```

- **Interface:** Next.js with `output: 'export'`, so the same static files serve the website, load inside the Tauri
  app, and are embedded in the CLI binary. Static export rules out route handlers, server actions, and
  server-rendered dynamic routes; the app routes on the client (`/t?store=…&path=…`). TypeScript types for the
  backend API are generated from the Rust types.
- **Backend interface:** one TypeScript interface (`list`, `detect`, `anatomy`, `rows`, `table`, `query`, `trace`,
  `checks`, `write`) with three implementations: the WASM worker, Tauri `invoke`, and HTTP to the CLI. A view never
  knows which one it has; it asks the backend what it supports (engines, formats, write mode).
- **Arrow** carries rows between the core, the engines, and the interface (Arrow IPC buffers).

### What runs where

Checked on 2026-10-19 by compiling each crate for `wasm32-unknown-unknown`:

| Part | Browser (WASM) | Native |
|------|----------------|--------|
| Store access | `object_store` 0.14 with the `aws-base`, `gcp-base`, `azure-base`, and `http-base` features and `ring` for wasm; requests are subject to CORS (spec 13) | `object_store` with full features; credential chains |
| Parquet, Arrow IPC, Avro, ORC, Vortex | arrow-rs, parquet, apache-avro, orc-rust, vortex: compile | Same crates |
| Iceberg, Delta, Hudi, Paimon, Lance, SlateDB metadata | [lakebed](https://github.com/polythings/lakebed)'s format crates (Iceberg built; the others added there as lakeglass needs them), which do no I/O and build for wasm32. The official Rust crates (iceberg, deltalake, delta_kernel, hudi, lance, slatedb) don't compile to wasm32 | The same lakebed crates for exploring; the official crates where an engine needs them |
| Engines | DataFusion 55 (single-threaded); DuckDB-wasm (Parquet, Iceberg, DuckLake; no Delta extension in WASM) | DataFusion with table providers, native DuckDB, others (spec 8) |
| Writes (spec 11) | Plain object operations; Iceberg table operations through lakebed's commit | Plain object operations; table operations through lakebed (Iceberg) or the official crates (the rest, until lakebed has them) |

Reading table metadata with our own code is a deliberate choice, not only a WASM workaround: an explorer has to show
each metadata file as it is, including files the official libraries would reject or hide. That code is lakebed, a
sibling project shared with Timelake: one crate per format on a `Host` trait that lakeglass implements over its counted
store (spec 2). lakebed shows each format as it is (metadata JSON kept whole, manifests read field by field, unknown
types kept by name); lakeglass's `TableFormat` crates adapt it to the table model, and file anatomy (spec 5) stays
here.

### Extension points

| Trait | Does |
|-------|------|
| `FileFormat` | Recognize a file from its bytes and name; read its anatomy (spec 5); read rows as Arrow with projection and a limit |
| `TableFormat` | Recognize a table from a listing (spec 3); list snapshots; read schema history, partitioning, and the metadata tree; list data and delete files with statistics; plan which files a filter needs (spec 9) |
| `Engine` | Say what it reads; register tables and files; run SQL and return Arrow; return its plan and metrics (spec 8) |

A crate registers its implementation in a registry, and declares whether it builds for WASM. Each trait has a
conformance test suite that every implementation runs against fixtures.

### Crates and packages

| Package | Contents |
|---------|----------|
| `lakeglass-core` | Traits, registry, shared types (store map, anatomy, table model, trace, finding) |
| `lakeglass-store` | Connections, the counting layer, the cache (specs 1, 2) |
| `lakeglass-detect` | Detection (spec 3) |
| `lakeglass-parquet`, … | One crate per file format |
| `lakeglass-iceberg`, … | One crate per table format: the `TableFormat` trait over lakebed's crate for it (`lakebed-iceberg`, …) |
| `lakeglass-datafusion`, `lakeglass-duckdb`, … | One crate per engine |
| `lakeglass-checks` | Checks (spec 10) |
| `lakeglass-wasm` | The browser backend (wasm-bindgen) |
| `lakeglass` | The CLI and local server (spec 15) |
| `app/` | The Next.js interface; `app/src-tauri` wraps it as the Mac app (spec 14) |

Crates are added as they're built.

## Decisions

| ID | Decision | Status |
|---|---|---|
| D6 | Browser first: the interface is a Next.js static export and the core runs as WebAssembly in a worker; the Mac app and CLI run the same core natively. | Accepted |
| D7 | Store access through `object_store`; OpenDAL may be added later behind the same counting layer ([#6](https://github.com/polythings/lakeglass/issues/6)). | Accepted |
| D8 | lakeglass reads (and in write mode writes) table metadata with [lakebed](https://github.com/polythings/lakebed)'s format crates, a sibling project shared with Timelake: no I/O of their own, built for WASM, each format shown as it is. Formats lakeglass needs are added there. | Accepted |
| D9 | One TypeScript backend interface with WASM, Tauri, and HTTP implementations. | Proposed |
| D10 | Three Rust traits, `FileFormat`, `TableFormat`, and `Engine`, with one crate per implementation and a registry. | Proposed |
| D11 | Arrow in memory and between the core and the interface. | Proposed |

## History

- 2026-10-19: proposed as a local server with an embedded UI; changed the same day to browser first with Next.js
  after [#1](https://github.com/polythings/lakeglass/issues/1) and [#7](https://github.com/polythings/lakeglass/issues/7).
- 2026-10-20: table format readers and writers come from lakebed, shared with Timelake (D8 accepted).
