# Performance Budget & Core Web Vitals (2026)

> Canonical sources: web.dev Core Web Vitals, Next.js Partial Prerendering, MDN Web Performance,
> DebugBear, Chrome DevRel blog.

---

## The Targets (p75 of real users)

| Metric | Good | Target | What it measures |
|--------|------|--------|-----------------|
| **LCP** (Largest Contentful Paint) | < 2.5s | < 1.8s (stretch) | When the hero content appears |
| **INP** (Interaction to Next Paint) | < 200ms | < 100ms | Responsiveness across ALL interactions (replaced FID in March 2024) |
| **CLS** (Cumulative Layout Shift) | < 0.1 | < 0.05 | Visual stability during load |
| **TTFB** (Time to First Byte) | < 800ms | < 400ms | Server response speed |
| **FCP** (First Contentful Paint) | < 1.8s | < 1s | First pixel painted |

All three (LCP, INP, CLS) must pass for a "good" Core Web Vitals assessment.

Source: https://web.dev/articles/vitals

---

## LCP Optimization

LCP is usually caused by image delivery and server response.

### Images

```html
<!-- Good: modern formats, explicit dimensions, priority hint -->
<img
  src="/hero.jpg"
  srcset="/hero-400.avif 400w, /hero-800.avif 800w, /hero-1200.avif 1200w"
  sizes="(max-width: 640px) 400px, 1200px"
  width="1200"
  height="630"
  fetchpriority="high"
  decoding="async"
  alt="Product hero"
/>
```

Rules:
- **Serve AVIF first**, WebP fallback, JPEG safety net (AVIF ~94% browser support 2026)
- **Always set `width` and `height`** to prevent CLS
- **`loading="lazy"`** for below-the-fold; NEVER for the LCP candidate
- **`fetchpriority="high"`** on the LCP image
- Use framework components: Next.js `<Image>`, Astro `<Image>`, Nuxt `<NuxtImg>`, Angular `NgOptimizedImage`

### Server response

- `<Cache-Control: public, max-age=31536000, immutable>` for static assets
- Edge caching via CDN (Vercel, Cloudflare)
- Stream SSR to begin rendering before all data is ready
- Next.js Partial Prerendering (PPR): static shell + dynamic holes in same HTTP response

---

## INP Optimization

INP measures every interaction, not just the first. Every input handler > 50ms is suspect.

### Techniques

1. **Break up long tasks** — use `scheduler.yield()`:
   ```js
   async function processChunks(items) {
     for (let i = 0; i < items.length; i++) {
       await processItem(items[i]);
       if (i % 50 === 0) await scheduler.yield(); // yield to browser
     }
   }
   ```
2. **Debounce expensive handlers** — search input, resize, scroll
3. **Move heavy work to Web Workers** — JSON parsing, sorting, filtering large arrays
4. **Use `requestIdleCallback`** for non-critical background work
5. **`useDeferredValue` (React)** for expensive derived state
6. **Virtualize long lists** (TanStack Virtual, react-window) for > 100 items

### Measure

```js
// In production
import { onINP } from 'web-vitals';
onINP(({ value, id }) => analytics.track('INP', { value, id }));
```

---

## CLS Optimization

CLS is usually fonts + ads + unsized images.

### Fonts

```css
@font-face {
  font-family: 'Inter';
  src: url('/fonts/inter.woff2') format('woff2');
  font-display: optional;   /* zero CLS, safe fallback */
  /* or: font-display: swap + size-adjust overrides */
}
```

- **`font-display: optional`** → zero CLS (font loaded for 100ms, else fallback forever that session)
- **`font-display: swap`** with `size-adjust` metric overrides → faster LCP, minor CLS
- **Self-host, always.** Cache partitioning killed the "shared CDN cache" benefit of Google Fonts. Self-hosted WOFF2 beats Google Fonts CDN by ~180ms median LCP.

### Size-adjust fallback pattern

```css
@font-face {
  font-family: 'InterFallback';
  src: local('Arial');
  size-adjust: 107%;         /* match Inter's x-height */
  ascent-override: 90%;
  descent-override: 22%;
  line-gap-override: 0%;
}

body { font-family: 'Inter', 'InterFallback', sans-serif; }
```

### Other CLS Causes

- **Reserve space for async content** with `aspect-ratio` CSS or width/height on `<img>`/`<iframe>`
- **Never inject elements above existing content** (banners, cookie notices) — push down instead of shift
- **Set explicit dimensions** on ads, embeds, videos

---

## Modern Techniques (2024-2026)

### React Server Components (Next.js App Router)

- Cuts bundles 30-70% for data-display-heavy pages
- Data fetched on server, no API round trip from client
- Streaming SSR + Suspense → users see shell immediately, dynamic chunks stream in

