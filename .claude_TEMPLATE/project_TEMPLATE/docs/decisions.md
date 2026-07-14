# Decisions — {project name}

<!-- CONTRACT (binding):
  - PURPOSE: the append-only decision log (ADR-lite). One home for every architecture,
    engineering, and product decision + its rationale. Agents read it to NOT re-litigate
    settled choices.
  - APPEND-ONLY: never rewrite or delete an entry. To change a decision, add a new one and
    set the old one's Status to `superseded by NNNN`.
  - ENTRY FORMAT is fixed (below); the document has no other structure — newest on top.
  - "Open decisions" = an entry with Status `proposed`. `sprint-cycle` seeds these during
    planning; they resolve to `accepted` or `superseded`.
  - SPLIT when long → one file per decision under `decisions/NNNN-slug.md`, keep this as the index.
-->

## NNNN — {decision title}

- **Status:** {proposed | accepted | superseded by NNNN} · {YYYY-MM-DD}
- **Context:** {the forces at play — what made this a decision}
- **Decision:** {what was chosen}
- **Consequences:** {trade-off accepted, what this rules out}
