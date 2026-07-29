# Plan — {topic}

Date: {YYYY-MM-DD}
Sprint: {slug — or `standalone` / `none`}

**Loose scaffold**, Product/UX level (*Lastenheft* — what & why). Produced by `plan`;
recommended input for `spec-design`, usable by any workflow.

<!-- Sprint mode: this file is the SPRINT FILE — the plan above the board. Everything down to
     "Open questions" is frozen once the user approves it; only the Board at the bottom keeps
     moving, until `close-sprint` freezes the whole file with the sprint. -->


## Vision / goal

{the core intent in a few sentences — the problem and the value, in the user's terms}

## Scope

{**Sprint file:** the theme that binds this sprint and what's explicitly **out** — *not* a
feature list. The batch itself is the Board below; the scope is whatever sits in Active.
**Feature plan:** the one capability. Drop this heading if the vision already says it.}

## Target users *(optional)*

{who this is for; relevant contexts of use}

## User stories

- As a {role}, I want {capability}, so that {value}.
- {…}

## Features

*(Feature plan: list them here. **Sprint file: don't** — every item becomes a ticket in
`tickets/` and appears only on the Board. Same for the three sections below.)*

- **{feature}** — {what it does for the user, unambiguously; acceptance in plain words}

## Bugs

- **{bug}** — {observed behaviour vs expected, in user terms}

## UX

- **{improvement}** — {the friction today and the desired experience}

## Refactors

- **{refactor}** — {the user-visible reason it matters: stability, speed, maintainability}

## UI flows *(optional)*

{for screen-based capabilities — the steps the user walks through; reference mockups in
`docs/design/` and product docs where they exist.}

- **{flow name}:** {step 1 → step 2 → …}

## Architecture & domain decisions *(optional — sprint / large plans)*

{high-level only, and only when real architecture is at stake. Record **decisions**, not a doc. System boundaries, data model, key flows, binding
non-functionals, stack/domain choice + rationale.}

## Open questions

{anything still unclear — resolve with the user before handing the plan on. A question that
needs its own investigation is a `decision` ticket, not a line here.}

---

## Board *(sprint file only — the one living section)*

<!-- The column IS the status; tickets carry none. One line per ticket, mirroring `backlog.md`.
     Active   ← `open-sprint` pulls from `backlog.md` (this is the sprint scope)
     To Test  ← the build workflows, at commit
     Done     ← `e2e` or the user, once verified
     At close, `close-sprint` distils Done, carries anything left back to `backlog.md`, and
     freezes this file with the sprint. A mid-sprint bug may enter Active directly. -->

### Active

- `T-NNN` {title} — {spec/plan link, once it exists}

### To Test

- `T-NNN` {title}

### Done

- `T-NNN` {title}
