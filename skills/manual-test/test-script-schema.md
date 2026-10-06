# Test Script Schema

Test scripts are markdown files, one per feature or suite, living under `test-scripts/` (or passed ad-hoc by path/URL). `manual-test` reads them.

## File layout

```markdown
# <Feature / suite name>

## Preconditions
- **URL:** https://app.example.com/checkout
- **Login:** none | test-scripts/users/<role>.md | inline credentials
- **Data:** any seeded data, fixtures, or state the script assumes
- **Env:** staging | production | local (optional)

## Case: <case title>

### Steps
1. <do this> → **expected:** <this should happen>
2. <do that> → **expected:** <that should happen>

## Case: <another case title>

### Steps
1. <do this> → **expected:** <this should happen>
```

## Rules

- One `## Case:` per test case. A case is the unit of pass/fail; the retest loop runs per case.
- Steps are numbered; every step carries an `→ expected:` clause. A step without an expected result is a bug in the script — flag it, don't guess.
- A case **passes** only if every step's actual result matches its expected result.
- `Preconditions` apply to the whole file. If a case needs different preconditions, add its own `### Preconditions` under the case heading; it overrides per-case.
- Login: prefer a role file (a markdown file describing the role/credentials) over inline secrets. Never commit real credentials; put them in a git-ignored file or pass them per run.
- Keep expected results concrete and checkable ("error toast 'Card declined' appears") rather than vague ("works correctly").
