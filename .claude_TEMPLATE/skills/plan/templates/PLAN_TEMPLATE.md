# Plan — {topic}

Date: {YYYY-MM-DD}
Sprint: {slug — or `standalone` / `none`}

**Loose scaffold**, Product/UX level (*Lastenheft* — what & why). Produced by `plan`;
recommended input for `spec-design`, usable by any workflow.

## Vision / goal

{the core intent in a few sentences — the problem and the value, in the user's terms}

## Scope

{**Sprint plan:** the batch of features/fixes under this umbrella, and what's explicitly out.
**Feature plan:** the one capability. Drop this heading if the vision already says it.}

## Target users *(optional)*

{who this is for; relevant contexts of use}

## User stories

- As a {role}, I want {capability}, so that {value}.
- {…}

## Features

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

{anything still unclear — resolve with the user before handing the plan on}
