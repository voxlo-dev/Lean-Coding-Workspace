# E2E Report — {feature}

Result of validating `e2e_{feature}.md`. Written by `e2e` to
`artefacts/{sprint}/e2e-report_{feature}.md`, frozen by `close-sprint`. Stands on its own:
`test-dump/` paths may be referenced, never relied on.

## Run

- Date: {YYYY-MM-DD} · Env: {device / emulator / browser · build}
- Driver: {framework + invocation, or `agent` / `user`}
- Context: {this flow in isolation | full suite} — an isolated run cannot see order dependence, so
  its green says nothing about the suite
- Script: {path in the test tree — committed · `test-dump/{name}` — temporary · none}

## Steps

`✅ pass · ❌ fail · ⚠️ partial · ⏭️ skipped (precondition unmet)`

| # | Step | Mode | Verdict | Observed (only if not ✅) |
| --- | --- | --- | --- | --- |
| 1 | {name} | {auto/agent} | {✅} | |

## Result

- Pass: {n} · Fail: {n} · Blocker: {if any}

## Bugs found

1. [{area}] {symptom — what deviated from expected} → {the fix implementation should make}

{Green run with no bugs: drop both this section and the one above's blocker.}
