# Add Plugin to Marketplace

Add a new plugin to this marketplace. The user will provide the plugin details.

## Instructions

1. Ask the user for the plugin information:
   - Plugin name (kebab-case, e.g., `my-cool-plugin`)
   - Display name (human-readable)
   - Description
   - GitHub repository URL (or local path)
   - Entry point (default: `index.js` or `plugin.js`)
   - Type: `skill` or `command`

2. Create the plugin directory structure at `plugins/<plugin-name>/`

3. Update the `.claude-plugin/marketplace.json` to include the new plugin

4. If it's a skill, create the skill manifest
5. If it's a command, create the command markdown file

$ARGUMENTS
