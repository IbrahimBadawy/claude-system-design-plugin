# /integrate - Wire Systems Together (5 Integration Roles)

Connect systems in one of the five architectural roles from `architecture-spec.md`:
**Core**, **Module-of**, **Standalone**, **Bridge**, or **Dependent**. Generates
the anticorruption layer, event bridges, permission mapping, sibling-requirement
checks, and composition code.

## Usage
```
/integrate <core> <module>                        # Module-of: wire module into core
/integrate <a> <b> --bidirectional                # Either can be core
/integrate bridge <system> <coreA> <coreB>        # Bridge: mediate between 2+ cores
/integrate dependent <module> --parent <core> --requires <sibling1>,<sibling2>
                                                  # Dependent: module-of + required siblings
/integrate status                                 # All active integrations
/integrate graph                                  # Visualize system-of-systems
/integrate unwire <core> <module>                 # Disconnect (data preserved)
/integrate verify                                 # Contract + symmetric-uninstall + degradation tests
```

## The 5 Roles Recap (from architecture spec §4)

| Role | Purpose | Command form |
|------|---------|--------------|
| **Core** | Hosts modules | `/core-modules` (not /integrate) |
| **Module-of** | Plugs into a Core | `/integrate <core> <module>` |
| **Standalone** | Runs alone | No integration needed |
| **Bridge** | Mediates 2+ Cores | `/integrate bridge <name> <coreA> <coreB>` |
| **Dependent** | Module-of + requires siblings | `/integrate dependent <mod> --parent --requires` |

## What It Does

Given two apps (both produced by the plugin, or wrapped via `/app-as-module`):

1. **Discover contracts** — read both manifests, find compatibility
2. **Generate adapters** — core's services → module's ports
3. **Generate ACL** — translate terminology between the two domains
4. **Wire event bus** — bridge events (filter + translate + enrich)
5. **Align permissions** — map core's roles to module's permissions
6. **Sync shared kernel** — pick or align UserId / TenantId / time types
7. **Tenant wiring** — propagate tenant context correctly
8. **Write composition root** — the app that boots both together

## Example Flow (HR + Attendance)

```bash
# HR exists as a core-capable app
# Attendance exists as a standalone that was wrapped via /app-as-module

/integrate hr-system attendance

# Claude asks:
Q1: Is hr-system the core (provides identity/auth) and attendance the module?
  > yes

Q2: I see two different user concepts:
    HR has: Employee { id, fullName, email, departmentId, managerId, status }
    Attendance has: User { id, name, teamId, isActive }
   How should I map?
  > Map Employee.id → User.id (same UUIDs)
  > Map Employee.fullName → User.name
  > Map Employee.departmentId → User.teamId
  > Employee.status='active' && !terminationDate → User.isActive

Q3: Permissions mapping:
    HR roles: Super-admin, HR-Admin, Manager, Employee
    Attendance permissions: attendance:read:own, :team, :all, :manage:team, :override
   Recommended mapping:
    Super-admin          → attendance:* (all)
    HR-Admin             → attendance:read:all, attendance:override:all
    Manager              → attendance:read:team, attendance:manage:team
    Employee             → attendance:read:own
   Accept? (y/n/edit)
  > y

Q4: Event bridging:
    attendance emits: checked-in, checked-out, anomaly-detected
    HR wants to consume: none (HR doesn't care about every check-in)
    Subscribe HR to anomaly-detected only? (for HR report)
  > y

Q5: Admin UI mounting:
    Mount attendance admin at /admin/attendance inside HR's shell?
  > y

Q6: Database strategy:
    1. Attendance keeps its own tables (recommended)
    2. Share HR's database (more coupling)
  > 1

Q7: Deployment:
    1. Monolith (both in one process) — simplest
    2. Separate services, HTTP between them — more infra but decoupled
  > 1 for now, maybe 2 later
```

## Generated Files

### `integrations/hr-system__attendance/` (new folder)

```
integrations/hr-system__attendance/
├── INTEGRATION.md              # Human-readable overview
├── composition.ts              # Wires both apps together
├── acl.ts                      # Anticorruption layer
├── event-bridge.ts             # Event translation + filtering
├── permission-mapping.ts       # Role → permission map
├── shared-kernel.ts            # Aligned UserId, TenantId types
├── nav-integration.ts          # Mount module's admin UI in core's shell
└── tests/
    ├── contract.test.ts        # Verify both sides keep contracts
    ├── event-flow.test.ts      # End-to-end event bridge test
    └── acl.test.ts             # ACL correctness
```

