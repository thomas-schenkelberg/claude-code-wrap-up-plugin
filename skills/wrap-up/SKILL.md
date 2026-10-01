---
name: wrap-up
description: End-of-session wrap-up. Ensures _tracker.md, _prd.md, and AGENTS.md exist (auto-creates them from the inline starters in this skill if missing) and updates them, keeps an existing CLAUDE.md pointed at AGENTS.md, reviews the session for durable memory writes, then commits and pushes the git repos you touched, those doc updates included. Invoke explicitly via /wrap-up.
disable-model-invocation: true
---

End-of-session wrap-up. Run all 6 steps below in order. The commit comes last on purpose: Steps 1-4 write project files, and Step 6 commits them together with the rest of the session's work, so wrap-up never leaves its own updates uncommitted.

**Mandatory files.** After `/wrap-up` runs, all three of these MUST exist at the project root: `_tracker.md`, `_prd.md`, `AGENTS.md`. Steps 1, 2, and 3 auto-create any that are missing - using the starter content in Appendices A-C at the bottom of this skill - then proceed to update them. They never skip on the grounds that the file is absent.

**`AGENTS.md` is the one instruction file.** Claude Code reads it natively (since v2.1.277), and so do Codex, Cursor, Gemini CLI and most other coding agents. Claude-only notes go in its `## Claude Code` section. `CLAUDE.md` is optional: `/wrap-up` never creates one on its own and never deletes one. If the project already has a `CLAUDE.md`, Step 4 keeps it pointed at `AGENTS.md`.

**Project root** = the output of `git -C . rev-parse --show-toplevel` if the current working directory is inside a git repo; otherwise the current working directory itself.

**One project or many.** This skill only ever touches the project you're currently in - it's fine if that's your only one. If the project is not a git repo, Steps 1-5 still run and create/maintain the files, and Step 6 commits nothing (that's expected).

## Step 1: Update Tracker (mandatory)

- **Required file: `_tracker.md` at the project root.** If it does not exist, create it now from **Appendix A** below. When seeding:
  - Replace `{{PROJECT_NAME}}` with the basename of the project root.
  - Replace the placeholder date `YYYY-MM-DD` in the seed "Project scaffolding" entry with today's date.
  - Print `_tracker.md: created` and continue with the update steps below.
- Read `_tracker.md` and review what was done this session (from the conversation, `git status` / `git diff`, and any commits already made this session).
- **Move completed items** from "Next" to "Done" with today's date. **Add new items** discovered this session to "Next" or "Ideas". **Reprioritize "Next"** if the session changed what matters most.
- **Entry style - match a git commit subject line:** one line, under ~80 characters, imperative or past-tense, no multi-sentence explanations, no "Verified: ..." tails, no architecture context. If it wouldn't fit on `git log --oneline`, it's too long.
  - Good: `- [x] 2026-05-01: Add OAuth login flow to admin app`
  - Good: `- [x] 2026-05-01: Fix 502 cold-start on /classify endpoint`
  - Bad: `- [x] Auth v1 - M1 hardening: token introspection in _shared/verify-token.js, wired checkAuth() into 3 handlers, secrets moved to vault. Verified: invalid token -> 401. (2026-05-01)`
- If a change genuinely needs longer context, write it to memory or the commit message - the tracker is a changelog, not a narrative.
- **Size cap ~100 lines.** Before adding new entries, `wc -l _tracker.md`. If over 100, collapse older multi-line Done entries to one-liners (or fold pure-scaffolding entries into a single "Project scaffolding (YYYY-MM-DD)" line) until back under the cap. Do not delete history - `git log` is the durable record.
- **Never skip on the grounds that the file is missing** - create it. The only "no update needed" path is when `_tracker.md` already exists AND nothing this session is worth recording. Say "Tracker: no update needed" only in that case.

## Step 2: Update PRD (mandatory)

- **Required file: `_prd.md` at the project root.** If it does not exist, create it now from **Appendix B** below. When seeding:
  - Replace `{{PROJECT_NAME}}` with the basename of the project root.
  - Print `_prd.md: created` and continue with the update check below.
  - Flag to the user: "Seed PRD created - fill in Problem, Users, Scope, and Success criteria when you have a moment."
- Read `_prd.md` and check what changed this session.
- **Only edit** the file if there were substantive changes worth reflecting in the PRD:
  - Phase status changed (e.g., a phase completed)
  - New features or capabilities added
  - Architecture decisions that affect the spec
  - Version bump warranted
- **Never skip on the grounds that the file is missing** - create it. For routine bug fixes, style tweaks, or minor refactors, you may say "PRD: no update needed" - but the file must exist after this step.

## Step 3: Update AGENTS.md (mandatory)

