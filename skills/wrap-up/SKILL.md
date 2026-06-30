---
name: wrap-up
description: End-of-session wrap-up. Commits the git repos you touched, ensures _tracker.md, _prd.md, AGENTS.md, and CLAUDE.md exist (auto-creates them from the inline starters in this skill if missing) and updates them, and reviews the session for durable memory writes. Invoke explicitly via /wrap-up.
disable-model-invocation: true
---

End-of-session wrap-up. Run all 6 steps below in order.

**Mandatory files.** After `/wrap-up` runs, all four of these MUST exist at the project root: `_tracker.md`, `_prd.md`, `AGENTS.md`, `CLAUDE.md`. Steps 2, 3, 4, and 6 auto-create any that are missing — using the starter content in Appendices A–D at the bottom of this skill — then proceed to update them. They never skip on the grounds that the file is absent.

**Project root** = the output of `git -C . rev-parse --show-toplevel` if the current working directory is inside a git repo; otherwise the current working directory itself.

**One project or many.** This skill only ever touches the project you're currently in — it's fine if that's your only one. If the project is not a git repo, Step 1 commits nothing (that's expected) and Steps 2–6 still run and create/maintain the four files.

## Step 1: Commit and push the git repos touched this session

**Pre-authorized** by invoking `/wrap-up` — do not ask for confirmation.

Do not sweep the whole filesystem. Commit only the repos that were actually touched in this session.

1. **List the files you edited** this session (Write, Edit, migration files written locally, new source folders, etc.). Do NOT include files that were only read.
2. **Resolve each file's git repo root** with `git -C <dir> rev-parse --show-toplevel 2>/dev/null`. Skip files that error (the path is not inside a git repo — e.g. config writes under `~/.claude/`, files in a non-versioned working directory, files under any cloud-synced directory that isn't a git repo). Collect the unique set of repo roots.
3. **For each repo in that set:**
   - `git -C <repo> status` + `git -C <repo> diff` + `git -C <repo> log -1 --oneline` — sanity check.
   - **Stage only the files YOU edited this session.** Never `git add -A` / `git add .` — a linter or the user may have modified unrelated files and those aren't yours to commit.
   - **Do NOT stage:** `.env*`, `*.pem`, `*.key`, `credentials.json`, files whose name or content contains `service_role`, `sb_secret_`, `SUPABASE_SERVICE_ROLE_KEY`, `AWS_SECRET_ACCESS_KEY`, or any binary > 10 MB. If a staged file trips this filter on review, `git restore --staged` it and flag to the user.
   - **Commit via HEREDOC** with a concise message summarising this session's changes to that repo. Include the standard Claude Code co-author trailer:
     ```
     Co-Authored-By: Claude <noreply@anthropic.com>
     ```
   - `git -C <repo> push` on the current branch. **Never** `--force`, `--no-verify`, or `--no-gpg-sign`.

**On push failure** (protected branch, non-fast-forward, pre-commit hook): stop that repo, report it in the final summary, continue to the next. Do not retry. Do not force-push.

**Never commit:** anything under `~/.claude/`, or files in directories that are not git repos (cloud-sync folders without a `.git`, scratch dirs, etc.).

If no touched file resolved to a git repo, say "Commit: no git repo was touched this session" and move on — the remaining steps still run.

## Step 2: Update Tracker (mandatory)

- **Required file: `_tracker.md` at the project root.** If it does not exist, create it now from **Appendix A** below. When seeding:
  - Replace `{{PROJECT_NAME}}` with the basename of the project root.
  - Replace the placeholder date `YYYY-MM-DD` in the seed "Project scaffolding" entry with today's date.
  - Print `_tracker.md: created` and continue with the update steps below.
