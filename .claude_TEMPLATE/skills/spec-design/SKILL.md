---
name: spec-design
description: "Stage 1 of dynamic-workflow: brainstorm a feature, design its UI if any, decide the test and implementation strategy, then write the spec. The spec's decisions drive the whole pipeline, so decide deliberately here."
---

# Spec Design

Turn an idea or plan into a spec that **fixes every downstream decision** — the *Pflichtenheft*. `dynamic-workflow` executes what this skill records, so the judgement lives here.

1. **Brainstorm** — dialogue the idea into shape before writing anything; scale the effort to its complexity and take a clear ask at face value.
   - Gather context in this order: **`docs/behaviour.md` first if it exists** — the current truth of how the product behaves and the basis your delta builds on (specs in `artefacts/` are frozen deltas, so read current behaviour here); **then codegraph** for structure and impact; **then** `docs/architecture.md` / `decisions.md` / `dev.md` if needed; **then** the relevant code and recent commits, for what the above leave open.
   - The requirements basis is the **ticket(s)** this spec implements (`backlog/T-NNN-*.md`, indexed on the sprint board): they say *what* and *why*, you produce the *how*, and the sprint file frames them. **A spec may cover several tickets** where they only make sense together — list them all in the header and cut the work packages so each maps to a ticket (or a slice of one); that mapping is what lets the board move at the right time.
   - Ask only what you genuinely need, **one question at a time**, multiple-choice where possible — purpose, scope (in/out), constraints, success criteria.
   - Propose 2–3 approaches with trade-offs; lead with your recommendation.
   - Shape the design into small units with one clear purpose and clean interfaces — that split drives the implement packages in step 5.
   - **Right-size the spec by estimated coding effort**, so the pipeline overhead fits the work: **small (≲300 LOC)** → **fold the next feature(s) into this same spec** until it's a worthwhile chunk · **mid-size** → one feature, one spec · **very complex (≳1500 LOC)** → **split into several focused specs** run in sequence, each with its own pipeline pass.
   - Present the design in sections sized to their complexity and get the user's nod before writing the spec.
2. **UI** — if the feature has a sophisticated UI, its design is a primary step of the spec. Invoke `ui-design`:
   - **No design system yet?** It establishes the styleguide first — brand & tone, palette, type, spacing, theming, components as atoms + patterns → `docs/design/Styleguide.html`. Every later feature designs against it, so it comes before any feature mockup.
   - **Then** lay out *this* feature's mockup against that styleguide — UX-first: rework the layout or regroup elements where that serves the experience.
   - Capture the mockup (and a link to the styleguide) in the spec's UI section.
3. **Decide the test strategy** — a coverage level, plus whether e2e is needed:
   - **none** · **smoke** (a fixed, committed smoke script) · **core** (key flows, no redundancy) · **full-TDD** (red-green-refactor throughout).
   - **smoke = a committed smoke script.** One reusable script per feature (`test/smoke/{feature}.*` or the framework's equivalent): **setup → run the happy path → assert → tear down its own state**. The implement package writes it; it joins the test suite and reruns on every later change with one command. Record which happy path it must cover.
   - **Recommended default: core, technique `direct`.** Reserve **full-TDD** for logic-heavy, spec-stable units (algorithms, state machines, parsers) — inline TDD costs context without independent bug-finding, since the same model writes test and code from the same understanding, so its tests confirm assumptions rather than catch them.
   - **e2e? yes / no** — orthogonal, pairs with any level.
   - Record the level and **which modules / components must be tested**, at that altitude rather than as test code: the implement packages write the tests themselves (with `direct` alongside or right after the package's code, with `tdd` red-green upfront), so there is no separate test stage. If e2e is in scope, write the **e2e test case as its own Markdown file**: seed `artefacts/{sprint}/e2e_{feature}.md` from the `e2e` skill's `templates/e2e-testcase.md` (When → Then steps) for that skill to consume.
   - **Confirm the strategy with the user via `AskUserQuestion`** — recommended level (and e2e yes/no) as the lead option with the alternatives.
4. **Decide the implementation strategy** — two **orthogonal** choices, both recorded in the spec:
   - **Technique** (how each package is built): **direct** (write the code, then its package tests to the recorded level) · **tdd** (`superpowers:test-driven-development`, only when the test strategy is full-TDD) · **debugging** (`superpowers:systematic-debugging`, for bugfix-shaped work).
   - **Execution** (who holds the context): **inline** — the main thread does the work · **subagent-driven** — each package goes to a fresh subagent so the orchestrator's context stays clean across many packages. Independent of the technique. **Strongly recommended from ≥3 packages**, or whenever context pressure is likely. If subagent-driven, pick the flavour: **dynamic** — the compact sequential loop built into `dynamic-workflow` (the default) · **full** — `superpowers:subagent-driven-development` (adds per-task spec + code-quality review subagents; heavier, stricter).
   - **Confirm technique + execution with the user via `AskUserQuestion`** — lead with your recommendation and its rationale before it's locked into the spec.
5. **Write the spec** — copy `templates/SPEC_TEMPLATE.md` to `artefacts/{sprint}/spec_{feature}.md` (ask for the current sprint if unclear), fill it, and size the **implement packages** (≥1; one is allowed, large independent work takes more — and ≥3 is the signal for **subagent-driven** execution).

Return to `dynamic-workflow`, which owns the review pause and the spec commit. **The spec review is mandatory even in autonomous mode** — a spec, like a plan, is always user-validated before it drives implementation.
