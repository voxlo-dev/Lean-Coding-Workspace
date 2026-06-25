---
name: spec-design
description: "Stage 1 of dynamic-workflow: brainstorm a feature, design its UI if any, decide the test and implementation strategy, then write the spec. The spec's decisions drive the whole pipeline, so decide deliberately here."
---

# Spec Design

Turn an idea into a spec that **fixes every downstream decision**. `dynamic-workflow`
executes whatever this skill records — it does not re-decide — so the judgement lives here.

1. **Brainstorm** — invoke `superpowers:brainstorming`; don't reinvent it. Settle purpose, scope (in/out), approach, components, data flow.
2. **UI** — if the feature has a UI, invoke `ui-design` to lay out its mockup against the styleguide; capture it in the spec's UI section.
3. **Decide the test strategy** — pick a coverage level, plus whether e2e is needed:
   - **none** · **minimal** (smoke / happy path) · **core** (key flows, no redundancy) · **full-TDD** (red-green-refactor throughout)
   - **e2e? yes / no** — orthogonal; can pair with any level.
   - Record the level and **which modules / components must be tested** — not concrete test code (the implement step writes that). If e2e is in scope, write the **e2e test case as its own Markdown file** (`docs/specs/{spec}-e2e.md`, Given/When/Then steps) for the `e2e` skill to consume.
4. **Decide the implementation strategy** — record exactly one in the spec:
   - **direct** — implement inline in the main thread.
   - **subagents** — independent packages delegated (`superpowers:subagent-driven-development`).
   - **tdd** — `superpowers:test-driven-development`; **only valid when the test strategy is full-TDD**.
   - **debugging** — for bugfix-shaped work (`superpowers:systematic-debugging`).
5. **Write the spec** — copy `docs/specs/SPEC_TEMPLATE.md` to a new file under `docs/specs/`, fill it, and size the **implement packages** (≥1; large, independent work → more packages, which is also the signal for the `subagents` strategy).

Return to `dynamic-workflow`, which owns the review pause and the spec commit.
