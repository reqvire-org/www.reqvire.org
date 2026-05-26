---
layout: page
nav_order: 10
title: Coding Assistants
---

## Overview

---

Reqvire integrates with coding assistants by exposing the engineering knowledge graph as structured, AI-ready context:

1. **Claude Code plugin** (marketplace plugin with slash commands and skills)
2. **Codex skill package** (installable to `$CODEX_HOME/skills`)
3. **MCP server** (standard tool interface for MCP-capable clients)

All approaches support ontology-driven engineering, capability-driven planning, requirements-as-code, semantic traceability, and AI-native MBSE workflows.

## Prerequisites

---

Before using either integration, ensure you have:

1. **Reqvire CLI** installed
2. A repository initialized with Reqvire model files

Install Reqvire CLI:

```bash
curl -fsSL https://raw.githubusercontent.com/reqvire-org/reqvire/main/scripts/install.sh | bash
```

For detailed options, see the [Installation Guide](https://www.reqvire.org/user_guide#installation).

## Option 1: Claude Code Plugin

---

### Install

1. Ensure **Claude Code** is installed (see [claude.com/claude-code](https://claude.com/claude-code)).
2. Add marketplace:

```text
/plugin marketplace add https://github.com/reqvire-org/reqvire
```

3. Install plugin:

```text
/plugin install reqvire@reqvire-org
```

4. Restart Claude Code.

### What You Get

- Specialized skills (`syseng`, `task-master`)
- Slash commands (`/reqvire:*`) for model operations
- Native chat-driven MBSE workflows in Claude Code

### Main Slash Commands

- `/reqvire:analyze-model`
- `/reqvire:add-requirement`
- `/reqvire:add-verification`
- `/reqvire:add-capability`
- `/reqvire:analyze-coverage`
- `/reqvire:analyze-impact`
- `/reqvire:collect`
- `/reqvire:lint-model`
- `/reqvire:consolidate`
- `/reqvire:generate-tasks`
- `/reqvire:find-redundant-verifications`
- `/reqvire:rename-element`
- `/reqvire:mv`
- `/reqvire:mv-file`
- `/reqvire:rm`

## Option 2: Codex Skill

---

Reqvire includes a Codex skill package in this repository at:

- `codex-skills/reqvire-syseng`

Install it globally on your machine:

```bash
./scripts/install-codex-skill.sh
```

This installs the skill to:

- `$CODEX_HOME/skills/reqvire-syseng`
- default `CODEX_HOME` is `~/.codex`

The installer removes any existing `reqvire-syseng` before copying the latest repo version.

For complete instructions, see [Codex Skills Guide](https://github.com/reqvire-org/reqvire/blob/main/doc/CODEX_SKILLS.md).

## Option 3: MCP Server

---

Reqvire can run as an MCP server for assistants that discover and call tools through the Model Context Protocol:

```bash
reqvire mcp
```

The MCP server uses RMCP Streamable HTTP and advertises read/report tools by default. Mutation tools are only advertised when started with `--enable-mutations`.

For assistants that need context-sizing metadata, start the server with `--with-size-estimates`. This adds `size_estimate` records to MCP model evidence returned by tools such as `reqvire.read_element` and `reqvire.model`.

For semantic model workflows, call `reqvire.ontologies` through MCP to collect ontology `#### Ontology` blocks and semantic-contract `#### Shapes` blocks as Turtle or JSON-LD. Pass `full: true` when the assistant also needs generated Reqvire model context triples for elements, relations, attachments, concept references, ontology declarations, and shape references.

For Streamable HTTP request details and the active tool contract, see the [MCP Server](/mcp_server/) guide.

## Recommended Workflow (Any Assistant)

---

1. **Capability first**: understand or define the operational ability being changed.
2. **Ontology context**: attach or inspect semantic vocabulary that gives the capability meaning.
3. **Requirements**: define implementable obligations that specify the capability.
4. **Refinements**: add specs, constraints, behaviors, states, I/O contracts, or semantic contracts.
5. **Verifications**: ensure capabilities or leaf requirements are verified.
6. **Implementation links**: connect code, tests, proofs, and evidence with `satisfiedBy`.
7. **Validate**: run `reqvire validate`, `reqvire lint`, `reqvire coverage`.

## Example Assistant Prompts

---

- "Show unverified leaf requirements and propose verifications."
- "Explain the capability, ontology context, requirements, and verification evidence for this change."
- "Analyze change impact against `main` and summarize invalidated verifications."
- "Create implementation tasks for the impacted requirements with traceability."
- "Refactor this requirement to extract technical details into specification elements."

## Use Cases

---

- Engineering knowledge graph exploration and navigation
- Coverage and traceability analysis
- Change impact assessment
- MBSE-guided capability planning and implementation task generation
- Ontology-driven context collection for AI assistants
- Refactoring and consolidation of requirement structures
