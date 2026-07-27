---
name: rebase-main
description: |
  Rebase a stacked PR branch onto the latest main after its base PR was squash-merged.
  Use when asked to "rebase main", "rebase onto main", or "update stacked PR".
allowed-tools:
  - Bash
  - Read
  - Edit
  - AskUserQuestion
---

## Instructions

Rebase one or more stacked PR branches onto `origin/main` after their base PR has been squash-merged.

### Input

`$ARGUMENTS` contains branch names or PR numbers (space-separated). If empty, use the current branch.

If PR numbers are given, resolve them to branch names:
```bash
gh pr view PR_NUMBER --json headRefName --jq '.headRefName'
```

### Per-branch workflow

1. **Fetch and snapshot the pre-rebase diff:**
   ```bash
   git fetch origin main BRANCH_NAME
   git checkout BRANCH_NAME
   git diff origin/main...HEAD > /tmp/pre-rebase.diff
   ```

2. **Ensure no unpushed commits:**
   ```bash
   git log --oneline origin/BRANCH_NAME..HEAD
   ```
   If there are unpushed commits, push them first (`git push --no-verify`) before proceeding.

3. **Rebase:**
   ```bash
   git rebase origin/main
   ```

4. **Conflict resolution:**
   - Read each conflicted file to understand both sides
   - Resolve by choosing the correct side or combining changes
   - Stage resolved files with `git add`
   - Continue with `git rebase --continue`

5. **Verify diff is unchanged:**
   ```bash
   git diff origin/main...HEAD > /tmp/post-rebase.diff
   diff /tmp/pre-rebase.diff /tmp/post-rebase.diff
   ```
   This MUST be empty. If not, the rebase introduced unintended changes - investigate before pushing.

6. **Verify build:**
   Build and run tests using the repo's standard commands (check README/Makefile for the right invocations).

7. **Push:**
   ```bash
   git push --force-with-lease --no-verify
   ```

8. **Update PR base branch:**
   ```bash
   gh pr edit BRANCH_NAME --base main
   ```

### Rules
- **Only resolve conflicts - never modify code beyond what's needed to combine both sides of a conflict**
- If tests fail after rebase, report the failures but do NOT fix them - that's a separate task
- If unsure which side of a conflict to take, use `AskUserQuestion`
- After pushing, switch back to the original branch the user was on
