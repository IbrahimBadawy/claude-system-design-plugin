---
description: UX copy is not filler. Every button labels the outcome, every error explains what/why/what-now, no lorem ipsum ships, no "click here" or raw error codes. Inclusive language enforced. Sentence case across the app.
globs: "*.{tsx,jsx,vue,svelte,html,md,json}"
---

# Rule 27: Copy Quality

## The Rule

UX copy is part of the product, not a placeholder. Every string must pass the quality
gate before shipping.

## Five Principles

1. **Clear beats clever** — write for scanning. 14 words = 90% comprehension; 8 = 100%
2. **Specific beats generic** — verb + object on every button ("Send reset link", not "Submit")
3. **Empathetic beats polite** — "We couldn't…" not "You failed to…"
4. **Brief beats exhaustive** — cut 30% from every draft
5. **Consistent beats creative** — same concept → same word everywhere

## Banned Patterns

Enforced via linter (alex.js + custom rules):

- **"Click here"**, **"Here"**, **"Learn more"** as whole link text
- Raw error codes without explanation ("Error 500", "E_NO_PERM")
- **Lorem ipsum** in shipped code
- Placeholder as the only label (every input needs `<label>`)
- Generic button labels: "Submit", "OK", "Continue" without context
- **ALL CAPS sentences** (slower reading, screams)
- Blame language: "You failed to…" / "Invalid input"
- Mixed Oxford comma (pick one per project)
- Passive voice in instructions
- Idioms that don't translate ("kick the tires", "low-hanging fruit")

## Required Patterns

- **Buttons:** verb + object ("Create account", "Delete project", "Send invoice")
- **Errors:** what happened + why + what now
  > Bad: "Error"
  > Good: "Couldn't send the code. This email isn't registered. Try another or [create an account]."
- **Empty states:** explanation + primary CTA
  > Bad: "No items."
  > Good: "No projects yet. Create your first one to get started. [Create project]"
- **Destructive confirmations:** name the consequence, button label names the action
  > Bad: "Are you sure? [OK] [Cancel]"
  > Good: "Delete 'Q3 plan'? This removes 12 linked files. [Delete 'Q3 plan'] [Keep it]"
- **Toasts:** 1 sentence, ≤10 words, auto-dismiss 4-6s
- **Sentence case** across app (not Title Case)

## Inclusive Language

Tech tropes to retire:
- master/slave → **primary/replica**
- blacklist/whitelist → **blocklist/allowlist**
- sanity check → **smoke test / quick check**
- dummy variable → **placeholder variable**

Never use: crazy, insane, lame, dumb, retarded, tone-deaf, blind to.

Gender: singular "they" when unknown. Avoid "guys" — use "folks", "everyone", "team".

## Voice & Tone

Project MUST have a `design/VOICE.md` with:
- Voice (constant across the app)
- Tone by state (success, error, empty, loading, legal)
- Inspiration examples
- Glossary of project-specific terms

## Reading Level

- Onboarding / marketing: ≤ 8th grade (Hemingway / Flesch)
- Legal / billing: whatever the content requires (but still plain)
- Error messages: 6th-8th grade

## i18n-Ready Copy

- Never concat translations: `"Welcome, " + name` → `t('welcome', { name })`
- Short sentences (< 15 words) — translators expand 30-40%
- ICU MessageFormat for plurals and gender
- Context comments for translators

## Enforcement

- `/copy-audit` scans all user-facing strings
- ESLint plugin bans `"Click here"` and friends in JSX text
- alex.js linter runs in CI for inclusive language
- Grade-level check in CI for key pages (landing, onboarding)
- PR blocked on lorem ipsum detection

## Complexity-Driven Scope

- **Simple:** ban list + glossary
- **Medium:** above + voice/tone doc + grade-level check on landing
- **Complex:** above + full i18n workflow + inclusive language CI + microcopy library

## Why This Rule Exists

- Every "Error 500" shipped is a bug report waiting to happen
- "Click here" literally fails WCAG 2.4.4 — it's an accessibility bug
- Lorem ipsum in production is the #1 sign of "we ran out of time" — and users notice
- Bad copy undoes great design. Users judge apps by words as much as visuals
- Inclusive language is table stakes in 2026

## Escape Hatch

Forbidden patterns allowed ONLY in:
- Internal debug UIs behind feature flags
- Test fixtures clearly named as such
- Code comments (not user-facing)
