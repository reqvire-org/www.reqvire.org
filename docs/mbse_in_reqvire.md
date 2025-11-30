---
layout: page
title: false
---

## Reqvire's MBSE Integration: Overview

Reqvire brings true Git-native Model-Based Systems Engineering to modern engineering teams.

### What is MBSE?

Model-Based Systems Engineering (MBSE) is a methodology that shifts from traditional document-based approaches to using formal, digital models as the primary means of representing, analyzing, and verifying complex systems. It emphasizes creating interconnected models of system requirements, architecture, behavior, and interfaces to improve traceability, reduce errors, and support collaborative development—especially in fields like aerospace, automotive, and software engineering.

### MBSE in Reqvire

Reqvire integrates MBSE by embedding model-driven principles directly into its Git-native "Requirements-as-Context" framework. Rather than treating models as siloed artifacts in specialized tools (e.g., SysML diagramming software), Reqvire unifies them with requirements management and AI-assisted development in a single, version-controlled Git repository. This creates a "living" system model that's queryable by AI tools (like the Claude Code plugin) and enforceable across the development lifecycle.

The core idea: **Requirements act as the canonical source of truth**, while system models provide structured representations of how those requirements translate into system behavior and architecture. This unification enables automated workflows that keep everything aligned, traceable, and adaptable to changes.

## How It Works: Key Mechanisms
Reqvire's MBSE features are designed for seamless integration without disrupting Git workflows. Here's how it unifies requirements, system models, and AI:

1. **Version-Controlled System Modeling**  
   - System models are stored as structured, executable artifacts in Git (e.g., using formats like YAML or JSON for requirements links, or integrated with tools for behavioral diagrams).  
   - Models represent system architecture, interfaces, and dynamics, ensuring they're "living" documents that evolve with code branches, pull requests, and CI/CD pipelines.

2. **Bidirectional Traceability**  
   - Automatically links requirements to model elements, code, tests, and other artifacts.  
   - For example, a change in a requirement instantly highlights impacted model components, preventing drift and enabling full lineage tracking from spec to implementation.

3. **Smart Change Propagation**  
   - When requirements evolve, Reqvire identifies downstream effects on the system model (e.g., updating behaviors or interface definitions).  
   - This propagates changes consistently across the repo, reducing manual rework and errors.

4. **Verification and Validation**  
   - Tracks coverage of verifications (e.g., tests proving a model element meets requirements) and validations (e.g., ensuring the overall system behaves as specified).  
   - Supports specification-driven development, where generated code or simulations remain tied to originating model/requirement nodes.

5. **AI-Enhanced Reasoning**  
   - AI tools query the unified context (requirements + models) to suggest model refinements, generate compliant code, or simulate behaviors.  
   - Integration with plugins like Claude allows natural-language interactions, e.g., "Refine the control system model based on updated safety requirements."

## Example Workflow in Reqvire
A typical MBSE-driven workflow might look like this:

1. **Define Requirements**: Author structured specs in Git/.
2. **Build System Model**: Create linked model elements or derived requirements (e.g., state machines or block definitions) using reqvire cli or AI codings assistants.
3. **Trace and Link**: Use Reqvire commands to analyse traces, coverages and understand model.
4. **AI-Assisted Iteration**: Query via Claude: "Generate test cases for this subsystem model," pulling context from the unified repo.  
5. **Propagate Changes**: Edit a requirement; Reqvire flags model updates needed and automates propagation.  
6. **Validate in PR**: During code review, visualize traceability and change impact to confirm compliance or iterate over required updates.
7. **Merge and Deploy**: Full audit trail ensures the deployed system matches the model.

## Benefits for Engineering Teams
- **Eliminates Outdated Docs**: Models and requirements stay synchronized in Git, avoiding the "doc rot" common in traditional MBSE.  
- **Boosts Efficiency**: Automation cuts manual tracing by 50–70% (based on similar tools), accelerating delivery.  
- **Enhances Quality**: Full traceability and validation reduce compliance risks in regulated industries.  
- **Scales Collaboration**: Git-native design fits DevOps teams, with AI lowering the MBSE learning curve.  
- **Reduces Misalignment**: Developers and modelers work from one shared context, minimizing handoffs.

In essence, Reqvire democratizes MBSE by making it lightweight and developer-friendly, turning complex systems engineering into an intelligent, traceable extension of everyday Git workflows.

For hands-on details, check the [installation guide](https://www.reqvire.org/user_guide#installation) or [Claude plugin docs](https://www.reqvire.org/claude_plugin.html).
