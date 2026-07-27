# ai-config

Config for AI coding agents.

> Written for [Claude Code](https://docs.anthropic.com/en/docs/claude-code) but the markdown is agent-agnostic - adapt for any tool that accepts system prompts or instruction files.

## Global config

### Install

Copy `global/AGENTS.md` into your agent's config directory (e.g. `~/.claude/CLAUDE.md`).

(Optional) Fill in the Tech and Domain sections with a high-level description that applies globally (project-specific details belong in each repo's own config).

## Skills

### Install

Symlink the skills you want into your agent's skills directory (e.g. `~/.claude/skills/`).

### Git

| Skill            | Description                                              |
|------------------|----------------------------------------------------------|
| `/commit`        | Stage all changes and commit with a detailed message     |
| `/amend`         | Stage all changes and amend the previous commit          |
| `/push`          | Stage, commit, and push in one step                      |
| `/rebase`        | Rebase a branch onto its base branch                     |
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

| Skill repo                                   | Description                            |
|----------------------------------------------|----------------------------------------|
| [gstack](https://github.com/garrytan/gstack) | `/browse`, `/qa`, `/review`, and more. |
