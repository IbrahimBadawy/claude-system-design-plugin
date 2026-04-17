# /shared-components - Shared UI, Features, and Services Manager

Design, catalog, audit, and manage shared components across the 3 ownership
layers (Core-level / Domain-level / Module-level). Enforces the Factor-Before-Copy
principle from `architecture-spec.md §7`.

Based on `.claude/knowledge/shared-components.md`.

## The 3-Layer Ownership

| Layer | Owned by | Examples |
|-------|---------|----------|
| **Core-level** | Core itself | DataTable, Form, Toast, Auth, i18n (cross-cutting) |
| **Domain-level** | Foundation module | GradeTable, StudentPicker (a related family of modules) |
| **Module-level** | Single module | Module-internal helpers |

**Promotion rule:** start at the lowest layer; when a second consumer
appears, promote upward via a **deliberate versioned refactor** — never
copy-paste.

## Usage

```
/shared-components                               # Interactive setup
/shared-components catalog                       # Browse all shared (by layer)
/shared-components audit                         # Find violations (copies, forks, undeclared use)
/shared-components create <name> --level core    # Scaffold a new shared component
/shared-components promote <name> --from <a> --to <b>   # Migrate component upward
/shared-components demote <name> --to module-level      # Reverse direction
/shared-components consumers <name>              # Who uses this?
/shared-components extension-points <name>       # What slots/render-props does it expose?
/shared-components fix-suggest                   # Auto-fix suggestions for violations
```

## What `/shared-components` Generates

### Initial catalog (`design/SHARED-CATALOG.md`)

```markdown
# Shared Components & Features Catalog: <project>

## Core-Level Shared

### UI Components (14)
- DataTable v2.3.0 — 8 consumers
- Form v2.1.0 — 12 consumers
- Toast v1.5.0 — all modules
- ConfirmDialog v1.2.0 — all modules
... 10 more

### Behavioral Features (9)
- Search (unified query contract) v1.0.0
- Filter primitives v1.0.0
- Pagination v1.0.0
- Bulk actions v1.0.0
- Import (CSV/Excel) v1.1.0
- Export (CSV/Excel/PDF) v1.1.0
- Attachments v1.0.0
- Tagging v1.0.0
- Commenting v1.0.0

### Cross-Cutting Services (10)
- Authentication v2.0.0
- Authorization (5-dim RBAC) v2.0.0
- Logging (structured) v1.0.0
- Auditing v1.0.0
- Notifications v1.0.0
- i18n v1.0.0
- Theming (tokens) v1.0.0
- Caching v1.0.0
- Background jobs v1.0.0
- Event bus v1.0.0

## Domain-Level Shared

### sis-foundation (for all academic modules)
- GradeTable v1.2.0 — used by: grades, transcripts, reports
- StudentPicker v1.0.0 — used by: enrollment, attendance, grades
- AcademicYearContext v1.0.0 — used by: 9 modules
- CourseAutocomplete v1.0.0 — used by: 4 modules

## Module-Level (internal, per-module)

### attendance module (3 internal)
- ShiftCalendar v0.1.0
- BiometricStatus v0.1.0
- AnomalyBadge v0.1.0

### grades module (2 internal)
- FinalizeWizard v0.1.0
- GradeDistributionChart v0.1.0
```

### Catalog as code

`packages/ui/catalog.ts` — machine-readable registry of all shared
components, their layer, version, consumers, contract.

### ESLint plugin

Configures `import/no-restricted-paths` to enforce:
- Modules can import from `@/ui/*` (core) and `@/domains/<their-domain>/*`
- Modules CANNOT import from other domain packages
- Modules CANNOT import from other modules' `internal/`
- Modules CANNOT import from shared components' `.internal.ts`

### Storybook organization

Stories organized by layer:
```
Storybook sidebar:
├── Core/
│   ├── UI/
│   │   ├── DataTable
│   │   ├── Form
│   │   └── ...
│   ├── Behavioral/
│   │   ├── Search
│   │   ├── Import
│   │   └── ...
│   └── Services/
├── Domain: sis-foundation/
│   ├── GradeTable
│   └── ...
└── Module: attendance/
    └── internal/...
```

### Manifest validator

