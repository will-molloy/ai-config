---
name: merge-main
description: |
  Update a branch by merging its base branch (resolved from the PR, defaults to main).
  Merges when the history is clean, or cherry-pick rebases when there are stale commits
  to exclude (e.g. from squash-merged stacked PRs).
  Use when asked to "merge main", "fix conflicts", or "update branch".
allowed-tools:
  - Bash
  - Read
  - Edit
  - AskUserQuestion
---

## Instructions

Update one or more branches with the latest from their base branch.

### Input

`$ARGUMENTS` contains branch names or PR numbers (space-separated). If empty, use the current branch.

If PR numbers are given, resolve them to branch names:
```bash
gh pr view PR_NUMBER --json headRefName --jq '.headRefName'
```

### Per-branch workflow

1. **Resolve the base branch and snapshot the pre-merge diff:**
   ```bash
   base=$(gh pr view BRANCH_NAME --json baseRefName --jq '.baseRefName' 2>/dev/null || echo "main")
   git fetch origin "$base" BRANCH_NAME
   git checkout BRANCH_NAME
   git diff "origin/$base...HEAD" > /tmp/pre-merge.diff
   ```
   List all commits not on the base: `git log --oneline "origin/$base..HEAD"`
   Identify the ticket prefix from the branch name (e.g. `proj-123-...` -> `PROJ-123`).
   Check whether all commits match the ticket prefix.

2. **Choose strategy:**
   - **All commits match the ticket** -> simple merge: `git merge "origin/$base"`
   - **Some commits don't match** (e.g. from a squash-merged base PR) -> cherry-pick rebase to keep only this ticket's commits (see step 3b)

3a. **Simple merge:**
   ```bash
   git merge "origin/$base"
   ```
   If conflicts arise, resolve them (see conflict resolution below), then `git commit --no-edit`.

3b. **Cherry-pick rebase:**
   First, ensure all commits are on the remote:
   ```bash
   git log --oneline origin/BRANCH_NAME..HEAD
   ```
   If there are unpushed commits, push them first (`git push --no-verify`) before proceeding. This ensures `git reset --hard` cannot lose work.

   Then rebase:
   ```bash
   git reset --hard "origin/$base"
   git cherry-pick COMMIT1 COMMIT2 ... # oldest first, only ticket commits
   ```

4. **Conflict resolution (either strategy):**
   - Read each conflicted file to understand both sides
   - Resolve by choosing the correct side or combining changes
   - Stage resolved files with `git add`
   - For merge: complete with `git commit --no-edit`
   - For cherry-pick: continue with `git cherry-pick --continue`

5. **Verify diff is unchanged:**
   ```bash
   git diff "origin/$base...HEAD" > /tmp/post-merge.diff
   diff /tmp/pre-merge.diff /tmp/post-merge.diff
   ```
   This MUST be empty. If not, the merge/rebase introduced unintended changes - investigate before pushing.

6. **Verify build:**
   Build and run tests using the repo's standard commands (check README/Makefile for the right invocations).

7. **Push:**
   - After merge: `git push --no-verify`
   - After cherry-pick rebase: `git push --force-with-lease --no-verify`

### Rules
- **Only resolve conflicts - never modify code beyond what's needed to combine both sides of a conflict**
- If tests fail after rebase/merge, report the failures but do NOT fix them - that's a separate task
- If unsure which side of a conflict to take, use `AskUserQuestion`
- After pushing, switch back to the original branch the user was on
