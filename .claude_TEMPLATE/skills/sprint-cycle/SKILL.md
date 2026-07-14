---
name: sprint-cycle
description: "Use to manage the whole sprint cycle: close the active sprint (scope check → changelog → merge/PR to main), then plan the next one via the `plan` skill (sprint-plan mode: architecture-aware, optional web research, possible domain switch). Also runs for a brand-new project (planning only, no sprint to close). The thinking layer above the build workflows; hands off to dynamic-workflow. Invokable by Claude or via /sprint-cycle."
---

# Sprint Cycle

The thinking layer above the build workflows. One run = one **sprint transition**: wrap up
the active sprint, then plan the next. Architecture *decisions* live here; the architecture
*doc* does not — `maintain-docs` fills that once things are actually implemented.

**A sprint** groups a batch of related work under one `artefacts/{sprint}/` folder and
one branch. Its length is the **user's call** — no time-boxes, no story points. A typical
sprint holds ~1 sprint plan, 1–3 feature plans, 1–5 specs (1–10 packages each), 0–3 e2e
runs, several minimal-workflow fixes, and a few maintain-docs passes. **No sprint at all is
fine** — a lone `minimal-workflow` fix or a maintenance pass needs none.

**Change nothing in the product code here** — this skill produces the changelog, the sprint
plan, the branch, and the living-context update, then hands off.

## 0. Detect the mode

Read `AGENTS.md` → **Current sprint**.

- **Not scaffolded yet** (no `AGENTS.md`) → run `project-initialiser` first, then come back here for the first sprint.
- **Active sprint exists** → full cycle: step 1 → 2 → 3 → 4.
- **New project, or no active sprint** → skip to step 3 (planning). A brand-new project runs the
  planning wide — deep brainstorm + research + domain/stack — but writes a sprint plan, not an
  architecture doc.

## 1. Close the active sprint

**Scope check — read-light, run nothing new:**

- Every `spec_*` in the sprint folder at **Status: done**?
- Tests green: the unit suite passes and the existing **e2e reports** (`e2e-run_*` / `e2e-report_*`) are green — **do not start a new e2e run**.
- Docs current? — **estimate, don't read**: scan the sprint's git history for `docs/` changes that match the code changes. Code moved but `docs/behaviour.md` didn't → flag it (behaviour drift is the classic rot).
- `docs/decisions.md` — no entries still stuck at Status `proposed` that this sprint should have resolved?

**Anything missing → STOP.** List exactly what's open and recommend the fix (e.g. "spec-X still draft → `dynamic-workflow`"; "behaviour drifted → `maintain-docs`"; "decision NNNN still `proposed` → resolve with the user"). **Never auto-fix** — hand the recommendation back.

**All clear — record what shipped:**

- **If the project has a `CHANGELOG.md`** — cut its `[Unreleased]` section into a dated release section (`## [x.y.z] — YYYY-MM-DD`), leaving a fresh empty `[Unreleased]`. Link the specs.
- **If it doesn't** — the git history (Conventional Commits, one branch per sprint) *is* the record; write no changelog file. Optionally summarise the sprint in the merge/PR body instead.

## 2. Release / deploy *(reserved)*

- **Integrate per the project's Version Control rules** (CLAUDE.md / AGENTS.md): open a **PR to `main`** with the changelog, **or merge the sprint branch directly to `main`**. Follow whichever the rules specify.

- ToDo: Placeholder for a future `release` skill (build, deploy, tag). For now: note it's not wired
yet and **pause** — let the user run any release step manually before planning the next sprint.

## 3. Plan the next sprint

Invoke **`plan` in sprint-plan mode** — it owns the planning dialogue, the architecture/domain
decisions, the optional web research, and (new project) the styleguide-level `ui-design` call. It
writes `artefacts/{sprint}/sprint-plan.md`: the sprint's umbrella scope — goals, the
architecture decisions, and the batch of features/fixes in scope.

- **Ground it** for `plan`: codegraph if indexed, `AGENTS.md`, `docs/architecture.md` (draft or filled), `docs/behaviour.md` and `docs/decisions.md` if present, project memory. For a brand-new project, note the target instead and run the planning **wide** (deep brainstorm + research + domain/stack).
- **A complex plan may already exist** (pasted from a Claude chat, or a `plan_*` *Lastenheft*) → feed it to `plan` as the basis.

`spec-design` later formalises the sprint plan into specs; `dynamic-workflow` derives specs directly
from it. Get the user's approval on the plan before moving on.

## 4. Open the branch & update living context

- **New sprint branch** — create `<sprint-slug>` (matches the workspace branching rule: one branch per sprint). Ask the user for the slug if unclear.
- **`AGENTS.md`** — set **Current sprint** to the new slug (the only living pointer here). Goals live in the `sprint-plan.md`, not in `AGENTS.md`.
- **`docs/decisions.md`** (if the project has it) — record the sprint's **architecture decisions** as entries (Status `accepted`), and seed anything still unresolved as Status `proposed`. This is the home for decisions; the sprint plan references them, it doesn't duplicate them.

Then hand off to `dynamic-workflow` (spec each feature from the sprint plan) — or `minimal-workflow` for the small stuff.

## Handoff & boundaries

- Produces: the release cut in `CHANGELOG.md` (at close, if present), decision entries in `docs/decisions.md`, `sprint-plan.md` (via `plan`), the branch, the `Current sprint` update.
- Never writes the architecture/behaviour docs, specs, or product code — those belong to `maintain-docs`, `spec-design`, and the build workflows. `maintain-memory` runs at the workflows' memory step, not here.
