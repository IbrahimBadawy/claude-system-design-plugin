---
description: Every system is built as Core + Modules. Modules are plug-and-play — same codebase can run standalone, be a core for other apps, or be a module inside another core. No hard-coded cross-module imports; communication via ports, events, and published APIs only.
globs: "*"
---

# Rule 29: Modular by Default

## The Rule

Every system produced by this plugin is built as **Core + Modules**:

- **Core** provides identity, auth, event bus, tenant context — nothing domain-specific
- **Modules** own business capabilities, with versioned contracts (ports + events + APIs)
- Modules communicate via **ports**, **events**, or **published APIs** — never by
  importing each other's internals
- Every module can, in principle, run **standalone OR as a module** (dual-boot)
- The same codebase can be **a core for other apps OR a module inside another core**

## Why This Rule Exists

- Monolithic apps without module boundaries become unmaintainable after ~18 months
- Every successful long-lived app eventually gets extended by another app — either this
  app consumes another, or another consumes this. Module boundaries make that trivial
- "Extract to microservice later" almost never works without pre-existing module boundaries
- The user's example: HR core / Attendance module / or the reverse. Both directions
  work only if both apps were built modularly from day 1

## What's Required

### Every module MUST have:

- [ ] `module.manifest.json` declaring requires / provides / events / permissions
- [ ] Its own database tables (no queries to other modules' tables)
- [ ] Versioned API (REST/GraphQL/tRPC) or event schema as its only external interface
- [ ] Ports defined for what it needs from outside (UserDirectoryPort, AuthPort, EventBusPort, etc.)
- [ ] Lifecycle hooks: `install`, `activate`, `deactivate`, `uninstall`, `healthCheck`
- [ ] Admin UI mounted at `/admin/<module-name>` (config + audit + health)
- [ ] Per-tenant enable/disable support
- [ ] Migrations + seeds scoped to this module
- [ ] Contract tests (Pact or equivalent)
- [ ] Can run standalone (with default adapters) AND as a module (with injected ports)

### The core MUST stay minimal:

- Identity (users, tenants, orgs)
- Auth + permissions
- Event bus
- Contract registry
- Configuration / feature flags
- Observability baseline

The core MUST NOT own domain business logic — that's what modules are for.

## What's Banned

- **Cross-module direct imports** — module A cannot `import { x } from '../moduleB/src/y'`
- **Cross-module database queries** — no JOINs across module-owned tables
- **Module knowledge in the core** — core never mentions specific modules
- **Hard-coded adapters** — modules must receive their dependencies (ports) via
  composition, not import them
- **Circular module dependencies** — if A needs B and B needs A, one of them must be
  in the core or in a shared kernel

## Communication Patterns (in priority order)

1. **Events** (loose coupling — prefer this for notifications, audits, reactions)
2. **Ports + adapters** (for required dependencies known at boot)
3. **Published APIs** (synchronous data queries across modules)
4. **Shared kernel** (tiny — only `UserId`, `TenantId`, `ISODateTime`, `Money`)

## Anticorruption Layer (ACL)

When two systems that weren't built together need to integrate, put an ACL between them:
- Translate terminology
- Filter irrelevant events
- Enrich missing data
- Map permissions

`/integrate` command generates ACLs automatically.

## Complexity-Driven Scope

- **Simple:** 2-3 folders under `features/`, no strict manifest, in-process events
- **Medium:** Manifests required, ports for cross-feature deps, admin UI per module, in-process or Redis events
- **Complex:** Full plug-and-play contracts, contract tests, independent deploy, ACLs for external systems, module catalog

## Enforcement

- ESLint `import/no-restricted-paths` blocks cross-module internal imports
- `/core-modules audit` scans for modularity violations
- `/module events map` visualizes the event graph (circular dependency detection)
- Pre-commit hook: fail on direct imports across module boundaries
- Contract tests in CI: fail if module's promised events/APIs don't match schema

## Escape Hatch

Direct coupling allowed ONLY in:
- Within a single module (its own internals)
- Shared kernel (tiny, stable, versioned)
- Core module (explicitly shared across everything)
- Adapters implementing ports (the ONE place where core + modules touch)
