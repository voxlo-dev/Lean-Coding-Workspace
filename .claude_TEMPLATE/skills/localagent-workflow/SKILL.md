---
name: localagent-workflow
description: "Use for a full feature build that must stay robust on a weak/local model: a sequential, context-frugal pipeline (plan gate → per-unit TDD loop → e2e/docs) where every step gets a tiny single-purpose context. Tests and code are written by separate agents behind a visibility wall so TDD is forced. Runner-neutral — drivable by any agent harness or an external local-model runner."
---

# Localagent Workflow

Sequential, context-frugal multi-agent build for a **weak (~30B) local model**. You are the
**orchestrator**: pure control flow — plan, decompose, delegate, update `STATE.md`, enforce gates.
Never write specs, contracts, tests or code yourself; catch yourself doing content work → stop and
dispatch the agent. `STATE.md`, not your context window, is your working memory.

**TDD is forced by construction:** `localagent-test-author` and `localagent-implementer` are separate
agents behind a **visibility wall** — neither ever sees the other's files. Both derive independently
from a shared `contract.md` (interfaces) + `spec.md` (behaviour), and `localagent-verifier` runs the
tests against the code, so code that passes satisfies the contract, not the test text.

Nothing assumes a harness or a project layout. The agent prompts are plain Markdown — one job each,
declared inputs only, one artifact, one status line back — and their frontmatter carries both harness
dialects at once, so an external local-model runner can drive them straight from this file instead.

## Setup — register the agents

The eight `localagent-*` prompts beside this skill are **agent definitions, not documentation** — a
harness must be able to *run* one, or there is nothing to dispatch and the model does every step
itself in one context, the failure this workflow exists to prevent. Copy them **flat** into its agent
directory (`~/.claude/agents/`, `~/.config/opencode/agents/`, or project-level `.claude/agents/` ·
`.opencode/agents/`): both scan recursively, but OpenCode folds a subfolder into the agent's id while
Claude Code keys off `name:`, so a nested copy answers to a different name in each. Nothing else may
live there — a stray file is scanned as an agent, which is why `templates/` stays with this skill and
the orchestrator passes their paths in the brief.

`localagent-orchestrator` is the **primary** agent: run the workflow *as* that session
(`claude --agent localagent-orchestrator`, or select it in the harness); the other seven are subagents.

## Dispatch

Every agent runs in a **fresh, isolated context**; nothing carries between steps but the files. You
**call it by name** and hand it a 2–3 line brief plus the path(s) to its declared inputs — never open
its definition file, its prompt is not yours to read. (An external runner instead sends that file as
the system prompt and the brief as the turn.) **Pass paths, never inline artifact content**, and never
pass test files to `localagent-implementer` until the wall drops.

- **One agent at a time, sequential** — a local model serves one inference at a time; keep that shape
  everywhere so a run behaves the same in every runner.
- **A general-purpose agent is no substitute.** A dispatch that will not start — not registered, call
  rejected, tool error — is `BLOCKED`. Doing the step yourself, or handing it to an unrestricted
  agent, is the one failure that voids the whole run: every guarantee rests on who wrote what.
- **One restriction is enforced; everything else is prompt.** The wall — a `read`/`glob`/`grep` deny
  on the test globs in the implementer's definition, widened to the project's naming — is the only
  permission any agent carries, because a peek at the tests is invisible afterwards. Tighten nothing
  else: a small model holds a prompt well but loses the thread the moment a tool call is refused, so
  a scope narrow enough to trip it costs more than it protects.
- **No frontmatter names a model or a tool list.** Both harnesses define those keys with different
  types, so either one makes the file invalid somewhere. The prompts are written for a ~30B local
  model; don't loosen them for a stronger one.

## Artifacts

```
<repo>/localagent/
├── PLAN.md          ← planning, from templates/PLAN.md
├── STATE.md         ← the ledger, from templates/STATE.md
├── E2E.md           ← e2e report
└── units/U<N>/      ← spec.md + contract.md, and the failure reports of its rework
```

Tests and production code go into the repo's normal trees, docs are updated in place. Everything under
`localagent/` records one run: committed with it, never edited afterwards, not living documentation.

