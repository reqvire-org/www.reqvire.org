---
layout: home
nav_order: 1
title: About Reqvire
permalink: /
---

# What is Reqvire?
{: .fs-9 }

**Reqvire** is a semantic engineering graph for building verifiable and traceable systems.
{: .fs-6 .fw-300 }

It connects ontologies, capabilities, requirements, refinements, verifications, and implementation evidence directly inside your Git workflow.

[View it on GitHub](https://github.com/reqvire-org/reqvire){: .btn .fs-5 .mb-4 .mb-md-0 }

## Engineering Knowledge Graph

Reqvire is an engineering knowledge graph for systems development. It connects the engineering knowledge that usually lives in disconnected documents, tickets, spreadsheets, diagrams, tests, and code:

- **Ontologies** define domain concepts, relationships, vocabulary, and semantic meaning.
- **Capabilities** describe coherent operational, product, business, regulatory, or system abilities.
- **Requirements** define implementable obligations, constraints, guarantees, and behavioral expectations.
- **Refinements** turn requirements and capabilities into precise engineering contracts.
- **Verifications** provide evidence that requirements and capabilities are satisfied.
- **Implementation artifacts** connect the model to code, tests, proofs, and generated evidence.

```text
Ontology
    ↓
Capabilities
    ↓
Requirements
    ↓
Refinements
    ↓
Verification
    ↓
Implementation evidence
```

Unlike traditional requirements tools, Reqvire treats engineering knowledge as a living semantic engineering graph rather than disconnected documents. The graph stays versioned, reviewable, queryable, and aligned with the codebase.

Reqvire naturally sits between:

- SysML and MBSE
- knowledge graphs
- semantic engineering
- context engineering
- AI-native development infrastructure

## Why Reqvire?

Traditional engineering knowledge is fragmented across:

- documents
- tickets
- architecture diagrams
- code
- spreadsheets
- wikis
- test systems

That fragmentation creates stale specifications, broken traceability, inconsistent semantics, disconnected verification, weak AI context, and difficult change propagation.

Reqvire keeps the model in Git so normal engineering workflows still work: branches, pull requests, reviews, CI, diffs, and audit history.

## AI-Native Engineering

Reqvire models are designed for both humans and AI systems. The semi-structured modeling language gives AI assistants a stable context layer they can reason over:

- semantic search
- traceability analysis
- impact analysis
- architecture-aware code generation
- AI-assisted verification
- context-aware task planning
- intelligent change propagation

Reqvire does not replace engineering judgment. It gives engineers and assistants a shared, traceable model of intent, obligations, evidence, and implementation context.

## Key Capabilities

### Semantic Engineering in Git

Keep engineering knowledge versioned, traceable, reviewable, AI-readable, and semantically connected alongside implementation artifacts.

### Capability-Driven Engineering

Develop systems around coherent operational capabilities instead of disconnected feature lists or isolated requirement statements. Capabilities decompose hierarchically, connect to ontology concepts, generate requirements, support verification, and remain stable across implementation changes.

### Ontology-Driven Engineering

Bring semantic vocabulary and domain meaning directly into the engineering workflow. Ontologies make terminology explicit, reusable, and visible to humans and AI systems.

### Specification-Driven Development

Develop from capabilities and requirements while keeping implementation, architecture, tests, and verification artifacts aligned with engineering intent.

### Automated Traceability

Maintain links between ontologies, capabilities, requirements, refinements, code, tests, proofs, and verification artifacts.

### Verification and Validation

Track verification coverage, behavioral correctness, implementation alignment, and validation evidence throughout the lifecycle.

### Smart Change Propagation

Identify impacted requirements, capabilities, verifications, implementation artifacts, and semantic dependencies before changes drift out of alignment.

## Automation

Reqvire can generate:

- model diagrams and structural views
- traceability reports
- verification and implementation coverage reports
- change impact reports
- submodel and coupling analysis
- semantic ontology exports
- interactive HTML documentation
- MCP tools for AI assistants

## Next Steps

- [User Guide](user_guide.md)
- [Strategic Vision](mbse_in_reqvire.md)
- [Engineering Graph](requirements.md)
- [Modeling Language](modeling_language.md)
- [Verifications](verifications.md)
- [Coding Assistants](coding_assistants.md)
