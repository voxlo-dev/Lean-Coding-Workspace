---
name: maintain-docs
description: "Use at the docs step of the minimal and dynamic workflows (per-run mode: the decision pair, spec status, pointers), and at sprint close from close-sprint (sprint-close mode: behaviour.md plus the batched low-churn docs). Triggers: code, assets, architecture, or user-facing behaviour changed and docs may have drifted."
---

# Maintain Docs

**Graduate durable truth into `docs/`.** This is the step that stops docs from rotting: specs describe *deltas*, and here they turn into durable docs, so a reader gets current truth from one place instead of stacked spec addenda. Judge each doc from the changes you made *before* opening it, and touch only what your mode owns.

**Two modes — the caller sets it:**

- **Per-run** (minimal/dynamic-workflow) — only what is an **input to the next run**: the decision pair, the spec's Status/ACs, the pointers in `AGENTS.md`. Small, hot, cheap. The run's behaviour delta stays in its spec, which is what `spec-design` reads.
- **Sprint-close** (`close-sprint`) — the rest, `behaviour.md` first, then `architecture.md`, `dev.md`, `product/`, `ASSETS.md`. Nobody reads them *during* a sprint, and they come out better written from the whole sprint at once than from five separate deltas.

**Four rules that override everything below:**

- **Lifespan split.** `docs/` = durable truth about the *shipped* system, `artefacts/` = the sprint's process history, live until `close-sprint` freezes it. You **write into `docs/`**; in `artefacts/` you touch exactly two things — a `spec_*` file's Status/ACs and the current sprint's `sprint-decisions.md` (append-only). The workflows and `close-sprint` move the board.
- **No durable doc references `artefacts/`** — it must stand on its own, not depend on a folder that gets closed. Distil what you need out of a spec instead of linking it. **Exception: `docs/decisions.md`**, whose index lines link their `sprint-decisions.md` section by design.
- **Present state only** (global) — no "not", "no longer", "used to", no removal notes: delete the obsolete sentence rather than annotate it. Only `docs/decisions.md` (`superseded by`) and `artefacts/` record history.
- **Respect each doc's `CONTRACT` header, then drop it.** It states what belongs in the doc, what stays out, and (for `architecture.md`) the status mechanic. Follow it, and **delete it the first time you fill that doc**, leaving the one-line pointer to its template. The section layout under it is a *suggestion*: restructure within the contract, remove any section you leave empty.

## Step 1 — Relevance gate

Pure internal refactors, typo/formatting fixes and test-only changes → skip the sweep.

Then per edit the global **write only what gets read again** test: will anyone read it · is it relevant to someone other than the user · will it still be true in a month. A small change earns a small edit — often one sentence, sometimes none; a paragraph on a two-line change is drift waiting to happen.

## Step 2 — Per-doc decision (decide before reading)

For each doc ask "does *this* change affect it?" from what you already know, and open it only on yes. **The optional docs only exist if the project opted in at init — skip any that isn't there, the project chose against it.**

| Doc | Mode | Update when | Action |
| --- | --- | --- | --- |
| `AGENTS.md` → Current sprint | per-run | the sprint pointer moved | keep the one-line pointer correct; also fix code-style/conventions if they changed (single source; the README points here) |
| `AGENTS.md` → Doc map | per-run | a doc was added or removed | keep the map listing only docs that exist |
| `artefacts/{sprint}/sprint-decisions.md` | per-run | a decision was **settled** this run — with or without a `decision` ticket | **append a `##` section**, ~15 lines: the forces, what was decided, why over the alternatives. Write it now, not at sprint close, so the rest of the sprint doesn't re-litigate it. Create the file from `shape/templates/SPRINT_DECISIONS_TEMPLATE.md` if missing |
| `docs/decisions.md` | per-run | same trigger — **write both in one move** | **append one line**: take `Next decision` from the top and increment it, short description, `accepted`, link to that section. An index, with the rationale kept in the sprint file. To overturn, append the new decision and mark the old line `superseded by NNNN`. A still-open question stays a `decision` ticket |
| `artefacts/{sprint}/spec_*` | per-run | a spec was implemented this run | update its **Status** (draft → done) and tick the **acceptance criteria** met — only with that context in hand |
| `docs/behaviour.md` | **sprint-close** | the sprint changed product behaviour, a rule, or a UX invariant | **your first stop at close.** Fold the sprint's behaviour deltas — from its `spec_*` files and the sprint file's Behaviour context — into the rulebook in place, one pass over the whole sprint. Present tense and declarative; rationale lives in `decisions.md` |
| `ASSETS.md` | sprint-close | assets were added, moved, or repurposed | add/adjust the row(s); keep `Used in` accurate |
| `docs/dev.md` | sprint-close | setup, env, build/debug workflow, or a dependency quirk changed | record it **only if system-independent** — machine-specific setup goes to memory instead. Link codegraph/generated docs for API signatures, and file bugs/todos as tickets |
| `docs/architecture.md` | sprint-close | a drafted subsystem was implemented, or core structure changed | fill the subsystem from the real code, flip its **Status** `planned → implemented`, bump the doc's top **Status** `draft → partial → current` |
| `docs/product/` | sprint-close | user-facing interaction changed | read the relevant page first, then edit very targeted |
| `docs/design/` | never | design system changed | **`ui-design` owns the styleguide and mockups** — leave them to it |

**Not maintained here:** everything else under `artefacts/` — plans, e2e cases, reports, `release-{version}/` — belongs to the workflow that wrote it, which corrects it while the sprint runs. `docs/release.md` is the one durable doc owned elsewhere: the user writes it, `release` appends its waivers.

## The distillation rule

- **`behaviour.md` is the anti-rot core, and the product overview `shape` reads whole.** Fold the sprint's deltas in at close, one pass, smallest accurate change — that is what makes specs disposable.
- **`architecture.md` draft → filled.** Fill a `planned` subsystem from the real code once it's built, and flip its status from that code — a plan alone leaves it `planned`.
- **`dev.md` vs. memory — one home, never both.** Would this still be true on another machine? Yes → `docs/dev.md`. No (absolute paths, local installations, personal tool setup, machine-only quirks) → `maintain-memory`.
- **Split** any durable doc past ~300–500 lines into one file per topic under a same-named folder, keeping the original as the index.

## Audit mode — optional, on request only

**Not part of the normal delta sweep.** Run only when the user explicitly asks to *audit* doc (or `AGENTS.md`) quality. Where the sweep distils *this run's* delta, the audit judges the *existing* docs against a quality bar.

1. **Score each doc** — commands/workflows current · architecture clarity · non-obvious patterns captured · conciseness · currency (matches the code now) · actionability. Grade **A** (comprehensive/current) → **F** (missing/stale).
2. **Report before touching anything** — a short per-doc table (score, concrete issues, recommended additions), then get the user's OK.
3. **Fix targeted** — only genuinely useful additions (real commands, real gotchas, drifted structure), each as a diff with a one-line *why*. Keep every addition project-specific and non-obvious.
