# wrap-up

**`wrap-up` is a Claude Code plugin.** It bundles two **skills** — `/wrap-up` and `/init-project` — that you invoke as slash commands in any Claude Code session.

> Quick glossary: a **plugin** is the installable unit you add with `/plugin marketplace add` + `/plugin install`. A **skill** is a `/<name>` procedure that lives inside a plugin. This repo is one plugin (`wrap-up`) containing two skills.

- **`/wrap-up`** — run it at the end of a working session. It commits and pushes the git repos you actually touched, then makes sure `_tracker.md`, `_prd.md`, `AGENTS.md`, and `CLAUDE.md` exist (creating any that are missing) and updates them, then reviews the session for anything worth writing to your Claude Code memory.
- **`/init-project`** — run it once when you start a new project. It seeds those same four files, asking you for a project name. It never overwrites a file you already have.

---

## Why install this — what you get

- **One command instead of a checklist.** End a session by typing `/wrap-up` rather than remembering to commit, push, update the changelog, refresh the spec, and note learnings.
- **You stop losing work.** `/wrap-up` commits and pushes the repos you touched this session — staging only the files you edited, filtering out anything that looks like a secret, never force-pushing, never bypassing hooks.
- **Every project keeps a living changelog and a real spec.** `_tracker.md` (one-line Done / Next / Ideas) and `_prd.md` (problem, users, scope, architecture, phases) are created automatically the first time and kept current after that. No more "what was I doing here three weeks ago?"
- **Your coding agents get proper instructions.** `AGENTS.md` (works for any agent — Claude Code, Codex, Cursor, Gemini CLI) and `CLAUDE.md` (Claude-specific) are created and maintained, so the next session — or the next agent, or a teammate — picks up context instead of guessing.
- **Durable learnings.** The session is reviewed for corrections and gotchas worth saving to Claude Code memory, applying a "will a future session actually need this?" filter.
- **Zero config, works everywhere.** Plain Markdown — no JavaScript, no hooks, no MCP server. Runs in the Claude Code CLI, the VS Code extension, and (via a manual copy) managed agents like Cowork. Works whether you have one project or fifty.

In short: it's an opinionated, low-friction end-of-session routine — handy for anyone, and especially for people early in their Claude Code journey who haven't built the habit yet.

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

### Installing without `/plugin` (Cowork / managed agents / manual)

If you're somewhere `/plugin` commands aren't available, copy the two skill folders into your Claude Code skills directory:

```
cp -r skills/wrap-up      ~/.claude/skills/wrap-up
cp -r skills/init-project ~/.claude/skills/init-project
```

This works because the skills are self-contained — the starter file content is inlined into `skills/wrap-up/SKILL.md` (Appendices A–D), so there's no separate `templates/` directory to locate.

---

## Repository layout

```
.claude-plugin/marketplace.json   marketplace catalog (one plugin entry: wrap-up → ./)
.claude-plugin/plugin.json         plugin manifest — the semver version lives here
skills/wrap-up/SKILL.md            the /wrap-up procedure + Appendices A–D (the four starter file bodies)
skills/init-project/SKILL.md       the /init-project procedure (reuses wrap-up's Appendices A–D)
AGENTS.md  CLAUDE.md  _tracker.md  _prd.md   this repo dogfoods its own convention
README.md  LICENSE  .gitignore
```

The skills are plain Markdown — Claude reads them and executes them as a procedure. No JS, no hooks, no MCP server.

---

## Typical workflow

**Once per project, at the start:**

```
/init-project
```

