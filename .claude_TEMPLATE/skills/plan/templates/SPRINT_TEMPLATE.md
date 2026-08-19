# Sprint — {slug}

Date: {YYYY-MM-DD}

<!-- The SPRINT FILE: the frame above, the board below. Written by `plan` (sprint mode),
     opened by `open-sprint`, frozen by `close-sprint` with the sprint.
     - The frame is written once and approved by the user; only a deliberate re-plan touches it.
     - The board is the one living section — the workflows move lines, nobody rewrites it.
     - NO WORK DETAIL HERE. Every item is a ticket in `backlog/`; this file shows one line per
       ticket, its Summary verbatim. Keep the whole frame under ~a page.
     - NO DECISION RATIONALE HERE — that goes to `sprint-decisions.md` next to this file. -->

## Goal

{the theme that binds this sprint — the problem and the value, in a few sentences}

## Non-goals

{what is explicitly out of this sprint, so nobody re-argues it mid-flight}

## (UI) flow *(optional)*

{for a user journey — the steps the user walks through and how the tickets are connected}

## Decisions

{one line per decision this sprint settled, each linking its section in `sprint-decisions.md`
— or `none yet`. Anything still open is a `decision` ticket on the board, not a line here.}

---

## Board

<!-- The sprint scope: every ticket pulled into this sprint, one line each, its Summary
     verbatim. The status token is the only thing that moves — flip the word, never re-sort
     or restructure the list. Ticket files stay in `backlog/`; this board only indexes them.
     open     ← `open-sprint` pulls it from `backlog.md` (this is the sprint scope)
     active   ← a build workflow picked it up
     to test  ← that workflow committed it
     done     ← `e2e` or the user verified it
     At close, `close-sprint` distils the done ones and dissolves their ticket files, carries
     anything unfinished back to `backlog.md`, and freezes this file.
     A mid-sprint bug may be added here directly. -->

### Tickets

- [`T-NNN`](../../backlog/T-NNN-{slug}.md) {title} — {summary} · {open / active / to test / done} · {spec link, once it exists}
