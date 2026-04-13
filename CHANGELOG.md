# Changelog

All notable changes to this project will be documented in this file.
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).
This project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.2.0] - 2026-04-13

### Added
- **Knowledge Builder**: `/knowledge build`, `/knowledge import`, `/knowledge list`, `/knowledge show`, `/knowledge update`, `/knowledge add`, `/knowledge search`, `/knowledge docs`
- **knowledge-builder skill**: Auto-triggers for unfamiliar domains, researches web + docs, imports PDFs/manuals
- Per-topic knowledge folders with docs/ subfolder for user-provided documentation

### Changed
- Project folder structure: added `knowledge/` with per-topic subfolders and docs/

## [1.1.0] - 2026-04-13

### Added
- **3-Gate Workflow**: Discovery -> Planning -> Implementation with explicit user gates
- **Discovery Phase**: Free discussion before planning, with diagrams/ and research/ folders
- **Smart Scope Detection**: Simple/Medium/Complex auto-detection
- **Mermaid diagram generation** during discovery phase
- Project folder structure: discovery/, discovery/diagrams/, discovery/research/

### Changed
- Rule 13 rewritten as "3-Gate Workflow" (was "No Code Without Plan")
- Workflow section in README and index.html completely redesigned
- project.md command updated with new folder structure

## [1.0.0] - 2026-04-13

### Added
- **34 Commands**: quickstart, design, evaluate, improve, implement, plan, frontend, backend-libs, docs, research, opensource, knowledge, schema, api, domain, gateway, tenancy, estimate, tradeoff, scale, cost, adr, security, perf, debt, privacy, cicd, failure, monitor, checklist, postmortem, project, project-deps, project-template
- **16 Rules**: requirements-first, design-before-code, justify-decisions, think-about-failure, security-by-default, clean-code, scalability, observability, data-model, api-standards, research-before-code, auto-documentation, no-code-without-plan (3-gate), domain-modeling, accessibility, performance-budgets
- **10 Skills**: design-system, evaluate-system, implement-system, research-tech, project-manager, documentation, frontend-design, devops, opensource-research, knowledge-builder
- **16 Design References**: Rate Limiter, URL Shortener, Unique ID Generator, Key-Value Store, Chat System, News Feed, Notification System, Search Autocomplete, Web Crawler, Proximity Service, Video Platform, File Storage, Payment System, Leaderboard, Metrics Monitoring, Stock Exchange
- **4 Templates**: SaaS Dashboard, REST API Service, E-Commerce Platform, System Design Document
- **Knowledge Base**: patterns, technology-guide (2026), distributed-systems (DDIA deep read), clean-architecture-guide (SOLID, GoF, Clean Code, Code Complete, Refactoring), recommended-tools (50+), domains/ (custom knowledge)
- **Planning System**: Master plan + 10 sub-plans with 3-gate approval workflow
- **Project Management**: Multi-project tracking, dependencies, templates, core/dependent relationships
- **Auto Documentation**: Docs generated alongside code with Swagger, Storybook, TypeDoc, Docusaurus
- **Open Source Research**: Build vs buy vs fork analysis with license checks
- **Enterprise Profile**: settings.enterprise.json for restricted environments
- **Evaluation Suite**: 5 benchmarks with scoring rubrics + 2 full examples
- **Hooks**: PostToolUse reminder for documentation updates
- **Landing Page**: index.html with full feature showcase
- **LICENSE**: MIT
- **CONTRIBUTING.md**: Guide for adding commands, rules, skills, templates
