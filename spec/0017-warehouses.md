---
number: 17
title: Proprietary warehouses
area: Formats
status: planned
updated: 2026-10-19
versions: []
related: [7, 8]
issues: [3]
---

# 17. Proprietary warehouses

**Planned** · updated 2026-10-19 · milestone: [Warehouses](milestones/warehouses.md)
· related: [spec 7](0007-catalogs.md), [spec 8](0008-sql-engines.md)

## Summary

Later, after the open formats: connect to Snowflake, BigQuery, and Redshift through their APIs to list databases and
tables, show what each warehouse exposes about storage (micro-partitions, clustering depth, partitions and clusters),
and run SQL there as a remote engine. Where a warehouse manages Iceberg tables in the user's bucket, lakeglass links
the warehouse table to the files.

## Design

To be written when the milestone starts. Each warehouse is a connection type (spec 7) and an engine (spec 8).

## Decisions

| ID | Decision | Status |
|---|---|---|
| D34 | Proprietary warehouses come after the open formats. | Accepted |

## Open questions and issues

- [#3](https://github.com/polythings/lakeglass/issues/3) Scope, when the milestone starts.

## History

- 2026-10-19: proposed after [#3](https://github.com/polythings/lakeglass/issues/3): yes in the future, open formats
  first.
