# Spec — {feature name}

Date: {YYYY-MM-DD} · Status: {draft | approved | done} · Tickets: {`T-NNN`, … it implements — else —}

Feature spec for `dynamic-workflow` — the *Pflichtenheft*, written by `spec-design`
(template: `~/.agents/skills/spec-design/templates/SPEC_TEMPLATE.md`).
**A throwaway artifact**, live for its sprint: later specs read its Behaviour delta as current
truth, and at sprint close it graduates — behaviour → `behaviour.md`, structure →
`architecture.md`, decisions → `decisions.md`. Keep it lean and implementation-facing.

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
contracts it adds or alters against the sprint file's **Behaviour context**. The spec's core
and the sprint's current truth until `close-sprint` folds it into `docs/behaviour.md`; the
acceptance criteria below are its testable form. Keep it wherever the project has a
`behaviour.md`.}

## Components

{the units touched/added — purpose, interface, dependencies. Implementation guidance.}

## UI / mockups

{only for a feature with a UI — invoke `ui-design`. Self-contained HTML mockup inline, or
linked from `docs/design/mockups/`, designed against `docs/design/Styleguide.html`.}

## Strategy

Decided by `spec-design`; the pipeline follows it without re-deciding.

- **Testing:** {none | smoke | core | light-tdd | strict-tdd} · **e2e:** {yes | no}
- **Test scope:** {which modules / components the tests must cover — not concrete tests}
- **Smoke script:** {if smoke — path of the committed smoke script the package writes & the happy path it covers, e.g. `scripts/smoke/{feature}.*`; else —}
- **Delegation:** {inline | delegated | delegated+review}
- **e2e test case:** {if e2e, link the handoff file `artefacts/{sprint}/e2e_{feature}.md`}

## Implement packages

One or more, numbered in run order, commit after each. This is the only work-package axis to size —
cut by coherent unit of work, never by what happens to be independently testable. Where the
spec covers several tickets, name the ticket each package serves: that is what tells the
workflow when a ticket may go to `to test`.

**Each package carries its own acceptance criteria** — the observable outcome that makes it
done. Tests are one criterion; the suite goes green once, at the end of the run. With
`light-tdd`, package 1 is the contract & tests package (signatures + tests, confirmed red) and
every later package names the tests it turns green.

1. **{package name}** — {what it builds} · ticket: {`T-NNN` or —} · files: {paths to read/touch}
   - [ ] {observable outcome that makes this package done}
   - [ ] {…}

## Acceptance criteria

The testable form of the **Behaviour delta**, for the feature as a whole — each rule/invariant
above should map to a check. Per-package criteria live with their package.

- [ ] {observable, testable outcome}
- [ ] {…}

## Open questions

{unresolved points; remove when none. A real *decision* — an architecture/product choice with
trade-offs — becomes a `decision` ticket in `backlog/`, linked here, rather than being settled
in a spec. Its outcome is written into `artefacts/{sprint}/sprint-decisions.md`.}
