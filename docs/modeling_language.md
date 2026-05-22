---
layout: page
nav_order: 6
title: Modeling Language
---

## Reqvire Modeling Language

Reqvire uses semi-structured Markdown as a lightweight MBSE modeling language. Models stay readable in Git while still being machine-validated, queryable, and useful as context for coding assistants.

## Core Elements

Each Reqvire model consists of:

- **Features** - product/capability, stakeholder, regulatory, or external-context anchors.
- **Requirements** - implementable system obligations that specify features.
- **Ontologies** - first-class OWL/Turtle vocabulary and reusable semantic model terms.
- **Feature refinements** - `source` context owned by a feature.
- **Requirement refinements** - `semantic-contract`, `specification`, `constraint`, `behavior`, `state`, and `input-output`.
- **Verifications** - evidence that requirements are satisfied by tests, proofs, analysis, inspection, or demonstration.
- **Relations** - explicit links between model elements and implementation/evidence artifacts.
- **Files and folders** - physical organization for model content.

Elements are defined using `###` Markdown headers. Metadata, relations, details, and attachments are represented as reserved `####` subsections.

## Feature and Requirement Ownership

Reqvire enforces feature-rooted ownership:

- A `feature` may be a root.
- A feature may derive from another feature.
- A requirement must resolve to exactly one owning feature.
- A top-level requirement connects to its owning feature through `specify`.
- A child requirement may inherit ownership through `derivedFrom` to another requirement.
- Cross-feature semantic dependencies must be explicit attachments so change impact is preserved.

This keeps ownership, change impact, and coverage roll-up auditable.

Feature roots should remain real capability roots, not universal buckets. Use one top feature when its requirements truly specify the same capability. Use subfeatures only when the capability has meaningful product, interface, stakeholder, regulatory, or domain slices that need independent ownership or collection. Reuse shared ontology through explicit ontology attachments across feature roots instead of placing unrelated requirements under the same feature hierarchy.

## Document Structure

Model files must begin with one of these level-1 headings:

- `# Elements` for files containing multiple elements.
- `# Documents` for a single document-style element.

```markdown
# Elements

### My Feature

Feature scope.

#### Metadata
  * type: feature
```

```markdown
# Documents

## Metadata
  * type: specification

## Relations
  * refine: [Some Requirement](Requirements.md#some-requirement)

## My Document Element
Any markdown content is allowed here.
```

Element names must be globally unique across the model. Reqvire uses stable element identifiers so links can survive file moves and renames.

## Metadata

Metadata is defined in a `#### Metadata` subsection:

```markdown
#### Metadata
  * type: requirement
  * priority: high
  * owner: Platform Team
```

Reserved metadata:

- `type` - element type.
- `status`, `priority`, `risk`, `owner` - governance metadata, valid only on `feature` and `requirement` elements.

Governance metadata defaults:

| Key | Default | Accepted values |
|-----|---------|-----------------|
| `status` | `approved` | `draft`, `review`, `approved` |
| `priority` | `medium` | `low`, `medium`, `high`, `critical` |
| `risk` | `low` | `low`, `medium`, `high`, `critical` |
| `owner` | unassigned | free-form string |

Requirements inherit missing governance fields from the nearest parent requirement or owning feature. Features inherit missing values from parent features.

Refinements and verifications must not declare governance metadata.

## Element Types

| Category | Type | Purpose |
|----------|------|---------|
| Feature | `feature` | Product/capability, stakeholder, regulatory, or external-context anchor |
| Requirement | `requirement` | Implementable system obligation |
| Ontology | `ontology` | OWL/Turtle vocabulary, concept, relation, and semantic model definitions |
| Feature refinement | `source` | Stakeholder, regulatory, contractual, or external source material |
| Requirement refinement | `semantic-contract` | Requirement-owned SHACL shape profile over reachable ontology |
| Requirement refinement | `specification` | Detailed technical specification |
| Requirement refinement | `constraint` | Limit or boundary |
| Requirement refinement | `behavior` | Behavioral contract |
| Requirement refinement | `state` | Lifecycle states, state machines, transitions, and state-dependent contracts |
| Requirement refinement | `input-output` | Payloads, messages, schemas, fixtures, and data contracts |
| Verification | `test-verification` | Test evidence; requires `satisfiedBy` |
| Verification | `formal-proof-verification` | Proof/model-checking/theorem/fixture/report evidence; requires `satisfiedBy` |
| Verification | `analysis-verification` | Analysis evidence |
| Verification | `inspection-verification` | Inspection evidence |
| Verification | `demonstration-verification` | Demonstration evidence |

If no type is specified, normal model files default to `requirement`. Files in `Verifications/` directories default to verification.

## Relations

Relations are defined in `#### Relations`:

```markdown
#### Relations
  * specify: [Parent Feature](Features.md#parent-feature)
  * verifiedBy: [Contract Test](Verifications.md#contract-test)
  * satisfiedBy: [implementation.rs](../src/implementation.rs)
  * refinedBy: [Payload Contract](Contracts.md#payload-contract)
```

