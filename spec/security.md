---
title: Security and credentials
updated: 2026-10-19
---

# Security and credentials

## Summary

lakeglass handles cloud credentials, reads untrusted files, and can write when the user allows it. Credentials go only
to the store they belong to, never through a lakeglass server; writes are off unless turned on per connection; and
every file is parsed defensively.

## Design

### Credentials

- **Browser (spec 13):** the user enters an access key, a session token, a SAS token, or presigned URLs, or connects
  to a public bucket. Requests go from the browser straight to the store; the lakeglass website has no server that
  sees them. Keys stay in memory for the session unless the user chooses to remember a connection, which stores it in
  the browser's local storage, with a warning that anything running on the page's origin could read it. The docs
  recommend short-lived, read-only, prefix-scoped keys.
- **Mac app and CLI (specs 14, 15):** credentials come from the standard provider chains (AWS profiles and SSO, GCP
  application default credentials, Azure CLI and managed identity, environment variables). The macOS app can keep
  entered keys in the Keychain. Config files name a profile, never a key.
- **Least privilege:** the docs give a read-only policy per cloud, and a separate write policy for write mode
  (spec 11).

### Local server

The CLI's server binds 127.0.0.1 and requires a per-launch token in the URL it opens, so other local users and web
pages can't call its API. Binding elsewhere needs an explicit flag and a warning.

### Untrusted files

Readers check every offset and length against the file size, cap allocations, and are fuzzed. A malformed file
produces an error with the offset where parsing stopped, never a crash.

### No telemetry

lakeglass sends nothing anywhere except requests to the stores, catalogs, and engines the user configured. The
website serves static files only.

## Decisions

| ID | Decision | Status |
|---|---|---|
| D12 | No lakeglass server ever handles a user's credentials or data; the website is static. | Proposed |
| D13 | Browser connections are kept in memory unless the user chooses to remember them. | Proposed |
| D14 | The CLI server listens on localhost with a per-launch token. No telemetry anywhere. | Proposed |

## History

- 2026-10-19: proposed.
