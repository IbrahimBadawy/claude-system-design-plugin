# /rbac - Multi-Dimensional Permissions & Access Control

Design a production-grade permission system following `architecture-spec.md §8`
and `permissions-model.md`. Not a simple `is_admin` boolean — a full
**multi-dimensional model** where **N is determined by the domain**, with
Profiles as the primary admin abstraction.

## The Model — N Dimensions (domain-specific)

Every permission is the intersection of **N dimensions**. N is determined
by the project, not hardcoded.

### 3 universal dimensions (always present)

1. **Organizational scope** — hierarchical tree (domain-specific shape)
2. **Functional scope** — app hierarchy (Main App → Sub-App → Feature)
3. **Action type** — View / Insert / Edit / Close / Open / Delete (+ module-custom)

### Domain-specific dimensions (declared in discovery)

Examples of how N varies by domain:

| Domain | Typical dimensions | N |
|--------|-------------------|---|
| Simple SaaS | Org + Functional + Action | 3 |
| CRM / helpdesk | Org + Functional + Action + Resource-owner | 4 |
| Hospital | Org + Shift + Functional + Action | 4 |
| Retail chain | Org + Region + Store + Functional + Action | 5 |
| SIS (education) | Org + Academic-year + Semester + Functional + Action | 5 |
| Factory | Plant + Line + Shift + Functional + Action | 5 |
| Multi-tenant SaaS w/ regions | Tenant + Region + Org + Functional + Action | 5 |
| Regulated enterprise | Tenant + Region + Legal-entity + Fiscal-period + Org + Functional + Action | 7+ |

**Your project's N is set during discovery** and recorded in
`design/PERMISSIONS.md`. The plugin generates schema, evaluator, and admin UI
from that declaration — not hardcoded to 5 or any other number.

## Usage

```
/rbac                              # Interactive design: asks dimensions first, then §8.6 questions
/rbac --domain <domain>            # Domain preset (sis, hospital, erp, retail, factory, saas-simple)
/rbac discover                     # Run the mandatory discovery questions (Q0 + 1-10)
/rbac dimensions                   # View / edit the declared dimensions for this project
/rbac generate                     # Generate schema + seeds + middleware + admin UI + reports
/rbac profiles                     # Manage profiles (list / create / clone / version / archive)
/rbac permissions                  # Declare action types per module
/rbac assign <user> <profile>      # Assign profile (or group or override)
/rbac who-can <action> <scope>     # Report 1: who can do X in scope Y?
/rbac what-can <user>              # Report 2: what can user Z do? with source chain
/rbac profiles-with <action>       # Report 3: which profiles grant X?
/rbac changes --last 30d           # Report 4: recent permission changes
/rbac audit                        # Verify every endpoint matches declared dimensions
/rbac test                         # Run decision-table tests
```

## Discovery Phase Questions (from §8.6 — MANDATORY)

When a project has non-trivial permissions, `/rbac discover` MUST ask these
before any implementation:

```
Question 0 (NEW — dimension setup):
  What dimensions of scoping apply to this domain?

  Universal dimensions (automatically included):
    ✓ Organizational (hierarchical)
    ✓ Functional (hierarchical — main app → sub-app → feature)
    ✓ Action (extensible enum)

  Domain-specific dimensions — tick all that apply:
    [ ] Academic year            (temporal range — for education)
    [ ] Fiscal year / period     (temporal range — for finance/ERP)
    [ ] Semester / term          (enum set — for education)
    [ ] Shift                    (enum set — for hospital/factory/retail)
    [ ] Region / country         (hierarchical — multinational orgs)
    [ ] Store / branch           (reference — retail)
    [ ] Tenant                   (reference — multi-tenant SaaS)
    [ ] Plant / line             (hierarchical — factory)
    [ ] Project / matter         (reference — consulting/legal)
    [ ] Phase / campaign         (temporal range — marketing)
    [ ] Legal entity             (reference — regulated enterprise)
    [ ] Resource owner           (reference — CRM "own records")
    [ ] Product category         (hierarchical — e-commerce)
    [ ] Custom: ____________________

  Final N dimensions: ___

Question 1: Organizational hierarchy
  For the Organizational dimension, what's the shape?
  (level names from root to leaf)
  Examples:
    SIS:         University → College → Department → Program → Cohort
    Hospital:    Group → Hospital → Department → Ward
    Retail:      HQ → Region → Country → City → Store
    Factory:     Company → Plant → Line → Cell
    Simple SaaS: Org → Team (or just Org)

Question 2: For each domain-specific dimension declared in Q0:
  - Type (hierarchical / temporal_range / enum_set / reference)
  - Values or shape or unit
  Example (SIS academic_year):
    Type: temporal_range
    Unit: year
    Values: 2020/2021, 2021/2022, 2022/2023, 2023/2024, 2024/2025, 2025/2026, ...

Question 3: Functional hierarchy (main app → sub-app → feature)?
  Paste your app tree.

Question 4: Action types beyond standard 6?
  (approve / reject / export / import / print / publish / submit / archive / ...)

Question 5: Initial profiles to ship?
  (tailored to your domain — Super Admin, Dean, Faculty, Student for SIS;
  System Admin, Chief, Doctor, Nurse for hospital; etc.)

Question 6: Default scopes + actions per profile?
  For each profile, grant values across EVERY declared dimension.

Question 7: Group + individual overrides expected?

Question 8: Guests / visitors?

Question 9: Day-1 permission reports required?
  Standard 4:
    [✓] Who can do X?
    [✓] What can user Z do?
    [✓] Which profiles grant action X?
    [✓] Recent permission changes
  Additional:

Question 10: Audit + compliance requirements?
  Retention: ___ years
  Regulations: [ ] GDPR [ ] HIPAA [ ] FERPA [ ] SOX [ ] PCI [ ] Other
```

