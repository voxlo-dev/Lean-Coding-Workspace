---
name: dynamic-workflow
description: "Use for feature work or any change that warrants a spec. spec-design decides the test and implementation strategy once (technique + inline vs subagent-driven execution); this pipeline then executes them in a fixed order — implement, optional e2e, docs, memory, PR. From ≥3 packages it orchestrates work via sequential subagents to keep context clean. Trivial one-liners use minimal-workflow."
---

# Dynamic Workflow

1. **Spec** — invoke `spec-design`. It brainstorms, designs the UI if there is one, decides the **test** and **implementation strategy** (technique + execution mode), and writes the spec. With a sprint file present (`artefacts/{sprint}/sprint.md`), work an `open` ticket off its board and flip it to `active` as you pick it up — the ticket is the requirement, the sprint file the frame. One spec may cover **several tickets** where they only make sense together; then its packages carry the ticket they serve. Link the spec on each board line. **Review the spec with the user, then commit it — this review always happens, even in autonomous mode.**
2. **Implement** — work the spec's implement packages **in order**, each built with the spec's **technique**, committed per package (optional review first). With test strategy **smoke**, the package writes the committed smoke script the spec names (setup → happy path → teardown its own state) and runs it once. The spec's **execution mode** decides who does the work:
   - **inline** → build the package in the main thread: `direct` (locate code, write it, run tests to green) · `tdd` (`superpowers:test-driven-development`, only when the test strategy is full-TDD) · `debugging` (`superpowers:systematic-debugging`).
   - **subagent-driven** → **orchestrate instead of implementing** (see below). Flavour `dynamic` = the loop below; flavour `full` = `superpowers:subagent-driven-development`.

   Whenever a package leans on a third-party library/framework/API, look up current usage via **context7** rather than trusting recall — subagent handoffs get the same instruction. Technique and mode are **fixed**; the one sanctioned deviation is a failing or buggy package → `superpowers:systematic-debugging`, then resume.
3. **e2e** (optional) — only if the spec's test strategy includes it → invoke `e2e` (subagent-driven mode: delegate to an e2e / browser subagent). A red result sends you back to step 2.
4. **Docs** — invoke `maintain-docs` in **per-run mode** (subagent-driven: delegate to a docs subagent), then commit. The low-churn docs are `close-sprint`'s batch.
5. **Memory** — invoke `maintain-memory`.
6. **Board** — flip **every ticket the spec implemented** to `to test` (straight to `done` if the e2e step already verified it). A ticket whose packages aren't all done keeps its status. Flip the word, leave every other file alone. Something worth doing but out of scope → capture it as a ticket in `backlog/`.

**Stuck? Escalate.** On a technical problem, pause and ask the user after ~5 solution attempts (an attempt = a new approach via a tool call).

## Dynamic subagent-driven orchestration

Used when the spec's execution mode is **subagent-driven / dynamic**. The point is **context management**: over many packages an inline thread fills with read files and loses the spec across compactions. The caller stays a pure **coordinator** — its context holds only the spec, the package list and the reports; every read/write/test happens in fresh subagents, discarded after each package.

**Models:** orchestrate on a capable model (Opus/Fable); dispatch **Sonnet-class** subagents for the work (e2e can be a browser agent).

**The loop — one package at a time, sequentially:**

1. **Handoff** — written from the **spec**, not from reading the code: a self-contained brief with the package goal, the exact files/interfaces it touches, the technique, acceptance criteria, and only the spec/context slices it needs. Always include the spec path so a compacted orchestrator can re-anchor. Dispatch one implementer subagent.
2. **Report** — the subagent implements, runs tests, commits, and returns a short report (what it did, test result, concerns). You read the **report, not the diff**.
3. **Advance or escalate** — clean → mark the package done, next package. **Stuck / blocked → escalate to the user**, rather than taking the implementation over yourself.

Repeat until every package is done, then run e2e (step 3) and docs (step 4) delegated the same way, so the orchestrator never loads their context either.

**Orchestrator rules:**

- **Write handoffs and read reports.** Reaching to open a source file and fix it is the signal to re-dispatch or escalate instead.
- **Sequential only.** One subagent at a time, each handoff building on the previous report. Parallel fan-out is `orchestrator-workflow`'s job.
- **Escalate, don't grind.** A blocked subagent goes to the user, not into your context.
- Need per-task spec + code-quality review gates? That's the `full` flavour — `superpowers:subagent-driven-development`.
