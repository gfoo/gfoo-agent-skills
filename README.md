# gfoo-claude-marketplace

A Claude Code **plugin marketplace** — a git-hosted catalog that distributes plugins.

Multi-plugin architecture: each plugin lives in `plugins/<name>/` with its own skills, templates, and manifest.

## Structure

```
.claude-plugin/
  marketplace.json              # Marketplace catalog (lists all plugins)
plugins/
  <plugin-name>/
    .claude-plugin/plugin.json  # Plugin manifest (name, version)
    skills/                     # Skill modules
    templates/                  # Optional shared templates
    workflow.md                 # Optional user-level rules
```

To add a new plugin, create `plugins/<name>/` with a `.claude-plugin/plugin.json` and register it in `marketplace.json`. See `CLAUDE.md` for details.

## Available Plugins

_None yet — the first plugin (a memory / knowledge-management system) is under design._

## Quick Start

```bash
# Add the marketplace
/plugin marketplace add https://github.com/gfoo/gfoo-claude-marketplace.git

# Install a plugin
/plugin install <plugin-name>
```

See each plugin's README for full documentation.
