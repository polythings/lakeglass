---
number: 1
title: Purpose and requirements
area: Foundations
status: planned
updated: 2026-10-19
related: [2, 13]
issues: [2, 3]
---

# 1. Purpose and requirements

**Planned** · updated 2026-10-19 · related: [spec 2](0002-architecture.md), [spec 13](0013-v0.md)

## Summary

lakeglass is an open-source (Apache-2.0) dashboard for looking inside object storage. It connects to a bucket, finds
the tables and files stored there, and shows them: tables, metadata files, data files down to their pages, query
results from pluggable engines, how a query reads a table, and what could be stored better. This spec holds the
requirements as stated, the goals and non-goals, and the terms the other specs use.

## Design

### Requirements (as stated)

| ID | Requirement |
|----|-------------|
| R1 | An Apache-2.0 dashboard for exploring data warehouses, data lakes, and lakehouses. |
| R2 | Connect to an object store and detect what is stored there. |
| R3 | Explore plain object stores too, with no table format. |
| R4 | Explore tables, see their data, and query them. |
| R5 | See metadata files. |
| R6 | See data files and understand them: Parquet, Vortex, SSTables, "pig" ([#2](https://github.com/polythings/lakeglass/issues/2)), and others. |
| R7 | Visuals that explain how such systems are structured. |
| R8 | Detect and show ways the data could be optimized. |
| R9 | Visual explanations of how queries run against specific tables. |
| R10 | See tables. |
| R11 | Query with different engines; adding an engine is easy. |
| R12 | Support any lakehouse's underlying data, extendable with more formats: Parquet, Arrow IPC, Vortex, Iceberg, Delta Lake, Hudi, Fluss, and others. |
| R13 | References the user gave: [onehouseinc/LakeView](https://github.com/onehouseinc/LakeView) and [treeverse/lakeview](https://github.com/treeverse/lakeview) ([landscape](landscape.md)). |

### Goals

- **G1 — Finds things on its own:** given only a bucket and credentials, it maps what is there (spec 4).
- **G2 — Shows the real files:** every view leads down to the objects and bytes behind it; nothing is a black box.
- **G3 — Explains:** structure, query plans, and findings are drawn, not only listed (spec 10).
- **G4 — Cheap to look:** reads are ranged, cached, and counted; a look at a large table costs a known, small number
  of requests (spec 3).
- **G5 — Safe:** read-only (spec 2, D4); credentials stay on the user's machine (spec 12).
- **G6 — Extendable:** a file format, table format, or engine is one crate behind a shared trait (spec 2).

### Non-goals (for now)

- Writing, compacting, or deleting data ([#4](https://github.com/polythings/lakeglass/issues/4)).
- Being a catalog or a query engine of its own: lakeglass reads catalogs and embeds engines.
- Monitoring and alerts over time. lakeglass looks at the current state (and history the formats keep themselves).
- Proprietary warehouses explored through their own APIs, unless [#3](https://github.com/polythings/lakeglass/issues/3)
  says otherwise.

### Terms

| Term | Meaning |
|------|---------|
| **Store** | A bucket or directory lakeglass connects to, with its credentials (spec 3). |
| **Map** | What detection found in a store: prefixes labelled as tables, file sets, or unknown (spec 4). |
| **Table** | A set of files with metadata that defines it as one table: Iceberg, Delta, Hudi, Paimon, Lance, DuckLake, or a Hive-style directory (spec 6). |
| **Snapshot** | One version of a table: an Iceberg snapshot, a Delta commit, a Hudi instant, a Lance version. |
| **Metadata file** | A file that describes a table or a file set rather than holding rows: `metadata.json`, a manifest, a `_delta_log` entry, a Hudi instant. |
| **Data file** | A file that holds rows: Parquet, ORC, Avro, Arrow IPC, Vortex, Lance, SSTable (spec 5). |
| **Anatomy** | The byte layout of one file: header, row groups or segments, column chunks, pages, footer, indexes (spec 5). |
| **Engine** | A query engine lakeglass embeds or connects to (spec 7). |
| **Trace** | The record of how one query read a table: metadata read, files and row groups skipped and why, requests and bytes (spec 8). |
| **Check** | A rule that looks for a way a table or file could be stored better and reports a finding with evidence (spec 9). |

## Decisions

| ID | Decision | Status |
|---|---|---|
| D1 | Name: **lakeglass**. Crates are `lakeglass` and `lakeglass-<part>` (spec 2). Free on crates.io, npm, and PyPI on 2026-10-19. | Accepted |
| D2 | Its own public repository, `github.com/polythings/lakeglass`, independent of Timelake. It reads Timelake tables as Iceberg tables. | Accepted |
| D3 | License: **Apache License 2.0**. | Accepted |

## Open questions and issues

- [#2](https://github.com/polythings/lakeglass/issues/2) What "pig" meant (Puffin?).
- [#3](https://github.com/polythings/lakeglass/issues/3) Whether proprietary warehouses are in scope.

## History

- 2026-10-19: proposed.
