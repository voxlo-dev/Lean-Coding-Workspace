# Behaviour — {project name}

<!-- CONTRACT (binding — the section layout below is only a suggestion, this header is not.
     Delete this comment once the doc holds real rules; template:
     `~/.agents/project_TEMPLATE/docs/behaviour.md`):
  - PURPOSE: single source of truth for how the *shipped* product behaves — product
    semantics as a rulebook, and the overview of everything the product does.
  - WHO WRITES: `maintain-docs` in sprint-close mode, once per sprint, folding in the deltas
    the sprint's `spec_*` files recorded. A spec is a throwaway describing one delta; THIS doc
    carries the state.
  - WHO READS: `plan`, whole — it cuts each sprint's **Behaviour context** from here.
    `spec-design` works from that slice, reading a section here only when the slice falls
    short, never wholesale.
  - WHAT GOES IN: declarative rules, invariants, per-screen interaction contracts — present
    tense, current truth. Obsolete rules get deleted, never annotated.
  - WHAT STAYS OUT: prose narrative, history/changelog, rationale (→ `decisions.md`),
    structure/components (→ `architecture.md`), implementation detail, links into
    `artefacts/`.
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
