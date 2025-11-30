---
layout: page
---

## What is Reqvire?

**Reqvire** is a lightweight, Git-native **Requirements-as-Context** framework that redefines how modern engineering teams design, build, and maintain software.

It unifies system modeling, requirements management, and AI-assisted development into a single workflow—providing complete traceability, intelligent automation, and faster delivery while staying fully aligned with your codebase.


## Key Features

### **Requirements-as-Context**
Create a structured, canonical source of truth for your project.  
Reqvire provides a consistent context layer that AI tools can reliably query—so they always understand what needs to be built, where changes belong, and how system components fit together.

### **Requirements-as-Code**
Evolve requirements from static documents into version-controlled, executable artifacts.  
Reqvire keeps your system model alive in Git—no more lost specs, outdated docs, or knowledge locked in transient tickets.

### **Intelligent Engineering**
Bring Model-Based Systems Engineering (MBSE) directly into your Git workflow:

- **Specification-Driven Development**  
  Develop from requirements. Enforce clear specifications and generate code that stays tied to its originating requirements.

- **Automated Traceability**  
  Maintain instant, bidirectional links between requirements, code, tests, and artifacts.

- **Verification & Validation**  
  Track verifications, ensure coverage, and validate that implementations meet intended behavior.

- **Smart Change Propagation**  
  Identify impacted parts of the system and help propagate requirement changes consistently across the model and codebase.

- **Seamless Integration**  
  Works naturally with branches, pull requests, reviews, and CI/CD. No workflow disruption—just added intelligence.


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