- **Required file: `AGENTS.md` at the project root.** If it does not exist, create it now from **Appendix C** below, replacing `{{PROJECT_NAME}}` with the basename of the project root. Print `AGENTS.md: created` and continue.
- Read `AGENTS.md`.
- **Only edit** the file if:
  - A new workflow, command, or tool was added that agents need to know about
  - Project structure changed (new folders, renamed paths)
  - A rule or convention was established for working on this project
  - A Claude Code-only detail changed (MCP servers, skills, hooks, preferred model) - that goes in its `## Claude Code` section
- **Never skip on the grounds that the file is missing** - create it. If nothing this session warrants an edit, say "AGENTS.md: no update needed" - but the file must exist after this step.
- **Will Claude Code actually read it?** On v2.1.277 or later it does, with two exceptions: Claude Code older than 2.1.277, or a `CLAUDE.md`, `CLAUDE.local.md` or `.claude/CLAUDE.md` in a parent folder of the project root (the user's own `~/.claude/CLAUDE.md` doesn't count) - by default any of those makes Claude Code skip `AGENTS.md`. The fix for both is a one-line `CLAUDE.md` at the project root containing `@AGENTS.md`; Claude never reads the file twice. If the project root has no `CLAUDE.md` and you notice either case (`claude --version`, a look at the parent folders), mention it in the summary and offer to create that one-line file. Don't create it unasked.

## Step 4: Check CLAUDE.md (only if the project has one)

- **No `CLAUDE.md` at the project root:** nothing to do (Step 3 already covered the cases where one would help). Say "CLAUDE.md: none (not needed)". Do not create one.
- **A `CLAUDE.md` exists:** keep it - never delete it, never move its content out on your own. Read it and:
  - **Make sure it imports `AGENTS.md`.** If no line reads `@AGENTS.md`, add that line right below the first heading. Without it, Claude Code's default setting skips `AGENTS.md` whenever a `CLAUDE.md` is present. Print `CLAUDE.md: added @AGENTS.md import`.
  - **Drop the outdated claim** "Claude Code reads this file (not `AGENTS.md` directly)" if an earlier version of this plugin's starter put it there (the rest of that note can stay) - it stopped being true with Claude Code v2.1.277.
  - **Otherwise only edit** it if something it already says changed this session. New project knowledge goes into `AGENTS.md` (Claude-only notes in its `## Claude Code` section), not here. Do NOT duplicate information that already lives in `AGENTS.md`, `_tracker.md`, or memory.
- If nothing applies, say "CLAUDE.md: no update needed".

## Step 5: Review Session & Update Memory

- **Always run this step** if the project / user has Claude Code memory configured. Review the full conversation for learnings before deciding whether to write.
- Look for:
  - User corrections ("no, do it this way") - these are feedback memories
  - Confirmed approaches the user validated - also feedback memories
  - New tools, projects, or conventions established - user/project memories
  - Non-obvious gotchas that will recur - project/reference memories
- Apply the **recurrence test**: "Will a future session need this fact?" If no, don't write.
- **Never write:**
  - One-time fixes already applied (config typos, resolved migrations)
  - Info that lives in files future sessions will read anyway (`AGENTS.md`, `CLAUDE.md`, `_tracker.md`)
  - Task status or backlog items (those go in `_tracker.md`)
- **Clean up:** If any existing memories have been resolved by structural fixes (code changes, template updates), delete them.
- **Keep the memory index from overflowing.** Claude loads `MEMORY.md` into every session but truncates it past ~200 lines or ~25KB, whichever comes first - the tail silently drops, so your newest entries can go invisible. Check it (`wc -lc MEMORY.md`); if it's near either limit, compact it. Recall is index-only (Claude has no semantic search over the topic files), so removing an entry *hides* it - only drop entries already covered by an always-loaded rule, or ones trivially self-re-explaining; never drop name spellings, handles, or behavioral corrections. Past ~190 entries you must cut the entry *count*, not just shorten the hooks. Archive removed entries into a `_archive/` subfolder rather than deleting them.
- If memory isn't configured or nothing passes the recurrence test, say "Memory: reviewed session - no new learnings to persist" and move on.

## Step 6: Commit and push the git repos touched this session

**Pre-authorized** by invoking `/wrap-up` - do not ask for confirmation.

Do not sweep the whole filesystem. Commit only the repos that were actually touched in this session.

