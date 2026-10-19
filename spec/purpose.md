---
title: Purpose and requirements
updated: 2026-10-19
---

# Purpose and requirements

## Summary

lakeglass is an open-source (Apache-2.0) app for looking inside object storage. It connects to a bucket, finds the
tables and files stored there, and shows them: tables, metadata files, data files down to their pages, query results
from pluggable engines, how a query reads a table, and what could be stored better. It runs in the browser, as a Mac
app, and from the command line. This page holds the requirements as stated, the goals and non-goals, and the terms the
specs use.

## Design

### Requirements (as stated)

| ID | Requirement |
|----|-------------|
| R1 | An Apache-2.0 dashboard for exploring data warehouses, data lakes, and lakehouses. |
| R2 | Connect to an object store and detect what is stored there. |
| R3 | Explore plain object stores too, with no table format. |
| R4 | Explore tables, see their data, and query them. |
| R5 | See metadata files. |
| R6 | See data files and understand them: Parquet, Vortex, SSTables, Puffin, and others. |
| R7 | Visuals that explain how such systems are structured. |
| R8 | Detect and show ways the data could be optimized. |
| R9 | Visual explanations of how queries run against specific tables. |
| R10 | See tables. |
| R11 | Query with different engines; adding an engine is easy. |
| R12 | Support any lakehouse's underlying data, extendable with more formats: Parquet, Arrow IPC, Vortex, Iceberg, Delta Lake, Hudi, Fluss, and others. |
| R13 | References the user gave: [onehouseinc/LakeView](https://github.com/onehouseinc/LakeView) and [treeverse/lakeview](https://github.com/treeverse/lakeview) ([landscape](landscape.md)). |
| R14 | Browser-based from the start, as a Next.js app, with Rust compiled to WebAssembly where needed; also packaged as a Mac app. |
| R15 | Writing is allowed, but the app must be very clear about the dangers, with a banner while writes are possible. |
| R16 | Published on crates.io, npm, PyPI, and Homebrew. |
| R17 | Proprietary warehouses later; open formats first. |

### Goals

- **G1 — Finds things on its own:** given only a bucket and credentials, it maps what is there (spec 3).
- **G2 — Shows the real files:** every view leads down to the objects and bytes behind it.
- **G3 — Explains:** structure, query plans, and findings are drawn, not only listed.
- **G4 — Cheap to look:** reads are ranged, cached, and counted; a look at a large table costs a known, small number
  of requests (spec 2).
- **G5 — Safe:** read-only unless the user turns on write mode for a connection (spec 11); credentials go only to
  the stores they belong to ([security](security.md)).
- **G6 — Extendable:** a file format, table format, or engine is one crate behind a shared trait
  ([architecture](architecture.md)).

### Non-goals (for now)

- Being a catalog or a query engine of its own: lakeglass reads catalogs and embeds engines.
- Monitoring and alerts over time.
- Proprietary warehouses (spec 17 explores them later).

### Terms

| Term | Meaning |
|------|---------|
| **Connection** | A store or catalog lakeglass is connected to, with its credentials and its mode (read-only or write) (spec 1). |
| **Backend** | Where the Rust core runs: in the browser as WebAssembly, or natively in the Mac app or CLI ([architecture](architecture.md)). |
| **Store map** | What detection found in a store: prefixes labelled as tables, file sets, or unknown (spec 3). |
| **Table** | Files plus metadata that define one table: Iceberg, Delta, Hudi, Paimon, Lance, DuckLake, or a Hive-style directory (spec 6). |
| **Snapshot** | One version of a table: an Iceberg snapshot, a Delta commit, a Hudi instant, a Lance version. |
| **Metadata file** | A file that describes a table or a file set rather than holding rows. |
| **Data file** | A file that holds rows: Parquet, ORC, Avro, Arrow IPC, Vortex, Lance, SSTable (spec 5). |
| **Anatomy** | The byte layout of one file: header, row groups or segments, column chunks, pages, footer, indexes (spec 5). |
| **Engine** | A query engine lakeglass embeds or connects to (spec 8). |
| **Trace** | How one query read a table: metadata read, files and row groups skipped and why, requests and bytes (spec 9). |
| **Check** | A rule that finds a way a table or file could be stored better (spec 10). |

## Decisions

| ID | Decision | Status |
|---|---|---|
| D1 | Name: **lakeglass**. Crates `lakeglass` and `lakeglass-<part>`, npm `lakeglass` and `@polythings/lakeglass-*`, PyPI `lakeglass`. | Accepted |
| D2 | Its own public repository, `github.com/polythings/lakeglass`, independent of Timelake. It reads Timelake tables as Iceberg tables. | Accepted |
| D3 | License: **Apache License 2.0**. | Accepted |
| D4 | "pig" in the original ask meant **Puffin**, Iceberg's statistics and deletion-vector files ([#2](https://github.com/polythings/lakeglass/issues/2)). | Accepted |
| D5 | Open formats first; proprietary warehouses later (spec 17, [#3](https://github.com/polythings/lakeglass/issues/3)). | Accepted |

## History

- 2026-10-19: proposed; R14–R17 added from the answers to issues #1–#8.
