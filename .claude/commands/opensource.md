# /opensource - Open Source Systems Research

Research existing open source systems in the same domain. Compare build-vs-buy-vs-fork.

## Usage
```
/opensource <system-type>                  # Research open source alternatives
/opensource <system-type> --compare        # Detailed comparison matrix
/opensource <system-type> --recommend      # Give recommendation with justification
```

## Behavior

### Phase 1: Domain Research

When designing any system, FIRST search for existing open source solutions:

1. **Search the web** for: "<system-type> open source", "<system-type> open source self-hosted", "<system-type> GitHub", "<system-type> alternatives open source 2026"
2. **Search GitHub** for: most starred repositories in the category
3. **Search awesome lists** for: "awesome-<system-type>", "awesome-selfhosted"
4. **Check Product Hunt / AlternativeTo** for: open source alternatives

### Phase 2: Evaluate Each Solution

For every open source system found, gather:

```markdown
## [System Name]

**GitHub**: [url] | **Stars**: [count] | **License**: [MIT/GPL/AGPL/etc.]
**Language**: [primary language] | **Last Update**: [date]
**Website**: [url] | **Demo**: [url if available]
**Docker**: [yes/no] | **Cloud Hosted**: [yes/no]

### What It Does
[Brief description of features and capabilities]

### Tech Stack
| Layer | Technology |
|-------|-----------|
| Backend | [language + framework] |
| Frontend | [framework] |
| Database | [DB] |
| Other | [queue, cache, etc.] |

### Key Features
- [Feature 1]
- [Feature 2]
- [Feature 3]

### Strengths
- [Strength 1]
- [Strength 2]

### Weaknesses
- [Weakness 1]
- [Weakness 2]

### Community Health
- Contributors: [count]
- Issues (open/closed): [count]
- Release frequency: [monthly/quarterly/etc.]
- Documentation quality: [Poor/Fair/Good/Excellent]

### Customization
- Plugin/Module system: [yes/no, how]
- API: [REST/GraphQL/etc.]
- Theming: [yes/no]
- Multi-tenancy: [yes/no, how]
- i18n: [languages supported]
```

### Phase 3: Build vs Buy vs Fork Decision Matrix

Present a structured comparison:

```markdown
## Decision: Build from Scratch vs Use Open Source

### Options

| Criteria | Build from Scratch | Use [System A] | Fork [System B] |
|----------|-------------------|----------------|-----------------|
| **Time to Market** | 6-12 months | 1-2 months | 2-4 months |
| **Cost** | High (dev team) | Low (hosting only) | Medium |
| **Customization** | 100% flexible | Limited by system | Flexible but maintenance burden |
| **Tech Stack Match** | Exact match | May not match team expertise | Can modify |
| **Maintenance** | Full ownership | Community maintained | Fork maintenance burden |
| **Scalability** | Design for your scale | May have limits | Depends on architecture |
| **Security** | Your responsibility | Community audited | Your fork, your responsibility |
| **Features Match** | Build exactly what you need | May have 70% fit | Modify to fit |
| **Learning Curve** | Team knows the code | Learn the system | Learn + modify |
| **Long-term Risk** | Full control | Project may die | Fork diverges from upstream |
| **Compliance** | Full control | Check license | Check license (AGPL = copyleft!) |
| **Support** | Internal | Community / paid | Internal |

### License Considerations
| License | Can Use Commercially | Must Open Source Your Code | Can Modify | Notes |
|---------|---------------------|--------------------------|------------|-------|
| **MIT** | Yes | No | Yes | Most permissive |
| **Apache 2.0** | Yes | No | Yes | Patent protection |
| **GPL v3** | Yes | Yes (if distributed) | Yes | Copyleft |
| **AGPL v3** | Yes | Yes (even for SaaS!) | Yes | Strongest copyleft - careful! |
| **BSL** | Limited | After change date | Yes | Time-delayed open source |
| **SSPL** | No (for SaaS) | Yes | Yes | MongoDB-style, not true OSS |

**WARNING**: AGPL means if you run it as a SaaS, ALL your code must be open source.
Check the license BEFORE deciding to use any open source system.
```

### Phase 4: Recommendation

Based on analysis, recommend ONE of:

**Option A: Build from Scratch**
When to choose:
- Requirements are unique and don't match any existing system
- Team has the expertise and time
- Need full control over architecture and scaling
- Compliance/security requirements are strict
- No suitable open source option exists

**Option B: Use as-is (Deploy & Configure)**
When to choose:
- Open source system covers 80%+ of requirements
- License is compatible (MIT/Apache preferred)
- Active community and good documentation
- Team is willing to learn the system
- Time to market is critical
- Standard use case (ERP, CMS, LMS, etc.)

**Option C: Fork & Customize**
When to choose:
- System covers 60-80% of requirements
- Need significant customization
- Team can maintain the fork
- Architecture is sound and extensible
- License allows forking (check carefully)

**Option D: Hybrid (Use modules/APIs from open source)**
When to choose:
- Some components are standard (auth, payment, email)
- Core business logic is unique
- Open source components have good APIs
- Want to avoid reinventing the wheel for commodity features

## Common Domain References

### ERP Systems
| System | Stars | License | Stack | Best For |
|--------|-------|---------|-------|----------|
| **ERPNext** | 18K+ | GPL v3 | Python/Frappe/MariaDB | SMB, manufacturing, full ERP |
| **Odoo** | 35K+ | LGPL v3 (CE) | Python/PostgreSQL | Most popular, huge module ecosystem |
| **Dolibarr** | 5K+ | GPL v3 | PHP/MySQL | Simple ERP/CRM for small business |
| **iDempiere** | 1K+ | GPL v2 | Java/PostgreSQL | Enterprise, accounting-heavy |

