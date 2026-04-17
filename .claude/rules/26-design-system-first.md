---
description: Every project must have design tokens BEFORE UI code. No hex colors, no arbitrary spacing, no magic pixel values in components. Atomic design hierarchy enforced. Token-first beats component-first.
globs: "*.{css,scss,tsx,jsx,vue,svelte}"
---

# Rule 26: Design System First

## The Rule

Every project must establish **design tokens** before any UI component is written.
Components consume tokens ONLY — never raw hex, never arbitrary pixel values, never
one-off colors.

## 8 Token Categories (required)

Every project has tokens defined for:

1. **Color** — primitive + semantic + component tiers
2. **Spacing** — 8-point grid (4, 8, 12, 16, 24, 32, 48, 64, 80)
3. **Typography** — family, scale (6-8 sizes max), weights, line-heights
4. **Border radius** — sm / md / lg / full
5. **Shadow** — layered, never flat single shadows
6. **Motion** — duration (fast/normal/slow) + easing tokens
7. **Z-index** — dropdown / modal / toast / tooltip layers
8. **Breakpoints** — sm / md / lg / xl / 2xl

## Three-Tier Token Model

1. **Primitive tokens** — raw values: `blue-500: #3B82F6`
2. **Semantic tokens** — intent: `color-primary: var(--blue-500)`
3. **Component tokens** — specific: `button-primary-bg: var(--color-primary)`

**Enforcement:** components consume ONLY semantic or component tokens, never primitives
directly. Swapping brand color = one-line change in semantic tier.

## What's Banned

- Hex colors in components (`color: #3B82F6`) → use `color-primary` token
- Arbitrary spacing (`margin: 13px`) → use nearest `--space-*` token
- Font sizes outside the scale (`font-size: 15px`) → use scale value
- Flat single shadows (`box-shadow: 0 2px 4px rgba(0,0,0,0.1)`) → use layered shadow tokens
- Physical CSS properties (`margin-left`, `padding-right`) → use logical properties
- Colors defined in dark mode without matching light mode token

## Atomic Design Hierarchy

Components organized by level:
```
src/components/
├── atoms/         # Button, Input, Label, Icon, Badge
├── molecules/     # SearchBar, FormField
├── organisms/     # Header, DataTable, ProductCard
├── templates/     # DashboardLayout
└── pages/         # Actual routes (thin — compose templates)
```

**Dependency direction:** higher imports lower, never sideways.
- Atoms NEVER import molecules/organisms
- Enforce via ESLint `import/no-restricted-paths`

## Enforcement

- ESLint rule: `no-restricted-syntax` flags hex color literals in components
- Stylelint: `declaration-property-value-disallowed-list` blocks physical CSS
- Pre-commit hook: fail if components reference primitive tokens directly
- `/design-system audit` scans for violations

## Dark Mode

Every color token has a dark-mode override. No `dark:` classes per component —
they're an afterthought.

```css
:root {
  --color-bg: 0 0% 100%;
  --color-fg: 222 47% 11%;
}
.dark {
  --color-bg: 222 47% 11%;
  --color-fg: 210 40% 98%;
}
```

## Complexity-Driven Scope

- **Simple**: Starter token set (8 categories, defaults). No Storybook required.
- **Medium**: Full starter set + dark mode + atomic design folder structure + Storybook
- **Complex**: Full set + multi-brand theming + Style Dictionary export to iOS/Android + visual regression on tokens

## Why This Rule Exists

- Token-first is 10× cheaper than retrofitting. Renaming 200 `#3B82F6` to a token
  takes 4 hours the first time, then zero forever
- Dark mode retrofits are the #1 cause of UI bugs in year-2 apps — build it in from day 1
- Consistent visuals = professional feel. Hex soup in components screams "unfinished"
- Swapping design system (from "what we have" to shadcn/Carbon/Material) must be
  possible without touching every component — tokens make that trivial

## Escape Hatch

Raw values allowed ONLY in:
- Token definition files (`tokens.css`, `theme.ts`)
- Generated/vendored code (e.g., icon SVGs)
- Third-party library wrappers

All other usages must reference a token.
