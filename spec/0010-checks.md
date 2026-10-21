---
number: 10
title: Checks
area: Insights
status: planned
updated: 2026-10-21
versions: []
related: [5, 6, 9, 11, 12]
issues: [13]
---

# 10. Checks

**Planned** · updated 2026-10-21 · milestones: [v0](milestones/v0.md), [More formats](milestones/more-formats.md),
[Write mode](milestones/write-mode.md)
· related: [spec 5](0005-file-anatomy.md), [spec 6](0006-table-explorer.md), [spec 9](0009-query-trace.md),
[spec 11](0011-write-mode.md), [spec 12](0012-settings.md)

## Summary

Checks look for ways a table, file, or store could be stored better and report findings: what was found, the
evidence (numbers and the files they come from), why it matters (requests, bytes, or time it costs), and the usual
fix as SQL or a command for the user's engine. In write mode some fixes can run from lakeglass (spec 11); otherwise
lakeglass only shows them. Every check can be turned off and has its thresholds as settings.

## Design

### Finding

Check id, subject (store, table, file, column), severity (`info`, `warn`), evidence, estimated effect, fix, and a
link to the check's docs. Findings are computed on demand for what the user opened; a check that needs a full listing
or every manifest shows its cost first.

### Checks

| Check | Looks at | Finds | Milestone |
|-------|----------|-------|-----------|
| `files.small` | Table files | Median data file under `checks.small_file_bytes` (default 32 MiB) | v0 |
| `files.row_group_size` | Parquet anatomy | Row groups far from `checks.row_group_target_bytes` | v0 |
| `files.missing_stats` | Parquet, table stats | Columns used in filters with no min/max | v0 |
| `files.no_page_index` | Parquet | No column or offset index | v0 |
| `layout.overlap` | File statistics | Files whose ranges on a sort or filter column overlap, so pruning can't skip them | v0 |
| `snapshots.many` | Snapshots | More than `checks.max_snapshots` kept | v0 |
| `metadata.manifests` | Iceberg | Many small manifests per snapshot | v0 |
| `deletes.backlog` | Delete files | Delete files piling up on data files (merge-on-read backlog) | v0 |
| `files.dictionary_fallback` | Parquet pages | Column chunks that fell back from dictionary to plain encoding | More formats |
| `files.codec` | Anatomy | Uncompressed columns, or a codec far worse than the file's others | More formats |
| `partitions.skew` | Partition sizes | Partitions far from the median | More formats |
| `partitions.too_many` | Partition counts | Many partitions holding a few small files each | More formats |
| `metadata.log_size` | Delta, Hudi | Long commit logs without a recent checkpoint or archive | More formats |
| `files.orphans` | Full listing and metadata | Files under the table not referenced by any kept snapshot | More formats |
| `store.multipart` | Store | Incomplete multipart uploads holding storage | More formats |

Each check names the formats it applies to; format crates add their own.

## Decisions

| ID | Decision | Status |
|---|---|---|
| D27 | A finding always carries its evidence and its fix. | Proposed |

## Open questions and issues

- [#13](https://github.com/polythings/lakeglass/issues/13) Checks: which table-health ideas from lake-pulse to adopt?

## History

- 2026-10-19: proposed.
- 2026-10-21: lake-pulse's metrics compared with the checks ([#13](https://github.com/polythings/lakeglass/issues/13)).
