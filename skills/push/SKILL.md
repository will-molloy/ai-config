---
name: push
description: |
  Stage all changes, commit, and push in one step.
  Use when asked to "push", "push this", "commit and push", or "commit-push".
allowed-tools:
  - Bash
  - Read
---

## Instructions

1. **Read and follow** all instructions in `~/.claude/skills/commit/SKILL.md`.
2. **Then push:**
   ```bash
   git push --no-verify
   ```
   If the branch has no upstream yet:
   ```bash
   git push -u origin HEAD --no-verify
   ```

### Rules
- Never push to main/master
