# Architecture — {project name}

<!-- CONTRACT (binding — the STATUS mechanic is protocol between skills, keep it exact; the
     section layout is only a suggestion):
  - PURPOSE: the planned/implemented software *structure* — where things live and how the
    pieces connect. Optimised as agent context: "where does X live", boundaries, entry points.
  - WHO WRITES: seeded as `draft` by `project-initialiser`; `maintain-docs` fills subsystems
    from real code as they're built.
  - BOUNDARIES — keep these OUT: product behaviour/rules → `behaviour.md`; the *why* of a
    choice → `decisions.md`; api signatures, setup, gotchas → `dev.md`. This doc answers
    only *structure*.
  - STATUS (exact): doc top = `draft → partial → current`; each subsystem = `planned → implemented`.
    Never mark `implemented` from a plan alone — only from shipped code.
  - STRUCTURE: scale to the project. Sections below are suggestions; drop what you don't use,
    never leave them empty. Diagrams are OPTIONAL — in an indexed repo codegraph is the live
    structure; keep any diagram here only if it earns its upkeep.
  - SPLIT at ~300–500 lines → one file per subsystem under `architecture/`, keep this as the index.
-->

**Status:** {draft | partial | current}

## Overview / System context

{What the system is and who/what it talks to (users, external services, data stores). One
short paragraph; a context sketch only if it adds something words don't.}

## Module map

{The agent's fastest orientation — where things live. One row per top-level module/package.}

| Module / path | Responsibility | Entry point |
| --- | --- | --- |
| {path} | {the one job it owns} | {main file / public symbol} |

## Subsystems

{Repeat per subsystem worth calling out above the module map's granularity. Drop if the map suffices.}

### {Subsystem name}

- **Status:** {planned | implemented}
- **Purpose:** {the one job it owns}
- **Interface:** {how it's used — entry points, public API, events}
- **Depends on:** {other subsystems / external services}
- **Notes:** {internals worth knowing, constraints}

## Structural data flow

{How the *components* wire together for the main path — who calls whom, where state is
stored. Component-level, not user-scenario-level (scenarios live in `behaviour.md`). A step
list or sequence sketch.}

## Boundaries & cross-cutting

{Where the structural seams and shared machinery live: module boundaries, and where auth /
logging / error handling / config are wired in the code (which module owns each). The
*decision* behind any of these belongs in `decisions.md`, not here — name the location, not
the rationale.}
