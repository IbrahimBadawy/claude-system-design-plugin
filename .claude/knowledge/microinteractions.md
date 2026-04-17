# Microinteractions, Motion, and Polish

> What separates "works" from "feels professional". Canonical sources:
> Material Motion, Apple HIG, Luke Wroblewski on skeletons, TanStack Query
> optimistic patterns, Linear/GitHub/VSCode command palette conventions.

---

## Motion Principles

### Material Motion (Google)

- **Duration scales with distance/size** — small 100-200ms, medium 200-300ms, large 300-500ms
- **Standard easing curves:**
  - `standard: cubic-bezier(0.2, 0, 0, 1)` — most UI transitions
  - `emphasized: cubic-bezier(0.3, 0, 0, 1)` — attention-grabbing
  - `decelerate: cubic-bezier(0, 0, 0, 1)` — elements entering the screen
  - `accelerate: cubic-bezier(0.3, 0, 1, 1)` — elements leaving
- **Shared element transitions** — maintain visual continuity between states
- **Choreography** — sequential, not simultaneous. Leading elements move first

### Apple HIG Motion

- **Purposeful motion** — motion clarifies relationships, not decoration
- **Respect reduce-motion** — `prefers-reduced-motion: reduce` MUST disable non-essential animations
- **Consistency with platform** — spring physics, not cubic-bezier, for gestures on iOS

### Universal Rules

1. Never animate without a reason (focus shift, state change, revealing content)
2. Duration: 150-300ms for most UI; 500ms max for large transitions
3. All animations respect `prefers-reduced-motion`
4. Use `transform` and `opacity` ONLY for 60fps performance. Never animate `width`, `height`, `top`, `left`
5. Every interactive element has hover + active + focus state, animated

Sources:
- https://m3.material.io/styles/motion/overview
- https://developer.apple.com/design/human-interface-guidelines/motion

---

## Skeleton Screens vs Spinners

| Loading type | Pattern | When |
|-------------|---------|------|
| **Skeleton screen** | Gray shapes matching final layout | Content loading > 1s |
| **Spinner** | Indeterminate loading indicator | Action < 1s (form submit, button click) |
| **Progress bar** | Deterministic fill | Operation > 2s (upload, import) |
| **Optimistic UI** | Immediate UI update, reconcile on response | User-owned actions (like, toggle, inline edit) |

**Rule:** Any page/widget taking > 300ms to render data MUST show a skeleton that
matches the final layout.

Why skeletons beat spinners: perceived wait is shorter because the user sees structure
and can anticipate content placement (Luke Wroblewski's research, 2013; confirmed
by NN/g, 2023).

Source: https://www.nngroup.com/articles/progress-indicators/

---

## Optimistic UI

The pattern popularized by Linear, Superhuman, and TanStack Query:

```typescript
// 1. User clicks "like"
// 2. Immediately update UI (local state)
// 3. Fire server request
// 4. On success: commit (no UI change)
// 5. On failure: revert UI + show subtle toast

const mutation = useMutation({
  mutationFn: (id) => api.likePost(id),
  onMutate: async (id) => {
    await queryClient.cancelQueries({ queryKey: ['post', id] });
    const previous = queryClient.getQueryData(['post', id]);
    queryClient.setQueryData(['post', id], (old) => ({
      ...old,
      liked: true,
      likeCount: old.likeCount + 1
    }));
    return { previous };
  },
  onError: (err, id, ctx) => {
    queryClient.setQueryData(['post', id], ctx.previous);
    toast.error("Couldn't save. Retry?");
  },
  onSettled: (_, __, id) => {
    queryClient.invalidateQueries({ queryKey: ['post', id] });
  },
});
```

### When to use

- Low-risk, high-frequency actions (toggles, sorts, list reorders, drafts)

### When NOT to use

- Payments
- Deletes
- Permission changes
- Any irreversible action

Source: https://tanstack.com/query/v5/docs/react/guides/optimistic-updates

---

## Command Palette (Cmd-K)

Standard for power-user SaaS since 2023. Canonical pattern (Linear, GitHub, VSCode):

| Element | Standard |
|---------|---------|
| **Trigger** | `Cmd/Ctrl + K` (universal) or `/` for search-first apps |
| **Fuzzy search** | Match anywhere in label, rank by frecency (frequency + recency) |
| **Grouping** | Sections: Recent, Actions, Navigation, Settings, Help |
| **Keyboard-only** | Arrow keys navigate, Enter executes, Esc closes |
| **Context-aware** | Show actions relevant to current page/selection first |
| **Chainable** | `>` or `@` prefixes to scope (GitHub: `>` for commands, `#` for issues) |
| **Empty state** | "Type to search" + list top 5-8 common actions |

### Libraries

- **cmdk** (by shadcn) — React, headless, composable
- **kbar** — React, also mature
- Roll your own using Radix Dialog + Combobox for full control

Sources:
- Linear command menu: https://linear.app/docs/keyboard-shortcuts
- cmdk: https://cmdk.paco.me/

---

## Empty States

Every list/table/dashboard MUST distinguish FIVE states, not just "loading + loaded":

### 1. Loading
Skeleton matching final layout.

### 2. Empty (first-time)
- Clear headline ("No projects yet")
- One-sentence explanation ("Projects group related tasks")
- Primary CTA button ("Create project")
- Optional: illustration, video link, sample data
- Keyboard shortcut shown if relevant

