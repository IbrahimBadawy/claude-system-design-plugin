---
description: Every UI must be accessible. WCAG 2.2 AA is the minimum standard.
globs: "*.{tsx,jsx,vue,svelte,html}"
---

# Rule: Accessibility (WCAG 2.2 AA)

Every UI component MUST meet WCAG 2.2 AA standards:

1. **Keyboard Navigation**: Every interactive element reachable and operable via keyboard
2. **Screen Readers**: Semantic HTML, ARIA labels, alt text for images
3. **Color Contrast**: 4.5:1 minimum for normal text, 3:1 for large text
4. **Focus Management**: Visible focus indicators, logical tab order
5. **Forms**: Labels linked to inputs, error messages associated with fields
6. **No Motion Harm**: Respect prefers-reduced-motion media query
7. **Text Scaling**: UI works at 200% zoom

## Component Library Selection
When choosing a UI library, prefer those with built-in accessibility:
- React: Radix UI, Headless UI, Chakra UI, Mantine (all accessible by default)
- Vue: Headless UI, PrimeVue (good a11y support)

## Testing
- Run axe-core in CI (zero violations policy for critical/serious)
- Test with keyboard-only navigation
- Test with screen reader (NVDA or VoiceOver)

## RTL Support (Arabic)
- Use CSS logical properties (margin-inline-start, not margin-left)
- Set dir="rtl" on html element
- Test full UI flow in RTL mode
