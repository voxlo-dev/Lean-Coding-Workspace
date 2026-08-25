---
name: spec-design
description: "Stage 1 of dynamic-workflow: brainstorm a feature, design its UI if any, decide its testing preset and delegation, then write the spec. The spec's decisions drive the whole pipeline, so decide deliberately here."
---

# Spec Design

Turn an idea or plan into a spec that **fixes every downstream decision** — the *Pflichtenheft*. `dynamic-workflow` executes what this skill records, so the judgement lives here.

1. **Brainstorm** — dialogue the idea into shape before writing anything; scale the effort to its complexity and take a clear ask at face value.
   - Gather context in this order: **the sprint file first** — its **Behaviour context** (the behaviour slice this sprint works in, cut by `plan`) plus its **Decisions**; **then this sprint's `spec_*` at Status: done**, carrying the deltas since planning; **then codegraph** for structure and impact; **then** `docs/decisions.md` / `architecture.md` / `dev.md` if needed; **then** the relevant code and recent commits, for what the above leave open. `docs/behaviour.md` is the whole product's rulebook, written at sprint close — read a section only where the sprint slice falls short, never wholesale.
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
3. **Decide the testing** — one preset (coverage *and* when the tests are written), plus whether e2e is needed:

   | Preset | What the implement packages do |
   | --- | --- |
   | none | no tests |
   | smoke | one committed script per feature (`test/smoke/{feature}.*` or the framework's equivalent): setup → happy path → assert → tear down its own state. Joins the suite, reruns with one command on every later change; record which happy path it covers |
   | core | key flows, no redundancy — each package writes its tests alongside or right after its own code |
   | light-tdd | core coverage upfront: package 1 lands the signatures and the tests and confirms red, the later packages turn them green |
   | strict-tdd | full coverage, red-green-refactor per unit via `superpowers:test-driven-development` |

   - **Default: `light-tdd` where the feature carries real logic, `core` for UI, glue and thin CRUD.** Reserve `strict-tdd` for logic-heavy, spec-stable units (algorithms, state machines, parsers): per-unit red-green costs context without independent bug-finding, since one model writes test and code from the same understanding, so its tests confirm assumptions rather than catch them. `light-tdd` buys that back for one package of overhead — the tests exist before any implementation, delegated even from an agent that never sees it.
   - **e2e? yes / no** — orthogonal, pairs with any preset.
   - Record the preset and **which modules / components must be tested**, at that altitude rather than as test code — the implement packages write the tests themselves, so there is no separate test stage. The suite goes green **once, at the end of the run**, so never size a package around its testability. If e2e is in scope, write the **e2e test case as its own Markdown file**: seed `artefacts/{sprint}/e2e_{feature}.md` from the `e2e` skill's `templates/e2e-testcase.md` (When → Then steps) for that skill to consume.
   - **Confirm with the user via `AskUserQuestion`** — recommended preset (and e2e yes/no) as the lead option with the alternatives. **Name the skill an option pulls in** (`strict-tdd` → `superpowers:test-driven-development`), so its cost is visible at the pick.
4. **Decide the delegation** — who holds the context while the packages are built: **inline** (the main thread does the work) · **delegated** (each package goes to a fresh subagent via `dynamic-workflow`'s sequential loop, so the orchestrator's context stays clean) · **delegated+review** (`superpowers:subagent-driven-development` — adds per-task spec and code-quality review subagents; heavier, stricter).
   - **Delegated from ≥3 packages**, or whenever context pressure is likely. It is also what makes `light-tdd` bite hardest: the tests come from an agent with no implementation in its context.
   - **Confirm with the user via `AskUserQuestion`** — lead with your recommendation and its rationale, again naming the skill an option pulls in (`delegated+review` → `superpowers:subagent-driven-development`).
5. **Write the spec** — copy `templates/SPEC_TEMPLATE.md` to `artefacts/{sprint}/spec_{feature}.md` (ask for the current sprint if unclear), fill it, and size the **implement packages** (≥1; one is allowed, large independent work takes more — and ≥3 is the signal for **delegated**).
   - **Every package carries its own acceptance criteria** — the observable outcome that makes it done, and the bar a subagent is judged against. Cut packages by coherent unit of work.
   - **With `light-tdd`, package 1 is the contract & tests package**: the signatures, types and stubs the **Components** section fixes, plus the tests for the recorded modules, no logic. Its criterion is a red suite failing on missing implementation; every later package names the tests it turns green — so settle the Components interfaces *here*, don't leave them to the implementation.
   - **A bugfix-shaped package runs `superpowers:systematic-debugging`**, whatever the preset says — mark it in the package line.
   - Delete the template's guidance comments as you fill it, keeping the pointer line.

Return to `dynamic-workflow`, which owns the review pause and the spec commit. **The spec review is mandatory even in autonomous mode** — a spec, like a plan, is always user-validated before it drives implementation.