CI check: every `import` of a shared component in a module MUST be declared
in the module's `module.manifest.json` under
`dependencies.sharedComponents`.

---

## `/shared-components audit`

Scans for violations of §7.5 anti-patterns:

```
## Shared Components Audit

Violations — 9

### Copy-paste of shared code (§7.5 #1) — 3
1. modules/grades/components/GradesTable.tsx
   → 87% similar to packages/ui/DataTable.tsx
   → Use DataTable directly; add render-prop for grade-specific rows.

2. modules/attendance/components/AttendanceSearch.tsx
   → 92% similar to packages/ui/SearchBar.tsx
   → Use SearchBar; pass custom suggest() hook.

3. modules/reports/components/ReportExport.tsx
   → 78% similar to @core/features/export
   → Use core export; register 'report' formatter.

### Forking a shared component (§7.5 #2) — 1
4. modules/billing/ui/DataTableForBilling.tsx
   → Appears to be a copy of DataTable with extra "reconciliation" column.
   → Add via slot pattern: <DataTable slots={{ extraColumn: ... }} />
   → Or propose promotion: add "reconciliation" as a first-class prop via `/shared-components promote`.

### Module-specific logic in shared (§7.5 #3) — 2
5. packages/ui/DataTable.tsx line 142
   → `if (config.module === 'grades') { renderGradeLegend(); }`
   → Violation: module name inside shared code.
   → Extract via render prop `renderLegend?: () => ReactNode`.

6. packages/core/features/search/index.ts line 88
   → Special case for "transcript" query type.
   → Move to transcripts module; register via shared search's extension point.

### Undeclared consumption (§7.5 #4) — 3
7. Module "reports" imports `DataTable`, `Form`, `Export` but manifest only lists Form.
8. Module "attendance" imports `StudentPicker` (domain-level) — not in manifest.
9. Module "billing" uses `notifications` service — missing from `dependencies.crossCuttingServices`.

### Extension points missing
- DataTable has 3 hard-coded behaviors that consumers have asked to customize.
  Recommended: add `slots.toolbar`, `slots.emptyState`, `slots.rowActions`.

Violations by module:
- grades:       2
- attendance:   2
- reports:      2
- billing:      2
- billing:      1 (forking)

Run `/shared-components fix-suggest` for auto-fixable items.
```

### Auto-fix

`/shared-components fix-suggest` auto-applies safe fixes:
- Replace copy-paste of 80%+ similarity with import from shared
- Add missing manifest declarations
- Extract `if (module === X)` patterns into render props
- Refactor forks to use slot/extension-point patterns

---

## Creating a New Shared Component

```bash
/shared-components create DataTable --level core
```

Generates:

```
packages/ui/DataTable/
├── DataTable.tsx              # Public API
├── DataTable.stories.tsx      # Storybook variants
├── DataTable.test.tsx         # Unit + integration
├── DataTable.a11y.test.tsx    # Axe tests
├── DataTable.visual.test.tsx  # Playwright visual
├── .contract.v1.json          # Pinned public API (semver-tracked)
├── internal/                  # Private (restricted imports)
├── index.ts                   # Public exports only
└── README.md                  # Usage + extension points
```

With extension points scaffolded:

```tsx
export interface DataTableProps<T> {
  data: T[];
  columns: ColumnDef<T>[];

  // Stable configuration
  pageSize?: number;
  density?: 'compact' | 'normal' | 'spacious';
  selectable?: boolean;
  sortable?: boolean;
  filterable?: boolean;

  // Extension points (preferred over props-explosion)
  slots?: {
    toolbar?:    ReactNode;
    emptyState?: ReactNode;
    loadingState?: ReactNode;
    errorState?: ReactNode;
    rowActions?: (row: T) => ReactNode;
    footer?:     ReactNode;
  };

  // Render props for deeply custom rendering
  renderRow?:  (row: T, index: number) => ReactNode;
  renderCell?: (value: unknown, col: ColumnDef<T>, row: T) => ReactNode;

  // Hooks — consumers can tap in
  onRowClick?:    (row: T) => void;
  onBulkAction?:  (action: string, rows: T[]) => Promise<void>;
  onExport?:      (format: 'csv' | 'xlsx' | 'pdf') => Promise<Blob>;
}
```

---

## Promotion Workflow

