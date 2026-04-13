---
description: Always research official documentation before using any framework, library, or tool in implementation code
globs: "*.{ts,js,py,java,go,rs,tsx,jsx,vue,svelte}"
---

# Rule: Research Before Code

BEFORE writing ANY code that uses a framework, library, or external tool:

1. **Ask yourself**: "Am I 100% certain about the current API?"
2. **If ANY doubt exists**: STOP and research
3. **Use WebSearch** to find the official documentation
4. **Use WebFetch** to read the specific docs page you need
5. **Extract** the exact syntax, imports, configuration
6. **THEN** write code using verified information

## ALWAYS Research For:
- ORM/Database setup (Prisma, Drizzle, SQLAlchemy, TypeORM)
- Authentication libraries (NextAuth/Auth.js, Passport, JWT setup)
- UI frameworks (Tailwind CSS, shadcn/ui, Material UI)
- Build tool configuration (Vite, Next.js, Docker)
- Cloud SDK usage (AWS SDK, Firebase, Stripe)
- Any package you haven't used in the last month
- Any package where you're not sure of the version

## WHY This Rule Exists
- Library APIs change between versions (breaking changes)
- Configuration formats evolve (Tailwind v3 vs v4, Next.js 13 vs 14 vs 15)
- Import paths change (named exports, default exports, subpath exports)
- New best practices emerge (React Server Components, etc.)
- Outdated code causes hard-to-debug errors

## The Research Flow
```
1. WebSearch "<library> official docs <year>"
2. WebFetch <official-docs-url>
3. Extract: imports, API signature, config format, peer deps
4. Write code with confidence
```

## NEVER Guess
If you can't remember the exact syntax for something, look it up.
5 seconds of research saves 30 minutes of debugging.
