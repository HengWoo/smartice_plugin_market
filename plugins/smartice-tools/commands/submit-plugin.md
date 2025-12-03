---
description: Submit your plugin to the SmartIce marketplace via automated PR
argument-hint: <path-to-plugin>
allowed-tools: ["Read", "Write", "Bash", "Glob", "Grep", "Edit"]
---

# Submit Plugin to SmartIce Marketplace

You are helping a user submit their Claude Code plugin to the SmartIce Plugin Marketplace.

## Input
- Plugin path: $ARGUMENTS

## Workflow

### Step 1: Validate Plugin Path
Check that the provided path exists and contains a valid plugin:

```bash
ls -la "$ARGUMENTS"
ls -la "$ARGUMENTS/.claude-plugin/plugin.json"
```

If path doesn't exist or plugin.json is missing, ask user to provide correct path.

### Step 2: Read and Validate Plugin Manifest
Read the plugin.json and verify required fields:

```bash
cat "$ARGUMENTS/.claude-plugin/plugin.json"
```

Required fields:
- `name` (kebab-case)
- `version` (semver)
- `description`
- `author.name`

If missing fields, help user fix them before proceeding.

### Step 3: Check Plugin Structure
Verify the plugin has proper structure:
- At least one of: skills/, commands/, agents/, hooks/, or root SKILL.md
- README.md is recommended

Report what components were found.

### Step 4: Get Plugin Metadata from User
Ask user to confirm or provide:
- **Category**: design, development, testing, documentation, integration, productivity
- **Keywords**: 3-5 relevant keywords
- **Description override**: Use plugin.json description or provide custom marketplace description?

### Step 5: Fork Marketplace (if needed)
Check if user already has a fork:

```bash
gh repo view HengWoo/smartice_plugin_market --json isFork 2>/dev/null || echo "no fork"
```

If no fork exists, create one:

```bash
gh repo fork HengWoo/smartice_plugin_market --clone=false
```

### Step 6: Clone Fork to Temp Directory
```bash
TEMP_DIR=$(mktemp -d)
gh repo clone $(gh api user --jq '.login')/smartice_plugin_market "$TEMP_DIR/smartice_plugin_market"
cd "$TEMP_DIR/smartice_plugin_market"
```

### Step 7: Create Feature Branch
```bash
PLUGIN_NAME=$(cat "$ARGUMENTS/.claude-plugin/plugin.json" | python3 -c "import sys,json; print(json.load(sys.stdin)['name'])")
git checkout -b "add-plugin-$PLUGIN_NAME"
```

### Step 8: Copy Plugin to Marketplace
```bash
cp -r "$ARGUMENTS" "$TEMP_DIR/smartice_plugin_market/plugins/$PLUGIN_NAME"
```

### Step 9: Update marketplace.json
Read the current marketplace.json and add the new plugin entry:

```bash
cat "$TEMP_DIR/smartice_plugin_market/.claude-plugin/marketplace.json"
```

Add new entry to the plugins array with:
- name (from plugin.json)
- source: "./plugins/[plugin-name]"
- description
- version
- category (from user input)
- keywords (from user input)

Use the Edit tool to update marketplace.json.

### Step 10: Commit and Push
```bash
cd "$TEMP_DIR/smartice_plugin_market"
git add .
git commit -m "Add plugin: $PLUGIN_NAME"
git push -u origin "add-plugin-$PLUGIN_NAME"
```

### Step 11: Create Pull Request
```bash
gh pr create \
  --repo HengWoo/smartice_plugin_market \
  --title "Add plugin: $PLUGIN_NAME" \
  --body "## New Plugin Submission

**Plugin:** $PLUGIN_NAME
**Version:** [version]
**Category:** [category]

### Description
[plugin description]

### Components
- [ ] Skills
- [ ] Commands
- [ ] Agents
- [ ] Hooks

### Checklist
- [x] Plugin has valid plugin.json
- [x] Plugin has README.md
- [x] No secrets or API keys included
"
```

### Step 12: Report Success
Tell the user:
- PR was created successfully
- Provide the PR URL
- Explain that the marketplace maintainer will review and merge
- Remind them to clean up temp directory if desired

### Step 13: Cleanup
```bash
rm -rf "$TEMP_DIR"
```

## Error Handling

- If `gh` CLI is not authenticated, guide user to run `gh auth login`
- If plugin validation fails, explain what's wrong and how to fix
- If PR creation fails, provide the error and suggest manual steps

## Example Usage

```
/smartice:submit-plugin ~/my-plugins/awesome-tool
```
