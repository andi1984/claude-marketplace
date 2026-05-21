# andi1984's Claude Marketplace

Personal Claude Code marketplace for custom plugins, skills, and commands.

## Install

```bash
claude marketplace add https://github.com/andi1984/claude-marketplace
```

## Plugins

### Skills (auto-activate on trigger words)

| Plugin | Triggers | Description |
|--------|----------|-------------|
| **lua** | `lua`, `love2d` | Lua dev: tables, metatables, OOP, Love2D, performance |
| **make-automation** | `make`, `make.com`, `scenario`, `blueprint`, `webhook` | Make.com scenarios with MCP tools, blueprint JSON, cost optimization |
| **supabase-dev** | `supabase`, `rls`, `edge function` | RLS policies, migrations, edge functions, auth, TypeScript types |

### Commands (slash commands)

| Command | Description |
|---------|-------------|
| `/portfolio` | Quick portfolio overview — value, return, top holdings (Parqet MCP) |
| `/portfolio-performance [period]` | Detailed XIRR/TTWROR breakdown by period (Parqet MCP) |

## Marketplace Management Commands

| Command | Description |
|---------|-------------|
| `/add-plugin` | Add a new plugin interactively |
| `/new-skill` | Scaffold a new skill plugin |
| `/list-plugins` | List all registered plugins |
| `/validate` | Validate all plugin configs and registry |

## Structure

```
.
├── .claude-plugin/
│   └── marketplace.json      # Plugin registry
├── .claude/
│   ├── settings.json
│   └── commands/             # Marketplace management commands
├── plugins/
│   ├── lua/
│   ├── make-automation/
│   ├── supabase-dev/
│   └── parqet-portfolio/
└── README.md
```

## Adding a Plugin

```bash
# Interactive scaffold
/new-skill

# Or manually:
mkdir plugins/my-plugin
# create manifest.json + skill.md + README.md
# register in .claude-plugin/marketplace.json
/validate
```
