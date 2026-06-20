# Architecture — {project name}

One coherent document: from the big picture down to individual subsystems.
Scale it to the project — a small tool needs only Overview + a few subsystems;
a large system fills every section. Maintained by `maintain-docs`.

## Overview / System context

{What the system is, who/what it talks to (users, external services, data stores).}

```mermaid
{context diagram or ASCII sketch — system as a box, its neighbours around it}
```

## High-level architecture

{The main building blocks and how they fit together. One diagram, one or two paragraphs.}

```mermaid
{high-level diagram — major components and their relationships}
```

## Subsystems

{Repeat this block per subsystem. Drop the ones you don't need.}

### {Subsystem name}

- **Purpose:** {what it does, the one job it owns}
- **Interface:** {how it's used — entry points, public API, events}
- **Depends on:** {other subsystems / external services}
- **Notes:** {internals worth knowing, constraints}

## Data flow

{How data moves through the system for the key scenario(s). Sequence or step list.}

## Key decisions

{ADR-lite: the choices that shaped this architecture and why. Newest first.}

- **{decision}** — {context → choice → trade-off}

## Cross-cutting concerns

{Auth, logging, error handling, config, performance, security — whatever applies.}
