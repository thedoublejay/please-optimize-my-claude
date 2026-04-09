# CLAUDE.md Best Practices Reference

This document is the baked-in knowledge base for the please-optimize-my-claude skill.
It is used during Phase 2 analysis to compare user CLAUDE.md files against known best practices.

## 1. Token Budget Guidelines

Every word in CLAUDE.md costs tokens on every interaction. Recommended limits:

| Level | Budget | Rationale |
|-------|--------|-----------|
| Global (~/.claude/CLAUDE.md) | < 1,000 tokens | Loaded in EVERY project. Must be lean. |
| Project root (./CLAUDE.md) | < 3,000 tokens | Loaded for all work in this project. |
| Subdirectory (<repo>/CLAUDE.md) | < 2,500 tokens | Loaded when working in that directory. |

**How to estimate tokens:** word count × 1.5 (Claude averages ~1.3–1.5 tokens/word for English prose, ~1.5–2.0 for code-heavy content. CLAUDE.md files are a mix, so 1.5 is a conservative estimate). Note: HTML block comments (`<!-- ... -->`) are stripped before injection and should not be counted.

**Warning thresholds:**

- 80-100% of budget → "approaching limit"
- 100-150% of budget → "over budget"
- 150%+ of budget → "significantly over budget — audit recommended"

## 2. Hierarchy Design Principles

### What belongs at each level

**Global (~/.claude/CLAUDE.md):**

- Personal engineering preferences (coding style, commit habits)
- Universal behaviors (plan before implementing, read before writing)
- Tool preferences (preferred test runners, editors)
- Communication style preferences
- NOT: organization-specific conventions, project patterns, framework rules

**Project root (./CLAUDE.md):**

- Project-wide conventions (commit format, PR template, branch strategy)
- Shared architecture patterns (if multiple repos share patterns)
- Cross-repo dependency warnings (blast radius)
- Repository index (for monorepos)
- Quality gates (commands per repo type)
- NOT: repo-specific commands, repo-specific architecture, frontend rules in a mostly-backend project

**Subdirectory (<repo>/CLAUDE.md):**

- Repo-specific commands (build, test, lint, deploy)
- Repo-specific architecture (entities, patterns, key files)
- Domain-specific conventions (notification types, AI agent patterns)
- NOT: rules that apply to all repos (those go in root)
- NOT: personal preferences (those go in global)

### The hierarchy loading rule

Claude Code loads all levels simultaneously when working in a subdirectory:
global + root + subdirectory. Content at any level is visible, so duplication wastes tokens.

## 3. Anti-Patterns

### Duplication Across Levels

**Problem:** Same rule written in global, root, AND repo CLAUDE.md.
**Fix:** Keep at the highest appropriate level. Delete from lower levels.

### Organization Content in Global

**Problem:** Project-specific ticket formats (`[PROJ-1234]`), framework conventions, repo names in global.
**Fix:** Move to project root CLAUDE.md. Global should work for ANY project.

### Repo-Specific Content in Root

**Problem:** Frontend-specific rules (Next.js, Tailwind, Shadcn/UI) in root of a 15-repo monorepo.
**Fix:** Move to the specific repo's CLAUDE.md. Add a pointer in root: "See <repo>/CLAUDE.md".

### Generic Advice

**Problem:** "Use TypeScript", "Write tests", "Follow best practices" — obvious and wastes tokens.
**Fix:** Remove. Only include specific, actionable, non-obvious guidance.

### Stale Commands

**Problem:** Documented commands that no longer exist in package.json.
**Fix:** Verify commands exist. Remove or update stale ones.

### Verbose Agent Workflows

**Problem:** 100+ line agent orchestration blocks copy-pasted across repos.
**Fix:** If identical, extract to .claude/rules/ or keep only in one place.

### Process Documentation

**Problem:** "When to update this file" sections, meta-instructions about CLAUDE.md itself.
**Fix:** Keep minimal (2-3 lines max) or remove entirely.

