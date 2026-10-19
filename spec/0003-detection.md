---
number: 3
title: Store map and format detection
area: Stores
status: planned
updated: 2026-10-19
versions: []
related: [1, 5, 6]
issues: []
---

# 3. Store map and format detection

**Planned** · updated 2026-10-19 · milestone: [v0](milestones/v0.md)
· related: [spec 1](0001-connections.md), [spec 5](0005-file-anatomy.md), [spec 6](0006-table-explorer.md)

## Summary

Detection turns a listing into a store map: which prefixes are tables (and of which format), which are plain file
sets (and in which file format), and which are unknown. It works from object names first and reads bytes only to
confirm, so mapping a bucket costs mostly LIST requests. Each result carries its evidence and a confidence, and the
user can override it. The map is drawn as a tree and as a size map.

## Design

### Steps

1. **Walk** breadth-first from the chosen prefix to `detect.max_depth` levels and `detect.max_prefixes` prefixes.
2. **Match table signatures** on each prefix's children. A match marks the prefix as a table and stops the walk
   below it.
3. **Group plain files** by extension and by Hive-style `key=value/` segments into file sets.
4. **Confirm** with magic bytes from `detect.sample_files` files per set (default 3), reading the first and last
   bytes.
5. **Report** format, version where known, confidence (`certain`, `likely`, `guess`), and the evidence.

### Table signatures

| Format | Evidence in a listing |
|--------|-----------------------|
| Iceberg | `metadata/*.metadata.json` (also `.gz`), manifest lists `metadata/snap-*.avro`, optional `version-hint.text` |
| Delta Lake | `_delta_log/` with 20-digit `.json` commits, `*.checkpoint.parquet`, `_last_checkpoint` |
| Hudi | `.hoodie/hoodie.properties`; instants under `.hoodie/` (0.x) or `.hoodie/timeline/` (1.x) |
| Paimon | `snapshot/` (`LATEST`, `snapshot-N`), `schema/schema-N`, `manifest/`, `bucket-N/` |
| Lance | `_versions/*.manifest`, `data/*.lance` |
| DuckLake | `ducklake-<uuid>.parquet` data files and a `.ducklake` catalog file nearby; confirmed by opening the catalog |
| SlateDB | `manifest/<20-digit>.manifest`, `wal/*.sst`, `compacted/*.sst` |
| Fluss remote storage | `log/` segments (`<offset>.log`, `.index`, `.timeindex`), `kv/` snapshots, `lake/` |
| Hive-style directory | `key=value/` segments, `_SUCCESS`, `_committed_*` |

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
| SlateDB SST | No magic; known by its place in a SlateDB layout |

Each format crate supplies its own signatures; they are checked against the format's spec when the crate lands.

### Store map view

Prefixes as a tree and as a size map (area by bytes, colour by format), with object counts, sizes, the detection
evidence, and the requests the map cost. Overrides ("this prefix is a Delta table", "plain files") are kept per
connection.

## Decisions

| ID | Decision | Status |
|---|---|---|
| D18 | Detect from names first and confirm with sampled magic bytes; never read whole files to detect. | Proposed |
| D19 | Each result carries evidence and a confidence; the user can override it. | Proposed |

## Open questions and issues

None open.

## History

- 2026-10-19: proposed.
