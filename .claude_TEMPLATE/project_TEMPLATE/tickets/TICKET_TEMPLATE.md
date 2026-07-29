# T-NNN — {title}

<!-- CONTRACT (binding):
  - PURPOSE: one unit of work — the *what* and the *why*. Nothing else.
  - WRITTEN ONCE, THEN FROZEN. Only the Links section may gain a line later.
  - CAP ~half a page. The *how* is a plan/spec in `artefacts/{sprint}/` — link it, never inline it.
  - NO STATUS FIELD: the board position is the status (`backlog.md` while open, the sprint
    file's board while in flight). A ticket is listed in exactly one of them, never both.
  - FILE lives permanently in `tickets/`, named `T-NNN-{slug}.md`; it never moves between
    sprints. IDs are never reused.
  - WHO WRITES: `plan`, or any run that spots something worth capturing.
-->

- **Category:** {feature | bug | decision | ux | refactor | chore}
- **Importance:** {critical | high | medium | low}
- **Effort:** {S | M | L}
- **Depends on:** {`T-NNN`, … — or `none`}

## Why

{the problem or the value, in the user's terms. Bug → observed vs expected behaviour.
Decision → the forces that make this a real choice.}

## What

{the unambiguous outcome, acceptance in plain words. No solution design.
Decision → the question to settle, and the options if they're already known.}

## Links *(optional)*

{plan / spec / mockup / decision entry, added as they come into being}
