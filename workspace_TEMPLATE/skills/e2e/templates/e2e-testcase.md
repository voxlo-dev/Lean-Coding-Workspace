# E2E Test Case — {feature}

One end-to-end scenario through the real UI: written by `spec-design`, consumed by `e2e`,
living at `artefacts/{sprint}/e2e_{feature}.md`. Each step is a concrete, observable
When → Then. Phrase **Then** as something an assertion could decide, so `e2e` can script it; mark
a step `[agent]` only where a script can't reach it (other app, system dialog, hardware, or a
purely visual judgement).

## Scope

- **Validates:** {the user-facing flow this exercises}
- **Env:** {target the flow runs on, and the command that gets a build onto it}
- **Preconditions:** {state the flow starts from — fresh install / seeded data / known clock}
- **Mockup:** {path/link, where the feature was designed — `e2e` checks the screenshots against it · drop the line otherwise}

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
