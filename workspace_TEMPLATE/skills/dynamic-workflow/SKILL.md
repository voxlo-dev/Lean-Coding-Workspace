---
name: dynamic-workflow
description: "Use for feature work or any change that warrants a spec. spec-design picks a testing preset (none · smoke · core · light-tdd · strict-tdd) and a delegation mode once; this pipeline then executes them in a fixed order — implement, optional e2e, docs, memory, PR. From ≥3 packages it delegates work to sequential subagents to keep context clean. Trivial one-liners use minimal-workflow."
---

# Dynamic Workflow

1. **Spec** — invoke `spec-design`. It brainstorms, designs the UI if there is one, decides the **testing preset** and the **delegation**, and writes the spec. With a sprint file present (`artefacts/{sprint}/sprint.md`), work an `open` ticket off its board and flip it to `active` as you pick it up — the ticket is the requirement, the sprint file the frame. One spec may cover **several tickets** where they only make sense together; then its packages carry the ticket they serve. Link the spec on each board line. **Review the spec with the user, then commit it** — everything up to here is user-validated, whatever follows. Only then ask the **autonomy mode** for the rest of the run: pause for review BEFORE each commit (default), or run autonomously. Ask the **dispatch mode** in the same breath, but only where `~/.agents/DISPATCH-GUIDE.md` exists and the delegation is `delegated`: run the packages in this harness (default), or send them out per that guide. It is a per-run call, so never assume last run's answer.
2. **Implement** — work the spec's implement packages **in order**, committed per package (optional review first). **A package is done when its own acceptance criteria hold** — the spec states them per package; tests are one criterion, not the gate. Cut packages by *coherent unit of work*, never by what is independently testable; the suite goes green at step 3. The spec's **testing preset** says what each package owes: `core` writes its tests alongside or right after its code · `smoke` writes the committed smoke script the spec names (setup → happy path → teardown its own state) and runs it once · `light-tdd` runs the loop below · `strict-tdd` builds every unit through `superpowers:test-driven-development`. The spec's **delegation** says who does the work:
   - **inline** → build the package in the main thread.
   - **delegated** → **orchestrate instead of implementing**, via the loop below. **delegated+review** → `superpowers:subagent-driven-development`.

   Whenever a package leans on a third-party library/framework/API, look up current usage via **context7** rather than trusting recall. Preset and delegation are **fixed**; the sanctioned deviations are a package the spec marks bugfix-shaped, and a failure whose **cause is unclear** → `superpowers:systematic-debugging`, then resume. **Announce any skill that activates mid-run, and why** — the user picked the preset, not this.
3. **Green** — every package done → run the **full suite**, loop to green, commit the fixes. The run's hard gate: an in-flight package may leave a red test, a finished run may not. A failure you can't explain → `superpowers:systematic-debugging`, then re-run.
4. **e2e** (optional) — only if the spec's testing includes it → invoke `e2e`. **Run the existing automation inline first**: one command, and a green run ends the stage with no subagent at all. Only what it can't settle — red, a missing script, agent steps — goes to the **`e2e-runner` agent** (a browser agent where the project has one), whatever the spec's delegation says: that part is self-contained and token-hungry, so it never belongs in the main thread. Red reopens **the bug alone** — fix what the report names, no new spec and no re-planned packages, re-run that flow, continue at step 5.
5. **Docs** — invoke `maintain-docs` in **per-run mode**, then commit. Inline even under `delegated`, since the handoff would restate the run; a docs subagent only for genuinely broad drift. `behaviour.md` and the low-churn docs are `close-sprint`'s batch.
6. **Memory** — invoke `maintain-memory`.
7. **Board** — flip **every ticket the spec implemented** to `to test` (straight to `done` if the e2e step already verified it). A ticket whose packages aren't all done keeps its status. Flip the word, leave every other file alone. Something worth doing but out of scope → capture it as a ticket in `backlog/`.
8. **Checkpoint** — invoke `checkpoint`, then continue in a fresh chat.

**Weigh every dispatch right before it fires.** A subagent costs a context spin-up plus a handoff, so it pays only where the work is token-hungry, self-contained, or would flood the orchestrator's context; below that bar it runs in the main thread even under `delegated` — a per-run docs pass, a small fix after e2e, a one-file change. Holds for any dispatch a nested skill triggers (`systematic-debugging`, `e2e`) too.

**Stuck? Escalate.** On a technical problem, pause and ask the user after ~5 solution attempts (an attempt = a new approach via a tool call).

## Light TDD

Used when the spec's testing preset is **light-tdd**: tests-first without per-unit red-green, for one extra package of overhead.

1. **Contract & tests package** (the spec's package 1) — land the signatures, types and stubs the spec's **Components** fix, plus the tests for the modules the spec names, at core coverage. **No implementation logic**, and stubs fail loudly (throw / not-implemented) rather than returning a plausible value.
2. **Confirm red** — run the suite and check *why* it is red: every failure must come from missing implementation. Red from an import, compile or fixture error means the contract package isn't done. Record the failing tests; they are the map for the packages that follow.
3. **Implementation packages** turn their slice green, and treat the tests as **read-only**. A test that is wrong because the *signature* was wrong → change signature and test together in one move and say so in the report; bending an assertion to match the code is never the fix, and repeated cases mean the spec is off → escalate.

Red between packages is expected; step 3's green gate closes it.

## Delegated orchestration

Used when the spec's delegation is **delegated**. The point is **context management**: over many packages an inline thread fills with read files and loses the spec across compactions. The caller stays a pure **coordinator** — its context holds only the spec, the package list and the reports; every read/write/test happens in fresh subagents, discarded after each package.

**Models:** orchestrate on a capable model (Opus/Fable); dispatch **Sonnet-class** subagents for the work (e2e can be a browser agent). Under **dispatch mode** the work instead leaves the harness: `~/.agents/DISPATCH-GUIDE.md` — opened only once that mode is chosen, never to decide it — holds the launch command, the model per role and the rules — same handoff, same report, another process. A role the guide does not cover runs inline as above.

**The loop — one package at a time, sequentially:**

1. **Handoff** — fill `templates/handoff.md`: written from the **spec**, not from reading the code, self-contained, carrying the standing rules the template holds so no dispatch has to restate them. Dispatch one implementer subagent — **in-harness the handoff is the prompt, never a file**. With `light-tdd` the contract package gets its own subagent — that separation is what makes the tests independent; carry its failing-test list into every later handoff, and never widen an implementer's brief to the tests.
2. **Report** — the subagent implements, checks its acceptance criteria, commits, and returns a short report (what it did, criteria met, test state, concerns). You read the **report, not the diff** — under dispatch mode from the file the handoff named, the loop otherwise unchanged.
3. **Advance or decide** — criteria met → mark the package done, next package. A **blocked** report is yours to resolve, never the subagent's: re-dispatch with a narrowed brief, capture it as a ticket, or escalate to the user — but do not take the implementation over yourself.

Repeat until every package is done, then the green gate (step 3) delegated the same way, so the orchestrator never loads its context either; docs stay inline per step 5.

**Orchestrator rules:**

- **Write handoffs and read reports.** Reaching to open a source file and fix it is the signal to re-dispatch or escalate instead.
- **Sequential only.** One subagent at a time, each handoff building on the previous report.
- **Decide, don't grind.** A blocked subagent's obstacle is resolved per step 3, never absorbed into your context.
- Need per-task spec + code-quality review gates? That's **delegated+review** — `superpowers:subagent-driven-development`.
