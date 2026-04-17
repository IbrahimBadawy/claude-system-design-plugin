# /copy-audit - UX Writing Quality Gate

Audit or generate UX copy. Enforces the principles in `.claude/knowledge/microcopy.md`:
no lorem ipsum, no "Error 500", no "click here", every button verb + object, every error
answers what/why/what-now.

## Usage
```
/copy-audit                     # Scan all UI strings in the project, report issues
/copy-audit for <page>          # Audit copy on a specific page
/copy-audit generate <type>     # Generate copy for type (error, empty-state, button, confirm, toast)
/copy-audit voice               # Define / view the project's voice & tone framework
/copy-audit glossary            # Build/view the project terminology glossary
```

## What it checks

- [ ] No "Click here", "Here", "Learn more" as entire link text
- [ ] No raw error codes ("Error 500", "E_NO_PERM") without explanation
- [ ] No `lorem ipsum` shipped
- [ ] No placeholder-only labels (every input has `<label>`)
- [ ] Every button starts with a verb + object
- [ ] Every error answers: what happened, why, what now
- [ ] Every empty state has explanation + primary CTA
- [ ] Every destructive confirm names the consequence
- [ ] Destructive primary button NOT "OK"
- [ ] Sentence case (not Title Case) on buttons, labels, headings
- [ ] No blame language ("You failed to…" / "Invalid input")
- [ ] No mixed Oxford comma usage
- [ ] No idioms ("kick the tires", "low-hanging fruit")
- [ ] No ALL CAPS sentences
- [ ] Inclusive language (alex.js linter)
- [ ] Reading level ≤ 8th grade (Hemingway / Flesch)

## Output Format

```markdown
## Copy Audit: <project-name>

Strings scanned: 247
Issues found: 18

### Critical (block release) — 3
1. `src/pages/login.tsx:42` — "Error 500" shipped as error message.
   Replace with: "Couldn't sign you in. Try again in a moment."
2. `src/components/LinkList.tsx:15` — "Click here to read more"
   Replace with: "Read the full article"
3. `src/pages/dashboard.tsx:128` — lorem ipsum detected.
   Replace with real copy or remove.

### Serious — 7
4. `src/components/DeleteButton.tsx:8` — Confirm dialog: "Are you sure?"
   Suggest: "Delete 'Q3 plan'? This removes 12 linked files."
5. `src/pages/profile.tsx:92` — Button "Submit" (generic)
   Suggest: "Save profile" (verb + object)
...

### Warnings (non-blocking) — 8
- `src/pages/landing.tsx:5` — reading level grade 11 (target ≤ 8)
- `src/components/Pricing.tsx:23` — idiom "bang for your buck"
...

### Suggestions
Run `/copy-audit generate empty-state --for products` to auto-fix empty states.
```

## Voice & Tone

`/copy-audit voice` creates/shows `design/VOICE.md`:

```markdown
# Voice & Tone: <project-name>

## Voice (constant)
- Direct, minimal, craft-focused
- Plainspoken — avoid jargon
- Empathetic when things go wrong

## Tone by state
| State | Tone | Example |
|-------|------|---------|
| Success | Celebrate briefly | "Sent." |
| Error | Calm, specific, owns problem | "We couldn't send that. Try again." |
| Empty | Encouraging, shows path | "No projects yet. Create your first one." |
| Loading | Honest, not cute | "Processing 12k rows — ~20s" |
| Legal/billing | Neutral, precise, no humor | "Invoice 1234 is due April 30, 2026." |

## Inspiration
- [Linear style](https://linear.app/changelog)
- [Stripe style](https://stripe.com)
```

## Generate Mode

```
/copy-audit generate error --for api-timeout
```

Produces:
```
Title: "We couldn't reach the server"
Body: "Your request timed out. Check your connection and try again."
Primary: "Retry"
Secondary: "Contact support"
```

## Examples

```
/copy-audit                                 # Full scan
/copy-audit for /settings/profile           # One page
/copy-audit generate empty-state --for orders
/copy-audit generate error --for 404
/copy-audit voice                           # Edit voice framework
/copy-audit glossary                        # View/add terms
```

---

**What's next?**
- `/a11y-audit` — accessibility audit (labels, contrast, keyboard)
- `/ux-kit audit` — UX states and patterns
- `/implement --copy` — regenerate copy with the project voice
