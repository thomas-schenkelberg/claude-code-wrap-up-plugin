---
name: init-project
description: Bootstrap a new project with the four files /wrap-up maintains — `_tracker.md`, `_prd.md`, `AGENTS.md`, `CLAUDE.md`. Creates them from the starter content in the wrap-up skill (Appendices A–D), interactively asking for a project name. Never overwrites existing files. Invoke explicitly via /init-project.
disable-model-invocation: true
---

Bootstrap the project at the current working directory with the four files `/wrap-up` maintains. Run all steps in order.

This is the explicit, interactive way to set those files up **up front** — you pick the project name and confirm the directory. (If you skip it, `/wrap-up` creates the same four files at the end of your first session, using the directory name as the project name, which you can edit afterwards.)

## Step 1: Confirm the target directory

- Print the absolute path of the current working directory.
- Confirm with the user: "I'll create starter `_tracker.md`, `_prd.md`, `AGENTS.md`, `CLAUDE.md` here. Existing files will NOT be overwritten. Proceed?"
- Wait for explicit confirmation. If the user says no, stop.

## Step 2: Source of the starter content

- The starter content for all four files is defined verbatim in **Appendices A–D of the `wrap-up` skill** (`skills/wrap-up/SKILL.md`), which ships in the same plugin. Use that content — there is no separate templates directory.
  - Appendix A → `_tracker.md`
  - Appendix B → `_prd.md`
  - Appendix C → `AGENTS.md`
  - Appendix D → `CLAUDE.md`

## Step 3: Ask for the project name

- Default: the basename of the current working directory.
- Ask the user: "Project name? (default: `<basename>`)"
- Use the user's answer (or the default if they just press enter) as `{{PROJECT_NAME}}`.

## Step 4: For each file — create only if missing

For each of the four files (`_tracker.md`, `_prd.md`, `AGENTS.md`, `CLAUDE.md`):

1. Check whether the file already exists in the project root.
2. **If it exists:** skip it. Print `<file>: already exists — skipped`.
3. **If it does NOT exist:** take the corresponding Appendix block from `wrap-up`'s `SKILL.md`, replace `{{PROJECT_NAME}}` with the project name from Step 3, and write it to the project root. Print `<file>: created`.

For `_tracker.md` specifically, also replace the placeholder date `YYYY-MM-DD` in the seed "Project scaffolding" entry with today's date.

**Never overwrite.** If a file exists, the user already has their own version — leave it alone.

## Step 5: Suggest the next move

After all four files are processed, print:

- A summary list (which were created, which were skipped).
- A reminder: "Run `/wrap-up` at the end of each working session. It commits the repos you touched, then keeps these four files current."
- If `git status` shows the project root is not a git repo, say: "Tip: this directory is not a git repo. Run `git init` if you want `/wrap-up` to commit your work automatically." (Not required — `/wrap-up`'s file-maintenance steps run either way.)

## Notes

- This skill never edits existing project files. Its only job is to seed missing scaffolding.
- It ships alongside `/wrap-up` in the same plugin and reuses its Appendix A–D starter content. If you copy `init-project` standalone into `~/.claude/skills/`, also copy `skills/wrap-up/SKILL.md` so the starter content is available.
- The four files are independent. You can delete any of them later, but `/wrap-up` is mandatory about all four — it will recreate a missing one on the next session-end.
- The starter content is deliberately minimal. Fill it in as the project grows.
