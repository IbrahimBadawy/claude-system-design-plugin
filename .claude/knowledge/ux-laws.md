# UX Laws & Principles

> Canonical knowledge for every UI decision. Source: Jon Yablonski's *Laws of UX*,
> Don Norman's *The Design of Everyday Things*, Steve Krug's *Don't Make Me Think*,
> Adam Wathan & Steve Schoger's *Refactoring UI*.

Use this file as an authoritative checklist when:
- Designing a new screen (`/frontend design`, `/pages`)
- Reviewing UX (`/ux-kit audit`)
- Writing UX copy or choosing interactions

---

## 15 Laws of UX (Jon Yablonski) — Most Impactful

| # | Law | Rule to Enforce |
|---|-----|----------------|
| 1 | **Hick's Law** | Choices increase decision time logarithmically. Limit nav items to 5-7; paginate long lists; progressive disclosure for forms >7 fields. |
| 2 | **Fitts's Law** | Target time ∝ distance / size. Primary CTAs ≥ 44×44px touch / 32×32px desktop. Place frequent actions near the user's current focus. |
| 3 | **Miller's Law** | Short-term memory ~7±2 items. Chunk phone numbers; group form fields; section headers every 5-7 items. |
| 4 | **Jakob's Law** | Users spend most time on other sites. Use conventional patterns: logo top-left, cart top-right, search visible, footer bottom. Don't reinvent standard affordances. |
| 5 | **Doherty Threshold** | Productivity soars when response time < 400ms. Every interaction MUST feel < 400ms — optimistic UI, skeletons, or progress for anything longer. |
| 6 | **Peak-End Rule** | People judge experiences by the peak emotion and the end. Invest disproportionate polish in onboarding completion, checkout confirmation, and error recovery. |
| 7 | **Aesthetic-Usability Effect** | Users perceive aesthetically pleasing designs as more usable. Visual polish is NOT cosmetic; it affects trust and perceived functionality. |
| 8 | **Von Restorff Effect** | Isolated items are remembered. Use ONE primary CTA per view; make it visually distinct (color, size, contrast). |
| 9 | **Serial Position Effect** | First and last items are remembered best. Place critical actions at the start or end of lists/nav. |
| 10 | **Goal-Gradient Effect** | Motivation increases near goal. Show progress bars with clear end state; "2 of 3 steps" beats "step 2". |
| 11 | **Zeigarnik Effect** | People remember incomplete tasks. Use checklists with persistent state (onboarding checklists, setup progress). |
| 12 | **Tesler's Law** | Every system has irreducible complexity — someone absorbs it. Push complexity into the app, not onto the user. |
| 13 | **Law of Proximity** | Related items should be grouped spatially. Use 2-3 spacing scales: tight (related), medium (section), wide (major division). |
| 14 | **Law of Common Region** | Elements in a defined region are perceived as related. Use cards, bordered panels, or background fills to group. |
| 15 | **Postel's Law** | Be liberal in what you accept, conservative in what you produce. Parse `Jan 5, 2026`, `1/5/26`, `2026-01-05` equally; display ONE canonical format. |

**Source:** https://lawsofux.com/

---

## Don Norman's 6 Fundamental Principles

Enforce on every interactive component:

1. **Affordances** — The component's visual properties suggest its use. A button *looks* pressable (raised, shadow, hover state).
2. **Signifiers** — Explicit cues that communicate affordance. Icons + labels > icons alone. Underlined blue text means "link."
3. **Feedback** — Every action has immediate visible response within 100ms (press state) + outcome feedback within 1s (toast, data update).
4. **Mapping** — Control placement mirrors effect. Volume up = up arrow. Delete button near the item, not in a remote toolbar.
5. **Constraints** — Disable/hide impossible actions. Grey out "Submit" until form valid; don't let the user fail.
6. **Conceptual Model** — Interface matches the user's mental model of the task. "Folders" for files, "cards" for content items.

**Source:** *The Design of Everyday Things* (rev. 2013); https://www.nngroup.com/articles/design-principles-visual-perception/

---

## Steve Krug's Usability Principles

1. **Krug's First Law:** Don't make me think. Every page/action should be self-evident.
2. **Scannability over readability** — Users scan, don't read. Use headings, short paragraphs (≤3 sentences), bullet lists, bold key terms.
3. **Get rid of half the words** on every page, then half again.
4. **Clickable things look clickable**; non-clickable things don't. Distinct hover states.
5. **Navigation persistence** — User should always know: Where am I? What can I do? How do I get home?
6. **The trunk test** — Drop any user on any page; they should instantly identify: site ID, page title, section, primary nav, local nav, "you are here."

**Source:** *Don't Make Me Think* (3rd ed., 2014); https://sensible.com/dont-make-me-think/

---

