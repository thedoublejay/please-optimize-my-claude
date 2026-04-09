---
name: please-optimize-my-claude
description: Optimize your CLAUDE.md hierarchy — deduplicate content across global/project/repo levels, detect misplaced sections, compress bloat, check for stale content, and generate visual token usage reports. Use when CLAUDE.md files are bloated, duplicated across levels, or need organization.
tools: Read, Write, Edit, Glob, Grep, Bash, WebSearch, WebFetch, AskUserQuestion
---

# please-optimize-my-claude

Optimize the CLAUDE.md hierarchy in this project — deduplicate content across levels,
detect misplaced sections, compress bloat, and generate visual token reports.

## Arguments

- No arguments → Interactive mode (default)
- `auto` → Apply all optimizations (prompts for backup)
- `analyze` → Report only, no changes
- `restore` → Undo from .claude-md-backup/
- `--research` → Also fetch latest best practices from web

Parse `$ARGUMENTS` to determine mode and flags.

## Pipeline

Execute these phases in order:

### Phase 1: Discovery

1. Read `~/.claude/CLAUDE.md` (global). If not found or permission denied, note and continue.
2. Read `~/.claude/rules/**/*.md` (global rules). Note which have `paths:` frontmatter (conditional loading).
3. Find project root — walk up from cwd looking for `.git/` directory.
4. Read `<project-root>/CLAUDE.md` if it exists.
5. Glob `**/CLAUDE.md` excluding `node_modules/`, `.git/`, `dist/`, `build/`, `vendor/`, `.claude-md-backup/`.
6. Find `CLAUDE.local.md` alongside each CLAUDE.md found.
7. Find `.claude/rules/**/*.md` in the project root and each subdirectory.
8. Parse and resolve `@path/to/file` import references (relative to importing file, up to 5 levels deep).
9. Resolve symlinks — deduplicate by canonical path.
10. Build hierarchy: `{ global, globalRules, root, rootRules, subdirectories: [{claudeMd, localMd, rules, imports}...] }`.
11. Strip HTML block comments (`<!-- ... -->`) before counting (Claude Code strips these before injection).
12. Count tokens per file: word count × 1.5 (averages 1.3–1.5 tokens/word for prose, higher for code).

### Phase 2: Analysis

Run ALL detectors. Collect issues into a list with structure:
`{ type, severity, file, lines, description, suggestion, tokensRecoverable }`

**Detectors to run:**

**2a. Duplication Detection** — Compare content blocks across all hierarchy levels (including `.claude/rules/`, `CLAUDE.local.md`, `@import` content). Flag exact and semantic duplicates (same behavior — removing either loses no unique guidance); identify their lowest common ancestor level.

**2b. Misplacement Detection** — Flag: project-specific patterns (repo names, ticket IDs like `[PROJ-1234]`) in global; repo-specific paths/commands in root; universal rules buried in repo files.

**2c. Bloat Detection** — Flag: sections >300 words, oversized code examples, repeated explanations, workflow blocks duplicated across repos.

**2d. Stale Content Check** — Verify `npm run` commands exist in nearest `package.json`. Flag sections unchanged 90+ days via `git blame`. Check for stale file/directory references.

**2e. Contradiction Detection** — Find conflicting instructions across levels on the same topic.
Distinguish: contradiction (mutually exclusive) vs refinement (one narrows the other) vs exception
(one scopes out a subset). Only flag actual contradictions. Respect `paths:` frontmatter scoping —
rules in different path scopes cannot contradict. Present with confidence levels.

**2f. Token Budget Audit** — Flag files exceeding budgets (global <1,000, root <3,000, repo <2,500).
Calculate "What Claude Sees" per working directory: all of CLAUDE.md (ancestor levels),
CLAUDE.local.md, rules (unconditional), rules (path-conditional, shown separately), and @import-expanded content.

**2g. Best Practices Comparison** — Use Glob to find `**/please-optimize-my-claude/references/best-practices.md`, then Read it for the reference. Check for missing recommended sections, anti-patterns, structure issues. With `--research`, also fetch latest recommendations via WebSearch/WebFetch.

