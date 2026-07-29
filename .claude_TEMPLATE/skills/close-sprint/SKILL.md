---
name: close-sprint
description: "Use to close the active sprint (release scope): scope check → optional code review of the whole sprint diff → changelog cut → merge/PR to main. Never touches product code. Hands off to open-sprint for the next sprint. Invokable by Claude or via /close-sprint."
---

# Close Sprint

The wrap-up half of the sprint cycle. One run = one **sprint close**: verify the release
scope is actually done, optionally review it as a whole, record what shipped, integrate to
`main`. Planning the next sprint is `open-sprint`'s job — not this one.

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

## 1. Scope check — read-light, run nothing new

- Every `spec_*` in the sprint folder at **Status: done**?
- Tests green: the unit suite passes and the existing **e2e reports** (`e2e-run_*` / `e2e-report_*`) are green — **do not start a new e2e run**.
- Docs current? — **estimate, don't read**: scan the sprint's git history for `docs/` changes that match the code changes. Code moved but `docs/behaviour.md` didn't → flag it (behaviour drift is the classic rot).
- `docs/decisions.md` — no entries still stuck at Status `proposed` that this sprint should have resolved?

**Anything missing → STOP.** List exactly what's open and recommend the fix (e.g. "spec-X still draft → `dynamic-workflow`"; "behaviour drifted → `maintain-docs`"; "decision NNNN still `proposed` → resolve with the user"). **Never auto-fix** — hand the recommendation back.

## 2. Code review *(optional — ask the user)*

The one place where the sprint gets judged **as a whole**: individual runs only ever saw
their own package. Ask once — recommend it when the sprint shipped real feature work or
touched shared/core code; skip it for a tiny or docs-only sprint.

- **Scope:** the sprint's cumulative diff against `main` (`main...<sprint-branch>`), not the last commit.
- **How:** run the repo's review command (`/code-review`) or `superpowers:requesting-code-review`. Nothing available → review the diff yourself, prioritised: cross-package seams and duplication first (the classic sprint-level defect — per-run reviews can't see it), then correctness, then the workspace code-style rules.
- **Report, don't repair.** Present the findings ranked by severity and let the user decide. Anything they want fixed leaves this skill: `minimal-workflow` for a small fix, `dynamic-workflow` if it needs a spec. Come back here afterwards.

## 3. Record what shipped

- **If the project has a `CHANGELOG.md`** — cut its `[Unreleased]` section into a dated release section (`## [x.y.z] — YYYY-MM-DD`), leaving a fresh empty `[Unreleased]`. Link the specs.
- **If it doesn't** — the git history (Conventional Commits, one branch per sprint) *is* the record; write no changelog file. Optionally summarise the sprint in the merge/PR body instead.

## 4. Release / deploy *(reserved)*

- **Integrate per the project's Version Control rules** (CLAUDE.md / AGENTS.md): open a **PR to `main`** with the changelog, **or merge the sprint branch directly to `main`**. Follow whichever the rules specify.
- ToDo: Placeholder for a future `release` skill (build, deploy, tag). For now: note it's not wired
  yet and **pause** — let the user run any release step manually.

## Handoff & boundaries

- Produces: the review findings (if run), the release cut in `CHANGELOG.md` (if present), the merge/PR to `main`.
- Then → **`open-sprint`** to plan and open the next one. If the user is done for now, leave **Current sprint** as it is; `open-sprint` moves the pointer.
- Never writes plans, specs, docs, or product code — those belong to `open-sprint`, `spec-design`, `maintain-docs`, and the build workflows.