**Answers saved to `projects/<project>/design/PERMISSIONS.md`.**

**Without answers** → `/implement` refuses to scaffold permission-sensitive modules.

## Domain Presets (shortcut)

`/rbac --domain <kind> discover` pre-fills Q0 with typical dimensions:

| Domain | Pre-filled dimensions |
|--------|----------------------|
| `saas-simple` | Org + Functional + Action (N=3) |
| `crm` | Org + Functional + Action + Resource-owner (N=4) |
| `sis` | Org + Academic-year + Semester + Functional + Action (N=5) |
| `hospital` | Org + Shift + Functional + Action (N=4) |
| `retail` | Org + Region + Store + Functional + Action (N=5) |
| `factory` | Plant + Line + Shift + Functional + Action (N=5) |
| `ecommerce` | Org + Region + Product-category + Functional + Action (N=5) |
| `projects` | Org + Project + Phase + Functional + Action (N=5) |
| `erp` | Tenant + Fiscal-period + Legal-entity + Org + Functional + Action (N=6) |
| `multi-tenant-saas` | Tenant + Region + Org + Functional + Action (N=5) |

Users can still customize after the preset — add/remove dimensions freely.

## Profiles — the Primary Abstraction

A **Profile** is a named, reusable bundle of grants across **whatever
dimensions the project declared**. Profiles are the primary admin
abstraction — administrators assign profiles, not individual permissions.

- Each dimension in a profile: specific value, range, or "all"
- Multiple profiles per user → union (minus explicit denials)
- Versioned: rollback supported
- Tenant-specific or universal

### Example grant shapes (shape depends on dimension kind)

```jsonc
// SIS profile grant (5 dimensions)
{
  "scope": {
    "organizational":  { "nodeId": "<college-engineering>", "includesDescendants": true },
    "academic_year":   { "from": "2025-09", "to": "2026-08" },
    "semester":        { "values": ["first", "second"] },
    "functional":      { "nodeId": "<academic-app>", "includesDescendants": true }
  },
  "action_code": "approve",
  "effect": "grant"
}

// Hospital profile grant (4 dimensions)
{
  "scope": {
    "organizational": { "nodeId": "<icu-ward>", "includesDescendants": true },
    "shift":          { "values": ["night"] },
    "functional":     { "nodeId": "<patient-care>", "includesDescendants": true }
  },
  "action_code": "edit",
  "effect": "grant"
}

// Simple SaaS profile grant (3 dimensions)
{
  "scope": {
    "organizational": { "all": true },
    "functional":     { "nodeId": "<main-app>", "includesDescendants": true }
  },
  "action_code": "view",
  "effect": "grant"
}
```

The shape of `scope` adapts to what dimensions this project declared.

## Assignment Modes (unchanged by N)

| Mode | Specificity |
|------|-------------|
| **Profile-based** | Broadest |
| **Group-based override** | Middle |
| **User-specific override** | Most specific |

**Denials always win.** Deterministic evaluation:

```bash
/rbac assign user:dana@acme.com profile:Dean
/rbac assign group:engineering-chairs profile:Department-Head
/rbac override user:dana@acme.com --action grades:delete --effect deny --reason "on-leave"
```

## Generated Artifacts

### `design/PERMISSIONS.md`
Complete answers to Q0-10 + derived permission model + declared dimensions.

### Database schema (from `permissions-model.md`, adapted to declared dims)
- `dimensions` registry (records what THIS project uses)
- `org_nodes` (always — the Organizational dimension)
- `apps` (always — the Functional dimension)
- `action_types` (always — core + module-extensible)
- **Per-declared-dimension tables** (e.g., `academic_years`, `semesters`,
  `shifts`, `plants`, `lines` — only for dims this project actually declared)