### Partial Prerendering (PPR) — Next.js 15+

- Static shell built at build time
- Dynamic holes rendered per-request
- Same HTTP response → best of static + dynamic

```tsx
// app/dashboard/page.tsx
import { Suspense } from 'react';

export const experimental_ppr = true;

export default function Dashboard() {
  return (
    <>
      <StaticHeader />                    {/* pre-rendered */}
      <Suspense fallback={<Skeleton />}>
        <DynamicUserData />               {/* rendered per request */}
      </Suspense>
    </>
  );
}
```

### View Transitions API (Chrome 111+, Safari 18, Firefox 137+)

Smooth page transitions without a lib:

```js
document.startViewTransition(() => {
  updateDOM();
});
```

Baseline "newly available" as of 2025.

---

## Resource Hints (be surgical)

```html
<!-- Up to 4 critical third-party origins -->
<link rel="preconnect" href="https://api.example.com" />
<link rel="preconnect" href="https://cdn.example.com" crossorigin />

<!-- ONLY above-the-fold font + hero image -->
<link rel="preload" href="/fonts/inter.woff2" as="font" type="font/woff2" crossorigin />
<link rel="preload" href="/hero.avif" as="image" fetchpriority="high" />

<!-- Likely next navigations -->
<link rel="prefetch" href="/dashboard" />

<!-- Non-obvious JS module graph roots -->
<link rel="modulepreload" href="/chunks/critical.js" />
```

**Over-preloading slowed one real site by 0.9s.** Measure before adding.

Source: https://web.dev/learn/performance/resource-hints

---

## Bundle Budget in CI

```js
// next.config.js / vite.config.js — fail build on regression
performance: {
  maxAssetSize: 200 * 1024,    // 200KB per chunk
  maxEntrypointSize: 250 * 1024,
  hints: 'error',
}
```

Weekly bundle analysis:
- **Vite:** `rollup-plugin-visualizer` or `vite-bundle-analyzer`
- **Webpack:** `webpack-bundle-analyzer`
- **Cross-framework:** `bundle-stats` for build-over-build comparison

---

## Real-User Measurement

Lab tests (Lighthouse CI) lie about INP — no interaction in a lab run. Use TBT (Total
Blocking Time) as lab proxy.

Ship `web-vitals` library to production analytics:

```ts
import { onLCP, onINP, onCLS } from 'web-vitals';

function sendToAnalytics({ name, value, id }) {
  fetch('/vitals', {
    method: 'POST',
    body: JSON.stringify({ name, value, id }),
    keepalive: true,
  });
}

onLCP(sendToAnalytics);
onINP(sendToAnalytics);
onCLS(sendToAnalytics);
```

---

## Decision Rules

| Symptom | Action |
|---------|--------|
| LCP > 2.5s | Check: render-blocking CSS, slow TTFB, large/un-optimized hero image, 3rd-party blocking |
| INP > 200ms | Break up long tasks, debounce, move JSON parse/sort off main thread |
| CLS > 0.1 | Set image/iframe dimensions, reserve ad slots, `font-display: optional` |
| Hero image | Preload + `fetchpriority="high"`, no lazy |
| Below-fold image | `loading="lazy"` + `decoding="async"` + srcset |
| Rendering list of > 100 items | Virtualize (TanStack Virtual) |
| Page public + personalized | Next.js PPR — static shell + streamed dynamic |
| Cross-origin API called on every page | `preconnect` |

---

## Performance Checklist — auto-scaffold

- [ ] Framework `<Image>` component everywhere (AVIF+WebP, explicit dimensions)
- [ ] Self-hosted WOFF2 fonts, `font-display: optional` OR `swap` + size-adjust
- [ ] One font preloaded max (the above-the-fold one)
- [ ] `preconnect` to API origin + CDN
- [ ] Bundle budget in CI — fail on regression
- [ ] `web-vitals` shipped to analytics in production
- [ ] Lighthouse CI thresholds (LCP < 2.5s, CLS < 0.1, TBT < 200ms)
- [ ] Server Components where available (Next.js/Nuxt)
- [ ] Streaming SSR + Suspense boundaries
- [ ] Virtualize lists > 100 items
- [ ] View Transitions API for route changes (progressive enhancement)
- [ ] No layout-shifting elements injected above content
- [ ] `scheduler.yield()` in long tasks
- [ ] Web Workers for heavy sync work
- [ ] `useDeferredValue` for expensive derived state

Sources:
- https://web.dev/articles/vitals
- https://web.dev/learn/performance/resource-hints
- https://nextjs.org/docs/app/getting-started/partial-prerendering
- https://developer.chrome.com/blog/view-transitions-in-2025
- https://www.debugbear.com/blog/website-font-performance
