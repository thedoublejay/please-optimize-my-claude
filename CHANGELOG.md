# Changelog

All notable changes to this project will be documented here.

Format based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).
Versioning follows [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] — 2026-04-09

### Added

- Full 5-phase optimization pipeline: Discovery → Analysis → Report → Apply → Before/After
- 9 analysis detectors: duplication, misplacement, bloat, stale content, contradictions,
  token budget audit, best-practices comparison, missing repo index, security scan
- Three modes: interactive (summary-then-batch), auto (backup-then-apply), analyze (read-only)
- Restore mode: undo all changes from latest backup
- Backup with scope-matched paths (`~/.claude/backups/` for global, `.claude-md-backup/` for project)
- "What Claude Sees" token simulator per working directory
- Unicode bar chart token reports with before/after comparison
- Migration mode for splitting a monolithic CLAUDE.md into a proper hierarchy
- Auto-generate repo index for monorepos
- Live best-practices research via `--research` flag (fetches Anthropic docs)
- Weekly automated sync of best-practices reference from Anthropic documentation
- Security scan detector (2i): flags accidentally committed secrets, API keys, internal URLs,
  credentials, and PII in CLAUDE.md files with line numbers and safe replacement suggestions.
  Security findings always appear first in the report.
