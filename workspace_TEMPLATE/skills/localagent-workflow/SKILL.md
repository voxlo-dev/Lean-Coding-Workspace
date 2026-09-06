---
name: localagent-workflow
description: "Use for a full feature build that must stay robust on a weak/local model: a sequential, context-frugal pipeline (plan gate → per-unit TDD loop → e2e/docs) where every step gets a tiny single-purpose context. Tests and code are written by separate agents behind a visibility wall so TDD is forced. Runner-neutral — drivable by any agent harness or an external local-model runner."
---

# Localagent Workflow

Sequential, context-frugal multi-agent build for a **weak (~30B) local model**. You are the
**orchestrator**: pure control flow — plan, decompose, delegate, update `STATE.md`, enforce gates.
Never write specs, stubs, tests or code yourself; catch yourself doing content work → stop and
dispatch the agent. **A failure you could fix in one line is still not yours** — a red test, a broken
e2e flow, a typo in a stub: name it in a brief and send it back to the half that owns the file, or the
strongest context in the run is doing unwalled, untracked work. `STATE.md`, not your context window, is your working memory.

**The contract is code, not prose.** `localagent-spec-architect` writes each unit's surface as **stub
files in the repo's normal tree** — every exposed symbol at its real path with its full signature, a
body that only raises "not implemented" — and typechecks them before returning. Both halves compile
against the same declarations, so a signature cannot be read two ways.

**TDD is forced by construction:** `localagent-test-author` and `localagent-implementer` are separate
agents behind a **visibility wall**, deriving independently from that stub plus `spec.md`. The wall
is about **source, not evidence**: the implementer runs the tests and works from what they print, so
it closes its own fix loop; what it may never do is read the test text and shape code to fit it.

Nothing assumes a harness or a project layout. The agent prompts are plain Markdown — one job each,
declared inputs only, one artifact, one status line back — and their frontmatter carries both harness
dialects at once, so an external local-model runner can drive them straight from this file instead.

## Setup — register the agents

The seven `localagent-*` prompts beside this skill are **agent definitions, not documentation** — a
harness must be able to *run* one, or there is nothing to dispatch and the model does every step
itself in one context, the failure this workflow exists to prevent. Copy them **flat** into its agent
directory (`{home}/agents/`, or project-level `{project-agent-dir}/agents/`): both Claude Code and
OpenCode scan recursively, but OpenCode folds a subfolder into the agent's ID while Claude Code keys
off `name:`, so a nested copy answers to a different name in each. Source folders are a repo
convenience; the filename must be unique. Nothing else may live there — a stray file is scanned as an
agent, which is why `templates/` stays with this skill and the orchestrator passes its paths in the
brief.

**Check the target first**, and check by dispatching, not by looking: no agent directory in `{home}`
means it registers none, and a directory is not proof the harness reaches what is in it. Some want
another format — Codex takes TOML with the prompt under `developer_instructions`, which
`workspace-install` converts — and there the conversion is worth one live dispatch before trusting
it. Cannot dispatch at all → use `dynamic-workflow` rather than letting one context do every step.

`localagent-orchestrator` is the **primary** agent: run the workflow as that session through the
selected harness. The six workers are `mode: all` — dispatchable as subagents *and* addressable by
name from outside, which is what lets one run on a model this harness cannot reach. A worker demoted
to `mode: subagent` is silently replaced by the default agent on an external dispatch, wall and all.

## Dispatch

Every agent runs in a **fresh, isolated context** — no conversation, no earlier step, not even where
the repo is. You **call it by name**; never open its definition file, its prompt is not yours to read.
(An external runner instead sends that file as the system prompt and the brief as the turn.)

**This harness rarely sells the model this workflow is for, so read `~/.agents/DISPATCH-GUIDE.md`
before the first dispatch** — it holds the launch command, which model each role gets, and the rules
for sending work out of process. A dispatched agent runs by the same name and returns the same
status line, so everything below is unchanged by it: the brief, the sequencing, the gate. No guide,
or no role for a step → that step runs on this harness, and the wall is prompt-only.

The brief is four things: the **absolute working directory** · the **standing constraints** that bear
on this step — what the user's prompt and the project's rules impose (conventions, language, hard
limits), since no agent can see either · the **task**, one or two lines · the **paths** to its declared
inputs. Pass paths, never inline artifact content, and never test files to `localagent-implementer`
until the wall drops.

- **One agent at a time, sequential** — a local model serves one inference at a time; keep that shape
  everywhere so a run behaves the same in every runner.
- **A general-purpose agent is no substitute.** A dispatch that will not start — not registered, call
  rejected, tool error — is `BLOCKED`. Doing the step yourself, or handing it to an unrestricted
  agent, is the one failure that voids the whole run: every guarantee rests on who wrote what.
- **One restriction is enforced; everything else is prompt.** The wall — a `read`/`glob`/`grep` deny
  on the test globs in the implementer's definition, widened to the project's naming — is the only
  permission any agent carries, because a peek at the test source is invisible afterwards. **Running**
  the tests is never denied: it is what lets the implementer fix itself. Tighten nothing else — a
  small model holds a prompt well but loses the thread the moment a tool call is refused, so a scope
  narrow enough to trip it costs more than it protects.
