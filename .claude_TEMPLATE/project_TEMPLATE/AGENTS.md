# AGENTS.md

Agent-agnostic project guide — the single source for domain, structure and code style. `README.md` is for users, this file for contributors and AI agents.

## Domain

{domain of this project}

## Project outline

{what this is, entry points, key modules}

## Build / test / run

- Build: {cmd}
- Test:  {cmd}
- Run:   {cmd}
- Version: {every file + field carrying the version — `release` reads this; delete if the project never publishes}

## Conventions

{defined by the user}

### Code style

{conventions — default to domain conventions}

## Workflow settings

{Optional — this repo's pinned workflow overrides, so no run has to re-ask: default test levels (e.g. `core`, e2e off), default autonomy mode (pause-per-commit / autonomous). Delete the section if unused; the active sprint lives in **Current sprint** below.}

## Doc map

Docs are split by **lifespan**, and every fact has exactly one home:

- **durable** — `docs/`, truth about the shipped system, versioned with the code. Stands on its own: no durable doc links into `artefacts/`, the one exception being `docs/decisions.md`
- **living** — `backlog/`, open work, carried across sprints, editable throughout, dissolved once shipped
- **ephemeral** — `artefacts/`, process memory: live for the running sprint, frozen when `close-sprint` closes it

Rows in `{}` are optional and exist only where the project opted in — drop the row and the braces so the table lists the docs that are actually there.

| Doc | Tier | Content |
| --- | --- | --- |
| `README.md` | — | user-facing entry point |
| `AGENTS.md` | — | this file: domain, structure, code style, conventions |
| {`docs/behaviour.md`} | durable | SSOT for how the *shipped* product behaves — rules, invariants, per-screen interaction contracts. The whole-product overview `plan` reads; written once per sprint, at close. During a sprint its working slice is the sprint file's **Behaviour context**, and the specs' deltas against it |
| {`docs/decisions.md`} | durable | one line per settled decision, newest on top — the cheap read for "what is already decided here?", so agents skip re-litigating. Carries the `Next decision` counter. The rationale lives in the sprint that settled it (`artefacts/{sprint}/sprint-decisions.md`), linked per line — the one durable doc that may point into `artefacts/` |
| {`docs/architecture.md`} | durable | planned/implemented software structure: big picture, systems, subsystems |
| {`docs/dev.md`} | durable | engineering knowledge code/tests/codegraph miss: setup, env, build/debug workflows, dependency quirks (bugs & todos become tickets) |
| {`docs/release.md`} | durable | release runbook: version carriers, build & publish targets, the security gate and its waivers — what this project does *differently* from its domain's release rules, plus its own workflow names and secrets. `release` stops without it (only with releases) |
| {`docs/product/`} | durable | end-user docs (Diátaxis); single source for the wiki/docs-site, published from CI |
| {`docs/design/`} | durable | `Styleguide.html`, the design system (per-feature mockups live with their specs) |
| {`ASSETS.md`} | — | asset inventory — consult it before searching the asset tree |
| `backlog/` | living | one file per ticket (`T-NNN-{slug}.md`): *what* and *why*, category, importance, effort, dependencies — sharpened whenever understanding improves, never frozen. The file stays put from capture until `close-sprint` **dissolves** it; boards only ever index it. `backlog.md` lists what is not yet pulled into a sprint (Draft · Backlog), open decisions included as `decision` tickets, and carries the `Next ticket` counter |
| `artefacts/{sprint}/` | ephemeral | every workflow run artifact, bound to its sprint — type as filename prefix (`spec_` `user-stories_` `e2e_` `e2e-report_` `impl-report_` `handover_`), committed with its run and **correctable until `close-sprint` freezes the whole folder**. Its live core: `sprint.md` (frame with **Behaviour context** + board, one line per ticket with an `open`/`active`/`to test`/`done` token), `sprint-decisions.md` (reasoning per decision settled this sprint, indexed one line each in `docs/decisions.md`) and the `spec_*` behaviour deltas the next spec grounds on |
| {`artefacts/release-{version}/`} | ephemeral | one `release.md` per published version: scope, the composed changelog, gate results, the step state that makes a resumed run safe. Spans the sprints since the last tag, so it sits beside them rather than inside one |
| {`test-dump/`} | ephemeral | gitignored test output — screenshots, videos, traces, framework reports, logs, and any throwaway `e2e` script. Expendable by definition: nothing durable may point into it |
| {`localagent/`} | ephemeral | localagent-workflow run records (PLAN/STATE/units) — frozen with the sprint |

## Current sprint

{the active sprint slug — the current *scope of work*, spanning many runs, all their artifacts in `artefacts/{slug}/`. `open-sprint` sets it and is the only thing that moves it. `none` is valid: `minimal-workflow` fixes and maintenance passes need no sprint.}

<!-- The only living pointer that belongs in AGENTS.md. Other living state has a lifespan-correct
     home: goals → the sprint file; open work & open decisions → `backlog/`;
     gotchas/learnings → project memory (`maintain-memory`). -->
