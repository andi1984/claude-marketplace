# claude-marketplace

Personal Claude Code marketplace for custom plugins, skills, and commands.

## Structure

```
.
├── .claude/
│   ├── settings.json      # Project-level Claude Code settings
│   └── commands/          # Custom slash commands
│       ├── add-plugin.md
│       ├── list-plugins.md
│       ├── new-skill.md
│       └── validate.md
├── .claude-plugin/
│   └── marketplace.json   # Marketplace registry
└── plugins/               # Plugin directories
```

## Commands

- `/add-plugin` - Add a new plugin to the marketplace
- `/list-plugins` - List all available plugins
- `/new-skill` - Create a new skill from scratch
- `/validate` - Validate marketplace configuration

## Adding Plugins

Use `/add-plugin` or manually create a plugin directory under `plugins/` and register it in `.claude-plugin/marketplace.json`.
