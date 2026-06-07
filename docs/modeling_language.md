---
layout: page
nav_order: 4
title: Modeling Language
---

## Reqvire Modeling Language

Reqvire uses semi-structured Markdown as a lightweight semantic engineering and MBSE modeling language. Models stay readable in Git while still forming a machine-validated, queryable, traceable engineering knowledge graph for humans and AI assistants.

## Core Elements

Each Reqvire model consists of:

- **Ontologies** - first-class OWL/Turtle vocabulary and reusable semantic model terms.
- **Capabilities** - coherent operational, product, business, regulatory, or system abilities.
- **Requirements** - implementable obligations, constraints, guarantees, and behavioral expectations that specify capabilities.
- **Refinements** - requirement-owned `source`, `semantic-contract`, `semantic-query-contract`, `specification`, `constraint`, `behavior`, `state`, and `input-output` detail.
- **Verifications** - evidence that capabilities or requirements are verified by tests, proofs, analysis, inspection, or demonstration.
- **Relations** - explicit links between model elements and implementation/evidence artifacts.
- **Files and folders** - physical organization for model content.

Elements are defined using `###` Markdown headers. Metadata, relations, details, and attachments are represented as reserved `####` subsections.

## Physical Containment

Reqvire's logical graph is defined by element metadata and relations. Folders and files provide physical containment, review boundaries, and navigation.

Recommended top-level model planes:

```text
<model-root>/
  Capabilities/
  Ontologies/
  Verifications/
```

`Capabilities/` contains capability-rooted subgraphs: the capability element, child capabilities, requirements that specify the capability, and requirement-owned refinements or contracts used by those requirements.

`Ontologies/` contains reusable `ontology` elements. Capabilities attach ontology from this plane instead of nesting shared vocabulary inside capability files.

`Verifications/` contains verification elements grouped by verification domain. Verifications prove capabilities or requirements through `verify` / `verifiedBy`; evidence-backed verification types may use `satisfiedBy`.

The folder structure is guidance, not schema. File paths should help humans review the model, while `type`, `specify`, `refine`, `verify`, and attachments define the authoritative graph.

## Capability and Requirement Ownership

Reqvire enforces capability-rooted ownership while keeping ontology, requirement, and verification semantics explicit:

- A `capability` may be a root.
- A capability may derive from another capability.
- A requirement must resolve to exactly one owning capability.
- A top-level requirement connects to the capability it specifies through `specify`.
- A child requirement may inherit ownership through `derivedFrom` to another requirement.
- Cross-capability semantic dependencies must be explicit attachments so change impact is preserved.

This keeps ownership, change impact, verification, and coverage roll-up auditable.

Capabilities should describe what the system is able to accomplish, not how the system is implemented. Use one top capability when its requirements truly specify the same ability. Use child capabilities when concerns differ in verification, ownership, lifecycle, architecture impact, operational semantics, or requirement clusters. Reuse shared ontology through explicit ontology attachments across capability roots instead of placing unrelated requirements under the same capability hierarchy.

Capability prose may include optional semantic enrichment subsections such as `#### Stakeholder Need`, `#### Feature`, `#### Operational Context`, `#### Regulatory Driver`, `#### Mission Objective`, `#### Service Context`, `#### AI Context`, or `#### Notes`. These sections enrich human and AI understanding; they do not replace graph structure. If the concern needs independent traceability or verification, model it as a child capability.

## Document Structure

Model files must begin with one of these level-1 headings:

- `# Elements` for files containing multiple elements.
- `# Documents` for a single document-style element.

```markdown
# Elements

### My Capability

Capability scope.

#### Metadata
  * type: capability
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
- `status`, `priority`, `risk`, `owner` - governance metadata, valid only on `capability` and `requirement` elements.

Governance metadata defaults:

| Key | Default | Accepted values |
|-----|---------|-----------------|
| `status` | `approved` | `draft`, `review`, `approved` |
| `priority` | `medium` | `low`, `medium`, `high`, `critical` |
| `risk` | `low` | `low`, `medium`, `high`, `critical` |
| `owner` | unassigned | free-form string |

Requirements inherit missing governance fields from the nearest parent requirement or owning capability. Capabilities inherit missing values from parent capabilities.

Refinements and verifications must not declare governance metadata.

## Element Types

| Category | Type | Purpose |
|----------|------|---------|
| Capability | `capability` | Coherent operational, product, business, regulatory, or system ability |
| Requirement | `requirement` | Implementable system obligation |
| Ontology | `ontology` | OWL/Turtle vocabulary, concept, relation, and semantic model definitions |
| Refinement | `source` | Stakeholder, regulatory, contractual, policy, or external source material |
| Refinement | `semantic-contract` | SHACL shape profile over reachable ontology |
| Refinement | `semantic-query-contract` | Requirement-owned declarative SPARQL query contract over reachable ontology context |
| Refinement | `specification` | Detailed technical specification |
| Refinement | `constraint` | Limit or boundary |
| Refinement | `behavior` | Behavioral contract |
| Refinement | `state` | Lifecycle states, state machines, transitions, and state-dependent contracts |
| Refinement | `input-output` | Payloads, messages, schemas, fixtures, and data contracts |
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
  * specify: [Parent Capability](Capabilities.md#parent-capability)
  * verifiedBy: [Contract Test](Verifications.md#contract-test)
  * satisfiedBy: [implementation.rs](../src/implementation.rs)
  * refinedBy: [Payload Contract](Contracts.md#payload-contract)
```

