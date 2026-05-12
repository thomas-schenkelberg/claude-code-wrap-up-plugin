# PRD: claude-code-wrap-up-plugin

> Product requirements for the `wrap-up` Claude Code plugin. Updated when scope or architecture changes — not for routine wording tweaks.

## Problem

End-of-session hygiene is easy to skip: people forget to commit and push, projects drift without a changelog or a spec, and coding agents start each session guessing because there's no `AGENTS.md` / `CLAUDE.md`. New Claude Code users especially have no project-doc discipline yet. A single, low-friction "wrap up the session" command fixes all of this — and a "set up the project" command seeds the files it relies on.

## Users

- Claude Code users who want a consistent end-of-session routine without having to remember the steps.
- Specifically: AI Power Hour clients onboarding onto Claude Code — they get the plugin so the discipline comes built in.
- Works in the Claude Code CLI, the VS Code extension, and (via a manual copy into `~/.claude/skills/`) managed agents like Cowork. Works whether the user has one project or fifty.

## Scope

### In scope

- The `/wrap-up` skill: commit & push touched repos; ensure + update `_tracker.md`, `_prd.md`, `AGENTS.md`, `CLAUDE.md`; review session for memory writes.
- The `/init-project` skill: interactively seed those same four files in a new project; never overwrite.
- The four starter file bodies (inlined as Appendices A–D in `skills/wrap-up/SKILL.md`).
- Install / update / uninstall documentation; "works in a single project folder" and "manual install" notes.

### Out of scope (explicitly)

- CI / automated tests for the plugin itself.
- Running the consuming project's test suite or deploy (left as documented opt-in customizations, not built in).
- Managing other agents' own config formats beyond writing a generic `AGENTS.md`.
- Any runtime — no JS, no hooks, no MCP server.

## Success criteria

- A user installs once and, from then on, every project they run `/wrap-up` in ends up with a maintained `_tracker.md`, `_prd.md`, `AGENTS.md`, and `CLAUDE.md`, and their work is committed + pushed.
- The skills work with zero path configuration in every environment they're plausibly installed into (plugin install, manual `~/.claude/skills/` copy, managed agent).
- Someone who pastes the GitHub URL into Claude Code / Cowork gets, from the README alone, an accurate picture of what the plugin is, what it does for them, and how it's structured.

## Architecture

- **Plain-Markdown skills**, no runtime. Claude reads `SKILL.md` and executes it as a procedure. `disable-model-invocation: true` on both — they only run on the explicit slash command.
- **`.claude-plugin/marketplace.json`** — marketplace catalog (one plugin entry, `source: ./`). **`.claude-plugin/plugin.json`** — plugin manifest; the semver `version` lives here and is how installed users know to update.
- **Distribution**: GitHub repo `tschenkster/claude-code-wrap-up-plugin` acts as the marketplace; users `add` it then `install wrap-up@wrap-up`. (Repo renamed from `claude-code-wrap-up-skill` on 2026-05-12; GitHub redirects the old URL.)
- **Self-contained skills**: the four starter file bodies are inlined into `skills/wrap-up/SKILL.md` (Appendices A–D); `skills/init-project/SKILL.md` references them. No external files to locate.
- **Dogfooding**: this repo carries its own `_tracker.md`, `_prd.md`, `AGENTS.md`, `CLAUDE.md` at the root.

## Data model

Not applicable — there is no data store. The "entities" are the four project files the plugin maintains and the two plugin manifests.

## Phases

- **Phase 1 — done:** `/wrap-up` shipped (v1.0.0): commit/push + optional tracker/PRD/AGENTS/CLAUDE updates + memory review.
- **Phase 2 — done:** `/init-project` + starter templates (v1.1.0); tracker + PRD made mandatory (v1.2.0).
- **Phase 3 — current:** `AGENTS.md` + `CLAUDE.md` also mandatory; starters inlined; `templates/` removed; repo dogfoods; README states plugin-vs-skill and the user benefits (v1.3.0).
- **Phase 4 — backlog:** the Ideas list in `_tracker.md` (`allowed-tools` frontmatter, `--dry-run`, optional test step).
