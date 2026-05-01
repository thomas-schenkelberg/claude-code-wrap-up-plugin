# wrap-up

A Claude Code skill that runs an end-of-session wrap-up: commits and pushes the git repos you actually touched, then optionally updates `_tracker.md`, `_prd.md`, `AGENTS.md`, `CLAUDE.md`, and your Claude Code memory.

Invoke with `/wrap-up` at the end of a working session.

---

## Install

In any Claude Code session, run these two commands:

```
/plugin marketplace add tschenkster/claude-code-wrap-up-skill
/plugin install wrap-up@wrap-up
```

That's it. `/wrap-up` is now available in every project.

> **Note:** Claude Code requires a marketplace before installing a plugin. The marketplace name and the plugin name are both `wrap-up` — the install command literally reads "install plugin `wrap-up` from marketplace `wrap-up`."

To update later:

```
/plugin marketplace update wrap-up
```

To uninstall:

```
/plugin uninstall wrap-up@wrap-up
/plugin marketplace remove wrap-up
```

---

## Usage

At the end of a working session, type:

```
/wrap-up
```

Claude will run the 6-step procedure. You don't need to confirm individual steps — invoking the command is your authorization. Claude will still respect any per-tool permission prompts your Claude Code settings enforce (e.g. requiring approval for `git push`).

---

## What it does

1. **Commit & push the git repos you touched this session.** Resolves repo roots from the files you actually edited, stages only those files (never `git add -A`), filters out anything that looks like a secret (`.env*`, `*.pem`, service-role keys, etc.), commits with a concise message, and pushes. On push failure, reports and moves on — never force-pushes, never bypasses hooks.

2. **Update `_tracker.md`** (if present). Moves completed items to "Done" with today's date, adds new items discovered this session, keeps entries one-line and under ~80 chars, and trims the file back under ~100 lines if it's grown too long.

3. **Update `_prd.md`** (if present, and only if architecture/scope changed).

4. **Update `AGENTS.md`** (if present, and only if a new tool/workflow/structure rule was introduced).

5. **Review session for memory writes.** Looks for user corrections, validated approaches, and recurring gotchas — applies a "will a future session need this?" filter before writing.

6. **Update `CLAUDE.md`** (if present, and only if conventions or phase status changed).

Each step is conservative: if nothing meaningful changed, or the relevant file doesn't exist, the step says "no update needed" and moves on.

---

## Conventions this skill expects

The skill assumes some lightweight project conventions. None of them are required — each step skips itself if the relevant file is missing.

| File at project root | Purpose | Skill behavior if missing |
|---|---|---|
| `_tracker.md` | One-line `Done` / `Next` / `Ideas` changelog | Step 2 skips |
| `_prd.md` | Product/spec doc | Step 3 skips |
| `AGENTS.md` | Cross-agent project instructions | Step 4 skips |
| `CLAUDE.md` | Claude-specific project instructions | Step 6 skips |

If you keep your tracker / PRD elsewhere (or not at all), just leave the corresponding file absent and those steps no-op.

---

## Customizing

The skill is one file — `skills/wrap-up/SKILL.md`. Fork this repo or copy that file into your own `~/.claude/skills/wrap-up/SKILL.md` to customize.

Common adaptations:

- **Add a "run tests" step** at the top, before commit/push, gated on the presence of `package.json` / `pytest.ini` / etc.
- **Add a "deploy" step** at the end if your project has a deploy hook (e.g. `vercel --prod`, `gcloud run deploy`, `flyctl deploy`).
- **Loosen or tighten the secret-pattern blocklist** in Step 1.
- **Disable the memory step** (Step 5) if you don't use Claude Code's memory feature.

The skill is plain Markdown — Claude reads and executes it as a procedure. No JS, no hooks, no MCP server.

---

## Why a skill, not a slash command?

Modern Claude Code surfaces a skill as a `/<name>` invocation automatically when its `name` field matches. Skills also support frontmatter (`disable-model-invocation`, `allowed-tools`) that slash commands don't, and they can ship supporting files (templates, examples) alongside the procedure if the skill grows.

This skill sets `disable-model-invocation: true` so Claude only runs it when you explicitly type `/wrap-up` — never opportunistically.

---

## License

MIT — see [LICENSE](./LICENSE).
