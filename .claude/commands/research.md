# /research - Research Technologies and Patterns

Research the latest technologies, patterns, and best practices for a given topic.

## Usage
```
/research <topic>
```

## Behavior

### Phase 1: Web Research
1. Search the internet for the latest information on the topic
2. Look for:
   - Official documentation
   - Recent blog posts and articles (2024-2026)
   - Benchmark comparisons
   - Real-world case studies
   - Alex Xu / ByteByteGo articles on the topic
   - Engineering blogs (Netflix, Uber, Stripe, Airbnb, etc.)

### Phase 2: Knowledge Base Check
1. Check `.claude/knowledge/` for existing patterns
2. Cross-reference with book knowledge (DDIA, Clean Architecture, etc.)
3. Identify what's new vs what's established

### Phase 3: Synthesis Report

```markdown
## Research Report: [Topic]
**Date**: [today]
**Sources**: [number of sources consulted]

### Overview
[Brief description of the topic and why it matters]

### Current State (2026)
[What's the latest in this space]

### Key Options/Technologies

#### Option 1: [Name]
- **What**: [description]
- **Strengths**: [list]
- **Weaknesses**: [list]
- **Used by**: [companies]
- **Best for**: [use cases]
- **Documentation**: [link]

#### Option 2: [Name]
...

### Comparison Matrix
| Feature | Option A | Option B | Option C |
|---------|----------|----------|----------|
| Performance | ... | ... | ... |
| Scalability | ... | ... | ... |
| Cost | ... | ... | ... |
| Maturity | ... | ... | ... |
| Community | ... | ... | ... |

### Emerging Trends
- [Trend 1]
- [Trend 2]

### Recommendation
[Based on the research, what should be chosen and why]

### References
- [Source 1]
- [Source 2]
```

### Research Topics to Always Cover
- Latest version and features
- Performance benchmarks
- Scaling characteristics
- Community health and activity
- Cloud provider support
- Cost at scale
- Migration paths
- Known limitations

## Examples
```
/research best database for time-series data in 2026
/research Kafka alternatives for event streaming
/research authentication strategies for microservices
/research vector databases for RAG pipelines
/research serverless frameworks comparison
```
