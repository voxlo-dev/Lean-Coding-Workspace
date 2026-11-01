# Handoff — package {n}: {name}

Template: `~/.agents/skills/dynamic-workflow/templates/handoff.md` — filled per `dynamic-workflow`'s
loop for a `package-worker`, whose definition carries the standing rules and the return.

- **Spec:** `artefacts/{sprint}/spec_{feature}.md` → **package {n}** — read **Strategy**,
  **Components** and your package entry, whose acceptance criteria you are judged against; the rest
  only where those point. Your anchor if you compact.
- **From earlier reports:** {what this package depends on that the spec doesn't say — a moved
  signature, a deviation, a helper to reuse — else none}
- **Failing tests to turn green:** {light-tdd only — your slice of package 1's red list; else drop}
- **Narrowed:** {re-dispatch only — the narrowed brief and the obstacle from the last report; else drop}
- **Report to:** {out of process only — `report.md` beside this brief; else drop}
