---
number: 13
title: Browser app
area: Apps
status: planned
updated: 2026-10-19
versions: []
related: [1, 8, 14, 15]
issues: [10]
---

# 13. Browser app

**Planned** · updated 2026-10-19 · milestone: [v0](milestones/v0.md)
· related: [spec 1](0001-connections.md), [spec 8](0008-sql-engines.md), [spec 14](0014-mac-app.md),
[spec 15](0015-cli.md)

## Summary

lakeglass runs in the browser with nothing to install: a static Next.js site whose Rust core runs as WebAssembly in a
Web Worker and talks to the store directly. No lakeglass server sees credentials or data. Buckets need a CORS rule
that allows the site's origin; the app detects a missing rule and shows the exact one to add.

## Design

### Pieces

- Next.js static export ([architecture](architecture.md)); the site serves only static files.
- `lakeglass-wasm` in a Web Worker: store access, detection, anatomy, table readers, checks, DataFusion.
- DuckDB-wasm in a second worker as the second engine (spec 8).
- COOP and COEP headers on every page, so DuckDB-wasm and DataFusion can use threads where they support them.

### CORS

Browsers send signed requests cross-origin only if the bucket allows it. Checked 2026-10-19:

| Store | Default | What the user adds |
|-------|---------|--------------------|
| S3 | No CORS | A bucket CORS rule: methods GET, HEAD (PUT, DELETE for write mode); headers `Authorization`, `x-amz-*`, `Range`; exposed `ETag`, `Content-Range`, `Content-Length`, `Last-Modified`, `x-amz-*` |
| GCS | JSON API allows CORS | Nothing for the JSON API; the S3-compatible XML API needs a bucket CORS config |
| Azure Blob | Off | A Blob service CORS rule on the storage account |
| R2 | Off | A bucket CORS policy, also for presigned URLs |
| Tigris, MinIO | Varies | A bucket CORS rule allowing `Authorization` |

When a request fails with what looks like CORS, the app shows the rule for that store with the site's origin filled
in, as JSON for the console and as a CLI command, limited to the read methods unless write mode is on. A bucket that
can't be changed opens in the Mac app or the CLI instead (specs 14, 15).

### Local files

Dropped files and folders, and the File System Access API in browsers that have it, open without any store.

### Limits

WASM memory (4 GiB for wasm32) and single-threaded DataFusion bound how much a browser query can process; queries
over the limit say so and suggest the Mac app or the CLI.

## Decisions

| ID | Decision | Status |
|---|---|---|
| D31 | The website is static and talks to stores directly from the browser; buckets need a CORS rule, which the app writes for the user. | Proposed |

## Open questions and issues

- [#10](https://github.com/polythings/lakeglass/issues/10) Where the site is hosted before a domain.

## History

- 2026-10-19: proposed after [#1](https://github.com/polythings/lakeglass/issues/1): browser-based from the start.