- Read `_tracker.md` and review what was done this session (from the commits and git log).
- **Move completed items** from "Next" to "Done" with today's date. **Add new items** discovered this session to "Next" or "Ideas". **Reprioritize "Next"** if the session changed what matters most.
- **Entry style — match a git commit subject line:** one line, under ~80 characters, imperative or past-tense, no multi-sentence explanations, no "Verified: ..." tails, no architecture context. If it wouldn't fit on `git log --oneline`, it's too long.
  - Good: `- [x] 2026-05-01: Add OAuth login flow to admin app`
  - Good: `- [x] 2026-05-01: Fix 502 cold-start on /classify endpoint`
  - Bad: `- [x] Auth v1 — M1 hardening: token introspection in _shared/verify-token.js, wired checkAuth() into 3 handlers, secrets moved to vault. Verified: invalid token → 401. (2026-05-01)`
- If a change genuinely needs longer context, write it to memory or the commit message — the tracker is a changelog, not a narrative.
- **Size cap ~100 lines.** Before adding new entries, `wc -l _tracker.md`. If over 100, collapse older multi-line Done entries to one-liners (or fold pure-scaffolding entries into a single "Project scaffolding (YYYY-MM-DD)" line) until back under the cap. Do not delete history — `git log` is the durable record.
- **Never skip on the grounds that the file is missing** — create it. The only "no update needed" path is when `_tracker.md` already exists AND nothing this session is worth recording. Say "Tracker: no update needed" only in that case.

## Step 3: Update PRD (mandatory)

- **Required file: `_prd.md` at the project root.** If it does not exist, create it now from **Appendix B** below. When seeding:
  - Replace `{{PROJECT_NAME}}` with the basename of the project root.
  - Print `_prd.md: created` and continue with the update check below.
  - Flag to the user: "Seed PRD created — fill in Problem, Users, Scope, and Success criteria when you have a moment."
- Read `_prd.md` and check what changed this session.
- **Only edit** the file if there were substantive changes worth reflecting in the PRD:
  - Phase status changed (e.g., a phase completed)
  - New features or capabilities added
  - Architecture decisions that affect the spec
  - Version bump warranted
- **Never skip on the grounds that the file is missing** — create it. For routine bug fixes, style tweaks, or minor refactors, you may say "PRD: no update needed" — but the file must exist after this step.

## Step 4: Update AGENTS.md (mandatory)

- **Required file: `AGENTS.md` at the project root.** If it does not exist, create it now from **Appendix C** below, replacing `{{PROJECT_NAME}}` with the basename of the project root. Print `AGENTS.md: created` and continue.
- Read `AGENTS.md`.
- **Only edit** the file if:
  - A new workflow, command, or tool was added that other agents need to know about
  - Project structure changed (new folders, renamed paths)
  - A rule was established that applies to ALL coding agents, not just Claude
- **Never skip on the grounds that the file is missing** — create it. If nothing this session warrants an edit, say "AGENTS.md: no update needed" — but the file must exist after this step.

## Step 5: Review Session & Update Memory

- **Always run this step** if the project / user has Claude Code memory configured. Review the full conversation for learnings before deciding whether to write.
- Look for:
  - User corrections ("no, do it this way") — these are feedback memories
  - Confirmed approaches the user validated — also feedback memories
  - New tools, projects, or conventions established — user/project memories
  - Non-obvious gotchas that will recur — project/reference memories
- Apply the **recurrence test**: "Will a future session need this fact?" If no, don't write.
- **Never write:**
  - One-time fixes already applied (config typos, resolved migrations)
  - Info that lives in files future sessions will read anyway (`AGENTS.md`, `CLAUDE.md`, `_tracker.md`)
  - Task status or backlog items (those go in `_tracker.md`)
