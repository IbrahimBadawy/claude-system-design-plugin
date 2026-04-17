# State Management & Data Fetching (2026)

> Canonical sources: TanStack Query v5, Zustand, Jotai, React Hook Form + Zod,
> nuqs (URL state), Valibot.

---

## The Core Rule

**Server state and client state are separate disciplines.**

- Never put fetched data in Zustand/Redux
- Never put UI toggles in TanStack Query
- Crossing the streams causes 80% of "why is my state weird" bugs

---

## Default Stack

| State kind | Tool |
|-----------|------|
| **Server state** (API responses) | TanStack Query |
| **Global client state** | Zustand (or Jotai for fine-grained atomic) |
| **Form state** | React Hook Form + Zod |
| **URL state** (filters, sort, page, tab) | nuqs |
| **Local component state** | useState / useReducer |
| **DI (theme, auth user, i18n, feature flags)** | Context |

---

## Server State: TanStack Query v5

### Query keys as hierarchy

Design keys as `[entity, scope, filters]` from day one:

```ts
['todos']                                    // all todos queries
['todos', 'list']                             // all list queries
['todos', 'list', { status: 'open' }]         // this filter's list
['todos', 'detail', id]                       // one todo's detail
```

`queryClient.invalidateQueries({ queryKey: ['todos'] })` invalidates every `['todos', ...]`.

### Optimistic mutation pattern

```ts
const mutation = useMutation({
  mutationFn: (id) => api.likePost(id),

  // 1. Before mutation: snapshot + optimistic update
  onMutate: async (id) => {
    await queryClient.cancelQueries({ queryKey: ['post', id] });
    const previous = queryClient.getQueryData(['post', id]);
    queryClient.setQueryData(['post', id], (old) => ({
      ...old, liked: true, likeCount: old.likeCount + 1,
    }));
    return { previous };
  },

  // 2. On failure: rollback
  onError: (err, id, ctx) => {
    queryClient.setQueryData(['post', id], ctx.previous);
    toast.error("Couldn't save. Retry?");
  },

  // 3. Always: refetch to sync truth
  onSettled: (_, __, id) => {
    queryClient.invalidateQueries({ queryKey: ['post', id] });
  },
});
```

### Stale-while-revalidate defaults

- `staleTime: 0` (default) → fresh data always, minimal staleness
- `staleTime: 5 * 60_000` for data that rarely changes (user profile, feature flags)
- `gcTime: 5 * 60_000` (default, was `cacheTime`)

### v5 simplified optimistic UI

```tsx
// Newer v5 pattern — no cache manipulation needed
const { mutate, variables, isPending } = useMutation({ ... });

return (
  <List>
    {todos.map(t => <Item key={t.id} {...t} />)}
    {isPending && <Item {...variables} dimmed />} {/* optimistic */}
  </List>
);
```

Source: https://tanstack.com/query/v5/docs

---

## Client State: Zustand

### Rules

1. **One store per domain** (auth, cart, ui) — not one god-store
2. **Never put server responses in it** — that's TanStack Query's job
3. **Use `persist` middleware sparingly** — only for user preferences
4. **Selectors prevent unnecessary re-renders**

```ts
// stores/cart.ts
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

type CartStore = {
  items: Item[];
  addItem: (item: Item) => void;
  clear: () => void;
};

export const useCart = create<CartStore>()(
  persist(
    (set) => ({
      items: [],
      addItem: (item) => set((s) => ({ items: [...s.items, item] })),
      clear: () => set({ items: [] }),
    }),
    { name: 'cart-storage' }
  )
);

// Usage — pick only what you need
const itemCount = useCart((s) => s.items.length);
```

Source: https://zustand.docs.pmnd.rs/

---

## Context: For DI Only

Use Context for:
- Theme
- Locale / i18n
- Auth user object (rarely changes)
- Feature flags

**DO NOT** use Context for frequently-changing state — every consumer re-renders on any change.

```tsx
// ✓ Good: DI pattern
const ThemeContext = createContext<Theme>('light');

// ✗ Bad: Mutable state that changes often
const CartContext = createContext<Cart>({ items: [] });  // use Zustand instead
```

---

## Forms: React Hook Form + Zod

