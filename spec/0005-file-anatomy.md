---
number: 5
title: File anatomy and rows
area: Formats
status: planned
updated: 2026-10-19
versions: []
related: [3, 6, 10, 16]
issues: []
---

# 5. File anatomy and rows

**Planned** · updated 2026-10-19 · milestones: [v0](milestones/v0.md), [More formats](milestones/more-formats.md)
· related: [spec 3](0003-detection.md), [spec 6](0006-table-explorer.md), [spec 10](0010-checks.md),
[spec 16](0016-format-explainers.md)

## Summary

For each data or metadata file, lakeglass shows its anatomy (where every part sits in the file, its size, its encoding,
and the statistics and indexes it carries) and its rows. Anatomy is read from footers and headers with range
requests; rows are read on demand with a limit. All formats fill one shared model, so the interface draws them the
same way.

## Design

### Anatomy model

- **Node:** kind (file, header, row group, stripe, segment, column chunk, page, dictionary, index, footer, block,
  blob), byte offset and length, and properties (codec, encoding, value count, null count, min and max, uncompressed
  size, …).
- **Children** in file order; a node can reference another (a page index points at pages).
- **Totals** per column and per kind: bytes, compression ratio, share of the file.

The interface shows the byte map (the file as a strip of cells coloured by kind or by column), the node tree, and
the properties of the selected node.

### Formats

| Format | Anatomy | Rows | Milestone |
|--------|---------|------|-----------|
| Parquet | Row groups, column chunks, pages (from the page index, or page headers when `anatomy.read_page_headers` is on), dictionaries, statistics, page index, bloom filters, key-value metadata, sort order | Yes | v0 |
| Arrow IPC | Schema, record batches, dictionary batches, buffers per column | Yes | v0 |
| Avro | Header and schema, blocks (count, size, codec) | Yes | v0 |
| JSON (metadata files) | Pretty-printed and folded, with links to the files it names | – | v0 |
| CSV, JSON lines | Size, sampled schema | Yes | v0 |
| ORC | Stripes, streams per column, row index, statistics, bloom filters | Yes | More formats |
| Vortex | Layout tree, segments, encodings per array, statistics | Yes | More formats |
| Lance | Pages and column metadata (file format v2) | Yes | More formats |
| Puffin | Blobs: type (theta sketch, deletion vector), fields, snapshot, offsets | Decoded blob summaries | More formats |
| RocksDB SST | Data blocks, index, filter, properties block, footer | Keys and values, raw | More formats |
| SlateDB SST | Blocks, index, filter, info | Keys and values, raw | More formats |

### Rows

Arrow with a projection and a limit (`rows.preview_limit`, default 1,000), from a chosen row group or offset. Nested
columns are shown collapsed; binary values as hex with a length.

### Safety

Readers treat files as untrusted ([security](security.md)) and are fuzzed.

## Decisions

| ID | Decision | Status |
|---|---|---|
| D21 | One anatomy model for all formats; formats fill it, the interface draws it. | Proposed |

## Open questions and issues

None open.

## History

- 2026-10-19: proposed.
