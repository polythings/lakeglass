---
number: 3
title: Stores and connections
area: Sources
status: planned
updated: 2026-10-19
related: [2, 4, 8, 12]
issues: [6]
---

# 3. Stores and connections

**Planned** · updated 2026-10-19 · related: [spec 2](0002-architecture.md), [spec 4](0004-detection.md),
[spec 8](0008-query-trace.md), [spec 12](0012-security.md)

## Summary

How lakeglass reaches a store, lists it, and reads from it: which stores it supports, how it lists very large
buckets, how reads are ranged and cached, and how every request is counted so the UI can show what a look cost.

## Design

### Stores

S3, GCS, Azure Blob Storage, S3-compatible stores (R2, Tigris, MinIO, Ceph), HTTP(S) with range support
(read-only file access, no listing), and a local directory. A store is a URL plus options:
`s3://bucket/prefix`, `gs://…`, `az://…`, `file:///path`. Endpoint, region, and profile are options; credentials
come from the provider chains (spec 12).

### Listing

- **LIST with delimiter** for browsing: one level at a time, paged, as the user opens prefixes.
- **Full listing** of a prefix when a view needs every object (table size, orphan files). Bounded by
  `store.list_max_objects`; when the bound is hit, the view says the numbers are partial.
- **Inventory reports** (S3 Inventory, GCS Storage Insights) as an alternative to listing for very large buckets:
  lakeglass reads the report's Parquet or CSV files and sizes prefixes from them, and can compare two report dates.
  The report is as old as its last run, and the UI shows that date.

### Reads

- Metadata and footers are read with range requests: the last 64 KiB of a file (`store.footer_prefetch_bytes`)
  usually holds a Parquet, ORC, Vortex, or Lance footer in one request.
- Concurrent reads are capped (`store.max_concurrent_requests`).
- **Cache:** objects in object stores don't change under the same key and ETag, so blocks are cached by
  (key, ETag, range) in memory and optionally on local disk (`cache.disk_bytes`, 0 = off). Listings are cached for
  `cache.list_ttl_seconds` and can be refreshed by hand.

### Counting

A wrapper around the store records every request: operation (LIST, HEAD, GET), key, range, bytes, latency, cache
hit or miss, and the cause (a view, a check, a query). The UI shows the totals per view and per query, and an
estimated price from a per-store price table (`store.price_per_1k_get` and friends; defaults from public list
prices, editable). Engines embedded in-process use the same wrapper; an engine with its own I/O (DuckDB's httpfs)
is counted only where it reports its own requests (spec 7).

## Decisions

| ID | Decision | Status |
|---|---|---|
| D10 | Blocks are cached by key, ETag, and range; listings by prefix with a time-to-live. | Proposed |
| D11 | Inventory reports are a listing source next to LIST, for buckets too large to list. | Proposed |

## Open questions and issues

- [#6](https://github.com/polythings/lakeglass/issues/6) `object_store` or OpenDAL.

## History

- 2026-10-19: proposed.
