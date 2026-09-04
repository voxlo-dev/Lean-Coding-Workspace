# Developer Docs — {project name}

<!-- CONTRACT (binding — sections are suggestions, this header is not. Delete this comment
     once the doc holds real content; template: `~/.agents/project_TEMPLATE/docs/dev.md`):
  - PURPOSE: the engineering knowledge that code, tests and codegraph DON'T capture — setup,
    environment, non-obvious build/debug workflows, external-dependency quirks. Whatever the
    code, the tests or (in an indexed repo) codegraph already state gets a link instead of a
    copy; hand-copied API signatures are the classic drift trap.
  - SYSTEM-INDEPENDENT ONLY: everything here holds on any contributor's machine. Absolute
    paths, local installations, personal tool setup and machine-only quirks go to project
    memory — one home per fact.
  - BOUNDARIES: structure → `architecture.md`; product behaviour → `behaviour.md`;
    decisions + rationale → `decisions.md`; user-facing docs → `product/`; per-session
    gotchas & fragile-area warnings → project memory; bugs & tech debt → a ticket in `backlog/`.
  - WHO WRITES: `maintain-docs`. Keep edits factual.
  - SPLIT at ~300–500 lines → one file per topic under `dev/`, this stays the index.
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

{Link the live reference — codegraph in an indexed repo, otherwise generated docs (typedoc /
rustdoc / …). Note here only what generation can't express: usage contracts, invariants a
caller must uphold.}
