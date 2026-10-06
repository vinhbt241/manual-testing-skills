# Browser & Evidence Conventions

Both `manual-test` and `play-around` drive the browser through the **Playwright MCP server** (`@playwright/mcp`). It launches a **local** Chromium — no third party hosts or sees the browser.

## Prerequisites

The skills need the Playwright MCP server exposed to whatever agent runs them. The skills are harness-neutral; only the wiring differs. Run one of these, once:

- **Pi:** `pi mcp add playwright -- npx -y @playwright/mcp@latest`
- **Claude Code:** `claude mcp add playwright -- npx -y @playwright/mcp@latest`
- **Codex:** `codex mcp add playwright -- npx -y @playwright/mcp@latest`

Each writes to that harness's own MCP config (Pi: `~/.pi/agent/mcp.json`; Claude Code: `~/.claude.json` or a project `.mcp.json`; Codex: its own config). Add the harness's project-scope flag if you want the server committed to the repo instead of the user config.

## Operating the browser

- Prefer the **accessibility snapshot** (`browser_snapshot`) to read page structure — it's compact and needs no vision model.
- Use `browser_navigate`, `browser_click`, `browser_type`, `browser_press_key`, etc. to act. Resolve elements from the snapshot, not from CSS guesses.
- Take `browser_take_screenshot` when you need visual evidence or to disambiguate a broken layout.
- Read `browser_console_messages` and `browser_network_requests` to collect console errors and failed network calls for findings.

## Evidence bundle

For every finding capture, at minimum:

- **URL** at the moment of the observation
- **Steps to reproduce** (the exact actions that led there)
- **Expected vs actual**
- **Console errors** (from `browser_console_messages`)
- **Failed network requests** (4xx/5xx from `browser_network_requests`)
- **Screenshot** saved to `docs/test-reports/assets/<id>.png`

## Report location

One report per run: `docs/test-reports/<yyyy-mm-dd>-<slug>.md`. Screenshots: `docs/test-reports/assets/<id>.png`.

## Error signals (exploratory)

`play-around` treats these as unexpected errors worth a finding:

- console errors
- failed network requests (4xx/5xx)
- uncaught JS exceptions
- visible error toasts, dialogs, or error pages
- broken images or media
- dead links (404 pages)
- horizontal-scroll layout overflow
- accessibility violations

## Security note (read before running against a sensitive app)

The browser itself is local: no third-party service gets access to the app or the machine. But the **agent's model** receives everything the agent observes — page text, snapshots, screenshots, console/network output — because the model is the one doing the observing.

The skills are **provider-agnostic**: they work with any model. If the app-under-test contains data that must not leave the machine, run with a self-hosted/local model (Ollama, vLLM, …); the skills are unchanged — only the model config changes.
