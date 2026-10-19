---
number: 11
title: Settings
area: Foundations
status: planned
updated: 2026-10-19
related: [3, 4, 5, 7, 9]
issues: []
---

# 11. Settings

**Planned** · updated 2026-10-19 · related: [spec 3](0003-stores.md), [spec 4](0004-detection.md),
[spec 5](0005-file-formats.md), [spec 7](0007-engines.md), [spec 9](0009-checks.md)

## Summary

Every check and every read that can cost much is a named setting, so a user can see and change what lakeglass does
and what it costs. Settings are named for the mechanism they change, and each is documented with its trade-off and
why its default is what it is.

## Design

- **Names** are dotted and describe the mechanism: `detect.sample_files`, `store.footer_prefetch_bytes`,
  `checks.small_file_bytes`; never an outcome like `fast` or `cheap`.
- **Scopes**, most specific wins: built-in default → user config file (`~/.config/lakeglass/config.toml`) → store →
  session (changed in the UI) → a single view or query.
- **Every check** has `checks.<id>.enabled` and its thresholds.
- **One registry** lists every setting with type, default, scope, and doc text; the CLI, the UI settings page, and
  the docs are generated from it.

Settings named so far: `store.list_max_objects`, `store.footer_prefetch_bytes`, `store.max_concurrent_requests`,
`store.price_per_1k_get` (and LIST, PUT), `cache.disk_bytes`, `cache.list_ttl_seconds`, `detect.max_depth`,
`detect.max_prefixes`, `detect.sample_files`, `anatomy.read_page_headers`, `anatomy.max_metadata_bytes`,
`rows.preview_limit`, `table.confirm_above_requests`, `query.max_rows`, `query.timeout_seconds`, and the check
thresholds in spec 9.

## Decisions

| ID | Decision | Status |
|---|---|---|
| D23 | Every check and costly read is a setting, named for its mechanism, from one registry that generates the CLI, UI, and docs. | Proposed |

## Open questions and issues

None open.

## History

- 2026-10-19: proposed.
