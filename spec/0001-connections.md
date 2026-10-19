---
number: 1
title: Store connections
area: Stores
status: planned
updated: 2026-10-19
versions: []
related: [2, 11, 13]
issues: []
---

# 1. Store connections

**Planned** · updated 2026-10-19 · milestone: [v0](milestones/v0.md)
· related: [spec 2](0002-request-counting.md), [spec 11](0011-write-mode.md), [spec 13](0013-browser-app.md)

## Summary

A connection is a store URL, credentials, and a mode (read-only by default). lakeglass connects to S3, GCS, Azure
Blob Storage, S3-compatible stores (R2, Tigris, MinIO, Ceph), HTTP(S) files, and local files, from the browser or
natively.

## Design

- **URL and options:** `s3://bucket/prefix`, `gs://…`, `az://…`, `https://…/file.parquet`, `file:///path`; endpoint,
  region, path-style addressing, and profile as options.
- **Credentials** per backend as in [security](security.md): keys, session tokens, SAS tokens, or presigned URLs in
  the browser; provider chains natively; none for public buckets.
- **Local files:** dropped files and folders in the browser (the File System Access API where the browser has it);
  paths natively.
- **Listing:** LIST with a delimiter, one level at a time, paged, as the user opens prefixes; a full listing of a
  prefix only when a view needs it, bounded by `store.list_max_objects`.
- **Test on connect:** one LIST at the root prefix; errors are explained (credentials, region, CORS with the rule to
  add, permissions with the missing action).
- **Mode:** each connection is read-only unless write mode is turned on for it (spec 11).

## Decisions

| ID | Decision | Status |
|---|---|---|
| D15 | A connection is read-only until write mode is turned on for that connection. | Accepted |

## Open questions and issues

None open.

## History

- 2026-10-19: proposed.
