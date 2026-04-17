# Internationalization (i18n) & RTL

> Canonical sources: MDN CSS Logical Properties, W3C BIDI basics, Material Design
> Bidirectionality, RTL Styling 101, ICU MessageFormat, next-intl.

---

## Top 10 Checklist

1. **CSS logical properties everywhere** — `margin-inline-start/-end`, `padding-block`, `border-inline`, `inset-inline-start`, `text-align: start/end`. Delete every `margin-left` / `padding-right` / `text-align: left` from user-facing CSS
2. **`<html lang="xx" dir="ltr|rtl">` set correctly** based on locale. Switch `dir` on locale change, not via user toggle alone
3. **Bidi-aware formatting for user-generated text** — wrap with `<bdi>` or `dir="auto"`; use Unicode isolates (U+2068 FSI … U+2069 PDI) for server-side strings; never rely on visual order
4. **Mirror directional icons only:** arrows (back/forward/chevrons), progress bars, undo/redo, text-align-left. **Don't mirror:** clocks, play/pause/media controls, checkmarks, numbers, logos, calendars, phone/envelope icons, volume
5. **Numbers, dates, currency via `Intl`** — never string concat. `new Intl.NumberFormat(locale, {style:"currency", currency:"EGP"})`. Decide early: Arabic-Indic digits (٠١٢) or Western (012)?
6. **Plurals and gender via ICU MessageFormat.** Arabic has 6 plural forms (zero/one/two/few/many/other): `{count, plural, zero{...} one{...} few{...} other{#...}}`
7. **Font stack per locale** via `:lang(ar)` or `[lang="ar"]`:
   ```css
   [lang="ar"] {
     font-family: "IBM Plex Sans Arabic", "Noto Sans Arabic",
                  "Cairo", "Tajawal", "Segoe UI", Tahoma, sans-serif;
   }
   ```
   Load weights 400/500/700 only; subset to Arabic range
8. **Translation keys are namespaced and stable** — `auth.login.submit` not `button-23`. Include description & screenshot in the source entry for translators
9. **Never concatenate translations**, always interpolate: `t('welcome', { name })` where the message is `"Welcome, {name}"` — lets translators move the name where it belongs grammatically
10. **Test RTL in CI** — screenshot snapshots in `en` and `ar` for every page; Playwright test forces `dir="rtl"`; reviewer passes one key journey in RTL before release

---

## CSS Logical Properties (mandatory)

Replace **every** physical property with its logical counterpart:

| Physical (wrong) | Logical (right) |
|------------------|-----------------|
| `margin-left` | `margin-inline-start` |
| `margin-right` | `margin-inline-end` |
| `padding-top` | `padding-block-start` |
| `padding-bottom` | `padding-block-end` |
| `border-left` | `border-inline-start` |
| `border-right` | `border-inline-end` |
| `top` | `inset-block-start` |
| `bottom` | `inset-block-end` |
| `left` | `inset-inline-start` |
| `right` | `inset-inline-end` |
| `text-align: left` | `text-align: start` |
| `text-align: right` | `text-align: end` |
| `float: left` | `float: inline-start` |
| `float: right` | `float: inline-end` |
| `width` | `inline-size` |
| `height` | `block-size` |

### Tailwind Support (2024+)

Tailwind 3.3+ supports logical properties: use `ms-4` (margin-inline-start) instead of
`ml-4`, `pe-2` instead of `pr-2`, etc.

Enforce via ESLint rule or Stylelint:
```js
// stylelint config
"declaration-property-value-disallowed-list": {
  "/^(margin|padding|border|inset)-/": ["/-(left|right)$/"]
}
```

Source: https://developer.mozilla.org/en-US/docs/Web/CSS/Guides/Logical_properties_and_values

---

## Icon Mirroring Quick Reference

| Mirror (RTL flips) | Don't mirror |
|---|---|
| `<` `>` arrows, chevrons | clock faces (time moves clockwise both scripts) |
| Back, forward, next, prev | play ▶ / pause ⏸ / media scrub |
| Undo ↺ / Redo ↻ | checkmark ✓ |
| Text-align left/right | numbers, percentages |
| Reply, forward arrows | brand logos, trademarks |
| Indent / outdent | envelope, phone, camera |
| Progress bars | search magnifier, settings cog |

### Implementation

Option A (CSS): flag directional icons with a class, flip via `[dir="rtl"]`:
```css
[dir="rtl"] .icon-directional {
  transform: scaleX(-1);
}
```

Option B (SVG): ship mirrored SVGs per direction using `:dir()` pseudo-class or
separate components `<BackIcon />` vs `<BackIconRtl />`.

---

## Intl API (built-in)

### Numbers
```js
new Intl.NumberFormat('en-US').format(1234567.89);    // "1,234,567.89"
new Intl.NumberFormat('de-DE').format(1234567.89);    // "1.234.567,89"
new Intl.NumberFormat('ar-EG').format(1234567.89);    // "١٬٢٣٤٬٥٦٧٫٨٩"
```

