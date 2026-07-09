---
name: maintain-docs
description: "Use at the docs step of the minimal and dynamic workflows. Triggers: code, assets, architecture, or user-facing behaviour changed and docs may have drifted."
---

# Maintain Docs

Keep the docs current — **living context first**, everything else by priority. Judge each doc from the changes you made *before* opening it, and spend effort by tier: always refresh living context, don't labour a low-priority doc.

## Step 1 — Relevance gate

Were there changes a reader would care about? Pure internal refactors, typo/formatting fixes, and test-only changes → you can skip the doc sweep. 

## Step 2 — Per-doc decision (decide before reading)

For each doc, ask "does *this* change affect it?" from what you already know. Open a doc only if the answer is yes. **Optional docs (`ASSETS.md`, `docs/architecture/`, `docs/developer/`, `docs/wiki/`, `docs/design/`) only exist if the project opted in at init — if a doc isn't there, skip it; never re-create what the project chose not to have.**

| Doc | Priority | Update when | Action |
| --- | --- | --- | --- |
| `AGENTS.md` → Living context | high | goals, decisions, or direction moved | your first stop — keep it current every run; edit the relevant subsection, also fix code-style/conventions if they changed (single source — never copy into README). **Gotchas/learnings go to project memory via `maintain-memory`, not here.** |
| `AGENTS.md` → Doc map | high | a doc was added or removed | keep the map listing only docs that exist |
| `docs/artefacts/{sprint}/spec_*` | when in context | a spec was implemented this run | update its **Status** (e.g. draft → done) and tick the **acceptance criteria** you met — only with that context in hand; otherwise leave it. Every other file in `docs/artefacts/` (plans, e2e cases, reports) is a frozen run record — never edited here |
| `ASSETS.md` (if present) | medium | assets were added, moved, or repurposed | add/adjust the row(s); keep `Used in` accurate |
| `docs/developer/` (if present) | medium | api reference, guides, key decisions, long-term todos, or known bugs changed | see Architecture & developer docs below |
| `docs/architecture/` (if present) | low | a drafted subsystem was actually implemented, or core architecture changed | fill the subsystem from the real code and flip its **Status** `planned → implemented`; bump the doc's top **Status** `draft → partial → current`. See Architecture & developer docs below |
| `docs/wiki/` (if present) | low | user-facing interaction changed | read the wiki Home page first, then make very targeted edits |
| `docs/design/` (if present) | — | design system changed | **don't touch here** — the styleguide and mockups are owned by `ui-design` |

**Not maintained here:** everything under `docs/artefacts/` is a frozen run record — the only exception is a `spec_*` file's Status + acceptance criteria (per the table above). Plans, e2e cases, and reports there are pure run history, never edited at the docs step. `localagent/` run records are likewise never touched here. Both are committed as-is by their own workflow.

## Architecture & developer docs

- **Draft → filled.** `project-initialiser` seeds `Architecture.md` as a `draft` skeleton whose subsystems are marked `planned`. When a subsystem is genuinely built, fill it from the real code and set its **Status** to `implemented`; raise the doc's top **Status** to `partial`, and to `current` once no `planned` subsystem remains. **Never mark `implemented` from a plan alone — only from shipped code.**
- Record core changes/additions; keep edits factual, no speculation. Keep `Architecture.md` focused on the software architecture; engineering detail that would clutter it — api reference, guides, key decisions, long-term todos, known bugs — belongs in `docs/developer/`.
- **Split either when it grows past ~300–500 lines:** break the document into one file per subsystem/topic under `docs/architecture/` or `docs/developer/`, keeping a short top-level index (`Architecture.md` / `Developer-Docs.md`) that links them.

