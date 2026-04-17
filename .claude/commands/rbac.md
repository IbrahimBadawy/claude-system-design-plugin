# /rbac - 5-Dimensional Permissions & Access Control

Design a production-grade permission system following `architecture-spec.md §8`
and `permissions-model.md`. Not a simple `is_admin` boolean — a full **5-dimensional
model** with Profiles, assignment modes, specialized reports, and mandatory
discovery-phase questions.

## The 5 Dimensions

Every permission is the intersection of 5 dimensions. **All 5 must align** for access.

| # | Dimension | Example values |
|---|-----------|---------------|
| 1 | **Organizational scope** (hierarchical) | University → College → Department → Program → Cohort |
| 2 | **Academic year** (annual) | Specific year, range, or all |
| 3 | **Semester** (sub-annual) | First, Second, Summer, range, or all |
| 4 | **Functional scope** (app hierarchy) | Main App → Sub-App → Feature |
| 5 | **Action type** | View · Insert · Edit · Close · Open · Delete (+ custom per module) |

## Usage

```
/rbac                              # Interactive design: asks the §8.6 mandatory questions
/rbac --domain <domain>            # Domain-specific default profiles (sis, hospital, erp, retail)
/rbac discover                     # Run mandatory discovery-phase questions (§8.6)
/rbac generate                     # Generate schema + seeds + middleware + admin UI + reports
/rbac profiles                     # Manage profiles (list / create / clone / version / archive)
/rbac roles                        # Legacy alias — profiles are the new primary abstraction
/rbac permissions                  # Declare action types per module
/rbac assign <user> <profile>      # Assign profile (or group or override)
/rbac who-can <action> <scope>     # Report: who can do X in Y? (§8.5 report 1)
/rbac what-can <user>              # Report: what can user Z do? with source chain (report 2)
/rbac profiles-with <action>       # Report: which profiles grant X? (report 3)
/rbac changes --last 30d           # Report: recent permission changes (report 4)
/rbac audit                        # Verify every endpoint is 5-dim guarded
/rbac test                         # Run decision-table tests
```

## Discovery Phase Questions (from §8.6 — MANDATORY)

When a project has non-trivial permissions, `/rbac discover` MUST ask these
before any implementation:

```
Question 1: Organizational hierarchy
  What's the shape of your org hierarchy? How many levels?
  Examples:
    SIS: University → College → Department → Program → Cohort
    Hospital: Group → Hospital → Department → Ward → Shift
    Retail: HQ → Region → Country → City → Store → Aisle
    Factory: Company → Plant → Line → Cell → Machine
  Enter level names (root → leaf):

Question 2: Time scoping
  Are permissions time-scoped?
    [ ] Academic years (for education)
    [ ] Fiscal years (for finance)
    [ ] Semesters/terms
    [ ] Campaigns / seasons
    [ ] None — permissions are timeless
  Time shapes:

Question 3: Functional hierarchy (apps → sub-apps → features)
  What's the app tree to secure?
  Example:
    Academic
      ├── Enrollment
      ├── Grades
      │   ├── Enter Grades
      │   ├── Publish Grades
      │   └── Reopen Grades
      └── Transcripts
    Student Affairs
      ├── Attendance
      └── Conduct
  Paste your app tree:

Question 4: Action types beyond standard 6
  Standard: view / insert / edit / close / open / delete
  Additional (tick all that apply):
    [ ] Approve   [ ] Reject    [ ] Export   [ ] Import
    [ ] Print     [ ] Publish   [ ] Unpublish [ ] Submit
    [ ] Assign    [ ] Impersonate [ ] Lock   [ ] Unlock
    [ ] Other: ___

Question 5: Initial profiles to ship
  List profiles needed from day 1 (with short description each):
  Suggestions (pick + modify):
    - Super Admin
    - Tenant Admin (per-university / per-hospital / per-company)
    - Mid Admin (Dean / Department Head / Manager)
    - Operator (Faculty / Doctor / Staff)
    - Support (Student Affairs / Registry / Helpdesk)
    - End User (Student / Patient / Customer)
    - Guest / Visitor

Question 6: Default scopes + actions per profile
  For each profile above, which (dim1, dim2, dim3, dim4, dim5) defaults?
  I'll ask per profile ...

Question 7: Group + individual overrides
  Do you need:
    [ ] Group-based grants (e.g., "all department chairs in Engineering")
    [ ] Individual user overrides (deny access even if profile grants it)
    [ ] Temporary overrides (expires after date)

Question 8: Guests / Visitors
  Does the system accept unauthenticated visitors?
    [ ] Yes — public pages only
    [ ] Yes — authenticated visitors (parents, prospective students)
    [ ] No
  If yes: rate limits? Allowed actions? Audit?

Question 9: Day-1 permission reports required
  Standard 4 reports:
    [✓] Who can do X in scope Y?
    [✓] What can user Z do (with source chain)?
    [✓] Which profiles grant action X?
    [✓] Recent permission changes (last N days)
  Additional reports needed?

Question 10: Audit + compliance requirements
  Retention of audit logs: ___ years
  Regulations: [ ] GDPR [ ] HIPAA [ ] FERPA [ ] SOX [ ] PCI [ ] Other
  Right to be forgotten: [ ] yes [ ] no
  Data residency constraints: ___
```

