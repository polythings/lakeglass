# Contributing to lakeglass

This is the code guide for everyone who changes this repository, people and AI agents alike. Formatters and linters
enforce the mechanical rules (see [Checks](#checks)). This guide covers what they can't: how code should read.
[spec/](spec/README.md) holds the design, one spec per part.

## Principles

1. **Readable first.** Someone new should understand a function from its name, its signature, and one pass through
   the body.
2. **Idiomatic.** Rust should read like Rust and TypeScript like TypeScript, not like Java.
3. **Modular.** Each crate has one job and a small public API. Each file format, table format, and engine is its own
   crate behind a shared trait (architecture.md), so adding one never touches the others.
4. **One definition of each thing.** Before writing a helper, constant, or type, look for an existing one.
5. **Behaviour, spec, and tests change together.** A change users can see updates its spec, adds or updates tests, and
   adds a line to [CHANGELOG.md](CHANGELOG.md).
6. **Writes are explicit.** Nothing writes to a user's store outside write mode, and every write shows a preview and
   asks for confirmation (spec 11).
7. **Every check and every costly read is a setting.** It has a name for the mechanism it changes
   (`scan.sample_files`, not `scan.quick`), and docs saying what it costs and why its default is what it is.

## Comments and docs

- **Public items get a short doc comment**: what it does, and anything a caller must know (errors, units,
  invariants). One to three lines is usually enough.
- **Comments explain why, not what.** Explain a non-obvious choice, a workaround, or a number that came from a
  measurement.
- **No long comments.** If an explanation needs a page, it belongs in a spec. Link to it from the code.

## Credit

lakeglass learns from other projects' ideas, never from copying their code. [PRIOR_ART.md](PRIOR_ART.md) lists the
ideas we took and the projects we're studying.

- **Never copy code**, whatever its license: not by pasting, and not by translating it into another language.
- **Credit ideas from open-source projects** with a row in PRIOR_ART.md and a short comment where the idea is
  implemented.
- **Don't name open-core, dual-licensed, or source-available projects, or commercial products, as a source.** Naming
  them in comparisons is fine.

## Rust

- **Errors:** `thiserror` enums in libraries, `anyhow` in binaries. Keep the cause with `#[from]` or `#[source]`. No
  `unwrap()` outside tests.
- **Types over strings:** enums and newtypes instead of strings and magic numbers. Parse once at the boundary.
- **Don't trust files.** Every reader treats bytes from a store as untrusted: check lengths and offsets before using
  them, cap allocations, and return an error instead of panicking on a malformed file.
- **Builds for the browser.** Crates the browser uses build for `wasm32-unknown-unknown`: no threads, no blocking
  I/O, no `std::time::Instant`, no C dependencies without a WASM build. Native-only code sits behind a feature.
- **Reads are ranged and counted.** Read footers and metadata with range requests; go through the instrumented store
  so every request is counted (spec 2).
- **Async:** never block inside async code. Don't hold a `Mutex` across an `.await`.
- **Suppressing a lint:** `#[expect(lint, reason = "...")]` with a real reason. Never `#[allow]`.

## TypeScript

- The interface is Next.js with a static export: no route handlers, server actions, or server-rendered dynamic
  routes. It must work on the website, inside the Mac app, and from the CLI.
- Views talk to the backend interface only, never to WASM, Tauri, or HTTP directly.
- Strict mode, no `any`. Types for API responses are generated from the Rust types, not written twice.
- Components are small and named for what they show. Data fetching stays out of drawing code.
- Large tables and file maps are virtualized; never render one DOM node per row or per page of a big file.

## Tests

- **A bug fix starts with a test that fails**, then the fix.
- **Tests run offline** against fixture tables in `fixtures/` or ones the test writes to a temporary directory.
- **Malformed input is tested:** truncated files, wrong lengths, and garbage footers must produce errors, not panics.

## Checks

Install once: `uv tool install prek && prek install`. Hooks then run on commit and on push. Before you push, or before
calling work done:

```bash
prek run --all-files --hook-stage pre-push   # every formatter and linter
```

The test commands are added with the first code.

## Dependencies

Keep dependencies on their newest versions; check the latest release when adding one. Actions are pinned to a commit
with the version in a comment (`uses: actions/checkout@<sha> # v7.0.1`).

## Commits

- **Subject:** a short imperative sentence with the area first, for example `Detect: …`, `Parquet: …`, `Iceberg: …`,
  `Engine: …`, `UI: …`, or `Spec: …`. Add a body when the why isn't obvious.
- **Scope:** one logical change per commit.
- **Secrets:** never commit credentials or `.env*` files.

## Changelog

[CHANGELOG.md](CHANGELOG.md) is for users, not a list of commits. Anything a user can notice gets one line under
`## Unreleased`, in `Added`, `Changed`, `Deprecated`, `Removed`, `Fixed`, or `Security`, in the same commit as the
change.

## License

Contributions are licensed under the Apache License 2.0, like the rest of the project (see [LICENSE](LICENSE)).
