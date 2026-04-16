---
name: frontend-design
description: Frontend design skill - triggered when designing or implementing user interfaces. Produces a COMPLETE frontend: full page inventory, permission-aware UI, all UX states (empty/loading/error/partial/success), keyboard navigation, accessibility (WCAG 2.2 AA), dark mode, RTL, dashboards, admin panel, audit log UI. Selects the best UI library, enforces Rule 24 (UX Completeness) and Rule 25 (RBAC by Default).
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, WebFetch, WebSearch, Agent
---

# Frontend Design Skill

You are designing or implementing a frontend. Your output is a **complete, professional,
usable app** — not a happy-path skeleton.

## The Mandate

When you finish frontend work, the user should say: "there's no page I wanted that
isn't here, no button that doesn't work, no state that's ugly, no action that doesn't
give feedback." If any of those fail, the job is not done.

## When This Skill Activates

- User runs `/frontend design`, `/frontend libraries`, `/frontend layout`
- User asks to build a UI, page, dashboard, or frontend component
- During `/implement` when working on the frontend layer

## Step 0: COMPLETENESS CHECK (run first)

Before picking a UI library or writing any code, verify completeness:

1. **PAGES.md exists** — if not, run `/pages` to enumerate ALL pages
2. **RBAC.md exists** — if not, run `/rbac` to design the permission system
3. **UX kit is defined** — if not, run `/ux-kit` to define states, feedback, a11y

If any is missing, STOP and build that first. Visual polish on an incomplete app is
wasted effort.

## Step 1: ASK Before Designing

After completeness is confirmed, ask:

1. **App type?** Dashboard / SaaS / Landing / E-commerce / Admin / Mobile
2. **Visual style?** Modern minimal / Corporate / Dark & sleek / Colorful / Data-dense
3. **Layout?** Sidebar + Content / Top navbar / Multi-panel / Full-width
4. **Must-haves?** Dark mode, RTL, responsive, charts, tables, forms, file upload
5. **Cross-check PAGES.md** — confirm nothing is missing

## Step 2: Mandatory Frontend Structure

Every frontend produced by this skill MUST include:

### Core Layout
- **Shell**: header + sidebar (or top nav) + main + optional right panel
- **Nav**: active route highlighted, responsive (drawer on mobile)
- **User menu**: avatar, profile, settings, logout, theme toggle
- **Global search**: Cmd-K / Ctrl-K palette (cmdk, kbar, or equivalent)
- **Notification center**: bell icon with dropdown, mark read, filters
- **Breadcrumbs**: on nested pages
- **Keyboard shortcuts**: `?` opens cheatsheet modal

### Auth Pages (all of them)
- Login (email+password, OAuth buttons, forgot link)
- Register (with email verification flow)
- Forgot password + reset password (token-based)
- Email verification pending + confirmation
- 2FA enroll + challenge
- Invite acceptance (if multi-user)

### User Account Pages
- Profile (avatar, name, bio)
- Account settings (email, password, language, timezone)
- Security (2FA, active sessions, device list)
- Notification preferences (per-channel, per-category)
- Billing / subscription (if paid)
- API keys / tokens (if API)
- Danger zone (delete account, export data)

### Admin Panel (if multi-user)
- Admin dashboard with KPIs
- Users list (search, filter, invite, suspend, impersonate)
- User detail (profile, roles, activity, login history)
- Roles management (list + edit + assign)
- Permissions matrix (role × permission checkbox grid)
- Audit log (searchable, exportable)
- System settings
- Feature flags

### Main Dashboard
- Personalized greeting
- Quick stats cards (4-8 KPIs)
- Main chart / visualization
- Activity feed or recent items
- Quick actions panel
- Optional: onboarding checklist for first-time users

### Per-Entity Pages (list / detail / create / edit)
For EACH core entity (Products, Orders, Customers, etc.):
- **List**: data table with search + sort + filter + pagination + bulk actions + export
- **Detail**: full info + related data + activity timeline + actions + comments
- **Create**: multi-step form (if complex) + auto-save draft + validation + preview
- **Edit**: pre-filled + change tracking + save/cancel
- **Archive/Trash**: soft-deleted items, restore, permanent delete

### System Pages
- 404 (friendly, with search + home link)
- 403 ("no permission" + contact admin)
- 500 (friendly error + retry + request ID)
- Maintenance mode
- Rate limited
- Offline (for PWAs)

### Legal / Compliance
- Terms of Service
- Privacy Policy
- Cookie settings (granular consent)

## Step 3: Select Best UI Library

ALWAYS research the latest version before recommending.

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

### Svelte
| Need | Recommendation | Why |
|------|---------------|-----|
| Modern | **Skeleton** | Tailwind-based, clean |
| Enterprise | **Carbon** | IBM's system |
| Flowbite Svelte | **Flowbite-Svelte** | Tailwind + Flowbite |

