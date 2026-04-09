# please-optimize-my-claude

> Optimize your CLAUDE.md hierarchy — deduplicate content, detect misplacement, compress bloat, and visualize token usage across global/project/repo levels.

[![CI](https://github.com/thedoublejay/please-optimize-my-claude/actions/workflows/ci.yml/badge.svg)](https://github.com/thedoublejay/please-optimize-my-claude/actions/workflows/ci.yml)
[![Latest Release](https://img.shields.io/github/v/release/thedoublejay/please-optimize-my-claude)](https://github.com/thedoublejay/please-optimize-my-claude/releases)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

---

## The Problem

Every word in your CLAUDE.md files is injected into every Claude request — silently, on every keystroke.

Most developers start with one global `~/.claude/CLAUDE.md`. That's fine. But as soon as you work across multiple projects or organizations, the hierarchy grows:

```text
~/.claude/CLAUDE.md          ← loaded in EVERY project, for EVERY organization you work in
  org-a/
  ├── CLAUDE.md              ← loaded for all repos in org-a
  ├── repo-a/CLAUDE.md       ← loaded when working in repo-a
  ├── repo-b/CLAUDE.md
  ├── repo-c/CLAUDE.md
  └── repo-d/CLAUDE.md
  org-b/
  ├── CLAUDE.md              ← different org, different conventions
  ├── api/CLAUDE.md
  └── frontend/CLAUDE.md
```

**The hidden cost:** When working in `org-a/repo-a`, Claude loads `~/.claude/CLAUDE.md` + `org-a/CLAUDE.md` + `org-a/repo-a/CLAUDE.md` — all at once, every request. If the same rule appears in all three levels, you're paying for it three times. If your global file has org-specific ticket formats or framework rules that only apply to one project, you're paying for those everywhere.

**For a single global CLAUDE.md:** Even without a monorepo, your global file loads in every project — personal, work, client. Rules that only make sense for one context (a specific commit format, a framework preference, an internal tool) inflate every other session. This skill identifies exactly what should stay global vs. what belongs in a project-level file.

No tool analyzes the full hierarchy and helps you fix it. This skill does.

---

## Why This Skill?

### Key Features

- **Full discovery** — maps every CLAUDE.md, CLAUDE.local.md, `.claude/rules/`, and `@import` reference across all levels
- **Cross-level deduplication** — detects duplicates across hierarchy levels and keeps content at the right one
- **"What Claude Sees" simulator** — shows tokens loaded per directory, including conditional rules
- **Visual token reports** — Unicode bar charts with before/after comparison
- **Misplacement detection** — finds organization-specific content in global, repo-specific content in root, and generic content buried in repo files
- **Security scan** — detects accidentally committed secrets, API keys, internal URLs, credentials, and PII; flags with line numbers and suggests safe replacements
- **Contradiction detection** — catches conflicting instructions with confidence levels ("Definite conflict" vs "Possible tension"), respects path-scoped rules
- **Stale content check** — uses git blame and package.json script verification to find outdated sections
- **Migration mode** — helps split a monolithic CLAUDE.md into a proper hierarchy
- **Auto-generate repo index** — creates a directory map in your root CLAUDE.md for quick navigation
- **Three modes** — interactive (guided review), auto (apply all), analyze (report only)
- **Backup & restore** — reversible changes with scope-matched backups (`~/.claude/backups/` for global, `.claude-md-backup/` for project)
- **Live best practices research** — optionally fetch the latest CLAUDE.md recommendations from the web

---

## Quick Start

**Recommended:** Install via Claude Code native plugin:

```bash
/install thedoublejay/please-optimize-my-claude
```

**Also available** via skills.sh marketplace:

```bash
npx skills add thedoublejay/please-optimize-my-claude
```

> **Note:** This skill is designed for and tested with Claude Code. The `analyze` mode works best in other agents; interactive and auto modes may have limited support.

Then run from any project directory:

```bash
/please-optimize-my-claude
```

---

## Usage

| Command | Mode | Behavior |
|---------|------|----------|
| `/please-optimize-my-claude` | Interactive (default) | Walk through findings with summary-then-batch approach |
| `/please-optimize-my-claude auto` | Auto | Apply all optimizations automatically (prompts for backup first) |
| `/please-optimize-my-claude analyze` | Analyze | Report only — no files modified |
| `/please-optimize-my-claude restore` | Restore | Undo all changes from latest `.claude-md-backup/` |
| Any mode + `--research` | Live research | Also fetch latest best practices from web before analyzing |

### Examples

```bash
# Just see what's wrong — no changes
/please-optimize-my-claude analyze

# Interactive: review findings and decide what to apply
/please-optimize-my-claude

# Auto-apply everything with backup
/please-optimize-my-claude auto

# Undo last optimization
/please-optimize-my-claude restore

# Include live research from Anthropic docs
/please-optimize-my-claude analyze --research
```

---

## How It Works

The skill runs a 5-phase pipeline:

1. **Discovery** — finds all CLAUDE.md, CLAUDE.local.md, `.claude/rules/`, and `@import` references. Resolves symlinks. Strips HTML comments. Counts tokens (word count × 1.5).
2. **Analysis** — runs 9 detectors: duplication, misplacement, bloat, stale content, contradictions, token budget audit, best practices comparison, missing index detection, security scan.
3. **Visual Report** — outputs a Unicode-rich terminal report with hierarchy tree, freshness scores, token bar charts, and "What Claude Sees" per working directory.
4. **Apply** — in interactive mode: summary-then-batch approach. In auto mode: backup first, then apply all. In analyze mode: report only.
5. **Before/After Comparison** — re-counts tokens post-optimization, shows savings as percentages.

---

## Visual Report Examples

### Hierarchy Tree

```text
📁 Instruction Surface
──────────────────────
~/.claude/CLAUDE.md (global)
~/.claude/rules/ (global rules)
│   └── 2 rule files
└── ./CLAUDE.md (project root)
    ├── .claude/rules/ (project rules)
    │   └── 1 rule file (path-scoped: src/**)
    ├── ./api/CLAUDE.md
    ├── ./auth/CLAUDE.md
    ├── ./frontend/CLAUDE.md
    │   └── CLAUDE.local.md (personal, not committed)
    ├── 3 @import references resolved
    └── 9 repos without CLAUDE.md ⚠️
```

### Freshness via git blame

```text
📅 CLAUDE.md Freshness
──────────────────────
Global          last modified 3 days ago
Root            last modified 45 days ago  ⚠️ review recommended
api             last modified 12 days ago
frontend        last modified 2 days ago
```

### Token Usage Bar Chart

```text
📊 Token Usage Per File
───────────────────────
Global          ████░░░░░░░░░░░░░░░░   920 tokens  (budget: 1,000) ✅
Root            ████████████████░░░░ 4,200 tokens  (budget: 3,000) ⚠️ OVER
api             ███████████████░░░░░ 3,800 tokens  (budget: 2,500) ⚠️ OVER
```

### "What Claude Sees" Simulator

```text
📈 Tokens Loaded Per Working Directory
──────────────────────────────────────
Working in ./api/          → 8,920 tokens (CLAUDE.md: 7,400 + rules: 1,200 + local: 320)
Working in ./auth/         → 8,520 tokens (CLAUDE.md: 7,800 + rules: 720)
Working in root ./         → 5,120 tokens (CLAUDE.md: 4,200 + rules: 920)

  Includes: global + root + repo CLAUDE.md, CLAUDE.local.md, unconditional rules
  Path-scoped rules shown separately (load only when matching files are read)
```

### Before/After Comparison

```text
╔══════════════════════════════════════════════════════════════╗
║              ✅ Optimization Complete                        ║
╠══════════════════════════════════════════════════════════════╣

📊 Before vs After
──────────────────
                    BEFORE          AFTER           SAVED
Global              ████  920    →  ███  720     →  -200 (22%)
Root                ████████ 4,200 → █████ 2,800 → -1,400 (33%)
api                 ███████ 3,800  → ████ 2,100  → -1,700 (45%)
─────────────────────────────────────────────────────────────
TOTAL               14,820         → 10,520       → -4,300 (29%)
```

---

## Token Budgets

| Level | Budget | Rationale |
|-------|--------|-----------|
| Global (`~/.claude/CLAUDE.md`) | < 1,000 tokens | Loaded in EVERY project. Must be lean. |
| Project root (`./CLAUDE.md`) | < 3,000 tokens | Loaded for all work in this project. |
| Subdirectory (`<repo>/CLAUDE.md`) | < 2,500 tokens | Loaded when working in that directory. |

Token estimate: word count × 1.5 (HTML comments not counted — Claude Code strips them before injection).

---

## Compatibility

This skill is **designed for and tested with Claude Code**. CLAUDE.md is a Claude Code convention — the hierarchical file discovery, `CLAUDE.local.md`, `.claude/rules/`, and `@import` syntax are all Claude Code features.

| Feature | Claude Code | Other Agents |
|---------|-------------|--------------|
| SKILL.md execution | Full support | Varies by agent |
| `AskUserQuestion` tool | Supported | May not be available — interactive mode may not work |
| `WebSearch` / `WebFetch` tools | Supported | May not be available — `--research` flag may not work |
| `$ARGUMENTS` | Supported | May not be available |
| CLAUDE.md hierarchy concept | Native | Agent may not use CLAUDE.md at all |

**Recommendation:** Use this skill with Claude Code. If you use it with another agent, `analyze` mode works best — read-only, no interactive prompts.

---

## Edge Cases

| Scenario | Behavior |
|----------|----------|
| No CLAUDE.md files found | Shows message and exits gracefully |
| Only global exists | Reports global stats, suggests creating project-level file |
| Git not initialized | Skips stale content check and freshness scoring |
| Large monorepo (50+ files) | Processes all files, shows progress, groups issues by type |
| File is empty | Flags as `[EMPTY]` in report |
| Symlinked CLAUDE.md | Resolves to canonical path, warns before modifying |
| `@import` references | Resolves up to 5 levels deep, tracks effective token count |
| `.claude/rules/` with `paths:` frontmatter | Marks as "conditional" in token simulator |
| CLAUDE.local.md present | Included in analysis, noted as personal/gitignored |
| Backup directory conflict | Always uses ISO timestamp — guaranteed unique |
| No backup found for restore | Shows message: "No backup found. Nothing to restore." |

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on making changes, commit conventions, and the PR process.

Issues: [github.com/thedoublejay/please-optimize-my-claude/issues](https://github.com/thedoublejay/please-optimize-my-claude/issues)

---

## License

MIT — see [LICENSE](LICENSE)

---

## Support

If this skill saves you tokens, Claude credits donations are always welcome — they go straight into running more `/please-optimize-my-claude` sessions.

This skill is provided **as-is**, with no warranty. See [LICENSE](LICENSE) for details.

---

Created by JJ Adonis — Powered by Claude