**Write `STATE.md` after every step and re-read it at the start of the next round** — a context reset
must be survivable from it alone. Shape, status ladder and `Attempts`: `templates/STATE.md`.

## Phase 1 — Plan, then the gate

Produce `localagent/PLAN.md` from `templates/PLAN.md`, whose guidance on the stack and on unit size is
binding. Both are settled here and nowhere else: the **stack** — no agent later may decide it, and one
forced to will decide it badly and alone — and a **unit list** kept small *and few*, since every seam
between two units is a place the blind halves can disagree.

Interactive by default: plan *with* the user in 2–3 tight rounds — goal, must-haves vs nice-to-haves,
constraints, what "done" looks like, risky areas — grounded in `codegraph explore` if the repo is
indexed (never index it yourself), else a brief scoped look. Headless: derive PLAN.md from the brief.

**Plan gate — the only routine pause.** Show the unit list and the stack, get explicit approval,
**stop until approved**; silence is not approval, requested changes → revise and re-show. After it the
run is autonomous. (Headless: pause if a human is reachable, else record auto-approval in STATE.)

**Scaffold, once.** Nothing runnable yet — no manifest, no test runner → dispatch `localagent-scaffold`
before the first unit; it installs exactly the approved stack and returns the test command every later
agent needs. An existing project skips this.

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
| impl → verified | `localagent-verifier` | the unit's tests + implicated src + prior `done` units' test paths (regression set) | verdict + failure report |

3. **Update** `STATE.md` from the agent's status line: advance the unit, or rework per the table
   below. On `done`, append its interface line and reset `Attempts`. Re-read, continue.
4. All units `done` → finalize.

### Who fixes what

Every failure has one owner, and **the report that reaches them is written in contract terms** — never
in the other side's source. That is what keeps the wall standing through rework: each half only ever
sees `contract.md` plus a statement of how its own output departs from it.

| Verifier verdict | Owner gets | Attempt |
| --- | --- | --- |
| `RED` — code misses the contract | **implementer**: expected vs actual + the contract point that failed | counts |
| `ESCALATE test-mismatch` — a test contradicts it | **test-author**: what the test does vs what the contract says | free |
| `ESCALATE contract` — the contract is wrong or ambiguous | **spec-architect**: it rewrites `contract.md`, then tests *and* code are re-derived | free, reset `Attempts` |
| `ESCALATE toolchain` — runner or build config broken | **scaffold**: the error; not a unit failure at all | free |

A conflict that cannot be stated in contract terms is the contract's fault, not the test's — route it
to the spec-architect rather than letting either half "just check" the other's files. Earlier still,
the spec-architect may return `ESCALATE too-large`: re-cut that unit in `PLAN.md`, update `STATE.md`,
dispatch again — a planning correction, cheaper than any row above.

**Wall drop.** On the `RED` path only, at `k ≥ 3` add the unit's test file paths to the implementer's
brief to break the deadlock. At `k ≥ 5`, escalate the unit.

## Phase 3 — Finalize

1. **e2e** — dispatch `localagent-e2e` only if a surface exists: browser/UI (a `frontend`/`web`/
   `client` dir, a UI-framework manifest, served HTML) or a meaningful integration one (API,
   persistence, external service). Neither → skip, note `e2e: no surface` in STATE.
2. **docs** — dispatch `localagent-docs`.
3. **memory** — persist the run's durable decisions and gotchas wherever the project keeps them, and
   prune what went stale.
4. Update the project's work tracking if it has any, then commit / PR per its version-control rules.

## Escalation

Any `ESCALATE` the table above does not route, a `BLOCKED`, `FIXES_REQUIRED` from e2e, or ~5 failed
attempts on one unit: write the reason to STATE Blockers, set `Phase: blocked`, **stop the run**, and
surface the exact blocker to the user. Never route around one — a weak-model run stops early rather
than grinds.

## When NOT to use

The wall, the ledger and the tiny per-step contexts cost throughput and buy nothing where the model
can hold a whole feature at once. Reach for this only when the model is weak or context discipline is
the priority, and never for a one-line fix.
