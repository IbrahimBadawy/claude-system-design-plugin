# Permissions Model — Practical Implementation Guide

> Practical companion to `architecture-spec.md §8`. Read the spec first; this
> file shows the data model, evaluation algorithm, admin UI, and reports —
> **all generalized for an N-dimensional permission model where N is
> determined by the domain**.

## The N-Dimensional Permission (not fixed)

Every permission is the **intersection of N dimensions**, where **N is
determined by the domain, not fixed**. All declared dimensions MUST align
for access to be granted.

### 3 universal dimensions (always present)

1. **Organizational scope** — hierarchical (varies by domain shape)
2. **Functional scope** — app hierarchy (main → sub → feature)
3. **Action type** — view/insert/edit/... + custom per module

### Domain-specific dimensions (declared per system)

Examples of how N varies:

| Domain | Declared dimensions | N |
|--------|--------------------|---|
| Simple SaaS | Org, Functional, Action | 3 |
| CRM | Org, Functional, Action, Resource-owner | 4 |
| Hospital | Org, Shift, Functional, Action | 4 |
| Factory | Plant, Line, Shift, Functional, Action | 5 |
| SIS | Org, Academic-year, Semester, Functional, Action | 5 |
| Multi-tenant SaaS | Tenant, Region, Org, Functional, Action | 5 |
| Regulated enterprise | Tenant, Region, Legal-entity, Fiscal-period, Org, Functional, Action | 7 |

The plugin's job is to generate the right schema + evaluator for **whatever
N the domain declares**.

---

## Declaring the Dimensions (per project)

`design/PERMISSIONS.md` declares the dimensions for THIS project:

```yaml
# projects/<project>/design/PERMISSIONS.md (front-matter block)
permissions:
  dimensions:
    - name: organizational
      type: hierarchical
      required: true
      # shape declared below
      shape: [University, College, Department, Program, Cohort]

    - name: academic_year          # domain-specific (SIS)
      type: temporal_range
      required: false              # can a grant say "all years"? yes → false
      unit: year

    - name: semester               # domain-specific (SIS)
      type: enum_set
      required: false
      values: [first, second, summer]

    - name: functional
      type: hierarchical
      required: true

    - name: action
      type: enum_extensible
      required: true
      core_values: [view, insert, edit, close, open, delete]
      # modules extend via manifest

  defaultDeny: true          # unmatched context denies (§8.4)
  denyWinsOverGrants: true   # §8.4 guarantee
```

For a **hospital** instead, you'd declare 4 dimensions (no academic_year or
semester; add `shift`). For a **simple SaaS**, just the 3 universal ones.

---

## Flexible DB Schema

The schema adapts to the declared dimensions. This avoids hardcoding
`academic_years` or `semesters` tables into every app.

### Core tables (always present)

```sql
-- Users & tenants (from Core)
-- org_nodes: the "organizational" dimension (always declared)
CREATE TABLE org_nodes (
  id           UUID PRIMARY KEY,
  parent_id    UUID REFERENCES org_nodes(id),
  level_name   TEXT NOT NULL,   -- declared shape, e.g., 'university', 'college', ...
  name         TEXT NOT NULL,
  path         LTREE NOT NULL,
  tenant_id    UUID NOT NULL
);
CREATE INDEX ON org_nodes USING GIST (path);

-- apps: the "functional" dimension (always declared)
CREATE TABLE apps (
  id UUID PRIMARY KEY,
  parent_id UUID REFERENCES apps(id),
  kind TEXT,   -- 'main-app' | 'sub-app' | 'feature'
  name TEXT, module_id TEXT,
  path LTREE
);

-- action_types: the "action" dimension (always declared, module-extensible)
CREATE TABLE action_types (
  code TEXT PRIMARY KEY,
  display_name TEXT,
  defined_by TEXT   -- 'core' | module_id
);
```

### Dimension registry (the N bit — flexible)

```sql
-- Registry of dimensions for THIS system
CREATE TABLE dimensions (
  name         TEXT PRIMARY KEY,      -- 'organizational', 'academic_year', 'shift', ...
  kind         TEXT NOT NULL,         -- 'hierarchical' | 'temporal_range' | 'enum_set' | 'reference'
  backing_table TEXT,                 -- optional: table that stores values (e.g., 'academic_years')
  config       JSONB,                 -- type-specific config (shape, units, enum values)
  required     BOOLEAN DEFAULT true
);

-- Seed-time: insert one row per declared dimension
-- SIS example would insert: organizational, academic_year, semester, functional, action
-- Simple SaaS would insert only: organizational, functional, action
```

### Dimension-specific tables (optional, one per domain-specific dimension)

Each domain-specific dimension may have its own backing table. Example
for SIS (`academic_year` + `semester`):

