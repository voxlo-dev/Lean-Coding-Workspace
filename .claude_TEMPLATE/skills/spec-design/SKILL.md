---
name: spec-design
description: "Stage 1 of dynamic-workflow: brainstorm a feature, design its UI if any, decide the test and implementation strategy, then write the spec. The spec's decisions drive the whole pipeline, so decide deliberately here."
---

# Spec Design

Turn an idea or plan into a spec that **fixes every downstream decision** — the *Pflichtenheft*. `dynamic-workflow`
executes whatever this skill records — it does not re-decide — so the judgement lives here.

1. **Brainstorm** — dialogue the idea into shape before writing anything; scale the effort to its complexity and don't interrogate a clear ask:
   - Gather project context in this order: **`docs/behaviour.md` first if it exists** — it is the current truth of how the product behaves, and the basis your delta builds on (**never reconstruct current behaviour from old specs in `artefacts/` — those are frozen deltas**); **then query codegraph** for structure and impact; **then** `docs/architecture.md` / `docs/decisions.md` / `docs/dev.md` if needed; **then** the relevant code and recent commits — read files only for what the above don't answer. The requirements basis is the **ticket(s)** this spec implements (`backlog/T-NNN-*.md`, listed on the sprint file's board) — they say *what* and *why*, you produce the *how*; the sprint file frames them. **A spec may cover several tickets** where they only make sense together: list them all in the header, and cut the work packages so each package maps to a ticket (or to a slice of one) — that mapping is what lets the board move at the right time.
   - Ask only what you genuinely need, **one question at a time**, multiple-choice when you can — purpose, scope (in/out), constraints, success criteria.
   - Propose 2-3 approaches with trade-offs; lead with your recommendation.
   - Shape the design into small units with one clear purpose and clean interfaces — that split drives the implement packages in step 5.
   - **Right-size the spec (rule of thumb by estimated effort).** Judge the coding effort and set the spec's scope accordingly, so the pipeline overhead fits the work:
     - **Small / easy (≲300 LOC)** — one feature alone doesn't justify a full spec run; **fold the next feature(s) into this same spec** until it's a worthwhile chunk.
     - **Mid-size** — one feature, one spec.
     - **Very complex (≳1500 LOC)** — **split into several focused specs** run in sequence, each with its own pipeline pass; don't cram it into one.
   - Present the design in sections sized to their complexity and get the user's nod before writing the spec.
2. **UI** — if the feature has a sophisticated UI, treat its design as a primary step of the spec, not a checkbox. Invoke `ui-design`:
   - **No design system / styleguide yet?** `ui-design` establishes it first at styleguide level — brand & tone, palette, type, spacing, theming (light/dark), components as atoms + patterns → `docs/design/Styleguide.html`. This is the single source every later feature designs against; create it before any feature mockup.
   - **Then** lay out *this* feature's concrete mockup against that styleguide — UX-first: rework the layout or regroup elements rather than squeezing the feature in by the path of least effort.
   - Capture the mockup (and a link to the styleguide) in the spec's UI section.
3. **Decide the test strategy** — pick a coverage level, plus whether e2e is needed:
   - **none** · **smoke** (a **fixed, committed smoke script**, see below) · **core** (key flows, no redundancy) · **full-TDD** (red-green-refactor throughout)
   - **smoke = a committed smoke script, not ad-hoc clicking.** One reusable script per feature (`test/smoke/{feature}.*` or the test framework's equivalent): **setup → run the happy path → assert → tear down its own state**. The implement package writes it; it joins the test suite and reruns on every later change with a single command — no manual tool-call-heavy driving each time. Record which happy path it must cover.
   - **Recommended default: core (with technique `direct`).** Reserve **full-TDD** for logic-heavy, spec-stable units (algorithms, state machines, parsers). Inline TDD costs context without independent bug-finding — the same model writes test and code from the same understanding, so tests confirm its assumptions rather than catch them.
   - **e2e? yes / no** — orthogonal; can pair with any level.
   - Record the level and **which modules / components must be tested** — not concrete test code. The implement packages write the tests themselves: with `direct`, alongside or right after the package's code to the recorded level; with `tdd`, red-green upfront. There is no separate test stage. If e2e is in scope, write the **e2e test case as its own Markdown file** — seed `artefacts/{sprint}/e2e_{feature}.md` from the `e2e` skill's `templates/e2e-testcase.md` (When → Then steps) for the `e2e` skill to consume.
   - **Confirm the strategy with the user via the `AskUserQuestion` tool** — present your recommended level (and e2e yes/no) as the lead option with the alternatives; don't decide the test strategy silently.
4. **Decide the implementation strategy** — two **orthogonal** choices, both recorded in the spec:
   - **Technique** (how each package is built): **direct** (write the code, then its package tests to the recorded level) · **tdd** (`superpowers:test-driven-development`, only when the test strategy is full-TDD) · **debugging** (`superpowers:systematic-debugging`, for bugfix-shaped work).
   - **Execution** (who holds the context): **inline** — the main thread does the work · **subagent-driven** — each package is delegated to a fresh subagent so the orchestrator's context stays clean across many packages. Independent of the technique (subagent-driven can run `direct` *or* `tdd`). **Strongly recommended from ≥3 packages**, or whenever context pressure is likely. If subagent-driven, pick the flavour:
     - **dynamic** — the compact sequential loop built into `dynamic-workflow` (handoff → report, no parallelism, no per-task review subagents; the default).
     - **full** — `superpowers:subagent-driven-development` (adds per-task spec + code-quality review subagents; heavier, stricter).
   - **Confirm technique + execution with the user via the `AskUserQuestion` tool** — lead with your recommendation and its rationale; let the user validate before it's locked into the spec.
5. **Write the spec** — copy this skill's `templates/SPEC_TEMPLATE.md` to `artefacts/{sprint}/spec_{feature}.md` (ask the user for the current sprint if unclear), fill it, and size the **implement packages** (≥1; just one package is allowed, but large, independent work needs more packages — and ≥3 packages is the signal to switch execution to **subagent-driven**).

Return to `dynamic-workflow`, which owns the review pause and the spec commit. **The spec review is mandatory even in autonomous mode** — a spec (like a plan) is never auto-approved; the user validates it before it drives implementation.
