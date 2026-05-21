# Claude Marketplace — Developer Guide

## Plugin Structure

Each plugin lives in `plugins/<name>/` with this layout:

```
plugins/<name>/
  .claude-plugin/
    plugin.json         ← manifest (official schema)
  skills/
    <name>/
      SKILL.md          ← uppercase, with frontmatter
  commands/             ← flat .md files (for command plugins)
    foo.md
  README.md
```

## plugin.json Schema

Only `name` is required. All other fields optional.

```json
{
  "name": "plugin-name",
  "displayName": "Human Name",
  "version": "1.0.0",
  "description": "Brief description",
  "author": {
    "name": "Author Name",
    "email": "author@example.com",
    "url": "https://github.com/author"
  },
  "homepage": "https://...",
  "repository": "https://github.com/...",
  "license": "MIT",
  "keywords": ["keyword1", "keyword2"]
}
```

**Do NOT use**: `type`, `category`, `triggers`, `entryPoint`, `tags`, `owner`, `source`
Fields with wrong TYPE fail to load. Unknown fields are silently ignored.

### Component path fields (add only when needed)

```json
{
  "skills": "./custom/skills/",
  "commands": ["./commands/foo.md"],
  "hooks": "./hooks/hooks.json",
  "agents": ["./agents/reviewer.md"]
}
```

- `skills` ADDS to the default `skills/` scan
- `commands` REPLACES the default `commands/` scan
- Omitting these uses auto-discovery from default locations

## SKILL.md Format

Must be `SKILL.md` (uppercase). Frontmatter required:

```markdown
---
name: skill-name
description: One-line description of what the skill does and when to use it
---

# Skill Title

Content here...
```

Optional frontmatter fields: `triggers`, `context`, `license`

## Command File Format

Flat `.md` file in `commands/`:

```markdown
---
description: One-line description shown in /help
allowed-tools: Bash(git:*), Read
---

Command instructions here. Use $ARGUMENTS for user input.
```

## marketplace.json Schema

Lives at `.claude-plugin/marketplace.json`. Plugin entries use `git-subdir` source type:

```json
{
  "$schema": "https://anthropic.com/claude-code/marketplace.schema.json",
  "name": "marketplace-name",
  "displayName": "Human Name",
  "description": "...",
  "version": "1.0.0",
  "owner": { "name": "...", "email": "..." },
  "repository": "https://github.com/...",
  "plugins": [
    {
      "name": "plugin-name",
      "displayName": "Human Name",
      "description": "...",
      "author": { "name": "..." },
      "category": "skills",
      "source": {
        "source": "git-subdir",
        "url": "https://github.com/org/repo.git",
        "path": "plugins/plugin-name",
        "ref": "main"
      }
    }
  ]
}
```

## Auto-Discovery Rules

Claude Code auto-discovers (no manifest needed):
- Skills: `skills/<name>/SKILL.md` in each plugin dir
- Commands: `commands/*.md` in each plugin dir
- Single skill at root: `SKILL.md` at plugin root (v2.1.142+)

## Validation

```bash
claude plugin validate ./plugins/<name>
claude plugin validate ./plugins/<name> --strict  # warnings as errors
```

## Adding a New Skill Plugin

1. `mkdir -p plugins/<name>/.claude-plugin plugins/<name>/skills/<name>`
2. Write `plugins/<name>/.claude-plugin/plugin.json` (schema above)
3. Write `plugins/<name>/skills/<name>/SKILL.md` (with frontmatter)
4. Write `plugins/<name>/README.md`
5. Add entry to `.claude-plugin/marketplace.json` with `git-subdir` source
6. `/validate` → commit → push

## Adding a New Command Plugin

1. `mkdir -p plugins/<name>/.claude-plugin plugins/<name>/commands`
2. Write `plugins/<name>/.claude-plugin/plugin.json`
3. Write `plugins/<name>/commands/<cmd>.md` (with frontmatter)
4. Write `plugins/<name>/README.md`
5. Add to marketplace.json
6. Commit → push

## Known Working Source

Docs: https://code.claude.com/docs/en/plugins-reference
