---
number: 8
title: SQL with pluggable engines
area: Queries
status: planned
updated: 2026-10-19
versions: []
related: [2, 9]
issues: []
---

# 8. SQL with pluggable engines

**Planned** · updated 2026-10-19 · milestone: [v0](milestones/v0.md)
· related: [spec 2](0002-request-counting.md), [spec 9](0009-query-trace.md)

## Summary

SQL runs through pluggable engines. Each engine is a crate (or, in the browser, a worker module) implementing the
`Engine` interface: it declares what it can read, registers the tables and files the user opened, runs SQL, and
returns Arrow plus its plan and metrics. The same query can run on two engines side by side to compare results,
plans, and cost.

## Design

### The interface

- `capabilities()`: formats it reads, SQL dialect, explain and analyze support, and whether its I/O goes through the
  counting layer (spec 2).
- `register(source)`: make a table (at an optional snapshot) or a file set queryable under a name.
- `query(sql, limits)`: stream Arrow batches, with `query.max_rows` and `query.timeout_seconds`; cancellable.
- `explain(sql, analyze)`: the plan as a tree of operators with metrics, normalized into one plan model, with the
  engine's own text kept alongside.

### Engines

| Engine | Browser | Native | Reads | Requests counted | Milestone |
|--------|---------|--------|-------|------------------|-----------|
| DataFusion | WASM, single-threaded | In-process | Parquet, Arrow, CSV, JSON, Avro; tables through lakeglass's table readers | Yes | v0 |
| DuckDB | DuckDB-wasm in a worker | duckdb-rs | Parquet, CSV, JSON; Iceberg and DuckLake extensions (Delta only natively) | What DuckDB reports | v0 |
| Polars | – | In-process | Parquet, IPC, CSV; Iceberg and Delta scans | Partly | Desktop |
| Flight SQL, Trino, Spark Connect | Over HTTP where CORS allows | Remote | Through their own catalogs | No | Later |

The interface offers an engine for a table only if it can read it; others are listed as unavailable with the reason.
In-process engines get credentials from lakeglass in memory, never in SQL text.

DuckDB-wasm runs multi-threaded only on pages served with COOP and COEP headers; the website sets them (spec 13).

## Decisions

| ID | Decision | Status |
|---|---|---|
| D24 | DataFusion first, because it reads through the counting layer and can be traced exactly; DuckDB second, in both the browser and natively. | Proposed |
| D25 | Engine plans are normalized into one plan model; the raw plan text is kept. | Proposed |

## Open questions and issues

None open.

## History

- 2026-10-19: proposed.
