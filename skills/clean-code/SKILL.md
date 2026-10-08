---
name: clean-code
description: |
  Clean up a branch's changes so they read easily - naming, ordering, structure, comments and test helpers.
  Use when asked to "clean up the code" or "tidy this up".
allowed-tools:
  - Bash
  - Read
  - Edit
  - Write
---

# Clean code

Cleans up the code a branch changes so it's easier to read, without changing what it does for its callers.

The cleanup brings the code in line with two sets of rules:
- the "Code style", "Testing" and "Code comments" sections of the global instructions (`CLAUDE.md` or `AGENTS.md`)
- the checklist below, which adds to those sections rather than repeating them

## Inputs

- A path or commit range. If none is given, clean the current branch's changes against its base branch.

## Rules

- The code must have passing tests before any cleanup. If the changed code has no tests, stop and say what's missing.
- Keep what the code does for its callers the same. Bug fixes and feature changes are out of scope, so list them instead of making them.
- Changing types to follow the code style is in scope, such as making data immutable or closing a type.
- Touch only the code the branch adds or changes, plus untouched code the change makes inconsistent.
- Make a change only when it's a clear win. A rename that's merely different is churn.
- Preserve the user's own edits and wording.
- Leave the changes uncommitted.

## Steps

1. Read the full diff, then read each changed file whole. Order and naming only make sense in context.
2. Run the tests for the changed code and confirm they pass.
3. Work through the global code style rules and the checklist below, and apply each clear win.
   Use the editor's refactoring tools for renames and moves where they're available.
4. Run the targeted tests, the type checker and the linter. Fix anything the cleanup broke.

## Checklist

### Naming

- **One word per concept.** The same thing has the same name everywhere the change touches it,
  including tests, types, variables and error messages.
  Fix two names for one thing, or one name for two things.
- **Use the existing vocabulary.** Prefer the term the schema, API or domain already uses over a new one.
  When the code already settled on a word, new code matches it.
- **Names say what the thing is.** A name shouldn't read as a different operation.
  For example, `roundTotals` for totals per page sounds like rounding.
- **No redundant qualifiers.** Drop a prefix the module or type already implies.
- **No borrowed jargon.** Every noun should name something the codebase or ticket defines.

### Order

- **Types and constants first**, above all functions.
- **Functions in calling order, in one direction per file.** Either callers above the functions they call (top-down)
  or helpers above their callers (bottom-up). Match the file's existing direction.

### Structure

- **Keep data that changes together in one value.** Parallel collections keyed the same way
  (one map of names, another of totals) drift apart. One record per key can't.
- **Derive instead of duplicating.** If one value follows from another, compute it.
- **No indirection that only passes values through.** Inline a single-use helper when splitting it out
  forces callers to hand over internals it should own.
- **No unreachable fallbacks.** A default that can't fire hides a broken invariant. Make the invariant hold by type, or throw.
- **No test-only visibility.** A helper made public only so a test can reach it belongs in its own module.

### Comments

- **Beside the line it explains**, at the narrowest scope that contains it.
- **One plain sentence** that says what the code does or why. Rewrite clever or vague wording in plain words.
- **No comment that restates a name.** Prefer renaming over explaining a bad name.
- **Present state only.** No history, no abandoned approaches, no references to other repos.

### Tests

- **Helpers use the production vocabulary.** A test helper's name matches what the production code calls the thing.
- **Mocks respond to what the code asks for.** A mock that returns data whatever the request was lets a broken request pass.
  Derive the response from the request, so a wrong request fails the test.
- **Fixture values are distinct where mixing them up is a bug.** Two inputs with identical values can't catch one used in place of the other.
- **Test names state the behaviour**, matching the repo's naming convention.

## Output

- The changes made, grouped by file, each with a one-line reason.
- The test, type check and lint results.
- Judgement calls left alone, each with the trade-off in one line, so the user can decide.
- An "Out of scope" list for bug fixes and feature changes noticed along the way, one line each.
