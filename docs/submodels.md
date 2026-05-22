---
layout: page
nav_order: 9
title: Submodels and Subgraphs
---

# Submodels and Subgraphs

Reqvire `submodels` report helps you analyze **independent feature-rooted subgraphs** and the couplings between them.

## What Is a Submodel

A submodel is a feature-rooted graph:

- Feature hierarchy is resolved through feature `derive` / `derivedFrom`.
- Requirements enter the graph through `specifiedBy` / `specify`.
- Requirement hierarchy is resolved through requirement `derive` / `derivedFrom`.
- In full mode (`reqvire submodels`), each submodel is rooted at a feature with no feature parent.
- In scoped mode (`reqvire submodels --from "<ROOT_NAME>"`), the selected feature or requirement becomes the scope boundary and is not itself reported as a submodel entry.
- Scoped submodels are the first independent branch roots below the selected scope.

## Why It Is Useful

Use this report to:

- detect unnecessary coupling between independent model areas,
- review architecture boundaries before refactors,
- validate attachment-boundary modeling practices,
- keep impact and context flows understandable for humans and AI assistants.

## Cross-Submodel Couplings

The report includes requirement-to-requirement relations where source and target belong to different feature roots.

This makes cross-boundary dependencies explicit and reviewable.

Use `reqvire lint --auditable` / `reqvire lint --auditable --json` to get remediation-ready hints for individual cross-feature links before applying structural changes.

## Commands

```bash
# Full report
reqvire submodels

# JSON report
reqvire submodels --json

# Scope by one feature or requirement name
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
