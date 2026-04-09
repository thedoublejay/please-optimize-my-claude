# Contributing to please-optimize-my-claude

## Getting Started

1. Fork the repo
2. Clone: `git clone https://github.com/<your-fork>/please-optimize-my-claude.git`
3. Install locally: `npx skills add ./`
4. Test by running `/please-optimize-my-claude analyze` in a project with CLAUDE.md files

## Making Changes

- Edit `skills/please-optimize-my-claude/SKILL.md` for skill logic changes
- Edit `skills/please-optimize-my-claude/references/best-practices.md` for best practices updates
- All Markdown files must pass markdownlint (`npx markdownlint-cli2 "**/*.md"`)

## Commit Convention

- `feat: description` — new features
- `fix: description` — bug fixes
- `docs: description` — documentation updates
- `chore: description` — maintenance

## Pull Request Process

1. Create a branch from `main`
2. Make your changes
3. Ensure markdownlint passes
4. Open a PR with a clear description
5. Wait for review

## Reporting Issues

- Use [GitHub Issues](https://github.com/thedoublejay/please-optimize-my-claude/issues)
- Include what you expected, what happened, and your CLAUDE.md hierarchy structure

## Development Setup

No build step required — the skill is pure Markdown.

1. Fork and clone the repo
2. Install locally: `npx skills add ./` (or `/install ./` in Claude Code)
3. Test: run `/please-optimize-my-claude analyze` in any project with CLAUDE.md files

## Testing Your Changes

**Skill logic changes** (`SKILL.md`): Run all four modes against a project with a real CLAUDE.md hierarchy:

- `/please-optimize-my-claude analyze` — read-only, safe to run anywhere
- `/please-optimize-my-claude` — interactive mode
- `/please-optimize-my-claude auto` — applies changes (use a throwaway project)
- `/please-optimize-my-claude restore` — verify restore works after auto

**Best-practices changes** (`references/best-practices.md`): Run `/please-optimize-my-claude analyze --research`
and verify the reference is loaded without errors.

**Linting:** Always run before opening a PR:

```bash
npx markdownlint-cli2 "**/*.md"
```

## What Makes a Good PR

- One logical change per PR
- Link the issue it closes (`Closes #123`)
- Update `CHANGELOG.md` for any user-facing changes
- Keep diffs minimal — targeted edits over rewrites

## License

By contributing, you agree that your contributions will be licensed under MIT.
