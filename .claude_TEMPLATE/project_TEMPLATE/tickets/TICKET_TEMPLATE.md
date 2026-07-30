# T-NNN — {title}

<!-- CONTRACT (binding):
  - PURPOSE: one unit of work, in full — the *what* and the *why*. This is where the detail
    lives: the sprint file and `backlog.md` only carry a one-line summary of it.
  - WRITTEN ONCE, THEN FROZEN. Two exceptions: the Links section may gain a line, and a
    `decision` ticket gains its Outcome when it is settled.
  - CAP ~1 page. Still no solution design — the *how* is a spec in `artefacts/{sprint}/`.
  - NO STATUS FIELD: the board position is the status (`backlog.md` while open, the sprint
    file's board while in flight). A ticket is listed in exactly one of them, never both.
  - FILE lives permanently in `tickets/`, named `T-NNN-{slug}.md`; it never moves between
    sprints. IDs are never reused.
  - WHO WRITES: `plan`, or any run that spots something worth capturing.
-->

- **Summary:** {one line — this is what the board and the backlog show}
- **Category:** {feature | bug | decision | ux | refactor | chore}
- **Importance:** {critical | high | medium | low}
- **Effort:** {S | M | L}
- **Depends on:** {`T-NNN`, … — or `none`}

## Why

{the problem or the value, in the user's terms. Bug → observed vs expected behaviour.
Decision → the forces that make this a real choice.}

## What

{the unambiguous outcome. No solution design.
Decision → the question to settle, and the options if they're already known.}

## User story *(optional)*

As a {role}, I want {capability}, so that {value}.

## Outcome *(decision tickets only — filled when settled)*

{what was decided and why, in a few lines. This is the detail behind the one-line entry in
`docs/decisions.md`; that index links here rather than repeating it.}

## Links *(optional)*

{spec / mockup / decision entry / related tickets, added as they come into being}
