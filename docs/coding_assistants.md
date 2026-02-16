---
layout: page
nav_order: 2
title: Coding Assistants
---

## Overview

---

Reqvire integrates with coding assistants through two main approaches:

1. **Claude Code plugin** (marketplace plugin with slash commands and skills)
2. **Codex skill package** (installable to `$CODEX_HOME/skills`)

Both approaches support MBSE-first, requirements-as-code workflows.

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
- `/reqvire:add-feature`
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

## Recommended Workflow (Any Assistant)

---

1. **Requirements first**: define or update requirements.
2. **Refinements**: add specs/constraints/behaviors.
3. **Verifications**: ensure leaf requirements are verified.
4. **Implementation links**: connect code/tests with `satisfiedBy`.
5. **Validate**: run `reqvire validate`, `reqvire lint`, `reqvire coverage`.

## Example Assistant Prompts

---

- "Show unverified leaf requirements and propose verifications."
- "Analyze change impact against `main` and summarize invalidated verifications."
- "Create implementation tasks for the impacted requirements with traceability."
- "Refactor this requirement to extract technical details into specification elements."

## Use Cases

---

- Requirements model exploration and navigation
- Coverage and traceability analysis
- Change impact assessment
- MBSE-guided feature planning and implementation task generation
- Refactoring and consolidation of requirement structures
