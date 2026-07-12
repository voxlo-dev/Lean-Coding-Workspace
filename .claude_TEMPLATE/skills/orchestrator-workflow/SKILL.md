---
name: orchestrator-workflow
description: "Use for larger feature work that warrants a full autonomous pipeline: pair-planning, a written spec with work packages, auto-scaled (parallel) implementation, and an E2E validation loop. Heavier than dynamic-workflow — reach for it when the work is big enough to justify the ceremony. Orchestrate on Opus/Fable, delegate to Sonnet subagents."
---

# Orchestrator Workflow

A control-flow workflow that routes a task through four phases — pair planning → spec → auto-scaled implementation → E2E validation — passing artifact paths between dedicated subagents and enforcing gates between phases. It finishes with the standard docs → memory → git/PR steps.

**You are the dispatcher.** Pure control flow: no implementation, no spec writing, no testing. Read each worker file, dispatch it as a subagent, pass paths, enforce gates.

> **Core rule:** if you catch yourself doing content work (writing code, writing the spec, evaluating tests) — stop and delegate.

**Models:** you orchestrate on Opus/Fable (or `opusplan`). Every worker subagent runs on **Sonnet**.

**When NOT to use:** a single small change → `minimal-workflow`. A normal feature with a handful of WPs → `dynamic-workflow`. This workflow only earns its overhead when the work is large, parallelisable, and benefits from a dedicated E2E gate.

## Configuration

No config file — these are the defaults. Resolve them at Bootstrap and pass the resolved values into each worker's prompt; workers never read config themselves.

| Setting | Default | Effect |
| --- | --- | --- |
| `smoke_tests` | on | Phase 4 runs one happy-path check per user story |
| `integration_tests` | on | Phase 4 tests component interactions per WP |
| `full_e2e` | on | Phase 4 writes repeatable end-to-end journeys per story |
| `fix_as_failing_test` | on | Every CRITICAL/HIGH W4 fix request ships a confirmed-red failing test for W3 to drive green |
| `parallel_threshold` | 5 | WP count at which Phase 3 switches from single-agent to parallel-dispatcher mode |
| `reindex_post_impl` | on | After implementation, refresh the codegraph index (only if the repo is indexed) — non-blocking |

**Test levels are asked, not assumed.** At Bootstrap, ask the user which of the three test levels (smoke / integration / full E2E) to run for this pass — the table values are the defaults you offer. The user can also override any other setting at invocation or pin it in the project `AGENTS.md`.

## Bootstrap

