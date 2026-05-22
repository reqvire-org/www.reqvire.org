---
layout: page
nav_order: 4
title: Requirements
---

## Conceptual Overview

Reqvire separates product intent from system obligations:

- **Features** define product capability, stakeholder scope, regulatory context, ownership, and shared ontology context.
- **Ontologies** define reusable domain/model meaning that features attach and requirements inherit.
- **Requirements** define what the system shall do for those features.
- **Refinements** add detailed contracts to features or requirements.
- **Verifications** prove requirements.

This keeps the model close to MBSE practice: features describe the capability and domain context, while requirements remain testable obligations with implementation and verification evidence.

## Features

A `feature` answers:

- What product capability, stakeholder need, regulatory area, external obligation, or domain slice is this?
- Why does this area exist in the product model?
- Who owns the scope and routing for this slice?
- What source material or ontology defines the domain context?
- Which requirements belong under this capability?

Feature:

```text
API Authentication
```

Answers:

- What capability/domain is this?
- Why does this area exist in the product model?
- What stakeholder, regulatory, source, or policy context owns it?
- What ontology defines its domain language?
- Which requirements belong under this capability?

A feature is not a weaker requirement. It is the capability anchor for a product, stakeholder, regulatory, or external context. It defines the scope and language of the model area before individual system obligations are written.

Because a feature owns the capability context, it can own `source` refinements and attach `ontology` elements from `requirements/Ontologies`. Those define vocabulary, source authority, external context, domain structure, and reusable meaning for the capability. They are not implementation obligations by themselves.

Features can form feature hierarchies using `derive` / `derivedFrom` with other features, and they connect to requirements through `specifiedBy` / `specify`.

Features are not directly verified or satisfied. Feature verification and implementation coverage roll up from the requirements that specify them.

Do not create one universal top feature that owns every ontology and is specified by every requirement. That collapses the model into one feature-root subgraph and hides change impact. Use a top feature when requirements truly specify the same capability. Use subfeatures only when the capability has meaningful product, interface, stakeholder, regulatory, or domain slices that need their own ownership, collection, or coverage view. Attach shared ontology explicitly from the consuming feature.

For example, one `System Model Interfaces` feature can attach the interface ontology and be specified by CLI, web documentation, and MCP interface requirements when those are all obligations for the same interface capability. A separate feature root should attach the interface ontology only when it depends on that language without implementing the interface capability.

```markdown
### API Authentication

API authentication capability and access-token domain context.

#### Metadata
  * type: feature

#### Attachments
  * [Access Token Ontology](AuthContracts.md#access-token-ontology)

#### Relations
  * specifiedBy: [API Access Token Validation](AuthRequirements.md#api-access-token-validation)
```

## Requirements

A `requirement` answers:

- What shall the system do?
- Under what condition, state, interface, or operational scope?
- What implementation or evidence can satisfy it?
- What verification proves it?

Requirement:

```text
The system shall reject API requests whose access token does not conform to the access token semantic contract.
```

Answers:

- What must the system do?
- Under what condition, interface, state, or scope?
- What implementation or evidence can satisfy it?
- What verification proves it?

A requirement is the obligation anchor. It should stay testable, implementation-facing, and evidence-facing. Requirements are where Reqvire tracks verification, implementation satisfaction, and coverage.

Requirements can derive from other requirements through `derive` / `derivedFrom`, and top-level requirements are specified by a feature through `specify`.

Requirements may also own shapes-only `semantic-contract` refinements when a specific obligation needs a SHACL profile over ontology terms already reachable from the owning requirement context.

```markdown
### API Access Token Validation

The system shall reject API requests whose access token does not conform to the access token semantic contract.

#### Metadata
  * type: requirement

#### Relations
  * specify: [API Authentication](Features.md#api-authentication)
  * verifiedBy: [Access Token Contract Test](Verifications.md#access-token-contract-test)
  * satisfiedBy: [auth_middleware.rs](../src/auth_middleware.rs)
```

## Ontologies and Semantic Contracts

An `ontology` element is a first-class semantic model. It carries Turtle/OWL vocabulary and reusable terms such as classes, properties, relationships, allowed semantic categories, and stable model rules. Keep authored ontology elements in a dedicated `requirements/Ontologies` folder; feature files attach those ontology elements to make terms reachable for their descendant features and specifying requirements.

Use ontology for:

- domain vocabulary and concept definitions,
- ontology classes and properties,
- business object structure,
- allowed semantic relationships,
- shared model/domain meaning across feature subgraphs through explicit attachments.