Supported core relations:

| Relation | Direction | Description |
|----------|-----------|-------------|
| `derivedFrom` | child -> parent | Hierarchy inside the same family: capability-to-capability, requirement-to-requirement, or ontology-to-ontology |
| `derive` | parent -> child | Inverse hierarchy relation |
| `specify` | requirement -> capability | Requirement specifies a capability |
| `specifiedBy` | capability -> requirement | Capability is specified by a requirement |
| `verifiedBy` | capability or requirement -> verification | Capability or requirement is verified by a verification element |
| `verify` | verification -> capability or requirement | Verification verifies a capability or requirement |
| `satisfiedBy` | requirement or evidence-backed verification -> artifact | Links implementation or evidence artifacts |
| `satisfy` | artifact -> requirement or evidence-backed verification | Inverse satisfaction relation |
| `refinedBy` | requirement -> refinement | Requirement owns a subtype-compatible refinement |
| `refine` | refinement -> owner | Refinement points to its owner |
| `trace` | any -> any | Soft traceability |

Requirement-owned refinements:

- `requirement refinedBy source`
- `requirement refinedBy semantic-contract`
- `requirement refinedBy semantic-query-contract`
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

- Capabilities may attach `ontology` elements to define reachable semantic context.
- Requirements may attach `semantic-contract`, `semantic-query-contract`, `specification`, `constraint`, `behavior`, `state`, or `input-output` refinements owned by other requirement subgraphs.
- Refinements must already have a `refine` owner relation before they can be attached. Ontology elements are not refinements and are attached directly.
- Attachments are used for cross-boundary contracts; elements in the same hierarchy use ownership relations instead.

## Ontologies, Concept References, Semantic Contracts, and Semantic Query Contracts

Use `ontology` elements for reusable meaning:

- `ontology` requires exactly one `#### Ontology` fenced Turtle block.
- Authored ontology elements should live under `requirements/Ontologies`, not inside capability files.
- Ontology elements can use `derive` / `derivedFrom` only with other ontology elements.
- Ontology elements do not author `#### Attachments`.
- Capabilities attach ontology elements to make terms reachable for descendant capabilities and specifying requirements.
- OWL ontology content should define classes, object properties, datatype properties, subclass relations, domain/range, labels, comments, restrictions, equivalence, inverse properties, property chains, and other reusable semantic meaning.
- Use XSD datatypes through `owl:DatatypeProperty` and `rdfs:range`. Use `rdfs:label` and `rdfs:comment` for ordinary display labels and descriptions instead of project-specific name/meaning properties unless those properties are true domain concepts.

Use `#### Concept References` when requirement prose needs readable labels bound to ontology terms:

```markdown
#### Concept References
  * Service Endpoint: api:ServiceEndpoint
```

Use `semantic-contract` elements as requirement-owned SHACL profiles:

- `semantic-contract` must refine exactly one requirement.
- `#### Shapes` is required.
- `#### Ontology` is forbidden.
- SHACL shapes should target OWL classes and validate operational constraints such as required properties, cardinality, datatypes, allowed values, patterns, and closed-world data-quality rules.
- SHACL shape profiles are validation contracts. They are not exported as SHACL-AF materialization rules unless explicit `sh:rule` support is authored and implemented.

Use `semantic-query-contract` elements as requirement-owned declarative query contracts:

- `semantic-query-contract` must refine exactly one requirement.
- `#### Query` is required and must contain exactly one fenced `sparql` code block.
- `#### Ontology` and `#### Shapes` are forbidden.
- Query kind or purpose classification is not part of the initial model.
- Search JSON exposes semantic query contract identity and parsed Query block details.
- Ontology collection and full semantic export do not emit query contracts until a dedicated query-export command exists.

Reqvire validates ontology, semantic contract, and semantic query contract content by parsing Turtle, checking query-contract structure, and running lightweight SHACL sanity checks:

- malformed Turtle is rejected,
- duplicate ontology term declarations are rejected,
- conflicting declarations are rejected,
- SHACL references must resolve through reachable ontology context,
- references to terms in another capability subgraph require an explicit ontology attachment,
- semantic query contracts must use a single `#### Query` fenced `sparql` block and may not include ontology or shape blocks.

Use ontology when content defines domain meaning, vocabulary, object structure, or semantic relationships. Use semantic contracts when one requirement obligation needs a SHACL profile over reachable ontology. Use semantic query contracts when one requirement needs a declarative graph query contract over reachable context. Use requirements for implementable system obligations.

## Example

````markdown
# Elements

### API Authentication

API authentication capability and access-token domain context.

#### Metadata
  * type: capability

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

- [Engineering Graph](requirements.md)
- [Verifications](verifications.md)
- [Implementation Coverage](implementation_coverage.md)
- [Submodels and Subgraphs](submodels.md)
