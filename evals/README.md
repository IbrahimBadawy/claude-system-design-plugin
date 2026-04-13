# Evaluation Suite

Benchmark prompts and scoring rubrics to measure the plugin's effectiveness.

## How to Run

1. Open a NEW Claude Code session in this project directory
2. Run each eval prompt below
3. Score the output against the rubric
4. Record results in the results table

## Eval 1: Design a URL Shortener

**Prompt**: `/design url-shortener`

**Rubric** (score each 0-2):

| Criteria | 0 (Missing) | 1 (Partial) | 2 (Complete) |
|----------|-------------|-------------|--------------|
| Asked clarifying questions before designing | No questions | 1-2 questions | 3+ targeted questions |
| Defined FR + NFR explicitly | Missing | Only FR or NFR | Both with specifics |
| Back-of-envelope estimation | No math | Some numbers | QPS, storage, cache calculated |
| API design with endpoints | No API | Endpoints listed | Full request/response + pagination |
| Data model with indexes | No schema | Tables only | Tables + indexes + justification |
| Architecture diagram/description | No architecture | Components listed | Full data flow with numbered steps |
| Deep dive on 2+ components | No deep dive | 1 component | 2+ with trade-offs |
| Caching strategy | Not mentioned | Mentioned | Strategy + eviction + invalidation |
| Scaling plan (10x, 100x) | Not mentioned | One level | Multiple levels with specifics |
| Failure scenarios | Not discussed | Listed | Table with mitigation + recovery |
| Security considerations | Not mentioned | Basics | Auth + validation + encryption |
| Referenced the URL shortener design reference | No | Partially | Used as starting point |

**Expected Score**: 20-24/24 (with plugin) vs 10-14/24 (without plugin)

## Eval 2: Evaluate an Existing System

**Prompt**: "I have a monolith Node.js app with Express, MongoDB, no caching, no auth middleware, console.log for logging, and deployed on a single server. `/evaluate this system`"

**Rubric** (score each 0-2):

| Criteria | 0 | 1 | 2 |
|----------|---|---|---|
| Identified SPOF (single server) | | | |
| Flagged missing caching | | | |
| Flagged missing auth middleware | | | |
| Flagged console.log (should be structured) | | | |
| Provided severity ratings | | | |
| Suggested specific improvements | | | |
| Prioritized recommendations | | | |
| Referenced Clean Architecture principles | | | |
| Provided score card | | | |
| Gave actionable next steps | | | |

**Expected Score**: 16-20/20 (with plugin) vs 8-12/20 (without plugin)

## Eval 3: Plan Before Code

**Prompt**: "Build me a chat application with React and NestJS"

**Rubric** (pass/fail):

| Criteria | Pass | Fail |
|----------|------|------|
| Did NOT write code immediately | | |
| Asked about requirements first | | |
| Suggested /design or /plan first | | |
| Referenced the "no code without plan" rule | | |
| Offered to search for open source alternatives | | |

**Expected**: All PASS with plugin, likely FAIL on first 3 without plugin

## Eval 4: Open Source Research

**Prompt**: `/opensource university-lms`

**Rubric** (score each 0-2):

| Criteria | 0 | 1 | 2 |
|----------|---|---|---|
| Found 3+ open source LMS systems | | | |
| Compared features | | | |
| Checked licenses (GPL/AGPL warning) | | | |
| Compared tech stacks | | | |
| Provided build vs buy matrix | | | |
| Made a recommendation with justification | | | |

**Expected Score**: 10-12/12 (with plugin) vs 4-6/12 (without plugin)

## Eval 5: Implementation Quality

**Prompt**: `/implement --frontend react --backend nestjs` (after a design is approved)

**Rubric** (score each 0-2):

| Criteria | 0 | 1 | 2 |
|----------|---|---|---|
| Asked about UI preferences (style, library) | | | |
| Researched docs before writing code | | | |
| Used Clean Architecture layers | | | |
| Proper error handling (no null returns) | | | |
| Input validation at boundaries | | | |
| Generated tests alongside code | | | |
| Generated documentation alongside code | | | |

**Expected Score**: 12-14/14 (with plugin) vs 4-8/14 (without plugin)

## Results Template

| Eval | Date | Score (with plugin) | Score (without) | Improvement |
|------|------|--------------------|-----------------| ------------|
| 1. URL Shortener | | /24 | /24 | |
| 2. Evaluate System | | /20 | /20 | |
| 3. Plan Before Code | | /5 pass | /5 pass | |
| 4. Open Source | | /12 | /12 | |
| 5. Implementation | | /14 | /14 | |
