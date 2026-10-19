---
number: 18
title: Packages and installers
area: Apps
status: planned
updated: 2026-10-19
versions: []
related: [13, 14, 15]
issues: [8, 9]
---

# 18. Packages and installers

**Planned** · updated 2026-10-19 · milestones: [v0](milestones/v0.md), [Desktop](milestones/desktop.md)
· related: [spec 13](0013-browser-app.md), [spec 14](0014-mac-app.md), [spec 15](0015-cli.md)

## Summary

lakeglass is published on crates.io, npm, PyPI, and Homebrew, from GitHub Actions on each release. The registries
forbid packages that only hold a name, so the first release (0.0.1) is a working CLI that lists a store, maps it,
and prints a Parquet file's anatomy.

## Design

| Channel | Package | Contents |
|---------|---------|----------|
| crates.io | `lakeglass` and `lakeglass-*` | The CLI and the library crates |
| npm | `lakeglass` (the browser backend: WASM and TypeScript types) and `@polythings/lakeglass-*` for interface packages | For using the core from other web apps |
| PyPI | `lakeglass` | Python bindings (PyO3, built with maturin) and the CLI binary |
| Homebrew | `polythings/tap/lakeglass` (formula, CLI) and `--cask lakeglass` (Mac app, spec 14) | From the project's own tap, `github.com/polythings/homebrew-tap` |
| GitHub Releases | CLI binaries for macOS, Linux, Windows; the `.dmg` | Built by the release workflow |

- **Release workflow:** a tag builds and publishes everything; the Homebrew formula is generated and pushed to the
  tap. Publishing uses trusted publishing (OIDC from GitHub Actions) on crates.io, npm, and PyPI, so no long-lived
  tokens are stored. crates.io and npm set it up only for packages that exist, so their first publish is done by hand
  with a short-lived token; PyPI takes a pending publisher before the first upload.
- **homebrew-core** accepts formulae only for projects with 75 or more stars (225 when self-submitted) and a 30-day-old
  repository; until then the tap.
- **First release:** 0.0.1 once the CLI does something real (map, anatomy). Name-only placeholders are against the
  crates.io, npm, and PyPI policies.

## Decisions

| ID | Decision | Status |
|---|---|---|
| D35 | Publish on crates.io, npm, PyPI, and Homebrew (own tap first), from CI with trusted publishing. | Accepted |
| D36 | The first published version is a working 0.0.1, not a name placeholder. | Proposed |

## Open questions and issues

- [#8](https://github.com/polythings/lakeglass/issues/8) First publish and registry accounts.
- [#9](https://github.com/polythings/lakeglass/issues/9) Signing for the cask.

## History

- 2026-10-19: proposed after [#8](https://github.com/polythings/lakeglass/issues/8); the domain is not bought yet.
