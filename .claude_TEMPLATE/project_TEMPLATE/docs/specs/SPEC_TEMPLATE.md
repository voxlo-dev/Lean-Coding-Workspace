# Spec — {feature name}

Date: {YYYY-MM-DD} · Status: {draft | approved | done}

Feature spec for the spec-workflow. Comes out of brainstorming; drives the
phase packages — tests → implement → [e2e] → docs, committing after each.

## Goal / problem

{what we're solving and why; the user value}

## Scope

- **In:** {what this feature includes}
- **Out:** {explicitly not included}

## Approach

{the chosen approach in a few sentences; link to architecture if relevant}

## Components

{the units touched/added — purpose, interface, dependencies}

## Data flow

{how data moves for the main scenario; sequence or steps}

## Work packages

Fixed phases, run in order, commit after each. Size only the implement packages.

- **Test:** all tests for this spec (unit + e2e) — {key flows to cover; existing tests to merge with}
- **Implement:** {one or more; what each builds} · files: {paths to read/touch}
- **e2e** (only if UI): run the e2e tests against the built UI
- **Docs:** {which docs need updating}

## Acceptance criteria

- [ ] {observable, testable outcome}
- [ ] {…}

## Test strategy

{unit/integration + e2e tests that prove the criteria}

## Open questions

{unresolved points; remove when none}
