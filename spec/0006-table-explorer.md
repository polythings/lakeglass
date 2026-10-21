---
number: 6
title: Table explorer
area: Formats
status: planned
updated: 2026-10-20
versions: []
related: [3, 5, 7, 9, 10, 16]
issues: []
---

# 6. Table explorer

**Planned** · updated 2026-10-20 · milestones: [v0](milestones/v0.md), [More formats](milestones/more-formats.md),
[Catalogs and large stores](milestones/catalogs.md)
· related: [spec 3](0003-detection.md), [spec 5](0005-file-anatomy.md), [spec 7](0007-catalogs.md),
[spec 9](0009-query-trace.md), [spec 10](0010-checks.md), [spec 16](0016-format-explainers.md)

## Summary

Every table format is mapped onto one model (snapshots, schema history, partitioning, a metadata tree, data files,
delete files), so the interface shows any of them the same way. The format's own metadata files stay visible: each
part of the model links to the file it came from, and the metadata tree is drawn as a node graph.

## Design

### Table model

| Part | Contents |
|------|----------|
| Overview | Format and version, size, files, rows, last commit, properties |
| Snapshots | Id, time, operation (append, overwrite, delete, compaction), summary counts, parent, and the metadata file that defines it, on a timeline |
| Schema history | Each schema version with the snapshot that introduced it and the field-level changes |
| Partitioning | Spec history; partitions with file count, rows, and bytes |
| Metadata tree | The files that define the chosen snapshot and how they point at each other, as a node graph |
| Data files | Path, format, rows, bytes, partition, column statistics, sort order |
| Delete files | Position deletes, equality deletes, deletion vectors; which data files they apply to |

Every view can be pinned to an older snapshot.

### Formats

| Format | Snapshots from | Metadata tree | Milestone |
|--------|----------------|---------------|-----------|
| Iceberg (v1–v3) | `metadata.json` snapshot log | metadata.json → manifest list → manifests → files; Puffin statistics and deletion vectors | v0 |
| Delta Lake | `_delta_log` commits and checkpoints | Commits and checkpoint → add and remove actions; deletion vectors | v0 |
| Hive-style directory | One state | Partition directories → files | v0 |
| Hudi (0.x, 1.x) | Timeline instants, active and archived | Instants → file groups → file slices (base and log files); metadata table | More formats |
| Paimon | `snapshot/` | Snapshot → manifest lists → manifests → files per bucket; changelog | More formats |
| Lance | `_versions/` manifests | Manifest → fragments → data and deletion files; indexes | More formats |
| SlateDB | Manifests | Manifest → WAL SSTs and sorted runs | More formats |
| Fluss remote storage | Log offsets, KV snapshots | Log segments and indexes; KV snapshot SSTs; links to tiered Paimon, Iceberg, or Lance tables | More formats |
| DuckLake | Catalog tables | Catalog rows → files | Catalogs and large stores |

Readers are lakekit's format crates, shared with Timelake ([architecture](architecture.md), D8); Iceberg is built
there, and the other formats are added there as this spec needs them.

### Cost

Opening a table reads its newest metadata only: for Iceberg, the metadata.json and the manifest list, then manifests
as views need them. A view that needs every manifest shows the request count first when it exceeds
`table.confirm_above_requests`.

## Decisions

| ID | Decision | Status |
|---|---|---|
| D22 | One table model for all formats, with links from each part to the metadata file it came from. | Proposed |

## Open questions and issues

None open.

## History

- 2026-10-19: proposed.
- 2026-10-20: readers come from lakekit (D8).