### `composition.ts` (example)

```ts
import { startHRCore } from '@hr-system';
import { attendanceLifecycle } from '@attendance';
import { acl } from './acl';
import { mapEventIn, mapEventOut } from './event-bridge';
import { rolePermissionMap } from './permission-mapping';

async function boot() {
  const hr = await startHRCore();

  // Wire attendance as a module of hr
  const attendance = await attendanceLifecycle.install(hr.moduleContext);

  // Provide the adapters
  attendance.connect({
    userDirectory: {
      getUserById: async (id) => {
        const emp = await hr.employees.findById(id);
        return emp ? acl.hrEmployeeToAttendanceUser(emp) : null;
      },
      listUsersByDepartment: async (deptId) => {
        const emps = await hr.employees.findByDepartment(deptId);
        return emps.map(acl.hrEmployeeToAttendanceUser);
      },
    },
    auth: {
      verifyToken: hr.auth.verifyToken,
      hasPermission: (principal, permission) => {
        return rolePermissionMap[principal.role]?.includes(permission) ?? false;
      },
    },
    events: {
      publish: (topic, payload) => hr.eventBus.publish(topic, mapEventOut(topic, payload)),
      subscribe: (topic, handler) => hr.eventBus.subscribe(topic, async (e) => {
        const mapped = mapEventIn(topic, e);
        if (mapped) await handler(mapped);
      }),
    },
  });

  // Mount admin UI under HR's shell
  hr.nav.mount('/admin/attendance', attendance.adminUI);

  await hr.start();
}

boot().catch(console.error);
```

### `acl.ts` (Anticorruption Layer)

```ts
import type { HREmployee } from '@hr-system/domain';
import type { User } from '@attendance/domain';

export const acl = {
  hrEmployeeToAttendanceUser(emp: HREmployee): User {
    return {
      id: emp.id,
      name: emp.fullName,
      teamId: emp.departmentId,
      isActive: emp.status === 'active' && !emp.terminationDate,
    };
  },

  attendanceAnomalyToHRNotification(anomaly: AnomalyEvent): HRNotification {
    return {
      type: 'attendance-anomaly',
      employeeId: anomaly.userId,
      severity: anomaly.type === 'no-checkout' ? 'warning' : 'info',
      message: `Employee had attendance anomaly: ${anomaly.description}`,
      createdAt: anomaly.timestamp,
    };
  },
};
```

### `event-bridge.ts`

```ts
// Filters + translates events between HR's bus and attendance's bus
export function mapEventOut(topic: string, payload: unknown): unknown {
  // Module emits → core bus
  // Translate attendance event names to HR's conventions if needed
  return payload;
}

export function mapEventIn(topic: string, event: unknown): unknown | null {
  // Core emits → module subscribers
  // Return null to filter out events the module doesn't care about
  if (topic === 'hr.employee.salary-changed') return null; // attendance doesn't care
  if (topic === 'hr.employee.terminated') return { userId: event.employeeId }; // enrich
  return event;
}
```

### `INTEGRATION.md`

```markdown
# Integration: HR System + Attendance

**Core:** hr-system v2.3.0
**Module:** attendance v1.0.0
**Mode:** Monolithic (single deployable)
**Created:** 2026-04-16

## What This Integration Does

HR system hosts the attendance module as a mounted component. Attendance
consumes HR's user directory and auth; HR receives attendance anomaly events
for its manager alerts.

## Ports Wired

| Port | Implementation |
|------|----------------|
| UserDirectoryPort | HR's EmployeeService (via ACL) |
| AuthPort | HR's AuthService |
| EventBusPort | HR's shared event bus |
| StoragePort | HR's S3 adapter |

## Events Bridged

| Event | Direction | Filter | Transform |
|-------|-----------|--------|-----------|
| attendance.checked-in | module → core | no subscribers in core | — |
| attendance.anomaly-detected | module → core | all | → hr.notification |
| hr.employee.terminated | core → module | all | → deactivate user |
| hr.employee.salary-changed | core → module | filtered out | — |

## Permission Mapping

... table ...

## Admin UI

Mounted at `/admin/attendance` within HR's shell.

## Deployment

Single deployable. Both modules share:
- Process / runtime
- Event bus (in-memory)
- Database cluster (but separate schemas: hr.* and attendance.*)

## Future: Split to Services

If scaling needs separation, this integration can move to:
- HR as a service, attendance as a service, events over NATS, HTTP for queries.
  The composition file changes; the contracts don't.

## Contract Tests

Run: `npm run test:integration:hr-attendance`
Covers: user lookup translation, event bridge, permission mapping,
tenant context propagation.
```

