# wrap-up

Two Claude Code skills for the start and end of a working session.

- **`/init-project`** — bootstrap a new project with the four files `/wrap-up` expects (`_tracker.md`, `_prd.md`, `AGENTS.md`, `CLAUDE.md`). Never overwrites existing files.
- **`/wrap-up`** — at the end of a session, commit and push the git repos you actually touched, then optionally update `_tracker.md`, `_prd.md`, `AGENTS.md`, `CLAUDE.md`, and your Claude Code memory.

---

## Install

In any Claude Code session, run these two commands:

```
/plugin marketplace add tschenkster/claude-code-wrap-up-skill
/plugin install wrap-up@wrap-up
```

That's it. Both `/init-project` and `/wrap-up` are now available in every project.

> **Note:** Claude Code requires a marketplace before installing a plugin. The marketplace name and the plugin name are both `wrap-up` — the install command literally reads "install plugin `wrap-up` from marketplace `wrap-up`."

To update later (two commands — works identically in the CLI and the VS Code extension):

```
/plugin marketplace update wrap-up        # refresh the marketplace catalog
/plugin update wrap-up@wrap-up            # upgrade the installed plugin
```

The first command alone only refreshes what's *available*; it does not upgrade the installed copy. If Claude Code prompts you afterwards, run `/reload-plugins` to pick up the new version in the current session.

To uninstall:

```
/plugin uninstall wrap-up@wrap-up
/plugin marketplace remove wrap-up
```

---

## Typical workflow

**Once per project, at the start:**

```
/init-project
```

Creates the four scaffolding files from the bundled templates (only if they don't already exist) and asks for a project name. After that, your project has `_tracker.md`, `_prd.md`, `AGENTS.md`, and `CLAUDE.md` ready to fill in.

**Once per working session, at the end:**

```
/wrap-up
```

Claude runs the 6-step procedure. You don't need to confirm individual steps — invoking the command is your authorization. Claude will still respect any per-tool permission prompts your Claude Code settings enforce (e.g. requiring approval for `git push`).

---

## What `/wrap-up` does

1. **Commit & push the git repos you touched this session.** Resolves repo roots from the files you actually edited, stages only those files (never `git add -A`), filters out anything that looks like a secret (`.env*`, `*.pem`, service-role keys, etc.), commits with a concise message, and pushes. On push failure, reports and moves on — never force-pushes, never bypasses hooks.

2. **Update `_tracker.md` (mandatory).** If the file does not exist, it is auto-created from the bundled template. Then: moves completed items to "Done" with today's date, adds new items discovered this session, keeps entries one-line and under ~80 chars, and trims the file back under ~100 lines if it's grown too long.

3. **Update `_prd.md` (mandatory).** If the file does not exist, it is auto-created from the bundled template. Then: only edits the file if architecture/scope changed this session.

4. **Update `AGENTS.md`** (if present, and only if a new tool/workflow/structure rule was introduced).

5. **Review session for memory writes.** Looks for user corrections, validated approaches, and recurring gotchas — applies a "will a future session need this?" filter before writing.

6. **Update `CLAUDE.md`** (if present, and only if conventions or phase status changed).

Steps 2 and 3 are mandatory — `_tracker.md` and `_prd.md` are always present after a `/wrap-up` run. Steps 4 and 6 are conservative: if `AGENTS.md` / `CLAUDE.md` doesn't exist or nothing meaningful changed, the step says "no update needed" and moves on. (Use `/init-project` to seed all four files at once.)

---

## What `/init-project` does

Creates the four files below at the current working directory, using the templates that ship with the plugin. **Existing files are never overwritten** — if you already have a `_tracker.md`, the skill leaves it alone.

| File | Purpose |
|---|---|
| `_tracker.md` | One-line `Done` / `Next` / `Ideas` changelog. Capped at ~100 lines. |
| `_prd.md` | Product/spec doc — problem, users, scope, success criteria, architecture, phases. |
| `AGENTS.md` | Cross-agent project instructions (works for Claude Code, Codex, Cursor, Gemini CLI, …). |
| `CLAUDE.md` | Claude-specific config; imports `AGENTS.md` via `@AGENTS.md`. |

The templates are deliberately minimal — fill them in as the project grows.

If the directory is not a git repo, the skill suggests `git init` so `/wrap-up` can commit your work.

---

## Conventions this skill expects

`/wrap-up` looks for these four files at the project root. `_tracker.md` and `_prd.md` are mandatory and auto-created from templates if missing. `AGENTS.md` and `CLAUDE.md` are optional — if absent, those steps no-op. `/init-project` exists to seed all four in one step at project start.

| File at project root | Purpose | `/wrap-up` behavior if missing |
|---|---|---|
| `_tracker.md` | One-line `Done` / `Next` / `Ideas` changelog | **auto-created from template** |
| `_prd.md` | Product/spec doc | **auto-created from template** |
| `AGENTS.md` | Cross-agent project instructions | Step 4 skips |
| `CLAUDE.md` | Claude-specific project instructions | Step 6 skips |

If you don't want a tracker or PRD on a given project, don't run `/wrap-up` there — they're non-negotiable for any project that uses this skill.

---

## Customizing

The skills are plain Markdown — `skills/wrap-up/SKILL.md` and `skills/init-project/SKILL.md`. The starter templates live in `templates/`. Fork this repo or copy the files into your own `~/.claude/skills/` to customize.

Common adaptations to `/wrap-up`:

- **Add a "run tests" step** at the top, before commit/push, gated on the presence of `package.json` / `pytest.ini` / etc.
- **Add a "deploy" step** at the end if your project has a deploy hook (e.g. `vercel --prod`, `gcloud run deploy`, `flyctl deploy`).
- **Loosen or tighten the secret-pattern blocklist** in Step 1.
- **Disable the memory step** (Step 5) if you don't use Claude Code's memory feature.

Common adaptations to the templates:

- Change the wording / structure to fit your team's conventions.
- Add team-specific sections to `AGENTS.md` (linter rules, branch naming, code review process).
- Drop a template you don't want — `/init-project` only seeds files that have a corresponding template in `templates/`.

No JS, no hooks, no MCP server. Claude reads the Markdown and executes it as a procedure.

---

## Why a skill, not a slash command?

Modern Claude Code surfaces a skill as a `/<name>` invocation automatically when its `name` field matches. Skills also support frontmatter (`disable-model-invocation`, `allowed-tools`) that slash commands don't, and they can ship supporting files (templates, examples) alongside the procedure if the skill grows.

Both skills set `disable-model-invocation: true` so Claude only runs them when you explicitly type the command — never opportunistically.

---

## License

MIT — see [LICENSE](./LICENSE).
