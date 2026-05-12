# Wrap-Up: a Claude Code plugin

**Wrap-Up handles the housekeeping around your work with Claude Code: it saves what you did, and it keeps a short status page for every project up to date.** One command at the end of a session, and you never lose work or lose the thread.

It's built for people who use Claude Code to get real work done - finance and operations teams, founders, analysts - not only software engineers. You don't need to be technical to use it.

> **New here?** *Claude Code* is Anthropic's AI assistant that you run on your computer (in a terminal, or inside the VS Code editor) to do work with you. A *plugin* is an add-on you install once; after that it's available in every project. Wrap-Up is one plugin that adds two commands: `/init-project` and `/wrap-up`.

---

## The problem it solves

You work with Claude Code for an hour, you get somewhere good, you close the laptop. Three weeks later you open the same project and have to reconstruct what you did, what's still open, and why you made the choices you made. Sometimes it's worse: the work was never committed, so it lives only on your machine, and your machine is the single point of failure.

Wrap-Up takes care of the end of the session so you don't have to remember to:

- **Save your work.** `/wrap-up` commits and pushes whatever you changed, with sensible guardrails: it only touches the files you actually edited, it skips anything that looks like a password or key, and it never rewrites history.
- **Keep a running log.** A short `_tracker.md` file - *done / next / ideas* - that actually gets maintained. "Where was I?" becomes a ten-second read.
- **Keep a one-page brief.** A `_prd.md` file - the problem, who it's for, what's in and out of scope - so the project doesn't quietly drift off course.
- **Give the AI a memory.** Two small instruction files (`AGENTS.md`, `CLAUDE.md`) so the next session - or a colleague, or a different AI tool - starts with context instead of a blank page.
- **Write down what you learned.** The session is reviewed for anything worth keeping, and the useful bits are saved to Claude Code's own memory.

All of that happens when you type one thing: `/wrap-up`.

---

## Install it (two lines, once)

Open Claude Code and paste these two commands:

```
/plugin marketplace add tschenkster/claude-code-wrap-up-plugin
/plugin install wrap-up@wrap-up
```

That's the whole setup. From now on, `/init-project` and `/wrap-up` work in every project.

To pick up a newer version later:

```
/plugin marketplace update wrap-up
/plugin update wrap-up@wrap-up
```

To remove it: `/plugin uninstall wrap-up@wrap-up` then `/plugin marketplace remove wrap-up`.

---

## How you use it

**When you start a new project:** run `/init-project` once. It asks you for a project name, then creates the four files described below (and skips any you already have). Setup takes a minute.

**At the end of every session:** run `/wrap-up`. Claude saves your work and brings the four files up to date. You don't have to approve each step - running the command is your go-ahead. If your Claude Code is set up to ask before things like pushing to GitHub, you'll still get that prompt; the plugin doesn't bypass it.

If you skip `/init-project` and just use `/wrap-up`, that's fine - the first time, it creates the four files for you and uses the folder name as the project name. You can edit that afterward.

---

## The four files, in plain terms

| File | What it is | Why it helps you |
|---|---|---|
| `_tracker.md` | A short log: done / next / ideas | "Where was I on this?" answered in seconds |
| `_prd.md` | A one-pager: problem, users, scope, plan | Keeps the project from drifting |
| `AGENTS.md` | Plain instructions for any AI coding tool | The AI starts each session with context, not a cold start |
| `CLAUDE.md` | Notes specific to Claude Code (it loads `AGENTS.md`) | Same idea, tuned for the tool you're using |

These are ordinary text files in your project folder. You can open them, read them, and edit them like any document. Wrap-Up creates them once and keeps them current - it will not overwrite changes you've made.

---

## Good to know

- **It only touches the project you're working in.** One project or fifty, it doesn't go wandering through your other folders.
- **It's conservative.** It *creates* the four files if they're missing, but it only *changes* an existing one when there's a real reason to. No busywork edits.
- **Nothing to configure.** No account to connect, no settings to tune. It's a set of plain-text instructions Claude follows.
- **Same behaviour everywhere.** Works the same in the Claude Code command line and in the VS Code extension.

---

## Why this exists

I'm Thomas - a fractional CFO who helps tech companies put AI to work in finance and operations. Wrap-Up is the end-of-session routine I use myself, packaged so the people I work with can use it too. It's deliberately small and opinionated: the value is that it's *automatic*, so the discipline doesn't depend on anyone remembering it.

---

## Under the hood (for developers)

The plugin is two Markdown "skills" - `skills/wrap-up/SKILL.md` (the `/wrap-up` procedure, with the four starter file bodies as Appendices A-D) and `skills/init-project/SKILL.md` - packaged via `.claude-plugin/marketplace.json` + `.claude-plugin/plugin.json`. No JavaScript, no hooks, no MCP server: Claude reads the Markdown and follows it. The repo dogfoods its own convention (it carries `_tracker.md` / `_prd.md` / `AGENTS.md` / `CLAUDE.md` at the root); see [`AGENTS.md`](./AGENTS.md) for how to test locally and publish a version.

Installing without `/plugin` (for example, in a managed agent like Cowork): copy `skills/wrap-up/` and `skills/init-project/` into `~/.claude/skills/`. It works standalone because the starter content is inlined into the skill rather than kept in a separate folder.

To change what a freshly-created `_tracker.md` / `_prd.md` / `AGENTS.md` / `CLAUDE.md` looks like, edit Appendices A-D in `skills/wrap-up/SKILL.md` (and nowhere else - `/init-project` reads the same blocks).

> This repository was renamed from `claude-code-wrap-up-skill` to `claude-code-wrap-up-plugin` on 2026-05-12. GitHub redirects the old URL, so an existing install via the old name keeps working; new installs should use the name above.

---

## Author

Built and maintained by **tschenkster - Thomas Schenkelberg** ([LinkedIn](https://www.linkedin.com/in/thomas-schenkelberg/)), a fractional CFO who helps tech companies put AI to work in finance and operations. If this is useful to your team, that's the day job - feel free to reach out.

## License

MIT - see [LICENSE](./LICENSE). Copyright (c) 2026 tschenkster (Thomas Schenkelberg | https://www.linkedin.com/in/thomas-schenkelberg/).
