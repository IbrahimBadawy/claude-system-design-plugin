# /ux-kit - UX Quality Kit & Patterns

Enforce professional UX on every page. Empty states, loading, errors, feedback,
keyboard shortcuts, accessibility — nothing skipped.

## Usage
```
/ux-kit                            # Run the full UX checklist on the project
/ux-kit audit                      # Scan pages and report UX gaps
/ux-kit generate                   # Generate UX kit components (toast, dialog, etc.)
/ux-kit for <page>                 # UX spec for a specific page
/ux-kit patterns                   # Reference list of UX patterns
```

## Why This Command Exists

"It works" is not done. A page is done when:
- It has a clear empty state.
- It shows a loading skeleton (not a blank screen).
- It handles errors gracefully with a retry.
- It gives feedback on every action (toast / inline).
- It's keyboard-navigable.
- It's responsive at 320px, 768px, 1280px, 1920px.
- It passes axe-core at WCAG 2.2 AA.
- It respects `prefers-reduced-motion`.
- It has a focus state on every interactive element.
- It includes confirmation for destructive actions.

## The UX Completeness Checklist

Every page MUST implement these:

### 1. States (all 5)
| State | What |
|-------|------|
| **Empty** | No data yet — friendly illustration + primary CTA to create first item |
| **Loading** | Skeleton matching final layout, not a spinner |
| **Error** | Clear message + retry button + contact support link for 500s |
| **Partial** | Some data loaded, some failed — show what you have |
| **Success** | Toast / inline confirmation after every write action |

### 2. Feedback Patterns
| Pattern | When |
|---------|------|
| Toast (3-5s) | Quick confirmation (saved, deleted, sent) |
| Inline status | Form field validation (green check / red error) |
| Banner | System-wide messages (maintenance, subscription expired) |
| Modal | Confirmations for destructive actions |
| Optimistic update | Assume success, rollback on error |
| Progress indicator | Operations > 2 seconds |

