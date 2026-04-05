---
name: git-commit
description: >
  Create atomic git commits with conventional message format.
  Use after completing a task or unit of work.
---

## Steps

1. Check current repository state with `git status`
2. Review changes with `git diff` to confirm only task-related changes are included
3. Stage specific files with `git add <file>` — never `git add .` or `git add -A`
4. Write the commit following project convention

## Message convention

```
<type>(<scope>): <short description>

<optional body: why this change was made>
```

Allowed types:
- `feat` — new functionality
- `fix` — bug fix
- `refactor` — restructuring without behavior change
- `test` — add or modify tests
- `docs` — documentation
- `chore` — maintenance (dependencies, config)

## Expected output

An atomic commit that:
- Contains only changes from one task or logical unit
- Has a message that explains the why, not the what
- Does not include unrelated files
- Does not include sensitive files (.env, credentials, secrets)

## Common mistakes

- Giant commits mixing multiple tasks
- Using `git add .` and including unwanted files
- Generic messages like "fix bug" or "update code"
- Committing files with secrets or credentials
- Using `--no-verify` to skip hooks without justification
