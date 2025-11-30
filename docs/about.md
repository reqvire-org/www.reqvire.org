---
layout: page
title: About
---

## What is Reqvire?

**Reqvire** is a lightweight, Git-native **Requirements-as-Context** framework that transforms how modern engineering teams build software. 

It seamlessly blends system modeling, requirements management, and AI-assisted development, empowering teams to deliver better products faster with complete traceability and intelligent automation.

## Reqvire unlocks

- **Requirements-as-Context**: Establish a single source of truth that structures project context. Reqvire ensures AI tools can retrieve and manage precise information, guiding them to understand precisely what needs to be built, where to make changes, and how all system components connect.
- **Requirements as Code**: Transform requirements from static documents into living, version-controlled assets that evolve with your codebase, ensuring that the foundational system model remains intact and accessible over time, rather than getting lost in transient task documents
- **Intelligent Engineering**: 
  - **Context-Aware Generation**: Understand the "why" behind every component through Reqvire's traceability structure.
  - **Specification-Driven Coding**: Generate code directly tied to specific requirements, reducing guesswork and misinterpretation.
  - **Verification-Backed Validation**: Refer to defined verifications to ensure implementation meets intended behavior.
  - **Trace-Based Refactoring**: Assist in propagating requirement changes efficiently through the codebase and related artifacts.  
- **Effortless Integration**: Plug seamlessly into your existing workflow with Git branches, pull requests, and CI/CD pipelines—no disruption, just enhancement


## Reqvire Modeling Language

Reqvire uses a lightweight, opinionated modeling language based on **semi-structured Markdown**.

### Why Markdown Matters

By sticking to a semi-structured Markdown format:
- AI tools don’t need complex parsers or DSL interpreters
- Models can be used as-is by LLMs like ChatGPT, Claude, or private copilots
- Validation and generation become trivial to automate

---

### Key Elements

Each Reqvire model is made up of Markdown files and elements representing:

- **Requirements** – User needs, constraints, and system-level behaviors.
- **System Structure** – Components, modules, and their interconnections.
- **Behaviors** – States, flows, and operations.
- **Traceability Links** – Relations between requirements, architecture, and test cases.
- **Verification Definitions** – Mappings between requirements and their validation/test strategies.

These documents are written using simple conventions, headers, and embedded tags.

---

### Human in Control — Always

While Reqvire empowers AI tools to act as smart collaborators, the **human engineer remains the system’s captain** — setting direction, making decisions, and approving outcomes.

Reqvire ensures that:
- Every AI-suggested change is traceable and reviewable
- System evolution remains understandable and documented
- AI tooling acts in service of the engineer’s intent, not in place of it

---

## Diagram & Traceability Automation

Reqvire automatically parses structured Markdown to generate:
- **Architecture diagrams** (component hierarchies, interfaces, dependencies)
- **Traceability matrices** linking requirements to architecture, verifications, and tests
- **Impact reports** showing what was changed, and what it affects
- **Requirement flow diagrams** visualizing hierarchical relationships and derivations
- **Verification coverage maps** showing which leaf requirements are verified and how
- **Change propagation analysis** identifying all downstream effects of modifications
- **Compliance matrices** mapping requirements to standards, regulations, or policies
- **Model summary reports** providing overview statistics and health metrics
- **Interactive HTML documentation** with clickable diagrams and searchable content
- **Mermaid diagram exports** for integration with documentation platforms
- **JSON/CSV exports** for integration with external tools and dashboards

---

## CI/CD Integration

Reqvire is designed for automation. In typical Git-based workflows, you can:

- **Validate Markdown structure** using static checks
- **Auto-generate diagrams** during CI builds or PRs
- **Generate traceability and change impact reports** for stakeholder review
- **Block PRs** if requirements are incomplete or broken
- **Package models** into versioned documentation releases
- **Integrate with GitHub Actions/GitLab CI** for automated validation
- **Generate reports** that become part of your release artifacts

