# UX Writing & Microcopy

> Canonical sources: Nielsen Norman Group, Mailchimp Content Style Guide,
> Shopify Polaris Content, GOV.UK Content Design, 18F Inclusive Language.

---

## 5 Foundational Principles

1. **Clear beats clever** — Write for scanning, not reading. 14 words = 90% comprehension; 8 words = 100%
2. **Specific beats generic** — Every button starts with a strong verb that names the outcome
3. **Empathetic beats polite** — Acknowledge friction; don't blame users
4. **Brief beats exhaustive** — Cut 30% from every draft
5. **Consistent beats creative** — Same concept → same word everywhere

---

## Top 10 Checklist

1. **Buttons start with a verb that names the result** — "Create account", "Send reset link", "Delete project". NEVER "Click here", "Submit", "OK" alone, "Continue" without context
2. **Error messages answer three questions:** *What happened, why, what now?*
   > "We couldn't send your code. The email `x@y` isn't registered. Try another email or [create an account]."
3. **Empty states encourage action.** Not "No items" → "No projects yet. Create your first one to get started." [Primary CTA]
4. **Confirmation dialogs state consequence and are reversible when possible.** "Delete 42 files? This can't be undone." Primary action reads "Delete 42 files" not "OK". Destructive buttons visually distinct
5. **Toasts are 1 sentence, ≤10 words**, auto-dismiss 4-6s, NEVER for critical info. Errors stay until dismissed
6. **Onboarding is progressive** — one thing at a time, let users defer. Skip is always visible
7. **Labels match user vocabulary**, not internal code. "Projects" not "Workspaces" unless users say workspace
8. **Avoid jargon & idioms** — No "kick the tires", "low-hanging fruit", "piece of cake" (doesn't translate, confuses non-native speakers and cognitively impaired users)
9. **Sentence case for everything** (buttons, labels, headings) except brand names and proper nouns — easier to read, more conversational
10. **One voice, many tones** — voice stays constant (friendly, direct, plainspoken). Tone flexes by context: celebratory on success, calm + specific on error, professional in legal/billing

---

## Good vs Bad

| Context | Bad | Good |
|---------|-----|------|
| Error | "Something went wrong." | "We couldn't save your changes. Your connection dropped — try again." |
| Delete | "Are you sure?" / "OK, Cancel" | "Delete 'Q3 plan'? This permanently removes 12 files. / [Delete] [Keep]" |
| Empty list | "No data." | "No invoices yet. Your first one will appear here after you send it. [Send invoice]" |
| Success toast | "Success!" | "Invite sent to dana@acme.com" |
| Form field | Placeholder "Enter email" (no label) | `<label>Work email</label>` + help: "We'll use this to send your receipts" |
| Loading | "Loading…" for 30s | "Crunching 12,000 rows — about 20 seconds left" + cancel |
| 404 | "Error 404" | "This page has moved or never existed. [Back to dashboard] or [Search]" |

---

## Forbidden Patterns (enforce at lint/review)

- **"Click here"**, **"Here"**, **"Learn more"** as entire link text (fails SC 2.4.4 and hurts screen readers)
- Raw error codes with no explanation ("Error 500", "E_NO_PERM")
- **Lorem ipsum in shipped code**
- Placeholder text as the only label
- **ALL CAPS for sentences** (slower reading, screams)
- Emoji instead of words in critical paths (translation risk + screen reader weirdness)
- **Blame language:** "You failed to…" / "Invalid input" → "That didn't match; try…"
- Mixed Oxford comma usage — pick one per project
- Passive voice in instructions ("Data should be saved" → "Save your changes")

---

## Voice & Tone Framework

Codify per project:

```
Voice (constant):     [friendly | professional | playful | technical]
Tone slider by state:
  Success:   celebrate briefly, move on
  Error:     calm, specific, owns the problem (we, not you)
  Empty:     encouraging, shows the path
  Loading:   honest about time, not cute
  Legal/billing: neutral, precise, no humor
```

**Example — Linear voice & tone:**
- Voice: direct, minimal, craft-focused
- Success: "Sent." (not "Great! Your message was successfully sent!")
- Error: "We couldn't send that. Try again." (no emoji, no blame)

**Example — Mailchimp voice & tone:**
- Voice: friendly, slightly playful, never cringe
- Success: "High fives! Your campaign is sent."
- Error: "Hmm, something didn't go as planned. Here's what to try..."

---

## i18n-Friendly Copy

- **Short sentences** (< 15 words) — translators expand 30-40% in German/Arabic
- **Avoid contractions** in UI that'll be translated (harder to regex-swap)
- **NEVER concatenate strings** — `"Welcome, " + name + "!"` breaks translations. Use ICU:
  ```
  welcome_message = "Welcome, {name}!"
  ```
- **Context comments for translators:**
  ```json
  "cart_button": {
    "message": "Add to cart",
    "description": "Button on product page to add item to shopping cart"
  }
  ```
- **Don't hardcode singular/plural** — use ICU `plural`:
  ```
  "{count, plural, =0 {No items} one {1 item} other {# items}}"
  ```
- **Don't rely on word order for meaning**

---

## Inclusive Language

### Disability
- Follow community preference
- **Default: person-first** ("person with a disability")
- **Accept identity-first** ("autistic person", "Deaf user")
- **Never:** crazy, insane, lame, dumb, retarded, tone-deaf, blind to

### Gender
- **Singular "they"** when unknown — "Users can update their profile" (not "his or her")
- Avoid "guys" for mixed groups; use "folks", "everyone", "team"

### Race / Ethnicity
- Only mention if relevant
- Adjectives not nouns — "a Hispanic person" not "a Hispanic"
- **Capitalize Black and Indigenous**

### Age
- Avoid "old", "elderly"; use "older adult" or age range
- No "millennials" as an insult

### Tech Tropes to Retire
- master/slave → **primary/replica**
- blacklist/whitelist → **blocklist/allowlist**
- sanity check → **smoke test / quick check**
- dummy variable → **placeholder variable**

---

## Copy Quality Checklist

Run on every copy change:

- [ ] Every button starts with a verb + object
- [ ] Every error answers: what happened, why, what now
- [ ] Every empty state has: explanation + primary CTA
- [ ] Every destructive confirmation names the consequence
- [ ] Destructive primary buttons are NOT "OK" — they name the action
- [ ] Sentence case throughout
- [ ] Ban-list linter enforced: "click here", "submit", "something went wrong", lorem ipsum, raw codes
- [ ] Inclusive language linter (e.g., alex.js) passes
- [ ] Reading level ≤ 8th grade for general content (onboarding, marketing)
- [ ] No idioms or culturally-specific references
- [ ] Consistent terminology across the app (glossary maintained)

Sources:
- https://www.nngroup.com/articles/3-is-of-microcopy/
- https://styleguide.mailchimp.com/voice-and-tone/
- https://polaris.shopify.com/content/voice-and-tone
- https://www.gov.uk/guidance/content-design/writing-for-gov-uk
- https://guides.18f.gov/content-guide/our-style/inclusive-language/
