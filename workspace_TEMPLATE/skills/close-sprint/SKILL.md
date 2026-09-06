---
name: close-sprint
description: "Use to close the active sprint: clear the kanban board (finish or carry over) → optional whole-sprint code review → distil and dissolve the done tickets → batched maintain-docs pass, behaviour.md first → merge to main. Never touches product code, never publishes — that's `release`. Hands off to open-sprint for the next sprint. Invokable directly or via /close-sprint."
---

# Close Sprint

The wrap-up half of the sprint cycle. One run = one **sprint close**: clear the board, optionally review the sprint as a whole, distil what it produced, write the batched docs, integrate to `main`. Close a sprint once its scope is done — planning the next one is `open-sprint`'s job.

**A sprint integrates, it does not publish.** A version spans however many sprints it needs; `release` cuts it from `main` after. Nothing here bumps a version, writes a changelog or tags.

**Leave the product code untouched here.** This skill produces the distilled docs and the integration, then hands off; review findings go back as recommendations.

## 0. Detect the mode

Read `AGENTS.md` → **Current sprint**. No `AGENTS.md` → run `project-initialiser` first · `none` → nothing to close, go to `open-sprint` · active sprint → continue.

## 1. Clear the board

Read the board in `artefacts/{sprint}/sprint.md`. Every ticket not at **done** needs the user's call — **finish it now, or carry it over**:

- **Carry over** — put the line back into `backlog/backlog.md` (**Backlog**, or **Draft** if the sprint proved it isn't ready) and drop it from the board; the ticket file stays in `backlog/` as always. This is mandatory: the board freezes with the sprint, so anything left on it silently disappears.
- **Finish it** — hand back to `dynamic-`/`minimal-workflow`, then return here.

Then the scope check — **read-light, and the only thing run is the suite:**

- Every `spec_*` in the sprint folder at **Status: done**?
- Tests green: the unit suite passes, and the **whole e2e suite** runs once here — the suite command `docs/dev.md` names, every flow in the index, the sprint's only full pass since each `e2e` run drove just its own change's flows. Red is a step-2 finding, never papered over with an earlier green `e2e-report_*`.
- Every spec's **Behaviour delta** filled where the project has a `behaviour.md`? That is what step 4 folds into the rulebook, so a spec that shipped behaviour without recording it is the gap to catch here.

**Anything missing → STOP.** List exactly what's open and hand the fix back as a recommendation ("spec-X still draft → `dynamic-workflow`", "spec-Y ships behaviour but records no delta → back to the run that wrote it").

## 2. Code review *(only when the project's version-control rules call for it)*

The one place the sprint gets judged **as a whole**; individual runs only ever saw their own package. **Solo project: off, and don't ask** — run it only on request. Where the rules prescribe review before merge, run it whenever the sprint shipped real feature work or touched shared/core code.

- **Scope:** the cumulative diff against `main` (`main...<sprint-branch>`), not the last commit.
- **How:** the repo's review command (`/code-review`) or `superpowers:requesting-code-review`. Nothing available → review the diff yourself, prioritised: cross-package seams and duplication first (the classic sprint-level defect per-run reviews can't see), then correctness, then the workspace code-style rules.
- **Report, don't repair.** Findings ranked by severity, the user decides. Anything to fix leaves this skill — `minimal-workflow` for a small fix, `dynamic-workflow` if it needs a spec — then come back.

## 3. Distil, then dissolve the board

The board is now all **done**. Each ticket left durable truth behind — graduate it, then the ticket has served its purpose.

**Graduate** (only what isn't recorded yet — most landed during the sprint):

- **`decision` tickets** → a `##` section in `artefacts/{sprint}/sprint-decisions.md` (forces, what was decided, why over the alternatives) plus one `accepted` line in `docs/decisions.md`. Normally written the moment the decision was settled; here you only catch what slipped.
- **Behaviour** is step 4's job — the specs' deltas and the sprint file's Behaviour context go into `docs/behaviour.md` in one pass, so don't pre-empt it here.

**Dissolve** — once that holds, **delete the ticket files of every done ticket** from `backlog/`, and those only; a carried-over ticket is still open and stays. Their lines remain on the frozen board as the record of what shipped, the reasoning lives in the docs it graduated into, git history keeps the rest. This is what keeps `backlog/` bounded.

## 4. Batched docs pass

Invoke **`maintain-docs` in sprint-close mode**: `docs/behaviour.md` first — fold every spec's Behaviour delta into the rulebook in one pass — then the low-churn docs nobody reads *during* a sprint (`architecture.md`, `dev.md`, `product/`, `ASSETS.md`), written from the whole sprint at once, which reads better than five separate deltas.

## 5. Integrate

**Per the project's Version Control rules** (AGENTS.md / AGENTS.md). **Solo default: merge the sprint branch straight into `main`** and say so — no PR, no approval round. Where the rules prescribe the PR flow, open the PR and summarise the sprint in its body.

The frozen sprint folder *is* the record of what shipped — no separate summary doc. `release` composes the changelog from it when the version is cut.

**Then empty `.temp/`** — e2e output and dispatch briefs, reports and transcripts, all of it, no age rule. Anything in there that still matters at a sprint boundary is misfiled: a report belongs in `artefacts/{sprint}/`, a flow worth keeping in the test tree. A project that runs without sprints never reaches this step, so say plainly that the folder is disposable at any time rather than treating this as its only cleaner.

## 6. Release? *(optional, and usually not now)*

Only where the project publishes. Does this sprint complete a **version's** worth of scope? Yes → `release` · no → sprints accumulate on `main` until one does. Never publishes: skip, don't ask.

## 7. Checkpoint

Invoke `checkpoint`; whatever comes next starts in a fresh chat.

## Handoff & boundaries

- **The sprint file freezes here** — frame, board *and* `sprint-decisions.md`. Together they are the sprint's archive, and every board line reads `done` (step 1).
- Produces: the all-done board, the carried-over backlog lines, the dissolved ticket files, any missing entries in `sprint-decisions.md` + `docs/decisions.md`, the batched docs pass, the review findings (if run), the merge/PR to `main`.
- Then → **`open-sprint`** for the next one, and **`release`** where step 6 said yes. If the user is done for now, leave **Current sprint** as it is; `open-sprint` moves the pointer.
- Plans, specs, docs and product code belong to `open-sprint`, `spec-design`, `maintain-docs` and the build workflows.
