---
name: research-tech
description: Technology research skill - triggered when researching technologies, comparing tools, or seeking latest best practices. Searches the web, reads documentation, and synthesizes findings.
allowed-tools: Read, Write, Glob, Grep, WebFetch, WebSearch, Agent
---

# Technology Research Skill

You are researching technologies and best practices.

## Research Process

### Step 1: Define Research Scope
- What exactly are we researching?
- What decisions does this inform?
- What criteria matter most? (performance, cost, team expertise, maturity)

### Step 2: Search Strategy
Search in this order:
1. **Official documentation** of the technology
2. **ByteByteGo / Alex Xu articles** for system design context
3. **Engineering blogs** from major companies (Netflix, Uber, Stripe, Airbnb, Meta, Google)
4. **Benchmark comparisons** (recent, 2024-2026)
5. **Community discussions** (GitHub issues, Stack Overflow, HN)
6. **Cloud provider docs** (AWS, GCP, Azure) for managed service options

### Step 3: Evaluation Criteria
For each technology, assess:
- **Performance**: Throughput, latency, resource usage
- **Scalability**: How it scales horizontally and vertically
- **Reliability**: Fault tolerance, data durability
- **Maturity**: Production battle-tested, stable API
- **Community**: Active development, contributor count, issue response time
- **Cost**: Licensing, infrastructure, operational overhead
- **Integration**: How well it fits with existing stack
- **Team**: Learning curve, hiring availability

### Step 4: Synthesis
- Compare options in a structured matrix
- Identify clear winner or context-dependent recommendations
- Note any emerging trends or upcoming changes
- Provide actionable recommendation with justification

## Common Research Topics
- Database selection (SQL vs NoSQL vs NewSQL)
- Message queue comparison (Kafka vs RabbitMQ vs Pulsar)
- Caching solutions (Redis vs Memcached vs Valkey)
- Search engines (Elasticsearch vs Meilisearch vs Typesense)
- API styles (REST vs gRPC vs GraphQL)
- Container orchestration (Kubernetes vs alternatives)
- Observability stack (OpenTelemetry ecosystem)
- CI/CD tools and practices
- Authentication/authorization solutions
- Vector databases for AI/ML
- Serverless platforms

## Output
Structured research report with comparison matrix and clear recommendation.