**Answers saved to `projects/<project>/design/PERMISSIONS.md`.**

**Without answers** → `/implement` refuses to scaffold permission-sensitive
modules.

---

## Profiles — the Primary Abstraction

A **Profile** is a named, reusable bundle of permissions across the 5
dimensions. **Administrators assign profiles, not individual permissions.**

- Each dimension: specific value, range, or "all"
- Multiple profiles per user → union (minus explicit denials)
- Versioned: rollback supported
- Can be tenant-specific or universal

### Example — Dean profile for SIS

```jsonc
{
  "name": "Dean",
  "description": "Full authority within the college for the current academic year",
  "version": 1,
  "grants": [
    {
      "orgScope": "{collegeId}",
      "includesDescendants": true,
      "yearFrom": "current",
      "yearTo": "current",
      "semesters": ["first", "second", "summer"],
      "appScope": "academic.*",
      "actions": ["view", "approve"],
      "effect": "grant"
    },
    {
      "orgScope": "{collegeId}",
      "yearFrom": "current", "yearTo": "current",
      "semesters": ["first", "second", "summer"],
      "appScope": "grades.finalize",
      "actions": ["approve"],
      "effect": "grant"
    },
    {
      "orgScope": "{collegeId}",
      "yearFrom": "historical",
      "appScope": "*",
      "actions": ["view"],
      "effect": "grant"
    }
  ]
}
```

---

## Assignment Modes (§8.4)

| Mode | When | Specificity |
|------|------|-------------|
| **Profile-based** | User receives a profile; permissions derive | Broadest |
| **Group-based override** | Extra grant/deny for group members | Middle |
| **User-specific override** | Grant/deny for one user, overrides profile + group | Most specific |

**Denials always win over grants.** Deterministic evaluation; see
`permissions-model.md` for the algorithm.

```bash
/rbac assign user:dana@acme.com profile:Dean                  # profile-based
/rbac assign group:engineering-chairs profile:Department-Head  # group-based
/rbac override user:dana@acme.com --action grades:delete --effect deny --reason "on-leave"
```

---

## Generated Artifacts

### `design/PERMISSIONS.md`
Complete answers to §8.6 questions + derived permission model.

### Database schema (from `permissions-model.md`)
- `org_nodes` (hierarchy with LTREE)
- `academic_years`, `semesters`
- `apps` (functional hierarchy)
- `action_types` (core + module-extensible)
- `profiles`, `profile_grants`
- `user_profiles`, `groups`, `group_members`, `group_profiles`
- `user_overrides`
- `permission_audit`

