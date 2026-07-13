# dotagents

A centralized repository for managing AI agent rules, behavior guidelines, and coding standards.

By defining project-specific rules in a structured format under the **[`.agents`](.agents/)** directory, this repository ensures that AI coding assistants (such as Gemini Antigravity, Cursor, and others) align with the team's development workflows, communication protocols, and technical conventions.

> [!NOTE]
> The configurations and rules defined in this repository are opinionated and tailored to the personal preferences of [@adhemukhlis](https://github.com/adhemukhlis). You may need to adjust them to fit your team's workflow and styling guidelines before adopting.

## Repository Structure

```text
.
├── .agents/
│   └── rules/
│       ├── language-protocol.md      # Defines communication and deliverable language rules
│       └── typescript/
│           └── code-style-guide.md   # Enforces TypeScript clean code and styling standards
└── .gitignore
```

### Key Components

- **[Language Protocol](.agents/rules/language-protocol.md)**: Standardizes how the AI agent communicates. It enforces Bahasa Indonesia for conversational discussions and chats, while requiring Professional Technical English for all code, comments, documentation, commits, and pull requests.
- **[TypeScript Code Style Guide](.agents/rules/typescript/code-style-guide.md)**: Outlines rules for clean code (e.g., guard clauses, single responsibility), strict typing (disallowing `any`, preferring `type` over `interface`), runtime validation (via Zod/Valibot), and error handling. It also provides reference configurations for `tsconfig.json` and ESLint.

## Getting Started

### Using the Rules in Your Workspace

To apply these rules to your local development environment, copy or symlink the `.agents` folder into the root of your project:

```bash
# Clone the repository
git clone https://github.com/adhemukhlis/dotagents.git

# Symlink the .agents folder to your project root
ln -s /path/to/dotagents/.agents /path/to/your/project/.agents
```

AI agents that support loading instructions from workspace configuration files will automatically detect and follow these rules.

## Adding New Rules

When creating a new rule under `.agents/rules/`, use standard Markdown formatting and include YAML frontmatter at the top of the file:

```markdown
---
title: Rule Name
description: Brief description of what the rule enforces.
trigger: always_on | on_demand
---

# Rule Title

- Guideline 1
- Guideline 2
```
