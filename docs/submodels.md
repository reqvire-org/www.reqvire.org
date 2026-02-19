---
layout: page
nav_order: 9
title: Submodels and Subgraphs
---

# Submodels and Subgraphs

Reqvire `submodels` report helps you analyze **independent requirement subgraphs** and the couplings between them.

## What Is a Submodel

A submodel is a requirement hierarchy resolved through `derivedFrom` relations.

- In full mode (`reqvire submodels`), each submodel is rooted at a top requirement with no parent.
- In scoped mode (`reqvire submodels --from "<ROOT_NAME>"`), the selected root becomes the scope boundary and is not itself reported as a submodel entry.
- Scoped submodels are the first independent branch roots below the selected root.

## Why It Is Useful

Use this report to:

- detect unnecessary coupling between independent model areas,
- review architecture boundaries before refactors,
- validate attachment-boundary modeling practices,
- keep impact and context flows understandable for humans and AI assistants.

## Cross-Submodel Couplings

The report includes requirement-to-requirement relations where source and target belong to different resolved top roots.

This makes cross-boundary dependencies explicit and reviewable.

Use `reqvire lint --auditable` / `reqvire lint --auditable --json` to get remediation-ready hints for individual cross-root links before applying structural changes.

## Commands

```bash
# Full report
reqvire submodels

# JSON report
reqvire submodels --json

# Scope by one root name
reqvire submodels --from "Platform System"
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
