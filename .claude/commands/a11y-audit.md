# /a11y-audit - Accessibility Audit (WCAG 2.2 AA+)

Comprehensive accessibility check against the 6 issues that cause 96% of real-world
failures (WebAIM Million 2025), plus WAI-ARIA APG compliance, keyboard contract,
and screen reader testing guidance.

## Usage
```
/a11y-audit                         # Full project a11y audit
/a11y-audit for <page>              # Audit specific page
/a11y-audit contrast                # Check all color token pairs
/a11y-audit keyboard                # Walk through keyboard contract
/a11y-audit aria                    # Verify WAI-ARIA patterns
/a11y-audit fix <issue-id>          # Auto-fix a specific issue
```

## What it checks

### The Big 6 (96% of errors — WebAIM)
1. Color contrast (4.5:1 body / 3:1 large & UI; APCA Lc 60/45)
2. Alt text on every meaningful image
3. Form labels linked to every input
4. No empty links
5. No empty buttons
6. `<html lang>` set

### WCAG 2.2 specifics
- [ ] Focus visible, no `outline: none` without replacement (SC 2.4.11)
- [ ] Target size ≥ 24×24 CSS px (SC 2.5.8)
- [ ] No drag-only interactions (SC 2.5.7)
- [ ] Dragging alternative exists
- [ ] Consistent help (SC 3.2.6)
- [ ] Redundant entry prevented (SC 3.3.7)
- [ ] Accessible authentication — no cognitive puzzles (SC 3.3.8)

### WAI-ARIA Patterns
- [ ] Dialogs use `role="dialog" aria-modal="true" aria-labelledby`
- [ ] Focus moves into dialog on open, returns to trigger on close
- [ ] Escape closes dialog
- [ ] Comboboxes use canonical ARIA
- [ ] Menus/menubars use arrow keys, Escape closes
- [ ] Tabs use `role=tablist/tab/tabpanel aria-selected`
- [ ] Roving tabindex on composite widgets
- [ ] Carousels have pause/prev/next, pause on focus

### Keyboard Contract
- [ ] Every interactive element reachable via Tab
- [ ] Tab order follows visual order
- [ ] No keyboard traps
- [ ] Skip link as first focusable element
- [ ] Escape closes dropdowns, modals, menus
- [ ] Arrow keys navigate composite widgets

### Forms
- [ ] `<label for>` on every input
- [ ] `autocomplete` attribute set (email, current-password, one-time-code, tel)
- [ ] Required fields marked with `aria-required` + visible `*`
- [ ] Errors use `aria-invalid` + `aria-describedby` + `role="alert"`
- [ ] Related checkboxes/radios in `<fieldset><legend>`
- [ ] Error summary at top of long forms

### Screen Reader
- [ ] Semantic HTML (header/nav/main/article/footer landmarks)
- [ ] Heading hierarchy correct (no `h1` → `h3` skips)
- [ ] Icon-only buttons have `aria-label` or `.visually-hidden` text
- [ ] Live regions for dynamic updates
- [ ] Status messages use `role="status"` or `role="alert"`

## Output Format

```markdown
## Accessibility Audit: <project-name>

Pages scanned: 34
Score: 87/100

### Critical (block release) — 2
1. `src/pages/checkout.tsx` — Checkout button has no accessible name
   Fix: add `aria-label="Complete checkout"` or visible text
2. `src/components/Modal.tsx` — Dialog doesn't trap focus
   Fix: use Radix Dialog or `focus-trap-react`

### Serious — 5
3. `tokens/colors.css` — `--color-muted-fg` / `--color-bg` ratio is 3.8:1 (need 4.5:1)
4. `src/pages/settings.tsx:42` — Input without label (uses placeholder only)
5. `src/components/DeleteBtn.tsx` — Icon-only button, no aria-label
...

### Warnings — 8
6. Heading skip: h1 → h3 on `/dashboard`
7. `<img alt="logo.png">` — filename isn't description
...

### Passing ✓
- All 34 pages have `<html lang>` set
- Skip link present
- Keyboard-traversable nav
```

## Integration

- Runs `axe-core` via `@axe-core/cli` or `@axe-core/playwright`
- Runs `Pa11y` on key routes
- Checks Lighthouse a11y score (target ≥ 95)
- Validates color tokens against WCAG contrast + APCA
- Flags ARIA misuse (reports "ARIA used but wrong pattern" from APG)

## Fix Mode

`/a11y-audit fix <issue-id>` auto-applies recommended fix when safe:

- Adding `alt=""` to decorative images
- Adding `aria-label` from context
- Replacing `<div onclick>` with `<button>`
- Replacing `<a href="#">` with `<button>` when no navigation

## Examples

```
/a11y-audit                          # Full audit
/a11y-audit for /checkout            # One page
/a11y-audit contrast                 # Token contrast matrix
/a11y-audit keyboard                 # Walk keyboard contract
/a11y-audit aria                     # ARIA pattern check
/a11y-audit fix 3                    # Auto-fix issue #3
```

---

**What's next?**
- `/copy-audit` — catch blame language, empty states, missing labels
- `/i18n setup` — add RTL and multi-language
- `/ux-kit audit` — completeness check for UX patterns
