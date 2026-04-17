# Accessibility — Deep Practice (WCAG 2.2 AA+)

> Canonical sources: WAI-ARIA Authoring Practices (APG), WebAIM Million 2025,
> WCAG 2.2, APCA, Microsoft Inclusive Design, W3C COGA.

---

## The Brutal Reality (WebAIM Million 2025)

- **94.8%** of home pages fail WCAG automated checks
- **Six issues cause 96% of all errors:**
  1. Low-contrast text (79% of pages)
  2. Missing alt text (55.5%)
  3. Missing form labels
  4. Empty links
  5. Empty buttons
  6. Missing document language
- Pages using ARIA average **more errors** (57 vs 25) than pages without — ARIA is a
  power tool usually pointed at the wrong target

Fix these six and you're ahead of 95% of the web.

Source: https://webaim.org/projects/million/2025

---

## Top 10 Checklist

1. **Color contrast 4.5:1 body / 3:1 large & UI** — measure every token pair at design time. Plan migration path to **APCA** (Lc 60 for body, Lc 45 for large) as WCAG 3 draft matures
2. **Every image has `alt`** — decorative: `alt=""`; informative: describe content and function, not appearance
3. **Every form input has a visible `<label for>`** linked by id. Placeholders are NOT labels. Add `autocomplete` (`email`, `current-password`, `one-time-code`, `tel`) on every relevant field
4. **Focus visible, never `outline: none`** without replacement (WCAG 2.2 SC 2.4.11: ≥2px thick, 3:1 contrast against both states)
5. **Target size ≥ 24×24 CSS px** (WCAG 2.2 SC 2.5.8), 44×44 on touch-primary surfaces
6. **Keyboard reachable and operable** — every interactive control, tab order follows visual order, no keyboard traps, no drag-only interactions (SC 2.5.7)
7. **Dialog pattern:** `role="dialog" aria-modal="true" aria-labelledby`, focus moves inside on open, trap cycles Tab/Shift+Tab, Escape closes, focus restores to trigger on close. Prefer **native `<dialog>` + `showModal()`**
8. **Skip link** is the first focusable element (`<a href="#main" class="skip-link">`), visible on focus, targets `<main id="main" tabindex="-1">`
9. **Roving tabindex** for composite widgets (tabs, menu, tree, grid, toolbar): parent `tabindex=0`, children `tabindex=-1`, arrow keys move within, Tab exits
10. **Screen reader tested** on at least one pair per OS — NVDA+Firefox (Windows), VoiceOver+Safari (macOS/iOS), TalkBack+Chrome (Android). Automated tools catch only ~40-57% of issues

---

## WAI-ARIA APG — Canonical Patterns

The plugin must implement these correctly:

| Pattern | Key ARIA | Keyboard contract |
|---------|----------|-------------------|
| **Combobox (autocomplete)** | `role=combobox aria-expanded aria-controls aria-activedescendant` | ↓ open list, ↑↓ move, Enter commit, Esc clear |
| **Dialog (modal)** | `role=dialog aria-modal=true aria-labelledby` | Tab trap, Esc close, focus restore |
| **Menu / Menubar** | `role=menu role=menuitem` | ↑↓ move, → open submenu, Esc close |
| **Tabs** | `role=tablist/tab/tabpanel aria-selected aria-controls` | ←→ move, Home/End, auto-activate or manual |
| **Tree** | `role=tree/treeitem aria-expanded aria-level aria-setsize` | ↑↓ move, → expand, ← collapse, type-ahead |
| **Grid** | `role=grid/row/gridcell` | Arrow keys, PgUp/PgDn, Ctrl+Home/End |
| **Accordion** | `<button aria-expanded aria-controls>` | Space/Enter toggle, ↑↓ between headers |
| **Carousel** | `role=group aria-roledescription="carousel"` + controls | Prev/Next, pause on focus/hover, no auto |

**Rule:** Before building a custom interactive component, check APG. If it exists there,
use an accessible library (Radix UI, Headless UI, React Aria) that implements it.

Source: https://www.w3.org/WAI/ARIA/apg/

---

## Forms Accessibility — Deep Rules

