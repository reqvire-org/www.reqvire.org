---
layout: page
nav_order: 8
title: Submodels and Subgraphs
---

# Submodels and Subgraphs

Reqvire `submodels` report helps you analyze **independent capability-rooted subgraphs** inside the engineering knowledge graph and the couplings between them.

## What Is a Submodel

A submodel is a capability-rooted graph:

- Capability hierarchy is resolved through capability `derive` / `derivedFrom`.
- Requirements enter the graph through `specifiedBy` / `specify`.
- Requirement hierarchy is resolved through requirement `derive` / `derivedFrom`.
- In full mode (`reqvire submodels`), each submodel is rooted at a capability with no capability parent.
- In scoped mode (`reqvire submodels --from "<ROOT_NAME>"`), the selected capability or requirement becomes the scope boundary and is not itself reported as a submodel entry.
- Scoped submodels are the first independent branch roots below the selected scope.

## Why It Is Useful

Use this report to:

- detect unnecessary coupling between independent model areas,
- review architecture boundaries before refactors,
- validate attachment-boundary modeling practices,
- keep impact and context flows understandable for humans and AI assistants.
- preserve clean boundaries between ontology-driven capability areas.

## Cross-Submodel Couplings

The report includes requirement-to-requirement relations where source and target belong to different capability roots.

This makes cross-boundary dependencies explicit and reviewable.

Use `reqvire lint --auditable` / `reqvire lint --auditable --json` to get remediation-ready hints for individual cross-capability links before applying structural changes.

## Commands

```bash
# Full report
reqvire submodels

# JSON report
reqvire submodels --json

# Scope by one capability or requirement name
reqvire submodels --from "API Authentication"
```

## Output Shape

Text report sections:

- `Submodels`
- `Cross-Submodel Couplings`
- `Summary`

JSON fields:

- `submodels`
- `cross_submodel_couplings`
- `summary`

## Related Pages

- [User Guide](user_guide.md#submodels-report)
- [Modeling Language](modeling_language.md)