- `profiles`, `profile_grants` (with JSONB scope shaped to declared dims)
- `user_profiles`, `groups`, `group_members`, `group_profiles`
- `user_overrides`
- `permission_audit`

### Seeds
- Action types (core + module extras)
- Starter profiles matching the domain
- Declared dimensions registered

### Backend middleware (dimension-agnostic)

```typescript
@Permissions('grades:edit')
@Post('grades/:id')
async updateGrade(
  @CurrentUser() user: User,
  @PermissionContext() ctx: Record<string, unknown>
) {
  // PermissionsGuard already called canUser(user, 'grades:edit', ctx)
  return this.service.update(id, body);
}
```

The `@PermissionContext()` decorator extracts the declared dimension values
from request/headers/params. Helpers per dimension kind.

### Frontend `<Can>` (dimension-agnostic)

```tsx
<Can
  action="grades:edit"
  context={{
    organizational: collegeId,
    academic_year: currentYear,       // only if academic_year is declared
    semester: currentSemester,        // only if semester is declared
    functional: 'grades.finalize'
  }}
  fallback={<AccessDeniedBanner />}
>
  <GradesEditForm />
</Can>
```

### Admin UI (adaptive to declared dims)

Profile editor generates one tab per declared dimension, with picker UI
matching the dimension's kind (tree / range / enum / reference).

## The 4 Canonical Reports (§8.5, dimension-aware)

All 4 reports work regardless of N — they query the `profile_grants`
scope JSONB.

```
/rbac who-can grades:edit --college Engineering --year 2025/2026 --semester first
/rbac what-can dana@acme.com
/rbac profiles-with action:grades:delete --scope "Engineering"
/rbac changes --last 30d
```

## `/rbac audit`

Scans codebase for permission violations, validating against **declared dimensions**:

```markdown
## Multi-Dim RBAC Audit

Declared dimensions (5): organizational, academic_year, semester, functional, action

Endpoints scanned: 127
Guarded: 122 ✓
Unguarded (intentional public): 3 ✓
Unguarded (MISSING guard): 2 ✗

Endpoints with missing dimension in context: 4
  POST /api/v1/grades/finalize — missing `semester` in context extraction

Profile coverage:
  Super Admin:   covers 94% of actions
  Dean:          covers 62% — gaps: [billing, hr-admin]
  Faculty:       covers 8% ✓ (expected for role)

Declared dimensions never used in any grant: 0 ✓ (good)
Dimensions missing from evaluator: 0 ✓

Fix 2 missing guards + 4 context extraction gaps before shipping.
```

## Testing

`/rbac test` runs decision-table tests against declared dimensions:

```
## Decision Table Tests (5 declared dimensions)

For profile "Dean":
  ✓ can view grades in own college, current year, any semester
  ✓ can approve grades in own college, current year, current semester
  ✗ can edit grades in different college            [correctly denied]
  ✓ can view grades in historical year              [correctly granted]
  ✗ can approve grades in historical year           [correctly denied]

Denial-wins test:
  12 test cases checking user/group deny beats profile grant: all ✓

Scope boundary tests:
  Grant at college → covers departments: ✓
  Grant at college → does NOT cover sibling college: ✓

Dimension-matching tests:
  Temporal range overlap: ✓
  Enum set membership: ✓
  Hierarchical ancestor match: ✓

All tests passed (62/62)
```

## Integration with Other Commands

- `/discover permissions` — runs Q0-10; project's N is set here
- `/core-modules` — modules declare permissions.provides + consumes in manifest
- `/pages` — admin UI pages include Dimensions editor
- `/implement` — refuses to scaffold permission-sensitive modules until
  `PERMISSIONS.md` exists with declared dimensions
- `/security` — RBAC audit included in security review

## Examples

```
/rbac discover                                 # Start Q0-10; declare dimensions first
/rbac --domain sis discover                    # Pre-fill with SIS dimensions (N=5)
/rbac --domain hospital discover               # Pre-fill with Hospital dimensions (N=4)
/rbac --domain saas-simple discover            # Pre-fill with Simple SaaS (N=3)
/rbac dimensions                               # View declared dimensions
/rbac generate                                 # Build everything based on declared dims
/rbac profiles                                 # Manage profiles
/rbac assign dana@acme.com Dean                # Assign profile
/rbac who-can grades:edit --college Engineering --year 2025/2026
/rbac what-can dana@acme.com
/rbac audit                                    # Verify endpoint-to-dims alignment
/rbac test                                     # Decision-table tests
```

---

**What's next?**
- `/pages` — generate admin UI pages (profile editor adapts to declared dims)
- `/shared-components` — ensure admin UI uses shared components
- `/implement --rbac` — build the multi-dim permission system
- `/security` — broader security review
