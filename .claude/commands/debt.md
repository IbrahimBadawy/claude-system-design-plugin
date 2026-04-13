# /debt - Technical Debt Management

Audit, classify, and create a remediation plan for technical debt.

## Usage
```
/debt <system>                      # Full tech debt audit
/debt track                         # Show current debt backlog
```

## Debt Classification (Martin Fowler's Quadrant)

|  | Reckless | Prudent |
|--|---------|---------|
| **Deliberate** | "We don't have time for design" | "We know this is debt, shipping now, fixing in sprint 3" |
| **Inadvertent** | "What's layering?" | "Now we know how we should have done it" |

## Audit Categories

| Category | What to Check | Tools |
|----------|--------------|-------|
| Code Quality | Complexity, duplication, smells | SonarQube, CodeClimate |
| Dependencies | Outdated, vulnerable, unused | Snyk, npm audit, Dependabot |
| Architecture | Boundary violations, coupling | Custom fitness functions |
| Testing | Coverage gaps, flaky tests | Istanbul, pytest-cov |
| Security | Known vulnerabilities, weak configs | Snyk, OWASP ZAP |
| Documentation | Outdated docs, missing docs | `/docs status` |
| Performance | Slow queries, memory leaks | Profiling tools |
| Infrastructure | Manual processes, config drift | IaC audit |

## Debt Tracking Format
```markdown
| ID | Category | Description | Severity | Effort | Impact | Added | Fix-By |
|----|----------|-------------|----------|--------|--------|-------|--------|
| TD-001 | Code | Duplicated auth logic in 3 services | High | M | High | 2026-04 | 2026-05 |
| TD-002 | Deps | React 18 -> 19 migration needed | Medium | L | Medium | 2026-04 | 2026-06 |
```

## Remediation Rules
- **20% rule**: Allocate ~20% of each sprint to debt reduction
- **Debt ceiling**: CI fails if coverage < 70% or lint errors > N
- **Boy Scout Rule**: Leave code cleaner than you found it
- **No new debt without ticket**: Every shortcut creates a tracked debt item

## Examples
```
/debt university-system
/debt track
```
