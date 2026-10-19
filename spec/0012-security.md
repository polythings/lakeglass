---
number: 12
title: Security and credentials
area: Foundations
status: planned
updated: 2026-10-19
related: [2, 3, 5, 7]
issues: [1]
---

# 12. Security and credentials

**Planned** · updated 2026-10-19 · related: [spec 2](0002-architecture.md), [spec 3](0003-stores.md),
[spec 5](0005-file-formats.md), [spec 7](0007-engines.md)

## Summary

lakeglass handles cloud credentials and reads untrusted files, so it keeps credentials on the user's machine, asks
for read access only, serves its UI to the local machine only, and parses files defensively.

## Design

- **Credentials** come from the standard provider chains (AWS profiles and SSO, GCP application default
  credentials, Azure CLI and managed identity, environment variables). lakeglass doesn't store secrets itself; a store
  entry in the config names a profile, not a key.
- **Least privilege:** the docs give a read-only policy per cloud (list and get on the chosen prefixes). lakeglass
  works with exactly that.
- **Local only:** the server binds 127.0.0.1 and requires a per-launch token in the URL it opens, so other local users
  and web pages can't call its API. Binding elsewhere needs an explicit flag and a warning.
- **No telemetry.** lakeglass sends nothing anywhere except requests to the stores, catalogs, and engines the user
  configured.
- **Untrusted files:** readers check every offset and length, cap allocations, and are fuzzed (spec 5, D15).
- **Logs and exports** never contain credentials or presigned URLs.

## Decisions

| ID | Decision | Status |
|---|---|---|
| D24 | Credentials only from provider chains; nothing secret in lakeglass's own config. | Proposed |
| D25 | The server listens on localhost with a per-launch token; no telemetry. | Proposed |

## Open questions and issues

- [#1](https://github.com/polythings/lakeglass/issues/1) A shared team server would need authentication; out of scope
  until decided.

## History

- 2026-10-19: proposed.
