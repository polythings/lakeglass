---
number: 2
title: Request counting and cache
area: Stores
status: planned
updated: 2026-10-19
versions: []
related: [1, 9]
issues: []
---

# 2. Request counting and cache

**Planned** · updated 2026-10-19 · milestone: [v0](milestones/v0.md)
· related: [spec 1](0001-connections.md), [spec 9](0009-query-trace.md)

## Summary

Every request lakeglass makes to a store goes through one layer that counts it, caches what can be cached, and
records what caused it. Each view and each query shows the requests, bytes, and estimated price it cost.

## Design

- **Recorded per request:** operation (LIST, HEAD, GET, PUT, DELETE), key, byte range, bytes, latency, cache hit,
  and the cause (a view, a check, a query, a write).
- **Ranged reads:** footers and metadata are read with range requests; the last `store.footer_prefetch_bytes`
  (64 KiB) of a file usually holds a Parquet, ORC, Vortex, or Lance footer in one request.
- **Concurrency:** at most `store.max_concurrent_requests` at once.
- **Cache:** objects don't change under the same key and ETag, so blocks are cached by (key, ETag, range): in memory,
  and on disk natively (`cache.disk_bytes`) or in the browser's Cache Storage. Listings are cached for
  `cache.list_ttl_seconds` and can be refreshed by hand.
- **Price:** a per-store price table (`store.price_per_1k_get`, LIST, PUT) with public list prices as defaults.
- **Engines:** DataFusion reads through this layer, so its requests are exact. DuckDB has its own I/O; its requests
  are shown only where DuckDB reports them (spec 8).

## Decisions

| ID | Decision | Status |
|---|---|---|
| D16 | One counting layer under every reader, check, write, and in-process engine that can use it. | Proposed |
| D17 | Blocks are cached by key, ETag, and range. | Proposed |

## Open questions and issues

None open.

## History

- 2026-10-19: proposed.
