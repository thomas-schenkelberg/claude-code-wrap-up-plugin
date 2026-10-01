# PRD: claude-code-wrap-up-plugin

> Product requirements for the `wrap-up` Claude Code plugin. Updated when scope or architecture changes - not for routine wording tweaks.

## Problem

End-of-session hygiene is easy to skip: people forget to commit and push, projects drift without a changelog or a spec, and coding agents start each session guessing because there's no `AGENTS.md`. New Claude Code users especially have no project-doc discipline yet. A single, low-friction "wrap up the session" command fixes all of this - and a "set up the project" command seeds the files it relies on.

## Users

- Claude Code users who want a consistent end-of-session routine without having to remember the steps.
- Specifically: AI Power Hour clients onboarding onto Claude Code - they get the plugin so the discipline comes built in.
- Works in the Claude Code CLI, the VS Code extension, and (via a manual copy into `~/.claude/skills/`) managed agents like Cowork. Works whether the user has one project or fifty.

## Scope

### In scope

- The `/wrap-up` skill: ensure + update `_tracker.md`, `_prd.md`, `AGENTS.md`; keep an existing `CLAUDE.md` importing `AGENTS.md`; review session for memory writes; then commit & push touched repos, wrap-up's own doc writes included (commit moved last in v1.5.1).
- The `/init-project` skill: interactively seed those same three files in a new project; never overwrite.
- The three starter file bodies (inlined as Appendices A-C in `skills/wrap-up/SKILL.md`).
- Install / update / uninstall documentation; "works in a single project folder" and "manual install" notes.

### Out of scope (explicitly)

- CI / automated tests for the plugin itself.
- Running the consuming project's test suite or deploy (left as documented opt-in customizations, not built in).
- Managing other agents' own config formats beyond writing a generic `AGENTS.md`.
- Creating or deleting `CLAUDE.md` files on the user's behalf (the one-line `@AGENTS.md` compatibility file is offered, not created).
- Any runtime - no JS, no hooks, no MCP server.

## Success criteria

- A user installs once and, from then on, every project they run `/wrap-up` in ends up with a maintained `_tracker.md`, `_prd.md`, and `AGENTS.md`, Claude Code actually loads that `AGENTS.md`, and their work is committed + pushed.
- The skills work with zero path configuration in every environment they're plausibly installed into (plugin install, manual `~/.claude/skills/` copy, managed agent).
- Someone who pastes the GitHub URL into Claude Code / Cowork gets, from the README alone, an accurate picture of what the plugin is, what it does for them, and how it's structured.

## Architecture

- **Plain-Markdown skills**, no runtime. Claude reads `SKILL.md` and executes it as a procedure. `disable-model-invocation: true` on both - they only run on the explicit slash command.
- **`.claude-plugin/plugin.json`** - plugin manifest; the semver `version` lives here and is how installed users know to update. The marketplace catalog is *not* in this repo any more - it lives in a sibling repo `thomas-schenkelberg/marketplace` that lists this plugin (and any future ones).
- **Distribution**: GitHub repo `thomas-schenkelberg/claude-code-wrap-up-plugin` hosts the plugin sources; the marketplace at `thomas-schenkelberg/marketplace` points to it. Users `add` the marketplace repo, then `install wrap-up@thomas-schenkelberg`. (Repo history: created at `tschenkster/claude-code-wrap-up-skill` on 2026-05-01, renamed to `...-plugin` on 2026-05-12, transferred to the `thomas-schenkelberg` GitHub Org on 2026-05-13, marketplace renamed `wrap-up` -> `thomas-schenkelberg` on 2026-05-15, marketplace extracted into its own repo on 2026-05-19. GitHub redirects every prior URL automatically; users who previously added this repo as a marketplace need to remove + re-add via the new marketplace repo - the cache is keyed on name.)
- **Self-contained skills**: the three starter file bodies are inlined into `skills/wrap-up/SKILL.md` (Appendices A-C); `skills/init-project/SKILL.md` references them. No external files to locate.
- **One instruction file** (v1.5.0, 2026-10-01): `AGENTS.md` is required; Claude Code reads it natively since v2.1.277. `CLAUDE.md` is optional and never auto-created or deleted. Because the default setting `claude-md-or-agents-md` skips `AGENTS.md` whenever a `CLAUDE.md` sits in the working folder or above, `/wrap-up` adds `@AGENTS.md` to an existing project `CLAUDE.md`, and both skills offer a one-line `@AGENTS.md` `CLAUDE.md` for older Claude Code or a blocking parent-folder `CLAUDE.md`. Up to v1.4.0 the plugin created a `CLAUDE.md` shim claiming Claude Code doesn't read `AGENTS.md` directly; that claim is obsolete.
- **Dogfooding**: this repo carries its own `_tracker.md`, `_prd.md`, `AGENTS.md` at the root (no `CLAUDE.md`).

## Data model

Not applicable - there is no data store. The "entities" are the project files the plugin maintains and the plugin manifest.

## Phases

- **Phase 1 - done:** `/wrap-up` shipped (v1.0.0): commit/push + optional tracker/PRD/AGENTS/CLAUDE updates + memory review.
- **Phase 2 - done:** `/init-project` + starter templates (v1.1.0); tracker + PRD made mandatory (v1.2.0).
- **Phase 3 - done:** `AGENTS.md` + `CLAUDE.md` also mandatory; starters inlined; `templates/` removed; repo dogfoods; README states plugin-vs-skill and the user benefits (v1.3.0); MEMORY.md size check (v1.4.0).
- **Phase 4 - current:** `AGENTS.md` is the one required instruction file, `CLAUDE.md` optional (v1.5.0).
- **Phase 5 - backlog:** the Ideas list in `_tracker.md` (`allowed-tools` frontmatter, `--dry-run`, optional test step).
