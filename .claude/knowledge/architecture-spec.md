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

## 7. Shared Components & Shared Features

As systems grow, duplication becomes the single largest source of
inconsistency, bugs, and maintenance cost. To prevent this, the architecture
MUST treat reusable UI and behavior as first-class citizens, owned at the
appropriate layer.

### 7.1 Principle

Any capability that appears — or is likely to appear — in more than one place
MUST be factored into a **Shared Component** or **Shared Feature** and consumed
by modules through a stable contract, rather than re-implemented per module.

### 7.2 Ownership Layers

Shared elements are organized into three ownership layers, from most general
to most specific:

1. **Core-level shared** — provided by the Core itself, available to every
   module. This is the default home for anything that is truly cross-cutting.
2. **Domain-level shared** — provided by a mid-layer library or a designated
   "foundation" module, shared among a related group of modules (e.g., all
   academic modules in an SIS).
3. **Module-level shared** — internal to a single module, shared among its
   own sub-features.

A capability begins at the lowest layer that needs it and is promoted upward
once a second consumer appears. **Promotion is a deliberate, versioned
refactor — never an ad-hoc copy.**

### 7.3 Categories of Shared Elements

At minimum, the Core SHOULD provide shared implementations for:

**UI components** — data tables (with sorting, pagination, column management,
bulk selection), search bars, filter panels, forms and form fields, modals,
confirmation dialogs, toasts/notifications, breadcrumbs, empty states,
loading states, action menus, date/time pickers, file uploaders, and
rich-text editors.

**Behavioral features** — search (with a unified query contract), filtering
(with composable filter primitives), sorting, pagination, bulk actions,
import/export (CSV, Excel, PDF), printing, attachments, tagging, commenting,
audit trails, soft delete, archiving, and undo/redo.

**Cross-cutting services** — authentication, authorization, logging,
auditing, notifications, localization and translations, theming, caching,
background jobs, scheduling, file storage, and the event bus.

### 7.4 Contract Requirements

Every shared component or feature MUST:

- Expose a **stable, versioned public API** — props, events, hooks, or slots
  — documented in one place.
- Be **configurable** by the consumer without requiring modification of the
  shared code.
- Be **themeable and localizable** through the Core's theming and i18n systems.
- Be **testable and tested in isolation**, independently of any specific
  module.
- Follow a **single, consistent design language** so that a user moving
  between modules experiences a unified product.

### 7.5 Anti-Patterns to Avoid

- Copy-pasting a table or form "just for this module."
- Forking a shared component instead of extending it through its public
  contract.
- Embedding module-specific logic inside a shared component.
- Skipping the manifest declaration of which shared components a module
  consumes (module manifests SHOULD list their shared-component
  dependencies).

---

## 8. Permissions & Access Control

Permissions are a first-class concern of the architecture. Any system that
handles organizational, academic, financial, or personnel data MUST
implement the model described below. **Permissions are never an afterthought
bolted onto finished modules — they are designed up front, during discovery,
before implementation begins.**

### 8.1 Design Goals

The permission system MUST be:

- **Highly granular** — capable of expressing very specific access rules.
- **Easy to administer** — administrators should manage permissions through
  profiles and scopes, not by clicking through thousands of checkboxes.
- **Scope-aware** — permissions are always evaluated within an
  organizational, temporal, and functional context.
- **Auditable** — every grant, revocation, and access decision is logged
  and reportable.

### 8.2 The Five Dimensions of a Permission

Every permission in the system is defined by the intersection of five
dimensions. A user is authorized to perform an action **only if all five
dimensions align**.

#### 1. Organizational scope (hierarchical)

A nested hierarchy representing the institutional structure. For an SIS this
is typically:

> University → College → System → Program / Department → Academic Group (Year / Cohort)

Each system instantiates its own hierarchy; the Core provides the mechanism,
the domain provides the shape. A permission may be granted at any level of
the hierarchy and implicitly covers all descendants, or explicitly
restricted to a specific node.

#### 2. Academic year (temporal — annual)

Permissions may apply to a specific academic year (e.g., 2025/2026), a range
of years, or all years. Historical years are typically read-only for most
roles.

#### 3. Semester (temporal — sub-annual)

Within an academic year, permissions may be scoped to a specific term
(First, Second, Summer, …), a range of terms, or all terms.

#### 4. Functional scope (application hierarchy)

The hierarchy of features the permission applies to:

> Main Application → Sub-Application → Feature

A permission may target a whole main application, a single sub-application,
or an individual feature within a sub-application.

#### 5. Action type

