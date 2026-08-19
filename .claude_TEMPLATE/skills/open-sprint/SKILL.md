---
name: open-sprint
description: "Use to plan and open the next sprint (release scope) via the `plan` skill in sprint-file mode: architecture-aware, optional web research, possible domain switch — then pull tickets from the backlog onto the sprint board, branch, set Current sprint, record decisions. Also the entry point for a brand-new project (nothing to close). Hands off to dynamic-workflow. Invokable by Claude or via /open-sprint."
---

# Open Sprint

The planning half of the sprint cycle, and the thinking layer above the build workflows.
One run = one **new sprint**: plan the release scope, open its branch, point the project at
it. Architecture *decisions* live here; the architecture *doc* does not — `maintain-docs`
fills that once things are actually implemented.

**A sprint is a release scope, not a run.** It is the whole batch of work that ships
together, under one `artefacts/{sprint}/` folder and one branch — spanning **many workflow
runs**: a handful to a few dozen tickets, 1–5 specs (1–10 packages each), 0–3 e2e runs,
several minimal-workflow fixes, a few maintain-docs passes. Its length is the **user's
call** — no time-boxes, no story points. Never open a new sprint per run or per feature.
**No sprint at all is fine** — a lone `minimal-workflow` fix or a maintenance pass needs none.

**Change nothing in the product code here** — this skill produces the sprint file, the
branch, and the living-context update, then hands off.

## 0. Detect the mode

Read `AGENTS.md` → **Current sprint**.

- **Not scaffolded yet** (no `AGENTS.md`) → run `project-initialiser` first, then come back here for the first sprint.
- **A sprint is still active** → it must be closed first: run **`close-sprint`**, then return here.
- **New project, or no active sprint** → continue. A brand-new project runs the planning
  **wide** — deep brainstorm + research + domain/stack — but writes a sprint file, not an
  architecture doc.

## 1. Plan the sprint

Invoke **`plan` in sprint-file mode** — it owns the planning dialogue, the architecture/domain
decisions, the optional web research, the ticket cut, and (new project) the styleguide-level
`ui-design` call. It writes `artefacts/{sprint}/sprint.md`: the **sprint file** — theme,
architecture decisions, non-goals, and the board underneath.

- **Ground it** for `plan`: **`backlog/backlog.md` first** (what's already open and waiting), then codegraph if indexed, `AGENTS.md`, `docs/architecture.md` (draft or filled), `docs/behaviour.md` and `docs/decisions.md` if present, project memory. For a brand-new project, note the target instead and run the planning **wide**.
- **A plan may already exist** (pasted from a Claude chat, a document, tickets already sitting in the backlog) → feed it to `plan` as the basis rather than re-deriving it.

## 2. Pull the scope onto the board

The sprint scope **is** the board — no second list anywhere.

- Take from `backlog.md` **Backlog** (not Draft — refine it first or leave it) plus whatever `plan` just cut, and move those lines onto the sprint board at status `open`. Delete them from `backlog.md`: a ticket is indexed in exactly one place. **The ticket files don't move** — they stay in `backlog/` and the board links them.
- **Respect `Depends on`** — don't pull a ticket whose dependency stays behind.
- Size it against the sprint's theme, not against ambition. Anything not pulled simply stays in the backlog; that's the point of having one.

Get the user's approval on plan **and** the pulled scope before moving on. `dynamic-workflow`
then specs each ticket on the board; `spec-design` reads the ticket as its requirements basis.

## 3. Open the branch & update living context

- **New sprint branch** — create `<sprint-slug>` (matches the workspace branching rule: one branch per sprint). Ask the user for the slug if unclear.
- **`AGENTS.md`** — set **Current sprint** to the new slug (the only living pointer here). Goals live in the sprint file, not in `AGENTS.md`.
- **Record what the planning settled** (only if the project has `docs/decisions.md`) — for each decision: a `##` section with the reasoning in `artefacts/{sprint}/sprint-decisions.md`, a one-liner in the sprint file's **Decisions** section, and one `accepted` line in `docs/decisions.md` (next free `NNNN`, linking the sprint-decisions file). Index lines never carry the rationale. Anything still open is a `decision` ticket on the board, never a line here.

Then hand off to `dynamic-workflow` (spec each ticket on the board) — or `minimal-workflow` for the small stuff.

## Handoff & boundaries

- Produces: the sprint file `sprint.md` with its board (via `plan`), the tickets in `backlog/`, the sprint's `sprint-decisions.md` plus its index lines in `docs/decisions.md`, the branch, the `Current sprint` update.
- Never writes the architecture/behaviour docs, specs, or product code — those belong to `maintain-docs`, `spec-design`, and the build workflows. `maintain-memory` runs at the workflows' memory step, not here.
