---
name: check-review
description: |
  Sanity-check the user's PR review - catch wrong claims and important misses.
  Use when asked to "check my review" or "does my review look ok".
allowed-tools:
  - Bash
  - Read
---

# Check review

Checks that the user's review comments are correct and that nothing important in the PR
is missed. Output goes to chat only. Never post, edit or submit the review.

## Inputs

- PR URL or number. If none is given, use the PR already discussed in the conversation.

## Steps

1. Fetch the user's review comments.
   - Prefer their pending review.
   - If there's no pending review, use their most recently submitted review, and say so.
2. Read the PR diff and description, unless they're already in context.
3. Check each comment:
   - **Correct?** The claim matches the code.
   - **Clear?** The comment says what to change and why, with no wrong or needless hedges.
4. Find the issues the user's review misses.
   - If the conversation already holds an agent review of this PR, compare against that
     instead of reviewing again. Re-check it if the PR has new commits since.
   - Otherwise, review the diff independently.

## Output

- One-line verdict first.
- Comments that need a change, each with the reason and a suggested rewording.
- Misses, most important first, each in one line.
- Don't list comments that are fine.
