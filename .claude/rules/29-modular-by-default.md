---
description: Every system is built as Core + Modules (Microkernel pattern). Modules declare one of 5 roles (Core / Module-of / Standalone / Bridge / Dependent). Manifest-first development. Symmetric install/uninstall. Graceful degradation. Fractal composability — same pattern at every scale.
globs: "*"
---

# Rule 29: Modular by Default (Microkernel)

> Canonical source: `.claude/knowledge/architecture-spec.md`
> Practical guide: `.claude/knowledge/modular-architecture.md`

## The Rule

Every system produced by this plugin is built as **Core + Modules** following
the Microkernel pattern. The same pattern applies recursively at every scale
(fractal composability).

### The Six Design Principles (spec §5)

1. **Loose coupling** — modules communicate only through stable, versioned, public contracts. No reaching into internals.
2. **Explicit contracts** — all integration points, dependencies, and permissions are declared in manifests, never implied by runtime discovery alone.
3. **Graceful degradation** — absence of an optional parent Core or sibling module MUST NOT break the system.
4. **Symmetric lifecycle** — install and uninstall are inverse operations; no orphaned data, schema, or registrations after uninstall.
5. **Fractal composability** — same Core–Module pattern at every scale, from plugin-in-app to federation-of-enterprise-systems.
6. **Manifest-first development** — the Manifest is designed BEFORE the code; code conforms to the contract, not the other way around.

---

## Core Requirements (spec §2)

Every Core MUST be:

- **Self-sufficient** — fully operable standalone with zero modules installed
- **Structural backbone** — owns identity, auth, routing, config, logging, event bus, tenant context
- **Extension contract** — exposes versioned extension points (routes, events, UI slots, schema extensions, APIs, permissions)
- **Lifecycle owner** — manages register / install / activate / deactivate / upgrade / remove

The Core MUST NOT:
- Know about specific modules
- Own business logic for specific domains
- Bypass its own extension contract

---

## Module Requirements (spec §3)

Every module MUST have:

- [ ] `module.manifest.json` valid against the canonical schema
- [ ] **Roles declared** — at least one of: `core`, `module-of`, `standalone`, `bridge`, `dependent`
- [ ] **Parent Core** declared if `module-of` (with version range)
- [ ] **`requiresSiblings`** declared if `dependent` (enforced by Core before activation)
- [ ] **`bridges`** declared if `bridge` (the Cores being mediated)
- [ ] **Compatibility** — min/max Core version + platform requirements
- [ ] **Integration points** — routes, events, UI slots, schema extensions, API endpoints, scheduled jobs, permissions
- [ ] **Permissions & scopes** — declared, both `provides` and `consumes`
- [ ] **Assets** — migrations, seeds, translations, config templates, static files
- [ ] **Lifecycle hooks** — install, uninstall, enable, disable, upgrade, healthCheck
- [ ] **Graceful degradation** declaration for every optional dependency
- [ ] **Own database tables** (no cross-module queries / JOINs)
- [ ] **Dual-boot capability** — runs standalone (local adapters) AND attached (injected ports)
- [ ] **Contract tests** (Pact) — events, APIs, UI slots match manifest
- [ ] **Symmetric install/uninstall** — tested over 10 cycles with zero residue
- [ ] **Version compatibility tests** — works across declared Core version range

---

## The 5 Roles (spec §4)

A system MAY declare multiple roles simultaneously.

| Role | Meaning |
|------|---------|
| **Core** | Hosts other systems/modules as its own modules |
| **Module-of** | Attaches to another Core as a module |
| **Standalone** | Runs fully independently with no parent Core |
| **Bridge** | Mediates between 2+ independent Cores (translation, sync, event brokering) |
| **Dependent** | Module-of that requires specific sibling modules on the same Core |

**Enforcement:** every module declares roles in the manifest. `/core-modules audit`
and `/integrate verify` check role consistency against actual behavior.

---

## What's Banned

