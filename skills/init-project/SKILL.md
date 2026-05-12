---
name: init-project
description: Bootstrap a new project with the four files /wrap-up expects — `_tracker.md`, `_prd.md`, `AGENTS.md`, `CLAUDE.md`. Copies starter templates from the plugin into the current project root. Never overwrites existing files. Invoke explicitly via /init-project.
disable-model-invocation: true
---

Bootstrap the project at the current working directory with the four files `/wrap-up` expects. Run all steps in order.

## Step 1: Confirm the target directory

- Print the absolute path of the current working directory.
- Confirm with the user: "I'll create starter `_tracker.md`, `_prd.md`, `AGENTS.md`, `CLAUDE.md` here. Existing files will NOT be overwritten. Proceed?"
- Wait for explicit confirmation. If the user says no, stop.

## Step 2: Resolve the templates directory

- Templates ship with the plugin at `templates/` relative to the plugin root.
- Find the plugin root: it's the directory containing this `SKILL.md`'s parent's parent (`skills/init-project/SKILL.md` → plugin root is two levels up).
- The four template files are:
  - `templates/_tracker.md`
  - `templates/_prd.md`
  - `templates/AGENTS.md`
  - `templates/CLAUDE.md`

## Step 3: Ask for the project name

- Default: the basename of the current working directory.
- Ask the user: "Project name? (default: `<basename>`)"
- Use the user's answer (or the default if they just press enter) as `{{PROJECT_NAME}}`.

## Step 4: For each template — create only if missing

For each of the four files (`_tracker.md`, `_prd.md`, `AGENTS.md`, `CLAUDE.md`):

1. Check whether the file already exists in the project root.
2. **If it exists:** skip it. Print `<file>: already exists — skipped`.
3. **If it does NOT exist:** read the template from the plugin's `templates/` folder, replace `{{PROJECT_NAME}}` with the project name from Step 3, and write it to the project root. Print `<file>: created`.

For `_tracker.md` specifically, also replace the placeholder date `YYYY-MM-DD` in the seed "Project scaffolding" entry with today's date.

**Never overwrite.** If a file exists, the user already has their own version — leave it alone.

## Step 5: Suggest the next move

After all four files are processed, print:

- A summary list (which were created, which were skipped).
- A reminder: "Run `/wrap-up` at the end of each working session. It will commit your work, then update these files if useful."
- If `git status` shows the project root is not a git repo, say: "Tip: this directory is not a git repo. Run `git init` if you want `/wrap-up` to commit your work automatically."

## Notes

- This skill never edits existing project files. Its only job is to seed missing scaffolding.
- The four files are independent. You can delete any of them later if you don't want that step running — `/wrap-up` will skip the corresponding step.
- The starter templates are deliberately minimal. Fill them in as the project grows.