## 4. Recommended Sections by Level

### Global CLAUDE.md

- Core behaviors (3-5 bullet points)
- Problem framing template (if used)
- Git preferences (commit style, no co-authored-by, etc.)
- Code quality defaults (strict typing, no dead code, minimal diffs)

### Project root CLAUDE.md

- Repository map / index (for multi-repo projects)
- Cross-repo dependencies (blast radius)
- Shared conventions (architecture patterns, naming, testing)
- Git/PR conventions (commit format, PR template)
- Quality gates (commands per repo type)
- CI/CD awareness

### Repo CLAUDE.md

- Development commands (build, test, lint, dev server)
- Architecture overview (key directories, patterns)
- Domain-specific patterns
- Key entities / data model
- External dependencies and integrations

## 5. Optimization Techniques

### Link instead of inline

Instead of writing 50 lines about testing patterns, write:
"Testing conventions: see `docs/testing.md`" — 1 line vs 50.

### Use tables over prose

Tables are denser than bullet lists for structured data (commands, types, mappings).

### Prefer bullets over paragraphs

Bullets scan faster and use fewer tokens than equivalent prose.

### Compress code examples

Show the minimal example that illustrates the pattern. Remove boilerplate.
A 3-line example beats a 15-line one if it conveys the same idea.

### Use pointers for single-repo concerns

In root: "Frontend conventions: see [frontend/CLAUDE.md](frontend/CLAUDE.md)"
saves all the frontend content from loading in backend repos.

## 6. Claude Code Loading Behavior

- **Discovery:** Claude walks up the directory tree from cwd, finding CLAUDE.md at each level (stops before filesystem root `/`).
- **Concatenation:** All discovered files are concatenated into context (not overriding — there is no strict override mechanism).
- **Subdirectory loading:** Subdirectory CLAUDE.md files load on demand when Claude reads files there.
- **Precedence:** More specific locations take precedence via recency bias (the last thing Claude reads), but Claude may pick arbitrarily on true conflicts. This is not a hard override engine.
- **Local overrides:** `CLAUDE.local.md` (uppercase, no leading dot) loads after CLAUDE.md at each level (personal notes, gitignored).
- **Rules:** `.claude/rules/*.md` load alongside CLAUDE.md. Path-scoped rules (with `paths:` YAML frontmatter) only load when Claude reads matching files. User-level rules at `~/.claude/rules/` load before project rules.
- **Imports:** `@path/to/file` syntax in CLAUDE.md expands imported content into context (up to 5 levels deep). Supports `@~/` for home directory.
- **Auto-compaction:** When context gets large, Claude Code re-reads CLAUDE.md from disk fresh (survives compaction). Conversation-only instructions are lost.
- **Comment stripping:** Block-level HTML comments are stripped before injection (see token budget section above).

## 7. CLAUDE.md vs Other Files

| File | Purpose | When to use |
|------|---------|-------------|
| `CLAUDE.md` | Standing instructions for Claude in this directory | Conventions, commands, architecture |
| `CLAUDE.local.md` | Personal notes (not committed, gitignored) | Individual preferences, WIP notes |
| `.claude/rules/*.md` | Targeted rules with optional `paths:` frontmatter | Rules that only apply to specific file types |
| `@path/to/file` (in CLAUDE.md) | Import directive — content expanded inline | Modularize large CLAUDE.md files |
| `AGENTS.md` | Multi-agent orchestration instructions | When using custom sub-agents |
| `.claude/settings.json` | Tool permissions, MCP servers | Security and tool configuration |
| `.claude/skills/*/SKILL.md` | Reusable workflows invoked by name | Repeatable processes (deploy, review, etc.) |

**Rule of thumb:** If it applies always → CLAUDE.md. If it applies to specific files → .claude/rules/. If it's a workflow → skill. If it's personal → CLAUDE.local.md.
