---
layout: home
nav_order: 1
title: About Reqvire
permalink: /
---

# What is Reqvire?
{: .fs-9 }

**Reqvire** is a lightweight, Git-native **Requirements-as-Context** framework that redefines how modern engineering teams design, build, and maintain software.
{: .fs-6 .fw-300 }

It unifies system modeling, requirements management, context engineering and AI-assisted development into a single workflow—providing complete traceability, intelligent automation, and faster delivery while staying fully aligned with your codebase.

[View it on GitHub](https://github.com/reqvire-org/reqvire){: .btn .fs-5 .mb-4 .mb-md-0 }



## Key Features

---

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

---

Reqvire uses a lightweight, human- and AI-friendly modeling language built on **semi-structured Markdown**.

### Why Semi-Structured Markdown?
Choosing Markdown as the foundation gives Reqvire several superpowers:

- **Zero friction for AI** – LLMs (Claude, ChatGPT, Cursor, local models, etc.) can read, write, and reason about requirements and models without custom parsers  
- **Git-native from day one** – models live as regular `.md` files, fully diffable and reviewable in pull requests  
- **Instant validation & automation** – simple rules make it trivial to lint, auto-format, and generate artifacts  
- **No learning curve** – engineers already know Markdown; no new DSL or proprietary syntax to learn  

The result: requirements and system models that are simultaneously precise enough for traceability and loose enough for everyday editing and AI assistance.


## Human in Control — Always

---

While Reqvire empowers AI tools to act as smart collaborators, the **human engineer remains the system’s captain** — setting direction, making decisions, and approving outcomes.

Reqvire ensures that:
- Every AI-suggested change is traceable and reviewable
- System evolution remains understandable and documented
- AI tooling acts in service of the engineer’s intent, not in place of it

## Diagram & Traceability Automation

---

Reqvire automatically parses structured Markdown to generate:
- **Architecture diagrams** (component hierarchies, interfaces, dependencies)
- **Traceability reports** linking requirements to architecture, verifications, and tests
- **Impact reports** showing what was changed, and what it affects
- **Requirement flow diagrams** visualizing hierarchical relationships and derivations
- **Verification coverage maps** showing which leaf requirements are verified and how
- **Change propagation analysis** identifying all downstream effects of modifications
- **Model summary reports** providing overview statistics and health metrics
- **Submodels/subgraphs analysis** exposing independent hierarchies and cross-boundary couplings
- **Interactive HTML documentation** with clickable diagrams and searchable content

## CI/CD Integration

---

Reqvire is designed for automation. In typical Git-based workflows, you can:

- **Validate Markdown structure** using static checks
- **Generate traceability and change impact reports** for stakeholder review
- **Block PRs** if requirements are incomplete or broken
- **Package models** into versioned documentation releases
- **Integrate with GitHub Actions/GitLab CI** for automated validation
