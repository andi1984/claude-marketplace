# Create New Skill

Create a new skill plugin from scratch.

## Instructions

1. Ask the user for:
   - Skill name (kebab-case)
   - Display name
   - Description of what the skill does
   - Trigger keywords/phrases

2. Create the skill directory at `plugins/<skill-name>/`

3. Generate the skill files:
   - `manifest.json` - Skill metadata and configuration
   - `skill.md` - The skill prompt template
   - `README.md` - Documentation

4. Register the skill in `.claude-plugin/marketplace.json`

$ARGUMENTS