- **No frontmatter names a model or a tool list.** Both harnesses define those keys with different
  types, so either one makes the file invalid somewhere — which is why the model comes from the
  role table instead. The prompts are written for a ~30B local model; don't loosen them for a
  stronger one.

## Artifacts

```
<repo>/localagent/
├── PLAN.md          ← planning, from templates/PLAN.md
├── STATE.md         ← the ledger, from templates/STATE.md
├── E2E.md           ← e2e report
└── units/U<N>/      ← spec.md
```

The stubs, the tests and the production code go into the repo's normal trees; docs are updated in
place. Everything under `localagent/` records one run: committed with it, never edited afterwards,
not living documentation.

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
before the first unit; it installs exactly the approved stack, sets up the e2e harness with its one
empty driver script, and returns the test command, the e2e command and that script's path — record all
three in `STATE.md`, later agents get them from you. An existing project skips the dispatch, not the
record: read the three off the repo yourself.

## Phase 2 — Build loop

Seed `STATE.md` from the approved unit list, then loop:

1. **Pick** the next actionable unit — dependencies all `done`, status ≠ `done` — and read its next
   sub-step off the ladder.
2. **Dispatch:**

| Sub-step | Agent | Input | Output |
| --- | --- | --- | --- |
| pending → specced | `localagent-spec-architect` | the unit's PLAN entry + prior units' STATE interface lines + the paths of `templates/unit-stub.md` and `templates/unit-spec.md` | stub files (typechecking) + `units/U<N>/spec.md` |
| specced → tests-red | `localagent-test-author` | `spec.md` + the stub paths | test files, confirmed failing |
| tests-red → done | `localagent-implementer` | `spec.md` + the stub paths + the test command **(never the test files)** | filled-in code, whole suite green |

3. **Check, then update.** On the implementer's `DONE`, run the test command yourself and `git diff`
   the stub and test files — the gate is yours, not the agent's claim. Green and unmodified → `done`:
   append the unit's interface line, reset `Attempts`. Otherwise rework per the table below. Re-read
   `STATE.md`, continue.
4. All units `done` → finalize.

### Who fixes what

The implementer fixes its own code inside its own turn — a red test is not a round trip. What reaches
you is only what it decided is *not* its to fix, and **every rework brief you forward is written in
stub and acceptance-criterion terms** — never in the other side's source. That is what keeps the wall
standing through rework.

| Implementer verdict | Owner gets | Attempt |
| --- | --- | --- |
| `ESCALATE test-mismatch` — a test contradicts the stub or the spec | **spec-architect**: what the failure demands vs the declaration it contradicts. It judges — repairs the stub/spec, or returns `ESCALATE unfounded`, and then the test-author gets the fix instead | counts |
| `ESCALATE contract` — the stub or spec cannot be satisfied as written | **spec-architect**: the exact gap. It rewrites, then tests *and* code are re-derived | counts, reset on a rewrite |
| `ESCALATE toolchain` — runner or build config broken | **scaffold**: the error; not a unit failure at all | free |
| Your own test run red after a `DONE` | **implementer**: the failing behaviour and the criterion it misses | counts |
| A stub or test file was modified | revert it, re-dispatch the half that edited it with the breach named | counts |

The spec-architect owns both shared files, so **both content escalations route there** — with the stub
compiling, "test vs contract" is decidable by reading it. Earlier still, the
spec-architect may return `ESCALATE too-large`: re-cut that unit in `PLAN.md`, update `STATE.md`,
dispatch again — a planning correction, cheaper than any row above.

**Wall drop.** At `Attempts` ≥ 2 add the unit's test file paths to the implementer's brief to break
the deadlock. At ≥ 3, escalate the unit.

## Phase 3 — Finalize

1. **e2e** — dispatch `localagent-e2e` only if a surface exists: browser/UI (a `frontend`/`web`/
   `client` dir, a UI-framework manifest, served HTML) or a meaningful integration one (API,
   persistence, external service). Neither → skip, note `e2e: no surface` in STATE. Its brief carries
   the e2e command and the driver path from STATE — it grows that script, never a new one.
   **`FIXES_REQUIRED` routes like any red test:** the owning unit goes back to `tests-red` and its
   implementer is re-dispatched with the failing step in acceptance-criterion terms — the report's
   path, never its script source, so the wall holds — then e2e re-runs. Counts as an attempt; no
   owning unit, or past its budget → escalate.
2. **docs** — dispatch `localagent-docs`.
3. **memory** — persist the run's durable decisions and gotchas wherever the project keeps them, and
   prune what went stale.
4. Update the project's work tracking if it has any, then commit / PR per its version-control rules.

## Escalation

Any `ESCALATE` the table above does not route, a `BLOCKED`, an e2e `FIXES_REQUIRED` that no unit owns,
or a unit past its attempt budget: write the reason to STATE Blockers, set `Phase: blocked`, **stop the run**, and
surface the exact blocker to the user. Never route around one — a weak-model run stops early rather
than grinds.

## When NOT to use

The wall, the ledger and the tiny per-step contexts cost throughput and buy nothing where the model
can hold a whole feature at once. Reach for this only when the model is weak or context discipline is
the priority, and never for a one-line fix.