## `/integrate verify`

Runs contract tests to ensure:
- Every port a module needs has a mapping in composition
- Every event the module subscribes to has a bridge rule (or explicit filter)
- Every permission the module checks has a role mapping
- Tenant context propagates correctly
- ACL doesn't lose data in round-trips

```
## Integration Verification: hr-system ↔ attendance

### Ports — 5/5 wired ✓
- UserDirectoryPort → HR.EmployeeService (via ACL)
- AuthPort → HR.AuthService
- EventBusPort → HR.SharedBus
- StoragePort → HR.S3
- NotificationPort → HR.NotificationService

### Events — 3 bridged, 2 filtered, 0 unhandled ✓
All attendance events have destinations or explicit filters.
All HR events attendance cares about are bridged.

### Permissions — 5/5 mapped ✓
Every role in HR has a predictable attendance permission set.

### Tenant context ✓
All cross-bridge calls include tenantId.

### ACL round-trip ✓
HR.Employee → Attendance.User → reverse → original (lossless for relevant fields).

Integration is healthy.
```

## Integration Scenarios (from architecture spec §4)

### Scenario A: Module-of — HR core + Attendance module

HR provides identity. Attendance plugs in via ACL (Employee ↔ User).

```bash
/integrate hr-system attendance
```

### Scenario B: Reverse — Attendance was standalone, HR wraps it

Attendance ran standalone. Now HR is the new core; attendance becomes module-of.
Same command; `/app-as-module --for hr-system` was run first.

### Scenario C: Bidirectional

Both apps CAN be the core. Useful when deploying differently per customer.
Same codebase, different boot config.

```bash
/integrate hr-system attendance --bidirectional
```

### Scenario D: Bridge — Payroll Calculator between HR + Attendance

A Payroll Calculator system pulls employee data from HR and time data from
Attendance, computes payroll, writes results back. Acts as **Bridge**:

```bash
/integrate bridge payroll-calculator hr-system attendance
```

Generates:
- `payroll-calculator/manifest.json` with `"roles": ["bridge"]` and declared consumers
- ACL mapping both Cores' domain models → Payroll's internal model
- Event subscriptions: `hr.employee.salary-changed`, `attendance.daily-summary`
- No domain data ownership — only mapping/coordination state

### Scenario E: Dependent — Overtime Approval needs HR + Attendance siblings

An Overtime Approval module is Module-of HR, but ALSO requires Attendance to
be installed on the same HR Core:

```bash
/integrate dependent overtime-approval \
    --parent hr-system \
    --requires hr-employees@^2,attendance@^1
```

The Core now enforces: overtime-approval cannot be activated unless both
`hr-employees` AND `attendance` are installed + enabled on the same Core.

### Scenario F: Fractal — Federation of enterprise systems

HR, ERP, CRM all integrated as siblings under an Enterprise Federation Core,
with bridges between specific pairs. Same pattern, larger scale.

```bash
/integrate enterprise-fed hr-system        # HR as module of federation
/integrate enterprise-fed erp-system       # ERP as module of federation
/integrate bridge hr-erp-sync hr-system erp-system
```

## The 3 Integration Types

| Type | Manifest role | What `/integrate` generates |
|------|--------------|----------------------------|
| **Module-of** | `"roles": ["module-of"], "parent": {...}` | ACL + event bridge + permission map + composition |
| **Bridge** | `"roles": ["bridge"], "bridges": [...]` | Dual ACLs + cross-core event router + mapping state store |
| **Dependent** | `"roles": ["module-of"], "requiresSiblings": [...]` | Sibling check on enable + dependency graph + fail-fast on missing |

## Integration with Other Commands

- `/app-as-module` — wrap standalone app first, then `/integrate` to wire it
- `/core-modules` — design the whole core + module architecture upfront
- `/module events map` — visualize the event graph
- `/rbac` — align permissions between integrated apps
- `/test contract` — contract tests between integrated systems

## Examples

```
/integrate hr-system attendance                # Wire them together
/integrate erp crm --bidirectional             # Either can be the core
/integrate status                              # See all active integrations
/integrate verify                              # Contract check
/integrate unwire hr-system attendance         # Separate them again
```

---

**What's next?**
- `/module events map` — see the event graph across integrated systems
- `/test contract` — write contract tests
- `/implement --integration` — generate the composition code
