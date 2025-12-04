# SmartIce Plugin Marketplace

This repository contains Claude Code plugins for the SmartIce marketplace.

## Project Structure

```
plugins/
├── design-council/          # Multi-model frontend design workflow
│   ├── agents/
│   │   ├── design-strategist.md   # Creates design specs, interviews user
│   │   ├── opus-reviewer.md       # Reviews generated code for quality
│   │   └── adaptation-advisor.md  # Prepares guidance for failed iterations
│   ├── commands/
│   │   └── design-sprint.md       # Main orchestration command
│   └── skills/
│       └── design-orchestration/  # Design principles and guidelines
├── db-tools/                # Database review tools
└── smartice-tools/          # Marketplace submission tools
```

## Design-Council Plugin

### Workflow

1. **Design Sprint Command**: `/design-council:design-sprint "description" --rounds=3 --format=react`
2. **Staging Directory**: All generated code goes to `./.design-sprint-staging/round-N/`
3. **Color Palette Preview**: Designer creates `color-palette-preview.html` for user confirmation

### Output Formats

| Format | Use Case |
|--------|----------|
| `html` | Quick demos, design approval (single file, no build) |
| `react` | Production React components |
| `vue` | Vue 3 composition API |
| `svelte` | Svelte components |
| `nextjs` | Next.js App Router |

### Testing Changes

After modifying plugin files, sync to installed location:
```bash
cp -r plugins/design-council/* ~/.claude/plugins/marketplaces/smartice-plugin-market/plugins/design-council/
```

Then restart Claude Code to pick up changes.

### Key Files

- `plugins/design-council/scripts/gemini-generate.py` - Calls Gemini API for code generation
- Requires `GEMINI_API_KEY` environment variable

## Development Notes

- Agent namespaces: Use full names like `design-council:design-strategist`
- Staging directory pattern ensures reviewer reads fresh files, not cached content
- Color palette preview lets users confirm colors before expensive code generation

## Weather Dashboard Example

Test output in `weather-dashboard/` - a Vite React project with retro-futuristic styling.
Run with `cd weather-dashboard && npm run dev`.
