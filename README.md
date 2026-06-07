# gfoo-agent-skills

A tool-neutral collection of **Agent Skills** — portable `SKILL.md` capability packages that
work across multiple AI coding agents, not just one.

Skills conform to the open [Agent Skills standard](https://www.agensi.io/learn/agent-skills-open-standard)
(originated from Anthropic's `SKILL.md` for Claude Code): a directory with a `SKILL.md`, YAML
frontmatter (`name` + `description`), markdown instructions, and optional `scripts/` / `references/`
/ `assets/`. Skills that stick to this core format run unmodified on Claude Code, Pi, OpenCode,
Codex CLI, Gemini CLI, and others.

## Design principle: one source, many distributors

The skills are the source of truth. Each agent gets a thin **distribution adapter** on top — never
a copy:

| Agent | Adapter | Status |
|-------|---------|--------|
| Claude Code | `.claude-plugin/marketplace.json` (+ `/plugin install`) | present |
| Pi | `package.json` (`"pi"` key + `pi-package` keyword) | added with first skill |
| OpenCode / Codex / Gemini | read skill folders directly (no manifest) | works via convention |

## Structure

```
.claude-plugin/
  marketplace.json              # Claude Code distribution adapter
plugins/
  <plugin-name>/
    .claude-plugin/plugin.json  # Claude plugin manifest
    skills/<skill>/SKILL.md      # the portable skills (standard format)
```

## Available Skills

_None yet — the first is a memory / knowledge-management system, under design._

## Quick Start (Claude Code)

```bash
/plugin marketplace add https://github.com/gfoo/gfoo-agent-skills.git
/plugin install <plugin-name>
```

See `CLAUDE.md` for the cross-tool packaging conventions.
