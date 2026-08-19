---
name: close-sprint
description: "Use to close the active sprint (release scope): clear the kanban board (finish or carry over) → optional code review of the whole sprint diff → distil and dissolve the done tickets → batched maintain-docs pass → changelog cut → merge/PR to main. Never touches product code. Hands off to open-sprint for the next sprint. Invokable by Claude or via /close-sprint."
---

# Close Sprint

The wrap-up half of the sprint cycle. One run = one **sprint close**: clear the board, optionally review the sprint as a whole, distil what it produced, write the batched docs, integrate to `main`. Close a sprint once its release scope is done — planning the next one is `open-sprint`'s job.

**Leave the product code untouched here.** This skill produces the changelog and the integration, then hands off; review findings go back as recommendations.

## 0. Detect the mode

Read `AGENTS.md` → **Current sprint**. No `AGENTS.md` → run `project-initialiser` first · `none` → nothing to close, go to `open-sprint` · active sprint → continue.

## 1. Clear the board

Read the board in `artefacts/{sprint}/sprint.md`. Every ticket not at **done** needs the user's call — **finish it now, or carry it over**:

- **Carry over** — put the line back into `backlog/backlog.md` (**Backlog**, or **Draft** if the sprint proved it isn't ready) and drop it from the board; the ticket file stays in `backlog/` as always. This is mandatory: the board freezes with the sprint, so anything left on it silently disappears.
- **Finish it** — hand back to `dynamic-`/`minimal-workflow`, then return here.

Then the scope check — **read-light, run nothing new:**

- Every `spec_*` in the sprint folder at **Status: done**?
- Tests green: the unit suite passes and the existing **e2e reports** (`e2e-run_*` / `e2e-report_*`) are green — the existing ones, no fresh run.
- `docs/behaviour.md` current? — **estimate, don't read**: scan the sprint's git history for `behaviour.md` changes matching the code changes. Code moved but behaviour didn't → flag it (behaviour drift is the classic rot). Step 4 covers the other docs.

**Anything missing → STOP.** List exactly what's open and hand the fix back as a recommendation ("spec-X still draft → `dynamic-workflow`", "behaviour drifted → `maintain-docs`").

## 2. Code review *(optional — ask the user)*

The one place the sprint gets judged **as a whole**; individual runs only ever saw their own package. Ask once — recommend it when the sprint shipped real feature work or touched shared/core code, skip it for a tiny or docs-only sprint.

- **Scope:** the cumulative diff against `main` (`main...<sprint-branch>`), not the last commit.
- **How:** the repo's review command (`/code-review`) or `superpowers:requesting-code-review`. Nothing available → review the diff yourself, prioritised: cross-package seams and duplication first (the classic sprint-level defect per-run reviews can't see), then correctness, then the workspace code-style rules.
- **Report, don't repair.** Findings ranked by severity, the user decides. Anything to fix leaves this skill — `minimal-workflow` for a small fix, `dynamic-workflow` if it needs a spec — then come back.

## 3. Distil, then dissolve the board

The board is now all **done**. Each ticket left durable truth behind — graduate it, then the ticket has served its purpose.

**Graduate** (only what isn't recorded yet — most landed during the sprint):

- **`decision` tickets** → a `##` section in `artefacts/{sprint}/sprint-decisions.md` (forces, what was decided, why over the alternatives) plus one `accepted` line in `docs/decisions.md`. Normally written the moment the decision was settled; here you only catch what slipped.
- **Everything user-visible** → a line under `[Unreleased]` in `CHANGELOG.md` (if present), unless the run already added it.
- Behaviour deltas are already in `docs/behaviour.md` — leave them as they are.

**Dissolve** — once that holds, **delete the ticket files of every done ticket** from `backlog/`, and those only; a carried-over ticket is still open and stays. Their lines remain on the frozen board as the record of what shipped, the reasoning lives in the docs it graduated into, git history keeps the rest. This is what keeps `backlog/` bounded.

## 4. Batched docs pass

Invoke **`maintain-docs` in sprint-close mode**: the low-churn docs nobody reads *during* a sprint — `architecture.md`, `dev.md`, `product/`, `ASSETS.md` — written once from the whole sprint at once, which reads better than five separate deltas.

## 5. Record what shipped

- **With a `CHANGELOG.md`** — cut `[Unreleased]` into a dated release section (`## [x.y.z] — YYYY-MM-DD`), leaving a fresh empty `[Unreleased]`. Link the specs.
- **Without one** — the git history (one branch per sprint) *is* the record. Optionally summarise the sprint in the merge/PR body.

## 6. Release / deploy *(reserved)*

- **Integrate per the project's Version Control rules** (CLAUDE.md / AGENTS.md): a **PR to `main`** with the changelog, or a direct merge of the sprint branch — whichever the rules specify.
- ToDo: placeholder for a future `release` skill (build, deploy, tag). For now say it's not wired yet and **pause** — the user runs any release step manually.

## Handoff & boundaries

- **The sprint file freezes here** — frame, board *and* `sprint-decisions.md`. Together they are the sprint's archive, and every board line reads `done` (step 1).
- Produces: the all-done board, the carried-over backlog lines, the dissolved ticket files, any missing entries in `sprint-decisions.md` + `docs/decisions.md`, the batched docs pass, the review findings (if run), the release cut, the merge/PR to `main`.
- Then → **`open-sprint`** for the next one. If the user is done for now, leave **Current sprint** as it is; `open-sprint` moves the pointer.
- Plans, specs, docs and product code belong to `open-sprint`, `spec-design`, `maintain-docs` and the build workflows.
