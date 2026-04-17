---
description: Any app with user accounts implements the Multi-Dimensional Permission Model from architecture-spec §8 where N is determined by the domain. 3 dimensions are universal (Organizational, Functional, Action); additional ones are domain-specific and declared in discovery. Profiles are the primary admin abstraction. Denials always win. Full audit trail. 4 canonical reports required.
globs: "*"
---

# Rule 25: RBAC by Default (Multi-Dimensional, Domain-Specific N)

> Canonical source: `.claude/knowledge/architecture-spec.md §8`
> Practical guide: `.claude/knowledge/permissions-model.md`
> Command: `/rbac`

## The Rule

Every project with user accounts MUST implement the **Multi-Dimensional
Permission Model** from spec §8. No `if (user.isAdmin)`. No hardcoded
`resource:action:scope`. The permission model is **N-dimensional where N is
determined by the domain**, not fixed.

## The Universal 3 + Domain-Specific N-3

Every permission is the intersection of **N dimensions**. Three are always
present; the rest come from discovery.

### Universal (always 3)

1. **Organizational scope** — hierarchical tree (shape is domain-specific)
2. **Functional scope** — app hierarchy (Main App → Sub-App → Feature)
3. **Action type** — view / insert / edit / close / open / delete + module-extensible

### Domain-specific (varies)

Declared during `/discover permissions`. Examples:

| Domain | Extra dimensions | N |
|--------|------------------|---|
| Simple SaaS | (none) | 3 |
| CRM / helpdesk | Resource-owner | 4 |
| Hospital | Shift | 4 |
| E-commerce | Region | 4 |
| SIS | Academic-year, Semester | 5 |
| Retail | Region, Store | 5 |
| Factory | Plant, Line, Shift | 5 |
| Multi-tenant SaaS | Tenant, Region | 5 |
| Regulated enterprise | Tenant, Region, Legal-entity, Fiscal-period | 7 |

**The plugin's job is to make any N work**, driven by the dimensions registry
declared per project.

## What's Required

### Dimension declaration (§8.6 Q0 — mandatory)

- [ ] `design/PERMISSIONS.md` declares the project's dimensions
- [ ] Each dimension has: `name`, `type` (hierarchical / temporal_range /
      enum_set / reference), `required` flag, and type-specific config
- [ ] The 3 universal dimensions are always present
- [ ] Domain-specific dimensions added as needed

### Data model (dimension-flexible, from `permissions-model.md`)