1. **List the files you edited** this session (Write, Edit, migration files written locally, new source folders, etc.), **including every file wrap-up itself created or edited in Steps 1-4** (`_tracker.md`, `_prd.md`, `AGENTS.md`, `CLAUDE.md`). Those doc updates ship in the same commit as the session's work and are never left dirty: a later session would not count them as its own edits, so nothing else would ever commit them. Do NOT include files that were only read.
2. **Resolve each file's git repo root** with `git -C <dir> rev-parse --show-toplevel 2>/dev/null`. Skip files that error (the path is not inside a git repo - e.g. config writes under `~/.claude/`, files in a non-versioned working directory, files under any cloud-synced directory that isn't a git repo). Collect the unique set of repo roots.
3. **For each repo in that set:**
   - `git -C <repo> status` + `git -C <repo> diff` + `git -C <repo> log -1 --oneline` - sanity check.
   - **Stage only the files YOU edited this session** (wrap-up's own doc writes from Steps 1-4 included). Never `git add -A` / `git add .` - a linter or the user may have modified unrelated files and those aren't yours to commit.
   - **Do NOT stage:** `.env*`, `*.pem`, `*.key`, `credentials.json`, files whose name or content contains `service_role`, `sb_secret_`, `SUPABASE_SERVICE_ROLE_KEY`, `AWS_SECRET_ACCESS_KEY`, or any binary > 10 MB. If a staged file trips this filter on review, `git restore --staged` it and flag to the user.
   - **Commit via HEREDOC** with a concise message summarising this session's changes to that repo. Include the standard Claude Code co-author trailer:
     ```
     Co-Authored-By: Claude <noreply@anthropic.com>
     ```
   - `git -C <repo> push` on the current branch. **Never** `--force`, `--no-verify`, or `--no-gpg-sign`.

**On push failure** (protected branch, non-fast-forward, pre-commit hook): stop that repo, report it in the final summary, continue to the next. Do not retry. Do not force-push.

**Never commit:** anything under `~/.claude/`, or files in directories that are not git repos (cloud-sync folders without a `.git`, scratch dirs, etc.).

If no touched file resolved to a git repo, say "Commit: no git repo was touched this session" - the files from Steps 1-4 still exist on disk, they just aren't versioned.

## Summary

After all steps, give a brief summary:
- For each of `_tracker.md`, `_prd.md`, `AGENTS.md`: created / updated (and why) / no update needed
- `CLAUDE.md`: none / import added / updated / no update needed - plus the one-line-`CLAUDE.md` offer from Step 3 if it applies
- Memory: what was written, or that nothing passed the recurrence test
- What was committed and pushed per touched repo, wrap-up's own doc updates included (or that no git repo was touched)

---

Each appendix below uses a **4-backtick fence** so that any 3-backtick code blocks inside the starter content survive verbatim. When you create a file, copy everything between the 4-backtick markers, substitute the placeholders, and write it out.

## Appendix A - starter `_tracker.md`

> Canonical starter content. `/init-project` uses this same block. When creating the file, substitute `{{PROJECT_NAME}}` and `YYYY-MM-DD` as described in Step 1.

````markdown
# Tracker: {{PROJECT_NAME}}

> One-line entries. Max ~100 lines total. Detail belongs in commit messages or `_prd.md` - not here.

## Done

- [x] YYYY-MM-DD: Project scaffolding

## Next

- [ ] First feature / first deliverable

## Ideas

- [ ] Things to consider later
````

## Appendix B - starter `_prd.md`

> Canonical starter content. `/init-project` uses this same block. Substitute `{{PROJECT_NAME}}`.

````markdown
# PRD: {{PROJECT_NAME}}

> Product requirements: what the project does, who it serves, what's in scope. Updated when scope or architecture changes - not for routine bug fixes.

## Problem

What problem does this project solve? Who has it?

## Users

Who uses this? What roles?

## Scope

### In scope

- Feature 1
- Feature 2

### Out of scope (explicitly)

- Thing we are NOT doing

## Success criteria

How do we know this works? What metric or behavior tells us "done"?

## Architecture

High-level: what runs where, what depends on what. One paragraph or a small diagram.

## Data model

Key entities and relationships. Tables / collections / files.

## Phases

- **Phase 1 (current):** ...
- **Phase 2:** ...
- **Phase 3:** ...
````

## Appendix C - starter `AGENTS.md`

> Canonical starter content. `/init-project` uses this same block. Substitute `{{PROJECT_NAME}}`.

````markdown
# AGENTS.md: {{PROJECT_NAME}}

> Project instructions for any coding agent (Claude Code, Codex, Cursor, Gemini CLI, ...). This is the one instruction file; Claude-only notes go in the `## Claude Code` section at the bottom.

## What this project is

One sentence: what does it do.

## Stack

- Language / runtime:
- Framework:
- Database:
- Deploy target:

## How to run / build / test

```bash
# install
# run dev
# test
# build
# deploy
```

## Conventions

- Style / linter:
- Branch / commit conventions:
- File naming:

## Anything an agent should know before editing

- Gotchas, hidden constraints, things that look wrong but aren't.

## Claude Code

- Preferred model for this project:
- MCP servers used:
- Skills relevant to this project:
- Claude Code reads this file on its own (v2.1.277+). On an older version, or with a `CLAUDE.md` in a parent folder, add a one-line `CLAUDE.md` next to this file containing `@AGENTS.md`.
````
