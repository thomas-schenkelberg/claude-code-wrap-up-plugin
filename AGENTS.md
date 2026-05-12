# AGENTS.md: claude-code-wrap-up-plugin

> Project instructions for any coding agent working on this repo. This repo deliberately follows the convention its own plugin enforces — hence this file plus `CLAUDE.md`, `_tracker.md`, and `_prd.md` at the root.

**Maintainer:** tschenkster (Thomas Schenkelberg | https://www.linkedin.com/in/thomas-schenkelberg/). MIT licensed.

## What this project is

A **Claude Code plugin** named `wrap-up`, published to GitHub and installable via the Claude Code plugin/marketplace system. The plugin bundles **two skills** (each a `/<name>` slash command):

- **`/wrap-up`** — end-of-session routine: commit & push the git repos you touched, then ensure and update the four project files (`_tracker.md`, `_prd.md`, `AGENTS.md`, `CLAUDE.md`), then review the session for memory writes.
- **`/init-project`** — bootstrap a new project with those same four files, interactively (asks for a project name). Never overwrites.

There is no runtime: the skills are plain Markdown that Claude reads and executes as a procedure. No JavaScript, no hooks, no MCP server.

## Repository layout

```
.claude-plugin/marketplace.json   marketplace catalog (one plugin entry: wrap-up → ./)
.claude-plugin/plugin.json         plugin manifest — the version number lives here
skills/wrap-up/SKILL.md            the /wrap-up procedure + Appendices A–D (the four starter file bodies)
skills/init-project/SKILL.md       the /init-project procedure (reuses wrap-up's Appendices A–D)
AGENTS.md  CLAUDE.md  _tracker.md  _prd.md   this repo dogfooding its own convention
README.md  LICENSE  .gitignore
```

The starter content for the four project files is **inlined into `skills/wrap-up/SKILL.md` as Appendices A–D** (no separate `templates/` directory — it was removed in v1.3.0 because a skill can't reliably locate a sibling directory when installed standalone or run by a managed agent). `/init-project` references those same appendices.

## How to test locally

1. In a scratch project directory, point Claude Code at this repo as a marketplace:
   ```
   /plugin marketplace add /absolute/path/to/claude-code-wrap-up-plugin
   /plugin install wrap-up@wrap-up
   ```
2. Exercise the skills in a throwaway folder:
   - `/wrap-up` in a bare (non-git) folder → should create `_tracker.md`, `_prd.md`, `AGENTS.md`, `CLAUDE.md` (project name = the folder basename), commit nothing, and print a clean summary.
   - `git init`, then `/init-project` → should prompt for a project name and create the four files; re-running it should report all four "already exists — skipped".
3. Uninstall when done:
   ```
   /plugin uninstall wrap-up@wrap-up
   /plugin marketplace remove wrap-up
   ```

## How to publish a new version

1. Make the change in `skills/*/SKILL.md` (and the README if behavior changed).
2. Bump `version` in `.claude-plugin/plugin.json` (semver). Keep `.claude-plugin/marketplace.json`'s description in sync if it changed.
3. Update `README.md` — especially the "What `/wrap-up` does" list and the behavior table — and add a line to `_tracker.md`.
4. Commit with the standard Claude Code co-author trailer and push to `main`.
5. Installed users update with: `/plugin marketplace update wrap-up` then `/plugin update wrap-up@wrap-up`.

## Conventions

- **Markdown only.** No JS, no hooks, no MCP server. If a step needs logic, express it as instructions, not code.
- **Keep the two skills in sync.** The four starter file bodies live in `wrap-up`'s Appendices A–D and nowhere else; `init-project` points at them. Don't duplicate.
- **Bump the version on any behavior change.** The version in `plugin.json` is how installed users know to update.
- **Conservative by default.** The skills auto-*create* the four files but only *edit* an existing one when there's a real reason — never churn files for the sake of it.
- This repo dogfoods its own plugin: run `/wrap-up` at the end of a working session here too.
