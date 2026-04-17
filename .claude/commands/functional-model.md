# /functional-model - Classify All App Functionality

Extract every capability of the app into the 5-category functional taxonomy:
**Features / Tools / Tasks / Services / Flows**. Every item becomes admin-manageable,
permission-gated, and auditable.

Based on `.claude/knowledge/functional-taxonomy.md`.

## Usage
```
/functional-model                     # Full inventory (asks questions)
/functional-model generate            # Produce FUNCTIONAL-MODEL.md
/functional-model audit               # Compare code vs model, report gaps
/functional-model add <item>          # Add an item to the model
/functional-model admin-ui            # Scaffold admin pages for every category
```

## The 5 Categories

| Category | Definition | Example |
|----------|-----------|---------|
| **Features** | End-user capabilities | "Submit expense report" |
| **Tools** | Admin/power-user utilities | "Impersonate user", "Bulk import" |
| **Tasks** | Background / scheduled work | "Monthly payroll run", "Sync with device" |
| **Services** | APIs/events exposed to others | "attendance-service", "user-directory" |
| **Flows** | Multi-step orchestrated processes | "Onboarding", "Leave approval" |

## What `/functional-model` asks

```
Let me map your app's full functionality. I'll ask category by category.

=== Features (user-facing) ===
What can END USERS do in this app? List everything — I'll classify later.
(e.g., "submit expense", "apply for leave", "view payslip", "clock in")

=== Tools (admin / power-user) ===
What can ADMINS / SUPPORT / OPS do?
(e.g., "impersonate user", "bulk import", "reset password", "export data")

=== Tasks (background / scheduled) ===
What runs without direct user interaction?
(e.g., "nightly sync", "monthly payroll", "birthday emails", "cleanup")

=== Services (APIs to other apps) ===
What does this app expose for OTHER apps / modules to consume?
(e.g., "user directory API", "attendance API", "notification service")

=== Flows (multi-step processes) ===
What business processes span multiple steps?
(e.g., "employee onboarding", "leave approval", "order fulfillment")
```

For each item collected, ASK:
- Who can use/manage it? (permission scope)
- Is it tenant-specific? (multi-tenancy)
- Dependencies (what does it need from other features/services)?
- Audit: what must be logged when this happens?

## Generated `design/FUNCTIONAL-MODEL.md`

```markdown
# Functional Model: <Project Name>

## Features (User-Facing)
| ID | Name | Scope | Roles | Dependencies | Notes |
|----|------|-------|-------|--------------|-------|
| F01 | Submit expense | Per-user | Employee | File upload service | Requires approval flow F-FLOW-01 |
| F02 | Approve leave | Per-team | Manager | Leave service | Triggers F-FLOW-02 |
| ... | ... | ... | ... | ... | ... |

## Tools (Admin)
| ID | Name | Roles | Audit | Requires Confirmation |
|----|------|-------|-------|----------------------|
| T01 | Impersonate user | Super-admin | YES — who, when, why, IP | Type-to-confirm |
| T02 | Bulk import employees | HR-admin | YES — count, errors, file-hash | Preview before apply |
| ... | ... | ... | ... | ... |

## Tasks (Scheduled)
| ID | Name | Schedule | Retry | DLQ | Owner |
|----|------|---------|-------|-----|-------|
| TK01 | daily-digest | 0 8 * * * | 3x exp | yes | notifications-module |
| TK02 | monthly-payroll | 0 0 1 * * | manual only | yes | payroll-module |
| ... | ... | ... | ... | ... | ... |

## Services (APIs/Events)
| ID | Name | Version | Type | Consumers | SLA |
|----|------|---------|------|-----------|-----|
| S01 | user-directory | v2.1.0 | REST + events | 8 modules | 99.9% |
| S02 | attendance | v1.0.0 | REST + events | 4 modules | 99.95% |
| ... | ... | ... | ... | ... | ... |

## Flows (Orchestrated)
| ID | Name | Trigger | Steps | SLA | Owner |
|----|------|---------|-------|-----|-------|
| FL01 | Onboarding | Hire event | 7 | 2 days | HR + IT + Facilities |
| FL02 | Leave approval | User submit | 4 | 24 hrs | HR |
| ... | ... | ... | ... | ... | ... |

## Permissions (auto-derived)
- `feature:F01:use` → Employee, Manager, HR-Admin
- `tool:T01:run` → Super-admin
- `task:TK02:manage` → Payroll-admin, Super-admin
- `service:S01:consume` → Service-tokens only, per-consumer scopes
- `flow:FL01:manage` → HR-admin
- `flow:FL01:instance:view:own` → Employee (their own onboarding only)

## Admin UI Auto-Generated

After `/functional-model admin-ui`:
- `/admin/features` — list + toggle + config + audit per feature
- `/admin/tools` — catalog + audit trail per tool run
- `/admin/tasks` — scheduler UI with run history + retry
- `/admin/services` — API catalog + consumer list + rate limits
- `/admin/flows` — visual workflow + current instances + SLA tracking
```

## `/functional-model audit` — what it catches

```markdown
## Functional Model Audit

Inventory: 42 declared items
Implemented: 38
Undeclared (in code but not in model): 6 ← add these
Declared but not implemented: 4 ← build these or remove

### Features
F05 "Export to PDF" — declared ✓ implemented ✗
  Recommendation: implement or remove

### Tools
Found T06 "force-logout-user" in code — not in model ✗
  Recommendation: add to FUNCTIONAL-MODEL.md with audit + confirm

### Services
Found /api/internal/*/admin endpoints — not declared as services ✗
  Recommendation: either declare as services (with versioning) or mark as internal-only

### Permissions mismatch
F01 "submit expense" — model says "Employee role"
  Code uses `user.isEmployee && user.department !== 'Temp'` ✗
  Recommendation: move logic to RBAC permission `feature:F01:use`
```

## `/functional-model admin-ui`

Scaffolds the admin pages per category (from `.claude/knowledge/functional-taxonomy.md`):

- Features admin page (list + toggle + config + analytics)
- Tools admin page (catalog + audit)
- Tasks admin page (scheduler + history + retry)
- Services admin page (API catalog + consumers)
- Flows admin page (visual designer + instances)

## Discovery Phase Hook

During Discovery (Complex projects), `/discover mvp` now also runs
`/functional-model` to capture the full inventory upfront, so planning and
implementation match reality.

## Integration

- `/core-modules` — groups items by module boundaries (Services often = module boundary)
- `/rbac` — auto-generates permissions from the model
- `/pages` — auto-generates admin UI entries
- `/implement` — scaffolds the admin UI + scheduler + workflow engine

## Examples

```
/functional-model                     # Interactive inventory
/functional-model generate            # Save FUNCTIONAL-MODEL.md
/functional-model audit               # Find drift between model and code
/functional-model admin-ui            # Scaffold admin pages
/functional-model add "Bulk refund"   # Add a tool to the model
```

---

**What's next?**
- `/core-modules` — Group these into core + modules
- `/rbac` — Generate the full permission matrix
- `/pages` — Auto-scaffold admin UI for each category
- `/implement --admin` — Build the admin pages
