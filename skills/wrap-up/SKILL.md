---
name: wrap-up
description: End-of-session wrap-up. Commits touched repos, updates _tracker.md / _prd.md / AGENTS.md / CLAUDE.md if useful, and reviews the session for durable memory writes. Invoke explicitly via /wrap-up.
disable-model-invocation: true
---

End-of-session wrap-up. Run all 6 steps below in order.

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

## Step 2: Update Tracker (if useful)

- Read `_tracker.md` at the project root and review what was done this session (from the commits and git log).
- **Move completed items** from "Next" to "Done" with today's date. **Add new items** discovered this session to "Next" or "Ideas". **Reprioritize "Next"** if the session changed what matters most.
- **Entry style — match a git commit subject line:** one line, under ~80 characters, imperative or past-tense, no multi-sentence explanations, no "Verified: ..." tails, no architecture context. If it wouldn't fit on `git log --oneline`, it's too long.
  - Good: `- [x] 2026-05-01: Add OAuth login flow to admin app`
  - Good: `- [x] 2026-05-01: Fix 502 cold-start on /classify endpoint`
  - Bad: `- [x] Auth v1 — M1 hardening: token introspection in _shared/verify-token.js, wired checkAuth() into 3 handlers, secrets moved to vault. Verified: invalid token → 401. (2026-05-01)`
- If a change genuinely needs longer context, write it to memory or the commit message — the tracker is a changelog, not a narrative.
- **Size cap ~100 lines.** Before adding new entries, `wc -l _tracker.md`. If over 100, collapse older multi-line Done entries to one-liners (or fold pure-scaffolding entries into a single "Project scaffolding (YYYY-MM-DD)" line) until back under the cap. Do not delete history — `git log` is the durable record.
- **Skip** if no `_tracker.md` exists or nothing changed worth recording. Say "Tracker: no update needed" and move on.

## Step 3: Update PRD (if useful)

- Read `_prd.md` at the project root and check what changed this session.
- **Only update** if there were substantive changes worth reflecting in the PRD:
  - Phase status changed (e.g., a phase completed)
  - New features or capabilities added
  - Architecture decisions that affect the spec
  - Version bump warranted
- **Skip** for routine bug fixes, style tweaks, or minor refactors, or if no `_prd.md` exists. Say "PRD: no update needed" and move on.

## Step 4: Update AGENTS.md (if useful)

- Read `AGENTS.md` at the project root.
- **Only update** if:
  - A new workflow, command, or tool was added that other agents need to know about
  - Project structure changed (new folders, renamed paths)
  - A rule was established that applies to ALL coding agents, not just Claude
- **Skip** for most sessions, or if no `AGENTS.md` exists. Say "AGENTS.md: no update needed" and move on.

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
- If memory isn't configured or nothing passes the recurrence test, say "Memory: reviewed session — no new learnings to persist" and move on.

## Step 6: Update CLAUDE.md (if useful)

- Read `CLAUDE.md` at the project root.
- **Only update** if:
  - The "Current phase" status line changed
  - New naming conventions or rules were established
  - New domain concepts need documenting
  - Architecture structure changed
- Do NOT duplicate information that already lives in `AGENTS.md`, `_tracker.md`, or memory.
- **Skip** for most sessions, or if no `CLAUDE.md` exists. Say "CLAUDE.md: no update needed" and move on.

## Summary

After all steps, give a brief summary:
- What was committed and pushed per touched repo (or that no git repo was touched)
- Which files were updated (`_tracker.md`, `_prd.md`, `AGENTS.md`, memory, `CLAUDE.md`) and why, or that they were skipped
