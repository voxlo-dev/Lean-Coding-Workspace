---
name: localagent-workflow
description: "Use for a full feature build that must stay robust on a weak/local model: a sequential, context-frugal pipeline (plan gate → per-unit TDD loop → e2e/docs) where every step gets a tiny single-purpose context. Tests and code are written by separate agents behind a visibility wall so TDD is forced. Runner-neutral — drivable by any agent harness or an external local-model runner."
---

# Localagent Workflow

Sequential, context-frugal multi-agent build for a **weak (~30B) local model**. You are the
**orchestrator**: pure control flow — plan, decompose, delegate, update `STATE.md`, enforce gates.
Never write specs, contracts, tests or code yourself; catch yourself doing content work → stop and
dispatch the agent. Your context stays near-empty — `STATE.md` is your working memory, not your window.

**TDD is forced by construction:** `localagent-test-author` and `localagent-implementer` are separate
agents behind a **visibility wall** — the implementer never sees the test code. Both derive
independently from a shared `contract.md` (interfaces) + `spec.md` (behaviour), and
`localagent-verifier` runs the tests against the code, so code that passes satisfies the contract,
not the test text.

No harness or project layout is assumed: the agent prompts are plain Markdown whose frontmatter
carries both harness dialects at once — one job each, declared inputs only, one artifact, one status
line back — so an external local-model runner can drive them straight from this file instead.

## Dispatch

The seven `localagent-*` prompts that ship beside this skill are **agent definitions**, not
documentation: each carries frontmatter for both harness families, so a harness can be told to *run*
one. They must sit **flat** in its agent directory — `~/.claude/agents/` or
`~/.config/opencode/agents/`, project-level `.claude/agents/` or `.opencode/agents/` — before a run.
Both harnesses scan that directory recursively, but OpenCode folds a subfolder into the agent's id
while Claude Code keys off `name:`, so a nested copy answers to a different name in each. And
unregistered there is nothing to dispatch at all: the model then does every step itself in one
context, the failure this workflow exists to prevent.

Nothing else may live in that directory — a stray file is scanned as an agent. This skill's
`templates/` therefore stay with the skill, and the orchestrator passes their paths in the brief.

`localagent-orchestrator` is the **primary** agent: run the whole workflow *as* that session
(`claude --agent localagent-orchestrator`, or select it in the harness). The other six are subagents.

Every agent runs in a **fresh, isolated context**; nothing carries between steps but the files. In a
harness that has them registered you **call the agent by its name** and hand it a 2–3 line brief plus
the path(s) to its declared inputs — never open its definition file, its prompt is not yours to read.
An external runner instead sends the definition file as the system prompt and the brief as the turn.
Either way: **pass paths, never inline artifact content**, and never pass test files to
`localagent-implementer` until the wall drops.

- **One agent at a time, sequential** — a local model serves one inference at a time; keep that shape
  in every runner so a run behaves the same everywhere.
- **Agents are called by name, and a general-purpose agent is no substitute.** A dispatch that will
  not start — agent not registered, call rejected, tool error — is `BLOCKED`. Report it and stop;
  doing the step yourself, or handing it to an unrestricted agent, is the one failure that voids the
  whole run: every guarantee here rests on who wrote what.
- **One restriction is enforced; everything else is prompt.** The wall — a `read`/`glob`/`grep` deny
  on the test globs in the implementer's definition — is the only permission any agent carries, and
  it earns that because a peek at the tests is invisible afterwards and silently voids the TDD
  guarantee. Widen those globs to match the project's naming. Every other rule here is prose the
  agents keep: a small model holds a prompt well but loses the thread the moment a tool call is
  refused, so a scope tight enough to trip it costs more than it protects. Tighten only where a
  breach would be undetectable.
- **No agent frontmatter names a model or a tool list.** Both harnesses define those keys with
  different types, so either one makes the file invalid somewhere; the harness picks the model, and
  tool scope is expressed in the keys only one of them reads. The prompts are written for a ~30B
  local model; don't loosen them for a stronger one.

## Artifacts

```
<repo>/localagent/
├── PLAN.md          ← planning, from templates/PLAN.md
├── STATE.md         ← the ledger, from templates/STATE.md
├── E2E.md           ← e2e report
└── units/U<N>/      ← spec.md (behaviour) + contract.md (interfaces), by spec-architect
```