```sql
CREATE TABLE academic_years (
  id UUID PRIMARY KEY, label TEXT UNIQUE, starts DATE, ends DATE,
  tenant_id UUID NOT NULL
);

CREATE TABLE semesters (
  id UUID PRIMARY KEY, year_id UUID REFERENCES academic_years(id),
  label TEXT, kind TEXT,
  starts DATE, ends DATE
);
```

For a **hospital** (`shift`), you'd have:
```sql
CREATE TABLE shifts (
  code TEXT PRIMARY KEY, display_name TEXT,
  starts_at TIME, ends_at TIME
);
```

For a **factory** (`plant`, `line`):
```sql
CREATE TABLE plants (id UUID PRIMARY KEY, name TEXT, tenant_id UUID);
CREATE TABLE lines  (id UUID PRIMARY KEY, plant_id UUID, name TEXT);
```

For a **simple SaaS** (only the 3 universal dimensions), no additional
tables are needed.

### Profiles + grants (dimension-agnostic)

Grants use a flexible JSONB column to store per-dimension constraints. This
keeps the schema stable as dimensions vary across domains.

```sql
CREATE TABLE profiles (
  id           UUID PRIMARY KEY,
  name         TEXT NOT NULL,
  description  TEXT,
  version      INT NOT NULL DEFAULT 1,
  status       TEXT NOT NULL,   -- 'draft' | 'active' | 'archived'
  tenant_id    UUID NOT NULL,
  created_by UUID, updated_by UUID,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE profile_grants (
  id            UUID PRIMARY KEY,
  profile_id    UUID REFERENCES profiles(id),

  -- Dimension constraints stored as JSONB keyed by dimension name
  -- Shape depends on dimension.kind; validated by application layer
  scope         JSONB NOT NULL,
  -- Examples:
  --   SIS:        {"organizational": {...}, "academic_year": {...}, "semester": {...}, "functional": {...}}
  --   hospital:   {"organizational": {...}, "shift": {...}, "functional": {...}}
  --   simple:     {"organizational": {...}, "functional": {...}}

  action_code   TEXT NOT NULL REFERENCES action_types(code),
  effect        TEXT NOT NULL CHECK (effect IN ('grant', 'deny'))
);

-- Example scope values per dimension kind:
-- hierarchical:    { "nodeId": "uuid", "includesDescendants": true }
--                  or { "all": true }
-- temporal_range:  { "from": "2025-09-01", "to": "2026-08-31" }
--                  or { "mode": "current" } or { "all": true }
-- enum_set:        { "values": ["first", "second"] } or { "all": true }
-- reference:       { "ids": ["uuid1", "uuid2"] } or { "all": true }
```

This stays the same whether the domain has 3 or 7 dimensions — only the keys
inside `scope` differ.

### Assignment + overrides (unchanged by N)

```sql
CREATE TABLE user_profiles (user_id UUID, profile_id UUID, PRIMARY KEY (user_id, profile_id));
CREATE TABLE groups (id UUID PRIMARY KEY, name TEXT, tenant_id UUID);
CREATE TABLE group_members (group_id UUID, user_id UUID, PRIMARY KEY (group_id, user_id));
CREATE TABLE group_profiles (group_id UUID, profile_id UUID, PRIMARY KEY (group_id, profile_id));

CREATE TABLE user_overrides (
  id UUID PRIMARY KEY,
  user_id UUID,
  scope JSONB NOT NULL,     -- same flexible shape as profile_grants.scope
  action_code TEXT,
  effect TEXT,
  reason TEXT,
  expires_at TIMESTAMPTZ
);

CREATE TABLE permission_audit (
  id UUID PRIMARY KEY,
  actor_id UUID,
  event_type TEXT,
  subject_type TEXT, subject_id UUID,
  before_snap JSONB, after_snap JSONB,
  ip_address INET, user_agent TEXT,
  reason TEXT,
  timestamp TIMESTAMPTZ DEFAULT NOW()
);
```

---

## Evaluator (dimension-agnostic)

The canonical evaluator receives **whatever context keys the domain
declares** and validates each against the grant's scope:

```typescript
// core/rbac/evaluator.ts — one evaluator, any N
export async function canUser(
  userId: UserId,
  action: ActionCode,
  context: Record<string, unknown>  // keys determined by declared dimensions
): Promise<PermissionDecision> {
  // 1. Validate context has all required dimensions
  const dims = await getDeclaredDimensions();
  for (const dim of dims.filter(d => d.required)) {
    if (context[dim.name] === undefined) {
      throw new Error(`Missing required dimension: ${dim.name}`);
    }
  }

  // 2. Collect grants from 3 layers
  const profiles = await getUserProfiles(userId);
  const groupGrants = await getUserGroupGrants(userId);
  const userOverrides = await getUserOverrides(userId);

  const applicableGrants = [
    ...profiles.flatMap(p => p.grants),
    ...groupGrants,
    ...userOverrides,
  ].filter(g => matchesAllDimensions(g, action, context, dims));

  // 3. Deny wins
  const denials = applicableGrants.filter(g => g.effect === 'deny');
  if (denials.length > 0) return { allowed: false, reason: 'explicit-deny', source: mostSpecific(denials) };

  // 4. Grant if any applicable grant exists
  const grants = applicableGrants.filter(g => g.effect === 'grant');
  if (grants.length > 0) return {
    allowed: true,
    source: mostSpecific(grants),
    reasonChain: explain(grants, context),
  };

  // 5. Default deny
  return { allowed: false, reason: 'no-grant' };
}

function matchesAllDimensions(
  grant: Grant, action: ActionCode,
  context: Record<string, unknown>,
  dims: Dimension[]
): boolean {
  if (grant.action_code !== action) return false;
  for (const dim of dims) {
    if (!matchesDimension(grant.scope[dim.name], context[dim.name], dim.kind)) {
      return false;
    }
  }
  return true;
}

function matchesDimension(
  scopeSpec: unknown, contextValue: unknown, kind: DimKind
): boolean {
  if (scopeSpec === undefined || (scopeSpec as any).all) return true;
  switch (kind) {
    case 'hierarchical':    return isAncestorOrSelf(scopeSpec, contextValue);
    case 'temporal_range':  return isInRange(scopeSpec, contextValue);
    case 'enum_set':        return (scopeSpec as any).values.includes(contextValue);
    case 'reference':       return (scopeSpec as any).ids.includes(contextValue);
  }
}
```

### Key invariants

1. **Denial always wins** — at any layer
2. **Default deny** — unmatched context = denied
3. **All declared dimensions must match** — missing required dim → error
4. **Profile-based is broadest**, user override is most specific
5. **Every decision is traceable** via `reasonChain`
6. **Evaluator stays the same** regardless of N — driven by dimensions registry

---

## Starter Profiles (per domain)

`/rbac --domain <kind>` ships tuned defaults per domain. The dimensions
match the declared shape for that domain.

### SIS example (5 dimensions)

```jsonc
{
  "profiles": [
    {
      "name": "Dean",
      "grants": [
        {
          "scope": {
            "organizational":  { "nodeId": "<college-id>", "includesDescendants": true },
            "academic_year":   { "mode": "current" },
            "semester":        { "values": ["first", "second", "summer"] },
            "functional":      { "nodeId": "<academic-app>", "includesDescendants": true }
          },
          "action_code": "view", "effect": "grant"
        },
        {
          "scope": { "organizational": {...}, "academic_year": {"mode":"current"}, "semester": {"all":true}, "functional": {...} },
          "action_code": "approve", "effect": "grant"
        }
      ]
    }
  ]
}
```

### Hospital example (4 dimensions — no academic_year/semester; add shift)

```jsonc
{
  "profiles": [
    {
      "name": "Night-shift Nurse",
      "grants": [
        {
          "scope": {
            "organizational": { "nodeId": "<ward-id>", "includesDescendants": true },
            "shift":          { "values": ["night"] },
            "functional":     { "nodeId": "<patient-care-app>", "includesDescendants": true }
          },
          "action_code": "view", "effect": "grant"
        }
      ]
    }
  ]
}
```

### Simple SaaS example (3 dimensions — the minimum)

```jsonc
{
  "profiles": [
    {
      "name": "Admin",
      "grants": [
        {
          "scope": {
            "organizational": { "all": true },
            "functional":     { "all": true }
          },
          "action_code": "*", "effect": "grant"
        }
      ]
    },
    {
      "name": "Member",
      "grants": [
        {
          "scope": {
            "organizational": { "all": true },
            "functional":     { "nodeId": "<main-app>", "includesDescendants": true }
          },
          "action_code": "view", "effect": "grant"
        }
      ]
    }
  ]
}
```

### Factory example (5 dimensions: plant + line + shift + functional + action)

```jsonc
{
  "profiles": [
    {
      "name": "Line Supervisor (Plant A, Line 3)",
      "grants": [
        {
          "scope": {
            "organizational": { "nodeId": "<plant-A-id>", "includesDescendants": true },
            "plant":          { "ids": ["<plant-A-id>"] },
            "line":           { "ids": ["<line-3-id>"] },
            "shift":          { "values": ["day", "evening"] },
            "functional":     { "nodeId": "<ops-app>", "includesDescendants": true }
          },
          "action_code": "view", "effect": "grant"
        },
        { /* same scope, edit + override */ }
      ]
    }
  ]
}
```