**2h. Missing Index Detection** — Check if root CLAUDE.md has a repository/directory index. If
missing, prepare an auto-generated index table (repo name, type, purpose, CLAUDE.md link).

**2i. Security Scan** — Scan all CLAUDE.md files for accidentally committed sensitive content. Flag with severity `critical`:

- Secrets and tokens: patterns matching `sk-`, `ghp_`, `xoxb-`, `AKIA` (AWS), `Bearer`, base64 blobs >40 chars
- Private URLs/endpoints: internal hostnames, IP addresses, `localhost` with ports, VPN/internal domains
- Credentials: lines matching `password`, `secret`, `api_key`, `token`, `private_key` followed by `=` or `:` and a non-placeholder value
- Environment variable values: `ENV_VAR=actual_value` patterns (flag the value, not the variable name)
- PII patterns: email addresses, phone numbers embedded in examples

For each finding: show the file, line number, matched pattern, and a suggested replacement (e.g., redact to `<your-api-key>` or remove the line entirely). Security issues are always shown first in the report, before all other findings. In auto mode, prompt separately before removing security findings — never silently delete them.

### Phase 3: Report

Output the visual report to the user. Use Unicode box drawing characters (─│┌┐└┘├┤┬┴┼),
block elements (█░), and status indicators (✅ ⚠️).

Include ALL report sections:

- **Hierarchy tree** — including CLAUDE.local.md, .claude/rules/, @imports, symlink indicators
- **CLAUDE.md freshness** — via git blame: last modified date per file, flag unchanged for 45+ days
- **Token usage bar chart** — bar width proportional to tokens, max 20 chars wide
- **"What Claude Sees" simulator** — full instruction surface per working directory: CLAUDE.md (all ancestor levels), CLAUDE.local.md, rules (unconditional), rules (path-conditional, separately), and @import-expanded content
- **Issues summary** — by type with token counts and total estimated savings
- **Version footer** — current installed version

### Phase 4: Apply (mode-dependent)

**Interactive:** Show the full report first, then use AskUserQuestion once with options:
"Apply all", "Pick categories to apply", "Review each finding individually", "Skip (report only)".
If reviewing individually, show each issue with a diff preview and ask:
"Apply? [y]es / [n]o / [s]kip all of this type / [q]uit".

**Auto:** Prompt once for backup confirmation. Backup project files to
`.claude-md-backup/<ISO-8601-timestamp>/` (preserving relative paths) and global files to
`~/.claude/backups/<ISO-8601-timestamp>/`. Auto-add `.claude-md-backup/` to project `.gitignore`
if not present. Keep last 5 backups. Apply all optimizations. Show Phase 5 comparison.

**Analyze:** Stop after Phase 3. Do not modify any files.

**Restore:** Find the latest directory in `.claude-md-backup/`. List files to restore.
Confirm with user. Copy files back to original locations.

### Phase 5: Before/After Comparison

After applying optimizations, re-count tokens for all modified files.
Show the before/after comparison with bar charts and percentage reduction.
Show backup path and restore command.

### Migration Mode

If discovery finds only 1 CLAUDE.md file AND it exceeds 3,000 tokens, offer to
split it into a hierarchy. Identify content categories (global preferences, project
conventions, repo-specific details) and propose a split plan.

## Important Rules

- NEVER delete content without user approval (even in auto mode, the backup is mandatory)
- When deduplicating, keep content at the HIGHEST appropriate level (global > root > repo)
- When in doubt about whether something is a duplicate, flag it for manual review
- Always show token impact for every suggested change
- The visual report is the primary output — make it clear and scannable
- Analyze the FULL instruction surface: CLAUDE.md + CLAUDE.local.md + .claude/rules/ + @imports at every level
- Resolve symlinks before modifying files — warn if a file is symlinked
- Backup global files to `~/.claude/backups/`, project files to `.claude-md-backup/`
- For contradictions, distinguish "definite conflict" from "possible tension" — respect `paths:` scoping
