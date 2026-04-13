# Recommended Companion Tools & Plugins

Install these alongside this plugin for a complete professional development environment.

---

## Tier 1: Install Immediately (Essential)

### context7 - Always-Fresh Documentation
Fetches the LATEST docs for any library before writing code. Prevents outdated API usage.
```bash
/plugin install context7
```
- Supports: Next.js, React, Vue, Tailwind, Prisma, FastAPI, NestJS, and hundreds more
- 71K+ installs
- **Why**: Eliminates "outdated code" problem. When you write `import`, it checks the real docs first.

### uipro-cli - UI/UX Design Intelligence
Professional visual design database: styles, colors, fonts, UX patterns.
```bash
npm i -g uipro-cli
uipro init --ai claude
```
- 67 UI styles, 161 color palettes, 57 font pairings, 99 UX guidelines, 25 chart types
- 64K+ GitHub stars
- Supports: React, Next.js, Vue, Nuxt, Angular, Svelte, Flutter, React Native
- **Why**: Our plugin handles architecture; this handles visual design. Together = professional result.

### security-guidance - Security Pre-Hook
Blocks code edits that contain security vulnerabilities BEFORE they're written.
```bash
/plugin install security-guidance
```
- Monitors 9 patterns: XSS, SQL injection, eval(), hardcoded secrets, etc.
- Pre-tool hook = catches issues in real-time, not after the fact
- **Why**: Security by default, enforced at the tool level.

### code-review - Automated PR Review
5 parallel Sonnet agents review your code for bugs, CLAUDE.md compliance, and quality.
```bash
/plugin install code-review
```
- Checks: bugs, performance, security, code style, architectural compliance
- **Why**: Catches what manual review misses.

### playwright - Browser Testing
Microsoft Playwright for E2E testing - Claude controls a real Chrome browser.
```bash
/plugin install playwright
```
- Visual testing, form filling, navigation testing
- **Why**: Verify frontend actually works, not just compiles.

---

## Tier 2: Install Based on Your Stack

### Database Plugins
| Plugin | For | Install |
|--------|-----|---------|
| **prisma** | Prisma ORM (Node.js) | `/plugin install prisma` |
| **supabase** | Supabase (PostgreSQL + Auth + Storage) | `/plugin install supabase` |
| **neon** | Neon serverless PostgreSQL | `/plugin install neon` |
| **mongodb** | MongoDB | `/plugin install mongodb` |
| **planetscale** | PlanetScale MySQL | `/plugin install planetscale` |
| **cockroachdb** | CockroachDB distributed SQL | `/plugin install cockroachdb` |
| **pinecone** | Pinecone vector DB (AI/RAG) | `/plugin install pinecone` |
| **firebase** | Google Firebase | `/plugin install firebase` |

### Deployment Plugins
| Plugin | For | Install |
|--------|-----|---------|
| **vercel** | Vercel (Next.js, frontend) | `/plugin install vercel` |
| **cloudflare** | Cloudflare Workers & Pages | `/plugin install cloudflare` |
| **aws-serverless** | AWS Lambda, API Gateway, DynamoDB | `/plugin install aws-serverless` |
| **deploy-on-aws** | General AWS deployment | `/plugin install deploy-on-aws` |
| **netlify-skills** | Netlify functions & edge | `/plugin install netlify-skills` |
| **railway** | Railway platform | `/plugin install railway` |
| **terraform** | Infrastructure as Code | `/plugin install terraform` |

### API & Backend Plugins
| Plugin | For | Install |
|--------|-----|---------|
| **stripe** | Payment integration | `/plugin install stripe` |
| **postman** | API lifecycle management | `/plugin install postman` |
| **figma** | Design-to-code workflow | `/plugin install figma` |

### Code Intelligence (LSP)
| Plugin | For | Install |
|--------|-----|---------|
| **typescript-lsp** | TypeScript/JavaScript | `/plugin install typescript-lsp` |
| **pyright-lsp** | Python | `/plugin install pyright-lsp` |
| **gopls-lsp** | Go | `/plugin install gopls-lsp` |
| **rust-analyzer-lsp** | Rust | `/plugin install rust-analyzer-lsp` |
| **jdtls-lsp** | Java | `/plugin install jdtls-lsp` |