### 3. Destructive Action Protocol
- Confirmation dialog with **clear explanation**
- Type-to-confirm for dangerous actions (type the item's name)
- Mention what will be deleted (5 linked orders, etc.)
- Undo available for 5 seconds (toast with "Undo" button)
- After confirm: loading → success toast → navigation/refresh

### 4. Form Best Practices
- Labels above inputs (not placeholders as labels)
- Required fields marked
- Inline validation (on blur, not on every keystroke)
- Error messages specific and actionable
- Auto-save drafts for long forms
- Tab order matches visual order
- Submit button shows loading state
- Keyboard: Enter submits, Esc cancels

### 5. Table / List Patterns
- Search (debounced 300ms)
- Sort (per column, visible indicator)
- Filter (multi-select, saveable as "views")
- Bulk actions (checkboxes + floating action bar)
- Pagination (with page size selector) OR infinite scroll
- Row click → detail (whole row, not just a button)
- Empty state ("No orders match your filters. Clear filters?")
- Loading skeleton (rows, not spinner)
- Column customization (show/hide, reorder)
- Export (CSV, Excel, PDF)
- Row density (compact / normal / comfortable)

### 6. Navigation
- Breadcrumbs on deep pages
- Back button works correctly
- Logo links home
- Active nav item highlighted
- Mobile: hamburger + drawer or bottom tabs
- Cmd-K / Ctrl-K global search
- Keyboard shortcuts visible with `?`

### 7. Visual Polish
- Dark mode toggle (respect `prefers-color-scheme`)
- Reduced motion support (`prefers-reduced-motion`)
- Focus rings visible (no `outline: none`)
- Hover states on all interactive elements
- Transition animations (150-300ms)
- Icon + text (not icons alone)
- Loading: skeleton that matches final layout
- Images: lazy-loaded with placeholder

### 8. Accessibility (WCAG 2.2 AA)
- Semantic HTML (header, nav, main, article)
- ARIA where needed (live regions, roles, labels)
- Color contrast ≥ 4.5:1 (3:1 for large text)
- Alt text on images
- Form labels linked to inputs
- Keyboard navigation throughout
- Skip-to-content link
- Works at 200% zoom
- Screen-reader tested

### 9. Performance UX
- Initial paint < 1.5s (LCP < 2.5s)
- Interactive < 3s (TBT < 200ms)
- Skeleton while data loads (no jarring layout shifts, CLS < 0.1)
- Images lazy-loaded
- Pagination for > 100 items
- Debounced inputs (search, filter)

### 10. Copywriting
- No lorem ipsum
- Action-oriented button labels ("Save changes", not "OK")
- Friendly error messages ("Something went wrong. Try again?" not "Error 500")
- Inclusive language
- Consistent terminology across app
- Honest: "This will delete X and Y" not "Are you sure?"

### 11. RTL / Internationalization
- `dir="rtl"` support for Arabic/Hebrew
- CSS logical properties (`margin-inline-start`, not `margin-left`)
- Icons flip appropriately (back arrow, chevrons)
- Dates/numbers formatted per locale
- i18n framework set up even if only one language now

### 12. Onboarding
- First-time user tour (dismissable)
- Empty state with clear CTA ("Create your first project")
- Sample/demo data optional
- Progress indicator for setup steps
- "Getting started" checklist on dashboard

## Generated UX Kit Components

When `/ux-kit generate` runs, it creates (stack-appropriate):

```
src/
  components/
    ui/
      Toast/              # Toast provider + useToast() hook
      ConfirmDialog/      # Destructive action confirmation
      EmptyState/         # Standardized empty state
      LoadingSkeleton/    # Skeleton matching different layouts
      ErrorBoundary/      # React error boundary with fallback
      ErrorState/         # Error page for failed fetches
      Breadcrumbs/
      CommandPalette/     # Cmd-K global search (kbar / cmdk)
      KeyboardShortcuts/  # Modal that lists all shortcuts
      ThemeToggle/        # Light / dark / system
      A11ySkipLink/       # Skip-to-content for screen readers
    patterns/
      DataTable/          # Search + sort + filter + pagination + bulk
      FormWithAutosave/
      BulkActionBar/
      FilterPanel/
      SearchWithSuggestions/
      FileUpload/         # Drag-drop + progress + multi-file
```

Plus hooks:
```
src/
  hooks/
    useToast.ts
    useConfirm.ts
    useHotkeys.ts
    useDebounce.ts
    useDarkMode.ts
    useOnlineStatus.ts
    useReducedMotion.ts
    useOptimisticUpdate.ts
```

## `/ux-kit audit` — Quality Gate

Scans pages and reports missing UX patterns:

```
## UX Audit: project-name

Pages scanned: 34
Pages with all 5 states: 22 ✓
Pages missing empty state: 8
  - /products
  - /orders
  - /customers
  - ...
Pages missing loading skeleton: 12
Pages missing error state: 6
Forms without inline validation: 4
Tables without search: 3
Destructive actions without confirmation: 2
Pages failing axe-core: 5
Missing dark mode: app-wide

Recommendation priority:
1. Add empty states (users will see these first)
2. Add error states (prevent user confusion)
3. Fix axe violations (accessibility)
4. Add loading skeletons (perceived performance)
```

## `/ux-kit for <page>` — Per-Page UX Spec

For a specific page, generate:

```markdown
# UX Spec: Products List

## States
- Empty: "No products yet. Add your first product to get started." + "Add Product" CTA
- Loading: Table skeleton with 10 rows, 5 columns
- Error: "Couldn't load products." + Retry button + help link
- Success: Table with data

## Interactions
- Row click: → /products/:id (detail)
- Add button: → /products/new
- Bulk actions: Select all / delete / export / archive
- Search: debounced 300ms, searches name + SKU + description
- Filter: category (multi), status (single), price range (slider)
- Sort: name (default), price, created_at, updated_at
- Export: CSV, Excel — exports current filter
- Keyboard: `/` focus search, `n` new product, `Esc` clear filters

## Responsive
- Desktop: Full table, sidebar filters
- Tablet: Table with horizontal scroll, collapsed filters
- Mobile: Card list, filter bottom sheet

## A11y
- Table has caption
- Sortable columns use aria-sort
- Bulk select uses aria-checked with indeterminate
- Keyboard: Tab navigates cells, Enter opens detail
```

## Complexity-Driven Scope

| Complexity | What's Enforced |
|-----------|-----------------|
| **Simple** | States (empty/loading/error), toast, confirmation for deletes, basic a11y |
| **Medium** | Above + full form validation, table patterns, dark mode, keyboard shortcuts, responsive, RTL if Arabic |
| **Complex** | Everything + audit trail of UX decisions, i18n framework, onboarding tour, command palette, bulk operations, optimistic updates, performance budgets enforced in CI |

## Examples

```
/ux-kit                         # Full UX checklist
/ux-kit generate                # Scaffold kit components
/ux-kit audit                   # Find UX gaps in existing code
/ux-kit for orders-list         # Per-page spec
/ux-kit patterns                # Reference guide
```

---

**What's next?**
- `/frontend design` - Visual style + component library selection
- `/pages` - Full page inventory
- `/rbac` - Permission-aware UI (buttons hide per permission)
- `/implement --ux-kit` - Scaffold all kit components
