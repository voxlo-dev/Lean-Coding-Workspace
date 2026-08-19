# Decisions — {project name}

<!-- CONTRACT (binding):
  - The flat, append-only INDEX of every settled decision, newest on top. One line each, so
    "what is already settled here?" stays a single cheap read. Agents read it to not
    re-litigate; for the reasoning they follow the link.
  - Index only — never copy content here. Forces, options and rationale live in the sprint
    that settled it: `artefacts/{sprint}/sprint-decisions.md`.
  - NNNN is the decision's own number, next free one at append time. It is what
    `superseded by` points at, and it works for decisions that never had a ticket.
  - Settled only. An open question is *work*: a `decision` ticket on a board, not a line here.
  - Append-only — never rewrite or delete a line. To overturn, append the new decision and
    mark the old one `superseded by NNNN`.
  - Writers: `open-sprint` (what the planning settled) · `maintain-docs` (settled inside a
    run) · `close-sprint` (any Done `decision` ticket not yet recorded). Each writes the
    `sprint-decisions.md` section and this line in the same move.
  - SPLIT when long → one file per year/area under `decisions/`, keep this as the index.
-->

- **NNNN** {decision title} — {one line: what was decided} · `accepted` · [{sprint-slug}](../artefacts/{sprint-slug}/sprint-decisions.md)
- **NNNN** {decision title} — {one line} · `superseded by NNNN` · [{sprint-slug}](../artefacts/{sprint-slug}/sprint-decisions.md)