## Refactoring UI — 10 Visual Polish Rules

Wathan & Schoger's tactical playbook:

1. **Start with a feature, not a layout** — Design the core action first.
2. **Hierarchy through weight/color, not size** — Use font weight (400/600/700) and color (gray-900/gray-600/gray-500) before changing font size.
3. **Don't use gray text on colored backgrounds** — Desaturated tints of the background color instead.
4. **Establish a type scale** — Use 6-8 sizes max (12/14/16/18/20/24/30/36/48/60), not arbitrary values.
5. **Use fewer borders** — Use background color contrast, box-shadow, or spacing instead.
6. **Shadows give depth** — Use layered shadows: `0 1px 2px rgba(0,0,0,0.05), 0 4px 8px rgba(0,0,0,0.05)`. Never single flat shadows.
7. **Saturate colors as they get darker** — Light grays desaturated; dark text highly saturated for readability.
8. **Don't rely on default browser focus** — Design custom focus rings (2px ring + 2px offset).
9. **Supercharge the defaults** — Every native element (buttons, inputs, scrollbars) needs custom styling.
10. **Accessible colors don't have to be ugly** — Use HSL lightness to meet 4.5:1 contrast while keeping brand hue.

**Source:** https://www.refactoringui.com/

---

## The 30 Auto-Enforce Patterns Every Page Must Have

A senior engineer's checklist. Run via `/ux-kit audit`:

### Structure & Layout
1. Consistent page skeleton (header + sidebar + main + footer slots)
2. Max content width (`max-w-prose` ~65ch for reading, `max-w-7xl` for dashboards)
3. 8-point spacing grid — all spacing from token scale, no arbitrary margins

### States (mandatory for every data-bound component)
4. All 5 states wired: loading, empty-first-time, empty-filtered, error, loaded
5. Skeleton matches final layout — not a generic spinner
6. Error recovery — every error has a retry button or clear next step

### Interaction
7. Every action feels < 400ms (optimistic UI for user-owned; skeleton for server-bound)
8. One primary CTA per view (Von Restorff); secondary actions subordinated
9. Keyboard-complete — every action reachable via Tab + Enter; Cmd-K palette present
10. Visible focus rings — 2px offset, brand color, on every focusable element

### Visual System
11. Token-only styling (no hex, no arbitrary spacing, no raw pixel shadows)
12. Type scale enforced (6-8 sizes max); hierarchy via weight + color, not size alone
13. Layered shadows — never flat single shadows
14. Dark mode from day 1 — every token has light + dark value

### Data & Dashboards
15. KPI pattern — number + delta + sparkline + period context, every time
16. Chart type follows data shape (comparison→bar, trend→line, composition→stacked bar)
17. No chartjunk (no 3D, no decorative gradients, default gridlines off)
18. Direct labeling over legends where possible

### Accessibility (non-negotiable)
19. WCAG 2.2 AA — 4.5:1 contrast text, 3:1 non-text, 44×44px touch targets
20. Semantic HTML — `<button>` not `<div onclick>`; landmarks; heading hierarchy
21. `prefers-reduced-motion` respected — decorative motion disabled
22. Screen reader labels — every icon button has `aria-label`; every form field has associated label

### Writing
23. Plain language — 8th-grade reading level; active voice; no jargon unless domain-required
24. Error messages explain + suggest — "Couldn't save. Check your connection and retry."
25. Consistent terminology — one word per concept; build a glossary

### Performance
26. LCP < 2.5s, INP < 200ms, CLS < 0.1 — measured in CI
27. Skeleton while loading — no layout shift when content arrives
28. Image optimization — `next/image` or equivalent; width/height attrs to prevent CLS

### Power User
29. Command palette (Cmd-K) — present in every app; indexes actions + navigation + search
30. Keyboard shortcuts discoverable — `?` shows shortcut cheat sheet

---

## Quick Reference: When to Apply What

| Situation | Apply |
|-----------|-------|
| Choosing navigation structure | Hick's Law (limit 5-7), Jakob's Law (use conventions) |
| Designing a form | Miller's Law (chunk fields), Postel's Law (accept flexible input) |
| Designing a dashboard | Von Restorff (one primary action), Proximity + Common Region (group KPIs) |
| Building a progress flow | Goal-Gradient (show end), Zeigarnik (persist incomplete) |
| Writing error messages | Norman's Feedback, Krug's clarity, Refactoring UI's empathy |
| Picking colors | Refactoring UI rules 3, 6, 7, 10 |
| Touch targets (mobile) | Fitts's Law — 44×44px minimum |
| Animations and transitions | Doherty Threshold (feel < 400ms), Motion principles |
| Micro-interactions | Aesthetic-Usability Effect, Norman's Feedback |
| Writing empty states | Peak-End Rule, Tesler's Law |
