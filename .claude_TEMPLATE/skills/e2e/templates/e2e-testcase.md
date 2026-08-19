# E2E Test Case — {feature}

One end-to-end scenario through the real UI: written by `spec-design`, consumed by `e2e`,
living at `artefacts/{sprint}/e2e_{feature}.md`. Each step is a concrete, observable
When → Then that a human or agent can verify.

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
