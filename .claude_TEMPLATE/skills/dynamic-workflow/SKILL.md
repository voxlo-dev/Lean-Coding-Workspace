---
name: dynamic-workflow
description: "Use for feature work or any change that warrants a spec. spec-design decides the test and implementation strategy once (technique + inline vs subagent-driven execution); this pipeline then executes them in a fixed order — implement, optional e2e, docs, memory, PR. From ≥3 packages it orchestrates work via sequential subagents to keep context clean. Trivial one-liners use minimal-workflow."
---

# Dynamic Workflow

1. **Spec** — invoke `spec-design`. It brainstorms, designs the UI if there is one, decides the **test strategy** and the **implementation strategy** (technique + execution mode), and writes the spec. If the current sprint has a `artefacts/{sprint}/sprint-plan.md` (from `open-sprint`), spec this feature from it — it's the sprint's umbrella scope. **Review the spec with the user, then commit it — this review always happens, even in autonomous mode** (a spec is never auto-approved).
2. **Implement** — work the spec's implement packages **in order**, each built with the spec's **technique**, committed per package (optional review first). If the test strategy is **minimal**, the package writes the committed **smoke script** the spec names (setup → happy path → teardown its own state) and runs it once — not ad-hoc manual driving. The spec's **execution mode** decides who does the work:
   - **inline** → build the package in the main thread: `direct` (locate code, write it, run tests to green) · `TDD` (`superpowers:test-driven-development`, only when the test strategy is full-TDD) · `debugging` (`superpowers:systematic-debugging`).
   - **subagent-driven** → **orchestrate instead of implementing** (see below). Flavour `dynamic` = the loop below; flavour `full` = `superpowers:subagent-driven-development`.
   Whenever a package leans on a third-party library/framework/API, look up current usage via **context7** rather than trusting recall — it replaces most WebSearch for library docs. Subagent-driven packages: tell the handoff to do the same.
   Technique and mode are **fixed**. The one sanctioned deviation: a package that fails or turns buggy → `superpowers:systematic-debugging`, then resume.
3. **e2e** (optional) — only if the spec's test strategy includes e2e → invoke `e2e` (subagent-driven mode: delegate it to an e2e / browser subagent rather than driving it inline). A red result sends you back to step 2.
4. **Docs** — invoke `maintain-docs` (subagent-driven mode: delegate to a docs subagent), then commit.
5. **Memory** — invoke `maintain-memory`.

**Stuck? Escalate.** On a technical problem, pause and ask the user after ~5 solution
attempts (an attempt = a new approach via a tool call) — don't grind.

## Dynamic subagent-driven orchestration

Used when the spec's execution mode is **subagent-driven / dynamic**. The point is **context
management**: over many packages an inline thread fills with read files and loses the spec
across compactions. The calling model stays a pure **coordinator** — its context holds only
the spec, the package list, and the reports; every read/write/test happens in fresh subagents,
discarded after each package.

**Models:** orchestrate on a capable model (Opus/Fable); dispatch **Sonnet-class** subagents
for the work (e2e can be a browser agent).

**The loop — one package at a time, never in parallel:**

1. **Handoff** — from the **spec** (not from reading the code), write the subagent a self-contained brief: the package goal, the exact files/interfaces it touches, the technique (`direct` or `tdd`), acceptance criteria, and only the spec/context slices it needs. Always include the spec path so a compacted orchestrator can re-anchor. Dispatch one implementer subagent.
2. **Report** — the subagent implements, runs tests, commits, and returns a short report (what it did, test result, concerns). You read the **report, not the diff**.
3. **Advance or escalate** — report clean → mark the package done, next package. Subagent **stuck / blocked** → **escalate to the user**. Do **not** take over the implementation yourself.

Repeat until every package is done, then run e2e (step 3) and docs (step 4) the same way —
delegated to subagents — so the orchestrator never loads their context either.

**Orchestrator rules:**
- **Never implement.** You write handoffs and read reports. Reaching to open a source file and fix it is the signal to re-dispatch or escalate instead.
- **Sequential only.** One subagent at a time; each handoff builds on the previous report. Parallel fan-out is `orchestrator-workflow`'s job, not this one.
- **Escalate, don't grind.** A blocked subagent goes to the user, not into your context.
- Need per-task spec + code-quality review gates? That's the `full` flavour — use `superpowers:subagent-driven-development` instead.