Use requirement-owned `semantic-contract` for:

- SHACL validation constraints for one obligation,
- payload or state profiles over reachable ontology,
- examples or validation targets for a requirement-specific contract.

Use a requirement instead when the statement is a system obligation.

Rule of thumb:

- `X is...`, `X has...`, `X relates to Y...` may belong in ontology.
- `These fields form a valid object...` may belong in a requirement-owned semantic contract.
- `This domain term means...` may belong in ontology.
- `The system shall...` belongs in a requirement.

This split prevents requirements from becoming overloaded with vocabulary and data-model definitions. Ontology defines what domain objects mean; the requirement states what the system must do; the requirement semantic contract can define the exact shape/profile that makes the obligation precise.

````markdown
### Access Token Ontology

Defines access token domain meaning.

#### Metadata
  * type: ontology

#### Ontology

```turtle
@prefix auth: <urn:reqvire:auth:> .
@prefix owl: <http://www.w3.org/2002/07/owl#> .

auth:AccessToken a owl:Class .
auth:subject a owl:ObjectProperty .
```

````

Requirement-owned shape contract:

````markdown
### Access Token Validation Shape Contract

Defines the SHACL profile for one access-token validation requirement.

#### Metadata
  * type: semantic-contract

#### Relations
  * refine: [API Access Token Validation](AuthRequirements.md#api-access-token-validation)

#### Shapes

```turtle
@prefix auth: <urn:reqvire:auth:> .
@prefix sh: <http://www.w3.org/ns/shacl#> .

auth:AccessTokenValidationShape
  a sh:NodeShape ;
  sh:targetClass auth:AccessToken ;
  sh:property [
    sh:path auth:subject ;
    sh:minCount 1 ;
  ] .
```
````

## Sources

A `source` is a feature-owned refinement for stakeholder, regulatory, contractual, or external source material.

Use sources to capture why a feature exists without turning the source text into implementation requirements.

```markdown
### OAuth Provider Token Source

OAuth provider documentation defining access token claims, issuer, subject, and expiration semantics.

#### Metadata
  * type: source

#### Relations
  * refine: [API Authentication](Features.md#api-authentication)
```

## Governance Metadata

Reqvire supports governance metadata directly on `feature` and `requirement` elements:

```markdown
#### Metadata
  * type: feature
  * status: review
  * priority: high
  * risk: medium
  * owner: Identity Team
```

| Key | Meaning | Accepted values |
|-----|---------|-----------------|
| `status` | Lifecycle state | `draft`, `review`, `approved` |
| `priority` | Planning importance | `low`, `medium`, `high`, `critical` |
| `risk` | Realization risk | `low`, `medium`, `high`, `critical` |
| `owner` | Accountable person, role, or team | free-form string |

Defaults are `status: approved`, `priority: medium`, `risk: low`, and unassigned owner. Requirements inherit missing values from the nearest parent requirement or owning feature. Features inherit missing values from parent features.

Refinement and verification elements must not declare these governance keys; they receive governance context from their owning or related feature/requirement.

## Model Structure

The canonical structure is:

```text
feature
  derive -> feature
  refinedBy -> source
  attach -> ontology
  specifiedBy -> requirement

requirement
  derive -> requirement
  refinedBy -> specification / constraint / behavior / state / input-output
  verifiedBy -> verification
  satisfiedBy -> implementation/evidence
```

`derivedFrom` / `derive` stays inside the same family:

- feature to feature,
- requirement to requirement.

`specify` / `specifiedBy` is the bridge between requirements and features.

## Requirement Containment

Reqvire uses files and folders for physical organization, while feature and requirement relations define the logical model.

Example:

```text
requirements/
├── Features.md
├── Identity/
│   ├── Requirements.md
│   ├── SemanticContracts.md
│   └── Verifications.md
└── Security/
    ├── Requirements.md
    ├── Sources.md
    └── Verifications.md
```

In this example:

- `Features.md` contains product/capability anchors.
- `Ontologies/Identity.md` contains shared ontology elements.
- `Identity/Requirements.md` contains system obligations specified by identity features.
- `Identity/Verifications.md` contains evidence that verifies requirements.

## Verification and Coverage

Requirements are verified. Features receive coverage by roll-up from requirements.

```text
Feature
  specifiedBy -> Requirement
    verifiedBy -> Verification
    satisfiedBy -> Code or evidence
```

Implementation coverage applies to `requirement` elements. Feature coverage is computed from the requirements under the feature.
