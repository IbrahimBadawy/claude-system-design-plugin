# /app-as-module - Wrap Existing App to Be Pluggable

Take an existing standalone app and make it pluggable into ANOTHER core system —
without breaking its ability to run standalone. Creates the manifest, adapters,
contracts, and ACL (anticorruption layer).

**Scenario:** you built an attendance system standalone. Later you need it to plug
into an HR system as a module. This command wraps it.

## Usage
```
/app-as-module                      # Wrap the current project as a pluggable module
/app-as-module --for <core-app>     # Wrap specifically for a known core
/app-as-module contracts            # Extract the public contract surface
/app-as-module acl                  # Generate the anticorruption layer
/app-as-module verify               # Verify module contracts are self-contained
```

## What It Does

1. **Analyze** the existing app:
   - Find entry points (routes, event handlers)
   - Find database tables owned by this app
   - Find external dependencies (what it needs from outside)
   - Find internal-only code vs public surface

2. **Extract public contract** into `module.manifest.json`:
   - Events it publishes
   - Events it consumes
   - API surface (versioned)
   - Required ports (user-directory, auth, etc.)
   - Provided ports
   - Permissions it owns

3. **Create ports** for everything the app currently imports directly from
   "core-ish" things. Wraps them so a future core can implement the ports.

   Before:
   ```ts
   import { getUserById } from './db/users';   // internal coupling
   ```
   After:
   ```ts
   export interface UserDirectoryPort {
     getUserById(id: UserId): Promise<User | null>;
   }
   // Standalone: use internal impl.
   // Module mode: receive port from host core.
   ```

4. **Generate Anticorruption Layer (ACL)** skeletons for common core systems:
   - If target core is specified (`--for hr-system`), generate the mapping:
     - HR's `Employee` → Attendance's `User`
     - HR's `Manager-of` relationship → Attendance's team hierarchy
     - HR's permissions → Attendance's permissions

5. **Dual-boot support**:
   - Keep `main.ts` for standalone
   - Add `as-module.ts` export for module mode
   - Config loader checks environment

   ```ts
   // src/index.ts
   export { attendanceLifecycle } from './lifecycle';
   export { AttendancePort } from './contracts/ports';
   export type { CheckInEvent, CheckOutEvent } from './contracts/events';

   // src/main.ts (standalone)
   import { start } from './server';
   start(); // uses internal user-directory

   // src/as-module.ts (pluggable)
   import { attendanceLifecycle } from '.';
   export default attendanceLifecycle;
   ```

## Questions It Asks

```
Wrapping <current-project> as a pluggable module.

Q1: Is there a specific core system it will plug into?
  1. Specific core (e.g., HR system, ERP) — I'll generate the ACL for it
  2. Generic — just make it pluggable to any compatible core
  3. Not sure yet

Q2: What does this app own exclusively?
  (tables, entities — these stay internal and DON'T become shared)

Q3: What does this app need from the host core?
  - User/employee directory?
  - Authentication?
  - Organization structure (departments, teams)?
  - Roles/permissions catalog?
  - Notification service?
  - File storage?

Q4: Public surface — what should be callable from other modules?
  - APIs (versioned)?
  - Events (schema)?
  - UI components (shareable React/Vue components)?
  - Admin pages (mounted at /admin/<module-name>)?

Q5: Standalone mode support — keep it?
  Recommended YES — the same codebase works both ways.

Q6: Packaging:
  1. Monorepo package (pnpm workspace / Turborepo)
  2. npm-published package
  3. Git submodule
  4. Copy-paste (fork)
```

## Output Files

### `module.manifest.json`
Full manifest (see `/core-modules`).

### `src/contracts/ports.ts`
All ports this module needs from a core:
```ts
export interface UserDirectoryPort {
  getUserById(id: UserId): Promise<User | null>;
  listUsersByDepartment(deptId: string): Promise<User[]>;
}

export interface AuthPort {
  verifyToken(token: string): Promise<Principal>;
  hasPermission(principal: Principal, permission: string): boolean;
}

export interface EventBusPort {
  publish<T>(topic: string, payload: T): Promise<void>;
  subscribe<T>(topic: string, handler: (e: T) => Promise<void>): void;
}
```

