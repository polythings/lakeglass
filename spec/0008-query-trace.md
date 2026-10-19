---
number: 8
title: Query trace
area: Queries
status: planned
updated: 2026-10-19
related: [3, 5, 6, 7, 10]
issues: []
---

# 8. Query trace

**Planned** · updated 2026-10-19 · related: [spec 3](0003-stores.md), [spec 5](0005-file-formats.md),
[spec 6](0006-table-formats.md), [spec 7](0007-engines.md), [spec 10](0010-interface.md)

## Summary

The trace shows how a query reads a specific table, step by step: which metadata files were read, which data files
were skipped and why (partition, file statistics), which row groups and pages inside the remaining files were
skipped, how many bytes and requests each step cost, and how long it took. It answers "why did this query read
what it read" and points at what would let it read less.

## Design

### Two sources

1. **lakeglass's own pruning walk** for the table format: given the query's filter on one table, it walks the
   metadata tree (spec 6) and the file anatomy (spec 5) and records each decision with its reason: "manifest skipped:
   partition range 2026-09 doesn't overlap", "file skipped: max(ts) < lower bound", "row group skipped: page index".
   This works without running the query and for any table format with statistics.
2. **The engine's run:** the plan with metrics from `explain(analyze)` (spec 7) and, for engines on the counting
   store, the exact requests in order.

The UI shows both and marks where they differ (the engine read a file the walk would skip, or the reverse).

### Trace model

A list of stages (catalog → table metadata → manifests → files → row groups → pages → rows), each with: items in,
items kept, items skipped grouped by reason, requests, bytes, and time. Each item links to its file or anatomy node.

### What it shows

- A funnel from all files to the rows returned, with bytes at each stage.
- The requests as a timeline (waterfall), showing which ran in parallel and which waited on others.
- For each skipped or kept file, its statistics against the filter.
- Hints from the checks (spec 9) that apply to this query: "no statistics on `device_id`", "files not sorted by
  `ts`, so 412 of 500 overlap the filter".

## Decisions

| ID | Decision | Status |
|---|---|---|
| D20 | The trace combines lakeglass's own pruning walk with the engine's plan and its counted requests. | Proposed |

## Open questions and issues

None open.

## History

- 2026-10-19: proposed.
