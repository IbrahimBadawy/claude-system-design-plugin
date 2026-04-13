---
name: web-apps
description: Web application development skill - triggered when building web apps (SPA, SSR, PWA, static sites). Guides framework selection, rendering strategy, deployment, and web-specific patterns. Covers React, Vue, Angular, Next.js, Nuxt, Svelte, Astro.
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, WebFetch, WebSearch, Agent
---

# Web Application Development Skill

## When This Skill Activates
- User mentions: web app, website, SPA, SSR, PWA, dashboard, portal, landing page
- During `/implement` when building for the web
- During `/frontend design` for web platforms

## Framework Selection Guide

### Ask First
1. What type? (SPA, SSR, Static, PWA, Hybrid)
2. SEO important? (Yes -> SSR/SSG, No -> SPA)
3. Real-time features? (Chat, live updates -> WebSocket/SSE)
4. Offline needed? (Yes -> PWA with service worker)

### Framework Decision Tree
```
Need SEO + React?        -> Next.js (App Router)
Need SEO + Vue?          -> Nuxt 3
Need SEO + minimal JS?   -> Astro
Pure SPA (no SEO)?       -> React + Vite or Vue + Vite
Enterprise Angular?      -> Angular 18+
Maximum performance?     -> Svelte/SvelteKit
Static marketing site?   -> Astro or Next.js (SSG)
```

### Rendering Strategies
| Strategy | When | Framework |
|----------|------|-----------|
| **CSR (Client)** | Dashboards, admin panels, behind auth | React/Vue + Vite |
| **SSR (Server)** | SEO-critical, dynamic content | Next.js, Nuxt, SvelteKit |
| **SSG (Static)** | Blogs, docs, marketing | Astro, Next.js export |
| **ISR (Incremental)** | E-commerce, frequently updated | Next.js ISR |
| **Streaming SSR** | Large pages, fast TTFB | Next.js App Router, Remix |

### Web-Specific Architecture
```
Client (Browser)
  -> CDN (static assets, cached pages)
  -> Edge Functions (auth, redirects, A/B tests)
  -> API Routes / BFF (backend logic)
    -> Database + Cache + Queue
```

### PWA Checklist
- [ ] Service Worker for offline caching
- [ ] Web App Manifest (icons, theme, display mode)
- [ ] HTTPS required
- [ ] Installable prompt
- [ ] Push notifications (optional)
- [ ] Background sync (optional)

### Web Performance
- Core Web Vitals: LCP < 2.5s, INP < 200ms, CLS < 0.1
- Code splitting by route (lazy loading)
- Image optimization: next/image, WebP/AVIF, lazy loading
- Font optimization: font-display: swap, preload
- Bundle analysis: keep initial JS < 200KB

### Web Security
- CSP (Content Security Policy) headers
- CORS configured restrictively
- XSS prevention (output encoding)
- CSRF tokens for forms
- HttpOnly + Secure + SameSite cookies
- Subresource Integrity (SRI) for CDN scripts

### Deployment Platforms
| Platform | Best For | Features |
|----------|---------|---------|
| Vercel | Next.js, frontend | Edge functions, preview deploys |
| Netlify | Static, Jamstack | Forms, identity, functions |
| Cloudflare Pages | Edge-first | Workers, KV storage, global |
| Railway | Full-stack | Databases included, simple |
| AWS Amplify | AWS ecosystem | Full AWS integration |
| Docker + VPS | Full control | Any cloud, self-managed |
