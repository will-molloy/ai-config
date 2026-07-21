---
name: amend
description: |
  Stage all changes and amend the previous commit.
  Use when asked to "amend", "amend this", or "amend commit".
allowed-tools:
  - Bash
  - Read
---

## Instructions

Stage ALL changes and amend the previous commit.

**ALWAYS amend.** Never create a new commit.

Follow the commit message conventions in `/commit`.

### Steps

1. **Read the full diff and the previous commit:**
   ```bash
   git diff
   git diff --staged
   git log -1 --format='%s%n%n%b'
   ```
   Read the ENTIRE diff carefully. Do not rely on your memory of what changed -
   the user may have edited files since your last tool call.

2. **Decide whether to update the message:**
   - If the diff is trivial (formatting, typo, minor tweak) and doesn't change
     the meaning of the previous commit, use `--no-edit`.
   - If the diff adds, removes, or changes something substantive, rewrite the
     commit message to reflect the full amended content.

3. **Stage and amend:**
   - Stage everything: `git add -A`
   - Amend with no message change:
     ```bash
     git commit --amend --no-edit
     ```
   - Or amend with a new message using a HEREDOC:
     ```bash
     git commit --amend -F /dev/stdin << 'EOF'
     TICKET-ID Subject line

     Optional body with more detail.
     EOF
     ```

### Rules
- **ALWAYS use `--amend`** - never create a new commit
- Never skip pre-commit hooks (`--no-verify`)
- Never stage files that likely contain secrets (.env, credentials.json, etc.)
- Do NOT push - only amend
