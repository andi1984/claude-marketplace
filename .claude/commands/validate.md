---
allowed-tools: Bash(cat:*), Bash(ls:*), Bash(find:*), Read
description: Validate marketplace configuration and all plugins
---

Validate this marketplace. Run these checks and report pass/fail for each.

## Checks to Run

### 1. Registry valid JSON
Read `.claude-plugin/marketplace.json` and verify it parses as valid JSON.

### 2. All registered plugins exist on disk
For each plugin in `marketplace.json[].plugins[].path`, verify that directory exists.

### 3. Each plugin has required files
For each plugin directory, check:
- `manifest.json` exists and is valid JSON
- Entry point file exists (`entryPoint` from manifest, or `skill.md` for skills, or at least one `.md` in `commands/` for commands)
- `README.md` exists

### 4. Manifest required fields
Each `manifest.json` must have: `name`, `displayName`, `description`, `version`, `type`, `owner`

### 5. No orphaned plugin directories
List all directories in `plugins/` and verify each is registered in `marketplace.json`.

### 6. Version consistency
Confirm `manifest.json` version format is semver (`x.y.z`).

## Output Format

Print a table:

| Check | Plugin | Status | Detail |
|-------|--------|--------|--------|
| registry JSON | — | ✓ | |
| plugin exists | lua | ✓ | |
| required files | lua | ✓ | |
| manifest fields | lua | ✗ | missing: owner |
| orphaned dirs | — | ✓ | |

Then a summary line: `N checks passed, M failed`.

If all pass: `Marketplace valid. N plugins registered.`
If failures: list each fix needed.
