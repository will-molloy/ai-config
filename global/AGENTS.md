# General guidelines

- Never use the em dash. Use the plain dash "-" instead.
- Never modify files marked as auto-generated.
- Preserve the user's edits - comments, PR description changes, commit message tweaks, etc.
  Don't revert or overwrite them.
- In Markdown, avoid packing many sentences onto one long physical line (~200 chars):
  prefer one sentence per line; short sentences can share a line.
- When making technical decisions, do not give much weight to development cost.
  Instead prefer quality, simplicity, robustness, scalability, and long-term maintainability.
- Start bug fixes by reproducing the bug E2E, the way an end user hits it,
  so the fix addresses the real problem.
- When E2E testing, obsess over pixel perfection - fix anything that clearly looks off,
  even if unrelated. Hold lint failures, test failures, and flakiness to the same
  standard: fix them alongside the main work.
- Think before acting - discuss the approach before implementing, especially on
  tradeoffs or non-obvious implications.
- After completing a task don't prompt for the next one.

# Operational

- Clone repos to `~/Documents/Working/`. Create git worktrees there with a numeric
  suffix (e.g. `repo-name-2`), not under `.claude/worktrees/`.
- Save screenshots to `~/Pictures/Screenshots/<YYYY>/` with a descriptive filename.

# Tooling

- Use the repo's package manager scripts (e.g. `yarn test`, `make build`) - not raw binary invocations.
- Use `/browse` for all web browsing.
- When a JetBrains MCP server is connected, prefer its tools for refactoring
  (rename, move, reformat), building, running tests, inspections, and symbol search.

# Code style

Philosophy: make illegal states unrepresentable. Favour plain data values, closed type
hierarchies, and exhaustive pattern matching. Separate data from behaviour where practical.

- Prefer immutability by default; use mutable state only with a clear reason.
- Model with algebraic data types: records for product types, sealed hierarchies or
  discriminated unions for sum types, exhaustive switches over them.
- Default types to closed; only open for extension when it's an intentional design choice.
- Let the type system enforce constraints rather than runtime validation where possible.

# Testing

- Prefer TDD: write the test first, watch it fail, then implement.
- Only test our own logic - not third-party library behaviour.
- Don't test negatives (trust the underlying query/logic) or tautologies
  (a test that restates the implementation adds no value).
- Run targeted tests to verify changes, not the full suite.
- Use exact assertions - if the value is known, assert the whole thing.

# Workflow

- Work on branches and open a draft PR. Never commit directly to main.
- Commit often in logical chunks.
- Use imperative mood for commit messages and PR titles/descriptions.
- Branch names: lowercase, ticket-prefixed, no `feature/` prefix (e.g. `ticket-123-short-description`).
- Keep PR/commit descriptions terse - state what changed, don't restate the ticket's background or problem description; the reviewer can read the ticket.
- Before merging a PR, run /review and /clean-commits.

---

# Tech

<!-- e.g. "I build distributed cloud services" or "I build iOS apps" -->

---

# Domain

<!-- e.g. "I work on e-commerce" or "I work on healthcare" -->
