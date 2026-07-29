# Decisions — {project name}

<!-- CONTRACT (binding):
  - PURPOSE: the append-only INDEX of settled decisions. One line each, newest on top.
    Agents read it to see what is settled and NOT re-litigate it; when they need the
    reasoning, they follow the link.
  - INDEX ONLY — never copy content here. Context, options and rationale live in the
    decision's ticket (`tickets/T-NNN-*.md` → Outcome). This file carries a short
    description and the link, nothing more.
  - SETTLED ONLY. An open question is *work*, not a record: it lives as a `decision` ticket
    on the board and enters here once settled. Never seed `proposed` entries.
  - APPEND-ONLY: never rewrite or delete a line. To change a decision, add the new one and
    mark the old `superseded by NNNN`.
  - WHO WRITES: `close-sprint` (distilling Done decision tickets) and `maintain-docs`
    (a decision settled inside a run) and `open-sprint` (what the planning settled).
  - SPLIT when long → one file per year/area under `decisions/`, keep this as the index.
-->

- **NNNN** {decision title} — {one line: what was decided} · `accepted` {YYYY-MM-DD} · [`T-NNN`](../tickets/T-NNN-{slug}.md)
- **NNNN** {decision title} — {one line} · `superseded by NNNN` {YYYY-MM-DD} · [`T-NNN`](../tickets/T-NNN-{slug}.md)

<!-- A decision settled without a ticket (inside a plan or a run) links its sprint file or
     spec instead — but it still gets only one line here. -->
