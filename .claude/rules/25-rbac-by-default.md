---
description: Any app with user accounts gets a full Roles x Permissions x Resources model with audit logging and admin UI — not a simple is_admin boolean. Every sensitive action is permission-guarded and audited.
globs: "*"
---

# Rule 25: RBAC by Default

## The Rule

Every project with user accounts MUST implement a real permission system from day 1.
No `if (user.isAdmin)` checks scattered across the codebase. No "we'll add permissions
later". Wire it in from the start.

## Minimum Model

- `User` table
- `Role` table (tenant-scoped if SaaS)
- `Permission` table (system-level catalog)
- `user_role` junction (which users have which roles)
- `role_permission` junction (which roles have which permissions)
- `audit_log` table (who did what when)

For Complex projects, also add:
- `resource_acl` for row-level overrides
- Approval workflow fields (`requires_approval`, `approved_by`, `approved_at`)

## Permission Naming

All permissions follow `<resource>:<action>[:<scope>]`:

- `users:read:all` — read any user
- `orders:approve:team` — approve orders in user's team
- `settings:update:tenant` — change tenant settings
- `billing:read:own` — read own billing info

## Standard Roles (ship with these)

Every app ships with these default roles, customizable by tenant:

| Role | Default Permissions |
|------|--------------------|
| Super Admin | `*:*:all` (cross-tenant, platform owner) |
| Tenant Admin | `*:*:tenant` (all within tenant except billing global) |
| Manager | team + approval permissions |
| Editor | CRUD on own/team content |
| Member | CRUD on own, read shared |
| Viewer | read-only |
| Guest | limited external access |

## Every Endpoint Must Be Guarded

```typescript
// ✓ Good
@UseGuards(AuthGuard('jwt'), PermissionsGuard)
@Permissions('orders:approve:team')
@Post('orders/:id/approve')

// ✗ Bad
@Post('orders/:id/approve')   // no guard
async approve() { ... }

// ✗ Bad
@Post('orders/:id/approve')
async approve(@Req() req) {
  if (!req.user.isAdmin) throw new Error();  // ad-hoc check
}
```

## UI Must Be Permission-Aware

Buttons, menu items, whole pages hide based on permissions:

```tsx
<Can I="approve" a="orders" scope="team">
  <Button onClick={approve}>Approve</Button>
</Can>
```

No showing a button that 403s when clicked.

## Every Sensitive Action Is Audited

On create/update/delete/approve of any important entity:
- Write to `audit_log` table with: user_id, action, resource_type, resource_id,
  before_snapshot, after_snapshot, timestamp, IP, user-agent
- For destructive actions, capture a reason from the user
- Audit log has admin UI with filters + export

## Admin UI (mandatory for Medium+)

- Users list (search, filter, invite, suspend, impersonate)
- User detail (profile, roles, activity)
- Roles list + create/edit
- Role-permission matrix (checkbox grid)
- Audit log (searchable, exportable)

## Complexity-Driven Scope

| Complexity | What's Required |
|-----------|-----------------|
| **Simple** (with users) | 2-3 roles, basic `@Permissions` decorators, lightweight audit log |
| **Medium** | Standard 5 roles, permission middleware, audit log (7-30 day retention), admin UI for users + roles |
| **Complex** | Full model: roles + permissions + scopes + resource ACLs, custom roles per tenant, approval workflows, audit log with full compliance features |

## Skip Condition

RBAC is optional ONLY if:
- The app has NO user accounts (purely public), OR
- The app is a single-user tool (no multi-user scenario ever)

In all other cases, Rule 25 applies.

## How to Verify

Run `/rbac audit` — scans endpoints and verifies:
- Every route has a permission decorator
- No route is silently public
- Destructive actions log to audit
- Permission names follow convention
- Admin UI exists for roles + users

Run before declaring any milestone done.

## Why This Rule Exists

- Permission logic scattered across 100 endpoints is the #1 source of auth bugs.
- `if (user.isAdmin)` leaks to other parts of the app and is impossible to refactor.
- A permission system built on day 1 is cheap; retrofitted on day 90 is painful.
- Users expect role management — not having it makes the app look amateur.
- Audit log is a compliance requirement for most domains (SOC2, HIPAA, GDPR).
