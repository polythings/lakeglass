# Prior art

This file credits the ideas lakeglass takes from other open-source projects and keeps notes on what we're studying. We
take ideas, never code. How to credit is in [CONTRIBUTING.md](CONTRIBUTING.md#credit); how lakeglass compares with
other tools is in [spec/landscape.md](spec/landscape.md).

## Ideas

Methods we plan to adopt and implement ourselves. Licenses checked on 2026-10-19.

| Idea | From | License of the source | Where in lakeglass |
|---|---|---|---|
| Sizing prefixes from S3 Inventory reports instead of listing, and comparing two report dates to see growth | [treeverse/lakeview](https://github.com/treeverse/lakeview) | Apache-2.0 | Store sizes (spec 4) |
| Drill-down disk usage by directory | [ncdu](https://dev.yorhel.nl/ncdu) | MIT | Store map (spec 3) |
| Reading Parquet in the browser with range requests, through Rust compiled to WebAssembly | [parquet-viewer](https://github.com/XiangpengHao/parquet-viewer) | Apache-2.0 / MIT | Browser app (spec 13) |
| Showing a columnar file as a tree of its segments and encodings | [Vortex](https://github.com/vortex-data/vortex) (`vortex-tui`) | Apache-2.0 | File anatomy (spec 5) |
| Table health metrics as the basis for maintenance suggestions | [Apache Amoro](https://github.com/apache/amoro) | Apache-2.0 | Checks (spec 10) |

## Studying

| Project | License | Notes |
|---|---|---|
| [parquet-cli](https://github.com/apache/parquet-java/tree/master/parquet-cli) | Apache-2.0 | Page, dictionary, and footer dumps: what a file inspector should show |
| [hudi-cli](https://github.com/apache/hudi) | Apache-2.0 | Timeline, commits, compactions, and file slices of Hudi tables |
| [Apache XTable](https://github.com/apache/incubator-xtable) | Apache-2.0 | One model of a table across Iceberg, Delta, and Hudi metadata |
| [lance-data-viewer](https://github.com/lance-format/lance-data-viewer) | MIT | Browsing Lance tables |
| [hyparquet](https://github.com/hyparam/hyparquet), [hightable](https://github.com/hyparam/hightable) | MIT | Pure-JS Parquet reads by range; a virtualized table for large results |
| [lake-pulse](https://github.com/adobe/lake-pulse) | MIT / Apache-2.0 | Table health across formats: breaking schema changes, snapshot retention against age, deletion-vector load, and scores per area; candidates for the checks ([#13](https://github.com/polythings/lakeglass/issues/13)) |