## Step 4: Essential Companion Libraries

Beyond the UI library, every frontend needs:

| Category | React | Vue | Angular |
|----------|-------|-----|---------|
| **HTTP Client** | axios / ky | axios / ky | HttpClient (built-in) |
| **Server State** | TanStack Query | Vue Query | NgRx / signals |
| **Client State** | Zustand | Pinia | NgRx / signals |
| **Forms** | React Hook Form + Zod | VeeValidate + Zod | Reactive Forms |
| **Tables** | TanStack Table | PrimeVue DataTable | AG Grid / PrimeNG |
| **Charts** | Recharts / Tremor | ECharts Vue / Chart.js | ngx-charts / Chart.js |
| **Icons** | Lucide React | Iconify Vue | Angular Material Icons |
| **Animation** | Framer Motion | Vue Transition + GSAP | Angular Animations |
| **Toast** | Sonner | Vue Sonner | ngx-toastr |
| **Date** | date-fns / dayjs | date-fns / dayjs | date-fns / dayjs |
| **i18n** | react-i18next / next-intl | vue-i18n | @ngx-translate/core |
| **Command palette** | cmdk / kbar | custom | custom |
| **File upload** | react-dropzone | vue-file-agent | ngx-dropzone |
| **Rich text** | TipTap / Plate | TipTap | Quill / TipTap |

## Step 5: UX Completeness (Rule 24) — MANDATORY

Every page MUST have:

1. **5 states** — empty / loading (skeleton) / error / partial / success
2. **Feedback on every action** — toast, inline, banner, modal, optimistic
3. **Destructive action protocol** — confirmation + type-to-confirm for dangerous + undo toast
4. **Form best practices** — labels above, inline validation, auto-save, keyboard
5. **Tables** — search + sort + filter + pagination + bulk + export + empty state
6. **Keyboard nav** — Tab order, shortcuts, Cmd-K, `?` cheatsheet
7. **A11y floor** — WCAG 2.2 AA, semantic HTML, focus rings, alt text, axe-core in CI
8. **Visual polish** — dark mode, reduced motion, responsive, transitions 150-300ms
9. **No lorem ipsum** — real microcopy on every button, empty state, error
10. **RTL ready** — `dir="rtl"`, CSS logical properties

## Step 6: RBAC-Aware UI (Rule 25) — MANDATORY

Every interactive element checks permissions:

```tsx
// React example
import { Can } from '@/lib/rbac';

<Can I="approve" a="orders" scope="team">
  <Button onClick={approve}>Approve</Button>
</Can>

<ProtectedRoute required="admin:users:manage">
  <UsersAdminPage />
</ProtectedRoute>
```

```vue
<!-- Vue example -->
<template>
  <Can i="approve" a="orders" scope="team">
    <PButton @click="approve">Approve</PButton>
  </Can>
</template>
```

Hide buttons that the user can't use. Never show a button that 403s.

## Step 7: Companion Tool

For professional visual design (styles, palettes, fonts), recommend:
```bash
npm i -g uipro-cli && uipro init --ai claude
```
67 UI styles, 161 color palettes, 57 font pairings, 99 UX guidelines.

## Step 8: Implementation Standards

### Accessibility (WCAG 2.2 AA) — MANDATORY
- Semantic HTML, ARIA labels, alt text
- Keyboard navigation for all interactive elements
- Color contrast 4.5:1 minimum
- Focus indicators visible
- Prefer libraries with built-in a11y (Radix, Headless UI, Mantine, PrimeVue)

### RTL Support (Arabic / Hebrew)
- CSS logical properties: `margin-inline-start` not `margin-left`
- `dir="rtl"` on html element
- Icons that imply direction (back arrow, chevron) must flip
- Test full UI in RTL mode

### Performance
- Code splitting (lazy load routes)
- Image optimization (WebP/AVIF, lazy loading)
- Bundle < 200KB initial JS
- Core Web Vitals: LCP < 2.5s, INP < 200ms, CLS < 0.1

### Responsive Design
- Mobile-first approach
- Test at: 375px (mobile), 768px (tablet), 1280px (desktop), 1920px (wide)
- Use CSS Grid + Flexbox, not fixed widths

## Step 9: RESEARCH Before Code

ALWAYS WebSearch + WebFetch the docs for:
- The chosen UI library's latest version and API
- Installation and configuration
- Component props and usage examples
- Theme customization

## Step 10: Audit Before Declaring Done

Before saying "frontend is done":
1. Run `/pages audit` — all pages in inventory are implemented
2. Run `/rbac audit` — every endpoint & button is permission-guarded
3. Run `/ux-kit audit` — all pages have the 5 states + feedback + a11y
4. Run axe-core / Lighthouse — no critical/serious violations
5. Test keyboard-only navigation through critical flows
6. Test dark mode + RTL (if applicable)

Only after all four audits pass is the frontend "done".
