# Spec — {feature name}

Date: {YYYY-MM-DD} · Status: {draft | approved | done} · Plan: {link `artefacts/{sprint}/plan_{feature}.md` if any, else —}

Feature spec for `dynamic-workflow` — the *Pflichtenheft*. Comes out of `spec-design`.
**This is a throwaway artifact:** the durable truth it produces graduates elsewhere at the
docs step — behaviour → `behaviour.md`, structure → `architecture.md`, decisions →
`decisions.md`. Keep the spec lean and implementation-facing; don't turn it into documentation.

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
contracts it adds or alters against `docs/behaviour.md`. The spec's core: the acceptance
criteria below are its testable form. Drop only if the project has no `behaviour.md`.}

## Components

{the units touched/added — purpose, interface, dependencies. Implementation guidance.}

## UI / mockups

{only if this feature has a UI — invoke `ui-design`. Self-contained HTML mockup inline,
or linked from `docs/design/mockups/`; design against `docs/design/Styleguide.html`. Remove
this section if there's no UI.}

## Strategy

Decided by `spec-design`; the pipeline follows it without re-deciding.

- **Test:** {none | minimal | core | full-TDD} · **e2e:** {yes | no}
- **Smoke script:** {if minimal — path of the committed smoke script the package writes & the happy path it covers, e.g. `scripts/smoke/{feature}.*`; else —}
- **Modules to test:** {which modules / components the tests must cover — not concrete tests}
- **Implementation — technique:** {direct | tdd | debugging}
- **Implementation — execution:** {inline | subagent-driven: dynamic | subagent-driven: full}
- **e2e test case:** {if e2e, link the handoff file `artefacts/{sprint}/e2e_{feature}.md`}

## Implement packages

One or more, run in order, commit after each. This is the only work-package axis to size.

- **{package name}** — {what it builds} · files: {paths to read/touch}

## Acceptance criteria

The testable form of the **Behaviour delta** — each rule/invariant above should map to a check.

- [ ] {observable, testable outcome}
- [ ] {…}

## Open questions

{unresolved points; remove when none. A real *decision* (an architecture/product choice with
trade-offs) doesn't belong in this frozen spec — record it as a `proposed` entry in
`docs/decisions.md` and link it here.}
