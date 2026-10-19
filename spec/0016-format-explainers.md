---
number: 16
title: Format explainers
area: Interface
status: planned
updated: 2026-10-19
versions: []
related: [5, 6, 9]
issues: []
---

# 16. Format explainers

**Planned** · updated 2026-10-19 · milestones: [v0](milestones/v0.md), [More formats](milestones/more-formats.md)
· related: [spec 5](0005-file-anatomy.md), [spec 6](0006-table-explorer.md), [spec 9](0009-query-trace.md)

## Summary

For each table and file format, a page that shows how it is laid out and how a reader uses it, drawn from a real
table or file when the user has one open, and from a small built-in example otherwise. They explain structure (what
points at what), how a commit changes it, and how a query prunes it.

## Design

- **Structure:** the format's files as a node graph, with the role of each kind of file (for Iceberg: metadata.json,
  manifest list, manifest, data file, delete file, Puffin).
- **A commit, step by step:** which files an append, a delete, and a compaction add, and which pointer changes.
- **A read, step by step:** the path the trace (spec 9) takes for this format.
- **Side by side:** two formats' structures next to each other (Iceberg and Delta, Parquet and Vortex).
- Built-in examples are small fixture tables shipped with the app, so explainers work offline.
- Drawing follows the Timelake site's style: node graphs, dot-matrix sizes, motion only once.

## Decisions

None yet.

## Open questions and issues

None open.

## History

- 2026-10-19: proposed.
