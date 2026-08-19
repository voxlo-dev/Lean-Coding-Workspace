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

## Workflow settings

{Optional — pinned overrides for this repo's workflows, so they don't have to be re-asked each run. Delete the section if unused. Examples: default test levels (e.g. `core`, e2e off), orchestrator `parallel_threshold`, default autonomy mode (pause-per-commit / autonomous). (The active sprint is tracked in the **Current sprint** section below, not here.)}

## Doc map

Docs are split by **lifespan**: `docs/` is DURABLE (truth about the shipped system, versioned with the code); `backlog/` is LIVING (open work, carried across sprints, dissolved once shipped); `artefacts/` is EPHEMERAL (process/working memory, frozen per sprint). Every fact has one home. Rows wrapped in `{}` are optional — they exist only if the project opted in; drop the row (and braces) so the table lists only docs that exist.

| Doc | Tier | Audience | Form | Content |
| --- | --- | --- | --- | --- |
| `README.md` | — | user | single | user-facing entry point |
| `AGENTS.md` | — | coding agent | single | this file: domain, structure, code style, conventions |
| {`docs/behaviour.md`} | durable | agent · developer | self-splitting | SSOT for how the *shipped* product behaves — product semantics as a rulebook (rules, invariants, per-screen interaction contracts). Specs are deltas against this |
| {`docs/decisions.md`} | durable | agent · developer | append-only index | one line per settled decision, newest on top — the single cheap read for "what is already decided here?", so agents don't re-litigate. The rationale lives in the sprint that settled it (`artefacts/{sprint}/sprint-decisions.md`), linked from each line |
| {`docs/architecture.md`} | durable | developer · agent | self-splitting | planned/implemented software structure: big picture, systems, subsystems |
| {`docs/dev.md`} | durable | developer · agent | self-splitting | engineering knowledge code/tests/codegraph don't capture: setup, env, build/debug workflows, dependency quirks (not bugs/todos — those become tickets) |
| {`docs/product/`} | durable | user | self-splitting (Diátaxis) | end-user docs; single source for the wiki/docs-site (published from CI, never hand-edited) |
| {`docs/design/`} | durable | developer | single (`Styleguide.html`) | styleguide / design system (per-feature mockups live with their specs) |
| {`ASSETS.md`} | — | coding agent | single | asset inventory (consult before searching the asset tree) |
| {`CHANGELOG.md`} | durable | user | append-only | user-facing changelog, per release (only if the project has releases/external users) |
| `backlog/` | living | agent · developer | one file per ticket (`T-NNN-{slug}.md`) + `backlog.md` as its index | every unit of work as a mini-plan: *what* and *why*, category, importance, effort, dependencies. Written once, then frozen — no status field, the board position is the status. A ticket file stays here from capture until `close-sprint` **dissolves** it (deleted once its truth graduated); boards only ever index it. `backlog.md` lists what is not yet pulled into a sprint (Draft · Backlog), including open decisions as `decision` tickets |
| `artefacts/{sprint}/` | ephemeral | agent · developer | stacking (one folder per sprint; type is a filename prefix — `spec_` `user-stories_` `e2e_` `e2e-run_` `impl-report_` `handover_` `e2e-report_`) plus `sprint.md` and `sprint-decisions.md` | all workflow run artifacts, bound to their sprint/feature. Committed and frozen after the run — **except** two living files: `sprint.md` (the sprint file: frame + kanban board, one line per ticket with an `open`/`active`/`to test`/`done` status token) and `sprint-decisions.md` (the reasoning behind every decision this sprint settled, appended the moment it is settled, indexed one line each in `docs/decisions.md`). Both keep moving until `close-sprint` freezes them with the sprint. `open-sprint` opens each sprint, `close-sprint` closes it. `maintain-docs` only touches a spec's Status/ACs and appends to `sprint-decisions.md` — durable truth is distilled into `docs/`, never left here |
| {`localagent/`} | ephemeral | agent | stacking (one set per run) | localagent-workflow run records (PLAN/STATE/units) — committed, frozen after the run, not maintained |

## Current sprint

{the active sprint slug — the current *release scope*, spanning many runs; all their artifacts live in `artefacts/{slug}/`. `open-sprint` sets it and is the only thing that moves it. `none` is valid: `minimal-workflow` fixes and maintenance passes need no sprint.}

<!-- The only living pointer that belongs in AGENTS.md. Other living state has a lifespan-correct
     home: goals → the sprint file; open work & open decisions → `backlog/`;
     gotchas/learnings → project memory (`maintain-memory`). -->

