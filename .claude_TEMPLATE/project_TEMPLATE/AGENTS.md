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

Docs are split by **lifespan**: `docs/` is DURABLE (truth about the shipped system, versioned with the code); `artefacts/` is EPHEMERAL (process/working memory, frozen per sprint). Every fact has one home. Rows wrapped in `{}` are optional — they exist only if the project opted in; drop the row (and braces) so the table lists only docs that exist.

| Doc | Tier | Audience | Form | Content |
| --- | --- | --- | --- | --- |
| `README.md` | — | user | single | user-facing entry point |
| `AGENTS.md` | — | coding agent | single | this file: domain, structure, code style, conventions |
| {`docs/behaviour.md`} | durable | agent · developer | self-splitting | SSOT for how the *shipped* product behaves — product semantics as a rulebook (rules, invariants, per-screen interaction contracts). Specs are deltas against this |
| {`docs/decisions.md`} | durable | agent · developer | append-only ledger | ADR-lite decision log — one home for every architecture/engineering/product decision + rationale; agents don't re-litigate settled choices |
| {`docs/architecture.md`} | durable | developer · agent | self-splitting | planned/implemented software structure: big picture, systems, subsystems |
| {`docs/dev.md`} | durable | developer · agent | self-splitting | engineering knowledge code/tests/codegraph don't capture: setup, env, build/debug workflows, dependency quirks (not bugs/todos — those go to the issue tracker) |
| {`docs/product/`} | durable | user | self-splitting (Diátaxis) | end-user docs; single source for the wiki/docs-site (published from CI, never hand-edited) |
| {`docs/design/`} | durable | developer | single (`Styleguide.html`) | styleguide / design system (per-feature mockups live with their specs) |
| {`ASSETS.md`} | — | coding agent | single | asset inventory (consult before searching the asset tree) |
| {`CHANGELOG.md`} | durable | user | append-only | user-facing changelog, per release (only if the project has releases/external users) |
| `artefacts/{sprint}/` | ephemeral | agent · developer | stacking (one folder per sprint; type is a filename prefix — `plan_` `spec_` `user-stories_` `e2e_` `e2e-run_` `impl-report_` `handover_` `e2e-report_`) plus the sprint-level `sprint-plan.md` | all workflow run artifacts, bound to their sprint/feature, under the umbrella `sprint-plan.md`. Committed and frozen after the run; `sprint-cycle` opens each sprint and closes it (changelog/git history). `maintain-docs` only updates a spec's Status/ACs — durable truth is distilled into `docs/`, never left here |
| {`localagent/`} | ephemeral | agent | stacking (one set per run) | localagent-workflow run records (PLAN/STATE/units) — committed, frozen after the run, not maintained |

## Current sprint

{the active sprint slug — its run artifacts live in `artefacts/{slug}/`; `sprint-cycle` sets it. `none` is valid: `minimal-workflow` fixes and maintenance passes need no sprint.}

<!-- This is the only living pointer that belongs in AGENTS.md. Everything else that used to
     sit here now has a lifespan-correct home: current goals → the sprint's `sprint-plan.md`;
     open decisions → `docs/decisions.md` entries with Status `proposed`; gotchas/learnings →
     project memory (`maintain-memory`). -->