- **Cross-module direct imports** — module A cannot `import { x } from '../moduleB/src/y'`
- **Cross-module database queries** — no JOINs across module-owned tables
- **Module knowledge in the core** — core never hard-codes references to specific modules
- **Runtime-only discovery** — everything integration-relevant is in the manifest
- **Non-symmetric uninstall** — leaving orphaned data, schema, or registrations
- **Breaking optional dependencies** — missing optional siblings must NOT crash the module
- **Hard-failing on missing Core** — `module-of` MUST also run standalone with fallback adapters
- **Code-first manifests** — manifest must be authored before implementation
- **Circular module dependencies** — if A requires B and B requires A, move shared bits to Core or shared kernel

---

## Communication Priority (spec §5)

Modules talk to the Core and to each other via (in order of preferred coupling):

1. **Events** (pub/sub, loose coupling) — preferred for notifications/reactions
2. **UI Slots** (declarative composition) — host exposes slots, modules register components
3. **Ports + Adapters** (Hexagonal) — required dependencies known at boot
4. **Published APIs** (versioned, synchronous) — cross-module data queries
5. **Shared Kernel** (tiny, stable) — `UserId`, `TenantId`, `ISODateTime`, `Money` only

Bridges always use an **Anticorruption Layer** when translating between Cores.

---

## Lifecycle Contract (spec §3.2)

```
Install    : migrations + seeds + registrations + wiring
Uninstall  : MUST be exact inverse of install (zero residue)
Enable     : activate for a tenant (idempotent)
Disable    : deactivate for a tenant (data preserved)
Upgrade    : version N → N+1 with rollback support
Health     : liveness + readiness reporting
```

**Symmetric uninstall test:** run `install → uninstall` 10 times. Database,
filesystem, and Core registries must be byte-indistinguishable from
pre-install state (modulo audit logs).

---

## Complexity-Driven Scope

| Complexity | Enforcement level |
|------------|-------------------|
| **Simple** | 2-3 folders under `features/`, lightweight manifest, in-process events, no bridges or dependents, no upgrade hooks |
| **Medium** | Full manifests, ports for cross-feature deps, admin UI per module, in-process or Redis events, basic lifecycle hooks, single-role declarations |
| **Complex** | Full plug-and-play contracts, contract tests, independent deploy, ACLs for external systems, Module Catalog, Bridges, Dependents, all 5 roles available, symmetric-uninstall test in CI |

---

## Enforcement

- **ESLint `import/no-restricted-paths`** blocks cross-module internal imports
- **JSON Schema validation** on `module.manifest.json` (CI gate)
- **`/core-modules audit`** scans for modularity violations
- **`/module events map`** visualizes the event graph; detects circular dependencies
- **Pre-commit hook** fails on direct imports across module boundaries
- **Contract tests in CI** — module's declared events/APIs/UI-slots must match implementation
- **Symmetric-uninstall test** — runs in CI for Complex projects
- **Version-compatibility test** — module boots against min and max declared Core versions
- **Graceful-degradation test** — module boots with each optional dependency removed; required features still work

---

## Escape Hatch

Direct coupling allowed ONLY in:

- Within a single module (its own internals)
- Shared kernel (tiny, stable, versioned)
- Core-owned code (explicitly shared across everything)
- Adapters that implement declared ports (the ONE place Core + modules touch)

Everywhere else: go through events, ports, APIs, UI slots, or the ACL.

---

## Why This Rule Exists

- Monolithic apps without module boundaries become unmaintainable after ~18 months
- Every successful long-lived app eventually gets extended by another app —
  either this app consumes another, or another consumes this. Module
  boundaries make that trivial
- "Extract to microservice later" almost never works without pre-existing
  module boundaries
- The real-world example: HR core + Attendance module, OR Attendance built
  standalone → later wrapped by HR. Both directions, and every combination in
  between (Bridges, Dependents), work only if every system was built
  modularly from day 1 following this exact spec
