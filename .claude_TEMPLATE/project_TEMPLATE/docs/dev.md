# Developer Docs — {project name}

<!-- CONTRACT (binding — sections are suggestions, this header is not):
  - PURPOSE: the engineering knowledge that code, tests, and codegraph DON'T capture —
    setup, environment, non-obvious build/debug workflows, external-dependency quirks.
    If a fact is already in the code, the tests, or (in an indexed repo) codegraph, do NOT
    copy it here — link to the source instead. Hand-copied API signatures are the classic
    drift trap.
  - BOUNDARIES: structure → `architecture.md`; product behaviour → `behaviour.md`;
    decisions + rationale → `decisions.md`; user-facing docs → `product/`; per-session
    gotchas & fragile-area warnings → project memory; bugs & tech debt → the issue tracker.
  - WHO WRITES: `maintain-docs`. Keep edits factual, no speculation.
  - SPLIT at ~300–500 lines → one file per topic under `dev/`, keep this as the index.
-->

## Setup & environment

{what a fresh contributor needs that the README doesn't cover — required tooling versions,
env vars / secrets, local services, first-run gotchas.}

## Build / debug workflows

{non-obvious workflows — how to run a subset, attach a debugger, regenerate something,
common failure modes and their fix.}

## External dependencies

{third-party services / libraries with quirks worth knowing — auth, rate limits, version
pins that matter, sharp edges. Skip the ordinary ones.}

## API reference

{Do not hand-maintain signatures. In an indexed repo, codegraph is the live reference; else
link to generated docs (typedoc / rustdoc / …). Note here only what generation can't express
— usage contracts, invariants a caller must uphold.}

<!-- Bugs, limitations, and tech debt are NOT tracked here — they belong in the issue tracker
     (public, actionable) or project memory (fragile-area warnings for future sessions).
     Add a one-line pointer to the tracker if the project has one. -->
