# Agent instructions

lakeglass: a read-only dashboard for exploring data lakes, lakehouses, and plain object storage. The project is in
design: [spec/](spec/README.md) has one spec per part, with its status. Open questions are GitHub issues.

## Before changing code

- Follow [CONTRIBUTING.md](CONTRIBUTING.md). It is the code guide, and it applies to you.
- For anything beyond a small fix, check the specs. Agree on the design with the user before building something new,
  and record the decision in the spec's Decisions table.

## Rules

- **Done means checked.** Before you report work as finished, run `prek run --all-files --hook-stage pre-push` and
  the tests for what you touched. Report failures as failures.
- **Fix the code, not the checks.** Don't loosen lint, type, or format configs. Don't add `#[allow]` or
  `oxlint-disable` to get a check passing. Use `#[expect(..., reason = "...")]` only when the tool is genuinely wrong.
- **Keep things in sync:** a change in behaviour updates its spec (status, design, history), the tests, and
  `CHANGELOG.md` (under `Unreleased`) in the same commit.
- **Read-only.** Nothing in lakeglass writes, deletes, or rewrites objects in a user's store (spec 2, D4).
- **Every check and every costly read is a setting**, named for its mechanism (spec 11).
- **Ideas, not code.** Never copy or translate another project's code. Credit ideas in `PRIOR_ART.md`
  (CONTRIBUTING.md, "Credit").
- **Tests run offline.** No network, cloud accounts, or real buckets in tests; use small fixture tables built by the
  tests themselves or checked into `fixtures/`.
- **Never commit secrets** or `.env*` files. Don't put credentials in fixtures, logs, or screenshots.
- **Don't run Prettier or ESLint.** Formatting is oxfmt and rustfmt, linting oxlint and clippy.
