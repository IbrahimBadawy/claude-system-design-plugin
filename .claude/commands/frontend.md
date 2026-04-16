# /frontend - Frontend Design & Implementation

Professional frontend design with the best UI libraries, layouts, and component systems.
Produces a **complete** frontend: every page, every state, every UX pattern.

## Usage
```
/frontend design                    # Full frontend design flow
/frontend design --framework vue    # Design with specific framework
/frontend libraries <framework>     # Show best libraries for a framework
/frontend layout <type>             # Generate a specific layout (dashboard, landing, admin, etc.)
/frontend component <name>          # Generate a specific component
/frontend audit                     # Scan frontend for UX/accessibility/completeness gaps
```

## Related commands (recommended flow)

1. `/pages` — enumerate EVERY page the app needs (auth, admin, dashboard, CRUD, system)
2. `/rbac` — design permission system (buttons/pages hide based on roles)
3. `/frontend design` — visual style + component library + layout
4. `/ux-kit` — empty/loading/error states, feedback patterns, a11y
5. `/implement --frontend <framework>` — scaffold the whole thing

Run all of them before coding. The result is a frontend that feels complete,
not a happy-path skeleton.

## Recommended: Install UI/UX Pro Max Skill

For professional-grade visual design (styles, color palettes, typography, UX patterns),
install the uipro-cli skill alongside this plugin:

```bash
npm i -g uipro-cli
uipro init --ai claude
```

This adds a design intelligence database with:
- 67 UI styles (Glassmorphism, Neumorphism, Bento Grid, Minimalism, etc.)
- 161 industry-specific color palettes
- 57 font pairings with Google Fonts
- 25 chart types for dashboards
- 99 UX best-practice guidelines

**How they work together:**
- Our plugin handles **architecture** (which components, data flow, state management)
- uipro-cli handles **visual design** (which style, colors, fonts, spacing)
- Together = professionally designed AND well-architected frontend

If uipro-cli is installed, the `/frontend design` command will automatically leverage
its style database for better visual recommendations.

---

## Behavior

### Phase 0: Completeness (FIRST — always)

Before visual design, ensure completeness:

1. **Check `PAGES.md` exists** — if not, run `/pages` first to enumerate all pages
2. **Check `RBAC.md` exists** — if not, run `/rbac` to design permissions
3. **Check UX patterns list** — run `/ux-kit` to define states, feedback, keyboard, a11y

If any of these are missing, STOP and build them first. Visual polish on an incomplete
app is wasted effort — users notice missing pages and broken flows before color schemes.

### Phase 1: Discovery (ASK the user)

Before ANY frontend work, ask these questions:

**1. What type of application?**
- Admin Dashboard / Back-office
- Customer-facing Web App
- Landing Page / Marketing Site
- E-commerce Store
- SaaS Application
- Mobile App (React Native / Flutter)
- Internal Tool

**2. What visual style?**
- Modern & Minimal (clean lines, lots of whitespace)
- Corporate & Professional (structured, formal)
- Playful & Colorful (rounded corners, gradients, illustrations)
- Dark & Sleek (dark mode first, glassmorphism)
- Data-Dense (compact, information-heavy like Bloomberg)
- Material Design (Google-style)
- Apple-like (clean, premium feel)

**3. Layout preference?**
- Sidebar + Main Content (most common for dashboards)
- Top Navbar + Content (simple apps, landing pages)
- Sidebar + Top Navbar + Content (complex admin panels)
- Full-width (landing pages, content sites)
- Multi-panel (email-like: list + detail)

**4. Must-have features?**
- Dark/Light mode toggle
- RTL support (Arabic/Hebrew)
- Responsive (mobile-first)
- Accessibility (WCAG compliance)
- Animations/Transitions
- Data tables with sorting/filtering
- Charts and visualizations
- Form builder

**5. Core screens?** (cross-check with PAGES.md)
- Dashboard with KPIs
- User profile & settings
- Admin panel (users, roles, audit)
- Notifications center
- Global search (Cmd-K)
- Help / Docs
- Onboarding tour
- File upload
- Rich text editor
- Real-time notifications

### Phase 2: Technology Selection

Based on answers, select the BEST UI component library.
**ALWAYS research the latest version and docs before choosing.**

#### React Ecosystem (2026)

| Library | Best For | Style | Components |
|---------|---------|-------|------------|
| **shadcn/ui** | Modern SaaS, customizable | Tailwind | 50+ headless, copy-paste |
| **Ant Design** | Enterprise, data-heavy | CSS-in-JS | 70+ full-featured |
| **MUI (Material UI)** | Material Design, rapid dev | Emotion/CSS | 80+ components |
| **Mantine** | Modern, full-featured | CSS Modules | 100+ components + hooks |
| **Chakra UI** | Accessible, composable | Emotion | 60+ accessible |
| **NextUI** | Beautiful, modern | Tailwind | 40+ animated |
| **Radix UI** | Headless, accessible | Unstyled | 30+ primitives |
| **TremOR** | Dashboards, charts | Tailwind | Dashboard-focused |
| **Aceternity UI** | Animated, fancy effects | Tailwind + Framer | Animation-heavy |

**Additional React Libraries:**
- **React Router** or **TanStack Router** - Routing
- **TanStack Query (React Query)** - Server state management
- **Zustand** - Client state (simple) / **Redux Toolkit** (complex)
- **React Hook Form + Zod** - Forms + validation
- **TanStack Table** - Advanced data tables
- **Recharts** or **Nivo** - Charts/visualizations
- **Framer Motion** - Animations
- **React DnD** or **dnd-kit** - Drag and drop
- **TipTap** or **Plate** - Rich text editor
- **React Dropzone** - File upload
- **Sonner** or **React Hot Toast** - Toast notifications
- **cmdk** - Command palette (Cmd+K)
- **date-fns** or **dayjs** - Date formatting

