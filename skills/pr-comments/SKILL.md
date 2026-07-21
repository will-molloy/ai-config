---
name: pr-comments
description: |
  Review unresolved PR comments and create a task list to address them.
  Use when asked to "review PR comments", "check PR feedback", or "address review comments".
allowed-tools:
  - Bash
  - Read
  - AskUserQuestion
---

## Instructions

Given a PR number (passed as `$ARGUMENTS`, or inferred from the current branch), review all **unresolved** review threads and produce a task list.

### Steps

1. **Identify the PR.** If `$ARGUMENTS` contains a PR number, use it. Otherwise, detect from the current branch:
   ```bash
   gh pr list --head "$(git branch --show-current)" --json number --jq '.[0].number'
   ```

2. **Fetch unresolved threads.** Use the GraphQL API to get only unresolved review threads with all replies:
   ```bash
   OWNER_REPO=$(gh repo view --json nameWithOwner --jq '.nameWithOwner')
   gh api graphql -f query='
   {
     repository(owner: "OWNER", name: "REPO") {
       pullRequest(number: PR_NUMBER) {
         reviewThreads(first: 50) {
           nodes {
             isResolved
             comments(first: 10) {
               nodes {
                 author { login }
                 body
                 path
                 line
               }
             }
           }
         }
       }
     }
   }' --jq '.data.repository.pullRequest.reviewThreads.nodes[] | select(.isResolved == false)'
   ```
   Replace OWNER, REPO, and PR_NUMBER with actual values.

3. **Categorise each thread** as one of:
   - **Code change** — reviewer wants something changed in the code
   - **Question** — reviewer is asking for clarification (needs a reply)
   - **Discussion** — open-ended, no action required yet

4. **Present the task list.** For each unresolved thread, show:
   - File and line number
   - Reviewer name
   - Comment summary (one line)
   - Category (code change / question / discussion)
   - Your recommendation on how to address it

5. **Work through tasks.** Use `AskUserQuestion` if unsure how the user wants to handle a specific comment. For code changes, make the fix. For questions/discussion, draft a reply for the user to review.

### Rules
- Include ALL unresolved comments — both human and bot reviewers
- Never reply to reviewers on the user's behalf — draft replies and let the user post them
- Don't address already-resolved threads
