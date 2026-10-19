---
number: 9
title: Checks
area: Insights
status: planned
updated: 2026-10-19
related: [5, 6, 8, 11]
issues: [4]
---

# 9. Checks

**Planned** · updated 2026-10-19 · related: [spec 5](0005-file-formats.md), [spec 6](0006-table-formats.md),
[spec 8](0008-query-trace.md), [spec 11](0011-settings.md)

## Summary

Checks look for ways a table, file, or store could be stored better and report findings: what was found, the evidence
(numbers and the files they come from), why it matters (requests, bytes, or time it costs), and the usual fix as SQL
or a command for the user's engine. lakeglass never applies a fix (spec 2, D4). Every check can be turned off and
has its thresholds as settings.

## Design

### Finding

Check id, subject (store, table, file, column), severity (`info`, `warn`), evidence, estimated effect, fix, and a
link to the docs page for the check. Findings are computed on demand for what the user opened, with the cost shown
first when a check needs a full listing or every manifest.

### First checks

| Check | Looks at | Finds |
|-------|----------|-------|
| `files.small` | Table files | Median data file under `checks.small_file_bytes` (default 32 MiB) |
| `files.row_group_size` | Parquet anatomy | Row groups far below or above `checks.row_group_target_bytes` |
| `files.missing_stats` | Parquet, ORC, table stats | Columns used in filters with no min/max |
| `files.no_page_index` | Parquet | No column or offset index |
| `files.dictionary_fallback` | Parquet pages | Column chunks that switched from dictionary to plain encoding |
| `files.codec` | Anatomy | Uncompressed columns, or a codec far worse than the file's others |
| `layout.overlap` | File statistics | Files whose ranges on a sort or filter column overlap, so pruning can't skip them |
| `partitions.skew` | Partition sizes | Partitions far larger or smaller than the median |
| `partitions.too_many` | Partition counts | Many partitions holding a few small files each |
| `snapshots.many` | Snapshot list | More than `checks.max_snapshots` snapshots kept |
| `metadata.manifests` | Iceberg, Paimon | Many small manifests per snapshot |
| `metadata.log_size` | Delta, Hudi | Long commit logs without a recent checkpoint or archive |
| `deletes.backlog` | Delete files, Hudi log files | Delete files or log files piling up on data files (merge-on-read backlog) |
| `files.orphans` | Full listing and metadata | Files under the table not referenced by any kept snapshot |
| `store.multipart` | Store | Incomplete multipart uploads holding storage |

Each check names the formats it applies to. More come with each format crate.

## Decisions

| ID | Decision | Status |
|---|---|---|
| D21 | A finding always carries its evidence and the fix, and lakeglass never runs the fix. | Proposed |

## Open questions and issues

- [#4](https://github.com/polythings/lakeglass/issues/4) Whether fixes could ever run from lakeglass.

## History

- 2026-10-19: proposed.
