---
name: maintain-docs
description: "Use at the docs step of the minimal and dynamic workflows (per-run mode: the docs the next run reads), and at sprint close from close-sprint (sprint-close mode: the batched low-churn docs). Triggers: code, assets, architecture, or user-facing behaviour changed and docs may have drifted."
---

# Maintain Docs

**Distill the run's durable truth into `docs/`, then prune the working memory.** This is the step that stops docs from rotting: an ephemeral spec described the *delta*; here that delta graduates into the durable docs so the next session reads current truth from one place, not from stacked spec addenda. Judge each doc from the changes you made *before* opening it, and touch only the docs your mode owns.

**Two modes — the caller sets it:**

- **Per-run** (minimal/dynamic-workflow, after the change) — only the docs that are an **input to the next run**: `behaviour.md`, `decisions.md`, the spec's Status/ACs. Small, hot, cheap. These cannot wait: `spec-design` reads `behaviour.md` as current truth, and a stale one forces the next spec to reconstruct the present from frozen deltas.
- **Sprint-close** (`close-sprint`, once per sprint) — the rest: `architecture.md`, `dev.md`, `product/`, `ASSETS.md`. Nobody reads them *during* a sprint, and they come out better written from the whole sprint at once than from five separate deltas. The table's Mode column tells you which mode a doc belongs to.

**Two rules that override everything below:**

- **Lifespan split.** `docs/` = durable truth about the *shipped* system. `artefacts/` = frozen process history. You **write into `docs/`** and only ever touch a `spec_*` file's Status/ACs in `artefacts/` — never anything else there. The sprint file's board is moved by the workflows and `close-sprint`, never here.
- **Respect each doc's contract header.** Every durable doc opens with a `CONTRACT` comment: what belongs in it, what stays out, and (for `architecture.md`) the exact status mechanic. The section layout under it is a *suggestion* — restructure to fit the project, but never violate the contract, and never leave an empty template section standing.

## Step 1 — Relevance gate

Were there changes a reader would care about? Pure internal refactors, typo/formatting fixes, and test-only changes → you can skip the doc sweep.

## Step 2 — Per-doc decision (decide before reading)

For each doc, ask "does *this* change affect it?" from what you already know. Open a doc only if the answer is yes. **Optional docs (`docs/behaviour.md`, `docs/decisions.md`, `docs/architecture.md`, `docs/dev.md`, `docs/product/`, `docs/design/`, `ASSETS.md`, `CHANGELOG.md`) only exist if the project opted in at init — if a doc isn't there, skip it; never re-create what the project chose not to have.**

| Doc | Mode | Update when | Action |
| --- | --- | --- | --- |
| `docs/behaviour.md` (if present) | **per-run** | product behaviour, a rule, or a UX invariant changed | **your first stop.** Write the behaviour *delta* here so this doc carries the current truth. Update the rule/invariant/interaction-contract in place; a spec is a throwaway, this is the state. Present tense, declarative — no history, no rationale (that's `decisions.md`) |
| `AGENTS.md` → Current sprint | per-run | the sprint pointer moved | keep the one-line pointer correct; also fix code-style/conventions if they changed (single source — never copy into README) |
| `AGENTS.md` → Doc map | per-run | a doc was added or removed | keep the map listing only docs that exist |
| `docs/decisions.md` (if present) | per-run | a decision was **settled**, or an old one overturned | **append one line** — short description + link to where the reasoning lives (the `decision` ticket's Outcome, else the spec). It is an index: never copy the rationale in. To overturn, add the new line and mark the old `superseded by NNNN`. A still-open question is not a line here — capture it as a `decision` ticket in `backlog.md` |
| `CHANGELOG.md` (if present) | per-run | a user-visible change shipped | add a line under `[Unreleased]` in the right group (Added/Changed/Fixed/Removed). User's view only — not a git-log dump. (`close-sprint` cuts `[Unreleased]` into a release at sprint close) |
| `artefacts/{sprint}/spec_*` | per-run | a spec was implemented this run | update its **Status** (e.g. draft → done) and tick the **acceptance criteria** you met — only with that context in hand; otherwise leave it. Every other file in `artefacts/` (plans, e2e cases, reports) is a frozen run record — never edited here |
| `ASSETS.md` (if present) | sprint-close | assets were added, moved, or repurposed | add/adjust the row(s); keep `Used in` accurate |
| `docs/dev.md` (if present) | sprint-close | setup, env, build/debug workflow, or a dependency quirk changed | record it **only if it is system-independent** — absolute paths, local installs and machine-specific setup go to memory instead, never to both (see below). **Don't** copy api signatures (link codegraph/generated docs) and **don't** log bugs/todos here (→ a ticket in `backlog.md`) |
| `docs/architecture.md` (if present) | sprint-close | a drafted subsystem was actually implemented, or core structure changed | fill the subsystem from the real code and flip its **Status** `planned → implemented`; bump the doc's top **Status** `draft → partial → current`. See below |
| `docs/product/` (if present) | sprint-close | user-facing interaction changed | read the relevant page first, then make very targeted edits |
| `docs/design/` (if present) | never | design system changed | **don't touch here** — the styleguide and mockups are owned by `ui-design` |

**Not maintained here:** everything under `artefacts/` is a frozen run record — the only exception is a `spec_*` file's Status + acceptance criteria (per the table above). Plans, e2e cases, and reports there are pure run history, never edited at the docs step. `localagent/` run records are likewise never touched here. Both are committed as-is by their own workflow.

## The distillation rule

- **`behaviour.md` is the anti-rot core.** The spec was a delta; the durable truth of *how the product now behaves* must land here or the next spec re-derives it. Write the smallest accurate change — this is what makes specs disposable.
- **`architecture.md` draft → filled.** Fill a `planned` subsystem from real code only once it's built, then flip it to `implemented` (doc top `draft → partial → current`). **Never mark `implemented` from a plan.**
- **`dev.md` vs. memory — one home, never both.** Ask: would this still be true on another
  machine? Yes → `docs/dev.md`. No (absolute paths, local installations, personal tool setup,
  machine-only quirks) → `maintain-memory`. Duplicating a fact across both guarantees one of
  the two goes stale.
- **Split** any durable doc past ~300–500 lines into one file per topic under a same-named folder (`architecture/`, `dev/`, `behaviour/`, `decisions/`), keeping the original as the index.

## Audit mode — optional, on request only

**Not part of the normal delta sweep.** Run this only when the user explicitly asks to *audit* doc (or `AGENTS.md`/`CLAUDE.md`) quality — never as a routine pass, or you double-touch docs for nothing. Where the sweep above distils *this run's* delta, the audit judges the *existing* docs against a quality bar and proposes fixes.

1. **Score each doc** against the rubric — commands/workflows current · architecture clarity · non-obvious patterns captured · conciseness (no restating the obvious) · currency (matches the code now) · actionability (executable, not vague). Grade **A** (comprehensive/current) → **F** (missing/stale).
2. **Report before touching anything** — a short per-doc table (score + concrete issues + recommended additions). Present it, then get the user's OK.
3. **Fix targeted** — only genuinely useful additions (real commands, real gotchas, drifted structure); show each as a diff with a one-line *why*. Never pad with generic best-practice or restate what's obvious from the code. Respect each doc's `CONTRACT` header.
