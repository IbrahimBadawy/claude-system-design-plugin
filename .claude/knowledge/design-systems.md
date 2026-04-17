# Design Systems & Atomic Design

> How to build a design system that scales. Canonical sources: Brad Frost's
> *Atomic Design*, W3C Design Tokens spec, Style Dictionary, and comparisons
> of Material 3, Apple HIG, Fluent 2, Carbon, Polaris, Ant Design, shadcn/ui.

Use when: starting a new project (design tokens), auditing existing UI consistency,
choosing which public design system to base the app on.

---

## Atomic Design (Brad Frost)

5-level hierarchy — canonical since 2016 and still the standard:

| Level | Definition | Examples |
|-------|-----------|----------|
| **Atoms** | Smallest UI units, can't be broken further | Button, Input, Label, Icon, Avatar, Badge |
| **Molecules** | Simple groups of atoms functioning together | SearchBar (Input + Button), FormField (Label + Input + Error) |
| **Organisms** | Complex components forming a section | Header (Logo + Nav + Search + Avatar), ProductCard, DataTable |
| **Templates** | Page-level skeletons with placeholder content | Dashboard layout, SettingsPage layout |
| **Pages** | Templates filled with real content | The actual `/dashboard` route with live data |

### Enforcement Rules

1. **Dependencies flow upward only** — atoms NEVER import molecules/organisms
2. **Templates define layout + slots**; pages fill slots with data
3. **Storybook stories organized by atomic level** (one folder per level)
4. **A new component's level is declared** in its file header
5. **Design review at the right level** — review atoms in isolation, organisms in context

Source: https://atomicdesign.bradfrost.com/

---

## Design Tokens (W3C Spec)

The W3C Design Tokens Community Group spec (2024 draft) is the source of truth.
Every design system MUST have these **8 token categories**:

| Category | Purpose | Example tokens |
|----------|---------|----------------|
| **Color** | Palette + semantic + states | `--color-primary-500`, `--color-surface`, `--color-destructive` |
| **Spacing** | Layout rhythm (8-point grid) | `--space-1` (4px) … `--space-20` (80px) |
| **Typography** | Font family/size/weight/leading/tracking | `--font-sans`, `--text-sm`, `--leading-tight` |
| **Border Radius** | Corner rounding | `--radius-sm`, `--radius-md`, `--radius-lg`, `--radius-full` |
| **Shadow** | Elevation | `--shadow-xs` … `--shadow-2xl` (layered, not flat) |
| **Motion** | Duration + easing | `--duration-fast` (150ms), `--ease-out`, `--ease-in-out` |
| **Z-index** | Stacking order | `--z-dropdown: 1000`, `--z-modal: 1400`, `--z-toast: 1700` |
| **Breakpoints** | Responsive thresholds | `--bp-sm: 640px`, `--bp-md: 768px`, `--bp-lg: 1024px` |

### Token Tiers (Industry standard — Salesforce, Adobe)

1. **Primitive tokens** — raw values: `blue-500: #3B82F6`
2. **Semantic tokens** — intent-based: `color-primary: var(--blue-500)`
3. **Component tokens** — component-specific: `button-primary-bg: var(--color-primary)`

**Rule:** Components consume ONLY semantic or component tokens, NEVER primitives directly.
This lets you rebrand by swapping one layer.

Sources:
- W3C spec: https://tr.designtokens.org/format/
- Style Dictionary (Amazon, transforms tokens to CSS/iOS/Android): https://styledictionary.com/

---

## Shadcn/ui Token Approach (2024-2026 dominant in React)

```css
:root {
  --background: 0 0% 100%;
  --foreground: 222 47% 11%;
  --primary: 221 83% 53%;
  --primary-foreground: 210 40% 98%;
  --muted: 210 40% 96%;
  --border: 214 32% 91%;
  --radius: 0.5rem;
}
.dark {
  --background: 222 47% 11%;
  --foreground: 210 40% 98%;
  /* ... */
}
```

### Why this works

- **HSL triplets, no `hsl()` wrapper** → Tailwind can compose alpha: `bg-primary/50`
- **Every color is semantic**, not named by hue → swapping palette doesn't rename vars
- **Dark mode just swaps the `:root` block** → no component-level `dark:` classes
- **Radius is a single token** → consistent corner rounding across app

Source: https://ui.shadcn.com/docs/theming

---

## Major Design Systems — What Each Optimizes For

Pick the right base for your app's audience:

| System | Optimizes For | Key Feature |
|--------|--------------|-------------|
| **Material 3 (Google)** | Android-first, expressive, dynamic theming | Material You dynamic color from wallpaper; motion-heavy |
| **Apple HIG** | Platform fidelity, clarity, deference | Restraint, translucency, SF Symbols, typography-led |
| **Fluent 2 (Microsoft)** | Cross-platform enterprise productivity | Acrylic materials, soft corners, Segoe UI, depth tokens |
| **Carbon (IBM)** | Data-dense enterprise, accessibility first | WCAG AAA targets, IBM Plex, best-in-class data-viz |
| **Polaris (Shopify)** | Merchant admin workflows | Form-heavy patterns, extensive writing/voice guidelines |
| **Ant Design** | Enterprise CRUD apps | Highest component count, dense data tables |
| **Primer (GitHub)** | Developer tools, info density | Monospace integration, diff viewers, code-aware patterns |
| **Atlassian Design System** | Team collaboration | Workflow patterns, empty states, inline editing |

### Decision Matrix

