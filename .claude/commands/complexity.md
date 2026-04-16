# /complexity - View or Change Project Complexity

Manage the **Complexity** level of the active project. Complexity drives how much
ceremony (plans, diagrams, docs, tests) every other command produces.

## Usage
```
/complexity                          # Show the active project's complexity
/complexity set <simple|medium|complex>   # Change complexity
/complexity explain                  # Explain what each level means
/complexity suggest                  # Ask Claude to suggest a level based on the project
```

## Levels

| Level | Scope | Output |
|-------|-------|--------|
| **Simple** | Quick MVP, script, small tool | Front + Back, 1 diagram, code fast |
| **Medium** | Full-stack app, moderate scale | 5 core plans, auth, DB, tests, basic CI |
| **Complex** | Production SaaS, distributed | All 10 plans, infra, monitoring, security |

## Behavior

### `/complexity`

Read `projects/<active>/PROJECT.md` and show:

```
## Project: <name>
**Complexity**: Medium
**Phase**: Planning

### What this means
- /plan create will produce 5 core plans (architecture, DB, API, frontend, auth)
- /design produces lightweight 4-step output
- /implement expects auth + tests but no full infra
- 3-gate workflow is recommended but not strictly enforced
```

### `/complexity set <level>`

1. Update `PROJECT.md` → `**Complexity**: <level>`
2. Warn if downgrading with existing artifacts (e.g., downgrading to Simple when all 10
   plans already exist):
   ```
   Warning: 10 plans already exist. Downgrading to Simple will ignore them,
   but they won't be deleted. Continue? (y/n)
   ```
3. Confirm the change and show what changes going forward.

### `/complexity suggest`

Analyze PROJECT.md + STATUS.md + discovery notes and recommend a level:

```
Based on:
- Expected DAU: 500
- Features: 3 CRUD entities, 1 form
- Team size: 1 developer
- Deadline: 2 weeks

Suggested complexity: Simple

Reason: small scope, single developer, short timeline — overhead of formal planning
would cost more than it saves.

Apply? (y/n)
```

### `/complexity explain`

Long-form explanation of what each level does:

```
## Simple
- Discovery: 2-3 quick questions, one architecture sketch
- Design: Inline in chat, no formal DESIGN.md
- Plans: None required (optional QUICK-PLAN.md)
- Gates: Skipped. Go from idea → code.
- Tests: Smoke tests only
- Infra: Local dev; add Docker later if needed

## Medium
- Discovery: Architecture + data flow diagrams, ~5 questions
- Design: Lightweight DESIGN.md with API + data model
- Plans: 5 core plans (architecture, DB, API, frontend, auth)
- Gates: Gate 1 recommended, Gate 3 flexible
- Tests: Unit + integration + critical E2E
- Infra: Dockerfile + basic CI

## Complex
- Discovery: Full (stakeholders, user-journeys, risks, MVP, 4 diagrams)
- Design: Full 4-step + ADRs
- Plans: All 10 sub-plans + MASTER-PLAN + IMPLEMENTATION-ROADMAP
- Gates: All 3 gates strictly enforced
- Tests: Full test pyramid + chaos + contract tests
- Infra: Docker + CI/CD + Terraform + monitoring + runbooks
```

## Examples

```
/complexity                     # What am I working on?
/complexity set medium          # Switch to medium
/complexity suggest             # Let Claude pick
/complexity explain             # Show full definitions
```

## Notes

- No active project? The command asks you to run `/project add` first.
- Complexity can be overridden per-command with `--simple`, `--medium`, or `--complex` flags
  on commands like `/design` and `/plan create`.
