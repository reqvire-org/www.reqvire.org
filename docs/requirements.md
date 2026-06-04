---
layout: page
nav_order: 3
title: Engineering Graph
---

## Conceptual Overview

Reqvire models requirements as part of a semantic engineering graph, not as isolated statements. The model separates domain meaning, capability intent, implementable obligations, refinements, verification evidence, and implementation artifacts:

- **Ontologies** define reusable domain/model meaning.
- **Capabilities** define coherent operational, product, business, regulatory, or system abilities.
- **Requirements** define what the system shall do for those capabilities.
- **Refinements** add detailed engineering contracts to capabilities or requirements.
- **Verifications** prove capabilities or requirements.
- **Implementation artifacts** satisfy requirements or evidence-backed verifications.

This keeps Reqvire close to MBSE while also making it an engineering knowledge graph for AI-native development. Capabilities describe stable operational meaning, ontology provides shared vocabulary, and requirements remain testable obligations with implementation and verification evidence.

## Capabilities

A `capability` answers:

- What coherent operational, product, business, regulatory, or system ability is this?
- Why does this area exist in the product model?
- Who owns the scope and routing for this slice?
- What source material or ontology defines the domain context?
- Which requirements belong under this capability?

Capability:

```text
API Authentication
```

Answers:

- What capability/domain is this?
- Why does this area exist in the product model?
- What stakeholder, regulatory, source, or policy context owns it?
- What ontology defines its domain language?
- Which requirements belong under this capability?

A capability is not a weaker requirement. It is a coherent operational, product, business, regulatory, or system ability. It defines the scope and language of the model area before individual system obligations are written.

Because a capability owns the capability context, it can own compatible refinements and attach `ontology` elements from `requirements/Ontologies`. Those define vocabulary, source authority, external context, domain structure, contracts, behavior, and reusable meaning for the capability. They are not implementation obligations by themselves.

Capabilities can form capability hierarchies using `derive` / `derivedFrom` with other capabilities, and they connect to requirements through `specifiedBy` / `specify`.

Capabilities may be directly verified but are not directly satisfied. Capability implementation coverage rolls up from the requirements that specify them.

Capabilities should describe what the system is able to accomplish rather than how the system is implemented.

A capability is not:

- a UI screen,
- a deployment artifact,
- a code module,
- a ticket or task,
- a low-level implementation detail.

Good capabilities represent one coherent operational or system concern, one meaningful traceability anchor, and one stable semantic concept. They should remain stable over time, composable, implementation-independent, verifiable, and understandable by both humans and AI systems.

Do not create one universal top capability that owns every ontology and is specified by every requirement. That collapses the model into one capability-root subgraph and hides change impact. Use a top capability when requirements truly specify the same capability. Use subcapabilities only when the capability has meaningful product, interface, stakeholder, regulatory, or domain slices that need their own ownership, collection, or coverage view. Attach shared ontology explicitly from the consuming capability.

For example, one `System Model Interfaces` capability can attach the interface ontology and be specified by CLI, web documentation, and MCP interface requirements when those are all obligations for the same interface capability. A separate capability root should attach the interface ontology only when it depends on that language without implementing the interface capability.

```markdown
### API Authentication

API authentication capability and access-token domain context.

#### Metadata
  * type: capability

#### Attachments
  * [Access Token Ontology](AuthContracts.md#access-token-ontology)

#### Relations
  * specifiedBy: [API Access Token Validation](AuthRequirements.md#api-access-token-validation)
```

### Capability Decomposition

If a capability becomes too broad or contains multiple independently verifiable concerns, decompose it into child capabilities.

Use child capabilities when concerns differ in:

- verification,
- ownership,
- lifecycle,
- architecture impact,
- operational semantics,
- requirement clusters.

```text
CAP-001 Real-Time Collaboration
  ├─ CAP-001.1 Shared Editing
  ├─ CAP-001.2 Presence Awareness
  ├─ CAP-001.3 Commenting
  └─ CAP-001.4 Audit History
```

### Capability vs Requirement

Capability:

> what the system can accomplish

Requirement:

> obligations, constraints, or guarantees governing realization of the capability

Example:

- Capability: Real-Time Collaboration
- Requirements: updates propagate within one second, edit history is immutable, access respects workspace permissions

### Capability vs Ontology

Ontology defines concepts, relationships, and semantic meaning.

Capabilities define operational or system abilities using those concepts.

Example:

- Ontology: User, Workspace, Document, Comment
- Capability: Collaborative Document Editing

### Capability vs Feature

A feature is often user-facing, roadmap-oriented, or product-oriented. Capabilities are broader and more stable.

Features may be described inside capability content, but the `capability` element remains the primary traceable graph node.

### Optional Capability Context

Capability content can include optional semantic enrichment sections when they help humans or AI systems understand the capability:

- `#### Stakeholder Need`
- `#### Feature`
- `#### Operational Context`
- `#### Regulatory Driver`
- `#### Mission Objective`
- `#### Service Context`
- `#### AI Context`
- `#### Notes`

These subsections enrich interpretation. They are not independent graph nodes unless the model explicitly creates separate elements for them.

Use child capabilities instead when independent traceability or verification is required.

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

