---
number: 11
title: Write mode
area: Insights
status: planned
updated: 2026-10-20
versions: []
related: [1, 2, 10]
issues: []
---

# 11. Write mode

**Planned** · updated 2026-10-20 · milestone: [Write mode](milestones/write-mode.md)
· related: [spec 1](0001-connections.md), [spec 2](0002-request-counting.md), [spec 10](0010-checks.md)

## Summary

lakeglass can change data, but only on a connection where the user has turned write mode on, and it makes the danger
plain at every step: a warning when it's turned on, a red banner on every screen while it's on, a preview of exactly
what each action will write or delete, and a typed confirmation before it runs. Table operations go through each
format's own commit protocol; nothing edits a table's files behind its metadata.

## Design

### Turning it on

- Off by default for every connection (spec 1, D15). Turning it on opens a dialog that says what can now happen
  (objects deleted or overwritten, table history removed, data that may be unrecoverable without bucket versioning)
  and whether versioning is enabled on the bucket.
- It lasts for the session unless the user chooses to keep it on for that connection. Natively it can be disabled
  for good with `writes.allowed = false` in the config.

### While it's on

- A red banner across the top of every screen: "Write mode: changes to <connection> are possible", with a button to
  turn it off.
- Every action that can write is marked, and disabled on read-only connections.

### Every action

1. **Preview (dry run):** the exact objects to be written, overwritten, or deleted, the new metadata files, the
   snapshot that will result, and the request count and estimated price.
2. **Confirm:** type the table name (or the key, for an object) to run it.
3. **Run** through the counting layer (spec 2), with progress and cancel where the operation allows.
4. **Log:** every action is kept in a local log with its preview and result.

### Actions

| Action | Kind | Backends |
|--------|------|----------|
| Upload, copy, rename, delete objects | Plain objects | Browser and native |
| Abort incomplete multipart uploads | Plain objects | Browser and native |
| Expire snapshots | Table, through the format's commit | Native first |
| Remove orphan files | Table, after a full listing | Native first |
| Rewrite manifests, compact small files | Table, through the format's commit | Native first |
| Run a fix from a finding (spec 10) | Any of the above | As above |

Table actions go through each format's commit protocol. For Iceberg that is lakebed's (`lakebed-iceberg`, D8), which
builds for the browser as well as natively and whose tests check that iceberg-rust reads what it commits; it commits
format v2 tables kept without a catalog (`v<N>.metadata.json`), and tables in a catalog commit through the catalog
(spec 7). Other formats run natively first, through the official crates (delta-rs, …), until lakebed has them. In the
browser, writes also need PUT and DELETE in the bucket's CORS rule.

## Decisions

| ID | Decision | Status |
|---|---|---|
| D28 | Writing is allowed only in write mode, per connection, with a warning, a banner, a preview, and a typed confirmation. | Accepted |
| D29 | Table changes go through the format's commit protocol: lakebed's where it has the format (Iceberg, in the browser too), the official crates natively otherwise. | Proposed |

## Open questions and issues

None open.

## History

- 2026-10-19: proposed after [#4](https://github.com/polythings/lakeglass/issues/4): writing allowed, with the dangers
  made very clear.
- 2026-10-20: Iceberg table actions through lakebed's commit, in the browser too (D8).