Tests and production code go into the repo's normal trees, docs are updated in place. Everything
under `localagent/` is a record of one run: committed with it, never edited afterwards — it is not
living documentation.

**Write `STATE.md` after every step and re-read it at the start of the next round** — a context reset
must be survivable from it alone. Shape, status ladder and `Attempts` semantics: `templates/STATE.md`.

## Phase 1 — Plan, then the gate

Produce `localagent/PLAN.md` from `templates/PLAN.md`: target/systems, features, test strategy, and a
unit list with dependencies. Keep units small — each bounds every later agent's context. Interactive
by default: plan *with* the user in 2–3 tight rounds (goal, must-haves vs nice-to-haves, constraints,
what "done" looks like, risky areas), grounded in codegraph if indexed, else a brief scoped look.
Headless: derive PLAN.md from the task brief.

**Plan gate — the only routine pause.** Show the unit list, get explicit approval, **stop until
approved**; silence is not approval, requested changes → revise and re-show. After it the run is
autonomous. (Headless: pause if a human is reachable, else record auto-approval in STATE.)

## Phase 2 — Build loop

Seed `STATE.md` from the approved unit list, then loop:

1. **Pick** the next actionable unit — dependencies all `done`, status ≠ `done` — and read its next
   sub-step off the ladder.
2. **Dispatch:**

| Sub-step | Agent | Input | Output |
| --- | --- | --- | --- |
| pending → specced | `localagent-spec-architect` | the unit's PLAN entry + prior units' STATE interface lines + the unit templates' paths | `units/U<N>/spec.md` + `contract.md` |
| specced → tests-red | `localagent-test-author` | `spec.md` + `contract.md` | test files, confirmed failing |
| tests-red → impl | `localagent-implementer` | `spec.md` + `contract.md` **(never the tests)** | production code |
| impl → verified | `localagent-verifier` | the unit's tests + implicated src + prior `done` units' test paths (regression set) | verdict + behaviour-level failure report |

3. **Update** `STATE.md` from the agent's status line (`DONE` · `RED` · `ESCALATE` · `BLOCKED`):
   advance the unit, or rework. On `done`, append its interface line and reset `Attempts`. Re-read,
   continue.
4. All units `done` → finalize.

**Rework on `RED`** (red-cycle `k` = the unit's new `Attempts`): re-dispatch the implementer with the
verifier's **behaviour-level** report (expected vs actual + the contract point / acceptance criterion
that failed), never the test source. At `k ≥ 3` the **wall drops** — add the unit's test file paths to
break the deadlock. At `k ≥ 5`, escalate.

**Verifier safety valve:** a verifier judging the *test* wrong (it contradicts `contract.md`) returns
`ESCALATE test-mismatch` — re-dispatch `localagent-test-author`, or `localagent-spec-architect` if the
contract itself is wrong, not the implementer, so a bad test costs no attempt.

## Phase 3 — Finalize

1. **e2e** — dispatch `localagent-e2e` only if a surface exists: browser/UI (a `frontend`/`web`/
   `client` dir, a UI-framework manifest, served HTML) or a meaningful integration one (API,
   persistence, external service). Neither → skip, note `e2e: no surface` in STATE.
2. **docs** — dispatch `localagent-docs`.
3. **memory** — persist the run's durable decisions and gotchas wherever the project keeps them, and
   prune what went stale.
4. Update the project's work tracking if it has any, then commit / PR per its version-control rules.

## Escalation

An `ESCALATE`, a `BLOCKED`, `FIXES_REQUIRED` from e2e, or ~5 failed attempts on one unit: write the
reason to STATE Blockers, set `Phase: blocked`, **stop the run**, and surface the exact blocker to the
user. Never route around a blocker autonomously — a weak-model run stops early rather than grinds.

## Codegraph

Indexed repo (`.codegraph/` exists) → `localagent-spec-architect` and `localagent-implementer` may use
`codegraph explore "<topic>"` (CLI, so it works in any runner) instead of broad file reads. Do not
index the repo yourself.

## When NOT to use

The wall, the ledger and the tiny per-step contexts cost throughput and buy nothing where the model
can hold a whole feature at once — reach for this only when the model is weak or context discipline
is the priority, and never for a one-line fix.
