---
name: manual-test
description: Execute a manual-testing script against a live web app step by step, classify each case pass/fail/flaky/undetermined with a retest loop, and write a report. Use when the user wants a documented test script run against a live app.
disable-model-invocation: true
---

# Manual Test

Run a documented test script against a live web app the way a manual tester would, and produce a report of what passed, failed, flaked, and stayed undetermined.

Read `browser-evidence.md` and `test-script-schema.md` (bundled with this skill) before running. Drive the browser through the Playwright MCP tools. Never mark a step passed without observing its result.

## Process

### 1. Pick the script

- If the user passed a path or URL, use it. A path may be a file under `test-scripts/`, elsewhere in the repo, or an absolute path.
- If no argument, list `test-scripts/` and ask which script (or which case) to run.

Read the whole script before touching the browser. If any step lacks an `→ expected:` clause, flag it to the user — don't guess the expectation.

### 2. Set up

- Note the script's `Preconditions`: base URL, login, data, env.
- Authenticate per the script (role file or provided credentials). Never commit real credentials; prefer role files or per-run values.
- Record the environment and start time for the report.

### 3. Execute case by case

For each `## Case:`:

1. Navigate to the case's starting point (the base URL or the step that begins the case).
2. Perform each numbered step, then check the actual result against that step's `→ expected:` clause. Capture the URL, console errors, and failed network calls as you go.
3. A case **passes** only if every step matches its expected result. Any mismatch → the case **fails**.

### 4. Retest loop (failed cases only)

For each failed case, re-run it up to **3 attempts total** and classify:

- **real-fail** — fails every attempt with the same evidence.
- **flaky** — passes on any retry.
- **undetermined** — still inconsistent, or blocked by environment/data, after 3 attempts.

Loop: re-test failed cases until none remain undetermined (every case is real-fail or flaky). Never retry a case more than 3 times total.

### 5. Write the report

Write one report per run to `docs/test-reports/<yyyy-mm-dd>-<slug>.md`, following the conventions in `browser-evidence.md`. Summary line up top: `X pass / Y fail / Z flaky / N undetermined`. Each real-fail or undetermined case gets: URL, repro steps, expected vs actual, console errors, failed network calls, and a screenshot at `docs/test-reports/assets/<id>.png`.

Flag `real-fail` and `undetermined` cases for the human to confirm. `flaky` cases are noted and closed.
