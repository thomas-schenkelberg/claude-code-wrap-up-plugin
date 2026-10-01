# Wrap-Up: a Claude Code plugin

**Wrap-Up handles the housekeeping around your work with Claude Code: it saves what you did, and it keeps a short status page for every project up to date.** One command at the end of a session, and you never lose work or lose the thread.

It's built for people who use Claude Code to get real work done - finance and operations teams, founders, analysts - not only software engineers. You don't need to be technical to use it.

> **New here?** *Claude Code* is Anthropic's AI assistant that you run on your computer (in a terminal, or inside the VS Code editor) to do work with you. A *plugin* is an add-on you install once; after that it's available in every project. Wrap-Up is one plugin that adds two commands: `/init-project` and `/wrap-up`.

---

## The problem it solves

You work with Claude Code for an hour, you get somewhere good, you close the laptop. Three weeks later you open the same project and have to reconstruct what you did, what's still open, and why you made the choices you made. Sometimes it's worse: the work was never committed, so it lives only on your machine, and your machine is the single point of failure.

Wrap-Up takes care of the end of the session so you don't have to remember to:

- **Save your work.** `/wrap-up` commits and pushes whatever you changed, including the three status files below that it just brought up to date, with sensible guardrails: it only touches the files you and the wrap-up actually edited, it skips anything that looks like a password or key, and it never rewrites history.
- **Keep a running log.** A short `_tracker.md` file - *done / next / ideas* - that actually gets maintained. "Where was I?" becomes a ten-second read.
- **Keep a one-page brief.** A `_prd.md` file - the problem, who it's for, what's in and out of scope - so the project doesn't quietly drift off course.
- **Give the AI a memory.** One small instruction file, `AGENTS.md`, so the next session - or a colleague, or a different AI tool - starts with context instead of a blank page. Claude Code reads it on its own, and so do most other AI coding tools.
- **Write down what you learned.** The session is reviewed for anything worth keeping, the useful bits are saved to Claude Code's own memory, and that memory file is kept from silently overflowing its size limit.

All of that happens when you type one thing: `/wrap-up`.

---

## Install it (two lines, once)

Open Claude Code and paste these two commands:

```
/plugin marketplace add thomas-schenkelberg/marketplace
/plugin install wrap-up@thomas-schenkelberg
```

That's the whole setup. From now on, `/init-project` and `/wrap-up` work in every project. The same marketplace will carry any future plugins I publish, so you only do this once.

To pick up a newer version later:

```
/plugin marketplace update thomas-schenkelberg
/plugin update wrap-up@thomas-schenkelberg
```

To remove it: `/plugin uninstall wrap-up@thomas-schenkelberg` then `/plugin marketplace remove thomas-schenkelberg`.

**Migrating from the old install path?** If you previously added this repo directly (`/plugin marketplace add thomas-schenkelberg/claude-code-wrap-up-plugin`), switch once: `/plugin marketplace remove thomas-schenkelberg`, then the two install lines above.

---

## How you use it

**When you start a new project:** run `/init-project` once. It asks you for a project name, then creates the three files described below (and skips any you already have). Setup takes a minute.

**At the end of every session:** run `/wrap-up`. Claude brings the three files up to date, then saves your work, those updates included. You don't have to approve each step - running the command is your go-ahead. If your Claude Code is set up to ask before things like pushing to GitHub, you'll still get that prompt; the plugin doesn't bypass it.

If you skip `/init-project` and just use `/wrap-up`, that's fine - the first time, it creates the three files for you and uses the folder name as the project name. You can edit that afterward.

---

## The three files, in plain terms

| File | What it is | Why it helps you |
|---|---|---|
| `_tracker.md` | A short log: done / next / ideas | "Where was I on this?" answered in seconds |
| `_prd.md` | A one-pager: problem, users, scope, plan | Keeps the project from drifting |
| `AGENTS.md` | Plain instructions for Claude Code and any other AI coding tool (Claude-only notes go in its `## Claude Code` section) | The AI starts each session with context, not a cold start |

