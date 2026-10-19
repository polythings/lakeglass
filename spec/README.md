# Specs

lakeglass's design, in the spirit of Rust's RFCs and Python's PEPs, laid out like Timelake's. Start here, people and
agents alike.

- **Specs** (`NNNN-name.md`): one feature each, with the same template. A spec says what the feature does and why,
  what is still open (as GitHub issues), how far it is built, and the releases that changed it.
- **Milestones** ([`milestones/`](milestones/README.md)): goals made of specs, such as v0. The roadmap is the list of
  milestones; a spec can be part of more than one.
- **Background** (`name.md`, listed below the index): what isn't a feature: purpose and requirements, architecture,
  security, and the landscape.

## Statuses

| Status | Meaning |
|---|---|
| Done | Built and in effect; the spec describes how it works now. |
| In progress | Partly built: the spec says which parts are, and the open parts have issues. |
| Planned | Agreed or proposed, not built yet. |
| Dropped | Discussed and not done; kept so the reasons stay on record. |

Milestones are done, in progress, planned, or exploring (an idea being looked into, which may not happen).

## Writing and changing a spec

- **When:** anything beyond a small fix gets a spec, or a change to one, agreed before it is built. A spec is one
  feature someone could ask for by name; a theme or a version is a milestone.
- **New spec:** copy [TEMPLATE.md](TEMPLATE.md) to `NNNN-short-name.md` with the next free number (numbers are never
  reused; a replaced spec is marked Dropped and links its successor), and add it to a milestone.
- **Front matter:** `number`, `title`, `area` (one of the groups below), `status` (`done`, `in-progress`, `planned`,
  `dropped`), `updated`, `versions` (the releases that changed it, oldest first; `unreleased` until a release names
  it), and optionally `related` (spec numbers) and `issues` (issue numbers).
- **Sections:** Summary (a few sentences), Design, Decisions (rows with the next free `D` number), Open questions and
  issues, History (dated, newest last).
- **Links:** cite another spec as "spec 6", linked to its file.
- **Open questions** are GitHub issues (label `question`), listed under `issues`, not paragraphs in the spec. Tasks
  that aren't designs are issues too.
- **Same commit:** when work lands, its spec's status, versions, and history change with the code and the changelog,
  and the index below and the milestone's table are updated. (An index script, as in Timelake, comes with the first
  code.)

## Index

18 specs: 18 planned. Decisions D1–D36; accepted ones are marked in each spec.

### Stores

| # | Spec | Status | Updated |
|---:|---|---|---|
| 1 | [Store connections](0001-connections.md) | Planned | 2026-10-19 |
| 2 | [Request counting and cache](0002-request-counting.md) | Planned | 2026-10-19 |
| 3 | [Store map and format detection](0003-detection.md) | Planned | 2026-10-19 |
| 4 | [Store sizes from inventory reports](0004-inventory.md) | Planned | 2026-10-19 |

### Formats

| # | Spec | Status | Updated |
|---:|---|---|---|
| 5 | [File anatomy and rows](0005-file-anatomy.md) | Planned | 2026-10-19 |
| 6 | [Table explorer](0006-table-explorer.md) | Planned | 2026-10-19 |
| 7 | [Catalog connections](0007-catalogs.md) | Planned | 2026-10-19 |
| 17 | [Proprietary warehouses](0017-warehouses.md) | Planned | 2026-10-19 |

### Queries

| # | Spec | Status | Updated |
|---:|---|---|---|
| 8 | [SQL with pluggable engines](0008-sql-engines.md) | Planned | 2026-10-19 |
| 9 | [Query trace](0009-query-trace.md) | Planned | 2026-10-19 |

### Insights

| # | Spec | Status | Updated |
|---:|---|---|---|
| 10 | [Checks](0010-checks.md) | Planned | 2026-10-19 |
| 11 | [Write mode](0011-write-mode.md) | Planned | 2026-10-19 |

### Interface

| # | Spec | Status | Updated |
|---:|---|---|---|
| 12 | [Settings](0012-settings.md) | Planned | 2026-10-19 |
| 16 | [Format explainers](0016-format-explainers.md) | Planned | 2026-10-19 |

### Apps

| # | Spec | Status | Updated |
|---:|---|---|---|
| 13 | [Browser app](0013-browser-app.md) | Planned | 2026-10-19 |
| 14 | [Mac app](0014-mac-app.md) | Planned | 2026-10-19 |
| 15 | [CLI and local server](0015-cli.md) | Planned | 2026-10-19 |
| 18 | [Packages and installers](0018-packages.md) | Planned | 2026-10-19 |

## Background

| Page | What it covers |
|---|---|
| [Purpose and requirements](purpose.md) | Requirements R1–R17 as stated, goals, non-goals, terms |
| [Architecture](architecture.md) | The interface, the Rust core, the backends, what runs where, extension points |
| [Security and credentials](security.md) | Where credentials go, the local server, untrusted files |
| [Landscape](landscape.md) | Other tools in the same space |