| App type | Recommended Base |
|----------|------------------|
| Modern SaaS, startup, marketing-adjacent | **shadcn/ui** (Tailwind + Radix primitives) |
| Consumer mobile-web | **Material 3** or **Apple HIG** |
| Enterprise admin / data-heavy | **Carbon**, **Ant Design**, or **Fluent 2** |
| E-commerce admin | **Polaris** |
| Developer tools | **Primer** or **shadcn/ui** |
| Government / regulated | **Carbon** (WCAG AAA targets) |

Sources:
- Material 3: https://m3.material.io/
- Apple HIG: https://developer.apple.com/design/human-interface-guidelines
- Fluent 2: https://fluent2.microsoft.design/
- Carbon: https://carbondesignsystem.com/
- Polaris: https://polaris.shopify.com/
- Ant Design: https://ant.design/
- Primer: https://primer.style/
- Atlassian: https://atlassian.design/

---

## Starter Token Set (copy-paste for any new project)

```css
:root {
  /* Color — semantic */
  --color-bg: 0 0% 100%;
  --color-fg: 222 47% 11%;
  --color-primary: 221 83% 53%;
  --color-primary-fg: 210 40% 98%;
  --color-muted: 210 40% 96%;
  --color-muted-fg: 215 16% 47%;
  --color-border: 214 32% 91%;
  --color-success: 142 71% 45%;
  --color-warning: 38 92% 50%;
  --color-destructive: 0 84% 60%;

  /* Spacing (4px base, 8-point grid) */
  --space-0: 0;
  --space-1: 0.25rem;  /* 4px */
  --space-2: 0.5rem;
  --space-3: 0.75rem;
  --space-4: 1rem;     /* 16px */
  --space-5: 1.25rem;
  --space-6: 1.5rem;
  --space-8: 2rem;
  --space-10: 2.5rem;
  --space-12: 3rem;
  --space-16: 4rem;
  --space-20: 5rem;

  /* Typography */
  --font-sans: system-ui, -apple-system, 'Segoe UI', Roboto, sans-serif;
  --font-mono: ui-monospace, 'SF Mono', Menlo, monospace;
  --text-xs: 0.75rem;   /* 12px */
  --text-sm: 0.875rem;
  --text-base: 1rem;    /* 16px */
  --text-lg: 1.125rem;
  --text-xl: 1.25rem;
  --text-2xl: 1.5rem;
  --text-3xl: 1.875rem;
  --text-4xl: 2.25rem;
  --leading-tight: 1.25;
  --leading-normal: 1.5;
  --leading-relaxed: 1.75;

  /* Radius */
  --radius-sm: 0.25rem;
  --radius-md: 0.5rem;
  --radius-lg: 0.75rem;
  --radius-full: 9999px;

  /* Shadow — layered */
  --shadow-xs: 0 1px 2px 0 rgb(0 0 0 / 0.05);
  --shadow-sm: 0 1px 3px 0 rgb(0 0 0 / 0.1), 0 1px 2px -1px rgb(0 0 0 / 0.1);
  --shadow-md: 0 4px 6px -1px rgb(0 0 0 / 0.1), 0 2px 4px -2px rgb(0 0 0 / 0.1);
  --shadow-lg: 0 10px 15px -3px rgb(0 0 0 / 0.1), 0 4px 6px -4px rgb(0 0 0 / 0.1);
  --shadow-xl: 0 20px 25px -5px rgb(0 0 0 / 0.1), 0 8px 10px -6px rgb(0 0 0 / 0.1);

  /* Motion */
  --duration-fast: 150ms;
  --duration-normal: 250ms;
  --duration-slow: 400ms;
  --ease-out: cubic-bezier(0, 0, 0.2, 1);
  --ease-in-out: cubic-bezier(0.4, 0, 0.2, 1);
  --ease-spring: cubic-bezier(0.175, 0.885, 0.32, 1.275);

  /* Z-index */
  --z-dropdown: 1000;
  --z-sticky: 1100;
  --z-banner: 1200;
  --z-overlay: 1300;
  --z-modal: 1400;
  --z-popover: 1500;
  --z-toast: 1700;
  --z-tooltip: 1800;
}

.dark {
  --color-bg: 222 47% 11%;
  --color-fg: 210 40% 98%;
  --color-muted: 217 33% 17%;
  --color-muted-fg: 215 20% 65%;
  --color-border: 217 33% 17%;
}

@media (prefers-reduced-motion: reduce) {
  :root { --duration-fast: 0ms; --duration-normal: 0ms; --duration-slow: 0ms; }
}
```

---

## Design System Checklist

When setting up a new project:

- [ ] 8 token categories defined (color, spacing, type, radius, shadow, motion, z-index, breakpoints)
- [ ] Primitive + semantic + component token tiers established
- [ ] Dark mode token overrides in place
- [ ] Components consume semantic tokens only (no raw hex, no `px` magic numbers)
- [ ] Atomic design folder structure in Storybook
- [ ] Dependency rule enforced (atoms can't import molecules, etc.)
- [ ] `prefers-reduced-motion` overrides motion tokens
- [ ] Tokens exported via Style Dictionary if multi-platform
- [ ] Accessible color palette verified — 4.5:1 contrast text, 3:1 non-text
- [ ] Icon system standardized (one library: Lucide, Iconify, Heroicons)
- [ ] Typography scale max 6-8 sizes (no arbitrary `font-size`)
- [ ] Documentation site for the system (Storybook Docs, Zeroheight, or Mintlify)
