---
name: close-sprint
description: "Use to close the active sprint (release scope): clear the kanban board (finish or carry over) → optional code review of the whole sprint diff → distil Done tickets → batched maintain-docs pass → changelog cut → merge/PR to main. Never touches product code. Hands off to open-sprint for the next sprint. Invokable by Claude or via /close-sprint."
---

# Close Sprint

The wrap-up half of the sprint cycle. One run = one **sprint close**: clear the board,
optionally review the sprint as a whole, distil what it produced, write the batched docs,
integrate to `main`. Planning the next sprint is `open-sprint`'s job — not this one.

**A sprint is a release scope, not a run.** It is the whole batch of work that ships
together, under one `artefacts/{sprint}/` folder and one branch — spanning **many workflow
runs**. Close it only when that release scope is done, never per run or per feature.

**Change nothing in the product code here** — this skill produces the changelog and the
integration, then hands off. Review findings are handed back as recommendations, never fixed here.

## 0. Detect the mode

Read `AGENTS.md` → **Current sprint**.

- **Not scaffolded yet** (no `AGENTS.md`) → run `project-initialiser` first.
- **No active sprint** (`none`) → nothing to close; go straight to `open-sprint`.
- **Active sprint exists** → continue.

## 1. Clear the board

Read the sprint file's board (`artefacts/{sprint}/sprint-plan.md`). Every ticket left in
**Active** or **To Test** needs a call from the user — **finish it now, or carry it over**:

- **Carry over** — move the line back into `backlog.md` (**Backlog**, or **Draft** if the sprint proved it isn't ready). The ticket file itself never moves. This is mandatory: the board freezes with the sprint, so anything left on it silently disappears.
- **Finish it** — hand back to `dynamic-`/`minimal-workflow` and come back here.

Then the scope check — **read-light, run nothing new:**

- Every `spec_*` in the sprint folder at **Status: done**?
- Tests green: the unit suite passes and the existing **e2e reports** (`e2e-run_*` / `e2e-report_*`) are green — **do not start a new e2e run**.
- `docs/behaviour.md` current? — **estimate, don't read**: scan the sprint's git history for `behaviour.md` changes matching the code changes. Code moved but behaviour didn't → flag it (behaviour drift is the classic rot). The *other* docs are not checked here — step 4 writes them.

**Anything missing → STOP.** List exactly what's open and recommend the fix (e.g. "spec-X still draft → `dynamic-workflow`"; "behaviour drifted → `maintain-docs`"). **Never auto-fix** — hand the recommendation back.

## 2. Code review *(optional — ask the user)*

The one place where the sprint gets judged **as a whole**: individual runs only ever saw
their own package. Ask once — recommend it when the sprint shipped real feature work or
touched shared/core code; skip it for a tiny or docs-only sprint.

- **Scope:** the sprint's cumulative diff against `main` (`main...<sprint-branch>`), not the last commit.
- **How:** run the repo's review command (`/code-review`) or `superpowers:requesting-code-review`. Nothing available → review the diff yourself, prioritised: cross-package seams and duplication first (the classic sprint-level defect — per-run reviews can't see it), then correctness, then the workspace code-style rules.
- **Report, don't repair.** Present the findings ranked by severity and let the user decide. Anything they want fixed leaves this skill: `minimal-workflow` for a small fix, `dynamic-workflow` if it needs a spec. Come back here afterwards.

## 3. Distil the Done column

The board is now all **Done**. Each of those tickets left durable truth behind — graduate it,
then the ticket has served its purpose:

- **`decision` tickets** → an entry in `docs/decisions.md`, Status `accepted`, referencing the ticket. This is the only way a decision enters that doc.
- **Everything user-visible** → a line under `[Unreleased]` in `CHANGELOG.md` (if present), unless the run already added it.
- Behaviour deltas are already in `docs/behaviour.md` (written per run) — don't rewrite them here.

## 4. Batched docs pass

Invoke **`maintain-docs` in sprint-close mode**: the low-churn docs nobody reads *during* a
sprint — `architecture.md`, `dev.md`, `product/`, `ASSETS.md` — get written once, from the
whole sprint's changes at once. Written better this way than as five separate deltas.

## 5. Record what shipped

- **If the project has a `CHANGELOG.md`** — cut its `[Unreleased]` section into a dated release section (`## [x.y.z] — YYYY-MM-DD`), leaving a fresh empty `[Unreleased]`. Link the specs.
- **If it doesn't** — the git history (Conventional Commits, one branch per sprint) *is* the record; write no changelog file. Optionally summarise the sprint in the merge/PR body instead.

## 6. Release / deploy *(reserved)*

- **Integrate per the project's Version Control rules** (CLAUDE.md / AGENTS.md): open a **PR to `main`** with the changelog, **or merge the sprint branch directly to `main`**. Follow whichever the rules specify.
- ToDo: Placeholder for a future `release` skill (build, deploy, tag). For now: note it's not wired
  yet and **pause** — let the user run any release step manually.

## Handoff & boundaries

- **The sprint file freezes here** — plan *and* board. It is the sprint's archive; nothing moves it afterwards, and no ticket may be left on it (step 1).
- Produces: the cleared board, the carried-over backlog lines, decision entries in `docs/decisions.md`, the batched docs pass, the review findings (if run), the release cut in `CHANGELOG.md` (if present), the merge/PR to `main`.
- Then → **`open-sprint`** to plan and open the next one. If the user is done for now, leave **Current sprint** as it is; `open-sprint` moves the pointer.
- Never writes plans, specs, docs, or product code — those belong to `open-sprint`, `spec-design`, `maintain-docs`, and the build workflows.
