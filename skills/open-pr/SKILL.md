---
name: open-pr
description: |
  Create a draft PR for the current branch. Commits, pushes, and opens the PR.
  Use when asked to "open PR", "create PR", "raise PR", or "draft PR".
allowed-tools:
  - Bash
  - Read
  - AskUserQuestion
---

## Instructions

Create a draft PR for the current branch.

### Input

`$ARGUMENTS` is optional and may contain the ticket ID (e.g. `PROJ-123`). If not provided, infer it from the branch name.

### Steps

1. **Check state:**
   ```bash
   git status --short
   git log --oneline origin/main..HEAD
   ```

2. **Commit if needed:** If there are uncommitted changes, stage and commit them with the ticket prefix.

3. **Push:**
   ```bash
   git push -u origin HEAD --no-verify
   ```

4. **Check for PR template:**
   ```bash
   cat .github/PULL_REQUEST_TEMPLATE.md 2>/dev/null
   ```
   If a template exists, use it. Otherwise use the fallback format below.

5. **Create draft PR:**
   ```bash
   gh pr create --draft --title "TICKET-ID Description" --body "$(cat <<'EOF'
   ...
   EOF
   )"
   ```

### PR conventions
- Always create as **draft** (`--draft`)
- Title: `TICKET-ID <description>` in imperative mood (e.g. "Add unified form with X and Y", not "Unified form for X and Y"). No square brackets
- Write description bullets in imperative commit style - e.g. "Add X", "Remove Y", "Replace X with Y" - not passive meta-commentary like "Consolidated X" or "Changes were made to Y"
- Link tickets as markdown links where possible
- Link any related spec alongside the ticket URL if one exists
- Keep shared content AI-agnostic - don't mention specific tools
- For stacked PRs, add a blockquote after proposed changes:
  > **Stacked PR** - this builds on [#NNN](url), which should be reviewed and merged first. After #NNN is squash-merged, this branch will be rebased onto `main`.

### Description structure (critical - read carefully)

The description covers the **final diff against main** - what the code looks like NOW, not the incremental development history. Never describe intermediate steps ("collapse X into Y", "replace X with Y" when Y is the only thing that exists in the diff).

#### Changes vs Refactoring

Group bullets under **"Changes"** and **"Refactoring"** sections. The distinction is strict:

- **Changes** = net-new functionality that didn't exist before (new endpoints, new models, new packages). If you deleted it from the diff, the feature wouldn't work.
- **Refactoring** = restructuring, renaming, improving existing code (splitting controllers, adding logging, adding docs, renaming models, improving error handling). If you deleted it from the diff, the old code would still work.

Do NOT put refactoring in Changes. Logging, error handling improvements, XML docs, extension methods - these are all refactoring. Extracting existing code into smaller components/hooks is refactoring, not a new feature.

If the refactors need context (e.g. they were done as part of a prior approach), add a short intro line before the bullets explaining why they exist in this PR.

#### Tests

Omit the Tests section unless the PR is primarily about tests. Test changes are implied by code changes - listing them separately is noise.

If you do include a Tests section, keep it **high level** - one bullet per test project or area. Never list individual test names, counts, or specific assertions.

### Fallback format (when no PR template exists)
```
Ticket: <link to ticket>

<1-2 sentence summary>

## Changes
<Subsections describing what changed, grouped logically>

## Refactoring
<If applicable>
```

### Rules
- Before creating a PR, grep for TODO comments referencing the current ticket (e.g. `TODO.*PROJ-XXXXX`) and resolve them. TODOs referencing other tickets are fine to leave.
- When pushing to a branch with an open PR, update the PR description to reflect the latest changes (follow the same PR conventions above)
- Return the PR URL when done
- Never push to main/master
- Always link repos, PRs, issues as markdown links - never bare text references