---

## Tier 3: Community Skills (GitHub)

### Jeffallan/claude-skills (66 Specialized Skills)
The most comprehensive skill collection covering every layer of the stack.
```
GitHub: github.com/Jeffallan/claude-skills
Website: jeffallan.github.io/claude-skills
```
**Includes:**
- Languages (12): TypeScript, Python, Go, Rust, Java, Kotlin, Swift, C#, Ruby, PHP, Elixir, Scala
- Frameworks (7): React, Next.js, NestJS, Django, FastAPI, Spring Boot, Rails
- Infrastructure (5): Docker, Kubernetes, Terraform, AWS, GCP
- API/Architecture (5): REST, GraphQL, gRPC, Event-Driven, Microservices
- Testing (4): Unit, Integration, E2E, TDD
- DevOps (3): CI/CD, GitOps, Monitoring
- Security (3): OWASP, Auth/AuthZ, Secrets Management

### obra/superpowers - Development Methodology
Design-first, plan-first, TDD methodology that aligns perfectly with our plugin's workflow.
```
GitHub: github.com/obra/superpowers
Install: /plugin install superpowers@obra-superpowers
```

### cc-devops-skills (31 DevOps Generators)
Generates and validates: Docker, Kubernetes, Terraform, Helm, GitHub Actions, GitLab CI, Jenkins, Ansible, PromQL.
```
GitHub: github.com/akin-ozer/cc-devops-skills
Install: /plugin marketplace add akin-ozer/cc-devops-skills
```

### Trail of Bits Security Skills
Security analysis with CodeQL and Semgrep from the leading security research firm.
```
GitHub: github.com/trailofbits/skills
```

---

## Tier 4: Monitoring & Quality

| Plugin | For | Install |
|--------|-----|---------|
| **sentry** | Error monitoring | `/plugin install sentry` |
| **semgrep** | Security vulnerability detection | `/plugin install semgrep` |
| **sonarqube-agent-plugins** | Code quality analysis | `/plugin install sonarqube-agent-plugins` |
| **ralph-loop** | Autonomous iteration loops (57K installs) | `/plugin install ralph-loop` |

---

## Recommended Stack Combinations

### For University SaaS Projects
```
Our Plugin (architecture + planning)
+ context7 (fresh docs)
+ uipro-cli (design)
+ prisma or supabase (database)
+ security-guidance (security)
+ vercel or railway (deployment)
+ playwright (testing)
```

### For Enterprise Backend Systems
```
Our Plugin (architecture + planning)
+ context7 (fresh docs)
+ security-guidance + semgrep (security)
+ terraform (IaC)
+ aws-serverless or deploy-on-aws (deployment)
+ cc-devops-skills (DevOps)
+ sentry (monitoring)
+ code-review (quality)
```

### For Full-Stack Web Apps
```
Our Plugin (architecture + planning)
+ context7 (fresh docs)
+ uipro-cli (design)
+ prisma (database)
+ stripe (payments)
+ vercel (deployment)
+ playwright (testing)
+ security-guidance (security)
+ Jeffallan/claude-skills (stack-specific skills)
```

---

## Useful Links

### Awesome Lists (Bookmark These)
- [travisvn/awesome-claude-skills](https://github.com/travisvn/awesome-claude-skills) - Most comprehensive
- [hesreallyhim/awesome-claude-code](https://github.com/hesreallyhim/awesome-claude-code) - Skills + hooks + commands
- [rohitg00/awesome-claude-code-toolkit](https://github.com/rohitg00/awesome-claude-code-toolkit) - 135 agents, 35 skills, 176+ plugins
- [VoltAgent/awesome-agent-skills](https://github.com/VoltAgent/awesome-agent-skills) - 1000+ skills

### Plugin Marketplaces
- Built-in: `/plugin` > Discover (in Claude Code)
- [claude-plugins.dev](https://claude-plugins.dev/)
- [claudemarketplaces.com](https://claudemarketplaces.com/)
- [mcpmarket.com](https://mcpmarket.com/) - MCP tools
