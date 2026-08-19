---
name: maintain-docs
description: "Use at the docs step of the minimal and dynamic workflows (per-run mode: the docs the next run reads), and at sprint close from close-sprint (sprint-close mode: the batched low-churn docs). Triggers: code, assets, architecture, or user-facing behaviour changed and docs may have drifted."
---

# Maintain Docs

**Distill the run's durable truth into `docs/`.** This is the step that stops docs from rotting: the spec described a *delta*, here it graduates into durable docs so the next session reads current truth from one place, not from stacked spec addenda. Judge each doc from the changes you made *before* opening it, and touch only what your mode owns.

**Two modes — the caller sets it:**

- **Per-run** (minimal/dynamic-workflow) — only what is an **input to the next run**: `behaviour.md`, the decision pair, the spec's Status/ACs. Small, hot, cheap. These can't wait: `spec-design` reads `behaviour.md` as current truth, and a stale one forces the next spec to reconstruct the present from frozen deltas.
- **Sprint-close** (`close-sprint`) — the rest: `architecture.md`, `dev.md`, `product/`, `ASSETS.md`. Nobody reads them *during* a sprint, and they come out better written from the whole sprint at once than from five separate deltas.

**Two rules that override everything below:**

- **Lifespan split.** `docs/` = durable truth about the *shipped* system, `artefacts/` = frozen process history. You **write into `docs/`**; in `artefacts/` you touch exactly two things — a `spec_*` file's Status/ACs and the current sprint's `sprint-decisions.md` (append-only, the current sprint's). The workflows and `close-sprint` move the board.
- **Respect each doc's contract header.** Every durable doc opens with a `CONTRACT` comment: what belongs in it, what stays out, and (for `architecture.md`) the status mechanic. The section layout under it is a *suggestion* — restructure to fit the project within its contract, and remove any section you leave empty.

## Step 1 — Relevance gate

Pure internal refactors, typo/formatting fixes and test-only changes → skip the sweep.

## Step 2 — Per-doc decision (decide before reading)

For each doc ask "does *this* change affect it?" from what you already know, and open it only on yes. **The optional docs only exist if the project opted in at init — skip any that isn't there, the project chose against it.**

| Doc | Mode | Update when | Action |
| --- | --- | --- | --- |
| `docs/behaviour.md` | **per-run** | product behaviour, a rule, or a UX invariant changed | **your first stop.** Update the rule/invariant/interaction-contract in place so this doc carries the current truth — a spec is a throwaway, this is the state. Present tense and declarative; history and rationale live in `decisions.md` |
| `AGENTS.md` → Current sprint | per-run | the sprint pointer moved | keep the one-line pointer correct; also fix code-style/conventions if they changed (single source; the README points here) |
| `AGENTS.md` → Doc map | per-run | a doc was added or removed | keep the map listing only docs that exist |
| `artefacts/{sprint}/sprint-decisions.md` | per-run | a decision was **settled** this run — with or without a `decision` ticket | **append a `##` section**, ~15 lines: the forces, what was decided, why over the alternatives. Write it now, not at sprint close, so the rest of the sprint doesn't re-litigate it. Create the file from `plan/templates/SPRINT_DECISIONS_TEMPLATE.md` if missing |
| `docs/decisions.md` | per-run | same trigger — **write both in one move** | **append one line**: next free `NNNN`, short description, `accepted`, link to that section. An index, with the rationale kept in the sprint file. To overturn, append the new decision and mark the old line `superseded by NNNN`. A still-open question stays a `decision` ticket |
| `CHANGELOG.md` | per-run | a user-visible change shipped | one line under `[Unreleased]` in the right group (Added/Changed/Fixed/Removed), written as the user's view of the change |
| `artefacts/{sprint}/spec_*` | per-run | a spec was implemented this run | update its **Status** (draft → done) and tick the **acceptance criteria** met — only with that context in hand |
| `ASSETS.md` | sprint-close | assets were added, moved, or repurposed | add/adjust the row(s); keep `Used in` accurate |
| `docs/dev.md` | sprint-close | setup, env, build/debug workflow, or a dependency quirk changed | record it **only if system-independent** — machine-specific setup goes to memory instead. Link codegraph/generated docs for API signatures, and file bugs/todos as tickets |
| `docs/architecture.md` | sprint-close | a drafted subsystem was implemented, or core structure changed | fill the subsystem from the real code, flip its **Status** `planned → implemented`, bump the doc's top **Status** `draft → partial → current` |
| `docs/product/` | sprint-close | user-facing interaction changed | read the relevant page first, then edit very targeted |
| `docs/design/` | never | design system changed | **`ui-design` owns the styleguide and mockups** — leave them to it |

**Not maintained here:** everything else under `artefacts/` — plans, e2e cases, reports, `localagent/` records — is a frozen run record, committed as-is by its own workflow.

## The distillation rule

- **`behaviour.md` is the anti-rot core.** The durable truth of *how the product now behaves* must land here or the next spec re-derives it. Write the smallest accurate change — this is what makes specs disposable.
- **`architecture.md` draft → filled.** Fill a `planned` subsystem from the real code once it's built, and flip its status from that code — a plan alone leaves it `planned`.
- **`dev.md` vs. memory — one home, never both.** Would this still be true on another machine? Yes → `docs/dev.md`. No (absolute paths, local installations, personal tool setup, machine-only quirks) → `maintain-memory`.
- **Split** any durable doc past ~300–500 lines into one file per topic under a same-named folder, keeping the original as the index.

## Audit mode — optional, on request only

**Not part of the normal delta sweep.** Run only when the user explicitly asks to *audit* doc (or `AGENTS.md`/`CLAUDE.md`) quality. Where the sweep distils *this run's* delta, the audit judges the *existing* docs against a quality bar.

1. **Score each doc** — commands/workflows current · architecture clarity · non-obvious patterns captured · conciseness · currency (matches the code now) · actionability. Grade **A** (comprehensive/current) → **F** (missing/stale).
2. **Report before touching anything** — a short per-doc table (score, concrete issues, recommended additions), then get the user's OK.
3. **Fix targeted** — only genuinely useful additions (real commands, real gotchas, drifted structure), each as a diff with a one-line *why*. Keep every addition project-specific and non-obvious.
