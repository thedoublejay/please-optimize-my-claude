# please-optimize-my-claude — Benchmark Report

> Generated on 2026-04-09 against a synthetic 4-repo organization with intentionally
> bloated, duplicated CLAUDE.md files. This demonstrates the skill's analysis and
> optimization capabilities.

## Test Setup

- **Org:** Fake "Mavs Org" with 4 repos (backend-api, frontend-app, shared-lib, infra)
- **Files:** 5 CLAUDE.md files + 4 `.claude/rules/*.md` files (9 total)
- **Seeded problems:** Copy-pasted code style/testing/git sections, misplaced database conventions in a library repo, Jest standards in a Kubernetes repo, stale migration notes, version contradictions, redundant rules files

---

## Hierarchy Tree

```text
~/.claude/CLAUDE.md                          (global)
└─ CLAUDE.md                                 (project root)
   ├─ backend-api/
   │  ├─ CLAUDE.md
   │  └─ .claude/rules/
   │     ├─ code-style.md       [globs: **/*.ts]
   │     └─ testing.md          [globs: **/*.spec.ts, **/*.test.ts]
   ├─ frontend-app/
   │  ├─ CLAUDE.md
   │  └─ .claude/rules/
   │     ├─ components.md       [globs: src/components/**/*.tsx]
   │     └─ testing.md          [globs: **/*.spec.tsx, **/*.test.tsx]
   ├─ shared-lib/
   │  └─ CLAUDE.md
   └─ infra/
      └─ CLAUDE.md

9 files total │ 0 CLAUDE.local.md │ 4 conditional rules │ 0 @imports
```

---

## Token Usage (Before)

Tokens estimated as: word count × 1.5

| File | Words | Tokens | Bar |
|------|------:|-------:|-----|
| `~/.claude/CLAUDE.md` (global) | 340 | 510 | `████████░░░░░░░░░░░░` |
| `CLAUDE.md` (root) | 638 | 957 | `██████████████████░░` |
| `backend-api/CLAUDE.md` | 682 | 1,023 | `████████████████████` |
| `frontend-app/CLAUDE.md` | 656 | 984 | `███████████████████░` |
| `shared-lib/CLAUDE.md` | 394 | 591 | `███████████░░░░░░░░░` |
| `infra/CLAUDE.md` | 382 | 573 | `███████████░░░░░░░░░` |
| `backend-api` rules (×2) | 117 | 176 | `███░░░░░░░░░░░░░░░░░` |
| `frontend-app` rules (×2) | 109 | 164 | `███░░░░░░░░░░░░░░░░░` |
| **TOTAL (project)** | **2,978** | **4,468** | |

### Budget Check (Before)

| Level | Actual | Budget | Status |
|-------|-------:|-------:|--------|
| Global | 510 | 1,000 | ✅ |
| Root | 957 | 3,000 | ✅ |
| backend-api (CLAUDE.md + rules) | 1,199 | 2,500 | ✅ |
| frontend-app (CLAUDE.md + rules) | 1,148 | 2,500 | ✅ |
| shared-lib | 591 | 2,500 | ✅ |
| infra | 573 | 2,500 | ✅ |

---

## "What Claude Sees" Simulator (Before)

### Working in `backend-api/`

| Layer | Tokens |
|-------|-------:|
| `~/.claude/CLAUDE.md` (global) | 510 |
| `CLAUDE.md` (root) | 957 |
| `backend-api/CLAUDE.md` | 1,023 |
| `rules/code-style.md` (*.ts) | 74 |
| `rules/testing.md` (*.spec.ts) | 102 |
| **TOTAL** | **2,666** |

### Working in `frontend-app/`

| Layer | Tokens |
|-------|-------:|
| `~/.claude/CLAUDE.md` (global) | 510 |
| `CLAUDE.md` (root) | 957 |
| `frontend-app/CLAUDE.md` | 984 |
| `rules/components.md` (*.tsx) | 81 |
| `rules/testing.md` (*.spec.tsx) | 83 |
| **TOTAL** | **2,615** |

### Working in `shared-lib/`

| Layer | Tokens |
|-------|-------:|
| `~/.claude/CLAUDE.md` (global) | 510 |
| `CLAUDE.md` (root) | 957 |
| `shared-lib/CLAUDE.md` | 591 |
| **TOTAL** | **2,058** |

### Working in `infra/`

| Layer | Tokens |
|-------|-------:|
| `~/.claude/CLAUDE.md` (global) | 510 |
| `CLAUDE.md` (root) | 957 |
| `infra/CLAUDE.md` | 573 |
| **TOTAL** | **2,040** |

---

## Issues Found

**27 issues** | Est. savings: **~2,020 tokens (45% of project total)**

| Category | Count | Tokens Saved |
|----------|------:|-------------:|
| Duplication | 9 | ~1,360 |
| Misplacement | 5 | ~340 |
| Stale | 5 | ~320 |
| Contradiction | 1 | — |
| Security | 1 | — |
| Missing sections | 2 | (additive) |
| Bloat | 4 | (overlap w/ duplication) |

### Security Scan

| ID | Severity | File | Finding | Suggestion |
|----|----------|------|---------|------------|
| SEC-1 | ⚠️ Low | `infra/CLAUDE.md:69` | Internal URL: `https://datadog.acme.internal/infra` | Replace with `<datadog-dashboard-url>` or keep if intentional |

No secrets, tokens, credentials, or PII detected.

### Duplication (9 issues)

