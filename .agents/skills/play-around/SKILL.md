---
name: play-around
description: Explore a live web app by navigating and clicking through it, watching for unexpected errors, and reporting each finding for a human to confirm. Use when the user wants exploratory testing from a starting URL.
disable-model-invocation: true
---

# Play Around

Explore a live web app the way an untrained manual tester does: navigate, click around, and hunt for unexpected errors. Every finding is a single observation, so nothing is "confirmed" — everything lands for a human to judge.

Read `../_shared/browser-evidence.md` before running. Drive the browser through the Playwright MCP tools.

## Process

### 1. Start

- Take the starting URL from the user's argument (required). No URL → ask for one.
- Note the environment and start time for the report.

### 2. Explore within budget

- Start at the given URL. Follow **in-app links** (same origin) outward; prefer primary navigation and happy paths first, then edge links (footer, settings, forms, empty states).
- Stop after **20 pages** or **15 minutes**, whichever comes first, unless the user overrides (`--pages N`, `--minutes M`).
- Prefer the accessibility snapshot to read structure; use screenshots for layout or visually suspect states.

### 3. Watch for unexpected errors

Treat the following as a finding (the full signal list lives in `../_shared/browser-evidence.md`):

- console errors
- failed network requests (4xx/5xx)
- uncaught JS exceptions
- visible error toasts, dialogs, or error pages
- broken images or media
- dead links (404 pages)
- horizontal-scroll layout overflow
- accessibility violations

### 4. Record findings as you go

For each observation, capture: the URL, the exact steps that led there, what you saw vs what you expected, console errors, failed network calls, and a screenshot at `docs/test-reports/assets/<id>.png`.

Do **not** try to reproduce or classify — a single observation stays a single observation. Every finding is reported as **needs human confirm**.

### 5. Write the report

Write one report per run to `docs/test-reports/<yyyy-mm-dd>-<slug>.md`, following the conventions in `../_shared/browser-evidence.md`. Summary line up top (pages visited, findings count). Each finding gets the evidence bundle above. Close by listing the pages visited so the human can see the coverage.
