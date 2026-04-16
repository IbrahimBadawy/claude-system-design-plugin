---
description: After every implementation milestone, run the validation loop - automated checks, install missing pieces, ask for missing info, update PROJECT.md, summarize for user, then wait for approval before the next milestone.
globs: "*.{ts,js,py,java,go,rs,tsx,jsx,vue,svelte}"
---

# Rule 23: Milestone Validation

## The Rule

During `/implement`, break work into **milestones** (meaningful shippable slices).
After EACH milestone completes, STOP and run the validation loop before starting the next.

Do not silently move on. Do not batch multiple milestones without validating. The user
must see a clear gate between each slice.

## The Validation Loop (6 steps)

Run these six steps in order at the end of every milestone:

1. **Automated checks** — lint, typecheck, format, unit + integration tests, build, migrations, security audit.
2. **Acceptance criteria** — compare against the milestone's definition in `IMPLEMENTATION-ROADMAP.md`.
3. **Install missing pieces** — packages, env vars, migrations, generated docs.
4. **Ask for missing info** — secrets, credentials, design decisions that surfaced mid-build.
5. **Update PROJECT.md milestone table** — mark Done + record validation results + date.
6. **Summarize for user** — what was built, checks that passed, issues fixed, info needed. Wait for "yes proceed" before M+1.

## Stopping Points

You MUST stop and wait for the user:

- If any automated check fails and the root cause is ambiguous.
- If a secret/credential is required and not yet provided.
- If a domain decision came up that wasn't covered in discovery/planning.
- If a destructive change is needed (drop table, data migration, rename API).

## Non-Stopping Fixes

You MAY fix silently and mention in the summary:

- Auto-formatting drift.
- Missing `peerDependencies` install.
- Adding a `.env.example` entry for a new env var.
- Regenerating auto-generated files (Prisma client, OpenAPI spec).
- Adding missing types or JSDoc for files you just touched.

## Validation Record Format

Append to `PROJECT.md` → `## Milestones` table:

```markdown
| # | Milestone | Status | Validated | Notes |
|---|-----------|--------|-----------|-------|
| M1 | Scaffold | ✓ Done | 2026-04-16 | lint ✓ build ✓ |
| M2 | DB schema | ✓ Done | 2026-04-16 | 12 tables, migrations applied |
| M3 | Auth | Blocked | - | waiting: OAuth credentials |
```

## Complexity-Driven Rigor

- **Simple**: 2-3 milestones. Checks = build + run smoke.
- **Medium**: 4-6 milestones. Checks = lint + unit tests + build.
- **Complex**: 7-10 milestones. Checks = lint + types + unit + integration + build + security audit.

## Skill-Level Adjustments

- **Non-Programmer / Beginner** — use plain-English summaries, no raw linter output. "Everything checked out, 14 tests passed."
- **Intermediate / Professional** — show raw check output, error excerpts if any, and let them decide on non-critical warnings.

## Why This Matters

- Silent progression hides problems until they compound.
- Users lose trust if they find out at M5 that M2 was actually broken.
- Clear gates between milestones give the user confidence and control.
- Asking for missing info at the right moment is faster than guessing and refactoring later.