---

## Admin UI — dimension-adaptive

The profile editor UI adapts to the declared dimensions:

- For SIS: 5 tabs (Org, Academic Year, Semester, Functional, Action)
- For Hospital: 4 tabs (Org, Shift, Functional, Action)
- For Simple SaaS: 3 tabs (Org, Functional, Action)
- For Factory: 5 tabs (Plant, Line, Shift, Functional, Action)

Each tab's picker matches the dimension's `kind`:
- **hierarchical** → tree picker with "includes descendants" toggle
- **temporal_range** → date range picker with "all" / "current" shortcuts
- **enum_set** → multi-select checkboxes
- **reference** → autocomplete multi-picker

The admin UI **is generated from the dimensions registry**, not hand-coded
per domain.

---

## Declaring additional Action Types per Module (§8.2.3)

Unchanged from before — modules extend the action dimension via manifest:

```jsonc
{
  "id": "grades",
  "permissions": {
    "provides": ["grades:view", "grades:edit", "grades:approve", "grades:export"],
    "actionTypes": {
      "approve": { "displayName": "Approve final grades" },
      "export":  { "displayName": "Export to transcript system" }
    }
  }
}
```

---

## Discovery-phase Questions (from §8.6, N-dimensional form)

The `/discover permissions` sub-command asks these mandatory questions, with
**Q0 added** to establish the dimensions up-front:

```
Q0 (NEW): What dimensions of scoping apply to this domain?
    Start from the 3 universal ones:
      - Organizational (always — hierarchical tree)
      - Functional (always — app hierarchy)
      - Action (always)
    Add any domain-specific dimensions:
      [ ] Academic year / fiscal year (temporal range)
      [ ] Semester / period (enum set)
      [ ] Shift (enum set)
      [ ] Region / country (hierarchical)
      [ ] Tenant (reference — for multi-tenant SaaS)
      [ ] Store / branch (reference)
      [ ] Project / matter (reference)
      [ ] Plant / line (hierarchical)
      [ ] Campaign / phase (temporal range)
      [ ] Other: _______
    Final N = 3 + (count of domain-specific)

Q1: What shape does your organizational hierarchy take?
    (levels, names, depth)

Q2: For each domain-specific dimension declared in Q0, define:
    - name, type (hierarchical / temporal / enum / reference)
    - values / shape / unit

Q3: Functional hierarchy (main app → sub-app → feature)?

Q4: Action types beyond standard 6 (approve, export, submit, ...)?

Q5: Initial profiles to ship (tuned to the domain)?

Q6: Default scopes + actions per profile, across ALL declared dimensions?

Q7: Group / individual overrides expected?

Q8: Guests / visitors handling?

Q9: Day-1 permission reports required?

Q10: Audit + compliance requirements?
```

Answers saved to `projects/<project>/design/PERMISSIONS.md`. The plugin
generates schema, seeds, middleware, and admin UI from the declared
dimensions — **never hardcoded to a specific N**.

---

## Middleware & Frontend (dimension-agnostic)

Backend (NestJS):
```typescript
@Permissions('grades:edit')
@Post('grades/:id')
async updateGrade(
  @Param('id') id: string,
  @CurrentUser() user: User,
  @PermissionContext() ctx: Record<string, unknown>  // keys match declared dims
) {
  // PermissionsGuard called canUser(user, 'grades:edit', ctx)
  return this.service.update(id, body);
}
```

Frontend:
```tsx
<Can
  action="grades:edit"
  context={{
    organizational: collegeId,
    academic_year: currentYear,
    semester: currentSemester,
    functional: 'grades.finalize'
  }}
  fallback={<AccessDeniedBanner />}
>
  <GradesEditForm />
</Can>
```

The `context` keys depend on what the project declared. A hospital app
would pass `{ organizational, shift, functional }`. A simple SaaS would pass
`{ organizational, functional }`.

---

## Testing Requirements

- **Dimensions-registry tests** — evaluator rejects unknown dimensions, requires required ones
- **Decision-table tests** — tabulate profile × user × context → expected result (per domain)
- **Denial-wins tests** — explicit deny beats grant at every layer
- **Scope boundary tests** — grant at parent covers children; scoped grant doesn't cover siblings
- **Shape tests** — hierarchical range contains descendants; temporal range contains sub-ranges; enum set matches values only
- **Profile versioning tests** — rollback restores prior permissions
- **Audit tests** — every change + every access decision logged
- **Performance tests** — evaluator < 1ms cached; < 10ms cold

---

## See Also

- `architecture-spec.md §8` — canonical specification (now dimension-flexible)
- `/rbac` command — dimension-aware designer
- `/discover permissions` — first asks "what dimensions apply?"
- Rule 25 — enforces declared-dimensions approach
