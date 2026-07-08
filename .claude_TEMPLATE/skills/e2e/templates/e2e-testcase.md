# E2E Test Case — {feature}

Written by `spec-design`; consumed by the `e2e` skill. One end-to-end scenario through
the real UI. Keep steps concrete and observable — each is a When → Then a human or agent
can verify. Copied to `docs/artefacts/{sprint}/e2e_{feature}.md`.

## Scope

- **Validates:** {the user-facing flow this exercises}
- **Env:** {device / browser / build command, e.g. `./gradlew installDebug` on API 31+}
- **Preconditions:** {fresh install / seeded data / clock at known time / tools like ADB}

## Steps

### 1. {step name}

- **When:** {action the operator performs}
- **Then:** {observable expected result} ✓

### 2. {step name}

- **When:** {action}
- **Then:** {expected result} ✓

{add steps as needed; number them in run order}

## Expected outcomes

| Step | Expected result |
| --- | --- |
| {1 · name} | {result} |
| {2 · name} | {result} |
