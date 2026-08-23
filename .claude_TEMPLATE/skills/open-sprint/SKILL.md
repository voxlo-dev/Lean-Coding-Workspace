---
name: open-sprint
description: "Use to plan and open the next sprint via the `plan` skill in sprint-file mode: architecture-aware, optional web research, possible domain switch — then pull tickets from the backlog onto the sprint board, branch, set Current sprint, record decisions. Also the entry point for a brand-new project (nothing to close). Hands off to dynamic-workflow. Invokable by Claude or via /open-sprint."
---

# Open Sprint

The planning half of the sprint cycle, and the thinking layer above the build workflows. One run = one **new sprint**: plan its scope, open its branch, point the project at it. Architecture *decisions* live here; the architecture *doc* is `maintain-docs`' job once things are implemented.

**Sizing a sprint:** the whole batch that ships together, under one `artefacts/{sprint}/` folder and one branch, spanning many workflow runs — a handful to a few dozen tickets, 1–5 specs (1–10 packages each), 0–3 e2e runs, several minimal-workflow fixes, a few docs passes. Its length is the **user's call**: no time-boxes, no story points.

**Leave the product code untouched here** — this skill produces the sprint file, the branch and the living-context update, then hands off.

## 0. Detect the mode

Read `AGENTS.md` → **Current sprint**.

- **Not scaffolded yet** (no `AGENTS.md`) → `project-initialiser` first, then come back for the first sprint.
- **A sprint is still active** → close it first (`close-sprint`), then return.
- **New project, or no active sprint** → continue. A brand-new project plans **wide** — deep brainstorm + research + domain/stack — and still writes a sprint file rather than an architecture doc.

## 1. Plan the sprint

Invoke **`plan` in sprint-file mode** — it owns the planning dialogue, the architecture/domain decisions, the optional web research, the ticket cut, and (new project) the styleguide-level `ui-design` call. It writes `artefacts/{sprint}/sprint.md`: theme, architecture decisions, non-goals, and the board underneath.

- **Ground it** for `plan`: **`backlog/backlog.md` first** (what's already open and waiting), then **`docs/behaviour.md` read whole** — the slice it cuts becomes the sprint file's Behaviour context — then codegraph if indexed, `AGENTS.md`, `docs/architecture.md` (draft or filled), `docs/decisions.md` if present, project memory. For a brand-new project note the target instead and plan **wide**.
- **A plan may already exist** (pasted from a Claude chat, a document, tickets already in the backlog) → feed it to `plan` as the basis.

## 2. Pull the scope onto the board

The sprint scope **is** the board — one list, nowhere else.

- Take from `backlog.md` **Backlog** (Draft first needs refining) plus whatever `plan` just cut, and move those lines onto the sprint board at status `open`. Delete them from `backlog.md` — a ticket is indexed in exactly one place — leaving its `Next ticket` counter alone; numbers only go up. **The ticket files stay in `backlog/`** and the board links them.
- **Respect `Depends on`** — pull a ticket only with its dependencies.
- Size it against the sprint's theme rather than ambition. Anything not pulled stays in the backlog; that's the point of having one.

Get the user's approval on plan **and** pulled scope before moving on.

## 3. Open the branch & update living context

- **New sprint branch** `<sprint-slug>` (one branch per sprint). Ask for the slug if unclear.
- **`AGENTS.md`** — set **Current sprint** to the new slug, the only living pointer here; goals live in the sprint file.
- **Record what the planning settled** (if the project has `docs/decisions.md`) — per decision: a `##` section with the reasoning in `artefacts/{sprint}/sprint-decisions.md`, a one-liner in the sprint file's **Decisions** section, and one `accepted` line in `docs/decisions.md`, taking **`Next decision` from the top of that file and incrementing it**. Index lines stay rationale-free. Anything still open is a `decision` ticket on the board.

Then hand off to `dynamic-workflow` (spec each ticket on the board) — or `minimal-workflow` for the small stuff; `spec-design` reads the ticket as its requirements basis.

## Handoff & boundaries

- Produces: the sprint file with its board (via `plan`), the tickets in `backlog/`, `sprint-decisions.md` plus its index lines in `docs/decisions.md`, the branch, the `Current sprint` update.
- The architecture/behaviour docs, specs and product code belong to `maintain-docs`, `spec-design` and the build workflows; `maintain-memory` runs at the workflows' memory step.
