---
description: Any app with user accounts implements the 5-Dimensional Permission Model from architecture-spec §8. Every permission is the intersection of (Organizational scope, Academic year, Semester, Functional scope, Action type). Profiles are the primary admin abstraction. Deny always wins. Full audit trail. The 4 canonical reports are required.
globs: "*"
---

# Rule 25: RBAC by Default (5-Dimensional Model)

> Canonical source: `.claude/knowledge/architecture-spec.md §8`
> Practical guide: `.claude/knowledge/permissions-model.md`
> Command: `/rbac`

## The Rule

Every project with user accounts MUST implement the **5-Dimensional
Permission Model** from spec §8. No `if (user.isAdmin)` checks. No simple
3-field `resource:action:scope` — the full 5-dimensional model from day 1.

## The 5 Dimensions (all must align)

| # | Dimension | Nature |
|---|-----------|--------|
| 1 | **Organizational scope** | Hierarchical (University → College → Dept → …) |
| 2 | **Academic year** | Temporal (annual range or all) |
| 3 | **Semester** | Temporal (term range or all) |
| 4 | **Functional scope** | App hierarchy (Main App → Sub-App → Feature) |
| 5 | **Action type** | View / Insert / Edit / Close / Open / Delete (+ module-custom) |

A user is authorized ONLY if **all five dimensions align**.

## What's Required

### Data model (from `permissions-model.md`)

- [ ] `org_nodes` table with hierarchy (LTREE)
- [ ] `academic_years` + `semesters`
- [ ] `apps` (functional hierarchy)
- [ ] `action_types` (core-defined + module-extensible via manifest)
- [ ] `profiles` + `profile_grants`
- [ ] `user_profiles`, `groups`, `group_members`, `group_profiles`
- [ ] `user_overrides` (most specific, wins over profile + group)
- [ ] `permission_audit` (1-year minimum retention)

### Profiles as primary admin abstraction (§8.3)

- [ ] Administrators assign **profiles**, not individual permissions
- [ ] Profiles are versioned (rollback supported)
- [ ] Starter profiles shipped per domain (Super Admin, Dean, Faculty, ...)
- [ ] Visual 5-dimensional grant builder in admin UI
- [ ] "Preview: who gains/loses access if this ships"

### Assignment modes (§8.4, increasing specificity)

- [ ] Profile-based assignment (broadest)
- [ ] Group-based override
- [ ] User-specific override (most specific)
- [ ] **Denial always wins** over grants
- [ ] Effective permission computed deterministically

### Admin UI required (§8.5 — mandatory for Medium+)

- [ ] User management (create/edit/deactivate/lock/MFA/sessions)
- [ ] Profile management (CRUD + clone + version + archive)
- [ ] Assignment management (profile + group + override)
- [ ] Visitor / guest management (if applicable)
- [ ] Permission reports — 4 canonical (required):
  1. **Who can do X?** — given (action, org, year, semester, app) → users + sources
  2. **What can user Z do?** — given user → actions + source chain
  3. **Which profiles grant X?** — given (action, scope) → profiles
  4. **Recent changes** — audit-filtered
- [ ] Audit log (searchable, exportable, 1yr+ retention)

### Evaluator contract

- [ ] Single canonical `canUser(user, action, ctx)` function
- [ ] ctx = `{orgNodeId, year, semester, appNodeId}` — all 4 must be provided
- [ ] Returns `{allowed, reason, reasonChain}` for traceability
- [ ] Denial wins (explicit deny at any layer blocks)
- [ ] Default deny (no applicable grant = denied)
- [ ] Every access decision logged for audit trail
- [ ] Middleware / guard wraps every route needing it
- [ ] Frontend `<Can>` component uses same evaluator

### Module manifest declaration

Every module declares permissions it provides + consumes:

```jsonc
"permissions": {
  "provides": ["grades:view", "grades:edit", "grades:approve", ...],
  "actionTypes": {
    "approve": { "displayName": "Approve final grades" }
  },
  "consumes": ["users:view:*", "courses:view:scope"]
}
```

## Discovery-Phase Requirement (§8.6) — MANDATORY

When a project involves non-trivial permissions, `/discover permissions`
MUST be run during discovery and **all 10 questions answered** before
implementation. `/implement` refuses to scaffold permission-sensitive
modules without `design/PERMISSIONS.md`.

The 10 questions:
1. Organizational hierarchy shape + depth
2. Time scoping (annual / fiscal / semester / campaign / none)
3. Functional hierarchy (main → sub → feature)
4. Action types beyond standard 6
5. Initial profiles to ship
6. Default scopes + actions per profile
7. Group / individual overrides expected
8. Guests / visitors handling
9. Day-1 permission reports required
10. Audit + compliance requirements

## What's Banned

- Simple `is_admin` booleans scattered through code
- 3-field `resource:action:scope` without org/year/semester/app dimensions
- Ad-hoc `if (user.role === 'admin')` in route handlers
- Permissions hard-coded at compile time (must be profile-driven, admin-editable)
- Bypassing the canonical evaluator
- Building permission-sensitive features without `/discover permissions` completed
- Missing audit log on permission changes
- Missing any of the 4 canonical reports

## Complexity-Driven Scope

| Complexity | Minimum Requirements |
|-----------|---------------------|
| **Simple** (with users) | Full 5-dim model but 2-3 profiles (admin, user, guest), lightweight audit log |
| **Medium** | Standard 7-8 profiles per domain, full admin UI, all 4 reports, 1-year audit retention |
| **Complex** | Everything + group overrides + user overrides + versioned profiles + compliance-ready audit (configurable retention) + per-tenant profiles |

## Skip Condition

Rule 25 is optional ONLY if:
- The app has NO user accounts (purely public), OR
- The app is truly single-user with zero multi-user scenarios ever

## How to Verify

- `/rbac audit` — scans endpoints for missing/wrong-dim guards
- `/rbac test` — runs decision-table tests
- `/rbac who-can`, `/rbac what-can`, etc. — reports work
- `/a11y-audit` — permission admin UI is keyboard + screen-reader accessible

Run before declaring any milestone done.

## Why This Rule Exists

- Simple RBAC (role boolean) leaks to every endpoint and becomes unrefactorable
- Real-world permission needs span time (academic years, fiscal cycles) and
  organizational nesting — the 3-field model doesn't capture this
- Retrofitting 5 dimensions onto a built system is a full rewrite
- The reports are non-optional for compliance (SOC2, HIPAA, FERPA, GDPR)
- "Who can do X?" is the #1 question admins ask — if your system can't
  answer it in 1 second, you haven't shipped real RBAC
