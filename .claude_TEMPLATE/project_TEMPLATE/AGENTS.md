# AGENTS.md

Agent-agnostic project guide. Single source of truth for domain, structure, and code style. README.md is for users; this file is for contributors and AI agents.

## Domain

{domain of this project}

## Project outline

{what this is, entry points, key modules}

## Build / test / run

- Build: {cmd}
- Test:  {cmd}
- Run:   {cmd}

## Conventions

{defined by the user}

### Code style

{conventions — default to domain conventions}

## Doc map

Rows whose doc is wrapped in `{}` are optional — they exist only if the project opted in. Drop the row (and the braces) so the table lists only docs that exist.

| Doc | Audience | Form | Content |
| --- | --- | --- | --- |
| `README.md` | user | single | user-facing entry point |
| `AGENTS.md` | coding agent | single | this file: domain, structure, code style, conventions |
| `docs/specs/` | agent · developer | stacking (one file per feature) | feature specs (dynamic-workflow) |
| {`ASSETS.md`} | coding agent | single | asset inventory (consult before searching the asset tree) |
| {`docs/architecture/`} | developer · agent | self-splitting | planned/implemented architecture: big picture, systems, subsystems |
| {`docs/developer/`} | developer · agent | self-splitting | api reference, guides, key decisions, long-term todos, known bugs |
| {`docs/wiki/`} | user | self-splitting | GitHub wiki source: tutorials & reference |
| {`docs/design/`} | developer | single (`Styleguide.html`) | styleguide / design system (per-feature mockups live with their specs) |

## Living context

Human-set, committed project state — goals and open decisions. Keep factual and current.
Gotchas and learnings Claude discovers live in **project memory** (the `maintain-memory`
skill), not here.

### Current goals

{what we're working toward right now}

### Open decisions

{undecided questions and their options}
