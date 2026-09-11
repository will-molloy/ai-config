# ai-config

Markdown-only repo - no build, no tests, no dependencies.

## Structure

- `skills/` - slash command skills (one directory per skill, each containing `SKILL.md`)
- `global/` - shareable global agent configuration

## Rules

- Everything here should be genuinely reusable and battle-tested - no ad-hoc or project-specific stuff.
- Written primarily for Claude Code. Keep it high level - describe the intent rather than
  naming a specific tool, language, or MCP server, unless the specific is the point.
- All content must be scrubbed of company-specific references before committing.
- Skills should cross-reference each other where it avoids duplication (e.g. `/amend` references `/commit` for conventions).
