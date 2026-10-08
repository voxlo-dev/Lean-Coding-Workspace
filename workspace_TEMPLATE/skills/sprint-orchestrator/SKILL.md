---
name: sprint-orchestrator
description: "Use to run a whole sprint from one conductor chat: plans inline up front (open-sprint, every question settled, sprint-wide UI design, an automation pipeline the user approves), then dispatches each pipeline run as its own headless harness process and relays escalations up to the user. Needs a sprint-orchestrator section in ~/.agents/DISPATCH-GUIDE.md. Invokable directly or via /sprint-orchestrator."
---

# Sprint Orchestrator

A conductor above the sprint. **Plan** talks with the user and front-loads every decision; **Conduct** holds only the sprint file, reports and open questions, while each workflow run builds in a fresh process. The sprint file is the whole state — any chat resumes from it.

**Requires** `~/.agents/DISPATCH-GUIDE.md` with a `sprint-orchestrator` section — a Harness and Model per workflow — and its Rules, Launch and Resume. Missing → say so, offer `/dispatch-configurator`, stop; the plan phase alone is still `open-sprint`.

## 0. Detect the phase

Read `AGENTS.md` → **Current sprint**, then its sprint file.

- No active sprint, or one without a **Pipeline** section → **Plan** (an opened sprint resumes at step 3).
- Pipeline approved → **Conduct** at its first row not `done`.

## Plan — inline, with the user

1. **Open the sprint** — run `open-sprint` (and with it `shape`), minus its closing checkpoint handoff: this skill continues.
2. **Settle everything up front** — no run may need the user for what could be asked now. Per pulled ticket, walk what `spec-design` would ask — scope in/out, success criteria, the approach where a real choice exists, constraints — and write the answers into the ticket. Every open decision on the board gets settled now (`sprint-decisions.md` + `docs/decisions.md`, per `open-sprint` step 3); one that cannot be stays a `decision` ticket, and the rows depending on it wait behind it.
3. **Design the sprint's UI in one pass** — invoke `ui-design`: the styleguide if missing, then a mockup per UI ticket against it, all in one review. Link each mockup in its ticket's **Links**; runs build against it and design nothing new.
4. **Write the pipeline** — the sprint file's **Pipeline** section (`shape`'s `SPRINT_TEMPLATE.md`): rows in run order, `Depends on` respected.
   - **Workflow** per row: `dynamic-workflow` for spec-worthy work, several tickets in one row where they ship as one spec · `minimal-workflow` for one small, well-scoped change · an installed bundle workflow where it fits better · `manual` for what the user does or verifies by hand — never dispatched.
   - **Strategy** per dynamic row: testing preset, e2e yes/no, delegation — decided as `spec-design` steps 3–4 would, here once for the whole sprint.
   - **Mode**: `auto` — the run writes a lean spec nobody reviews · `review` — the run stops at its spec for the user. Default `auto`, `review` where the user wants to see the *how*.
   - Present it, revise until the user approves, commit sprint file, tickets and mockups.
5. **Checkpoint** — invoke `checkpoint`, then continue Conduct in a fresh chat.

## Conduct — one run at a time

Per row, in order, skipping `manual` and rows waiting on an unsettled decision:

1. **Brief** — fill `templates/run-brief.md` into `.temp/dispatch/{sprint}-{n}/brief.md`.
2. **Launch** per the guide's `sprint-orchestrator` row for that workflow, as a background process with a session id the guide's Resume can address. Set the row to `running {session}` and commit **before** launching — that line is what a fresh chat resumes from.
3. **Never wait blind** — the harness notifies on exit; besides, wake every **15 min** and check the transcript still moves. Stalled → stop it and treat as `BLOCKED`.
4. **Verdict** — the last stdout line; details in `report.md` beside the brief:
   - `DONE` → check the report against the board (its tickets at `to test` or `done`), row `done`, commit, next row.
   - `NEEDS_DECISION` → the escalation chain's top: answer from what the plan settled — sprint file, decisions, tickets — else ask the user, with the run's options and recommendation. A spec review under `review` mode goes to the user with its path. Write the answer to `answer-{k}.md` beside the brief; a settled decision also gets its two homes and sharpens its ticket. Resume the session per the guide's Resume, back to step 3.
   - `BLOCKED` or no verdict → read the report and `git log`; then resume with guidance, re-dispatch with a narrowed brief, or set the row `blocked` and capture a ticket, holding back its dependents. **Never take the work over yourself** — reaching for a source file is the signal to re-dispatch or ask.
5. **Context** — after ~5 runs or a long escalation, `checkpoint` and continue in a fresh chat; the pipeline holds everything else.

**Wrap up** — every row `done`, `blocked` or `manual`: tell the user what waits for them — `to test` tickets to verify, `manual` rows, blocked rows with their tickets. `close-sprint` stays the user's call.
