# Contributing to Startup Bookkeeper

Thanks for your interest in contributing! This skill is designed to help startup founders and small business owners track their finances through conversation with Claude. Here's how you can help make it better.

## Ways to Contribute

### Report Issues
- Found a scenario where the skill behaves unexpectedly? Open an issue.
- Include the prompt you used, what happened, and what you expected.

### Suggest Features
- Open an issue with the `enhancement` label.
- Describe the use case: who needs it and why.

### Submit Changes
1. Fork the repository
2. Create a feature branch (`git checkout -b feature/bank-csv-templates`)
3. Make your changes
4. Test by installing the skill locally and running through the example conversations
5. Submit a pull request with a clear description of what changed and why

## Guidelines

### Skill Design Principles
- **Conversational first**: Any new feature should work through natural language. Avoid requiring users to remember specific commands or syntax.
- **Confirm before committing**: Destructive actions (deletes, bulk changes) always need user confirmation.
- **Fail gracefully**: Handle edge cases with helpful messages, not crashes.
- **Stay in scope**: This is a tracking tool, not accounting software. Don't add features that require professional accounting knowledge to use correctly.

### What Makes a Good PR
- Solves a real problem that founders or small business owners face
- Includes example conversation flows showing the feature in action
- Keeps the SKILL.md under 500 lines (move details to `references/` if needed)
- Doesn't break existing functionality

### Ideas We'd Love Help With
- **Bank CSV parsers**: Templates for common banks (Chase, Mercury, Brex, Wise, etc.)
- **Industry category packs**: Pre-built categories for specific verticals (healthcare, ecommerce, consulting, etc.)
- **Budget templates**: Starter budgets for common startup stages
- **Localization**: Adapt P&L format, categories, and tax terminology for non-US markets
- **Dashboard improvements**: Better chart types, additional visualizations, mobile UX

## Code of Conduct

Be kind, be constructive, and remember that this project exists to help people who are building something. We're all in the same boat.
