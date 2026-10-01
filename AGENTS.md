# AGENTS.md: claude-code-wrap-up-plugin

> Project instructions for any coding agent working on this repo. This repo deliberately follows the convention its own plugin enforces - hence this file plus `_tracker.md` and `_prd.md` at the root. There is no `CLAUDE.md`: Claude Code reads this file natively (v2.1.277+); Claude-only notes are in the `## Claude Code` section at the bottom.

**Maintainer:** thomasschenkelberg (Thomas Schenkelberg | https://www.linkedin.com/in/thomas-schenkelberg/). MIT licensed.

## What this project is

A **Claude Code plugin** named `wrap-up`, published to GitHub and installable via the Claude Code plugin/marketplace system. The plugin bundles **two skills** (each a `/<name>` slash command):

- **`/wrap-up`** - end-of-session routine: commit & push the git repos you touched, then ensure and update the three project files (`_tracker.md`, `_prd.md`, `AGENTS.md`), keep an existing `CLAUDE.md` pointed at `AGENTS.md`, then review the session for memory writes.
- **`/init-project`** - bootstrap a new project with those same three files, interactively (asks for a project name). Never overwrites, never creates a `CLAUDE.md`.

There is no runtime: the skills are plain Markdown that Claude reads and executes as a procedure. No JavaScript, no hooks, no MCP server.

## Repository layout

```
.claude-plugin/plugin.json         plugin manifest - the version number lives here
skills/wrap-up/SKILL.md            the /wrap-up procedure + Appendices A-C (the three starter file bodies)
skills/init-project/SKILL.md       the /init-project procedure (reuses wrap-up's Appendices A-C)
AGENTS.md  _tracker.md  _prd.md    this repo dogfooding its own convention
README.md  LICENSE  .gitignore
```

The marketplace listing that catalogs this plugin lives in a separate repo: [`thomas-schenkelberg/marketplace`](https://github.com/thomas-schenkelberg/marketplace). This repo is a pure plugin source consumed by that marketplace.

The starter content for the three project files is **inlined into `skills/wrap-up/SKILL.md` as Appendices A-C** (no separate `templates/` directory - it was removed in v1.3.0 because a skill can't reliably locate a sibling directory when installed standalone or run by a managed agent). `/init-project` references those same appendices.

## Instruction file policy (since v1.5.0)

- `AGENTS.md` is the one required instruction file. Claude Code reads it natively since v2.1.277; Codex, Cursor, Gemini CLI and most other agents read it too.
- `CLAUDE.md` is optional. The skills never create one unasked and never delete one. `/wrap-up` adds an `@AGENTS.md` line to an existing `CLAUDE.md` that lacks it, because Claude Code's default setting (`claude-md-or-agents-md`) reads `AGENTS.md` only when no `CLAUDE.md` / `.claude/CLAUDE.md` / `CLAUDE.local.md` sits in the working folder or above.
- Clients may run older Claude Code. On < 2.1.277, or when a parent folder's `CLAUDE.md` blocks the fallback, a one-line `CLAUDE.md` containing `@AGENTS.md` makes Claude read it; an imported `AGENTS.md` is never read twice. The skills mention this and offer the file; they don't create it on their own.
- Reference: https://code.claude.com/docs/en/memory#agents-md

## How to test locally

1. The marketplace lives in a sibling repo. For local development against this plugin, clone `thomas-schenkelberg/marketplace`, point its `.claude-plugin/marketplace.json` plugin entry at this repo via `"source": "/absolute/path/to/claude-code-wrap-up-plugin"`, then add the marketplace as a local path:
   ```
   /plugin marketplace add /absolute/path/to/thomas-schenkelberg-marketplace
   /plugin install wrap-up@thomas-schenkelberg
   ```
   Don't commit the local-source change to the marketplace repo.
2. Exercise the skills in a throwaway folder:
   - `/wrap-up` in a bare (non-git) folder -> should create `_tracker.md`, `_prd.md`, `AGENTS.md` (project name = the folder basename), create no `CLAUDE.md`, commit nothing, and print a clean summary.
   - `/wrap-up` in a folder whose `CLAUDE.md` has no `@AGENTS.md` line -> should add that line and leave the rest of the file alone.
   - `git init`, then `/init-project` -> should prompt for a project name and create the three files; re-running it should report all three "already exists - skipped".
3. Uninstall when done:
   ```
   /plugin uninstall wrap-up@thomas-schenkelberg
   /plugin marketplace remove thomas-schenkelberg
   ```

## How to publish a new version

1. Make the change in `skills/*/SKILL.md` (and the README if behavior changed).
2. Bump `version` in `.claude-plugin/plugin.json` (semver).
3. Update `README.md` - especially the "The problem it solves" list and the files table - and add a line to `_tracker.md`.
4. Commit with the standard Claude Code co-author trailer and push to `main`.
5. If the plugin's one-line description changed, also update the matching entry in [`thomas-schenkelberg/marketplace`](https://github.com/thomas-schenkelberg/marketplace)'s `.claude-plugin/marketplace.json`.
6. Installed users update with: `/plugin marketplace update thomas-schenkelberg` then `/plugin update wrap-up@thomas-schenkelberg`.

## Conventions

- **Markdown only.** No JS, no hooks, no MCP server. If a step needs logic, express it as instructions, not code.
- **Keep the two skills in sync.** The three starter file bodies live in `wrap-up`'s Appendices A-C and nowhere else; `init-project` points at them. Don't duplicate.
- **Bump the version on any behavior change.** The version in `plugin.json` is how installed users know to update.
- **Conservative by default.** The skills auto-*create* the three files but only *edit* an existing one when there's a real reason - never churn files for the sake of it.
- **No em-dashes** in public prose (README, skills, manifests, commit messages): use ` - `.

## Claude Code

- End a session here with `/wrap-up`, or with any end-of-session routine that maintains `_tracker.md`, `_prd.md` and `AGENTS.md` the same way - this repo eats its own dog food.
- Any behavior change to a skill goes through the full "How to publish a new version" list (version bump, README, `_tracker.md`), even a small one.
- Preferred model: whatever the user is on; nothing in this repo needs a specific model.
