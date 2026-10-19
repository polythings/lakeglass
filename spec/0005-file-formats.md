---
number: 5
title: File formats and file anatomy
area: Formats
status: planned
updated: 2026-10-19
related: [2, 4, 9, 10]
issues: [2, 5]
---

# 5. File formats and file anatomy

**Planned** · updated 2026-10-19 · related: [spec 2](0002-architecture.md), [spec 4](0004-detection.md),
[spec 9](0009-checks.md), [spec 10](0010-interface.md)

## Summary

For each data or metadata file, lakeglass shows its anatomy (where every part sits in the file, what it costs in
bytes, how it is encoded, what statistics and indexes it carries) and its rows. Anatomy is read from footers and
headers with range reads; rows are read on demand with a limit.

## Design

### Anatomy model

One shared tree, so the UI draws every format the same way:

- **Node:** kind (file, header, row group, stripe, segment, column chunk, page, dictionary, index, footer, block),
  byte offset and length, and a set of properties (codec, encoding, value count, null count, min and max,
  uncompressed size, …).
- **Children** in file order; a node can reference another (a page index points at pages).
- **Totals** per column and per kind: bytes, compression ratio, share of the file.

A format crate fills the tree with whatever its format has; the UI shows the byte map (the file as a strip of
cells, coloured by kind or by column), the tree, and the property table of the selected node.

### Per format

| Format | Anatomy | Rows |
|--------|---------|------|
| Parquet | Row groups, column chunks, pages (from the page index, or by reading page headers when `anatomy.read_page_headers` is on), dictionaries, statistics, page index, bloom filters, key-value metadata, sort order | Yes |
| Arrow IPC | Schema, record batches, dictionary batches, buffers per column | Yes |
| ORC | Stripes, streams per column, row index, stripe and file statistics, bloom filters | Yes |
| Avro | Header and schema, blocks (count, size, codec) | Yes |
| Vortex | Layout tree, segments, encodings per array, statistics | Yes |
| Lance | Pages and column metadata (file format v2) | Yes |
| Puffin ([#2](https://github.com/polythings/lakeglass/issues/2)) | Blobs: type (theta sketch, deletion vector), fields, snapshot, offsets | Decoded blob summaries |
| RocksDB SST | Data blocks, index, filter, properties block, footer | Keys and values, raw |
| SlateDB SST | Blocks, index, filter, info | Keys and values, raw |
| CSV, JSON lines | Size, sampled schema | Yes |

### Rows

Rows come back as Arrow with a projection and a limit (`rows.preview_limit`, default 1,000), from a chosen row group
or offset. Wide or nested columns are shown collapsed. Binary values show as hex with a length.

### Safety

Files are untrusted input: every offset and length is checked against the file size before it's read, allocations
are capped (`anatomy.max_metadata_bytes`), and a malformed file produces an error with the offset where parsing
stopped, never a crash. Fuzz tests cover each parser.

## Decisions

| ID | Decision | Status |
|---|---|---|
| D14 | One anatomy tree for all formats; formats fill it, the UI draws it. | Proposed |
| D15 | Readers treat files as untrusted and are fuzzed. | Proposed |

## Open questions and issues

- [#2](https://github.com/polythings/lakeglass/issues/2) Puffin, or something else, for "pig".
- [#5](https://github.com/polythings/lakeglass/issues/5) Which formats come first.

## History

- 2026-10-19: proposed.