#### Vue Ecosystem (2026)

| Library | Best For | Style | Components |
|---------|---------|-------|------------|
| **PrimeVue** | Enterprise, most complete | Theme system | 90+ components |
| **Vuetify** | Material Design, full-featured | Material | 80+ components |
| **Naive UI** | Modern, TypeScript-first | CSS-in-JS | 90+ components |
| **Element Plus** | Enterprise, data-heavy | CSS vars | 70+ components |
| **Quasar** | Cross-platform (web+mobile+desktop) | Material | 70+ components |
| **Headless UI** | Headless, accessible | Unstyled | 10+ primitives |
| **Anu** | Utility-first, DX-focused | UnoCSS | Newer, growing |

**Additional Vue Libraries:**
- **Vue Router** - Routing
- **Pinia** - State management
- **VueUse** - Composition utility hooks (200+)
- **VeeValidate + Zod** - Form validation
- **Vue Query (TanStack)** - Server state
- **Vue Chart.js** or **ECharts Vue** - Charts
- **Vue Draggable Plus** - Drag and drop
- **Tiptap** - Rich text editor

#### Angular Ecosystem (2026)

| Library | Best For | Style | Components |
|---------|---------|-------|------------|
| **Angular Material** | Material Design, official | Material | 35+ components |
| **PrimeNG** | Enterprise, most complete | Theme system | 90+ components |
| **NG-ZORRO (Ant Design)** | Enterprise, data-heavy | Ant Design | 70+ components |
| **Nebular** | Modern admin panels | Custom themes | 40+ components |
| **Taiga UI** | Modern, accessible | CSS vars | 130+ components |

#### Next.js Specific
- **shadcn/ui** (default recommendation) - Tailwind-based, copy-paste
- **NextUI** - Beautiful with Tailwind
- Server Components support is CRITICAL - check library compatibility
- **next-intl** - Internationalization
- **next-themes** - Dark mode

#### Nuxt Specific
- **Nuxt UI** - Official Nuxt component library (Tailwind-based)
- **PrimeVue** works great with Nuxt
- **nuxt-icon** - Icon system
- **@nuxtjs/color-mode** - Dark mode

### Phase 3: Recommended Combinations (Opinionated)

**For Admin Dashboard / SaaS (React):**
```
Framework: Next.js (App Router)
UI: shadcn/ui + Tailwind CSS
State: Zustand + TanStack Query
Forms: React Hook Form + Zod
Tables: TanStack Table
Charts: Recharts or Tremor
Icons: Lucide React
Animation: Framer Motion
Toast: Sonner
Command: cmdk
```

**For Admin Dashboard / SaaS (Vue):**
```
Framework: Nuxt 3
UI: PrimeVue (or Nuxt UI for simpler projects)
State: Pinia + Vue Query
Forms: VeeValidate + Zod
Utils: VueUse
Charts: ECharts Vue
Icons: Iconify
```

**For Enterprise Data-Heavy (React):**
```
Framework: Next.js or Vite + React
UI: Ant Design
State: Redux Toolkit + RTK Query
Tables: Ant Design Table (built-in) + AG Grid for complex
Charts: Ant Design Charts or ECharts
Forms: Ant Design Form (built-in)
```

**For Enterprise Data-Heavy (Vue):**
```
Framework: Nuxt 3 or Vite + Vue
UI: PrimeVue or Element Plus
State: Pinia
Tables: PrimeVue DataTable
Charts: ECharts
```

**For Beautiful Marketing / Landing:**
```
Framework: Next.js
UI: Tailwind CSS + Aceternity UI + Magic UI
Animation: Framer Motion
Fonts: next/font
Images: next/image with blur placeholder
```

**For Mobile App:**
```
React Native: React Native Paper + React Navigation + Zustand
Flutter: Material 3 + Riverpod + GoRouter
```

### Phase 4: Layout Generation

Generate the actual layout code based on type:

**Dashboard Layout:**
```
+------------------------------------------+
| Top Navbar (logo, search, user menu)      |
+--------+--------------------------------+
| Side   | Main Content Area              |
| bar    |                                |
| (nav)  | +----------------------------+ |
|        | | Breadcrumb                  | |
|        | +----------------------------+ |
|        | | Page Content               | |
|        | |                            | |
|        | | Cards / Tables / Charts    | |
|        | |                            | |
|        | +----------------------------+ |
+--------+--------------------------------+
```

**Landing Page Layout:**
```
+------------------------------------------+
| Navbar (logo, links, CTA button)          |
+------------------------------------------+
| Hero Section (headline, sub, CTA)         |
+------------------------------------------+
| Features Section (3-4 cards)              |
+------------------------------------------+
| Social Proof (logos, testimonials)         |
+------------------------------------------+
| Pricing Section                           |
+------------------------------------------+
| FAQ Section                               |
+------------------------------------------+
| Footer (links, social, newsletter)        |
+------------------------------------------+
```

### Phase 5: Implementation

**ALWAYS before writing frontend code:**
1. WebSearch "<chosen-library> latest docs <year>"
2. WebFetch the installation page
3. WebFetch the component API page for components you'll use
4. Extract exact import paths, prop names, CSS classes
5. THEN write code

**Implementation order:**
1. Install and configure UI library
2. Set up theme/design tokens (colors, fonts, spacing)
3. Build layout shell (sidebar, navbar, routing)
4. Build reusable components (if not using library's)
5. Build pages one by one
6. Add interactivity (forms, state, API calls)
7. Add polish (loading states, error states, animations, responsive)
8. Dark mode / RTL if needed

## Examples
```
/frontend design
/frontend design --framework vue
/frontend libraries react
/frontend layout dashboard
/frontend component data-table
```