- [ ] `dimensions` registry table (records the project's N dimensions)
- [ ] `org_nodes` table (universal — the Organizational dimension)
- [ ] `apps` table (universal — the Functional dimension)
- [ ] `action_types` table (universal + module-extensible)
- [ ] Per-domain-dimension tables (only those this project declared —
      `academic_years` only if SIS, `shifts` only if hospital/factory, etc.)
- [ ] `profiles` + `profile_grants` (grants use flexible JSONB `scope`
      keyed by dimension name)
- [ ] `user_profiles`, `groups`, `group_members`, `group_profiles`
- [ ] `user_overrides`
- [ ] `permission_audit` (1-year minimum retention)

### Profiles as primary admin abstraction (§8.3)

- [ ] Administrators assign **profiles**, not individual permissions
- [ ] Profiles are versioned (rollback supported)
- [ ] Starter profiles shipped per domain
- [ ] Visual profile editor adapts to declared dimensions (1 tab per dim)
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
- [ ] Dimension-adaptive profile editor (one tab per declared dimension)
- [ ] Assignment management (profile + group + override)
- [ ] Visitor / guest management (if applicable)
- [ ] Permission reports — 4 canonical (required):
  1. **Who can do X?** — users + source chains
  2. **What can user Z do?** — actions + source chain
  3. **Which profiles grant X?**
  4. **Recent changes** — audit-filtered
- [ ] Audit log (searchable, exportable, 1yr+ retention)

### Evaluator contract (dimension-agnostic)

- [ ] Single canonical `canUser(user, action, context)` function
- [ ] `context` is `Record<string, unknown>` — keys match declared dimensions
- [ ] Returns `{allowed, reason, reasonChain}` for traceability
- [ ] Validates all required dimensions present in context
- [ ] Denial wins (explicit deny at any layer blocks)
- [ ] Default deny (no applicable grant = denied)
- [ ] Every access decision logged for audit trail
- [ ] Middleware / guard wraps every route needing it
- [ ] Frontend `<Can>` component uses same evaluator
- [ ] Same evaluator code regardless of N — driven by dimensions registry

### Module manifest declaration

Every module declares permissions it provides + consumes:

```jsonc
"permissions": {
  "provides": ["grades:view", "grades:edit", "grades:approve"],
  "actionTypes": {
    "approve": { "displayName": "Approve final grades" }
  },
  "consumes": ["users:view:*", "courses:view:scope"]
}
```

## Discovery-Phase Requirement (§8.6) — MANDATORY

When a project involves non-trivial permissions, `/discover permissions`
MUST run during discovery and **all 11 questions answered** (Q0 added for
dimension setup):

- **Q0**: What dimensions of scoping apply to this domain? (sets N)
- Q1: Organizational hierarchy shape + depth
- Q2: Per-dimension config (type, values, shape) for each non-universal dim
- Q3: Functional hierarchy (main → sub → feature)
- Q4: Action types beyond standard 6
- Q5: Initial profiles to ship
- Q6: Default scopes + actions per profile (across all declared dims)
- Q7: Group / individual overrides expected
- Q8: Guests / visitors handling
- Q9: Day-1 permission reports required
- Q10: Audit + compliance requirements

`/implement` refuses to scaffold permission-sensitive modules without
`design/PERMISSIONS.md` containing declared dimensions.

## What's Banned

- Simple `is_admin` booleans scattered through code
- **Hardcoded dimension counts** (no hardcoding "5 dimensions" everywhere)
- Evaluators that can't adapt to declared dimensions
- Profile editors that don't read from the dimensions registry
- Ad-hoc `if (user.role === 'admin')` in route handlers
- Permissions hard-coded at compile time (must be profile-driven)
- Bypassing the canonical evaluator
- Building permission-sensitive features without `/discover permissions`
  completed
- Missing audit log on permission changes
- Missing any of the 4 canonical reports

## Complexity-Driven Scope

| Complexity | Minimum |
|-----------|---------|
| **Simple** (with users) | 3 universal dimensions + 2-3 profiles + lightweight audit |
| **Medium** | Universal 3 + domain-specific dimensions + full admin UI + all 4 reports + 1-year audit retention |
| **Complex** | Everything + group overrides + user overrides + versioned profiles + compliance audit (configurable retention) + per-tenant profiles |

## Skip Condition

Rule 25 is optional ONLY if:
- The app has NO user accounts (purely public), OR
- The app is truly single-user with zero multi-user scenarios ever

## How to Verify

- `/rbac dimensions` — lists declared dimensions
- `/rbac audit` — scans endpoints for missing/wrong-dim guards; validates
  context keys match declared dimensions
- `/rbac test` — runs decision-table tests for the declared dims
- `/rbac who-can`, `/rbac what-can`, etc. — reports work
- `/a11y-audit` — permission admin UI is keyboard + screen-reader accessible

Run before declaring any milestone done.

## Why This Rule Exists

- Simple RBAC (role boolean) leaks to every endpoint and becomes
  unrefactorable
- Real-world permission needs vary wildly by domain — SIS needs academic
  year + semester, hospital needs shift, factory needs plant + line + shift
- A rigid "N=5" model forces domains into the wrong shape (too much for
  simple SaaS, too little for regulated enterprise)
- An N-dimensional evaluator driven by a dimensions registry is write-once,
  reuse-across-domains
- Retrofitting N dimensions onto a built system is a full rewrite; getting
  N right during discovery costs a few hours
- The 4 reports are non-optional for compliance (SOC2, HIPAA, FERPA, GDPR)
