---
name: localagent-workflow
description: "Use for a full feature build that must stay robust on a weak/local model: a sequential, context-frugal pipeline (plan gate → per-unit TDD loop → e2e/docs) where every step gets a tiny single-purpose context. Tests and code are written by separate agents behind a visibility wall so TDD is forced. Minimal hybrid of dynamic- and orchestrator-workflow; also runnable by an external local-model runner."
---

# Localagent Workflow

A sequential, context-frugal multi-agent workflow built for a **weak (~30B) local model**. You are the **orchestrator** (main thread). You hold only a thin `STATE.md` ledger and delegate every piece of content work to a single-purpose agent that gets a tiny, scoped context.

**Canonical logic lives in `PROTOCOL.md`** — read it and follow it. This file only maps that protocol onto Claude Code.

**TDD is forced by construction:** the `test-author` and the `implementer` are separate agents behind a **visibility wall** — the implementer never sees the tests. Both derive independently from a shared `contract.md` (interfaces) + `spec.md` (behaviour); the `verifier` runs the tests against the code.

## Claude Code mapping

- **You are the orchestrator** — pure control flow. Never write specs, contracts, tests, or code yourself; dispatch the matching agent. Keep your own context near-empty: after each step, write `STATE.md` and rely on it, not on your window.
- **Each agent = a subagent** dispatched with the Agent tool. The prompt is the content of `agents/<name>.md` (read it) plus a 2–3 line brief and the path(s) to its input file(s). Pass paths, never inline artifact content. **Never pass the test files to the `implementer`** — that breaks the wall (until it drops on rework, per PROTOCOL).
- **Models:** you orchestrate on Opus/Fable; dispatch agents on **Sonnet** (the `docs` agent can be Haiku). The agent prompts are written to also survive a fully-local 30B run — don't loosen them.
- **Sequential only** — one agent at a time (a single local model serves one inference at a time; keep the same shape in CC for parity).

> **Wall hardening (optional).** In Claude Code the visibility wall is only prompt discipline — nothing technically stops the `implementer` subagent from reading a test file. To enforce it, either (a) add a **PreToolUse hook** that denies `Read` on the test globs while the implementer runs, or (b) give the implementer a **restricted agent definition** whose tools exclude the test paths. On a real local runner the wall is enforced by simply never putting the tests in the implementer's context — the prompt-level rule already suffices there.

## Flow (see `PROTOCOL.md` for detail)

1. **Planning** → plan inline *with* the user (grounded in codegraph if indexed) → writes `localagent/PLAN.md` (systems, features, test strategy, unit list).
2. **Plan gate** → show the user the unit list, get explicit approval. **Stop until approved** — the only routine pause.
3. **Build loop** (per unit, dependency order, just-in-time): `spec-architect (spec + contract) → test-author (red) → [WALL] → implementer (green, blind) → verifier`. Update `STATE.md` after every sub-step. Rework on red: behaviour-only report; wall drops after 3 red cycles; escalate at 5.
4. **Finalize** → `e2e` (only if a browser/integration surface exists) → `docs` → invoke **`maintain-memory`** → move the ticket to **To Test** on the sprint file's board → commit / PR.

## Escalation

Any agent returning `ESCALATE` or `BLOCKED` (or ~5 failed attempts on one unit): record it in `STATE.md`, set `Phase: blocked`, **stop the run**, and surface the exact blocker to the user. Do not route around blockers autonomously.

## Files

```
localagent-workflow/
├── SKILL.md            ← this file (CC wrapper)
├── PROTOCOL.md         ← canonical orchestration logic — follow it
├── agents/             ← runner-neutral single-purpose prompts
│   ├── spec-architect.md   test-author.md   implementer.md
│   ├── verifier.md         e2e.md           docs.md
└── templates/          ← PLAN.md · STATE.md · unit-spec.md · unit-contract.md
```

## When NOT to use

Running on a capable cloud model with a normal feature → `dynamic-workflow`. Large parallelisable work → `orchestrator-workflow`. A one-line fix → `minimal-workflow`. This workflow trades throughput for tiny, predictable per-step context and enforced TDD — only worth it when the model is weak or context discipline is the priority.