### Seeds
- Action types: view, insert, edit, close, open, delete (+ module extras)
- Starter profiles (from Question 5 + 6)
- Your domain hierarchy (from Question 1)

### Backend middleware
```typescript
@Permissions('grades:edit')
@Post('grades/:id')
async updateGrade(
  @CurrentUser() user: User,
  @OrgScope() org: OrgNode,          // from URL param or header
  @AcademicContext() acad: AcadContext
) { ... }
```

```python
@router.post("/grades/{id}")
async def update_grade(
  user: User = Depends(require_permission("grades:edit")),
  acad: AcadContext = Depends(extract_acad_context),
  org: OrgNode = Depends(extract_org_scope),
): ...
```

### Frontend integration
```tsx
<Can
  action="grades:edit"
  org={collegeId}
  year={currentYear}
  semester={currentSemester}
  app="grades.finalize"
  fallback={<AccessDeniedBanner />}
>
  <GradesEditForm />
</Can>

const { allowed, reasonChain } = usePermission('grades:edit', ctx);
```

### Admin UI (§8.5 required)

- **User management** — full CRUD, reset password, MFA, sessions, lock/unlock
- **Profile management** — create, clone, version, archive
  - Visual 5-dimensional grant builder (tabs for each dimension)
  - "Preview: who gains/loses access if this ships"
- **Assignment management** — profile + group + user override
  - Live "effective permissions" preview for selected user
- **Visitor / guest management** (if applicable)
- **Permission reports** — the 4 canonical reports (see below)
- **Audit log** — searchable, filterable, exportable

### The 4 Canonical Reports (§8.5)

Generated as first-class admin pages:

#### 1. "Who can do X?"
```
/rbac who-can grades:edit --college "Engineering" --year 2025/2026 --semester first
```
```
Users who can edit grades in College Engineering for 2025/2026-first:
  12 users total

By source:
  Profile: Dean of Engineering    → Dana K. (direct)
  Profile: Dept Head              → Ahmed M., Sara O., ... (9 users)
  Group: grading-committee        → Lina F., Omar Z.
  User override (grant)           → Rami Y.

User override (deny):
  None
```

#### 2. "What can user Z do?"
```
/rbac what-can dana@acme.com
```
```
Dana K. (user:abc123)

Effective permissions tree:
  Academic                             (from Profile: Dean of Engineering)
    ├── Grades                         (view, approve)
    │   └── Finalize                   (approve)
    ├── Enrollment                     (view)
    └── Transcripts                    (view)
  Administration                       (from Profile: Dean of Engineering)
    ├── Faculty Mgmt                   (view, edit, approve)
    └── Reports                        (view, export)

Denials:
  Billing.financial-aid.edit           (from Group: on-leave)

Historical (view-only, year < 2025/2026):
  All academic features               (from Profile: Dean of Engineering)
```

#### 3. "Which profiles grant X?"
```
/rbac profiles-with action:grades:delete scope:"College Engineering"
```
```
Profiles that confer grades:delete in College Engineering:

  Profile: Super Admin          (global; 3 users)
  Profile: University Admin     (university scope; 5 users)
  Profile: Data-Admin Override  (college scope; 2 users)

Group overrides: none
Individual overrides: 1 user (with reason: audit-cleanup project)
```

#### 4. Recent changes
```
/rbac changes --last 30d --actor-type admin
```
```
Permission changes in the last 30 days (admin actors only):

2026-04-14 09:12 | Sara O. | GRANTED  | Profile "Dept Head" to Ahmed M. | reason: promoted
2026-04-12 14:33 | Sara O. | DENIED   | user-override grades:delete to Lina F. | reason: on leave
2026-04-08 10:05 | Admin   | CREATED  | Profile "Data Admin Override" v1 | reason: new compliance role
... (43 total)

Export: CSV | PDF | JSON
```

---

## `/rbac audit`

Scans codebase for permission violations:

