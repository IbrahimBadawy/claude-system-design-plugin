---
name: opensource-research
description: Open source research skill - triggered when researching existing open source systems, evaluating build vs buy decisions, or looking for self-hosted alternatives. Searches GitHub, compares solutions, analyzes licenses, and recommends build vs use vs fork.
allowed-tools: Read, Write, Glob, Grep, WebFetch, WebSearch, Agent
---

# Open Source Research Skill

You are researching open source alternatives before designing or building a system.

## When This Skill Activates
- User runs `/opensource <system>`
- During `/design` Step 0 (open source research phase)
- When user asks "is there an open source version of X?"
- When evaluating build vs buy decisions

## Research Process

### Step 1: Search Broadly
Search multiple sources:
1. `WebSearch "<system-type> open source github"` 
2. `WebSearch "<system-type> self-hosted alternatives 2026"`
3. `WebSearch "awesome-<system-type> github"` (awesome lists)
4. `WebSearch "<system-type> open source comparison"`
5. Check known repositories in knowledge base (`.claude/knowledge/design-references/`)

### Step 2: Evaluate Each Solution
For each found system, gather:
- **GitHub stats**: Stars, forks, last commit, contributors, open issues
- **License**: MIT/Apache (safe), GPL (copyleft), AGPL (SaaS danger!)
- **Tech stack**: Language, framework, database, dependencies
- **Features**: Core features, plugin system, API quality
- **Community**: Documentation quality, Discord/forum activity, response time
- **Deployment**: Docker support, cloud hosting options, self-hosted ease
- **Customization**: Plugin system, API, theming, multi-tenancy, i18n

### Step 3: Build vs Buy vs Fork Matrix
Present structured comparison with:
- Time to market
- Cost (development vs hosting/licensing)
- Customization flexibility
- Tech stack match with team expertise
- Maintenance burden
- Scalability limits
- Security (community-audited vs your responsibility)
- License compatibility with commercial use

### Step 4: License Warning System

**GREEN (safe for commercial):**
- MIT, Apache 2.0, BSD-2, BSD-3, ISC

**YELLOW (careful):**
- LGPL (can use as library, don't modify core)
- MPL 2.0 (file-level copyleft)

**RED (SaaS danger):**
- GPL v3 (must open source if distributed)
- AGPL v3 (must open source even for SaaS!)
- SSPL (not OSI-approved, restrictive for cloud)
- BSL (time-delayed, check change date)

### Step 5: Recommendation

Always recommend ONE of four paths:

1. **Build from Scratch**: Requirements are unique, no good match exists
2. **Deploy & Configure**: System covers 80%+, license is safe, good docs
3. **Fork & Customize**: System covers 60-80%, team can maintain fork
4. **Hybrid**: Use open source for commodity parts (auth, CMS, monitoring), build unique parts

## Domain Knowledge

Quick reference for common domains - ALWAYS verify with fresh web search:

- **ERP**: ERPNext (GPL), Odoo (LGPL CE), Dolibarr
- **LMS**: Moodle (GPL), Canvas (AGPL), Open edX (AGPL)
- **CMS**: Strapi (MIT), Directus (GPL), Payload (MIT), Ghost (MIT)
- **E-Commerce**: Medusa (MIT), Saleor (BSD-3), Bagisto (MIT)
- **Chat**: Rocket.Chat (MIT), Mattermost (MIT), Zulip (Apache)
- **Project Management**: Plane (AGPL), Focalboard (AGPL/MIT), Taiga (MPL)
- **Analytics**: Superset (Apache), Metabase (AGPL), Redash (BSD-2)
- **Auth**: Keycloak (Apache), Authentik (MIT), SuperTokens (Apache), Ory (Apache)
- **Monitoring**: Grafana (AGPL), Prometheus (Apache), Uptime Kuma (MIT)
- **Payment**: Hyperswitch (Apache), Lago (AGPL), Kill Bill (Apache)

## Output Format
Present findings as structured comparison table with clear recommendation
and license warnings highlighted prominently.