- **`<label for>` always.** If visually hidden, use `.visually-hidden` utility, NEVER `display: none`
- **Required:** `aria-required="true"` + visible `*` that's part of the accessible name ("Email *")
- **Errors:** `aria-invalid="true"` + `aria-describedby="email-error"` pointing to a `role="alert"` region. Announce dynamically
- **Group related checkboxes/radios** with `<fieldset><legend>`
- **`autocomplete` tokens** match WHATWG spec — critical for password managers and users with cognitive disabilities (SC 1.3.5)
- **Error summary** at the top of long forms links to each failed field

```html
<fieldset>
  <legend>Contact preferences</legend>

  <label>
    <input type="checkbox" name="contact" value="email" /> Email
  </label>
  <label>
    <input type="checkbox" name="contact" value="sms" /> SMS
  </label>
</fieldset>

<label for="email">Work email *</label>
<input
  id="email"
  type="email"
  autocomplete="email"
  aria-required="true"
  aria-invalid="true"
  aria-describedby="email-error"
/>
<span id="email-error" role="alert">
  Enter a valid email — e.g., name@company.com
</span>
```

---

## Screen-Reader-Only Utility

Ship this class in every project:

```css
.visually-hidden {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}
```

Use for:
- Icon-only buttons: `<button><Icon /> <span class="visually-hidden">Delete</span></button>`
- Form labels when design demands none
- Section headings that are redundant visually

---

## Microsoft Inclusive Design — 3 Principles

1. **Recognize exclusion** — every design choice excludes someone; ask who
2. **Solve for one, extend to many** — curb cuts, captions, voice control: built for specific needs, used by everyone
3. **Learn from diversity** — real users with permanent, temporary, and situational constraints

**Persona Spectrum example:** one-armed (permanent), arm in cast (temporary), new parent
holding a baby (situational). UI should serve all three.

Source: https://inclusive.microsoft.design/

---

## Cognitive Accessibility (W3C COGA)

- **Reading level ≤ 8th grade** for general content (measure with Hemingway / Flesch)
- **Plain language:** short sentences (<20 words), active voice, one idea per paragraph
- **Consistent layout** — don't move the primary action between pages
- **Avoid time limits;** if unavoidable, offer extension (SC 2.2.1)
- **Provide help affordances** (tooltips, inline hints) without cluttering

Source: https://www.w3.org/TR/coga-usable/

---

## Accessibility Testing Stack

- **axe DevTools** (browser extension, manual) + **`@axe-core/react`** in dev
- **Storybook a11y addon** — every component story gets axe check
- **Pa11y CI** — crawl key routes, fail build on new issues
- **Lighthouse CI** — overall score ≥ 95, no critical a11y violations
- **Playwright + `@axe-core/playwright`** for E2E a11y assertions
- Manual smoke: **keyboard-only pass, one screen reader pass before release**

---

## Keyboard Contract Checklist

Every interactive UI must answer:

- [ ] Can I reach this with Tab? (focus order matches visual order)
- [ ] Is the focus ring clearly visible?
- [ ] Can I activate with Enter or Space?
- [ ] If inside a composite widget (menu, tabs), do arrow keys work?
- [ ] Can I Esc out of dialogs/menus?
- [ ] Is focus restored correctly after modal close?
- [ ] No keyboard traps?
- [ ] Skip link to main content as first focusable element?

---

## A11y Audit Checklist — run before shipping

1. axe: 0 critical, 0 serious
2. Lighthouse a11y ≥ 95
3. Keyboard-only pass on primary flow
4. Screen reader pass (NVDA or VoiceOver)
5. Zoom to 200% — no content lost
6. `prefers-reduced-motion` honored
7. Color contrast verified for all token pairs
8. Alt text on every meaningful image
9. Form labels linked to every input
10. Heading hierarchy correct (`h1` → `h2` → `h3`, no skips)

Sources:
- https://webaim.org/projects/million/2025
- https://www.w3.org/WAI/ARIA/apg/
- https://www.w3.org/WAI/standards-guidelines/wcag/new-in-22/
- https://git.apcacontrast.com/documentation/APCA_in_a_Nutshell.html
- https://inclusive.microsoft.design/
- https://www.w3.org/TR/coga-usable/
