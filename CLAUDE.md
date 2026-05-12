# CLAUDE.md — claude-code-wrap-up-skill

@AGENTS.md

> Claude Code reads this file (not `AGENTS.md` directly). The `@AGENTS.md` line above imports it. This repo dogfoods the `wrap-up` plugin — these files exist because the plugin enforces them.

## Current phase

Phase 3 — all four project files (`_tracker.md`, `_prd.md`, `AGENTS.md`, `CLAUDE.md`) are mandatory in `/wrap-up`; the skills are self-contained (starter content inlined as Appendices A–D in `skills/wrap-up/SKILL.md`); the repo dogfoods its own convention. See `_prd.md` for the phase map.

## Claude-specific notes

- **On any behavior change to a skill:** also bump `version` in `.claude-plugin/plugin.json`, update the README ("What `/wrap-up` does" list + the behavior table), and add a one-liner to `_tracker.md`.
- **The starter file bodies have one home:** Appendices A–D in `skills/wrap-up/SKILL.md`. `skills/init-project/SKILL.md` references them. Never fork them into a second location.
- **No code.** This is a pure-Markdown plugin — no JS, no hooks, no MCP server. Keep it that way.
- **Run `/wrap-up` at the end of a session here** — this repo eats its own dog food.
- Preferred model: whatever the user is on; nothing in this repo needs a specific model.
