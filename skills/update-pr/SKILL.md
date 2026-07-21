---
name: update-pr
description: |
  Update an existing PR — commit, push, and refresh the description.
  Use when asked to "update PR", "update PR description", or "refresh PR".
allowed-tools:
  - Bash
  - Read
---

## Instructions

Update an existing PR description to reflect the current state of the branch. Follow the `/open-pr` skill conventions for formatting.

### Input

`$ARGUMENTS` is optional and may contain a PR number. If not provided, detect from the current branch:
```bash
gh pr list --head "$(git branch --show-current)" --json number --jq '.[0].number'
```

### Steps

1. **Fetch the current PR body first** — always read before writing:
   ```bash
   gh pr view <number> --json body --jq '.body'
   ```
   Treat this as the source of truth. The user may have made manual edits since the last push.

2. **Read the current diff:**
   ```bash
   git log --oneline origin/main..HEAD
   git diff origin/main..HEAD --stat
   ```

3. **Update the proposed changes section** to reflect the current branch state, following `/open-pr` conventions. Preserve everything else (GIFs, ticket links, spec links, compliance checkboxes, review sections) exactly as-is from the fetched body — do not rewrite sections you aren't updating.

4. **Push and edit:**
   ```bash
   git push
   gh pr edit <number> --body "..."
   ```

### Rules
- After editing, remind the user to run /review and /clean-commits before merging (same as `/open-pr`)