### 3. Empty (filtered)
Different from first-time empty.
- "No results for 'foo'"
- Clear-filter action
- Optionally: suggest broader search terms

### 4. Error
- What went wrong (plain language)
- Retry button
- Support link
- Include request ID for debugging (in small muted text)

### 5. Loaded
The actual content.

**Rule:** No data-bound component ships with only 2 states. Run `/ux-kit audit` to catch
missing states.

Source: https://emptystat.es/

---

## Hover, Focus, and Active States

Every interactive element has at least 3 visible states beyond default:

| State | Visual treatment |
|-------|------------------|
| Default | Base styling |
| Hover | Lighten/darken bg, cursor pointer, optional elevation |
| Focus | **2px ring, 2px offset**, brand color — NEVER use `outline: none` |
| Active (pressed) | Slight scale down (0.97-0.98) or darker bg |
| Disabled | 50% opacity, `cursor: not-allowed`, no hover effect |
| Loading | Spinner in button, disabled state |
| Success (transient) | Green checkmark for 2s, then back to default |
| Error (persistent) | Red ring, inline error message below |

### Focus Ring Pattern (Accessibility Critical)

```css
.button:focus-visible {
  outline: 2px solid hsl(var(--color-primary));
  outline-offset: 2px;
}
/* `:focus-visible` shows ring only on keyboard focus, not mouse click */
```

Never remove the focus ring. Design a custom one that matches the brand.

---

## Transitions Between States

### Page transitions

- View Transitions API (Chrome 111+, Safari 18, Firefox 137+)
- Next.js App Router: built-in loading UI via `loading.tsx`
- Fade + slight slide (8-12px) = 200ms ease-out

### Modal / dialog open

- Backdrop fade-in: 150ms ease-out
- Dialog scale-up from 0.95 + fade-in: 200ms ease-out
- Focus trap inside, restore focus on close

### Toast appear

- Slide-up from bottom-right (or top for errors): 250ms ease-out
- Stack: offset 8px per toast, max 3 visible, queue the rest
- Auto-dismiss: 4s for info, 6s for success, 8s for error, persistent for critical
- Swipe to dismiss on touch

### List item enter/exit

- Stagger 30-50ms between items for visual flow
- Reduce stagger (or skip) if list > 20 items

---

## Micro-Interactions That Matter

### Button press
- Scale: 0.97 on active, 1.0 on release, 150ms spring
- Haptic feedback on mobile (navigator.vibrate if available)

### Toggle switch
- Thumb slides + bg color changes simultaneously: 200ms ease-out
- Check for reduced motion preference

### Checkbox
- Check mark draws in (stroke-dasharray animation): 200ms
- Or simple fade-in if reduced-motion

### Tab switch
- Underline slides to new tab: 200ms ease-out
- Content cross-fades: 150ms

### Accordion expand
- Height auto-to-actual with overflow hidden: 250ms ease-in-out
- Chevron rotates 180°

### Search input focus
- Outline + subtle background tint: 150ms
- Cursor blinks at expected rate

### Heart/like
- Scale pulse: 1 → 1.2 → 1 over 300ms spring
- Color fills from center

### Pull-to-refresh (mobile/PWA)
- Show progress indicator as user pulls
- Snap back if released before threshold
- Bounce + spinner when triggered

---

## When NOT to Animate

- **Data tables** — users are scanning; animation distracts
- **Forms with rapid input** — typing, tab-switching
- **Keyboard-heavy interactions** — power users want immediate feedback
- **Loading states > 2s** — progress bar beats animated spinner
- **When `prefers-reduced-motion: reduce`** — disable all non-essential animations

---

## Performance Budget for Motion

- All animations use `transform` + `opacity` only (60fps)
- No animating `width`, `height`, `top`, `left`, `margin`, `padding`
- `will-change: transform` sparingly (GPU memory cost)
- Debounce rapid-fire animations (e.g., list reorder)

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

---

## Polish Checklist (30 Items)

Run on every screen before shipping:

### Interaction feedback
1. Every button has hover + active + focus visible states
2. Focus ring is 2px + 2px offset + brand color
3. Destructive actions show confirmation with undo
4. Success feedback appears within 1s of action
5. Error feedback is inline and specific
6. Loading state on every button during async action
7. Disabled state is visually distinct (50% opacity + not-allowed cursor)

### Transitions
8. Page transitions 150-300ms ease-out
9. Modal fades in, scales from 0.95 to 1
10. Toasts slide in from corner, stack, auto-dismiss
11. Accordion and dropdown expand smoothly
12. Tab indicator slides between tabs
13. List items stagger 30-50ms when > 5 appear at once

### Loading
14. Skeleton matches final layout (not generic spinner)
15. Progress bar for operations > 2s
16. Optimistic UI for likes/toggles/reorders
17. Server state rolls back on failure with toast
18. Spinner only for actions < 1s

### Empty/error states
19. Empty state has illustration + headline + CTA
20. Filtered empty state distinguishes from first-time empty
21. Error state has retry + support link + request ID
22. Offline state (PWA) shows cached data with indicator

### Keyboard
23. All actions reachable via Tab + Enter
24. Cmd-K command palette present
25. `?` shows keyboard shortcut cheatsheet
26. Esc closes modals/dropdowns
27. Arrow keys navigate lists and menus

### Motion quality
28. All animations respect `prefers-reduced-motion`
29. Use only `transform` and `opacity` (60fps)
30. No animation longer than 500ms

Run via `/ux-kit audit` — missing items block the milestone.
