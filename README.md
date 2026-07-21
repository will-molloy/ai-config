# ai-config

Config for AI coding agents.

> Written for [Claude Code](https://docs.anthropic.com/en/docs/claude-code) but the markdown is agent-agnostic - adapt for any tool that accepts system prompts or instruction files.

## Skills

### Git

| Skill            | Description                                              |
|------------------|----------------------------------------------------------|
| `/commit`        | Stage all changes and commit with a detailed message     |
| `/amend`         | Stage all changes and amend the previous commit          |
| `/push`          | Stage, commit, and push in one step                      |
| `/merge-main`    | Merge or cherry-pick rebase main into the current branch |
| `/clean-commits` | Rewrite branch history into logical commits              |

### Pull Requests

| Skill            | Description                                              |
|------------------|----------------------------------------------------------|
| `/open-pr`       | Create a draft PR for the current branch                 |
| `/update-pr`     | Update an existing PR description to match the branch    |
| `/pr-comments`   | Review unresolved PR comments and create a task list     |

### Testing

| Skill            | Description                                              |
|------------------|----------------------------------------------------------|
| `/mutation-test` | Run mutation testing to find test gaps                   |

### Third-party

| Skill repo                                   | Description                          |
|----------------------------------------------|--------------------------------------|
| [gstack](https://github.com/garrytan/gstack) | Code review, QA, design review, etc. |

### Install

Ask your agent to symlink or copy the skills you want (e.g. into `~/.claude/skills/`).
