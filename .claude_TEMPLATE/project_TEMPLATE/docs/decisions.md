# Decisions — {project name}

<!-- CONTRACT (binding):
  - PURPOSE: the append-only decision log (ADR-lite). One home for every architecture,
    engineering, and product decision + its rationale. Agents read it to NOT re-litigate
    settled choices.
  - APPEND-ONLY: never rewrite or delete an entry. To change a decision, add a new one and
    set the old one's Status to `superseded by NNNN`.
  - ENTRY FORMAT is fixed (below); the document has no other structure — newest on top.
  - SETTLED ONLY. An open question is *work*, not a record: it lives as a `decision` ticket
    on the board (`backlog.md` / the sprint file) and enters here as an `accepted` entry once
    settled, referencing its ticket. Never seed `proposed` entries here.
  - SPLIT when long → one file per decision under `decisions/NNNN-slug.md`, keep this as the index.
-->

## NNNN — {decision title}

- **Status:** {accepted | superseded by NNNN} · {YYYY-MM-DD}
- **Ticket:** {`T-NNN` it came from — or `—` if it was settled inside a plan}
- **Context:** {the forces at play — what made this a decision}
- **Decision:** {what was chosen}
- **Consequences:** {trade-off accepted, what this rules out}
