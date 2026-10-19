---
number: 12
title: Settings
area: Interface
status: planned
updated: 2026-10-19
versions: []
related: [2, 3, 5, 8, 10, 11]
issues: []
---

# 12. Settings

**Planned** · updated 2026-10-19 · milestone: [v0](milestones/v0.md)
· related: [spec 2](0002-request-counting.md), [spec 3](0003-detection.md), [spec 5](0005-file-anatomy.md),
[spec 8](0008-sql-engines.md), [spec 10](0010-checks.md), [spec 11](0011-write-mode.md)

## Summary

Every check and every read that can cost much is a named setting, so a user can see and change what lakeglass does
and what it costs. Settings are named for the mechanism they change, and each is documented with its trade-off and
why its default is what it is.

## Design

- **Names** are dotted and describe the mechanism: `detect.sample_files`, `store.footer_prefetch_bytes`,
  `checks.small_file_bytes`; never an outcome like `fast` or `cheap`.
- **Scopes**, most specific wins: default → user config (a file natively, browser storage on the website) →
  connection → session → a single view or query.
- **Every check** has `checks.<id>.enabled` and its thresholds.
- **One registry** in `lakeglass-core` lists every setting with type, default, scope, and doc text; the CLI, the
  settings screen, and the docs come from it.

Named so far: `store.list_max_objects`, `store.footer_prefetch_bytes`, `store.max_concurrent_requests`,
`store.price_per_1k_get` (and LIST, PUT), `cache.disk_bytes`, `cache.list_ttl_seconds`, `detect.max_depth`,
`detect.max_prefixes`, `detect.sample_files`, `anatomy.read_page_headers`, `anatomy.max_metadata_bytes`,
`rows.preview_limit`, `table.confirm_above_requests`, `query.max_rows`, `query.timeout_seconds`,
`writes.allowed`, and the check thresholds in spec 10.

## Decisions

| ID | Decision | Status |
|---|---|---|
| D30 | Every check and costly read is a setting, named for its mechanism, from one registry. | Proposed |

## Open questions and issues

None open.

## History

- 2026-10-19: proposed.