- **Clean up:** If any existing memories have been resolved by structural fixes (code changes, template updates), delete them.
- **Keep the memory index from overflowing.** Claude loads `MEMORY.md` into every session but truncates it past ~200 lines or ~25KB, whichever comes first - the tail silently drops, so your newest entries can go invisible. Check it (`wc -lc MEMORY.md`); if it's near either limit, compact it. Recall is index-only (Claude has no semantic search over the topic files), so removing an entry *hides* it - only drop entries already covered by an always-loaded rule, or ones trivially self-re-explaining; never drop name spellings, handles, or behavioral corrections. Past ~190 entries you must cut the entry *count*, not just shorten the hooks. Archive removed entries into a `_archive/` subfolder rather than deleting them.
- If memory isn't configured or nothing passes the recurrence test, say "Memory: reviewed session — no new learnings to persist" and move on.

## Step 6: Update CLAUDE.md (mandatory)

- **Required file: `CLAUDE.md` at the project root.** If it does not exist, create it now from **Appendix D** below, replacing `{{PROJECT_NAME}}` with the basename of the project root. Print `CLAUDE.md: created` and continue. (The starter `CLAUDE.md` is intentionally short — it imports `AGENTS.md` via `@AGENTS.md` and holds only Claude-specific notes.)
- Read `CLAUDE.md`.
- **Only edit** the file if:
  - The "Current phase" status line changed
  - New naming conventions or rules were established
  - New domain concepts need documenting
  - Architecture structure changed
- Do NOT duplicate information that already lives in `AGENTS.md`, `_tracker.md`, or memory.
- **Never skip on the grounds that the file is missing** — create it. If nothing this session warrants an edit, say "CLAUDE.md: no update needed" — but the file must exist after this step.

## Summary

After all steps, give a brief summary:
- What was committed and pushed per touched repo (or that no git repo was touched)
- For each of `_tracker.md`, `_prd.md`, `AGENTS.md`, `CLAUDE.md`: created / updated (and why) / no update needed
- Memory: what was written, or that nothing passed the recurrence test

---

Each appendix below uses a **4-backtick fence** so that any 3-backtick code blocks inside the starter content survive verbatim. When you create a file, copy everything between the 4-backtick markers, substitute the placeholders, and write it out.

## Appendix A — starter `_tracker.md`

> Canonical starter content. `/init-project` uses this same block. When creating the file, substitute `{{PROJECT_NAME}}` and `YYYY-MM-DD` as described in Step 2.

````markdown
# Tracker — {{PROJECT_NAME}}

> One-line entries. Max ~100 lines total. Detail belongs in commit messages or `_prd.md` — not here.

## Done

- [x] YYYY-MM-DD: Project scaffolding

## Next

- [ ] First feature / first deliverable

## Ideas

- [ ] Things to consider later
````

## Appendix B — starter `_prd.md`

> Canonical starter content. `/init-project` uses this same block. Substitute `{{PROJECT_NAME}}`.

````markdown
# PRD — {{PROJECT_NAME}}

> Product requirements: what the project does, who it serves, what's in scope. Updated when scope or architecture changes — not for routine bug fixes.

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

## Appendix C — starter `AGENTS.md`

> Canonical starter content. `/init-project` uses this same block. Substitute `{{PROJECT_NAME}}`.

````markdown
# AGENTS.md — {{PROJECT_NAME}}

> Project instructions for any coding agent (Claude Code, Codex, Cursor, Gemini CLI, …). Universal context lives here. Claude-specific config lives in `CLAUDE.md`.

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
````

## Appendix D — starter `CLAUDE.md`

> Canonical starter content. `/init-project` uses this same block. Substitute `{{PROJECT_NAME}}`.

````markdown
# CLAUDE.md — {{PROJECT_NAME}}

@AGENTS.md

> Claude Code reads this file (not `AGENTS.md` directly). The `@AGENTS.md` line above imports it. Add Claude-specific config below — MCP servers, skills, hooks, anything that only applies to Claude Code.

## Current phase

What stage is the project in right now? (e.g. "Phase 2 — building the import pipeline".)

## Claude-specific notes

- Preferred model for this project:
- MCP servers used:
- Skills relevant to this project:
````
