---
description: Every page in a user-facing app must have all 5 states (empty, loading, error, partial, success), feedback on every action, keyboard navigation, accessibility, and match the full UX kit. "It works" is not done.
globs: "*.{tsx,jsx,vue,svelte,html}"
---

# Rule 24: UX Completeness

## The Rule

Every page in a user-facing app MUST have all of the following. A page missing any
of these is not "done" — regardless of whether the happy path works.

## The 5 States (mandatory)

1. **Empty** — No data yet: friendly illustration/icon + primary CTA
2. **Loading** — Skeleton matching final layout (not a spinner)
3. **Error** — Clear message + retry + support link
4. **Partial** — Some data loaded, some failed: show what you have
5. **Success** — Toast / inline feedback after every write

## Feedback on Every Action (mandatory)

Every button click, form submit, save, delete, or network call gives feedback:
- Optimistic update where safe
- Loading state on the button (spinner + disabled)
- Toast on success / error
- Confirmation dialog for destructive actions
- Undo where possible (5-10 second window)

## Accessibility Floor (WCAG 2.2 AA)

- Semantic HTML (`<header>`, `<nav>`, `<main>`, `<article>`)
- Focus rings visible on ALL interactive elements
- Keyboard-navigable throughout
- Color contrast ≥ 4.5:1 (3:1 for large text)
- Form labels linked to inputs
- Alt text on meaningful images
- Axe-core in CI, zero critical/serious violations

## Visual Polish Floor

- Dark mode toggle (respects `prefers-color-scheme`)
- Reduced motion support (`prefers-reduced-motion`)
- Responsive at 320 / 768 / 1280 / 1920
- Hover states on interactive elements
- Transitions 150-300ms (respecting reduced motion)
- No lorem ipsum shipped

## Destructive Actions

- Confirmation dialog with clear consequences ("deletes 5 linked orders")
- For dangerous actions: type-to-confirm (type the item's name)
- After confirm: loading → success toast with Undo (if possible)
- Audit log entry (Rule 25)

## Tables / Lists

- Search (debounced 300ms)
- Sort (visible per-column indicator)
- Filter (multi-select, saveable views)
- Pagination OR infinite scroll
- Empty state when no results match filters
- Loading skeleton rows
- Whole-row clickable (not just a button)
- Bulk actions (checkboxes + floating bar)
- Export (CSV/Excel/PDF)

## Forms

- Labels above inputs
- Inline validation on blur
- Specific, actionable error messages
- Auto-save drafts for long forms
- Tab order matches visual order
- Submit shows loading + disables
- Enter submits, Esc cancels

## System Pages (every app must have)

- 404 — friendly, with search + home link
- 403 — "no permission" + contact admin
- 500 — friendly error + retry + request ID
- Maintenance — "back at HH:MM"
- Rate limited — "try again in X seconds"
- Offline — for PWAs

## Complexity-Driven Scope

| Complexity | What's Enforced |
|-----------|-----------------|
| **Simple** | 5 states + basic feedback + keyboard + a11y floor |
| **Medium** | Above + full tables/forms + dark mode + 404/500 + responsive |
| **Complex** | Above + RTL + i18n + onboarding + command palette + optimistic updates + perf budgets in CI + WCAG audit in CI |

## How to Verify

Run `/ux-kit audit` — it scans pages and reports missing patterns.
Run before declaring any milestone done.

## Why This Rule Exists

- "Done = happy path works" is how apps ship feeling unfinished.
- Users judge apps by the 30% of edge cases, not the 70% happy path.
- Empty states are the first thing new users see — they set the tone.
- Loading and error states determine whether the user gives up.
- Accessibility is a legal requirement in many jurisdictions and a moral one everywhere.

## Escape Hatch

A page may skip a specific state ONLY with explicit justification:
- "This page never has an empty state — there's always seeded data"
- Document why in the page spec
- Without justification, the state is mandatory
