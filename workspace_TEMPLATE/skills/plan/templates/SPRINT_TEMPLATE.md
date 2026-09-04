# Sprint — {slug}

Date: {YYYY-MM-DD} · template: `~/.agents/skills/plan/templates/SPRINT_TEMPLATE.md`

<!-- The SPRINT FILE: the frame above, the board below. Written by `plan` (sprint mode),
     opened by `open-sprint`, frozen by `close-sprint` with the sprint. Delete both comments
     in this file once it is filled — the template above keeps the protocol.
     - The frame is written once and approved by the user; only a deliberate re-plan touches it.
     - The board is the one living section — the workflows move lines, and that is all.
     - INDEX ONLY: every item is a ticket in `backlog/` and shows here as one line, its Summary
       verbatim. Work detail lives in the ticket, decision rationale in `sprint-decisions.md`
       next to this file. Keep the whole frame under ~a page. -->

## Goal

{the theme that binds this sprint — the problem and the value, in a few sentences}

## Non-goals

{what is explicitly out of this sprint, so nobody re-argues it mid-flight}

## Behaviour context

{the slice of `docs/behaviour.md` this sprint works in — the rules and invariants its tickets
touch, and what shall change. `spec-design` grounds here instead of the full rulebook;
`close-sprint` folds the deltas back. Drop where the project has no `behaviour.md`.}

## (UI) flow *(optional)*

{for a user journey — the steps the user walks through and how the tickets are connected}

## Decisions

{one line per decision this sprint settled, each linking its section in `sprint-decisions.md`
— or `none yet`. Anything still open stays a `decision` ticket on the board.}

---

## Board

<!-- The sprint scope: every ticket pulled into this sprint, one line each, its Summary
     verbatim. The status token is the only thing that moves — flip the word, and leave the
     order and shape of the list as they are.
     open     ← `open-sprint` pulls it from `backlog.md` (this is the sprint scope)
     active   ← a build workflow picked it up
     to test  ← that workflow committed it
     done     ← `e2e` or the user verified it
     At close, `close-sprint` distils the done ones and dissolves their ticket files, carries
     anything unfinished back to `backlog.md`, and freezes this file.
     A mid-sprint bug may be added here directly. -->

### Tickets

- [`T-NNN`](../../backlog/T-NNN-{slug}.md) {title} — {summary} · {open / active / to test / done} · {spec link, once it exists}
