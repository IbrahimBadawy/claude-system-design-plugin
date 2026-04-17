# /design-system - Design System & Token Designer

Generate, audit, or extend a design system. Enforces W3C design tokens, atomic design
hierarchy, and the standard 8 token categories (color, spacing, type, radius, shadow,
motion, z-index, breakpoints).

## Usage
```
/design-system                      # Generate a starter token set for the project
/design-system audit                # Check existing styles for token violations
/design-system tokens               # Show all defined tokens
/design-system dark-mode            # Generate dark mode overrides
/design-system base <base>          # Base on Material, Carbon, Polaris, shadcn, Fluent
/design-system atomic               # Organize components into atomic levels
```

## What `/design-system` generates

### `design/TOKENS.md` — source of truth
```markdown
# Design Tokens: <project-name>

## Color
### Primitive tokens (raw values)
blue-500: #3B82F6
gray-900: #111827
...

### Semantic tokens (intent)
color-primary: var(--blue-500)
color-surface: var(--gray-50)
...

### Component tokens
button-primary-bg: var(--color-primary)
...

## Spacing
... (8-point grid, --space-1 through --space-20)

## Typography
... (type scale, font stacks, line heights)

## Border Radius
...

## Shadow
...

## Motion
...

## Z-index
...

## Breakpoints
...
```

### `src/styles/tokens.css` — the actual CSS
Generated from the design doc. Includes:
- `:root` with light-mode values
- `.dark` class with dark-mode overrides
- `@media (prefers-color-scheme: dark)` support
- `@media (prefers-reduced-motion: reduce)` guards

### `src/styles/atomic.css` — component structure comments
```css
/* =============================================
   ATOMS - smallest units (Button, Input, Icon)
   ============================================= */

/* =============================================
   MOLECULES - small groups (SearchBar, FormField)
   ============================================= */

/* =============================================
   ORGANISMS - complex sections (Header, ProductCard)
   ============================================= */
```

### `.storybook/preview.ts` — Storybook configuration
- Light/dark theme switcher
- Viewport addon (375, 768, 1280, 1920)
- A11y addon enabled
- Stories organized by atomic level

### ESLint rule to enforce tokens
```js
// .eslintrc
rules: {
  "no-restricted-syntax": [
    "error",
    {
      "selector": "Literal[value=/^#[0-9a-fA-F]{3,8}$/]",
      "message": "Use design tokens instead of hex colors. See src/styles/tokens.css"
    }
  ]
}
```

## `/design-system audit` — what it catches

```markdown
## Design System Audit

### Hex colors used instead of tokens — 47
1. `src/components/Button.tsx:8` — `color: #3B82F6` → `var(--color-primary)`
2. `src/pages/landing.tsx:15` — `background: #FFFFFF` → `var(--color-bg)`
...

### Arbitrary spacing values — 12
1. `src/styles/layout.css:5` — `margin: 13px` → closest token `--space-3` (12px)
...

### Font sizes outside scale — 5
1. `src/components/Heading.tsx:10` — `font-size: 15px` (not in scale)
   Scale: 12/14/16/18/20/24/30/36/48/60
...

### Physical CSS properties (not logical) — 23
See /i18n audit for full list.

### Unused tokens — 8
- `--color-tertiary` defined but never used
...

### Tokens missing dark-mode values — 3
- `--shadow-md` has no dark-mode override
...
```

## Base Design System Options

`/design-system base shadcn` uses shadcn/ui's HSL CSS vars approach (dominant in React 2026).

`/design-system base carbon` uses IBM's Carbon for enterprise data-dense apps with WCAG AAA.

`/design-system base material` uses Material 3 with dynamic color.

`/design-system base polaris` uses Shopify's Polaris for admin/merchant UIs.

`/design-system base fluent` uses Microsoft's Fluent 2 for productivity apps.

`/design-system base primer` uses GitHub's Primer for developer tools.

## Atomic Design Organization

`/design-system atomic` scaffolds Storybook folders + enforces dependency direction:

```
src/components/
├── atoms/         # Button, Input, Label, Icon, Badge
├── molecules/     # SearchBar, FormField, MenuItem
├── organisms/     # Header, ProductCard, DataTable
├── templates/     # DashboardLayout, SettingsLayout
└── pages/         # Actual route components (thin)
```

ESLint import rule: atoms can't import molecules/organisms.

## Dark Mode

`/design-system dark-mode` generates a `.dark` class override for every color token:

```css
.dark {
  --color-bg: 222 47% 11%;
  --color-fg: 210 40% 98%;
  --color-muted: 217 33% 17%;
  --color-border: 217 33% 17%;
  /* ... */
}
```

Plus component overrides for shadows that need adjustment in dark mode.

## Integration

- `/frontend design` checks for a design system before picking a UI library
- `/pages` generates pages using token-only styling
- `/implement` enforces "no hex, no arbitrary spacing" during scaffolding
- `/a11y-audit` verifies token contrast pairs meet WCAG
- `/ux-kit` uses motion tokens for animations

## Examples

```
/design-system                       # Generate starter tokens
/design-system base shadcn           # Based on shadcn/ui
/design-system audit                 # Find token violations
/design-system dark-mode             # Add dark theme
/design-system tokens                # List all tokens
/design-system atomic                # Organize by atomic level
```

---

**What's next?**
- `/frontend design` — pick a UI library that uses these tokens
- `/a11y-audit contrast` — verify token contrast pairs
- `/ux-kit generate` — scaffold components that consume tokens
