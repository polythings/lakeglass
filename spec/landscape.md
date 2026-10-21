# Landscape

Tools that overlap with lakeglass, checked on 2026-10-19 (lake-pulse on 2026-10-21). Projects whose ideas we use are credited in
[PRIOR_ART.md](../PRIOR_ART.md); vendor-led, open-core, or commercial tools are listed here for comparison only.

| Tool | License | What it does | Compared with lakeglass |
|------|---------|--------------|-------------------------|
| [Onehouse LakeView](https://github.com/onehouseinc/LakeView) | Apache-2.0 extractor; hosted UI | Hudi monitoring: timeline, compaction backlog, partition skew, file sizes, alerts. The open repo extracts Hudi commit files and uploads them; dashboards run in the vendor's hosted service. | Hudi only, and the views are hosted. lakeglass runs in the browser or on the user's machine, covers many formats, and reads files down to pages. |
| [treeverse/lakeview](https://github.com/treeverse/lakeview) | Apache-2.0 | Prefix sizes for very large S3 buckets from S3 Inventory reports through Athena; compare two dates. Dormant. | No table formats or file contents. lakeglass uses its inventory idea (spec 4). |
| [parquet-viewer](https://github.com/XiangpengHao/parquet-viewer) | Apache-2.0 / MIT | In-browser Parquet viewer in Rust and WASM: metadata, SQL through DataFusion, files from S3 or URLs. | Parquet only. |
| [Nimtable](https://github.com/nimtable/nimtable) | Apache-2.0 (vendor-led) | Iceberg catalog browser: snapshots, manifests, file distribution, SQL, compaction. | Iceberg only, through catalogs. |
| [Apache Amoro](https://github.com/apache/amoro) | Apache-2.0 | Lakehouse management for Iceberg, Paimon, and Hudi: health metrics and self-optimizing (compaction). | Runs maintenance as a service; no file anatomy or query trace. |
| [lake-pulse](https://github.com/adobe/lake-pulse) | MIT / Apache-2.0 | Rust library that reports table health for Delta, Iceberg, Hudi, Lance, and Paimon: file sizes and compaction, partitions, snapshots and retention, schema changes, deletion vectors, with 0–1 scores. Reads through the official crates and `object_store`. | A library with a text report, native only (its crates don't build for WASM); no file anatomy, query trace, or interface. Its metrics overlap the checks (spec 10, [#13](https://github.com/polythings/lakeglass/issues/13)). |
| [Apache XTable](https://github.com/apache/incubator-xtable) | Apache-2.0 | Translates table metadata between Hudi, Delta, and Iceberg. | Conversion, not exploration. |
| [vortex-tui](https://github.com/vortex-data/vortex) | Apache-2.0 | TUI and web explorer for Vortex file layouts, encodings, and segments. | Vortex only. |
| [lance-data-viewer](https://github.com/lance-format/lance-data-viewer) | MIT | Local web UI for Lance tables. | Lance only. |
| [hudi-cli](https://github.com/apache/hudi) | Apache-2.0 | Inspects Hudi timelines, commits, compactions, and file slices. | Hudi only, command line. |
| [parquet-cli](https://github.com/apache/parquet-java/tree/master/parquet-cli) | Apache-2.0 | Parquet metadata, page, and dictionary dumps. | Command line, Parquet only. |
| Catalog UIs: [Polaris console](https://github.com/apache/polaris-tools), [Gravitino](https://github.com/apache/gravitino), [Unity Catalog](https://github.com/unitycatalog/unitycatalog), [Lakekeeper](https://github.com/lakekeeper/lakekeeper) | Apache-2.0 | Browse namespaces, tables, and permissions of their own catalog. | Catalog-level only; no files. |
| DuckDB UI | MIT extension; UI not open | Notebook UI for DuckDB, served by the vendor. | Not open source; one engine. |
