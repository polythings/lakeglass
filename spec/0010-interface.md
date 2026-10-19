---
number: 10
title: Interface
area: Interface
status: planned
updated: 2026-10-19
related: [2, 4, 5, 6, 7, 8, 9]
issues: [1, 7]
---

# 10. Interface

**Planned** · updated 2026-10-19 · related: [spec 2](0002-architecture.md), [spec 4](0004-detection.md),
[spec 5](0005-file-formats.md), [spec 6](0006-table-formats.md), [spec 7](0007-engines.md),
[spec 8](0008-query-trace.md), [spec 9](0009-checks.md)

## Summary

A web interface served by the `lakeglass` binary. It moves from the store down to a byte: store map → table →
metadata file or data file → anatomy node, with SQL, the query trace, and findings available at each level.
Structure is drawn as diagrams, not only listed.

## Design

### Views

| View | Shows |
|------|-------|
| Stores | Connected stores and catalogs; add one |
| Store map | Prefixes as a tree and as a size map (area by bytes), coloured by detected format; object counts and sizes; detection evidence |
| Table | Overview (format, version, size, files, rows, last commit); snapshot timeline; schema history; partitions; files; deletes; properties |
| Metadata tree | The table's metadata files as a node graph (for Iceberg: metadata.json → manifest list → manifests → files), with each file's contents on click |
| File | Anatomy byte map, node tree, properties; rows; for metadata files the decoded content (JSON, Avro) |
| SQL | Editor with the engine picker, results table, plan, and trace; compare two engines |
| Trace | Funnel, request waterfall, skipped and kept items with reasons (spec 8) |
| Findings | Checks for the current subject with evidence and fixes (spec 9) |
| Learn | For each format, a diagram of how it is laid out, drawn from a real table the user has open when there is one |

Every view shows the requests and bytes it cost (spec 3).

### Visual style

Follows the Timelake site: dot-matrix charts (small squares), rounded cards with an icon and a short title, one colour
per format, near-black dark theme, motion only once (draw in on open, no looping animation), plain factual labels.
Large lists and byte maps are virtualized.

### Stack

A single-page app in TypeScript and React, built with Vite, bun, oxlint, and oxfmt, and embedded in the binary
([#7](https://github.com/polythings/lakeglass/issues/7)). API types are generated from the Rust types.

## Decisions

| ID | Decision | Status |
|---|---|---|
| D22 | Every view links down to the files behind it and shows the requests it cost. | Proposed |

## Open questions and issues

- [#1](https://github.com/polythings/lakeglass/issues/1) How lakeglass runs.
- [#7](https://github.com/polythings/lakeglass/issues/7) UI stack.

## History

- 2026-10-19: proposed.
