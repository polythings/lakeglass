---
number: 4
title: Detection
area: Sources
status: planned
updated: 2026-10-19
related: [3, 5, 6]
issues: []
---

# 4. Detection

**Planned** · updated 2026-10-19 · related: [spec 3](0003-stores.md), [spec 5](0005-file-formats.md),
[spec 6](0006-table-formats.md)

## Summary

Detection turns a listing into a map: which prefixes are tables (and of which format), which are plain file sets
(and in which file format), and which are unknown. It works from object names first and reads bytes only to confirm,
so mapping a bucket costs mostly LIST requests. Every result carries its evidence and a confidence, and the user
can override it.

## Design

### Steps

1. **Walk** the store breadth-first from the chosen prefix, one level at a time, to `detect.max_depth` levels and
   `detect.max_prefixes` prefixes.
2. **Match table signatures** on each prefix's children (below). A match marks the prefix as a table and stops the
   walk below it: its files belong to the table.
3. **Group plain files** by extension and by Hive-style `key=value/` segments into file sets.
4. **Confirm** by reading magic bytes from `detect.sample_files` files per set (default 3), with range reads of the
   first and last bytes.
5. **Report** each prefix with format, version where known, confidence (`certain`, `likely`, `guess`), and the
   evidence: the files and bytes that matched.

### Table signatures

| Format | Evidence in a listing |
|--------|-----------------------|
| Iceberg | `metadata/*.metadata.json` (also `.gz`), manifest lists `metadata/snap-*.avro`, optional `version-hint.text` (Hadoop catalog only) |
| Delta Lake | `_delta_log/` with 20-digit `.json` commits, `*.checkpoint.parquet`, `_last_checkpoint` |
| Hudi | `.hoodie/hoodie.properties`; instants under `.hoodie/` (0.x) or `.hoodie/timeline/` (1.x) |
| Paimon | `snapshot/` (`LATEST`, `snapshot-N`), `schema/schema-N`, `manifest/`, `bucket-N/` data directories |
| Lance | `_versions/*.manifest`, `data/*.lance` |
| DuckLake | `ducklake-<uuid>.parquet` data files and a `.ducklake` catalog file nearby; confirmed only by opening the catalog |
| SlateDB | `manifest/<20-digit>.manifest`, `wal/*.sst`, `compacted/*.sst` |
| Fluss remote storage | `log/` segments (`<offset>.log`, `.index`, `.timeindex`), `kv/` snapshots, `lake/` |
| Hive-style directory | `key=value/` segments, `_SUCCESS`, `_committed_*` |

A table format crate supplies its own signature (spec 2, `TableFormat`); this table lists the planned ones.

### File signatures

| Format | Bytes |
|--------|-------|
| Parquet | `PAR1` at start and end (`PARE` at the end: encrypted footer) |
| Arrow IPC | `ARROW1` at start and end; a stream starts with `0xFFFFFFFF` |
| ORC | `ORC` at the start |
| Avro | `Obj\x01` at the start |
| Vortex | `VTXF` at start and end |
| Lance | `LANC` in the last 4 bytes |
| Puffin | `PFA1` at start and end |
| RocksDB SST | Magic number in the last 8 bytes |
| SlateDB SST | No magic; known only by its place in a SlateDB layout |

Signatures marked from memory in the research notes are verified against each format's spec before the crate lands.

### Overrides

The user can mark any prefix as a given format, or as plain files. Overrides are kept per store in the local
settings and shown as such.

## Decisions

| ID | Decision | Status |
|---|---|---|
| D12 | Detect from names first, confirm with sampled magic bytes; never read whole files to detect. | Proposed |
| D13 | Each detection result carries evidence and a confidence; the user can override it. | Proposed |

## Open questions and issues

None open.

## History

- 2026-10-19: proposed.
