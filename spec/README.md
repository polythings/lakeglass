# Specs

lakeglass's design, one numbered spec per part. Each spec says what a part does and why, what is still open, and how
far it is built. Start here, people and agents alike: find the spec, read its summary, follow its links. Other tools
in the same space are compared in [landscape.md](landscape.md).

## Statuses

| Status | Meaning |
|---|---|
| Done | Built and in effect; the spec describes how it works now. |
| In progress | Partly built: the spec says which parts are, and the open parts have issues. |
| Planned | Agreed or proposed, not built yet. |
| Dropped | Discussed and not done; kept so the reasons stay on record. |

## Writing and changing a spec

- **When:** anything beyond a small fix gets a spec, or a change to one, agreed before it is built.
- **New spec:** copy [TEMPLATE.md](TEMPLATE.md) to `NNNN-short-name.md` with the next free number. Numbers are never
  reused; a replaced spec is marked Dropped and links its successor.
- **Front matter:** `number`, `title`, `area`, `status` (`done`, `in-progress`, `planned`, `dropped`), `updated`, and
  optionally `related` (spec numbers) and `issues` (issue numbers).
- **Sections:** Summary, Design, Decisions (rows with the next free `D` number), Open questions and issues, History.
- **Open questions** are GitHub issues (label `question`), listed under `issues`, not paragraphs in the spec.
- **Same commit:** when work lands, its spec's status, design, and history change with the code and the changelog,
  and the index below is updated.

## Index

13 specs: 13 planned. Decisions D1–D3 are accepted; the rest are proposed.

### Foundations

| # | Spec | Status | Updated |
|---:|---|---|---|
| 1 | [Purpose and requirements](0001-purpose.md) | Planned | 2026-10-19 |
| 2 | [Architecture](0002-architecture.md) | Planned | 2026-10-19 |
| 11 | [Settings](0011-settings.md) | Planned | 2026-10-19 |
| 12 | [Security and credentials](0012-security.md) | Planned | 2026-10-19 |
| 13 | [First version (v0) and roadmap](0013-v0.md) | Planned | 2026-10-19 |

### Sources

| # | Spec | Status | Updated |
|---:|---|---|---|
| 3 | [Stores and connections](0003-stores.md) | Planned | 2026-10-19 |
| 4 | [Detection](0004-detection.md) | Planned | 2026-10-19 |

### Formats

| # | Spec | Status | Updated |
|---:|---|---|---|
| 5 | [File formats and file anatomy](0005-file-formats.md) | Planned | 2026-10-19 |
| 6 | [Table formats and catalogs](0006-table-formats.md) | Planned | 2026-10-19 |

### Queries

| # | Spec | Status | Updated |
|---:|---|---|---|
| 7 | [Query engines](0007-engines.md) | Planned | 2026-10-19 |
| 8 | [Query trace](0008-query-trace.md) | Planned | 2026-10-19 |

### Insights and interface

| # | Spec | Status | Updated |
|---:|---|---|---|
| 9 | [Checks](0009-checks.md) | Planned | 2026-10-19 |
| 10 | [Interface](0010-interface.md) | Planned | 2026-10-19 |
