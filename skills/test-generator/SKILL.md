---
name: test-generator
description: >
  Generate unit and integration tests aligned to acceptance criteria.
  Use when implementing a feature or fixing a bug.
---

## Steps

1. Read the acceptance criteria for the task in TASK.md
2. Identify the cases to cover:
   - Happy path
   - Input validations
   - Edge cases defined in the criteria
3. Write tests that verify behavior, not implementation
4. Follow the project's testing conventions (location, naming, framework)

## Principles

- **One test = one main assertion.** If you need to verify multiple things, write multiple tests.
- **Name tests by what they verify**, not what they execute. `should_reject_invalid_email` > `test_validation`.
- **Do not mock unnecessarily.** If you can test against the real dependency without excessive complexity, do it.
- **Tests are documentation.** Someone reading only the tests should understand what the feature does.

## Expected output

- Tests that cover the acceptance criteria of the task
- Tests that pass in a clean project (no dependency on uncontrolled external state)
- Tests located where the project convention expects them

## Common mistakes

- Writing tests that verify implementation details instead of behavior
- Creating tests that always pass (trivial or empty assertions)
- Not cleaning state between tests (execution order dependency)
- Testing third-party code instead of your own
- Ignoring acceptance criteria and testing something else
