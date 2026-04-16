# /pages - Comprehensive Page Inventory

Generate a complete inventory of pages, screens, and user flows that the app needs.
Nothing gets missed: auth, admin, dashboards, profile, settings, system pages — all in.

## Usage
```
/pages                             # Full page inventory (asks about app type)
/pages generate                    # Build the inventory into PAGES.md
/pages audit                       # Check existing code against the inventory
/pages add <name>                  # Add a custom page to the inventory
/pages for <feature>               # Generate pages for a specific feature
```

## Why This Command Exists

Most projects ship with gaps: no 404, no forgot-password, no empty states, no admin
audit trail, no notification center. This command forces a full enumeration of every
page a real user needs, including the ones that are easy to forget.

## Core Page Categories

Every project with users gets AT LEAST these categories. Skip only after explicit
user confirmation.

### 1. Auth & Onboarding
| Page | Required | Purpose |
|------|---------|---------|
| Login | ✓ | Email/password, OAuth buttons, "forgot password" link, "remember me" |
| Register / Sign up | ✓ | Form, terms checkbox, email verification trigger |
| Forgot password | ✓ | Enter email, send reset link |
| Reset password | ✓ | New password form (token from email) |
| Email verification | ✓ | Verification pending + confirmation pages |
| Two-factor setup | Optional | TOTP enroll, backup codes, SMS fallback |
| Two-factor challenge | If 2FA | Verification code entry |
| SSO / Social callback | If OAuth | OAuth return landing |
| Onboarding wizard | Recommended | 2-5 step intro for first-time users |
| Logout confirmation | Optional | Quick "you're logged out" page |
| Invite acceptance | Multi-user | Accept invitation from email link |

### 2. User Account
| Page | Required | Purpose |
|------|---------|---------|
| Profile | ✓ | Avatar, name, bio, contact info |
| Account settings | ✓ | Email, password change, language, timezone |
| Security | ✓ | 2FA, active sessions, devices, password history |
| Notifications preferences | ✓ | Channels (email/push/in-app), categories, frequency |
| Billing / Subscription | If paid | Current plan, invoices, payment methods, usage |
| API keys / Tokens | If API | Create, revoke, usage stats |
| Connected accounts | If OAuth | Manage linked social/OAuth accounts |
| Danger zone | ✓ | Delete account, export data (GDPR) |

### 3. Admin / Back-office (if multi-user)
| Page | Required | Purpose |
|------|---------|---------|
| Admin dashboard | ✓ | KPIs, charts, recent activity, quick actions |
| Users management | ✓ | List, search, filter, invite, suspend, impersonate |
| User detail | ✓ | Profile, roles, activity, login history, impersonate |
| Roles management | ✓ | List roles, create/edit, assign permissions |
| Permissions matrix | ✓ | Role × Permission grid with checkboxes |
| Audit log | ✓ | Who did what when (searchable, exportable) |
| System settings | ✓ | App-wide config (SMTP, limits, features) |
| Feature flags | Recommended | Toggle features per user / role / tenant |
| Tenants / Organizations | If SaaS | Multi-tenant management |
| Billing admin | If paid | Subscriptions, refunds, coupons |
| Integration settings | If integrations | Webhooks, API clients, OAuth apps |

### 4. Core Dashboard
| Page | Required | Purpose |
|------|---------|---------|
| Main dashboard | ✓ | Personalized home: KPIs, widgets, recent, quick actions |
| Analytics | Recommended | Charts, time ranges, export to CSV/PDF |
| Reports | Recommended | Saved reports, scheduling, sharing |
| Global search | Recommended | Cmd-K palette: users, entities, settings, help |

### 5. Per-Entity Pages (for EACH core entity)
For every domain entity (Product, Order, Customer, Project, Task, etc.), generate:

| Page | Required | Purpose |
|------|---------|---------|
| List / Index | ✓ | Data table, search, filter, sort, pagination, bulk actions, export |
| Detail view | ✓ | Full entity info, related data, activity, actions, comments |
| Create form | ✓ | Multi-step if complex, auto-save draft, validation, preview |
| Edit form | ✓ | Pre-filled, change tracking, save/cancel |
| Archive / Trash | Recommended | Soft-deleted items, restore, permanent delete |
| Import | Recommended | CSV/Excel upload, field mapping, preview, errors |
| Export | ✓ | CSV, Excel, PDF, JSON |
| Comparison / Diff | Optional | Compare 2+ entities side by side |

### 6. Communication
| Page | Required | Purpose |
|------|---------|---------|
| Notification center | ✓ | In-app notifications, mark read, filter, clear |
| Messages / Inbox | If messaging | Thread list, conversation view, compose |
| Comments / Discussions | If collab | Thread per entity, replies, mentions, reactions |
| Activity feed | Recommended | Timeline of team activity |
| Announcements | Admin | System messages to users |

### 7. Help & Support
| Page | Required | Purpose |
|------|---------|---------|
| Help / Docs | Recommended | Searchable knowledge base |
| FAQ | Optional | Common questions |
| Contact / Support | ✓ | Contact form or ticket creation |
| Changelog | Recommended | What's new, versioned |
| Feedback | Optional | Ratings, suggestions, bug reports |
| Keyboard shortcuts | Power users | Reference card (also modal triggered by `?`) |

