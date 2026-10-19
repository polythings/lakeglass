---
number: 7
title: Catalog connections
area: Formats
status: planned
updated: 2026-10-19
versions: []
related: [1, 6, 17]
issues: []
---

# 7. Catalog connections

**Planned** · updated 2026-10-19 · milestone: [Catalogs and large stores](milestones/catalogs.md)
· related: [spec 1](0001-connections.md), [spec 6](0006-table-explorer.md), [spec 17](0017-warehouses.md)

## Summary

Tables can be opened through a catalog as well as found in a store. A catalog gives namespaces, table names, and the
current metadata pointer; the tables then open in the same table explorer (spec 6).

## Design

- **Catalogs:** Iceberg REST (Polaris, Lakekeeper, Nessie, Gravitino, S3 Tables), AWS Glue, Unity Catalog, Hive
  Metastore, and DuckLake's SQL catalogs (a DuckDB file, SQLite, Postgres).
- **Browser:** Iceberg REST and S3 Tables work over HTTP subject to CORS; Glue needs signed AWS API calls (CORS
  allowed by AWS APIs only where the service supports it); Hive Metastore (Thrift) and Postgres need the native
  backend.
- **Credential vending:** where the catalog hands out storage credentials (Iceberg REST), lakeglass uses them for that
  table's files and says so.

## Decisions

| ID | Decision | Status |
|---|---|---|
| D23 | Catalogs are a connection type next to stores; tables open the same way from either. | Proposed |

## Open questions and issues

None open.

## History

- 2026-10-19: proposed.
