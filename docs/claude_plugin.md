---
layout: page
title: Claude Code Plugin
---

## Table of Contents
{:.no_toc}

* TOC
{:toc}

---

## Reqvire Plugin for Claude Code

Reqvire offers a plugin for Claude Code, bringing AI-native requirements engineering capabilities directly into your Claude Code workflow through the reqvire-org marketplace.

## What's Included

The Reqvire plugin for Claude Code provides:

- **Specialized Skills**: AI agents optimized for requirements engineering and task planning
- **Slash Commands**: Quick access to model analysis, verification management, and coverage reporting
- **Seamless Integration**: Work with your requirements model directly within Claude Code

### Skills

The plugin includes two specialized skills that Claude uses automatically when relevant:

1. **syseng** - Expert System and Requirements Engineer for exploring, analyzing, and managing MBSE models using Reqvire. Helps with adding new requirements, managing the model, adding new capabilities and features, understanding specifications, browsing requirements, analyzing model structure, checking verification coverage, and providing guidance on requirements-as-code methodology.

2. **task-master** - Expert at analyzing requirement changes, understanding what changed in specifications, and creating actionable implementation plans. Use when you need to understand what requirements changed, generate task lists from change-impact analysis, or plan implementation work with traceability.

### Commands

The plugin provides the following slash commands (all prefixed with `reqvire:`):

- `/reqvire:analyze-model` - Analyze your complete requirements model structure
- `/reqvire:add-requirement` - Add new requirements with proper traceability
- `/reqvire:add-verification` - Add verification elements linked to requirements
- `/reqvire:add-feature` - Add a complete feature with requirements and verifications
- `/reqvire:analyze-coverage` - Generate verification coverage reports
- `/reqvire:analyze-impact` - Analyze change impact across the model
- `/reqvire:lint-model` - Check model quality and detect issues
- `/reqvire:generate-tasks` - Generate implementation tasks from requirements
- `/reqvire:find-redundant-verifications` - Identify redundant verification relations

## Installation

### Prerequisites

Before installing the plugin, ensure you have:

1. **Claude Code** installed (available at [claude.com/claude-code](https://claude.com/claude-code))
2. **Reqvire CLI** installed on your system

To install the Reqvire CLI:

```bash
curl -fsSL https://raw.githubusercontent.com/reqvire-org/reqvire/main/scripts/install.sh | bash
```

For detailed CLI installation options, see the [Installation Guide](https://www.reqvire.org/installation).

### Installing the Plugin from GitHub

The Reqvire plugin is available through the reqvire-org marketplace for Claude Code:

1. Open Claude Code
2. Navigate to Settings > Marketplace
3. Add the reqvire-org GitHub repository as a marketplace source:
   ```
   https://github.com/reqvire-org/reqvire
   ```
4. Browse the marketplace and find "reqvire"
5. Click "Install" on the Reqvire plugin
6. Restart Claude Code to activate the plugin

## Getting Started

Once installed, you can immediately start using Reqvire's capabilities:

### Using Skills

Skills are specialized AI capabilities that work behind the scenes. You don't need to explicitly invoke them—just interact with Claude naturally, and the appropriate skill will activate based on your request.

**syseng skill** activates when you:
- Ask questions about your requirements model structure
- Request help adding or modifying requirements
- Need to understand verification coverage gaps
- Want guidance on MBSE best practices
- Ask to analyze traceability or relationships

**Example interactions:**
- "Show me which requirements aren't verified"
- "Help me add a new authentication requirement"
- "What's wrong with my model structure?"

**task-master skill** activates when you:
- Ask about what requirements have changed
- Need an implementation plan for new features
- Want a breakdown of tasks from requirement changes
- Request work items with traceability links

**Example interactions:**
- "What requirements changed in this branch?"
- "Generate implementation tasks for these changes"
- "Create a plan for implementing the new feature"

### Using Commands

Slash commands are invoked by typing them in the Claude Code chat with the `reqvire:` prefix:

```
/reqvire:analyze-model
```

This will analyze your complete requirements model and provide insights into structure, traceability, and potential issues.

```
/reqvire:analyze-coverage
```

This generates a verification coverage report showing which requirements are verified and which need attention.

## Use Cases

### Requirements Engineering

Use the plugin to:
- Analyze requirements structure and relationships
- Ensure proper traceability between requirements
- Validate model consistency
- Generate coverage and impact reports

### AI-Assisted Development

The plugin enhances Claude's understanding of your project by:
- Providing context about system requirements
- Understanding architectural relationships
- Ensuring changes align with requirements
- Generating implementation tasks from requirements

### MBSE Workflow

Support Model-Based Systems Engineering practices:
- Maintain requirements-code traceability
- Enforce requirements-first development
- Validate verification coverage
- Track change propagation

## Examples

### Analyzing Your Model

```
/reqvire:analyze-model
```

Claude will analyze your requirements structure, identify relationships, and provide insights into model quality.

### Adding a New Feature

```
/reqvire:add-feature user-authentication
```

Claude will guide you through adding a complete feature with requirements, verifications, and proper traceability.

### Checking Coverage

```
/reqvire:analyze-coverage
```

Get a detailed report showing verification coverage across all requirements, helping identify gaps in testing and validation.

### Generating Implementation Tasks

```
/reqvire:generate-tasks
```

Automatically generate a comprehensive task breakdown from requirement changes, with full traceability links and test verification steps.

## Learn More

- [User Guide](./user_guide.md) - Complete guide to using Reqvire
- [Requirements Documentation](./requirements.md) - Understanding requirements structure
- [Verifications](./verifications.md) - Understanding verification elements
- [GitHub Repository](https://github.com/reqvire-org/reqvire) - Source code and plugin files

## Support

If you encounter issues or have questions:

- [Open an issue](https://github.com/reqvire-org/reqvire/issues) on GitHub
- [Join discussions](https://github.com/reqvire-org/reqvire/discussions) with the community
- Visit our [documentation](https://www.reqvire.org) for detailed guides
