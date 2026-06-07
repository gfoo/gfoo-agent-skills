# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

A Claude Code **plugin marketplace** — a git-hosted catalog that distributes plugins. There is no source code to build or test.

## Structure

```
.claude-plugin/
  marketplace.json           # Marketplace catalog (lists all plugins)
plugins/
  <plugin-name>/
    .claude-plugin/
      plugin.json            # Plugin manifest (name, version)
    skills/                  # Skill modules (SKILL.md + assets/ + references/)
    templates/               # Optional shared templates
    workflow.md              # Optional user-level rules
```

## Adding a New Plugin

1. Create `plugins/<plugin-name>/.claude-plugin/plugin.json`:
   ```json
   {"name": "<plugin-name>", "version": "1.0.0", "description": "..."}
   ```
2. Add skills under `plugins/<plugin-name>/skills/<skill-name>/SKILL.md`
3. Register in `.claude-plugin/marketplace.json`:
   ```json
   {"name": "<plugin-name>", "source": "./plugins/<plugin-name>", "description": "..."}
   ```
4. Commit, bump version, push.

## Skill Anatomy

```
skills/<skill-name>/
  SKILL.md             # Required — frontmatter (name, description, user-invokable) + body
  assets/              # Optional — output templates referenced via @path
  references/          # Optional — metric definitions, checklists, rubrics
  scripts/             # Optional — executable helpers (Python stdlib-only preferred, no pip deps)
```

Scripts must be invoked via `${CLAUDE_PLUGIN_ROOT}/skills/<skill-name>/scripts/...` so the path
resolves regardless of how the plugin was installed (marketplace, local link, dev checkout).

## Versioning

Each plugin has its own version in its `plugin.json`. **Every push to `main` MUST include a version bump:**

1. Bump the version in the modified plugin's `plugin.json`
2. Update the version column in the root `README.md` plugins table
3. Commit: `chore(<plugin-name>): bump version to N.0.0`
4. Push

## Key Conventions

- Skills are **read-only auditors** or **document generators** — they never modify source code in consumer projects unless that is their explicit purpose.
- Generated documents include a generation date header and a changelog footer.
- `workflow.md` is NOT loaded by the plugin system — users must manually copy it to `~/.claude/rules/`.
