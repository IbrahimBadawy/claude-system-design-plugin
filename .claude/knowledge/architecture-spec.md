# Modular System Architecture Specification (Canonical)

> The authoritative specification for this plugin's modular architecture.
> All modular features (`/core-modules`, `/app-as-module`, `/integrate`,
> `/module`, Rule 29) MUST conform to this spec. Treat this file as
> source-of-truth; everything else elaborates on it.

---

## 1. Architectural Philosophy

The system follows a **Core–Module (Microkernel) architecture** designed for
composability, extensibility, and reusability across multiple scales. The
pattern operates on two levels:

1. **Intra-system modularity** — a single application composed of a Core and
   pluggable Modules.
2. **Inter-system composability** — a complete system (Core + Modules) can
   itself act as a Core for larger systems, or attach as a Module to another
   Core. The same pattern applies recursively at every scale.

---

## 2. The Core

The Core is the foundational layer of any system and **MUST** satisfy:

| Requirement | Meaning |
|-------------|---------|
| **Self-sufficient** | The Core MUST be fully operable as a standalone application with meaningful functionality of its own, **with zero modules installed**. |
| **Structural backbone** | Provides the fundamental data model, authentication, authorization, routing, configuration, logging, event bus, and all other cross-cutting infrastructure. |
| **Extension contract** | Exposes clearly defined, **versioned** extension points (hooks, APIs, events, UI slots, schema extension mechanisms) through which modules integrate. |
| **Lifecycle owner** | Manages registration, installation, activation, deactivation, upgrade, and removal of modules. |

---

## 3. Modules

A **Module** is a discrete, self-contained unit of functionality that plugs
into a Core to extend or augment its capabilities.

### 3.1 Module Manifest

Every module MUST ship with a declarative Manifest describing:

| Field | Purpose |
|-------|---------|
| **Identity** | unique ID, human-readable name, version, author, license |
| **Compatibility** | minimum/maximum Core version, platform requirements |
| **Dependencies** | required sibling modules and their minimum versions |
| **Integration points** | which Core extension points the module hooks into: routes, events, UI slots, schema additions, API endpoints, scheduled jobs, permissions |
| **Permissions & scopes** | what Core resources and APIs the module needs access to |
| **Assets** | migration scripts, static assets, translations, configuration templates, seed data |
| **Lifecycle hooks** | handlers for install, uninstall, enable, disable, upgrade |

### 3.2 Module Lifecycle

Modules MUST support the following operations:

| Operation | Required Behavior |
|-----------|-------------------|
| **Install** | Register with the Core, apply schema migrations, seed initial data, wire integration points. |
| **Uninstall** | Cleanly remove all module-owned data, schema changes, registrations, and artifacts. The Core's state after uninstall MUST be indistinguishable from the pre-install state. |
| **Enable / Disable** | Temporarily activate or deactivate without data loss. |
| **Upgrade** | Migrate from one version to another in a backward-compatible manner, with rollback support. |

**Symmetric Lifecycle Guarantee:** Install and Uninstall MUST be symmetric
inverse operations.

---

## 4. Hierarchical Composition (Systems-of-Systems)

Once a system matures into Core + Modules, it can play any of the following
roles in a larger ecosystem. A single system MAY fulfill multiple roles
simultaneously.

| Role | Description |
|------|-------------|
| **Core** | The system hosts other systems/modules as its own modules. |
| **Module-of** | The system attaches to another Core as a module. |
| **Standalone** | The system runs fully independently with no parent Core. |
| **Bridge / Middleware** | The system mediates between two or more independent Cores, translating data models, synchronizing state, or brokering events. |
| **Dependent** | The system attaches to a Core but additionally requires specific sibling systems/modules to be present on the same Core. Prerequisites MUST be declared in its Manifest, and the Core MUST enforce them before activation. |

### Canonical Example — HR ↔ Attendance

- An **HR system** is built as Core + its internal modules (employees, contracts,
  payroll, etc.). It is a complete standalone product.
- An **Attendance system** is separately built as Core + its modules (biometric
  device integration, shift scheduling, leave requests). It is ALSO a complete
  standalone product.
- When deployed together, the Attendance system attaches to the HR Core as a
  **Module-of**, deferring employee master data and organizational structure to
  HR while retaining its own Core responsibilities.
- A third system (e.g., **Payroll Calculator**) might act as a **Bridge**
  between HR and Attendance, pulling time data from one and employee data from
  the other.
- A fourth system (e.g., **Overtime Approval**) might be a **Dependent** module
  requiring both HR and Attendance to be installed on the same Core.

---

## 5. Design Principles

| Principle | What It Means |
|-----------|---------------|
| **Loose coupling** | Modules communicate with the Core and with each other only through stable, versioned, public contracts. No reaching into internals. |
| **Explicit contracts** | All integration points, dependencies, and permissions are declared in manifests, never implied by runtime discovery alone. |
| **Graceful degradation** | The absence of an optional parent Core or sibling module MUST NOT break the system. It should fall back to standalone behavior or disable only the features that depend on the missing piece. |
| **Symmetric lifecycle** | Install and uninstall are inverse operations; no orphaned data, schema, or registrations after uninstall. |
| **Fractal composability** | The same Core–Module pattern applies at every scale, from a plugin inside one app to a federation of enterprise systems. |
| **Manifest-first development** | The Manifest is designed before the code; code conforms to the contract, not the other way around. |

---

## 6. Plugin Development Guidelines

When developing any plugin (module) under this architecture:

1. **Target a specific Core** and declare the required Core version range.
2. **Author the Manifest before writing implementation code.**
3. **Implement all lifecycle hooks cleanly** (install, uninstall, enable, disable, upgrade).
4. **Depend only on public contracts** of the Core and sibling modules — never on their internal implementation.
5. **Ensure the plugin can be fully removed without residue** in database, filesystem, or Core registries.
6. **If the plugin is itself a system with its own sub-modules**, expose its own Core interface following these same rules recursively.
7. **Declare every integration relationship** (Core / Module-of / Standalone / Bridge / Dependent) explicitly in the Manifest.

---

## Compliance Matrix

How each plugin artifact implements this spec:

| Spec Section | Enforced By |
|--------------|-------------|
| 2. Core requirements | `/core-modules generate` — Core template satisfies self-sufficient + backbone + contract + lifecycle-owner |
| 3.1 Manifest fields | `module.manifest.json` schema (JSON Schema validated in CI) |
| 3.2 Lifecycle operations | `ModuleLifecycle` TS interface (install, uninstall, enable, disable, upgrade) |
| 4. Roles (Core / Module-of / Standalone / Bridge / Dependent) | `manifest.roles[]` + `/integrate` command wires accordingly |
| 5. Design principles | Rule 29 (Modular by Default) |
| 6. Development guidelines | `/module create`, `/app-as-module`, `/core-modules audit` |

---

## See Also

- `modular-architecture.md` — practical guide implementing this spec
- `functional-taxonomy.md` — 5-category functional classification that
  lives inside each module
- Rule 29 — enforcement of this spec at review time
- Commands: `/core-modules`, `/app-as-module`, `/integrate`, `/module create`
