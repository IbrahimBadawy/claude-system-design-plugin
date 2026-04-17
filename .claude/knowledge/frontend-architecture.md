# Modern Frontend Architecture (2024-2026)

> Canonical sources: Feature-Sliced Design, Bulletproof React, Next.js App Router,
> Astro Islands, Turborepo, Nx.

---

## Core Principles

1. **Feature-first beats type-first.** Organize `src/features/<name>/` with colocated `api/ components/ hooks/ stores/ types/ utils/`. Shared code lives outside, never inside a feature.
2. **Enforce a layered import direction.** Higher layers import from lower, never sideways. Use ESLint `import/no-restricted-paths` to enforce mechanically.
3. **Screaming architecture.** Top-level folder names shout the domain (billing, scheduling, patients), not the framework (components, hooks, utils).
4. **Absolute imports with `@/` alias.** Prevents `../../../../` hell.

---

## Feature-Sliced Design (FSD)

6 layers — higher imports lower, never sideways. Slices within a layer cannot import each other.

| Layer | Owns | Examples |
|-------|------|----------|
| **app** | Routing, providers, global styles, auth wrappers | `App.tsx`, root providers |
| **pages** | Route components (thin — compose widgets) | `DashboardPage.tsx` |
| **widgets** | Large composite blocks, feature-aware | `SidebarNav`, `ProductListWithFilters` |
| **features** | User actions / interactive behaviors | `add-to-cart`, `edit-profile`, `login-form` |
| **entities** | Business objects + their generic UI | `User`, `Product`, `Order` |
| **shared** | Reusable primitives, nothing domain-specific | `ui/`, `lib/`, `config/`, `api-client` |

```
src/
├── app/              # Entry, providers, routing
├── pages/            # Thin route components
├── widgets/          # Page regions composed of features
├── features/         # User actions (interactive)
│   └── add-to-cart/
│       ├── ui/
│       ├── model/
│       └── api/
├── entities/         # Business objects + generic UI
│   └── product/
└── shared/           # Reusable primitives
    ├── ui/
    ├── lib/
    └── api/
```

Source: https://feature-sliced.design/

---

## Bulletproof React (simpler alternative)

For smaller teams, when FSD feels too heavy:

```
src/
├── app/           # Providers, router, global
├── assets/
├── components/    # Shared UI
├── config/        # Env, constants
├── features/      # Feature modules
│   └── profile/
│       ├── api/
│       ├── components/
│       ├── hooks/
│       ├── routes/
│       ├── stores/
│       └── types/
├── hooks/         # Shared hooks
├── lib/           # Re-exported libs (axios instance, etc.)
├── providers/     # App-wide providers
├── routes/        # App-level routes
├── stores/        # Shared state
├── test/          # Test utilities
├── types/         # Shared types
└── utils/
```

Source: https://github.com/alan2207/bulletproof-react

---

## Next.js App Router — RSC Rules

1. **Server Components are the default**, not the exception
2. **Audit every `"use client"`** — if a component only renders data, remove it
3. **Push `"use client"` to leaves** — the Search input, not the whole Layout
4. **Pass Server Components into Client Components via `children`** to preserve server rendering inside client shells

```tsx
// ✓ Good: Server-rendered layout with client island
// page.tsx (server)
export default function Page() {
  return (
    <ClientShell>
      <ServerContent /> {/* Still server-rendered */}
    </ClientShell>
  );
}

// ✗ Bad: Marking whole tree as client
"use client"
export default function Page() {
  return <div>{/* entire tree client-rendered */}</div>;
}
```

### Environment Boundaries

Use `server-only` and `client-only` packages to make boundaries a compile-time error:

```ts
// lib/db.ts
import 'server-only';   // fails if imported from client

export const db = createClient(process.env.DATABASE_URL!);
```

Source: https://nextjs.org/docs/app/getting-started/server-and-client-components

---

## Rendering Strategy Decision Tree

| Use case | Framework | Rendering |
|----------|-----------|-----------|
| SaaS dashboard, auth, personalized | Next.js App Router | RSC + streaming SSR |
| Content/marketing/docs (>70% static) | Astro | Islands architecture |
| Content-heavy with some interactivity | Astro + React/Vue islands | SSG + hydrate islands |
| Resumability-first (low-end devices) | Qwik | Resumable SSR |
| Traditional SPA (internal admin tool) | Vite + React Router | CSR with prefetch |
| Partial personalization (e-commerce) | Next.js PPR | Static shell + dynamic holes |

---

## Monorepo Decision Matrix

| Size | Tool |
|------|------|
| ≤ 5 packages | pnpm workspaces alone |
| 5-50 packages | **pnpm + Turborepo** (great caching, Vercel-native) |
| > 50 packages or large teams with generators | **Nx** |
| Enterprise scale (Google/Meta) | Bazel |

### Turborepo standard layout

```
apps/
  web/             # Next.js app
  mobile/          # React Native
  docs/            # Astro docs
packages/
  ui/              # Shared components (shadcn-like)
  config/          # Shared tsconfig, eslint, tailwind
  api-client/      # Shared API SDK
  types/           # Shared TS types
  utils/           # Shared utils
turbo.json         # Pipeline + caching
pnpm-workspace.yaml
```

Source: https://turborepo.dev/docs/crafting-your-repository/structuring-a-repository

---

## ESLint Boundary Enforcement

```json
// .eslintrc.json
{
  "rules": {
    "import/no-restricted-paths": ["error", {
      "zones": [
        { "target": "./src/entities/**", "from": "./src/features/**" },
        { "target": "./src/shared/**", "from": "./src/features/**" },
        { "target": "./src/shared/**", "from": "./src/entities/**" },
        { "target": "./src/features/a/**", "from": "./src/features/b/**" }
      ]
    }]
  }
}
```

---

## Architecture Decision Rules

| If | Use |
|----|-----|
| New SaaS, team ≥ 3, interactivity everywhere | Next.js App Router + FSD-lite |
| Content site, blog, marketing, docs | Astro + Islands |
| Big frontend, solo/small team, shipping fast | Bulletproof React (Vite + React Router) |
| Multiple apps sharing design system + API | pnpm workspaces + Turborepo |
| Server-heavy dashboard, rare client interactivity | Next.js RSC, `"use client"` only on leaves |
| Every 2nd file starts with `"use client"` | Your tree is inverted — refactor |

---

## Checklist — enforce at scaffold time

- [ ] Feature-first folder structure (no flat `components/` dump)
- [ ] Layered import boundaries via ESLint `import/no-restricted-paths`
- [ ] Absolute imports with `@/` alias
- [ ] Screaming architecture (top-level folders name the domain)
- [ ] `server-only` / `client-only` packages used at boundaries
- [ ] `"use client"` pushed to leaves, not applied to whole trees
- [ ] Providers hoisted, NOT one god-provider (split Theme, Query, Auth)
- [ ] Context passes `{children}`, not wrapping everything
- [ ] Monorepo-ready layout from day 1 (even for single apps: `apps/*` + `packages/*`)
- [ ] Shared config packages for tsconfig, eslint, tailwind, prettier

Sources:
- https://feature-sliced.design/
- https://github.com/alan2207/bulletproof-react
- https://nextjs.org/docs/app/getting-started/project-structure
- https://docs.astro.build/en/concepts/islands/
- https://turborepo.dev/docs
