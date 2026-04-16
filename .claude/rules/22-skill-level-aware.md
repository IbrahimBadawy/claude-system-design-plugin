---
description: Adapt the number and depth of technical questions to the user's programming skill level (Non-Programmer, Beginner, Intermediate, Professional). For low-skill users, Claude picks sensible defaults; for Intermediate+ Claude asks the stack questions explicitly.
globs: "*"
---

# Rule 22: Skill-Level-Aware Behavior

## The Rule

Every project has a **User Skill** field in `PROJECT.md`: `Non-Programmer`, `Beginner`,
`Intermediate`, or `Professional`. Read it before asking any technical question or
making any technology choice.

- **Low skill** (Non-Programmer, Beginner) → Claude picks almost everything. Minimal
  technical questions. Smart defaults, explained briefly.
- **High skill** (Intermediate, Professional) → Claude asks explicitly. User is in
  control of stack, style, deployment target.

If there is no active project or no user skill set, ASK:

```
What's your programming skill level?

  1. Non-Programmer  - I'll pick almost everything for you. You describe what you want.
  2. Beginner         - I'll pick most tech choices and explain them.
  3. Intermediate     - I'll ask you: frontend, backend, DB, OS (Win/Linux), UI style, library preferences.
  4. Professional     - Full technical dialogue; I'll present trade-offs and expect opinions.

Which one?
```

## Behavior by Level

### Non-Programmer

- **Vocabulary**: Use plain English, no jargon ("data store" not "PostgreSQL with JSONB").
- **Questions**: Only product-level ("What should the app do? Who uses it?").
- **Never asks**: framework, library, DB engine, ORM, auth library, deployment OS.
- **Always picks defaults** and states them briefly:
  - Frontend: **Next.js + shadcn/ui** (fast, modern, accessible)
  - Backend: **Node.js + NestJS** (solid defaults, scales well)
  - Database: **PostgreSQL** (the safe, industry standard)
  - Deploy: **Linux + Docker** (runs anywhere)
  - Style: **Modern & Minimal**
- **Explanation style**: "I chose Next.js because it's the most popular modern framework
  and will give us a fast, SEO-friendly site."

### Beginner

- **Vocabulary**: Technical but with short explanations.
- **Questions**: Product-level + 1-2 high-level preferences:
  - "Do you want a web app, mobile app, or both?"
  - "Is this public-facing or internal only?"
  - "Do you prefer a clean modern look or something colorful?"
- **Never asks**: specific framework names, ORMs, auth libraries.
- **Picks defaults** like Non-Programmer but explains each choice in 1-2 sentences:
  "I'll use Next.js for the frontend because it has built-in SEO and server rendering,
  which makes your app faster and more discoverable."
- **Shows the stack picks** in a small table at the end and asks "want to change anything?"

### Intermediate

- **Vocabulary**: Full technical dialogue.
- **MUST ASK** (the 6 stack questions — see `/project` command):
  - Q3: Frontend framework
  - Q4: Backend framework
  - Q5: Database
  - Q6: Deployment OS (Linux / Windows / Both / Serverless)
  - Q7: UI visual style
  - Q8: UI library preference (e.g. PrimeVue, shadcn/ui, MUI)
- **Trade-offs**: Mention pros/cons when presenting options ("NestJS is opinionated and
  great for DI; Express is simpler and faster to start").
- **Respects opinions**: If the user says "use Vue + PrimeVue", go with it even if
  Claude would have picked something else.

### Professional

- Everything Intermediate does, **plus**:
- Asks about:
  - ORM (Prisma vs Drizzle vs TypeORM vs raw SQL)
  - Caching strategy (Redis, in-memory, CDN)
  - Message queue (Kafka, RabbitMQ, Redis Streams, SQS)
  - Auth flow (JWT vs sessions vs OAuth providers)
  - Infra (VPS vs managed K8s vs serverless)
  - Observability stack (Prometheus/Grafana vs Datadog vs OpenTelemetry)
- Presents trade-off tables with 2-3 alternatives per decision.
- Expects the user to push back — if the user says "I prefer Drizzle because X", document
  the rationale as an ADR.

## Interaction with Complexity

Complexity and user skill are **independent**. A Non-Programmer can have a Complex
project (big client system, with Claude driving all choices). A Professional can pick
Simple (fast MVP for internal tool, wants full control).

| Complexity × Skill | Behavior |
|--------------------|----------|
| Simple × Non-Programmer | Minimal questions, picks all defaults, 1 diagram inline, straight to code |
| Simple × Professional | Few product questions, asks stack, picks sensibly, straight to code |
| Complex × Non-Programmer | Full discovery (but phrased simply), Claude picks all tech, 10 plans with plain-English summaries |
| Complex × Professional | Full discovery, full stack dialogue, all 10 plans with ADRs and trade-off tables |

## Onboarding Order (always)

When a new project is created, ask questions in this order:

1. **Complexity** (Rule 21)
2. **User Skill** (Rule 22)
3. **Stack questions** — only if User Skill ≥ Intermediate (frontend, backend, DB, OS, style, lib)

Save everything in `PROJECT.md` before moving to Discovery.

## User Override

Users can always override during the conversation:
- "Pick for me" → Claude drops to Beginner-style defaults for that decision.
- "What are my options?" → Claude presents the full list regardless of skill level.
- "Change my skill level to Professional" → update `PROJECT.md`.

## Why This Matters

- Asking a non-programmer "Prisma or Drizzle?" is frustrating and unhelpful.
- Picking frameworks silently for a professional is condescending and often wrong.
- Matching question depth to the person saves time and produces better outcomes.
