---
name: to-test-script
description: Author manual-test test scripts from business-logic docs, design docs, and test plans, conforming to the shared test-script schema. Use when the user wants a documented test script written from source material.
disable-model-invocation: true
---

# To Test Script

Turn source material — business-logic docs, design docs, test plans — into test scripts that `manual-test` can run, conforming to `../_shared/test-script-schema.md`. Read that schema before writing anything.

## Inputs

- Accept a list of file paths and/or URLs, with pasted text as a fallback. Treat them as one corpus: a feature's script often needs the PRD *and* the design doc *and* the existing test plan together.
- Formats: structured text — markdown, tables, Gherkin.
- If no input is given, ask what to read.

## Process

### 1. Read and classify

Read the whole corpus. For each feature or suite, decide which mode applies:

- **reformat** — the source already contains test cases (e.g. a test plan). Reshape them into the schema; don't invent new cases.
- **derive** — the source describes rules, behavior, or screens but no cases. Synthesize cases from what the source states or strongly implies.

### 2. Map source to cases

- One `## Case:` per scenario, business rule, or acceptance criterion the source states.
- Happy path first, then the error/edge paths the source actually describes.
- Derive only what the source states or strongly implies. Do **not** invent edge cases the source doesn't ground.

### 3. Fill preconditions

Extract `Preconditions` from the source: URL, Login, Data, Env. When the source names roles (admin, customer, guest), write `test-scripts/users/<role>.md` describing the role and reference it from `Login`. Never write real credentials — the schema forbids it.

### 4. Collect gaps — never guess

A **gap** is anything the schema demands that the source doesn't supply:

- a step with no observable `→ expected:` result
- a vague expectation the source doesn't support making concrete ("works correctly")
- missing URL, login, data, or env
- a rule whose outcome the source doesn't state

### 5. Grill on gaps

If gaps exist, resolve them before writing: follow `../grilling/SKILL.md` to interview the user in rounds — frontier questions, one recommended answer each. If that file isn't present, grill inline the same way. Never fabricate an expectation, precondition, or case.

### 6. Write the scripts

- Write one file per feature/suite to `test-scripts/<slug>.md`, slug derived from the feature name.
- Every step carries an `→ expected:` clause; keep results concrete and checkable.
- Only grounded content is written. A gap the user defers becomes an explicit `<!-- GAP: ... -->` marker in the file, and is listed in the report.

## Output

Report the gap list alongside the written files: what was grounded, what was grilled, what was deferred. If the corpus yields more than 3 features, list them and confirm scope before writing.
