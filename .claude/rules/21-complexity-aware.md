---
description: Scale the depth and ceremony of your work to the project's complexity level (Simple, Medium, Complex). Ask the user if unsure - never assume.
globs: "*"
---

# Rule 21: Complexity-Aware Workflow

## The Rule

Every project has a **Complexity** field in `PROJECT.md`: `Simple`, `Medium`, or `Complex`.
Read it before doing anything design, planning, or implementation-related, and scale
your output accordingly.

If there is no active project or no complexity set, ASK:

```
Before I proceed, what's the complexity level for this task?

  1. Simple   — Front + Back only. Fast build. Minimal planning.
  2. Medium   — Full-stack with auth, DB, tests, basic CI/CD.
  3. Complex  — Production system: all 10 plans, microservices, monitoring, security.

Which one?
```

## Behavior by Level

### Simple

- **Discovery**: 2-3 quick questions, one architecture sketch.
- **Design**: No formal design doc. Outline components in chat.
- **Plans**: No `/plan create`. At most a lightweight `QUICK-PLAN.md`.
- **Implementation**: Start coding right after the discussion.
- **Testing**: Smoke tests only.
- **Infra**: Local dev setup is enough; skip Docker/CI until needed.
- **Rules relaxed**: Skip Rule 13 (no-code-without-plan).

### Medium

- **Discovery**: Architecture diagram + data flow diagram. ~5 clarifying questions.
- **Design**: Lightweight `DESIGN.md` with API + data model + component list.
- **Plans**: 5 core plans — architecture, DB, API, frontend, auth. No ops/monitoring plans.
- **Implementation**: Follow plans, but Gate 3 is recommended not mandatory.
- **Testing**: Unit + integration. E2E on critical path only.
- **Infra**: Dockerfile + basic CI workflow. No Terraform yet.

### Complex

- **Discovery**: Full structured discovery (stakeholders, user-journeys, risks, MVP).
  Generate all 4 standard diagrams (architecture, data flow, user journey, ERD).
- **Design**: Full `DESIGN.md` with all 4 framework steps + ADRs.
- **Plans**: All 10 sub-plans + MASTER-PLAN.md + IMPLEMENTATION-ROADMAP.md.
- **Implementation**: Strict 3-gate workflow. No code until Gate 3.
- **Testing**: Full test pyramid + chaos engineering + contract tests.
- **Infra**: Docker + CI/CD + Terraform + monitoring stack + runbooks.

## How to Apply

Before every significant output:

1. Read `projects/<active-project>/PROJECT.md` for `**Complexity**:`.
2. Adjust the scope of what you produce:
   - Simple: 1 diagram, 1 table, concise prose.
   - Medium: 2-3 diagrams, full API table, normal prose.
   - Complex: multiple diagrams per section, deep analysis, full doc.
3. If you catch yourself about to produce 10 plan files for a Simple project —
   STOP and pick the right scope.

## User Override

Users can always override by saying things like:
- "Be more thorough" → bump up one level for this response
- "Just a quick sketch" → bump down one level for this response

Don't change the project-level `Complexity` unless the user asks explicitly.

## Why this matters

- Forcing the full 3-gate workflow on a small script wastes time and frustrates the user.
- Skipping structure on a production SaaS creates risk and tech debt.
- Matching ceremony to scope is the difference between a helpful tool and a bureaucratic one.
