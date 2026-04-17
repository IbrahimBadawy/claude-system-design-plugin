# Modern Testing (2024-2026)

> Canonical sources: Kent C. Dodds (Testing Trophy), Testing Library, Playwright,
> Mock Service Worker (MSW), jest-axe, Pact.

---

## The Testing Trophy (Kent C. Dodds)

Replaces the outdated "Testing Pyramid." Order by ROI:

```
       ╔══════════╗
       ║   E2E    ║  few, critical journeys only (Playwright)
       ╠══════════╣
       ║          ║
       ║ INTEGRATION ║  biggest slice — components + APIs
       ║          ║
       ╠══════════╣
       ║   Unit   ║  pure functions, reducers, Zod schemas
       ╠══════════╣
       ║  Static  ║  TypeScript + ESLint — FREE coverage
       ╚══════════╝
```

Kent's rule: **"Write tests. Not too many. Mostly integration."**

---

## Core Principles

### 1. Test like a user

`@testing-library/react` queries in priority order:

1. `getByRole('button', { name: 'Save' })` — best; mirrors how screen readers + users find elements
2. `getByLabelText('Email')` — forms
3. `getByPlaceholderText` — only if no label
4. `getByText` — for content, not interactive elements
5. `getByDisplayValue` — for form values
6. `getByAltText` — images
7. `getByTitle` — last resort before test IDs
8. `getByTestId` — only when nothing else works

**Rule:** If a test breaks on refactor but user-visible behavior is unchanged, the test is
too implementation-coupled.

### 2. MSW for API mocking

Mock Service Worker = one set of handlers used in Vitest, Storybook, Playwright, AND dev
mode. Stops mocks from drifting from reality.

```ts
// features/orders/__tests__/handlers.ts
import { http, HttpResponse } from 'msw';

export const orderHandlers = [
  http.get('/api/orders', () => HttpResponse.json([...])),
  http.post('/api/orders', async ({ request }) => {
    const body = await request.json();
    return HttpResponse.json({ id: 'new-123', ...body }, { status: 201 });
  }),
];
```

Use same handlers across:
- Unit tests (Vitest)
- Component stories (Storybook)
- E2E tests (Playwright)
- Local dev mode (browser service worker)

### 3. Playwright for E2E

Playwright replaced Cypress for new projects (2023+). Best practices:

- **Locators** with `getByRole`, `getByLabel` (same philosophy as Testing Library)
- **Auto-waits** — no manual `waitFor` needed
- **Traces on retry** — `trace: 'on-first-retry'`
- **Parallel sharding in CI** — `--shard 1/4`, `--shard 2/4` etc.
- **Baseline screenshots generated in CI**, not locally (OS font differences)
- **Mask dynamic regions** (timestamps, avatars) in visual snapshots
- **`animations: 'disabled'`** for visual tests
- **One auth setup per project** using `storageState` — don't log in per test

### 4. Accessibility tests in CI

- `jest-axe` (Vitest/Jest) + `@axe-core/playwright` (E2E)
- **Zero-violation policy** for `critical` and `serious`
- **Caveat:** axe catches only ~30% of a11y issues → keyboard + screen reader manual checks stay mandatory

```tsx
import { axe, toHaveNoViolations } from 'jest-axe';
expect.extend(toHaveNoViolations);

test('no a11y violations', async () => {
  const { container } = render(<Dashboard />);
  expect(await axe(container)).toHaveNoViolations();
});
```

---

## Coverage: A Lagging Indicator

Coverage targets — aspirational, not the goal:

| Layer | Target | Notes |
|-------|--------|-------|
| Domain logic | 90% | Business rules, calculations |
| API routes | 80% | Handlers, middleware |
| UI components | 60-70% | Integration tests cover more |
| Utility functions | 100% | Pure, easy to test |

### Better leading indicators

- **Mutation score** (Stryker) — flips production code and checks if tests catch it. Way better than line coverage.
- **Flake rate** — what % of CI runs require retries? Flaky tests hide real bugs.

---

## Contract Tests Between Services

Pact with consumer-driven contracts:

```
frontend declares what it needs → pact file → backend verifies compliance
```

Removes "shared staging env" bottleneck. Every service deploys independently as long as
contracts pass.

Source: https://docs.pact.io/

---

## Component Testing: Choose ONE

- **Vitest + React Testing Library + jsdom** — fast, 90% of cases. Pick this first.
- **Playwright Component Testing** — real browser, slower, better for layout/focus/viewport-sensitive components

Don't run both — overlap wastes CI time.

---

## Visual Regression

| Approach | When |
|----------|------|
| **Playwright snapshots** | Small team, no Storybook, want free+in-repo |
| **Chromatic** (Storybook) | Design-system-heavy teams, branch-based review UI |
| **Percy** | Similar to Chromatic, different vendor |

---

## Regression Tests

Every bug fix adds a regression test FIRST, then the fix. Bug fixes without regression
tests are time bombs.

```
1. Reproduce bug in a failing test
2. Fix the code
3. Test passes
4. Ship
```

---

## Decision Rules

| If | Use |
|----|-----|
| Writing a new test | Prefer integration > unit |
| Test uses `getByTestId` | Refactor to `getByRole`/`getByLabelText` first |
| Mocking `fetch` manually | Replace with MSW handlers |
| Component renders user-visible UI | Vitest + RTL |
| Component is layout/focus/viewport-sensitive | Playwright Component Testing |
| Two teams integrate via HTTP API | Pact contracts |
| Design-system-heavy frontend | Chromatic (Storybook snapshots) |
| Small team, no Storybook yet | Playwright visual snapshots |
| Hit a bug in production | Failing regression test before fix |

---

## Testing Stack Checklist — auto-scaffold

- [ ] Static: TypeScript `strict: true` + ESLint + type-checked test files
- [ ] Unit: Vitest (or Jest) for pure functions
- [ ] Integration: React Testing Library + MSW for components + API
- [ ] E2E: Playwright for top 3-5 user journeys
- [ ] A11y: `jest-axe` + `@axe-core/playwright` in CI, zero critical/serious
- [ ] Contract: Pact for any API crossing team boundaries
- [ ] Visual: Playwright snapshots (or Chromatic if Storybook-heavy)
- [ ] Performance: Lighthouse CI with budgets
- [ ] Coverage: 80% line on domain + API routes, 60% overall
- [ ] Mutation: Stryker run nightly (not per-PR; too slow)
- [ ] Flake budget: alert if flake rate > 2% of CI runs

Sources:
- https://kentcdodds.com/blog/write-tests
- https://kentcdodds.com/blog/the-testing-trophy-and-testing-classifications
- https://testing-library.com/
- https://mswjs.io/
- https://playwright.dev/docs/best-practices
- https://github.com/NickColley/jest-axe
- https://docs.pact.io/
