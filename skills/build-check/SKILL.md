---
name: build-check
description: >
  Verify that the project compiles and all tests pass.
  Use after implementing changes and before committing.
---

## Steps

1. Run the project's build command to verify it compiles without errors
2. Run the existing test suite to verify nothing is broken
3. If a linter is configured, run it
4. Verify the project starts correctly if applicable

## Commands by stack

Identify the project stack and use the corresponding command. Common examples:

| Stack | Build | Tests | Lint |
|---|---|---|---|
| .NET | `dotnet build` | `dotnet test` | (project dependent) |
| Node/JS | `npm run build` | `npm test` | `npm run lint` |
| Python | (project dependent) | `pytest` | (project dependent) |
| Flutter | `flutter build` | `flutter test` | `flutter analyze` |

Always check the project for the actual commands before executing.

## Expected output

Confirmation that:
- The project compiles without errors
- Existing tests pass
- No new warnings were introduced (if the project treats them as errors)

## If something fails

- **Compilation error**: Fix before continuing. Do not commit code that does not compile.
- **Broken test**: Check if your changes broke it. If it is a pre-existing failure, document in the changelog.
- **New warning**: Evaluate if relevant. If so, fix. If not, document why it is ignored.

## Common mistakes

- Committing without verifying the build passes
- Assuming "it compiles in my context" means it works
- Ignoring broken tests assuming they are not your responsibility
- Not verifying the project starts after configuration changes
