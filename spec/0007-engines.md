---
number: 7
title: Query engines
area: Queries
status: planned
updated: 2026-10-19
related: [2, 3, 8]
issues: [3, 5]
---

# 7. Query engines

**Planned** · updated 2026-10-19 · related: [spec 2](0002-architecture.md), [spec 3](0003-stores.md),
[spec 8](0008-query-trace.md)

## Summary

SQL runs through pluggable engines. Each engine is a crate implementing the `Engine` trait: it declares what it can
read, registers the tables and files the user opened, runs SQL, and returns Arrow plus its plan and metrics. The
same query can run on two engines side by side to compare results, plans, and costs.

## Design

### The trait

- `capabilities()`: table and file formats it reads, SQL dialect, whether it can explain and analyze, whether its I/O
  goes through the counting store (spec 3).
- `register(source)`: make a table (with an optional snapshot) or a file set queryable under a name.
- `query(sql, limits)`: stream Arrow batches; honour a row limit (`query.max_rows`) and a timeout
  (`query.timeout_seconds`); cancel on request.
- `explain(sql, analyze)`: the engine's plan as a tree of operators with its metrics, normalized into one plan model
  for the UI, with the engine's own text kept alongside.

### Engines

| Engine | How | Reads | Requests counted |
|--------|-----|-------|------------------|
| DataFusion | In-process (Rust) | Parquet, Arrow, CSV, JSON, Avro; Iceberg, Delta, Hudi, Lance, Vortex through their table providers | Yes: uses the counting store |
| DuckDB | In-process (duckdb-rs) | Parquet, CSV, JSON; Iceberg, Delta, DuckLake through extensions | Only what DuckDB reports; its HTTP layer is its own |
| Polars | In-process | Parquet, IPC, CSV; Iceberg and Delta scans | Partly |
| Flight SQL | Remote | Whatever the server reads | No |
| Trino, Spark Connect | Remote | Through their catalogs | No |

The UI only offers an engine for a table it can read; others are listed as unavailable with the reason.

### Credentials to engines

In-process engines get the store's credentials from lakeglass in memory, never on disk or in SQL text. Remote engines
use their own credentials and catalogs; lakeglass only sends SQL.

## Decisions

| ID | Decision | Status |
|---|---|---|
| D18 | DataFusion is the first engine because it runs on the counting store, so its queries can be fully traced (spec 8). | Proposed |
| D19 | Engine plans are normalized into one plan model; the raw plan text is kept. | Proposed |

## Open questions and issues

- [#5](https://github.com/polythings/lakeglass/issues/5) Which engines come first.
- [#3](https://github.com/polythings/lakeglass/issues/3) Proprietary warehouses as engines.

## History

- 2026-10-19: proposed.