| ID | Finding | Files Affected | Savings |
|----|---------|---------------:|--------:|
| DUP-1 | **Code Style** — 14 rules duplicated verbatim | 5 CLAUDE.md + 2 rules | ~500 |
| DUP-2 | **Testing Standards** — structure, naming, coverage duplicated | 5 CLAUDE.md + 2 rules | ~400 |
| DUP-3 | **Git Conventions** — 5-7 rules duplicated | 5 CLAUDE.md | ~300 |
| DUP-4 | **Security** — base rules duplicated | 4 CLAUDE.md | ~120 |
| DUP-5 | **Database Conventions** — duplicated in root + backend + shared-lib | 3 CLAUDE.md | ~100 |
| DUP-6 | **API Conventions** — duplicated in root + backend + shared-lib | 3 CLAUDE.md | ~75 |
| DUP-7 | **Deployment** — general rules duplicated in root + infra | 2 CLAUDE.md | ~50 |
| DUP-8 | **Monitoring** — general rules duplicated in root + infra | 2 CLAUDE.md | ~50 |
| DUP-9 | **"Never commit .env"** — repeated in 3 files | 3 CLAUDE.md | ~15 |

### Misplacement (5 issues)

| ID | Finding | File | Fix |
|----|---------|------|-----|
| MIS-1 | Database Conventions in a library repo | `shared-lib/CLAUDE.md:86-92` | Remove entirely |
| MIS-2 | API Conventions in a library repo | `shared-lib/CLAUDE.md:94-100` | Remove entirely |
| MIS-3 | "Input validation on endpoints" in a library | `shared-lib/CLAUDE.md:106` | Remove |
| MIS-4 | Jest Testing Standards in a K8s/Helm repo | `infra/CLAUDE.md:71-89` | Remove |
| MIS-5 | TypeScript strict rules in a K8s/Helm repo | `infra/CLAUDE.md:9-13` | Replace with YAML conventions |

### Stale Content (5 issues)

| ID | Finding | File | Age |
|----|---------|------|-----|
| STALE-1 | "OLD MIGRATION NOTES (Completed March 2024)" | `root:127-137` | 2+ years |
| STALE-2 | "TODO: Future Improvements" (Q3 2025 is past) | `root:139-144` | Outdated |
| STALE-3 | "COMPLETED: Design System Migration (Feb 2025)" | `frontend-app:153-157` | 14 months |
| STALE-4 | "HISTORICAL: Cluster Migration (Nov 2024)" | `infra:100-105` | 17 months |
| STALE-5 | "Known Issues (Last Updated: January 2025)" | `backend-api:172-176` | 15 months |

### Contradiction (1 issue)

| ID | Finding | Confidence |
|----|---------|------------|
| CONTRA-1 | Root says "NestJS 10+" but backend-api says "NestJS 11". Root also has a TODO to "migrate to NestJS 11" — already done. | HIGH |

### Missing Sections (2 issues)

| ID | Finding | Fix |
|----|---------|-----|
| MISS-1 | No repository index table in root | Add repo table with type, stack, purpose |
| MISS-2 | No quality gates section in root | Add per-repo-type checklist |

---

## Before / After Comparison

| File | Before | After | Saved | Change |
|------|-------:|------:|------:|-------:|
| `CLAUDE.md` (root) | 957 | 922 | 35 | −3.7% |
| `backend-api/CLAUDE.md` | 1,023 | 558 | 465 | −45.5% |
| `frontend-app/CLAUDE.md` | 984 | 567 | 417 | −42.4% |
| `shared-lib/CLAUDE.md` | 591 | 205 | 386 | −65.3% |
| `infra/CLAUDE.md` | 573 | 234 | 339 | −59.2% |
| `backend-api` rules (×2) | 176 | 0 | 176 | DELETED |
| `frontend-app` rules (×2) | 164 | 0 | 164 | DELETED |
| **TOTAL** | **4,468** | **2,486** | **1,982** | **−44.4%** |

```text
Before  ████████████████████  4,468 tokens
After   ███████████░░░░░░░░░  2,486 tokens
Saved   ░░░░░░░░░█████████░░  1,982 tokens (44.4%)
```

### "What Claude Sees" — Before vs After

| Working Dir | Before | After | Change |
|-------------|-------:|------:|-------:|
| `backend-api/` | 2,666 | 1,990 | −25.4% |
| `frontend-app/` | 2,615 | 1,999 | −23.6% |
| `shared-lib/` | 2,058 | 1,637 | −20.5% |
| `infra/` | 2,040 | 1,666 | −18.3% |

---

## What Changed

- **Deduplicated:** Code Style, Testing Standards, Git Conventions, Security, Database, API, Deployment, Monitoring, Env Vars — all kept in root, repos reference only unique additions
- **Removed misplaced content:** Database/API conventions from shared-lib (it's a library), Jest testing from infra (it's Kubernetes YAML), TypeScript rules from infra (mostly YAML repo)
- **Removed stale content:** 5 historical/completed sections totaling ~320 tokens
- **Fixed contradiction:** Removed stale "NestJS 10+" claim (backend-api is already on 11)
- **Deleted 4 redundant rules files** that were 100% subsets of root + parent CLAUDE.md
- **Added missing sections:** Repository index table, Quality gates checklist

---

## Restore

```bash
# Backup location
.claude-md-backup/2026-04-09T14:40:24Z/

# Restore command
/please-optimize-my-claude restore
```
