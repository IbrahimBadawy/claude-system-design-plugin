---
description: Factor-before-copy. Any capability appearing in 2+ places MUST be promoted to Core-level, Domain-level, or Module-level shared with a stable versioned contract. No copy-paste, no forks, no module-specific logic inside shared code.
globs: "*"
---

# Rule 31: Shared First (Factor-Before-Copy)

> Canonical source: `.claude/knowledge/architecture-spec.md §7`
> Practical guide: `.claude/knowledge/shared-components.md`
> Command: `/shared-components`

## The Rule

Any capability (UI component, behavioral feature, or cross-cutting service)
that appears — or is likely to appear — in more than one place MUST be
factored into a **Shared Component / Feature** and consumed by modules
through a stable contract, never re-implemented per module.

## The 3 Ownership Layers

A capability begins at the **lowest layer that needs it** and is promoted
upward when a second consumer appears. Promotion is a **deliberate,
versioned refactor** — never an ad-hoc copy.

| Layer | Owned by | Home for |
|-------|---------|---------|
| **Core-level** | Core itself | Truly cross-cutting: DataTable, Form, Toast, auth, i18n, theming |
| **Domain-level** | Foundation module | Shared among a domain family: GradeTable for all academic modules |
| **Module-level** | Single module | Internal helpers of that module only |

## Required Shared Catalog

The Core SHOULD provide:

**UI components** — DataTable, SearchBar, FilterPanel, Form, FormField,
Modal, ConfirmDialog, Toast, Breadcrumbs, EmptyState, LoadingSkeleton,
ErrorState, ActionMenu, Date/Time pickers, FileUploader, RichTextEditor.

**Behavioral features** — search (unified query contract), filtering
(composable primitives), sorting, pagination, bulk actions, import/export
(CSV/Excel/PDF), printing, attachments, tagging, commenting, audit trails,
soft delete, archiving, undo/redo.

**Cross-cutting services** — authentication, authorization (5-dim RBAC),
logging, auditing, notifications, i18n, theming, caching, background jobs,
scheduling, file storage, event bus.

## Contract Requirements (§7.4) — every shared element

- [ ] **Stable, versioned public API** (props, events, hooks, slots) pinned in `.contract.vN.json`
- [ ] **Configurable** by consumers (slots, render props, hooks) — no modification of shared code
- [ ] **Themeable** via design tokens (no hex in shared code)
- [ ] **Localizable** via Core's i18n (no hardcoded strings)
- [ ] **Testable in isolation** (Storybook + unit + a11y + visual)
- [ ] **Consistent design language** across all shared elements

## Manifest Declaration

Every module's `module.manifest.json` declares shared-component consumption:

```jsonc
"dependencies": {
  "sharedComponents": {
    "DataTable": { "level": "core",   "versionRange": "^2.0.0" },
    "GradeTable": { "level": "domain", "versionRange": "^1.0.0" }
  },
  "sharedFeatures": {
    "search": { "level": "core", "versionRange": "^1.0.0" },
    "audit-trail": { "level": "core", "versionRange": "^1.0.0" }
  },
  "crossCuttingServices": {
    "authentication": { "level": "core", "versionRange": "^2.0.0" }
  }
}
```

Undeclared imports → CI fails.

## What's Banned (§7.5)

- **Copy-paste** — "just copy DataTable.tsx into my module and tweak it"
- **Fork** — "fork DataTable to v2-for-grades because it needed extra props"
- **Module-specific logic inside shared** — no `if (module === 'grades')` in shared code
- **Undeclared consumption** — every import of shared code must be listed in manifest
- **Ad-hoc promotion** — moving a component to shared without versioning + migration PRs
- **Props explosion** — if you need 15 props, use slots/render-props instead

## Promotion Workflow

Second consumer appearing triggers promotion:

```
Module A has StudentPicker → Module B needs similar
    ↓
/shared-components promote StudentPicker --from module-a --to domain/sis-foundation
    ↓
Analyze differences → parameterize → move to higher layer → bump to v1.0.0
    ↓
Migration PRs for module A (now imports from new location)
    ↓
Module B added as declared consumer
    ↓
Contract tests run across all consumers
```

**Demotion** (rare): if a "shared" component turns out to have only one
consumer, demote back to module-level. Reduces surface area.

## Enforcement

- `/shared-components audit` scans for:
  - Copy-paste (80%+ similarity to a shared component)
  - Forks (new component that looks like a shared one with extra props)
  - Module-specific `if/switch` patterns inside shared code
  - Undeclared consumption (import without manifest entry)
  - Missing extension points (shared component hard-coded where consumers ask to customize)
- ESLint `import/no-restricted-paths` blocks imports of `internal/` from outside
- CI schema-validates that every declared shared component has
  `.contract.vN.json`, Storybook story, a11y test
- Pre-commit hook warns on new module files that look like shared-component duplicates

## Complexity-Driven Scope

| Complexity | Enforcement |
|-----------|-------------|
| **Simple** | Core-level shared minimum (DataTable, Form, Toast, auth). Module-level OK for specifics. |
| **Medium** | Above + Domain-level for module families + manifest declaration required + audit in CI |
| **Complex** | Full 3-layer + promotion workflow + `.contract.vN.json` versioning + visual regression on every shared component + fork detection in CI |

## Why This Rule Exists

- Duplication is the #1 source of inconsistency, bugs, and maintenance cost
- By year 2, untracked duplication across modules makes refactoring impossible
- Users feel it: "the student picker in Enrollment looks/behaves different from the one in Grades" is a death sentence for product quality
- Promotion as a deliberate refactor (vs ad-hoc copy) gives everyone clear
  migration paths and versioning guarantees
- Admin-facing consistency builds trust; inconsistency erodes it

## Escape Hatch

Forks/copies allowed ONLY in:
- **Experimental prototypes** in a dev branch (with deletion deadline)
- **One-time migration shims** (tagged `// MIGRATION-SHIM: delete after vX`)
- **Breaking changes with parallel support** (old + new coexist for deprecation window)
- **External boundary adapters** (wrapping a 3rd-party library — that's the extension point)

Everything else: use the shared component, or propose promotion.