### 8. System & Utility
| Page | Required | Purpose |
|------|---------|---------|
| 404 Not Found | ✓ | Friendly, with search + links home |
| 403 Forbidden | ✓ | "You don't have permission" + contact admin |
| 500 Server Error | ✓ | Friendly error, request ID, retry |
| Maintenance mode | ✓ | "Back at HH:MM" |
| Rate limited | ✓ | "Too many requests, try again in X" |
| Offline | PWA | When no network |
| Terms of Service | ✓ (legal) | Legal text |
| Privacy Policy | ✓ (legal) | GDPR/CCPA compliant |
| Cookie settings | ✓ (GDPR) | Granular consent |

### 9. Marketing (if public-facing)
| Page | Required | Purpose |
|------|---------|---------|
| Landing page | ✓ | Hero, value prop, CTAs, social proof |
| Features | Recommended | Feature showcase |
| Pricing | If paid | Tiered plans, FAQ, calculator |
| About | Optional | Team, mission |
| Blog | Optional | CMS-driven or MDX |
| Contact | ✓ | Sales / support form |

## Complexity-Based Scoping

| Complexity | Page Count | What's Included |
|-----------|-----------|-----------------|
| **Simple** | 8-15 pages | Auth (login/register/forgot/reset), profile, main dashboard, 1-2 core entities (list/detail/create/edit), 404/500 |
| **Medium** | 20-35 pages | Above + Account settings, notifications, 3-5 entities full CRUD, admin basics (users + roles), audit log, help |
| **Complex** | 40-80+ pages | Above + full RBAC matrix, tenants, billing, integrations, analytics, reports, API keys, feature flags, all system pages, multi-language |

## How to Use

### Step 1: Ask the User

```
What kind of app are we building? I'll use this to generate the right page inventory.

  1. SaaS / Web App (multi-user, auth, dashboards)
  2. Admin panel / internal tool
  3. E-commerce storefront
  4. Blog / Content site
  5. Marketing site (no auth)
  6. Mobile-first web app
  7. Dashboard-only (reporting, no CRUD)
  8. Other (describe)

What are the core entities? (e.g. Products, Orders, Customers, Projects)
```

### Step 2: Generate PAGES.md

Create `projects/<active>/design/PAGES.md` with:

```markdown
# Pages Inventory: <Project Name>
**Generated**: <date>
**Complexity**: <level>
**Core Entities**: Product, Order, Customer

## Summary
Total pages: 34
- Auth & onboarding: 5
- User account: 4
- Admin: 6
- Dashboard: 2
- Product (CRUD + extras): 6
- Order (CRUD + extras): 5
- Customer (CRUD + extras): 4
- System: 2

## Auth & Onboarding
| # | Page | Path | Permissions | Status |
|---|------|------|-------------|--------|
| 1 | Login | /login | public | ☐ |
| 2 | Register | /register | public | ☐ |
| 3 | Forgot Password | /auth/forgot | public | ☐ |
| 4 | Reset Password | /auth/reset/:token | public | ☐ |
| 5 | Email Verify | /auth/verify/:token | public | ☐ |

## User Account
| # | Page | Path | Permissions | Status |
|---|------|------|-------------|--------|
| 6 | Profile | /profile | authenticated | ☐ |
| 7 | Settings | /settings | authenticated | ☐ |
| 8 | Security | /settings/security | authenticated | ☐ |
| 9 | Notifications Prefs | /settings/notifications | authenticated | ☐ |

... (continue for every category)

## Per-Page Spec
For every page in the inventory, generate a sub-doc with:
- Purpose
- Layout sketch
- Components needed
- Data fetched (API endpoints)
- Actions available (buttons, forms)
- Permissions check
- Empty/loading/error states
- Edge cases
```

### Step 3: Audit Existing Code

`/pages audit` scans `projects/<active>/src/` and compares against `PAGES.md`:

```
## Pages Audit

| # | Page | In Inventory | Implemented | Coverage |
|---|------|-------------|-------------|----------|
| 1 | Login | ✓ | ✓ | 100% |
| 2 | Register | ✓ | ✓ | 100% |
| 3 | Forgot Password | ✓ | ✗ Missing | 0% |
| ... | ... | ... | ... | ... |

### Missing (8 pages)
- Forgot Password  /auth/forgot
- Reset Password   /auth/reset/:token
- ...

### Incomplete (3 pages)
- Profile /profile — missing: avatar upload, timezone selector
- Users admin /admin/users — missing: bulk actions, impersonate

### Recommendation
Run /implement --pages to generate the missing pages.
```

## Integration with Other Commands

- `/implement` reads PAGES.md and generates code for every page with status `☐`
- `/frontend design` uses PAGES.md to plan the component library
- `/rbac` adds the "Permissions" column to every page
- `/docs generate` documents every page

## Examples

```
/pages                              # Ask about app type, generate inventory
/pages generate                      # Save PAGES.md
/pages for checkout                  # Generate checkout flow pages
/pages audit                         # Check what's built vs planned
/pages add admin-impersonate         # Add a custom page
```

---

**What's next?**
- `/rbac` - Design the permission system for these pages
- `/frontend design` - Design the components these pages need
- `/ux-kit` - Add UX quality gates (empty states, loading, errors)
- `/implement --pages` - Build the inventory in code
