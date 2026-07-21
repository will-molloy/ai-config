---
name: clean-commits
description: |
  Rewrite branch history into logical commits for a clean merge.
  Use when asked to "clean commits", "clean up commits", "rewrite history",
  "organize commits", or "tidy up the branch".
allowed-tools:
  - Bash
  - Read
---

## Instructions

1. **Read and follow** the commit conventions in `~/.claude/skills/commit/SKILL.md`.
2. Rewrite the current branch's commits into logical, atomic chunks against the base branch.

### Steps

1. **Guard: ensure we're on a feature branch:**
   ```bash
   branch=$(git branch --show-current)
   if [ "$branch" = "main" ] || [ "$branch" = "master" ]; then
     echo "ERROR: refusing to rewrite $branch"; exit 1
   fi
   ```

2. **Read the PR description to understand the intended structure:**
   ```bash
   gh pr view --json body --jq '.body'
   ```
   The PR description's Changes and Refactoring sections define the logical
   groupings. Each section or distinct bullet group is a candidate commit.
   Refactoring that is unrelated to the main feature should be a separate commit.

3. **Identify the base branch and read the diff:**
   ```bash
   base=$(gh pr view --json baseRefName --jq '.baseRefName' 2>/dev/null || echo "main")
   git log --oneline "origin/$base..HEAD"
   git diff "origin/$base..HEAD" --stat
   git diff "origin/$base..HEAD"
   ```
   Read the ENTIRE diff. Map each changed file to a commit group based on
   the PR description structure.

4. **Plan the commits:**
   Use the PR description sections as the primary guide. Each commit should:
   - Correspond to a logical group from the description
   - Build on its own (no forward references)
   - Include tests alongside the code they test

   Prefer more granular commits over fewer large ones. A typical split:
   refactoring first, then database layer, then each service/domain area
   that was changed. Don't collapse separate bullet groups into one commit
   just because they're in the same PR section - if the description lists
   them separately, they're likely separate commits.

5. **Save old HEAD and soft reset:**
   ```bash
   old_head=$(git rev-parse HEAD)
   git reset --soft "origin/$base"
   ```

6. **Unstage and re-commit in logical chunks:**
   ```bash
   git reset HEAD -- .
   ```
   Then selectively stage and commit in order using `git add <specific files>`.
   Use `git add -N <file>` for new files before `git add -p`.

7. **Verify the rewrite is identical:**
   ```bash
   git diff "$old_head"
   ```
   This MUST be empty. If not, something was missed.

8. **Force push:**
   ```bash
   git push --force-with-lease --no-verify
   ```

### Rules
- Never rewrite main/master
- Only rewrite commits between `origin/$base` and `HEAD`
- Verify the branch builds after the final commit
- The end state must be identical to before the rewrite (`git diff $old_head` is empty)