These are ordinary text files in your project folder. You can open them, read them, and edit them like any document. Wrap-Up creates them once and keeps them current - it will not overwrite changes you've made.

**What about `CLAUDE.md`?** You don't need one: Claude Code reads `AGENTS.md` by itself since version 2.1.277. Wrap-Up never creates a `CLAUDE.md` on its own and never deletes yours. If you already have one (earlier versions of this plugin created it), Wrap-Up keeps it and makes sure it contains the line `@AGENTS.md`, because Claude Code skips `AGENTS.md` when a `CLAUDE.md` is present that doesn't point to it. Two cases where you'd still want a one-line `CLAUDE.md` containing just `@AGENTS.md`: your Claude Code is older than 2.1.277, or a folder above your project has its own `CLAUDE.md`. Wrap-Up spots these and offers to create it; Claude never reads `AGENTS.md` twice.

---

## Good to know

- **It only touches the project you're working in.** One project or fifty, it doesn't go wandering through your other folders.
- **It's conservative.** It *creates* the three files if they're missing, but it only *changes* an existing one when there's a real reason to. No busywork edits.
- **Nothing to configure.** No account to connect, no settings to tune. It's a set of plain-text instructions Claude follows.
- **Same behaviour everywhere.** Works the same in the Claude Code command line and in the VS Code extension.

---

## Why this exists

I'm Thomas - a fractional CFO who helps tech companies put AI to work in finance and operations. Wrap-Up is the end-of-session routine I use myself, packaged so the people I work with can use it too. It's deliberately small and opinionated: the value is that it's *automatic*, so the discipline doesn't depend on anyone remembering it.

---

## Under the hood (for developers)

The plugin is two Markdown "skills" - `skills/wrap-up/SKILL.md` (the `/wrap-up` procedure, with the three starter file bodies as Appendices A-C) and `skills/init-project/SKILL.md` - packaged via `.claude-plugin/plugin.json`. The marketplace listing lives in a separate repo, [`thomas-schenkelberg/marketplace`](https://github.com/thomas-schenkelberg/marketplace), which catalogues this plugin (and any future ones). No JavaScript, no hooks, no MCP server: Claude reads the Markdown and follows it. The repo dogfoods its own convention (it carries `_tracker.md` / `_prd.md` / `AGENTS.md` at the root, no `CLAUDE.md`); see [`AGENTS.md`](./AGENTS.md) for how to test locally and publish a version.

Installing without `/plugin` (for example, in a managed agent like Cowork): copy `skills/wrap-up/` and `skills/init-project/` into `~/.claude/skills/`. It works standalone because the starter content is inlined into the skill rather than kept in a separate folder.

To change what a freshly-created `_tracker.md` / `_prd.md` / `AGENTS.md` looks like, edit Appendices A-C in `skills/wrap-up/SKILL.md` (and nowhere else - `/init-project` reads the same blocks).

> Repository history: created at `tschenkster/claude-code-wrap-up-skill` (2026-05-01), renamed to `claude-code-wrap-up-plugin` (2026-05-12), transferred to the `thomas-schenkelberg` GitHub Org (2026-05-13), marketplace extracted to a dedicated repo `thomas-schenkelberg/marketplace` (2026-05-19). GitHub redirects every prior URL automatically; existing installs that added this repo directly should re-add via the marketplace (see "Migrating from the old install path?" above).

---

## Author

Built and maintained by **thomasschenkelberg - Thomas Schenkelberg** ([LinkedIn](https://www.linkedin.com/in/thomas-schenkelberg/)), a fractional CFO who helps tech companies put AI to work in finance and operations. If this is useful to your team, that's the day job - feel free to reach out.

## License

MIT - see [LICENSE](./LICENSE). Copyright (c) 2026 thomasschenkelberg (Thomas Schenkelberg | https://www.linkedin.com/in/thomas-schenkelberg/).
