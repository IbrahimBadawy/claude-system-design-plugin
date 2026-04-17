---
description: Every app classifies its capabilities into Features/Tools/Tasks/Services/Flows and generates admin UIs for each. "Backend code that works" is not enough — the whole app must be manageable from the admin panel (toggle, configure, audit, scheduled, retry, permission-gate).
globs: "*"
---

# Rule 30: Functional Completeness

## The Rule

Every app must classify its full functionality into the **5-category taxonomy**
(Features, Tools, Tasks, Services, Flows) AND generate an admin UI for each.

The app is "done" when the admin can:
- See every feature in one list with enable/disable toggles
- See every tool with its audit log
- Manage every scheduled task (run now / pause / view history / retry)
- Browse every service's API catalog with consumer usage
- Edit every workflow visually and see live instances

Not before.

## The 5 Categories (details in `.claude/knowledge/functional-taxonomy.md`)

1. **Features** — user-facing capabilities ("Submit expense")
2. **Tools** — admin/operator utilities ("Impersonate user")
3. **Tasks** — background/scheduled work ("Monthly payroll")
4. **Services** — APIs/events exposed to others ("attendance-service")
5. **Flows** — multi-step orchestrated processes ("Onboarding")

## What's Required

Every app MUST:

- [ ] Have a `design/FUNCTIONAL-MODEL.md` listing every capability by category
- [ ] Auto-generate admin pages per category:
  - `/admin/features` — list + toggle + config + analytics
  - `/admin/tools` — catalog + audit trail per run
  - `/admin/tasks` — scheduler UI (next run, last run, retry, pause)
  - `/admin/services` — API catalog + consumer list + rate limits + versioning
  - `/admin/flows` — visual workflow designer + live instances
- [ ] Wire permissions per category:
  - `feature:<id>:use`
  - `tool:<id>:run`
  - `task:<id>:manage`
  - `service:<id>:consume`
  - `flow:<id>:manage`
- [ ] Audit every tool run + destructive feature + flow transition
- [ ] Make every task controllable: pause, retry, manual-trigger, view-history
- [ ] Version every service's API
- [ ] Support per-tenant enable/disable for every capability

## What's Banned

- Hidden endpoints — if code exists for it, it's declared in the model
- `cron: '0 * * * *'` with no admin visibility
- Feature flags hidden in env vars (they should be in admin UI)
- Tools that aren't audited
- Services without versioning
- Flows implemented as spaghetti `if/else` instead of state machines

## Relation to Other Rules

- **Rule 24 (UX Completeness)**: every admin page has 5 states + feedback
- **Rule 25 (RBAC by Default)**: permissions per category auto-derived
- **Rule 29 (Modular by Default)**: Services often = module boundaries
- **Rule 23 (Milestone Validation)**: each milestone ships complete admin UI, not just backend

## Complexity-Driven Scope

- **Simple:** classify features + minimal admin page (toggle, config)
- **Medium:** all 5 categories + admin UI for each + audit log
- **Complex:** above + visual workflow designer + API catalog + module catalog + per-tenant controls + analytics

## Enforcement

- `/functional-model audit` — compare model vs code, report drift
- `/pages audit` — verify admin pages exist for every category
- Pre-merge CI check: new route/handler without an entry in FUNCTIONAL-MODEL.md = fail

## Why This Rule Exists

- Most plugin-generated apps ship with good structure but look half-built to the admin
- Users expect to toggle features, not read feature-flag files
- Operations teams need to retry failed tasks without SSH access
- Compliance requires audit logs for all sensitive actions — declaring them upfront
  makes that automatic
- The user's own words: "don't just give me backend code that works; give me an app
  I can fully manage"

## Escape Hatch

Admin UI may be skipped ONLY for:
- **Simple projects** (single-user tools, internal scripts)
- **Capabilities explicitly marked `internal-only`** in the model (with justification)
- Development/debug endpoints clearly gated by `NODE_ENV !== 'production'`
