---
number: 14
title: Mac app
area: Apps
status: planned
updated: 2026-10-19
versions: []
related: [13, 15, 18]
issues: [9]
---

# 14. Mac app

**Planned** · updated 2026-10-19 · milestone: [Desktop](milestones/desktop.md)
· related: [spec 13](0013-browser-app.md), [spec 15](0015-cli.md), [spec 18](0018-packages.md)

## Summary

The same Next.js interface packaged as a macOS app with Tauri, with the Rust core running natively instead of as
WebAssembly. It needs no bucket CORS rules, uses the system's cloud credentials, has every engine and format, and can
run table writes (spec 11).

## Design

- **Tauri 2** wraps the static export (`frontendDist` pointing at the Next.js `out/` directory). The interface's
  backend interface calls Tauri commands (`invoke`) that run the native core ([architecture](architecture.md), D9).
- **Credentials** from the provider chains; keys entered in the app go to the Keychain ([security](security.md)).
- **Headers:** COOP and COEP set through Tauri's security headers.
- **Distribution:** `.dmg` from GitHub Releases and a Homebrew cask (spec 18). Signing with a Developer ID
  certificate and notarization are needed for both, since Homebrew casks must pass Gatekeeper
  ([#9](https://github.com/polythings/lakeglass/issues/9)).
- **Updates:** Tauri's updater against GitHub Releases, signed.
- Electron would also run the full Next.js server, at roughly a hundred megabytes more per download; nothing in
  lakeglass needs a Node server, so Tauri.

## Decisions

| ID | Decision | Status |
|---|---|---|
| D32 | The Mac app is Tauri around the same static interface, with the core running natively. | Accepted |

## Open questions and issues

- [#9](https://github.com/polythings/lakeglass/issues/9) Apple Developer account for signing and notarization.

## History

- 2026-10-19: proposed after [#7](https://github.com/polythings/lakeglass/issues/7).
