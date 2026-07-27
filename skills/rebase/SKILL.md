---
name: rebase
description: |
  Rebase a branch onto its base branch (resolved from the PR, defaults to main).
  Use when asked to "rebase", "rebase onto main", "update branch", or "update stacked PR".
allowed-tools:
  - Bash
  - Read
  - Edit
  - AskUserQuestion
---

## Instructions

Rebase one or more branches onto their base branch.

### Input

`$ARGUMENTS` contains branch names or PR numbers (space-separated). If empty, use the current branch.

If PR numbers are given, resolve them to branch names:
```bash
gh pr view PR_NUMBER --json headRefName --jq '.headRefName'
```

### Per-branch workflow

1. **Resolve the base branch:**
   ```bash
   base=$(gh pr view BRANCH_NAME --json baseRefName --jq '.baseRefName' 2>/dev/null || echo "main")
   ```

2. **Fetch and snapshot the pre-rebase diff:**
   ```bash
   git fetch origin "$base" BRANCH_NAME
   git checkout BRANCH_NAME
   git diff "origin/$base...HEAD" > /tmp/pre-rebase.diff
   ```

3. **Ensure no unpushed commits:**
   ```bash
   git log --oneline origin/BRANCH_NAME..HEAD
   ```
   If there are unpushed commits, push them first (`git push --no-verify`) before proceeding.

4. **Rebase:**
   ```bash
   git rebase "origin/$base"
   ```
   If the rebase produces conflicts from stale commits (e.g. from a squash-merged base PR), abort and cherry-pick instead:
   ```bash
   git rebase --abort
   git reset --hard "origin/$base"
   git cherry-pick COMMIT1 COMMIT2 ... # oldest first, only this ticket's commits
   ```

5. **Conflict resolution:**
   - Read each conflicted file to understand both sides
   - Resolve by choosing the correct side or combining changes
   - Stage resolved files with `git add`
   - For rebase: continue with `git rebase --continue`
   - For cherry-pick: continue with `git cherry-pick --continue`

6. **Verify diff is unchanged:**
   ```bash
   git diff "origin/$base...HEAD" > /tmp/post-rebase.diff
   diff /tmp/pre-rebase.diff /tmp/post-rebase.diff
   ```
   This MUST be empty. If not, the rebase introduced unintended changes - investigate before pushing.

7. **Verify build:**
   Build and run tests using the repo's standard commands (check README/Makefile for the right invocations).

8. **Push:**
   ```bash
   git push --force-with-lease --no-verify
   ```

### Rules
- **Only resolve conflicts - never modify code beyond what's needed to combine both sides of a conflict**
- If tests fail after rebase, report the failures but do NOT fix them - that's a separate task
- If unsure which side of a conflict to take, use `AskUserQuestion`
- After pushing, switch back to the original branch the user was on
