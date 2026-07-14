# E2E Run Log — {feature}

Protocol of one execution of `e2e_{feature}.md`. Per step: observed behaviour vs. expected,
plus a verdict. Written by the `e2e` skill (or its run agent). Copied to
`artefacts/{sprint}/e2e-run_{feature}.md`.

## Run metadata

- Date: {YYYY-MM-DD}
- Env: {device / emulator / browser · build}
- Driver: {manual / MCP server / browser agent}
- Mode: {blind — bugs not pre-disclosed / guided}

## Verdict legend

- ✅ PASS — matches expected · ❌ FAIL — deviates · ⚠️ PARTIAL · ⏭️ SKIPPED (precondition unmet)

## Steps

### 1. {step name}

| Sub-step | Observed | Verdict |
| --- | --- | --- |
| {expected check} | {what happened} | {✅/❌/⚠️/⏭️} |

### 2. {step name}

| Sub-step | Observed | Verdict |
| --- | --- | --- |
| {expected check} | {what happened} | {✅/❌/⚠️/⏭️} |

## Result

- Pass: {n} · Fail: {n} · Blocker: {if any}

## Bugs found

1. [{area}] {symptom — what deviated from expected}

## Hand-back to implementation

{concrete fixes the implement step should make, if the run was red}
