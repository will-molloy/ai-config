---
name: commit
description: |
  Stage all changes and commit with a detailed message.
  Use when asked to "commit", "commit this", or "commit changes".
allowed-tools:
  - Bash
  - Read
---

## Instructions

Stage ALL changes and create a NEW commit with a detailed message.

**NEVER amend.** Always create a new commit, even if the previous commit is yours.

### Steps

1. **Read the full diff** - the user may have made edits you haven't seen:
   ```bash
   git diff
   git diff --staged
   ```
   Read the ENTIRE diff carefully. Do not rely on your memory of what changed -
   the user may have edited files since your last tool call.

2. **Check recent commits for style:**
   ```bash
   git log --oneline -5
   ```

3. **Stage and commit:**
   - Stage everything: `git add -A`
   - Infer the ticket prefix from the branch name (e.g. `proj-123-...` -> `PROJ-123`)
   - Write the commit message using a HEREDOC:
     ```bash
     git commit -F /dev/stdin << 'EOF'
     TICKET-ID Subject line

     Optional body with more detail.

     Co-Authored-By: <agent>
     EOF
     ```

### Commit message conventions
- Imperative mood ("Add X", "Fix Y", not "Added X", "Fixed Y")
- Ticket ID with no brackets: `PROJ-123 Description`, not `[PROJ-123] Description`
- Short subject line; put detail in the body after a blank line
- Describe the diff as a snapshot - what the code looks like now, not the
  incremental steps taken to get there. If you added tests then renamed a helper
  then fixed a typo, the message is "Add Retry-After header tests", not a
  changelog of the working session.
- Only add co-author if you (Claude) contributed >50% of the changes in the diff

### Rules
- **NEVER use `--amend`** - always a new commit
- Never skip pre-commit hooks (`--no-verify`)
- Never stage files that likely contain secrets (.env, credentials.json, etc.)
- Do NOT push - only commit
