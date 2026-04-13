# /design - Full System Design

Start a complete system design from scratch using the 4-step framework.

## Usage
```
/design <system_name>
```

## Behavior

### Step 0: Open Source Research (BEFORE designing)
**Before writing any design**, search for existing open source systems in the same domain:
1. Use WebSearch to find: "<system-type> open source", "<system-type> GitHub"
2. Identify top 3-5 open source alternatives
3. Present to user with brief comparison:
   - Name, stars, license, tech stack, key features
4. ASK the user: "Build from scratch, use an existing system, or fork and customize?"
5. Based on answer:
   - **Build from scratch** -> Continue with full design (Step 1-4)
   - **Use existing** -> Design the deployment, customization, and integration
   - **Fork** -> Design what to keep, what to modify, and integration points
   - **Hybrid** -> Identify which components to reuse and which to build

See `/opensource` command for full research methodology.

### Step 1: Requirements & Estimation
1. Acknowledge the system to design
2. Ask 3-5 clarifying questions about scope, scale, and constraints
3. Once answered, document:
   - **Functional Requirements** (FR1, FR2, ...)
   - **Non-Functional Requirements** (scale, latency, availability, consistency)
   - **Out of Scope** items
   - **Assumptions**
4. Perform back-of-envelope estimation:
   - DAU, QPS (average and peak using x3 rule)
   - Storage (daily, monthly, yearly with replication)
   - Bandwidth (inbound/outbound)
   - Cache size (20% hot data rule)
   - Server count

### Step 2: High-Level Design
1. Design the **API** with full endpoint specifications:
   - Method, URL, description, auth requirement
   - Request/response JSON examples
   - Pagination, filtering, sorting
2. Design the **Data Model**:
   - Tables/collections with columns, types, constraints
   - Indexes based on query patterns
   - Relationships (1:1, 1:N, N:M)
3. Design the **Architecture**:
   - All components (clients, LB, API servers, DB, cache, queue, storage, CDN)
   - Data flow between components
   - Numbered flow for key use cases

### Step 3: Deep Dive
1. Select 2-3 critical components for deep dive
2. For each component:
   - Detailed design with algorithms/data structures
   - Technology choice with justification
   - Trade-offs considered (at least 2 alternatives)
   - Scaling approach
3. Address:
   - Database choice justification (SQL vs NoSQL vs specialized)
   - Caching strategy (cache-aside, write-through, etc.)
   - Sharding strategy if needed (key selection, approach)
   - Message delivery semantics (at-least-once, exactly-once)

### Step 4: Wrap Up
1. **Failure scenarios** table (failure, impact, mitigation, recovery)
2. **Monitoring** plan (key metrics, alerts, dashboards)
3. **Security** review (auth, encryption, input validation, OWASP)
4. **Scaling plan** table (current, 10x, 100x, 1000x)
5. **Deployment strategy** (blue-green, canary, rolling)
6. **Cost estimation** if applicable
7. **Future improvements** prioritized list

## Output Format
Use the System Design Document template from `.claude/skills/design-system/templates/`.

## Reference Patterns
When the system resembles a known design, reference patterns from the knowledge base:
- URL Shortener, Rate Limiter, Chat System, News Feed, etc.
- See `.claude/knowledge/` for full pattern library

## Examples
```
/design notification-system
/design ride-sharing-service
/design real-time-leaderboard
/design payment-system
/design university-management-system
```
