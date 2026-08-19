# Behaviour — {project name}

<!-- CONTRACT (binding — the section layout below is only a suggestion, this header is not):
  - PURPOSE: single source of truth for how the *shipped* product behaves — product
    semantics as a rulebook. Agents and developers read this to know the current truth.
  - WHO WRITES: `maintain-docs`, the behaviour *delta* at each feature's docs step. A spec is
    a throwaway describing that delta; THIS doc carries the state.
  - READ THIS FIRST: `spec-design` grounds new work here, so the current truth stays readable
    in one place instead of reassembled from stacked spec addenda.
  - WHAT GOES IN: declarative rules, invariants, per-screen interaction contracts — present
    tense, current truth.
  - WHAT STAYS OUT: prose narrative, history/changelog, rationale (→ `decisions.md`),
    structure/components (→ `architecture.md`), implementation detail.
  - STRUCTURE: organise by whatever fits the product — screen, feature area, rule domain. The
    blocks below are FORM PATTERNS: replace them with what fits, delete the rest.
  - SPLIT at ~300–500 lines → one file per area under `behaviour/`, this stays the index.
-->

## {Rule domain — e.g. Scheduling / Snooze / Streaks}

{Declarative rules that govern this area — one rule per line, unambiguous. Example form:}

- {Invariant: there is exactly one alarm source.}
- {When {condition} → {defined behaviour}.}
- {Edge: {missed deadline} → {defined behaviour}.}

## {Screen / surface — e.g. Home screen}

**Interaction contract:**

- {element / gesture} → {what happens}
- {state} → shows {what}
- **Invariants:** {what must always hold on this surface}