1. Consult already-loaded memory — the project `MEMORY.md` (and any imported domain/global memory) is in context. No gateway call.
2. **Ask the user which test levels to run** (smoke / integration / full E2E; defaults above). Resolve the remaining Configuration values (defaults + any user/`AGENTS.md` overrides).
3. Note whether the repo is codegraph-indexed (`.codegraph/` exists) — this decides how discovery and structural grounding run.
4. **Resolve the artifact folder** — ask the user for the current `{sprint}` (the user decides when a sprint rolls over; reuse the newest `docs/artefacts/*` folder if they don't care) and set `{feature}` as this build's slug. Every phase writes under `docs/artefacts/{sprint}/` with the filenames from Artifact Flow.

## Decision Tree

```
START: dispatcher receives task
│
├── Phase 1: Pair Planning  — run Phase1_PairPlanning.md INLINE (interactive, not a subagent)
│     ├── Discovery (inside Phase 1): is spec-{feature}.md present AND complete?
│     │     NO/incomplete → ground planning via codegraph (indexed repo) or a brief Explore subagent
│     │     YES           → load existing spec-{feature}.md + user-stories_{feature}.md as context
│     └── Gate: explicit user approval of plan_{feature}.md before Phase 2 (silence ≠ approval)
│
├── Phase 2: Spec Architect  — dispatch Worker_2_SpecArchitect.md (Sonnet)
│     Pass: plan_{feature}.md path + project key + codegraph availability + active test levels
│     Receive: spec-{feature}.md path + user-stories_{feature}.md path
│     Gate: both exist and BUILD_SPEC has ≥1 WP before Phase 3
│
├── Phase 3: Implementation (auto-scaled)  — dispatch Worker_3_Implementation.md (Sonnet)
│     Count WPs in BUILD_SPEC Section 9; compare to parallel_threshold
│     WP count <  threshold → mode = single
│     WP count >= threshold → mode = dispatcher (W3 self-routes parallel subagents per batch)
│           ⚠ dispatcher mode needs nested agents (a subagent spawning subagents). If your
│           CC version denies W3 the Agent tool, either you spawn one Sonnet subagent per WP
│           yourself (Step-3b prompt rules), or W3 falls back to single-agent (degraded).
│     Pass: BUILD_SPEC + user-stories_{feature}.md paths + project key + mode + threshold + codegraph availability
│     Receive: HANDOVER_READY + handover_{feature}.md   → optional reindex + Phase 4
│              ESCALATE_TO_W2 + reason         → re-dispatch W2 with the problem attached
│              FAILED/BLOCKED + details        → surface to user immediately
│
├── (Optional) Post-impl codegraph refresh  — if reindex_post_impl and repo is indexed
│     Run `codegraph init -i` (non-blocking) so later structural queries are fresh
│
└── Phase 4: E2E Testing  — dispatch Worker_4_E2E.md (Sonnet)
      Pass: BUILD_SPEC + user-stories_{feature}.md + handover_{feature}.md paths + project key + active test levels + fix_as_failing_test
      Receive: VALIDATION_PASS + e2e-report_{feature}.md → Finish
               FIXES_REQUIRED + fix list          → re-dispatch W3 (max 2 cycles)
               FAILED/BLOCKED + blocker            → surface to user
      W3 ↔ W4 rework: max 2 cycles, then surface to user with full status
```

**Dispatching a worker:** read the worker's `.md` file from this skill folder, then spawn a Sonnet subagent with that file's content as its prompt plus the inputs the tree lists. Workers are self-contained — pass paths, not inline artifact content.

## Worker Registry

| Phase | File | Trigger |
| --- | --- | --- |
| Phase 1: Pair Planning | `Phase1_PairPlanning.md` | Always first — run inline |
| Phase 2: Spec Architect | `Worker_2_SpecArchitect.md` | After plan_{feature}.md approved |
| Phase 3: Implementation | `Worker_3_Implementation.md` | After BUILD_SPEC + user-stories_{feature}.md exist |
| Phase 4: E2E Testing | `Worker_4_E2E.md` | After HANDOVER_READY |

Discovery, structural grounding, and the post-impl refresh use **codegraph** directly — no separate exploration worker. If the repo is not indexed, a brief `Explore` subagent stands in.

## Artifact Flow

All artifacts live under one sprint folder — `docs/artefacts/{sprint}/` — with the type as a filename prefix and the feature in the name. `{sprint}` is resolved at Bootstrap (ask the user; the user decides when a sprint rolls over); `{feature}` is this build's slug.

```
Phase 1  → docs/artefacts/{sprint}/plan_{feature}.md                (user-approved plan)
Phase 2  → docs/artefacts/{sprint}/spec-{feature}.md                (authoritative spec)
         → docs/artefacts/{sprint}/user-stories_{feature}.md        (stories + ACs)
Phase 3  → docs/artefacts/{sprint}/impl-report_{feature}_WP<N>.md
         → docs/artefacts/{sprint}/handover_{feature}.md
Phase 4  → docs/artefacts/{sprint}/e2e-report_{feature}.md
codegraph → <repo>/.codegraph/                                      (index — owned by codegraph)
```

Everything under `docs/artefacts/` is a **run record**: committed with the run and frozen afterwards — `maintain-docs` only ever touches a `spec-*` file's Status/ACs (see the `AGENTS.md` doc map). `plan_{feature}.md` here is the orchestrator's pair-plan, not to be confused with a `plan`-skill *Lastenheft* — both share the folder and the `plan_` prefix; one build keeps one.

## Dispatcher Rules

1. **No content work** — delegate everything.
2. **Phase gate order is strict** — 1 → 2 → 3 → (optional reindex) → 4 → Finish.
3. **Phase 1 gate** — explicit user approval of plan_{feature}.md before Phase 2. Silence is not approval.
4. **Artifact-path-only handover** — pass file paths verbatim; no inline content, no summaries.
5. **Escalation routing** — `ESCALATE_TO_W2` from any phase goes back to W2 with the specific reason attached.
6. **Blocked state** — `FAILED/BLOCKED`: surface the exact blocker to the user before any re-dispatch.
7. **W3 ↔ W4 loop limit** — 2 cycles maximum, then surface to user.
8. **Stuck workers** — a worker that can't make progress after ~5 distinct attempts escalates rather than grinding.
9. **codegraph is the structural lane** — in an indexed repo, discovery and grounding query codegraph directly; do not spawn Explore subagents for what the graph already knows.

## Finish

After Phase 4 returns `VALIDATION_PASS`, run the standard closing steps:

1. **Docs** — invoke `maintain-docs` (delegate to a docs subagent), then commit the doc updates.
2. **Memory** — invoke `maintain-memory` to persist decisions, rationale, and gotchas to the right scope (and prune stale entries).
3. **Git / PR** — commit the work per the workspace Version Control rules (Conventional Commits, feature branch) and open a pull request that links the spec, if the repo uses that flow.

## Final Output Conditions

Declare done when:
- `e2e-report_{feature}.md` status = `VALIDATION_PASS`
- All `impl-report_{feature}_WP<N>.md` files document completed work
- No open CRITICAL or HIGH issues
- Docs, memory, and git/PR steps complete
- Brief summary delivered to the user (what changed, files touched, open questions)

## Checklist

- [ ] Bootstrap done (memory consulted, test levels asked, config resolved, codegraph availability noted)?
- [ ] Phase 1: plan_{feature}.md exists and user explicitly approved?
- [ ] spec-{feature}.md + user-stories_{feature}.md exist with ≥1 WP?
- [ ] W3 mode chosen (single / dispatcher) from WP count vs `parallel_threshold`?
- [ ] handover_{feature}.md received from W3?
- [ ] Post-impl reindex run if enabled and repo indexed?
- [ ] W4 dispatched with BUILD_SPEC + user-stories_{feature}.md + handover_{feature}.md + active test levels + `fix_as_failing_test`?
- [ ] W4 returned VALIDATION_PASS (within the 2-cycle W3↔W4 limit)?
- [ ] Finish: `maintain-docs` invoked and committed?
- [ ] Finish: `maintain-memory` invoked?
- [ ] Finish: work committed and PR opened (if the repo uses that flow)?
- [ ] Final summary delivered to user?
