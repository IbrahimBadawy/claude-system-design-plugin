---
name: design-system
description: Comprehensive system design skill - triggered when designing new systems, features, or architectures. Uses the 4-step framework from Alex Xu with knowledge from DDIA, Clean Architecture, and distributed systems best practices.
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, WebFetch, WebSearch, Agent
---

# System Design Skill

You are designing a new system. Follow the 4-step framework strictly.

## Pre-Design Checklist
Before starting:
1. Identify if this resembles a known system design pattern (check knowledge base)
2. Understand the domain and business context
3. Clarify ambiguous requirements with the user

## Step 1: Requirements & Scope

### Functional Requirements
Ask the user to confirm:
- What are the core features? (must-have vs nice-to-have)
- Who are the users? (end users, admins, internal services)
- What are the main use cases?

### Non-Functional Requirements
Define explicitly:
- **Scale**: Expected DAU, peak concurrent users
- **Latency**: p99 target (< 200ms for user-facing, < 50ms for internal)
- **Availability**: SLA target (99.9% = 8.77 hrs downtime/year)
- **Consistency**: Strong vs eventual (depends on use case)
- **Durability**: Data loss tolerance (zero for financial, some for analytics)

### Back-of-Envelope Estimation
Calculate: QPS, storage, bandwidth, cache size, server count.
Use formulas from CLAUDE.md estimation cheat sheet.

## Step 2: High-Level Design

### API Design
- RESTful by default (GraphQL for flexible client queries, gRPC for internal)
- Version all APIs from day 1
- Include auth, rate limiting, pagination, idempotency

### Data Model
- Choose database based on access patterns (see rules/09-data-model.md)
- Design schema with proper indexes
- Plan for data growth and migration

### Architecture
Draw the component diagram showing:
- Client -> CDN -> Load Balancer -> API Gateway
- API Servers (stateless) -> Cache -> Database
- Message Queue -> Workers
- Object Storage, Search Engine, etc.

## Step 3: Deep Dive

Select 2-3 critical components and design in detail:
- Algorithm and data structure choices
- Technology selection with trade-off analysis
- Failure modes and recovery
- Scaling approach

### Reference Patterns (from books)
Apply these patterns where appropriate:
- **Fan-Out** (News Feed): Push for normal users, pull for celebrities
- **URL Shortening**: Base62 encoding, 301 vs 302 redirects
- **Rate Limiting**: Token Bucket with Redis
- **Chat**: WebSocket, message sync queue, presence service
- **Search Autocomplete**: Trie with top-K caching
- **Payment**: Idempotency, double-entry bookkeeping, reconciliation
- **File Sync**: Block-level delta sync, conflict resolution
- **Video**: DAG transcoding, adaptive bitrate, CDN
- **Leaderboard**: Redis Sorted Sets
- **Proximity**: Geohash or Quadtree
- **Stock Exchange**: Single-server, shared memory, sequential processing

## Step 4: Production Readiness

Address all items from the checklist command.
Focus on: failure scenarios, monitoring, security, scaling plan.

## Output
Use the System Design Document template in templates/ directory.

## Research Mode
If the design involves technologies you're not confident about:
1. Search the web for latest best practices
2. Check official documentation
3. Look for real-world case studies
4. Update recommendations based on findings
