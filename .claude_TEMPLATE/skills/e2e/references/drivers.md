# E2E Drivers

Per-project one-time setup, recorded in `docs/dev.md` so later runs skip this file. Two rules hold everywhere: **a plain-text reporter on stdout is the agent's only input**, and **all artifacts go to `test-dump/`** (gitignored, expendable).

## Playwright — the strongest option; pick it where the choice is open

```javascript
// playwright.config.ts
export default defineConfig({
  outputDir: 'test-dump/playwright',
  reporter: [['list'], ['json', { outputFile: 'test-dump/playwright/results.json' }]],
  use: { screenshot: 'only-on-failure', video: 'retain-on-failure', trace: 'retain-on-failure' },
});
```

`list` is the stdout stream to read; `json` is there for parsing a large or flaky run, never both at once. Skip the `html` reporter — it costs a browser and reads no better. `npx playwright codegen {url}` bootstraps a first script from a recorded click-through; treat its output as a draft to rewrite against stable selectors.

## Cypress

```javascript
// cypress.config.ts
e2e: {
  screenshotsFolder: 'test-dump/cypress/screenshots',
  videosFolder: 'test-dump/cypress/videos',
  video: true, videoCompression: true,
}
```

`cypress run` prints a readable summary and screenshots failures on its own; add `reporter: 'mochawesome'` only when a machine-readable file is actually needed.

## Espresso / Android

Least turnkey — expect real setup cost, and weigh a temporary script accordingly.

- Run: `./gradlew connectedDebugAndroidTest`; JUnit XML lands in `app/build/outputs/androidTest-results/connected/`.
- Screenshots are explicit: `Screenshot.capture()` (`androidx.test:runner`) inside the test, or `adb exec-out screencap -p > test-dump/{name}.png` from outside.
- Gradle's own stdout is the pass/fail signal; parse the XML only when a failure message is truncated.

## No framework

CLI, desktop or anything unsupported: a plain script in the project's language that drives the flow and asserts, printing one line per step. The value is the free rerun, not the tooling.