```tsx
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';

const schema = z.object({
  email: z.string().email('Must be a valid email'),
  password: z.string().min(8, 'At least 8 characters'),
});

type FormData = z.infer<typeof schema>;

function LoginForm() {
  const { register, handleSubmit, formState: { errors, isSubmitting } } = useForm<FormData>({
    resolver: zodResolver(schema),
    mode: 'onBlur',   // validate on blur (best UX)
  });

  const onSubmit = async (data: FormData) => {
    await login(data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('email')} aria-invalid={!!errors.email} />
      {errors.email && <span role="alert">{errors.email.message}</span>}

      <input type="password" {...register('password')} />
      {errors.password && <span role="alert">{errors.password.message}</span>}

      <button disabled={isSubmitting}>{isSubmitting ? 'Signing in…' : 'Sign in'}</button>
    </form>
  );
}
```

### Why this works

- **Uncontrolled inputs** → minimal re-renders (only errors re-render)
- **Zod schema → TS types → runtime validation** in one place
- **`mode: 'onBlur'`** → best UX (not onChange = too noisy, not onSubmit = too late)
- **`formState.isSubmitting`** → easy disabled state

### Valibot alternative

Valibot is tree-shakable (~1KB min), Zod is not (though Zod 4 is improving). Choose Valibot
when bundle size < 15KB matters.

Source: https://react-hook-form.com/get-started#SchemaValidation

---

## URL as State: nuqs

Anything shareable, bookmarkable, or back-button-traversable belongs in the URL:
filters, sort, pagination, tab, selected item.

```tsx
import { useQueryState, parseAsInteger, parseAsStringEnum } from 'nuqs';

function OrdersPage() {
  const [page, setPage] = useQueryState('page', parseAsInteger.withDefault(1));
  const [status, setStatus] = useQueryState('status',
    parseAsStringEnum(['open', 'closed', 'all']).withDefault('all')
  );

  return (
    <>
      <Select value={status} onChange={setStatus}>...</Select>
      <Pagination page={page} onChange={setPage} />
    </>
  );
}
```

- 6KB, works across Next.js, Remix, TanStack Router
- Type-safe parsers for number, date, enum, JSON
- Shallow route updates (no full navigation)

Source: https://nuqs.dev

---

## Real-Time Communication

| Tool | When |
|------|------|
| **Server-Sent Events (SSE)** | Default — one-way server→client, HTTP-native, auto-reconnect |
| **WebSockets** | Bidirectional at scale (chat, collaborative editing) |
| **tRPC subscriptions** | Both ends TypeScript + same team |
| **GraphQL subscriptions** | Already using GraphQL + complex subscription data |

### SSE Example

```ts
// Client
const eventSource = new EventSource('/api/events');
eventSource.addEventListener('order-update', (e) => {
  const order = JSON.parse(e.data);
  queryClient.setQueryData(['order', order.id], order);
});
```

---

## Decision Rules

| State kind | Where |
|-----------|-------|
| API response (products, user profile) | TanStack Query |
| UI toggles (modal open, sidebar collapsed) | Zustand (or `useState` if scoped) |
| Theme, locale, auth user, feature flags | Context (DI) |
| Form field values during edit | React Hook Form |
| Filters / sort / page / tab | URL (`nuqs`) |
| Derived computed values | `useMemo`, or Jotai atoms |
| Cross-tab sync | Zustand `persist` + storage listener, or BroadcastChannel |
| Real-time presence | SSE first, WebSocket if bidirectional |

---

## State Checklist — enforce at scaffold

- [ ] TanStack Query for server state, Zustand for client state, no mixing
- [ ] Query keys as `[entity, scope, filters]` hierarchy
- [ ] Optimistic mutations for single-user actions (like, favorite, reorder)
- [ ] Rollback on failure + toast
- [ ] Context for DI only (theme, auth user, i18n, flags) — never for mutable state that changes frequently
- [ ] Forms = React Hook Form + Zod resolver, `mode: 'onBlur'`
- [ ] Filters/sort/page/tab in URL via `nuqs`
- [ ] `server-only` / `client-only` packages at boundaries
- [ ] Error boundaries + Suspense at every async boundary
- [ ] SSE default for real-time, WebSocket only when bidirectional

Sources:
- https://tanstack.com/query/v5/docs
- https://zustand.docs.pmnd.rs/
- https://jotai.org/
- https://react-hook-form.com/get-started
- https://nuqs.dev
