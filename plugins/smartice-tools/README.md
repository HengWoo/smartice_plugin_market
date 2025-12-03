# SmartIce Tools

Utilities for contributing plugins to the SmartIce Plugin Marketplace.

## Installation

```
/plugin marketplace add HengWoo/smartice_plugin_market
/plugin install smartice-tools@smartice-plugin-market
```

## Commands

### /smartice-tools:submit-plugin

Submit your plugin to the marketplace with an automated PR.

**Usage:**
```
/smartice-tools:submit-plugin ./path/to/your-plugin
```

**What it does:**
1. Validates your plugin structure
2. Forks the marketplace repo (if needed)
3. Copies your plugin to the fork
4. Updates marketplace.json
5. Creates a Pull Request automatically

**Requirements:**
- GitHub CLI (`gh`) installed and authenticated
- Valid plugin with `.claude-plugin/plugin.json`

## Prerequisites

Make sure you're authenticated with GitHub CLI:
```bash
gh auth login
```
