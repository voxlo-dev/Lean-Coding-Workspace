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

- `README.md` — user-facing entry point
- `AGENTS.md` — this file: domain, structure, code style, conventions
{- `ASSETS.md` — asset inventory (consult before searching the asset tree)}
{- `CHANGELOG.md` — notable changes, major features only}
- `docs/specs/` — feature specs (spec-workflow); one file per invocation
{- `docs/architecture/` — the architecture document(s); big-picture, systems, subsystems}
{- `docs/wiki/` — source for the GitHub wiki (user-faced tutorials/reference)}
{- `docs/design/` — styleguide / design system (per-feature mockups live with their specs)}

## Living context

Human-set, committed project state — goals and open decisions. Keep factual and current.
Gotchas and learnings Claude discovers live in **project memory** (the `maintain-memory`
skill), not here.

### Current goals

{what we're working toward right now}

### Open decisions

{undecided questions and their options}
