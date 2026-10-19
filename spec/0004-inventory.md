---
number: 4
title: Store sizes from inventory reports
area: Stores
status: planned
updated: 2026-10-19
versions: []
related: [1, 3]
issues: []
---

# 4. Store sizes from inventory reports

**Planned** · updated 2026-10-19 · milestone: [Catalogs and large stores](milestones/catalogs.md)
· related: [spec 1](0001-connections.md), [spec 3](0003-detection.md)

## Summary

Listing a bucket with billions of objects is slow and costs millions of LIST requests. S3 Inventory and GCS Storage
Insights already write the full object list as Parquet or CSV on a schedule; lakeglass can read those reports to size
every prefix and compare two report dates to show growth.

## Design

- A connection can name an inventory location; lakeglass finds the newest report from its manifest and reads it with
  DataFusion (spec 8).
- The store map (spec 3) uses the report for sizes and counts, and LIST only for the prefixes the user opens.
- The report's date is shown on every number taken from it.
- **Compare:** pick two report dates to see growth per prefix.

The idea of sizing from inventory reports and comparing dates comes from
[treeverse/lakeview](https://github.com/treeverse/lakeview) (PRIOR_ART.md).

## Decisions

| ID | Decision | Status |
|---|---|---|
| D20 | Inventory reports are a listing source next to LIST, for buckets too large to list. | Proposed |

## Open questions and issues

None open.

## History

- 2026-10-19: proposed.
