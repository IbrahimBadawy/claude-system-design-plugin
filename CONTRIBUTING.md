# Contributing to System Design Architect

Thank you for your interest in contributing! This guide will help you get started.

## How to Contribute

### Reporting Issues
- Use GitHub Issues to report bugs or suggest features
- Include: what you expected, what happened, steps to reproduce
- For command issues: include the command you ran and the output

### Adding a New Command
1. Create a new `.md` file in `.claude/commands/`
2. Follow the existing command format (see `design.md` as reference)
3. Include: Usage, Behavior, Output Format, Examples
4. Update `CLAUDE.md` to list the new command
5. Update `README.md` command table
6. Update `index.html` commands section

### Adding a New Rule
1. Create a numbered `.md` file in `.claude/rules/`
2. Include frontmatter: `description`, `globs`
3. Keep rules concise and actionable
4. Update `CLAUDE.md` rules listing

### Adding a New Skill
1. Create a directory in `.claude/skills/<skill-name>/`
2. Add `SKILL.md` with frontmatter: `name`, `description`, `allowed-tools`
3. Write clear trigger conditions in the description
4. Update `CLAUDE.md` skills listing

### Adding a Design Reference
1. Create a numbered `.md` file in `.claude/knowledge/design-references/`
2. Follow the existing format: Requirements, Architecture, Key Decisions, Schema, Scaling
3. Update `INDEX.md` in the same directory

### Adding a Template
1. Create a `.md` file in `.claude/skills/design-system/templates/`
2. Include: Architecture, Tech Stack, Schema, API, Customize section

## Code of Conduct
- Be respectful and constructive
- Focus on improving the plugin for everyone
- Back claims with evidence or references

## Testing Your Changes
Before submitting:
1. Open the project in Claude Code
2. Test the affected commands with a sample system design
3. Verify the output matches the expected format
4. Check that numbers in CLAUDE.md, README.md, and index.html are consistent

## Pull Request Process
1. Fork the repository
2. Create a feature branch (`git checkout -b feature/new-command`)
3. Make your changes
4. Update documentation (CLAUDE.md, README.md, index.html, CHANGELOG.md)
5. Submit a pull request with a clear description

## Questions?
Open a GitHub Issue with the "question" label.
