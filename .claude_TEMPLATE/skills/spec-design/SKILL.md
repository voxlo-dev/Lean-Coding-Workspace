---
name: spec-design
description: "Stage 1 of dynamic-workflow: brainstorm a feature, design its UI if any, decide the test and implementation strategy, then write the spec. The spec's decisions drive the whole pipeline, so decide deliberately here."
---

# Spec Design

Turn an idea or plan into a spec that **fixes every downstream decision** — the *Pflichtenheft*. `dynamic-workflow`
executes whatever this skill records — it does not re-decide — so the judgement lives here.

1. **Brainstorm** — dialogue the idea into shape before writing anything; scale the effort to its complexity and don't interrogate a clear ask:
   - Check project context first — files, architecture docs, recent commits. If a plan exists (`docs/artefacts/{sprint}/plan_{feature}.md` from `plan`), read it as the requirements basis — the recommended, though optional, starting point.
   - Ask only what you genuinely need, **one question at a time**, multiple-choice when you can — purpose, scope (in/out), constraints, success criteria.
   - Propose 2-3 approaches with trade-offs; lead with your recommendation.
   - Apply **YAGNI** — cut every feature that isn't needed.
   - Shape the design into small units with one clear purpose and clean interfaces — that split drives the implement packages in step 5.
   - Present the design in sections sized to their complexity and get the user's nod before writing the spec.
2. **UI** — if the feature has a UI, invoke `ui-design` to lay out its mockup against the styleguide; capture it in the spec's UI section.
3. **Decide the test strategy** — pick a coverage level, plus whether e2e is needed:
   - **none** · **minimal** (smoke / happy path) · **core** (key flows, no redundancy) · **full-TDD** (red-green-refactor throughout)
   - **Recommended default: core (with technique `direct`).** Reserve **full-TDD** for logic-heavy, spec-stable units (algorithms, state machines, parsers). Inline TDD costs context without independent bug-finding — the same model writes test and code from the same understanding, so tests confirm its assumptions rather than catch them.
   - **e2e? yes / no** — orthogonal; can pair with any level.
   - Record the level and **which modules / components must be tested** — not concrete test code. The implement packages write the tests themselves: with `direct`, alongside or right after the package's code to the recorded level; with `tdd`, red-green upfront. There is no separate test stage. If e2e is in scope, write the **e2e test case as its own Markdown file** — seed `docs/artefacts/{sprint}/e2e_{feature}.md` from the `e2e` skill's `templates/e2e-testcase.md` (When → Then steps) for the `e2e` skill to consume.
4. **Decide the implementation strategy** — two **orthogonal** choices, both recorded in the spec:
   - **Technique** (how each package is built): **direct** (write the code, then its package tests to the recorded level) · **tdd** (`superpowers:test-driven-development`, only when the test strategy is full-TDD) · **debugging** (`superpowers:systematic-debugging`, for bugfix-shaped work).
   - **Execution** (who holds the context): **inline** — the main thread does the work · **subagent-driven** — each package is delegated to a fresh subagent so the orchestrator's context stays clean across many packages. Independent of the technique (subagent-driven can run `direct` *or* `tdd`). **Strongly recommended from ≥3 packages**, or whenever context pressure is likely. If subagent-driven, pick the flavour:
     - **dynamic** — the compact sequential loop built into `dynamic-workflow` (handoff → report, no parallelism, no per-task review subagents; the default).
     - **full** — `superpowers:subagent-driven-development` (adds per-task spec + code-quality review subagents; heavier, stricter).
5. **Write the spec** — copy this skill's `templates/SPEC_TEMPLATE.md` to `docs/artefacts/{sprint}/spec_{feature}.md` (ask the user for the current sprint if unclear), fill it, and size the **implement packages** (≥1; large, independent work → more packages — and ≥3 packages is the signal to switch execution to **subagent-driven**).

Return to `dynamic-workflow`, which owns the review pause and the spec commit.