Supported core relations:

| Relation | Direction | Description |
|----------|-----------|-------------|
| `derivedFrom` | child -> parent | Hierarchy inside the same family: feature-to-feature, requirement-to-requirement, or ontology-to-ontology |
| `derive` | parent -> child | Inverse hierarchy relation |
| `specify` | requirement -> feature | Requirement specifies a feature |
| `specifiedBy` | feature -> requirement | Feature is specified by a requirement |
| `verifiedBy` | requirement -> verification | Requirement is verified by a verification element |
| `verify` | verification -> requirement | Verification verifies a requirement |
| `satisfiedBy` | requirement or evidence-backed verification -> artifact | Links implementation or evidence artifacts |
| `satisfy` | artifact -> requirement or evidence-backed verification | Inverse satisfaction relation |
| `refinedBy` | owner -> refinement | Feature or requirement owns a subtype-compatible refinement |
| `refine` | refinement -> owner | Refinement points to its owner |
| `trace` | any -> any | Soft traceability |

Feature-owned refinements:

- `feature refinedBy source`

Requirement-owned refinements:

- `requirement refinedBy semantic-contract`
- `requirement refinedBy specification`
- `requirement refinedBy constraint`
- `requirement refinedBy behavior`
- `requirement refinedBy state`
- `requirement refinedBy input-output`

## Attachments

Attachments reference existing ontology elements or compatible requirement-owned refinements across explicit subgraph boundaries:

```markdown
#### Attachments
  * [Access Token Ontology](AuthContracts.md#access-token-ontology)
```

Attachment rules:

- Features may attach `ontology` elements.
- Requirements may attach `semantic-contract`, `specification`, `constraint`, `behavior`, `state`, or `input-output` refinements owned by other requirement subgraphs.
- Refinements must already have a `refine` owner relation before they can be attached. Ontology elements are not refinements and are attached directly.
- Attachments are used for cross-boundary contracts; elements in the same hierarchy use ownership relations instead.

## Ontologies, Concept References, and Semantic Contracts

Use `ontology` elements for reusable meaning:

- `ontology` requires exactly one `#### Ontology` fenced Turtle block.
- Authored ontology elements should live under `requirements/Ontologies`, not inside feature files.
- Ontology elements can use `derive` / `derivedFrom` only with other ontology elements.
- Ontology elements do not author `#### Attachments`.
- Features attach ontology elements to make terms reachable for descendant features and specifying requirements.

Use `#### Concept References` when requirement prose needs readable labels bound to ontology terms:

```markdown
#### Concept References
  * Service Endpoint: api:ServiceEndpoint
```

Use `semantic-contract` elements only as requirement-owned SHACL profiles:

- `semantic-contract` must refine a requirement.
- `#### Shapes` is required.
- `#### Ontology` is forbidden.

Reqvire validates ontology and semantic contract content by parsing Turtle and running lightweight SHACL sanity checks:

- malformed Turtle is rejected,
- duplicate ontology term declarations are rejected,
- conflicting declarations are rejected,
- SHACL references must resolve through reachable ontology context,
- references to terms in another feature subgraph require an explicit ontology attachment.

Use ontology when content defines domain meaning, vocabulary, object structure, or semantic relationships. Use requirement-owned semantic contracts when one obligation needs a SHACL profile over reachable ontology. Use requirements for system obligations.

## Example

````markdown
# Elements

### API Authentication

API authentication capability and access-token domain context.

#### Metadata
  * type: feature

#### Attachments
  * [Access Token Ontology](#access-token-ontology)

#### Relations
  * specifiedBy: [API Access Token Validation](#api-access-token-validation)

---

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

---

### API Access Token Validation

The system shall reject API requests whose access token does not conform to the access token semantic contract.

#### Metadata
  * type: requirement

#### Concept References
  * Access Token: auth:AccessToken

#### Relations
  * specify: [API Authentication](#api-authentication)
  * refinedBy: [Access Token Validation Shape Contract](#access-token-validation-shape-contract)
  * verifiedBy: [Access Token Contract Test](#access-token-contract-test)
  * satisfiedBy: [auth_middleware.rs](../src/auth_middleware.rs)

---

### Access Token Validation Shape Contract

Defines the SHACL profile for the access-token validation obligation.

#### Metadata
  * type: semantic-contract

#### Relations
  * refine: [API Access Token Validation](#api-access-token-validation)

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

---

### Access Token Contract Test

Test verifies access tokens conform to the semantic contract.

#### Metadata
  * type: test-verification

#### Relations
  * verify: [API Access Token Validation](#api-access-token-validation)
  * satisfiedBy: [test_access_token.rs](../tests/test_access_token.rs)
````

## Related Pages

- [Requirements](requirements.md)
- [Verifications](verifications.md)
- [Implementation Coverage](implementation_coverage.md)
- [Submodels and Subgraphs](submodels.md)