When Module A has `StudentPicker` and Module B needs similar:

```bash
/shared-components promote StudentPicker --from module-a --to domain/sis-foundation
```

Which does:

1. **Analyze** current implementation in Module A
2. **Detect differences** between what A needs and what B needs
3. **Propose parameterization** to cover both
4. **Move** the component to `packages/sis-foundation/StudentPicker/`
5. **Bump version** to 1.0.0 (new public API commitment)
6. **Write migration PR** for Module A to consume from new location
7. **Add Module B as declared consumer** in both modules' manifests
8. **Run contract tests** across all consumers to ensure no break
9. **Update shared catalog**

```
## Promotion: StudentPicker
  From: modules/module-a/internal/StudentPicker.tsx
  To:   packages/sis-foundation/StudentPicker/

### Changes
- Moved 4 files
- Parameterized: `coursesFilter?: (c: Course) => boolean`
  (was hard-coded in Module A; Module B needs different filter)
- Pinned public API as .contract.v1.json

### Migration PRs
- modules/module-a: import updated; no behavior change
- modules/module-b: import from new location

### Consumer manifests updated
- module-a/manifest.json: sharedComponents.StudentPicker added
- module-b/manifest.json: sharedComponents.StudentPicker added

### Tests
- 14 tests ported, 3 added for Module B scenarios, all passing
```

---

## Catalog Browser (`/shared-components catalog`)

Interactive catalog with filter:

```
╔══════════════════════════════════════════════════════════════╗
║ Shared Components Catalog                                   ║
╠══════════════════════════════════════════════════════════════╣
║ Layer:  [core] [domain] [module]    Type:  [ui] [behavior] [service] ║
║ Search: __________________________                          ║
╠══════════════════════════════════════════════════════════════╣
║ ★ DataTable        Core • UI       v2.3.0   8 consumers    ║
║ ★ Form             Core • UI       v2.1.0  12 consumers    ║
║ ★ Toast            Core • UI       v1.5.0  all modules     ║
║ ★ Search           Core • Behavior v1.0.0   9 consumers    ║
║ ★ Audit-Trail      Core • Service  v1.0.0  all modules     ║
║ ─ GradeTable       Domain • UI     v1.2.0   3 consumers    ║
║ ─ StudentPicker    Domain • UI     v1.0.0   4 consumers    ║
║ ○ ShiftCalendar    Module (attendance only) v0.1.0          ║
║                                                              ║
║ Legend:  ★ core  ─ domain  ○ module                         ║
╚══════════════════════════════════════════════════════════════╝

Click a component → props, slots, extension points, version history, consumers.
```

---

## Integration with Other Commands

- `/core-modules` — module manifests declare `sharedComponents` + `sharedFeatures` + `crossCuttingServices`
- `/design-system` — provides design tokens that all shared components use
- `/frontend design` — references catalog when planning component needs
- `/ux-kit` — the UX patterns (5 states, keyboard, etc.) are implemented by these shared components
- `/module create` — scaffolds manifest with declared shared dependencies
- `/implement` — refuses to import a shared component not declared in manifest

---

## Checklist — enforced by CI

- [ ] Every shared component has `.contract.vN.json` (pinned public API)
- [ ] Every shared component has Storybook stories
- [ ] Every shared component has a11y tests (axe clean)
- [ ] Every shared component is themeable (uses design tokens)
- [ ] Every shared component is localized (strings via `t()`)
- [ ] Every shared component has extension points (slots or render props)
- [ ] No `if (module === X)` patterns inside shared code
- [ ] No forks (similarity check >= 80% with a shared component triggers fail)
- [ ] Every module declares `dependencies.sharedComponents` it consumes

---

## Examples

```
/shared-components catalog
/shared-components create DataTable --level core
/shared-components create StudentPicker --level domain --domain sis-foundation
/shared-components promote StudentPicker --from module-a --to domain/sis-foundation
/shared-components audit
/shared-components fix-suggest
/shared-components consumers DataTable
/shared-components extension-points DataTable
```

---

**What's next?**
- `/design-system` — define tokens all shared components consume
- `/ux-kit` — UX patterns the shared components implement
- `/frontend design` — pick which shared components to build first
- `/module create <name>` — generate manifest with proper sharedComponents declarations
