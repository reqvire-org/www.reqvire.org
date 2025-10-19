---
layout: page
title: AI Compatibility
---

## Table of Contents
{:.no_toc}

* TOC
{:toc}

---

## AI Compatibility

Reqvire is built to be **AI-friendly from the ground up**.

### Example AI Use Cases

#### 1. Requirement Analysis

AI tools can analyze Markdown-based requirements to identify missing elements, suggest clearer phrasing, propose edge cases, and generate acceptance criteria or verification steps.

#### 2. Architecture Suggestion

By understanding relationships in architecture files, AI can detect inconsistent component relationships, propose modular design improvements, and suggest missing interfaces or dependencies.

#### 3. Traceability & Impact Prediction

AI tools can automatically trace which tests or requirements are impacted by changes, flag affected downstream areas, and summarize model diffs for engineering teams.

#### 4. Test Coverage Assistance

Based on requirements and use cases, AI can recommend missing test scenarios and create verifications based on test criteria extracted from requirements chain.

#### 5. Code Generation Assistance

AI can leverage structured requirements for:

- **Context-Aware Generation**: Understanding the "why" behind every component through Reqvire's traceability structure
- **Specification-Driven Coding**: Generating code directly tied to specific requirements, reducing guesswork or misinterpretation
- **Verification-Backed Validation**: Referring to defined verifications to ensure implementation meets intended behavior
- **Trace-Based Refactoring**: Assisting in propagating requirement changes through the codebase and related artifacts

#### 6. Report Generation

AI can automatically generate traceability reports, summarize system architecture, and prepare documentation for releases or reviews.

### Human in Control — Always

While Reqvire empowers AI tools to act as smart collaborators, the **human engineer remains the system's captain** — setting direction, making decisions, and approving outcomes.

Reqvire ensures that:

- Every AI-suggested change is traceable and reviewable
- System evolution remains understandable and documented
- AI tooling acts in service of the engineer's intent, not in place of it

### Integration with AI Tools

#### Claude Code Plugin

Reqvire offers a dedicated [Claude Code plugin](./claude_plugin.md) that brings AI-assisted requirements engineering directly into your workflow with specialized skills and commands for model management.

#### AI Assistants and Copilots

Your Reqvire requirements work seamlessly with:

- **ChatGPT** - Analyze requirements, suggest improvements, generate test scenarios
- **Claude** - Deep requirement analysis, traceability review, impact assessment
- **GitHub Copilot** - Context-aware code generation based on requirements
- **Cursor** - Intelligent refactoring with requirement awareness
- **Local models** - Local deployments can consume Reqvire models directly

### Best Practices for AI-Assisted Requirements Engineering

**1. Use Requirements as Context**

Include relevant requirements files in your AI context to get specification-aware responses:
```
Analyze this code against requirements in specifications/Authentication.md
```

**2. Leverage Traceability**

Ask AI to follow requirement chains:
```
Show me all verifications for requirement REQ-AUTH-001 and its derived requirements
```

**3. Generate Implementation Plans**

Let AI create task breakdowns from requirements:
```
Create an implementation plan for the requirements in this file
```

**4. Review Change Impact**

Use AI to analyze how changes affect related elements:
```
If I modify requirement REQ-DATA-005, what else might be impacted?
```

**5. Validate Consistency**

Ask AI to check for inconsistencies:
```
Review these requirements for conflicts or missing relations
```

### Why Reqvire Works Well with AI

**Structured but Readable**
- Semi-structured Markdown is easily parsed by AI tools
- No complex DSLs or proprietary formats
- Natural language requirements with clear structure

**Explicit Relationships**
- Clear parent-child hierarchies
- Defined verification links
- Traceable implementation connections

**Self-Documenting**
- Requirements serve as both spec and documentation
- Relations make dependencies explicit
- History preserved in Git

**Context-Rich**
- Each element contains description and rationale
- Relations provide full context graph
- Verifications define expected behavior