```markdown
## 5-Dim RBAC Audit

Endpoints scanned: 127
Permission-guarded: 122 ✓
Unguarded (intentional public): 3 (/login, /register, /forgot) ✓
Unguarded (MISSING guard): 2 ✗

1. POST /api/v1/grades/bulk-delete
   → add `@Permissions('grades:delete')` + dimension context extraction
2. GET /api/v1/reports/financial
   → unguarded; should require `reports:financial:view`

Endpoints using wrong dimension count: 4
  These use 3-dim permissions but should use 5-dim:
  - grades:edit → should include (org, year, semester, app-node)

Destructive actions without audit: 1
  POST /users/:id/delete → add audit log write

Profile coverage:
  Super Admin:   covers 94% of actions
  Dean:          covers 62% of actions — gaps: [billing, hr-admin]
  Faculty:       covers 8% of actions ✓ (expected)
  Student:       covers 3% of actions ✓ (expected)

Dead permissions (declared but never checked): 4
  - `reports:print:own` (no endpoint requires it)
  - ... 3 more

Action types defined but not used: 2
  - "submit" (declared by module `forms`)
  - "archive" (declared by module `shared`)

Recommendation: fix 2 missing guards + 4 dimension mismatches + 1 audit gap
before shipping.
```

---

## Domain-Specific Starter Profiles

`/rbac --domain <domain>` ships tuned defaults per domain:

- **sis** (education) — Super Admin, University Admin, Dean, Dept Head, Faculty, Student Affairs, Student, Guest
- **hospital** — System Admin, Hospital Admin, Chief, Department Head, Doctor, Nurse, Front Desk, Patient, Guest
- **erp** — System Admin, Company Admin, CFO, Finance Manager, Accountant, Warehouse Supervisor, Worker, Auditor
- **retail** — System Admin, Regional Manager, Store Manager, Shift Lead, Associate, Customer
- **factory** — Plant Admin, Line Supervisor, Operator, Quality Inspector, Maintenance

Each ships with sensible scope defaults (Dim 1) and action sets (Dim 5).

---

## Testing

`/rbac test` runs decision-table tests:

```
## Decision Table Tests

For profile "Dean":
  ✓ can view grades in own college, current year, any semester
  ✓ can approve grades in own college, current year, current/prior semester
  ✗ can edit grades in different college  [correctly denied]
  ✓ can view grades in historical year
  ✗ can approve grades in historical year [correctly denied]

For user "Dana" (Dean + group:grading-committee + override:deny-grades-delete):
  ✓ can edit grades in Engineering (via Dean profile)
  ✗ cannot delete grades (explicit user-level deny wins over group grant)
  ✓ group grant applies for approve:own-college

Denial-wins test:
  12 test cases checking user/group deny beats profile grant: all ✓

Scope boundary tests:
  Grant at college → covers departments: ✓
  Grant at college → does NOT cover sibling college: ✓
  Grant at college with includesDescendants=false → only college-level: ✓

All tests passed (47/47)
```

---

## Integration with Other Commands

- `/discover permissions` — runs the 10 mandatory §8.6 questions
- `/core-modules` — module manifests declare `permissions.provides` + `.consumes`
- `/pages` — admin UI pages derived from this command
- `/implement` — refuses to scaffold permission-sensitive modules until
  `design/PERMISSIONS.md` exists
- `/security` — includes RBAC audit in the security review

---

## Examples

```
/rbac discover                                 # Start the 10 mandatory questions
/rbac --domain sis generate                    # Full generate with SIS defaults
/rbac profiles                                 # Manage profiles
/rbac assign dana@acme.com Dean                # Assign profile
/rbac who-can grades:edit --college Engineering --year 2025/2026
/rbac what-can dana@acme.com
/rbac profiles-with grades:delete
/rbac changes --last 30d
/rbac audit                                    # Verify coverage
/rbac test                                     # Decision-table tests
```

---

**What's next?**
- `/pages` — generate admin UI pages for user mgmt, profile mgmt, reports
- `/shared-components` — ensure the admin UI uses shared components
- `/implement --rbac` — build the 5-dim permission system in code
- `/security` — broader security review
