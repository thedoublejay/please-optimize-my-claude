# Security Policy

## Reporting a Vulnerability

**Do not open a public GitHub issue for security vulnerabilities.**

Email: <work@josejai.me>
Subject line: `[SECURITY] please-optimize-my-claude — <brief description>`

You can expect:

- **Acknowledgment** within 48 hours
- **Status update** within 7 days (confirmed, not reproducible, or fix in progress)
- **Credit** in the release notes if you'd like it

## Scope

This skill runs entirely locally — it reads and optionally modifies CLAUDE.md files on your machine.
It does not transmit data to any external service except when `--research` is used,
which fetches public Anthropic documentation via `WebFetch`.

## Supported Versions

Only the latest release receives security fixes. Always use the latest version.
