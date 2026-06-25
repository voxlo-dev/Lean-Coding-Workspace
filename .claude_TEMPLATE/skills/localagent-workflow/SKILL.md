---
name: localagent-workflow
description: "Use for a full feature build that must stay robust on a weak/local model: a sequential, context-frugal pipeline (brainstorm → plan gate → per-unit TDD loop → e2e/docs) where every step gets a tiny single-purpose context. Minimal hybrid of dynamic- and orchestrator-workflow. Also runnable by an external local-model runner."
---

# Localagent Workflow

A sequential, context-frugal multi-agent workflow built for a **weak (~30B) local model**. You are the **orchestrator** (main thread). You hold only a thin `STATE.md` ledger and delegate every piece of content work to a single-purpose agent that gets a tiny, scoped context.

**Canonical logic lives in `PROTOCOL.md`** — read it and follow it. This file only maps that protocol onto Claude Code.

## Claude Code mapping

- **You are the orchestrator** — pure control flow. Never write specs, tests, or code yourself; dispatch the matching agent. Keep your own context near-empty: after each step, write `STATE.md` and rely on it, not on your window.
- **Each agent = a subagent** dispatched with the Agent tool. The prompt is the content of `agents/<name>.md` (read it) plus a 2–3 line brief and the path(s) to its input file(s). Pass paths, never inline artifact content.
- **Models:** you orchestrate on Opus/Fable; dispatch agents on **Sonnet** (the `docs` agent can be Haiku). The agent prompts are written to also survive a fully-local 30B run — don't loosen them.
- **Sequential only** — one agent at a time (a single local model serves one inference at a time; keep the same shape in CC for parity).

## Flow (see `PROTOCOL.md` for detail)

1. **Brainstorm** → dispatch `agents/brainstorm.md` → writes `localagent/PLAN.md` (systems, features, test strategy, unit list).
2. **Plan gate** → show the user the unit list, get explicit approval. **Stop until approved** — the only routine pause.
3. **Build loop** (per unit, dependency order, just-in-time): `spec → testspec → test-implement (red) → implement (green)`. Update `STATE.md` after every sub-step.
4. **Finalize** → `e2e` (only if a browser/integration surface exists) → `docs` → invoke **`maintain-memory`** → commit / PR.

## Escalation

Any agent returning `ESCALATE` or `BLOCKED` (or ~5 failed attempts on one step): record it in `STATE.md`, set `Phase: blocked`, **stop the run**, and surface the exact blocker to the user. Do not route around blockers autonomously.

## Files

```
localagent-workflow/
├── SKILL.md            ← this file (CC wrapper)
├── PROTOCOL.md         ← canonical orchestration logic — follow it
├── agents/             ← runner-neutral single-purpose prompts
│   ├── brainstorm.md   spec.md   testspec.md
│   ├── test-implement.md   implement.md   e2e.md   docs.md
└── templates/          ← PLAN.md · STATE.md · unit-spec.md · unit-testspec.md
```

## When NOT to use

Running on a capable cloud model with a normal feature → `dynamic-workflow`. Large parallelisable work → `orchestrator-workflow`. A one-line fix → `minimal-workflow`. This workflow trades throughput for tiny, predictable per-step context — only worth it when the model is weak or context discipline is the priority.
