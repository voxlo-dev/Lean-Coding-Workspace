# Spec — {feature name}

Date: {YYYY-MM-DD} · Status: {draft | approved | done}

Feature spec for the spec-workflow. Comes out of brainstorming; drives the
per-WP subagent loop (tests → implement → test → docs → commit).

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

Each WP is independently implementable and testable. A simple spec can be a single WP.

- **WP1 — {name}:** {what to build} · files: {paths to read/touch} · done when: {criterion}
- **WP2 — {name}:** {…}

## Acceptance criteria

- [ ] {observable, testable outcome}
- [ ] {…}

## Test plan

{unit + UI/integration tests that prove the criteria}

## Open questions

{unresolved points; remove when none}
