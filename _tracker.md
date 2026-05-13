# Tracker: claude-code-wrap-up-plugin

> One-line entries. Max ~100 lines total. Detail belongs in commit messages or `_prd.md` — not here.

## Done

- [x] 2026-05-01: v1.0.0 — initial release: `/wrap-up` skill, marketplace + plugin manifests, README, MIT license
- [x] 2026-05-12: v1.1.0 — add `/init-project` skill + starter templates in `templates/`
- [x] 2026-05-12: v1.2.0 — make `_tracker.md` and `_prd.md` mandatory in `/wrap-up` (auto-create if missing)
- [x] 2026-05-12: Fix README — plugin update needs two commands (`marketplace update` then `plugin update`)
- [x] 2026-05-12: v1.3.0 — make `AGENTS.md` + `CLAUDE.md` mandatory too; inline starters into `wrap-up` Appendices A–D; remove `templates/`; repo dogfoods its own convention; README states plugin-vs-skill + benefits
- [x] 2026-05-12: Attribution — LICENSE/plugin.json/marketplace.json/README/AGENTS.md now carry `tschenkster (Thomas Schenkelberg | linkedin.com/in/thomas-schenkelberg)`
- [x] 2026-05-12: Rename repo `claude-code-wrap-up-skill` → `claude-code-wrap-up-plugin`; README rewritten for non-technical / business readers
- [x] 2026-05-13: Transfer repo to `thomas-schenkelberg` GitHub Org; install URL is now `thomas-schenkelberg/...`; old URL redirects
- [x] 2026-05-13: Move local clone from `~/Code/` to `15-tools/`; register in `15-tools/REGISTRY-TOOLS.md`

## Next

- [ ] Watch for Claude Code plugin-schema changes (marketplace.json / plugin.json) and the `/plugin update` UX

## Ideas

- [ ] Optional `allowed-tools` frontmatter on the skills (scope what each can call)
- [ ] `/wrap-up --dry-run` mode: report what it would commit / create / update without doing it
- [ ] Optional pre-commit "run tests" step gated on `package.json` / `pyproject.toml`
