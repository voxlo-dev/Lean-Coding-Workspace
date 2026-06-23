---
name: maintain-docs
description: "Use at the commit step of the minimal and spec workflows. Triggers: code, assets, architecture, or user-facing behaviour changed and docs may have drifted."
---

# Maintain Docs

Keep docs current for **little time and few tokens**. The win is deciding what NOT to touch: judge each doc from the change you already made, *before* reading or editing it. Most commits touch zero or one doc.

## Step 1 — Relevance gate

Were there changes a reader would care about? Pure internal refactors, typo fixes, formatting, and test-only changes → **stop, update nothing.**

## Step 2 — Per-doc decision (decide before reading)

For each doc, ask "does *this* change affect it?" from what you already know. Open a doc only if the answer is yes. **Optional docs (`ASSETS.md`, `docs/architecture/`, `docs/wiki/`) only exist if the project opted in at init — if a doc isn't there, skip it; never re-create what the project chose not to have.**

| Doc | Update when | Action |
| --- | --- | --- |
| `AGENTS.md` → Living context | goals or open decisions changed | edit the relevant subsection; also fix code-style/conventions if they changed (single source — never copy into README). **Gotchas/learnings go to project memory via `maintain-memory`, not here.** |
| `AGENTS.md` → Doc map | a doc was added or removed | keep the map listing only docs that exist |
| `ASSETS.md` (if present) | assets were added, moved, or repurposed | add/adjust the row(s); keep `Used in` accurate |
| `docs/architecture/` (if present) | core architecture changed or was extended | see Architecture below |
| `docs/wiki/` (if present) | user-facing interaction changed | read the wiki Home page first, then make very targeted edits |

**Never touched here:**

- `CHANGELOG.md` — a future release skill owns it.
- `docs/specs/*` — specs are inputs, not maintained output.

## Architecture

- Record core changes/additions; keep edits factual, no speculation.
- **Split when it grows past ~300–500 lines:** break the single document into one file per subsystem under `docs/architecture/`, and keep a short top-level overview (`ARCHITECTURE.md`) linking them.

## Principle

Minimal, factual edits. Currentness over completeness. If unsure whether a doc needs updating, the relevance gate already answered: probably not.