Requirements can derive from other requirements through `derive` / `derivedFrom`, and top-level requirements are specified by a capability through `specify`.

Requirements may also own refinements when a specific obligation needs more precise behavior, state, semantic, input/output, constraint, or specification detail over ontology terms already reachable from the owning capability context.

```markdown
### API Access Token Validation

The system shall reject API requests whose access token does not conform to the access token semantic contract.

#### Metadata
  * type: requirement

#### Relations
  * specify: [API Authentication](Capabilities.md#api-authentication)
  * verifiedBy: [Access Token Contract Test](Verifications.md#access-token-contract-test)
  * satisfiedBy: [auth_middleware.rs](../src/auth_middleware.rs)
```

## Ontologies and Semantic Contracts

An `ontology` element is a first-class semantic model. It carries Turtle/OWL vocabulary and reusable terms such as classes, properties, relationships, allowed semantic categories, and stable model rules. Keep authored ontology elements in a dedicated `requirements/Ontologies` folder; capability files attach those ontology elements to make terms reachable for their descendant capabilities and specifying requirements.

Use ontology for:

- domain vocabulary and concept definitions,
- ontology classes and properties,
- business object structure,
- allowed semantic relationships,
- shared model/domain meaning across capability subgraphs through explicit attachments.

Use `semantic-contract` for:

- SHACL validation constraints for one capability or obligation,
- payload or state profiles over reachable ontology,
- examples or validation targets for capability-level or requirement-specific contracts.

Use a requirement instead when the statement is a system obligation.

Rule of thumb:

- `X is...`, `X has...`, `X relates to Y...` may belong in ontology.
- `These fields form a valid object...` may belong in a semantic contract.
- `This domain term means...` may belong in ontology.
- `The system shall...` belongs in a requirement.

This split prevents requirements from becoming overloaded with vocabulary and data-model definitions. Ontology defines what domain objects mean; the capability defines the stable ability; the requirement states what the system must do; a semantic contract can define the exact shape/profile that makes the capability or obligation precise.

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

A `source` is a capability-owned refinement for stakeholder, regulatory, contractual, or external source material.

Use sources to capture why a capability exists without turning the source text into implementation requirements.

```markdown
### OAuth Provider Token Source

OAuth provider documentation defining access token claims, issuer, subject, and expiration semantics.

#### Metadata
  * type: source

#### Relations
  * refine: [API Authentication](Capabilities.md#api-authentication)
```

## Governance Metadata

Reqvire supports governance metadata directly on `capability` and `requirement` elements:

```markdown
#### Metadata
  * type: capability
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

Defaults are `status: approved`, `priority: medium`, `risk: low`, and unassigned owner. Requirements inherit missing values from the nearest parent requirement or owning capability. Capabilities inherit missing values from parent capabilities.

Refinement and verification elements must not declare these governance keys; they receive governance context from their owning or related capability/requirement.

## Model Structure

The canonical structure is:

```text
capability
  derive -> capability
  refinedBy -> source / semantic-contract / specification / constraint / behavior / state / input-output
  attach -> ontology
  verifiedBy -> verification
  specifiedBy -> requirement

requirement
  derive -> requirement
  refinedBy -> specification / constraint / behavior / state / input-output
  verifiedBy -> verification
  satisfiedBy -> implementation/evidence
```

`derivedFrom` / `derive` stays inside the same family:

- capability to capability,
- requirement to requirement.

`specify` / `specifiedBy` is the bridge between requirements and capabilities.

## Model Containment

Reqvire uses files and folders for physical organization, while element metadata and relations define the logical model. A good layout makes capability ownership, ontology context, requirements, refinements, and verification evidence easy to find without replacing graph relations.

Recommended top-level model planes:

```text
requirements/
├── Capabilities/
│   ├── Product/
│   │   └── Collaboration/
│   │       ├── Collaboration.md
│   │       ├── CollaborationRequirements.md
│   │       ├── CollaborationBehaviors.md
│   │       └── Architecture/
│   │           └── CollaborationServiceSpecifications.md
│   └── Platform/
│       └── Identity/
│           ├── Identity.md
│           └── IdentityRequirements.md
├── Ontologies/
│   ├── Collaboration.md
│   └── Identity.md
└── Verifications/
    ├── Collaboration/
    │   └── CollaborationVerifications.md
    └── Identity/
        └── IdentityVerifications.md
```

In this example:

- `Capabilities/` contains capability-rooted subgraphs and the requirements/refinements owned by each capability.
- `Ontologies/` contains shared `ontology` elements that capabilities attach for semantic context.
- `Verifications/` contains verification elements grouped by verification domain.
- `Architecture/` folders live inside the capability subgraph they refine, unless reusable meaning should be extracted into ontology.

The folder names are guidance, not schema. The authoritative semantics remain the element `type` metadata and relations such as `specify`, `refine`, `verify`, and attachments.

## Verification and Coverage

Requirements are verified, and capabilities may be directly verified. Capabilities also receive coverage by roll-up from requirements.

```text
Capability
  specifiedBy -> Requirement
    verifiedBy -> Verification
    satisfiedBy -> Code or evidence
```

Implementation coverage applies to `requirement` elements. Capability coverage is computed from the requirements under the capability.