The operation permitted on the targeted scope:

> View · Insert · Edit · Close · Open · Delete

Additional action types (Approve, Export, Print, Submit, …) MAY be defined
per module and MUST be declared in that module's manifest.

### 8.3 Profiles

A **Profile** is a named, reusable bundle of permissions across the five
dimensions. Profiles are the primary administrative abstraction —
administrators assign profiles, not individual permissions.

- A profile defines a default set of grants across organizational scopes,
  academic years, semesters, functional scopes, and actions.
- Each dimension in a profile can be set to a specific value, a range, or
  "all."
- A profile can be assigned to a single user, a group of users, or applied
  universally to everyone holding that profile regardless of scope.
- A user MAY hold multiple profiles; the effective permission set is the
  **union of all assigned profiles**, minus any explicit denials.
- Profiles SHOULD be versioned so that changes can be rolled forward or
  back.

### 8.4 Assignment Modes

Permissions can be granted in three modes, in increasing order of
specificity:

1. **Profile-based assignment** — the user receives a profile; permissions
   derive from it.
2. **Group-based override** — an additional grant or denial applies to all
   members of a group.
3. **User-specific override** — an individual grant or denial applies to one
   user, overriding profile and group.

**Denials always win over grants.** The effective permission for any
request is computed deterministically from these three layers.

### 8.5 Required Administrative Capabilities

The permission subsystem MUST include:

- **User management** — create, edit, deactivate, reactivate, and delete
  users; reset passwords; force password change; lock/unlock accounts;
  manage sessions and devices; enforce password policies; support MFA.
- **Profile management** — create, clone, edit, version, archive, and
  delete profiles.
- **Assignment management** — assign and revoke profiles, group
  memberships, and individual overrides, with scope filters.
- **Visitor / guest management** — when the system accepts unauthenticated
  or lightly-authenticated visitors, provide a dedicated interface to
  monitor, rate-limit, and audit visitor activity.
- **Specialized permission reports** — a reporting module dedicated to
  permissions, answering questions such as:
  - *"Who can edit grades in College X for semester Y?"*
  - *"What can user Z do, and where does each permission come from?"*
  - *"Which profiles grant delete access to financial records?"*
  - *"Show all permission changes in the last 30 days."*
- **Full audit trail** — every permission change and every access decision
  is logged with actor, target, scope, timestamp, and reason.

### 8.6 Discovery-Phase Requirement (IMPORTANT)

Whenever a new system, module, or plugin is being designed, and that system
involves any non-trivial permissions, the assistant MUST — during the
discovery phase, before implementation begins — explicitly ask the user
about the permissions model. Specifically, the assistant should clarify:

1. **What organizational hierarchy applies** to this system, and how deep
   does it go?
2. Are permissions **time-scoped** (academic years, fiscal years, semesters,
   campaigns, …)?
3. What is the **functional hierarchy** (main apps → sub-apps → features) to
   be secured?
4. What **action types** are needed beyond the standard View / Insert / Edit
   / Close / Open / Delete?
5. What **initial profiles** should ship with the system (e.g., Super Admin,
   Dean, Department Head, Faculty, Student Affairs Officer, Student, Guest)?
6. What are the **default scopes and actions** for each profile?
7. Are there **group-based or individual overrides** expected?
8. Are there **guests or visitors**, and how should they be monitored?
9. What **permission reports** are required on day one?
10. What are the **audit and compliance requirements**?

**Only after these questions are answered — and the answers are recorded —
should implementation proceed.** A system with complex permissions that is
built before its permission model is agreed upon will inevitably require a
costly rewrite.

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
| 7. Shared components & features | `/shared-components` command + Rule 31 (Shared First). Practical guide: `shared-components.md` |
| 8. Permissions & access control | `/rbac` command (overhauled for 5 dimensions + profiles + reports) + Rule 25 (upgraded). `/discover permissions` asks the mandatory §8.6 questions. Practical guide: `permissions-model.md` |

---

## See Also

- `modular-architecture.md` — practical guide implementing §1-6 of this spec
- `shared-components.md` — practical guide implementing §7 of this spec
- `permissions-model.md` — practical guide implementing §8 of this spec
- `functional-taxonomy.md` — 5-category functional classification that
  lives inside each module
- Rule 25 (RBAC by Default) — enforcement of §8
- Rule 29 (Modular by Default) — enforcement of §1-6
- Rule 31 (Shared First) — enforcement of §7
- Commands: `/core-modules`, `/app-as-module`, `/integrate`, `/module create`,
  `/rbac`, `/shared-components`, `/discover permissions`
