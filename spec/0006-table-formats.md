---
number: 6
title: Table formats and catalogs
area: Formats
status: planned
updated: 2026-10-19
related: [2, 4, 5, 8, 9]
issues: [3, 5]
---

# 6. Table formats and catalogs

**Planned** · updated 2026-10-19 · related: [spec 2](0002-architecture.md), [spec 4](0004-detection.md),
[spec 5](0005-file-formats.md), [spec 8](0008-query-trace.md), [spec 9](0009-checks.md)

## Summary

Every table format is mapped onto one model (snapshots, schema history, partitioning, a metadata tree, data files,
delete files) so the UI can show any of them the same way and compare them. The format's own metadata files stay
visible: the model links each part back to the file it came from. Tables are found by detection (spec 4) or through
a catalog.

## Design

### Table model

| Part | Contents |
|------|----------|
| Snapshots | Id, time, operation (append, overwrite, delete, compaction), summary counts, parent, and the metadata file that defines it |
| Schema history | Each schema version, with the snapshot that introduced it and the field-level changes |
| Partitioning | Spec history; partitions with file count, rows, and bytes |
| Metadata tree | The files that define the current (or chosen) snapshot and how they point at each other: for Iceberg metadata.json → manifest list → manifests → data and delete files |
| Data files | Path, format, rows, bytes, partition, column statistics (min, max, nulls), sort order |
| Delete files | Position deletes, equality deletes, deletion vectors; which data files they apply to |
| Properties | Table properties and format version |

Time travel: every view can be pinned to an older snapshot.

### Per format

| Format | Snapshots from | Metadata tree |
|--------|----------------|---------------|
| Iceberg (v1–v3) | `metadata.json` snapshot log | metadata.json → manifest list → manifests → files; Puffin statistics and deletion vectors |
| Delta Lake | `_delta_log` commits and checkpoints | Commits and checkpoint → add and remove actions; deletion vectors |
| Hudi (0.x, 1.x) | Timeline instants (active and archived) | Instants → file groups → file slices (base file and log files); metadata table |
| Paimon | `snapshot/` | Snapshot → manifest lists → manifests → files per bucket; changelog |
| Lance | `_versions/` manifests | Manifest → fragments → data files and deletion files; indexes |
| DuckLake | Catalog tables (snapshot, data_file, delete_file) | Catalog rows → files |
| Hive-style directory | None (one state) | Partition directories → files |
| Fluss remote storage | Log offsets and KV snapshots | Log segments and index files; KV snapshot SSTs; tiered tables link to their Paimon, Iceberg, or Lance table |
| SlateDB | Manifests | Manifest → WAL SSTs and sorted runs of compacted SSTs |

### Catalogs

Tables can also be opened through a catalog, which gives names and namespaces and the current metadata pointer:
Iceberg REST (Polaris, Lakekeeper, Nessie, Gravitino, S3 Tables), AWS Glue, Unity Catalog, Hive Metastore. A catalog
is a source next to a store; its tables open in the same views. Whether proprietary warehouses (Snowflake, BigQuery,
Redshift) belong here is [#3](https://github.com/polythings/lakeglass/issues/3).

### Cost

Opening a table reads its newest metadata only: for Iceberg, the metadata.json and the manifest list, then manifests
as views need them. Views that need every manifest (all files, partition sizes) say so and show the request count
before they run when it exceeds `table.confirm_above_requests`.

## Decisions

| ID | Decision | Status |
|---|---|---|
| D16 | One table model for all formats, with links from each part to the metadata file it came from. | Proposed |
| D17 | Catalogs are a source next to stores; tables open the same way from either. | Proposed |

## Open questions and issues

- [#3](https://github.com/polythings/lakeglass/issues/3) Proprietary warehouses.
- [#5](https://github.com/polythings/lakeglass/issues/5) Which formats come first.

## History

- 2026-10-19: proposed.
