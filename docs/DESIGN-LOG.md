# Design Log

> **Session handoff.** This file is the durable memory of the design discussion, written so a fresh
> Claude Code session (opened from this folder) can resume with full context. Claude's built-in
> memory is scoped per-project-directory and does NOT carry across folders — this file is the bridge.
> `CLAUDE.md` points here so it is discovered on session start.
>
> **Working mode:** we are in *discussion mode*. Do NOT lock decisions or start building until the
> user gives an explicit signal ("on décide", "go"). Pitch the minimal solution first; defer config
> systems / DSLs / automation until asked. Reply in French (user's working language).

---

## 1. The repo

- **github.com/gfoo/gfoo-agent-skills** (public), cloned at `~/work/perso/gfoo-agent-skills`.
- Created 2026-06-07. Initially `gfoo-claude-marketplace`; renamed once we confirmed skills are
  cross-tool portable.
- Git identity: `Gilles Faucherand <gilles.faucherand@gmail.com>`, applied automatically via an
  `includeIf "gitdir:~/work/perso/"` in `~/.gitconfig` (work repos keep the UNIL email).
- `gh` defaults to host `github.unil.ch` on this machine — use `GH_HOST=github.com` for this repo.

## 2. Positioning — tool-neutral Agent Skills collection

Skills are the **source of truth**; each AI agent gets a thin **distribution adapter** on top
(never a copy of the skill content).

| Agent | Adapter | Status |
|-------|---------|--------|
| Claude Code | `.claude-plugin/marketplace.json` + `plugins/<name>/.claude-plugin/plugin.json` | scaffolded |
| Pi | `package.json` (`"pi"` key → skill dirs + `pi-package` keyword) | add with first skill |
| OpenCode / Codex CLI / Gemini CLI | read skill folders by convention, no manifest | works as-is |

### Why this is possible (research, 2026-06-07)

There is an **open "Agent Skills" standard**, originated from Anthropic's `SKILL.md` for Claude Code:
a directory + `SKILL.md`, YAML frontmatter with required `name` + `description`, markdown
instructions, optional `scripts/` / `references/` / `assets/`, progressive disclosure.
Supported by Claude Code, **Pi**, OpenCode, Codex CLI, Gemini CLI, GitHub Copilot, Cursor.
**Pi** = real terminal coding agent (Mario Zechner / earendil-works, MIT, model-agnostic); its skill
frontmatter (`name`+`description`) is identical to Claude Code's.

**Portability rules (also in CLAUDE.md):** keep frontmatter to core fields (`name`, `description`,
maybe `when_to_use` / `allowed-tools`); plain markdown + scripts/references/assets; do NOT depend on
Claude-only primitives (hooks, sub-agents, `Task`) in a shared skill. Prefer stdlib-only scripts so
both Claude and Pi invoke them the same way.

Sources: agensi.io (Agent Skills open standard + SKILL.md spec), code.claude.com/docs/skills,
platform.claude.com agent-skills, pi.dev/docs skills, github.com/badlogic/pi-mono,
github.com/PSPDFKit-labs/pi-skills, opencode.ai/docs/skills.

## 3. First skill under design: a memory / knowledge-management system

Accumulated decisions (NOT final — discussion mode):

- **Name** leaning **`memory-system`**. Rationale: "memory" is the container (episodic + semantic);
  "knowledge" / `kb` is just one layer inside it. (`gf-kb` was an earlier candidate, dropped.)
- **Two stores (Kahneman System 1 / System 2):**
  - *S1 — hot:* always-loaded compact index + one-line hooks; automatic recognition; ~free (already
    in context). Like today's `MEMORY.md`.
  - *S2 — cold:* full corpus, full files, discussion threads, `[[links]]`; retrieved deliberately;
    costs tokens + time. Two **distinct storages** (confirmed by user), with the hot layer being an
    index/view over the cold corpus (open question: regenerated vs separately written).
- **Two content types:**
  - *Facts* — atomic, frozen, semantic ("1 fact = 1 file"); write-once, replace on change.
  - *Discussions / decision logs* — evolving, episodic, append-only, can stay **open** then **closed**.
    Need an explicit open/closed state (avoid orphan threads — same trap as `conditional` items
    without a review date).
- **Skills by verb**, pivot keyword **"connaissance" / "knowledge"**, **multilingual** (FR / EN / +).
  Trigger by `description` matching, not rigid slash commands. Verbs route to ops:
  write (note/retiens), search (cherche/retrouve), read (lis/montre), forget (oublie/supprime).
  Open question: should search be cross-language (query "authentification" finds a note written
  "authentication")?
- **Search dial — parsimony ↔ exhaustiveness** = effectively the **S1→S2 switch**. Tentative name
  for the knob: **"ouverture"** (camera-aperture metaphor: gives a natural scale fermée/medium/grande
  + an implicit cap). Needs an **anti-flood token cap** on the exhaustive pole. Open: global default
  vs per-call param vs both.
- **Confirmation policy — graded by verb** (not uniform "always ask"):
  - delete/forget → **always confirm** (destructive, hard to undo)
  - write → **propose / validate** (lightweight: "je note ceci: … ok?")
  - read / search → **silent** (read-only, no side effects)
- **The S1→S2 gateway is the real design crux:** how S1 knows it must wake S2. Today that's the
  `description` hook in frontmatter. More important than the write/read ops themselves.
- **Caveat to bake in:** a recalled memory reflects what was true when written — verify before
  relying. S2's job is to *correct* S1 (anchoring/bias risk), not just complete it.

## 4. Open questions (not yet decided)

- Substrate: does `memory-system` tool the native memory mechanism, use a dedicated store, or hybrid?
- Scope: global (cross-project) vs per-project vs both levels?
- Trigger mechanism details: pure description-based auto-trigger, or also explicit `/skill` commands?
- Hot/cold storage: two physical stores, or one corpus + a generated hot index?
- Cross-language search behavior.
- Whether "ouverture" is the final name for the retrieval dial.

## Changelog

- 2026-06-07 — Initial design log created (session handoff before moving work into this repo).
