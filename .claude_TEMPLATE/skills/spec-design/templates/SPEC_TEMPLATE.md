# Spec — {feature name}

Date: {YYYY-MM-DD} · Status: {draft | approved | done} · Tickets: {`T-NNN`, … it implements — else —}

Feature spec for `dynamic-workflow` — the *Pflichtenheft*, written by `spec-design`.
**A throwaway artifact:** its durable truth graduates at the docs step — behaviour →
`behaviour.md`, structure → `architecture.md`, decisions → `decisions.md`. Keep it lean and
implementation-facing.

## Goal / problem

{what we're solving and why; the user value}

## Scope

- **In:** {what this feature includes}
- **Out:** {explicitly not included}

## Approach

{the chosen approach in a few sentences, including how components wire together for the main
path; link to `docs/architecture.md` if relevant.}

## Behaviour delta

{the product-semantic change this spec introduces — the rules / invariants / interaction
contracts it adds or alters against `docs/behaviour.md`. The spec's core; the acceptance
criteria below are its testable form. Keep it wherever the project has a `behaviour.md`.}

## Components

{the units touched/added — purpose, interface, dependencies. Implementation guidance.}

## UI / mockups

{only for a feature with a UI — invoke `ui-design`. Self-contained HTML mockup inline, or
linked from `docs/design/mockups/`, designed against `docs/design/Styleguide.html`.}

## Strategy

Decided by `spec-design`; the pipeline follows it without re-deciding.

- **Test:** {none | smoke | core | full-TDD} · **e2e:** {yes | no}
- **Smoke script:** {if smoke — path of the committed smoke script the package writes & the happy path it covers, e.g. `scripts/smoke/{feature}.*`; else —}
- **Modules to test:** {which modules / components the tests must cover — not concrete tests}
- **Implementation — technique:** {direct | tdd | debugging}
- **Implementation — execution:** {inline | subagent-driven: dynamic | subagent-driven: full}
- **e2e test case:** {if e2e, link the handoff file `artefacts/{sprint}/e2e_{feature}.md`}

## Implement packages

One or more, run in order, commit after each. This is the only work-package axis to size.
Where the spec covers several tickets, name the ticket each package serves — that is what
tells the workflow when a ticket may go to `to test`.

- **{package name}** — {what it builds} · ticket: {`T-NNN` or —} · files: {paths to read/touch}

## Acceptance criteria

The testable form of the **Behaviour delta** — each rule/invariant above should map to a check.

- [ ] {observable, testable outcome}
- [ ] {…}

## Open questions

{unresolved points; remove when none. A real *decision* — an architecture/product choice with
trade-offs — becomes a `decision` ticket in `backlog/`, linked here, rather than living in this
frozen spec. Its outcome is later written into `artefacts/{sprint}/sprint-decisions.md`.}
