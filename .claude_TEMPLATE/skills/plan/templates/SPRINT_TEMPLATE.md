# Sprint — {slug}

Date: {YYYY-MM-DD}

<!-- The SPRINT FILE: the frame above, the board below. Produced by `plan` (sprint mode),
     opened by `open-sprint`, frozen by `close-sprint` with the sprint.
     - The frame is written once and approved by the user; only a deliberate re-plan touches it.
     - The board is the one living section — the workflows move lines, nobody rewrites it.
     - NO WORK DETAIL HERE. Every item is a ticket in `tickets/`; this file shows one line per
       ticket, its Summary verbatim. Keep the whole frame under ~a page. -->

## Goal

{the theme that binds this sprint — the problem and the value, in a few sentences}

## Non-goals

{what is explicitly out of this sprint, so nobody re-argues it mid-flight}

## (UI) flow *(optional)*

{for a user journey — the steps the user walks through and how the tickets are connected}

## Architecture & domain decisions *(optional — only when real architecture is at stake)*

{high-level only, and only what this planning actually **settled**: system boundaries, data
model, key flows, binding non-functionals, stack/domain choice + rationale. These graduate to
`docs/decisions.md`. Anything still open is a `decision` ticket on the board, not a line here.}

---

## Board

<!-- The column IS the status; tickets carry none. One line per ticket, same shape as `backlog.md`.
     Active   ← `open-sprint` pulls from `backlog.md` (this is the sprint scope)
     To Test  ← the build workflows, at commit
     Done     ← `e2e` or the user, once verified
     At close, `close-sprint` distils Done, carries anything left back to `backlog.md`, and
     freezes this file. A mid-sprint bug may enter Active directly. -->

### Active

- `T-NNN` {title} — {summary} · {spec link, once it exists}

### To Test

- `T-NNN` {title} — {summary}

### Done

- `T-NNN` {title} — {summary}
