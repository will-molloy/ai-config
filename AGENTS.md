# ai-config

Markdown-only repo - no build, no tests, no dependencies.

## Structure

- `skills/` - slash command skills (one directory per skill, each containing `SKILL.md`)
- `global/` - shareable global agent configuration

## Rules

- Everything here should be genuinely reusable and battle-tested - no ad-hoc or project-specific stuff.
- All content must be agent-agnostic - no references to specific AI tools or models.
- All content must be scrubbed of company-specific references before committing.
- Skills should cross-reference each other where it avoids duplication (e.g. `/amend` references `/commit` for conventions).
- Keep skills cheap - rely on tool output (e.g. `git diff`) rather than reading files when possible.
