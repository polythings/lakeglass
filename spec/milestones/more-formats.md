---
number: 3
title: More formats
status: planned
updated: 2026-10-19
description: Hudi, Paimon, Lance, Vortex, ORC, Puffin, SlateDB, Fluss remote storage, and RocksDB SSTs in the file and table explorers, with their explainers and checks.
specs: [5, 6, 10, 16]
---

# More formats

**Planned** · updated 2026-10-19

## Summary

Each format is a crate behind the shared file and table interfaces, so this milestone adds crates without changing the views. Order: Puffin and Vortex, Hudi and Paimon, Lance and ORC, then the SSTable and log formats (SlateDB, Fluss, RocksDB).

<!-- specs:start -->

## Specs

| # | Spec | Status | Versions |
|---:|---|---|---|
| 5 | [File anatomy and rows](../0005-file-anatomy.md) | Planned | – |
| 6 | [Table explorer](../0006-table-explorer.md) | Planned | – |
| 10 | [Checks](../0010-checks.md) | Planned | – |
| 16 | [Format explainers](../0016-format-explainers.md) | Planned | – |

<!-- specs:end -->

## History

- 2026-10-19: proposed.
