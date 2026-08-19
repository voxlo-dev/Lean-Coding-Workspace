# T-NNN — {title}

<!-- CONTRACT (binding):
  - One unit of work: the *what* and the *why*, ~1 page. Solution design belongs to a spec in
    `artefacts/{sprint}/`. Written once, then frozen — only Links may grow.
  - Lives at `backlog/T-NNN-{slug}.md` its whole life, IDs never reused. Boards *index* it, so
    it stays put across sprints.
  - The board position is the status, and it is carried in exactly one board: `backlog.md`
    until pulled, the sprint board once in flight.
  - DISSOLVES at `close-sprint` — a done ticket is deleted once its durable truth has
    graduated: decisions → `artefacts/{sprint}/sprint-decisions.md`, behaviour →
    `docs/behaviour.md`, user-visible change → `CHANGELOG.md`. Git history keeps the rest.
  - Writer: `plan`, or any run that spots something worth capturing.
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

## Links *(optional)*

{spec / mockup / related tickets, added as they come into being.
A settled `decision` ticket needs none — its outcome is written straight into
`artefacts/{sprint}/sprint-decisions.md`, and this file then dissolves.}
