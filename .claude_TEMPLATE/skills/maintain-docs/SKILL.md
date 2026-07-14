---
name: maintain-docs
description: "Use at the docs step of the minimal and dynamic workflows. Triggers: code, assets, architecture, or user-facing behaviour changed and docs may have drifted."
---

# Maintain Docs

**Distill the run's durable truth into `docs/`, then prune the working memory.** This is the step that stops docs from rotting: an ephemeral spec described the *delta*; here that delta graduates into the durable docs so the next session reads current truth from one place, not from stacked spec addenda. Judge each doc from the changes you made *before* opening it, and spend effort by priority.

**Two rules that override everything below:**

- **Lifespan split.** `docs/` = durable truth about the *shipped* system. `artefacts/` = frozen process history. You **write into `docs/`** and only ever touch a `spec-*` file's Status/ACs in `artefacts/` — never anything else there.
- **Respect each doc's contract header.** Every durable doc opens with a `CONTRACT` comment: what belongs in it, what stays out, and (for `architecture.md`) the exact status mechanic. The section layout under it is a *suggestion* — restructure to fit the project, but never violate the contract, and never leave an empty template section standing.

## Step 1 — Relevance gate

Were there changes a reader would care about? Pure internal refactors, typo/formatting fixes, and test-only changes → you can skip the doc sweep.

## Step 2 — Per-doc decision (decide before reading)

For each doc, ask "does *this* change affect it?" from what you already know. Open a doc only if the answer is yes. **Optional docs (`docs/behaviour.md`, `docs/decisions.md`, `docs/architecture.md`, `docs/dev.md`, `docs/product/`, `docs/design/`, `ASSETS.md`, `CHANGELOG.md`) only exist if the project opted in at init — if a doc isn't there, skip it; never re-create what the project chose not to have.**

| Doc | Priority | Update when | Action |
| --- | --- | --- | --- |
| `docs/behaviour.md` (if present) | **high** | product behaviour, a rule, or a UX invariant changed | **your first stop.** Write the behaviour *delta* here so this doc carries the current truth. Update the rule/invariant/interaction-contract in place; a spec is a throwaway, this is the state. Present tense, declarative — no history, no rationale (that's `decisions.md`) |
| `AGENTS.md` → Current sprint | high | the sprint pointer moved | keep the one-line pointer correct; also fix code-style/conventions if they changed (single source — never copy into README) |
| `AGENTS.md` → Doc map | high | a doc was added or removed | keep the map listing only docs that exist |
| `docs/decisions.md` (if present) | high | a decision was made, or an old one overturned | **append** a new entry (never rewrite one); to overturn, add the new entry and set the old one's Status to `superseded by NNNN`. A still-open question → entry with Status `proposed` |
| `CHANGELOG.md` (if present) | medium | a user-visible change shipped | add a line under `[Unreleased]` in the right group (Added/Changed/Fixed/Removed). User's view only — not a git-log dump. (`sprint-cycle` cuts `[Unreleased]` into a release at sprint close) |
| `artefacts/{sprint}/spec-*` | when in context | a spec was implemented this run | update its **Status** (e.g. draft → done) and tick the **acceptance criteria** you met — only with that context in hand; otherwise leave it. Every other file in `artefacts/` (plans, e2e cases, reports) is a frozen run record — never edited here |
| `ASSETS.md` (if present) | medium | assets were added, moved, or repurposed | add/adjust the row(s); keep `Used in` accurate |
| `docs/dev.md` (if present) | medium | setup, env, build/debug workflow, or a dependency quirk changed | record it; **don't** copy api signatures (link codegraph/generated docs) and **don't** log bugs/todos here (→ issue tracker / memory) |
| `docs/architecture.md` (if present) | low | a drafted subsystem was actually implemented, or core structure changed | fill the subsystem from the real code and flip its **Status** `planned → implemented`; bump the doc's top **Status** `draft → partial → current`. See below |
| `docs/product/` (if present) | low | user-facing interaction changed | read the relevant page first, then make very targeted edits |
| `docs/design/` (if present) | — | design system changed | **don't touch here** — the styleguide and mockups are owned by `ui-design` |

**Not maintained here:** everything under `artefacts/` is a frozen run record — the only exception is a `spec-*` file's Status + acceptance criteria (per the table above). Plans, e2e cases, and reports there are pure run history, never edited at the docs step. `localagent/` run records are likewise never touched here. Both are committed as-is by their own workflow.

## Behaviour, decisions & the distillation rule

- **`behaviour.md` is the anti-rot core.** The spec you just implemented is a delta; the durable truth of *how the product now behaves* must land here, or the next spec will re-define it from scratch. Write the smallest accurate change: adjust the rule, invariant, or interaction contract that actually moved. `spec-design` reads this doc as its basis, so keeping it current is what makes specs disposable.
- **`decisions.md` is append-only.** Record the *why* of a choice here, never in `behaviour.md` or `architecture.md`. Never rewrite an entry — supersede it.

## Architecture & developer docs

- **Draft → filled.** `project-initialiser` seeds `architecture.md` as a `draft` skeleton whose subsystems are marked `planned`. When a subsystem is genuinely built, fill it from the real code and set its **Status** to `implemented`; raise the doc's top **Status** to `partial`, and to `current` once no `planned` subsystem remains. **Never mark `implemented` from a plan alone — only from shipped code.**
- Record core changes/additions; keep edits factual, no speculation. Keep `architecture.md` focused on software *structure*; behaviour → `behaviour.md`, rationale → `decisions.md`, and engineering knowledge code/codegraph don't capture (setup, env, build/debug workflows, dependency quirks) → `docs/dev.md`. Bugs/todos go to the issue tracker, not the docs.
- **Split either when it grows past ~300–500 lines:** break the document into one file per subsystem/topic under a same-named folder (`docs/architecture/`, `docs/dev/`, `docs/behaviour/`, `docs/decisions/`), keeping the original file as a short top-level index that links them.