### Currency
```js
new Intl.NumberFormat('en-US', { style: 'currency', currency: 'USD' })
  .format(99.99);                                      // "$99.99"

new Intl.NumberFormat('ar-SA', { style: 'currency', currency: 'SAR' })
  .format(99.99);                                      // "٩٩٫٩٩ ر.س."
```

### Dates
```js
new Intl.DateTimeFormat('en-US', { dateStyle: 'long' }).format(new Date());
// "April 16, 2026"

new Intl.DateTimeFormat('ar-EG', { dateStyle: 'long' }).format(new Date());
// "١٦ أبريل ٢٠٢٦"
```

### Relative Time
```js
new Intl.RelativeTimeFormat('en', { numeric: 'auto' }).format(-1, 'day');
// "yesterday"
```

---

## Key Structure Best Practices

```json
{
  "nav": {
    "home": "Home",
    "settings": "Settings"
  },
  "auth": {
    "login": {
      "title": "Sign in to your account",
      "email": "Work email",
      "submit": "Sign in",
      "errors": {
        "invalid": "Email or password is incorrect. Try again."
      }
    }
  },
  "projects": {
    "empty": "No projects yet. Create your first one to get started.",
    "count": "{count, plural, =0 {No projects} one {# project} other {# projects}}"
  }
}
```

### Rules

- **Max 3 levels deep**
- **Namespace by feature**, not component
- **Keep keys in English** as source of truth
- **Always provide `en`** as fallback
- **Lint for missing keys** in other locales in CI

---

## Translation Workflow

```
extract   → keys pulled from source via i18next-parser / @formatjs/cli
translate → Lokalise / Crowdin / Phrase, with context & screenshots
review    → native speaker + designer pass (layout, length)
deploy    → versioned JSON bundles, CDN cached, fallback to en on missing key
monitor   → log missing keys + mark to translate next sprint
```

---

## i18n Framework Recommendations

| Framework | Library | Why |
|-----------|---------|-----|
| **Next.js App Router** | `next-intl` | RSC-native, typesafe, ICU support |
| **Next.js Pages** | `next-i18next` | Mature, battle-tested |
| **Vue / Nuxt** | `vue-i18n` | Official, ICU via `intlify` |
| **Any React** | `react-i18next` or `FormatJS / react-intl` | |
| **Svelte** | `svelte-i18n` or `sveltekit-i18n` | |
| **Any framework** | `FormatJS` | Best ICU support, polyglot |

---

## Arabic-Specific Gotchas

- **Line-height** looks cramped at 1.4; bump to **1.6-1.75** for Arabic
- **Bold weights** are tricky — many Arabic webfonts look heavy; prefer Plex Arabic 500 over 700 for body emphasis
- **Diacritics (Tashkeel)** often break justification; test with real user content
- **Don't italicize Arabic** — no italic tradition; use weight or color for emphasis
- **Punctuation flips:** `؟ ، ؛` are RTL-contextual

```css
[lang="ar"] {
  line-height: 1.75;
  font-feature-settings: "kern", "liga";
}

[lang="ar"] strong,
[lang="ar"] b {
  font-weight: 500;  /* 500 looks bold enough in Arabic */
}
```

---

## Recommended Arabic Font Stacks

```css
/* Modern, clean */
font-family: "IBM Plex Sans Arabic", "Noto Sans Arabic", sans-serif;

/* Classic web-safe */
font-family: "Tajawal", "Cairo", "Segoe UI", Tahoma, sans-serif;

/* Elegant */
font-family: "Noto Kufi Arabic", "Dubai", serif;
```

Sources:
- https://fonts.google.com/noto/specimen/Noto+Sans+Arabic
- https://fonts.google.com/specimen/IBM+Plex+Sans+Arabic

---

## i18n Checklist

- [ ] `<html lang="..." dir="...">` set dynamically per locale
- [ ] CSS logical properties only (no `margin-left` etc. in user-facing components)
- [ ] All user-facing strings externalized (no hardcoded text)
- [ ] ICU MessageFormat for plurals and gender
- [ ] `Intl.*` APIs for numbers, dates, currency, relative time
- [ ] Directional icons flagged and flipped in RTL
- [ ] Non-directional icons NEVER flipped (clocks, checkmarks, logos)
- [ ] Arabic font stack via `:lang(ar)` with bumped line-height
- [ ] Translation workflow established (extract → translate → review → deploy)
- [ ] CI fails on missing keys in supported locales
- [ ] RTL screenshot test per page
- [ ] `<bdi>` or `dir="auto"` around user-generated content

Sources:
- https://developer.mozilla.org/en-US/docs/Web/CSS/Guides/Logical_properties_and_values
- https://rtlstyling.com/
- https://www.w3.org/International/articles/inline-bidi-markup/uba-basics
- https://m3.material.io/foundations/layout/understanding-layout/bidirectionality-rtl
- https://next-intl.dev/docs/design-principles
- https://phrase.com/blog/posts/guide-to-the-icu-message-format/