### University / Education (LMS)
| System | Stars | License | Stack | Best For |
|--------|-------|---------|-------|----------|
| **Moodle** | 5K+ | GPL v3 | PHP/MySQL | Most popular LMS worldwide |
| **Canvas LMS** | 5K+ | AGPL v3 | Ruby on Rails/PostgreSQL | Modern, API-first |
| **Open edX** | 8K+ | AGPL v3 | Python/Django/MongoDB | MOOC platform (edX/MIT) |
| **Chamilo** | 800+ | GPL v3 | PHP/MySQL | Simple, lightweight LMS |

### CMS / Content
| System | Stars | License | Stack | Best For |
|--------|-------|---------|-------|----------|
| **Strapi** | 63K+ | MIT | Node.js/SQLite/PostgreSQL | Headless CMS, API-first |
| **Directus** | 27K+ | GPL v3 | Node.js/Any SQL DB | Database-first headless CMS |
| **Ghost** | 47K+ | MIT | Node.js/MySQL | Publishing, blogs, newsletters |
| **WordPress** | - | GPL v2 | PHP/MySQL | Most popular CMS (43% of web) |
| **Payload CMS** | 25K+ | MIT | Node.js/TypeScript | Developer-first, code-based |

### E-Commerce
| System | Stars | License | Stack | Best For |
|--------|-------|---------|-------|----------|
| **Medusa** | 25K+ | MIT | Node.js/PostgreSQL | Modern headless commerce |
| **Saleor** | 20K+ | BSD-3 | Python/Django/GraphQL | Enterprise, GraphQL-first |
| **WooCommerce** | - | GPL v3 | PHP/WordPress | Most popular (36% of stores) |
| **Bagisto** | 13K+ | MIT | Laravel/PHP | Laravel ecosystem |

### Project Management
| System | Stars | License | Stack | Best For |
|--------|-------|---------|-------|----------|
| **Plane** | 30K+ | AGPL v3 | Python + Next.js | Jira/Linear alternative |
| **Taiga** | 7K+ | MPL 2.0 | Python/Django + Angular | Agile/Scrum teams |
| **Focalboard** | 21K+ | AGPL/MIT | Go + React | Trello/Notion alternative |
| **Leantime** | 4K+ | AGPL v3 | PHP | Simple PM for non-PMs |

### Chat / Communication
| System | Stars | License | Stack | Best For |
|--------|-------|---------|-------|----------|
| **Rocket.Chat** | 40K+ | MIT | Node.js/MongoDB | Slack alternative, enterprise |
| **Mattermost** | 30K+ | MIT/Enterprise | Go/React/PostgreSQL | DevOps-focused team chat |
| **Element/Matrix** | 11K+ | Apache 2.0 | Various/Synapse (Python) | Decentralized, encrypted |
| **Zulip** | 21K+ | Apache 2.0 | Python/Django/PostgreSQL | Threaded conversations |

### Analytics / BI
| System | Stars | License | Stack | Best For |
|--------|-------|---------|-------|----------|
| **Metabase** | 38K+ | AGPL v3 | Java/Clojure | Simple BI, self-service |
| **Apache Superset** | 62K+ | Apache 2.0 | Python/Flask/React | Advanced analytics |
| **Redash** | 26K+ | BSD-2 | Python/React | SQL-first dashboards |

### Auth / Identity
| System | Stars | License | Stack | Best For |
|--------|-------|---------|-------|----------|
| **Keycloak** | 22K+ | Apache 2.0 | Java | Enterprise SSO, OIDC |
| **Authentik** | 12K+ | MIT Entente | Python/Django/Go | Modern, flexible |
| **Ory** | 13K+ | Apache 2.0 | Go | Cloud-native identity |
| **SuperTokens** | 12K+ | Apache 2.0 | Node.js/Go | Self-hosted auth |

### Monitoring / Observability
| System | Stars | License | Stack | Best For |
|--------|-------|---------|-------|----------|
| **Grafana** | 64K+ | AGPL v3 | Go/React | Visualization, dashboards |
| **Prometheus** | 55K+ | Apache 2.0 | Go | Metrics collection |
| **Jaeger** | 20K+ | Apache 2.0 | Go | Distributed tracing |
| **Sentry** | 39K+ | BSL | Python/Django | Error tracking |
| **Uptime Kuma** | 57K+ | MIT | Node.js | Simple uptime monitoring |

### Payment
| System | Stars | License | Stack | Best For |
|--------|-------|---------|-------|----------|
| **Kill Bill** | 4K+ | Apache 2.0 | Java | Billing & payment platform |
| **Lago** | 7K+ | AGPL v3 | Ruby/Rails/React | Usage-based billing |
| **Hyperswitch** | 12K+ | Apache 2.0 | Rust | Payment orchestration |

## Integration with Design Workflow

This command is automatically invoked during `/plan create` as part of the research phase.
The results feed into:
- **MASTER-PLAN.md**: Technology decisions section
- **ADR**: "Build vs Buy" Architecture Decision Record
- **Cost estimation**: Comparing build cost vs deployment cost
- **Timeline**: Significant impact on project timeline

## Examples
```
/opensource erp
/opensource university-lms
/opensource e-commerce --compare
/opensource chat-system --recommend
/opensource project-management
/opensource analytics-dashboard
```
