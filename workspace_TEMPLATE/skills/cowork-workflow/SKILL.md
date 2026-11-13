---
name: cowork-workflow
description: "Use when the user wants to build a change together, hands-on: interview and plan in the chat, no spec, then small steps made straight in the code that the user reviews in the diff and adjusts, each committed once approved — then unreviewed tests or e2e, docs, memory. Needs the user at every step, so never dispatched."
---

# Cowork Workflow

Pair work: the user steers every step from the diff. No spec, no package dispatch, never a `sprint-autopilot` row — subagents only for exploration and e2e.

1. **Ground** — locate and understand the relevant code (codegraph if the repo is indexed, otherwise a targeted search) before asking anything. With a sprint file, work an `open` ticket off its board and flip it to `active`.
2. **Interview** — only what the code and ask leave open: behaviour, scope edge, approach where a real choice exists. **One question at a time**, multiple-choice where possible; a clear ask needs none.
3. **Plan in the chat** — the change as ordered **steps, each one diff the user can review in one sitting**: what changes where, how it gets verified at the end, what stays out. Never a file. Get the user's nod.
4. **Step loop** — per step:
   - **Edit the working tree directly**, uncommitted — the user's diff view is the review surface. A change leaning on a third-party library/framework/API → current usage via **context7**, not recall.
   - **Stop and hand over** — 2–3 lines: what changed, which files, what to look at. Never paste the diff.
   - **The user adjusts** — by request or by editing the code themselves. Re-read every file they touched before your next edit; their edits are the standard for style and naming, never reverted. Revise, hand over again, until they OK the step.
   - **OK → commit the step**, so the next step's diff starts clean and shows only what is new. A plan that proves wrong is revised in the chat, said out loud.
5. **Test, if the project has a test setup — autonomously**, no hand-over, no review: find an existing test covering the change, else write one or extend the closest, run the full suite, loop to green, commit. A fix that changes reviewed code → name it in the hand-back. **e2e** where the change touches a flow in `test/e2e/FLOWS.md` or the user asks → invoke `e2e`: existing automation inline first, what it can't settle to the **`e2e-runner` agent**. No test setup → the step reviews were the verification; say so.
6. **Docs** — invoke `maintain-docs` in **per-run mode**, then commit. **Changed behaviour without a spec?** One line into the sprint file's **Behaviour context** — `close-sprint` folds that into `docs/behaviour.md`.
7. **Memory** — invoke `maintain-memory`; the review rounds show the user's preferences — the ones that recur are worth keeping.
8. **Board, only if a ticket drove this** — flip its status to `to test` (straight to `done` if e2e verified it). Anything left undone becomes a ticket in `backlog/`.

**Stuck? Escalate.** On a technical problem, ask the user after ~5 solution attempts (an attempt = a new approach via a tool call). No checkpoint unless the user asks.