### `src/contracts/events.ts`
Event schemas this module emits/consumes:
```ts
export type CheckInEvent = {
  version: 1;
  userId: UserId;
  timestamp: ISODateTime;
  location?: GeoPoint;
  method: 'fingerprint' | 'face' | 'badge' | 'manual';
};

export type CheckOutEvent = { ... };
export type AnomalyDetectedEvent = { ... };
```

### `src/contracts/api.openapi.yaml`
OpenAPI 3.1 spec for the HTTP surface.

### `src/acl/<core-name>.ts` (if `--for <core>`)
Anticorruption layer:
```ts
// ACL: bridges HR domain to Attendance domain
import type { HREmployee, HRDepartment } from '@hr-system/shared';
import type { User, Team } from '../contracts/domain';

export function hrEmployeeToAttendanceUser(emp: HREmployee): User {
  return {
    id: emp.id as UserId,
    name: emp.fullName,
    teamId: emp.departmentId as TeamId,
    isActive: emp.status === 'active' && !emp.terminationDate,
  };
}

export function attendanceEventToHRFormat(event: CheckInEvent): HRAttendanceRecord {
  // Map our simpler event to HR's richer format
  return { ... };
}
```

### `src/standalone-adapters/` (only if keeping standalone mode)
Implementations of the ports using local code — for running standalone.
```ts
export class LocalUserDirectory implements UserDirectoryPort {
  async getUserById(id: UserId) {
    return db.users.findUnique({ where: { id } });
  }
}
```

### `src/module-bootstrap.ts`
How to initialize as a module:
```ts
export function initAttendanceModule(ports: {
  userDirectory: UserDirectoryPort;
  auth: AuthPort;
  events: EventBusPort;
  storage: StoragePort;
}) {
  // Compose with the host's ports
  const service = new AttendanceService(ports.userDirectory);
  registerEventHandlers(ports.events, service);
  registerAPIRoutes(ports.auth, service);
  return {
    lifecycle: attendanceLifecycle,
    api: service,
  };
}
```

## `/app-as-module verify`

Checks that the module doesn't leak internals:

```
## Module Contract Verification

### External imports (only from ports) — ✓ 5 clean
- UserDirectoryPort (used in 3 files)
- AuthPort (used in 8 files)
- EventBusPort (used in 5 files)

### Direct imports of other modules — VIOLATION
- src/reports/service.ts imports ../other-module/db (internal)
  Fix: expose the data via an event or a published port

### Shared types used — OK (all from @shared-kernel)
- UserId, TenantId, ISODateTime

### Internal tables only — ✓
- attendance_records, attendance_shifts, attendance_anomalies (owned)
- NOT touching: users, departments (those are core's)

### Events — ✓ 3 emitted, 2 consumed, all schema-documented

### API — ✓ OpenAPI 3.1 spec present, matches implementation

### Standalone mode — ✓ starts with local adapters
### Module mode — ✓ starts with injected ports

Module is self-contained and can be plugged in.
```

## Real-World Example

User's example: attendance system → HR core.

```bash
# 1. You built attendance-system standalone. It's running.
cd projects/attendance-system

# 2. Now wrap it to be HR-compatible.
/app-as-module --for hr-system

# Generates:
# - module.manifest.json
# - src/contracts/ports.ts (UserDirectoryPort, AuthPort, EventBusPort)
# - src/contracts/events.ts (CheckInEvent, etc.)
# - src/contracts/api.openapi.yaml
# - src/acl/hr-system.ts (maps HR.Employee ↔ Attendance.User)
# - src/standalone-adapters/ (for standalone mode)
# - src/module-bootstrap.ts

# 3. Standalone keeps working:
npm run start  # uses local adapters

# 4. In HR system, install it:
cd ../hr-system
pnpm add attendance-system
# Then in your HR app composition:
initAttendanceModule({
  userDirectory: hrUserDirectory,
  auth: hrAuth,
  events: hrEventBus,
  storage: hrS3Adapter,
});
```

## Integration

- `/core-modules` — design the architecture that this command then implements
- `/integrate` — wire this module INTO a specific core
- `/module events map` — visualize events flowing across modules

## Examples

```
/app-as-module                        # Wrap current project as generic module
/app-as-module --for hr-system        # Wrap to plug into a specific HR core
/app-as-module verify                 # Check contract cleanliness
/app-as-module acl --for hr-system    # Just regenerate the ACL
```

---

**What's next?**
- `/integrate <core> <module>` — plug this module into a core
- `/module events map` — visualize events
- `/rbac` — align permissions with the core's RBAC model
