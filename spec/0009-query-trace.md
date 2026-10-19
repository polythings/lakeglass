---
number: 9
title: Query trace
area: Queries
status: planned
updated: 2026-10-19
versions: []
related: [2, 5, 6, 8, 10]
issues: []
---

# 9. Query trace

**Planned** · updated 2026-10-19 · milestone: [v0](milestones/v0.md)
· related: [spec 2](0002-request-counting.md), [spec 5](0005-file-anatomy.md), [spec 6](0006-table-explorer.md),
[spec 8](0008-sql-engines.md), [spec 10](0010-checks.md)

## Summary

The trace shows how a query reads a specific table, step by step: which metadata files were read, which data files
were skipped and why (partition, file statistics), which row groups and pages inside the remaining files were
skipped, and the requests, bytes, and time each step cost. It answers "why did this query read what it read" and
points at what would let it read less.

## Design

### Two sources

1. **lakeglass's own pruning walk:** given the query's filter on one table, it walks the metadata tree (spec 6) and
   the file anatomy (spec 5) and records each decision with its reason ("manifest skipped: partition range 2026-09
   doesn't overlap", "file skipped: max(ts) < lower bound", "row group skipped: page index"). It works without
   running the query, for any table format with statistics.
2. **The engine's run:** the plan with metrics (spec 8) and, for DataFusion, the exact requests in order (spec 2).

Where the two differ (the engine read a file the walk would skip, or the reverse), the trace marks it.

### Trace model

Stages (catalog → table metadata → manifests → files → row groups → pages → rows), each with items in, items kept,
items skipped grouped by reason, requests, bytes, and time. Each item links to its file or anatomy node.

### Views

- A funnel from all files to the rows returned, with bytes at each stage.
- The requests as a waterfall: which ran in parallel and which waited on others.
- Each file's statistics against the filter.
- Findings from the checks (spec 10) that apply to this query: "no statistics on `device_id`", "files not sorted by
  `ts`, so 412 of 500 overlap the filter".

## Decisions

| ID | Decision | Status |
|---|---|---|
| D26 | The trace combines lakeglass's own pruning walk with the engine's plan and counted requests. | Proposed |

## Open questions and issues

None open.

## History

- 2026-10-19: proposed.
