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

{Optional — pinned overrides for this repo's workflows, so they don't have to be re-asked each run. Delete the section if unused. Examples: default test levels (e.g. `core`, e2e off), orchestrator `parallel_threshold`, default autonomy mode (pause-per-commit / autonomous), the current `{sprint}` label for `docs/artefacts/`.}

## Doc map

Rows whose doc is wrapped in `{}` are optional — they exist only if the project opted in. Drop the row (and the braces) so the table lists only docs that exist.

| Doc | Audience | Form | Content |
| --- | --- | --- | --- |
| `README.md` | user | single | user-facing entry point |
| `AGENTS.md` | coding agent | single | this file: domain, structure, code style, conventions |
| `docs/artefacts/{sprint}/` | agent · developer | stacking (one folder per sprint; type is a filename prefix — `plan_` `spec_` `user-stories_` `e2e_` `e2e-run_` `impl-report_` `handover_` `e2e-report_`) | all workflow run artifacts, bound to their sprint/feature (specs, plans, e2e cases, reports). Committed and frozen after the run; the user decides when a sprint rolls over. `maintain-docs` only updates a spec's Status/ACs — nothing else here |
| {`ASSETS.md`} | coding agent | single | asset inventory (consult before searching the asset tree) |
| {`docs/architecture/`} | developer · agent | self-splitting | planned/implemented architecture: big picture, systems, subsystems |
| {`docs/developer/`} | developer · agent | self-splitting | api reference, guides, key decisions, long-term todos, known bugs |
| {`docs/wiki/`} | user | self-splitting | GitHub wiki source: tutorials & reference (published to the GitHub wiki manually or via a subtree push — set the mechanism per project) |
| {`docs/design/`} | developer | single (`Styleguide.html`) | styleguide / design system (per-feature mockups live with their specs) |
| {`localagent/`} | agent | stacking (one set per run) | localagent-workflow run records (PLAN/STATE/units) — committed, frozen after the run, not maintained |

## Living context

Human-set, committed project state — goals and open decisions. Keep factual and current.
Gotchas and learnings Claude discovers live in **project memory** (the `maintain-memory`
skill), not here.

### Current goals

{what we're working toward right now}

### Open decisions

{undecided questions and their options}
