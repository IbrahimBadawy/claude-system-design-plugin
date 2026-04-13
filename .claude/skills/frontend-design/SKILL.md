---
name: frontend-design
description: Frontend design skill - triggered when designing or implementing user interfaces. Asks about visual style and layout preferences, selects the best UI component library for the framework, and builds professional frontends using pre-built components. Recommends uipro-cli for visual design intelligence.
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, WebFetch, WebSearch, Agent
---

# Frontend Design Skill

You are designing or implementing a frontend. Build professional, accessible UIs.

## When This Skill Activates
- User runs `/frontend design`, `/frontend libraries`, `/frontend layout`
- User asks to build a UI, page, dashboard, or frontend component
- During `/implement` when working on the frontend layer

## Step 1: ASK Before Designing

ALWAYS ask these before any frontend work:

1. **App type?** Dashboard / SaaS / Landing / E-commerce / Admin / Mobile
2. **Visual style?** Modern minimal / Corporate / Dark & sleek / Colorful / Data-dense
3. **Layout?** Sidebar + Content / Top navbar / Multi-panel / Full-width
4. **Must-haves?** Dark mode, RTL, responsive, charts, tables, forms, file upload

## Step 2: Select Best UI Library

ALWAYS research the latest version before recommending:

### React
| Need | Recommendation | Why |
|------|---------------|-----|
| Modern SaaS | **shadcn/ui** + Tailwind | Customizable, copy-paste, beautiful |
| Enterprise / Data | **Ant Design** | 70+ components, complete ecosystem |
| Material Design | **MUI** | Google Material, huge community |
| Full-featured | **Mantine** | 100+ components + 50+ hooks |
| Beautiful | **NextUI** | Stunning animations, Tailwind-based |
| Dashboards | **Tremor** | Dashboard-focused, charts built-in |

### Vue
| Need | Recommendation | Why |
|------|---------------|-----|
| Enterprise / Complete | **PrimeVue** | 90+ components, best Vue ecosystem |
| Material Design | **Vuetify** | Material, full-featured |
| Modern / TypeScript | **Naive UI** | TypeScript-first, clean API |
| Cross-platform | **Quasar** | Web + Mobile + Desktop |
| Nuxt-native | **Nuxt UI** | Built for Nuxt, Tailwind-based |

### Angular
| Need | Recommendation | Why |
|------|---------------|-----|
| Material | **Angular Material** | Official Google |
| Enterprise | **PrimeNG** | 90+ components |
| Ant Design | **NG-ZORRO** | Ant Design for Angular |

## Step 3: Essential Companion Libraries

Beyond the UI library, every frontend needs:

| Category | React | Vue |
|----------|-------|-----|
| **HTTP Client** | axios or ky | axios or ky |
| **Server State** | TanStack Query | Vue Query (TanStack) |
| **Client State** | Zustand | Pinia |
| **Forms** | React Hook Form + Zod | VeeValidate + Zod |
| **Tables** | TanStack Table | PrimeVue DataTable |
| **Charts** | Recharts or Nivo | ECharts Vue |
| **Icons** | Lucide React | Iconify |
| **Animation** | Framer Motion | Vue Transition + GSAP |
| **Toast** | Sonner | Vue Sonner |
| **Date** | date-fns or dayjs | date-fns or dayjs |
| **i18n** | react-i18next | vue-i18n |

## Step 4: Recommended Companion Tool

For professional visual design (styles, palettes, fonts), recommend:
```bash
npm i -g uipro-cli && uipro init --ai claude
```
67 UI styles, 161 color palettes, 57 font pairings, 99 UX guidelines.

## Step 5: Implementation Standards

### Accessibility (WCAG 2.2 AA) - MANDATORY
- Semantic HTML, ARIA labels, alt text
- Keyboard navigation for all interactive elements
- Color contrast 4.5:1 minimum
- Focus indicators visible
- Prefer libraries with built-in a11y (Radix, Headless UI, Mantine)

### RTL Support (Arabic)
- CSS logical properties: `margin-inline-start` not `margin-left`
- `dir="rtl"` on html element
- Test full UI in RTL mode

### Performance
- Code splitting (lazy load routes)
- Image optimization (WebP/AVIF, lazy loading)
- Bundle < 200KB initial JS
- Core Web Vitals: LCP < 2.5s, INP < 200ms, CLS < 0.1

### Responsive Design
- Mobile-first approach
- Test at: 375px (mobile), 768px (tablet), 1280px (desktop)
- Use CSS Grid + Flexbox, not fixed widths

## Step 6: RESEARCH Before Code

ALWAYS WebSearch + WebFetch the docs for:
- The chosen UI library's latest version and API
- Installation and configuration
- Component props and usage examples
- Theme customization