Asks for a project name, then creates `_tracker.md`, `_prd.md`, `AGENTS.md`, and `CLAUDE.md` (only the ones that don't already exist) — ready to fill in.

**Once per working session, at the end:**

```
/wrap-up
```

Claude runs the 6-step procedure. You don't need to confirm individual steps — invoking the command is your authorization. Claude will still respect any per-tool permission prompts your Claude Code settings enforce (e.g. requiring approval for `git push`).

---

## What `/wrap-up` does

1. **Commit & push the git repos you touched this session.** Resolves repo roots from the files you actually edited, stages only those files (never `git add -A`), filters out anything that looks like a secret (`.env*`, `*.pem`, service-role keys, etc.), commits with a concise message, and pushes. On push failure, reports and moves on — never force-pushes, never bypasses hooks. If nothing you touched is in a git repo, it says so and moves on.

2. **Update `_tracker.md` (mandatory).** Auto-created from the inline starter if missing. Then: moves completed items to "Done" with today's date, adds new items discovered this session, keeps entries one-line and under ~80 chars, and trims the file back under ~100 lines if it's grown too long.

3. **Update `_prd.md` (mandatory).** Auto-created from the inline starter if missing. Then: only edits the file if architecture or scope actually changed this session.

4. **Update `AGENTS.md` (mandatory).** Auto-created from the inline starter if missing. Then: only edits the file if a new workflow/tool was added or the project structure changed.

5. **Review the session for memory writes.** Looks for user corrections, validated approaches, and recurring gotchas — applies a "will a future session need this?" filter before writing anything.

6. **Update `CLAUDE.md` (mandatory).** Auto-created from the inline starter if missing (a short file that imports `AGENTS.md` via `@AGENTS.md`). Then: only edits the file if conventions or the project phase changed.

Steps 2, 3, 4, and 6 are **mandatory** — all four files are present after every `/wrap-up` run. But an *existing* file is only edited when there's a real reason; otherwise the step says "no update needed". Step 1 and Step 5 are conservative in the same spirit.

---

## What `/init-project` does

Creates the four files below at the current working directory. It's the explicit, interactive way to set them up **up front** — you pick the project name and confirm the directory. (If you skip it, `/wrap-up` creates the same four files at the end of your first session, using the directory name as the project name, which you can edit afterwards.)

**Existing files are never overwritten** — if you already have a `_tracker.md`, the skill leaves it alone.

| File | Purpose |
|---|---|
| `_tracker.md` | One-line `Done` / `Next` / `Ideas` changelog. Capped at ~100 lines. |
| `_prd.md` | Product/spec doc — problem, users, scope, success criteria, architecture, phases. |
| `AGENTS.md` | Cross-agent project instructions (Claude Code, Codex, Cursor, Gemini CLI, …). |
| `CLAUDE.md` | Claude-specific config; imports `AGENTS.md` via `@AGENTS.md`. |

If the directory isn't a git repo yet, the skill suggests `git init` — not required, since `/wrap-up`'s file-maintenance steps run either way.

---

## The four files this plugin maintains

`/wrap-up` keeps all four of these at the project root, creating any that are missing from the starter content inlined in `skills/wrap-up/SKILL.md` (Appendices A–D). `/init-project` seeds the same four in one interactive step at project start.

| File at project root | Purpose | `/wrap-up` behavior if missing |
|---|---|---|
| `_tracker.md` | One-line `Done` / `Next` / `Ideas` changelog | **auto-created from the inline starter** |
| `_prd.md` | Product/spec doc | **auto-created from the inline starter** |
| `AGENTS.md` | Cross-agent project instructions | **auto-created from the inline starter** |
| `CLAUDE.md` | Claude-specific project instructions | **auto-created from the inline starter** |

All four are non-negotiable for any project that uses this plugin. If you genuinely don't want them on a given project, don't run `/wrap-up` there.

---

## Working with a single project folder

The skills only ever touch the project you're currently in — there's no workspace scan, no assumption that you have more than one project. If your single folder isn't a git repo yet, `/wrap-up`'s commit step quietly does nothing (and tells you so); the four-file maintenance still runs. Run `git init` whenever you want commits + pushes to start happening automatically.

---

## Customizing

The skills are plain Markdown — `skills/wrap-up/SKILL.md` and `skills/init-project/SKILL.md`. The starter content for the four project files lives in `wrap-up`'s **Appendices A–D**. Fork this repo, or copy the skill folders into your own `~/.claude/skills/`, to customize.

Common adaptations to `/wrap-up`:

- **Add a "run tests" step** at the top, before commit/push, gated on the presence of `package.json` / `pytest.ini` / etc.
- **Add a "deploy" step** at the end if your project has a deploy hook (e.g. `vercel --prod`, `gcloud run deploy`, `flyctl deploy`).
- **Loosen or tighten the secret-pattern blocklist** in Step 1.
- **Disable the memory step** (Step 5) if you don't use Claude Code's memory feature.

To change what a newly-created `_tracker.md` / `_prd.md` / `AGENTS.md` / `CLAUDE.md` looks like, edit Appendices A–D in `skills/wrap-up/SKILL.md` (and nowhere else — `/init-project` reads the same blocks).

No JS, no hooks, no MCP server. Claude reads the Markdown and executes it as a procedure.

---

## Why skills, not legacy slash commands?

The two procedures are packaged as **skills** rather than `commands/*.md` files because modern Claude Code surfaces a skill as a `/<name>` invocation automatically when its `name` field matches, skills support frontmatter (`disable-model-invocation`, `allowed-tools`) that `commands/*.md` doesn't, and skills can ship supporting files alongside the procedure if needed. Both skills set `disable-model-invocation: true` so Claude only runs them when you explicitly type the command — never opportunistically. (This is separate from "plugin vs skill": the *plugin* is still how you install and update everything; skills are just how the two commands are implemented inside it.)

---

## Author

Built and maintained by **tschenkster — Thomas Schenkelberg** ([LinkedIn](https://www.linkedin.com/in/thomas-schenkelberg/)), a fractional CFO who helps tech companies put AI to work in finance operations. If this is useful to your team, that's the day job.

## License

MIT — see [LICENSE](./LICENSE). Copyright (c) 2026 tschenkster (Thomas Schenkelberg | https://www.linkedin.com/in/thomas-schenkelberg/).
