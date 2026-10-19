# Milestones

The roadmap: goals made of specs, in order. A spec can be part of more than one milestone. Milestones are done, in
progress, planned, or exploring (an idea being looked into, which may not happen).

| # | Milestone | Status | What it gives |
|---:|---|---|---|
| 1 | [v0: explore a bucket in the browser](v0.md) | Planned | Open a bucket in the browser, see what is in it, read Parquet files and Iceberg and Delta tables down to their pages, query them with DataFusion and DuckDB, trace a query, and see the first checks; the CLI's first commands published as 0.0.1. |
| 2 | [Desktop: Mac app and CLI](desktop.md) | Planned | The same interface as a signed Mac app and from `lakeglass open`, with the core running natively: no CORS rules, system credentials, every engine. |
| 3 | [More formats](more-formats.md) | Planned | Hudi, Paimon, Lance, Vortex, ORC, Puffin, SlateDB, Fluss remote storage, and RocksDB SSTs in the file and table explorers, with their explainers and checks. |
| 4 | [Catalogs and large stores](catalogs.md) | Planned | Open tables through Iceberg REST, Glue, Unity Catalog, Hive Metastore, and DuckLake catalogs; size buckets too large to list from inventory reports. |
| 5 | [Write mode](write-mode.md) | Planned | Change data from lakeglass on connections where write mode is on: object operations, expiring snapshots, removing orphans, compaction, and running fixes from findings, with previews and confirmations. |
| 6 | [Warehouses](warehouses.md) | Exploring | Snowflake, BigQuery, and Redshift as connections and engines, linked to the files they keep in the user's bucket. |
