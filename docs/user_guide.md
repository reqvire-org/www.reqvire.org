---
layout: page
nav_order: 5
title: User Guide
---

This user guide explains how to install and use Reqvire as a semantic engineering graph for building verifiable and traceable systems.

## Table of Contents

- [Installation](#installation)
- [Basic Commands](#basic-commands)
- [Workspace Selection](#workspace-selection)
- [MCP Server](#mcp-server)
- [JSON File Output](#json-file-output)
- [File Exclusion Patterns](#file-exclusion-patterns)
- [Working with Model Elements](#working-with-model-elements)
- [Element Manipulation](#element-manipulation)
- [Link and Unlink Commands](#link-and-unlink-commands)
- [Validation](#validation)
- [Formatting](#formatting)
- [Linting](#linting)
- [Generating Documentation](#generating-documentation)
- [Traceability](#traceability)
- [Resources Report](#resources-report)
- [Submodels Report](#submodels-report)
- [Collect Content](#collect-content)
- [Search and Filtering](#search-and-filtering)
- [Model Commands](#model-commands)
- [Change Impact Report](#change-impact-report)
- [Diagrams](#diagrams)
- [GitHub Integration](#github-integration)
  - [GitHub Actions](#github-actions)
  - [GitHub Issue Comment Commands](#github-issue-comment-commands)

---

## Installation

### Quick Install

Install Reqvire using the installation script:

```bash
curl -fsSL https://raw.githubusercontent.com/reqvire-org/reqvire/main/scripts/install.sh | bash
```

This will download and install the latest version of Reqvire for your platform.

### Installing from Source

If you prefer to build from source, you'll need Rust installed:

```bash
git clone https://github.com/reqvire-org/reqvire.git
cd reqvire
cargo build --release
```

The binary will be available at `target/release/reqvire`.

### Verify Installation

Check that Reqvire is installed correctly:

```bash
reqvire --version
```

---

## Basic Commands

Reqvire offers several core commands for managing your engineering knowledge graph:

### Help

View the available commands and options:

```bash
reqvire --help
```

### Version

Display the current version:

```bash
reqvire --version
```

## Workspace Selection

Use the global `--workspace <DIR>` option to run Reqvire against a model repository from another current working directory:

```bash
reqvire --workspace /path/to/repository validate
reqvire --workspace /path/to/repository search --filter-type requirement
reqvire --workspace /path/to/repository mcp
```

The option applies to normal CLI commands and to the MCP server. Reqvire changes into the selected workspace before loading the model, reading git state, or applying mutations.

## MCP Server

Reqvire includes an MCP server for coding assistants that support the Model Context Protocol:

```bash
reqvire mcp
```

The MCP server uses RMCP Streamable HTTP and binds to `127.0.0.1:8081` by default. The fixed endpoint is `/mcp`:

```bash
reqvire mcp --host 127.0.0.1 --port 8081
```

The default tool set is read/report only. Mutation tools are available only when explicitly enabled:

```bash
reqvire mcp --enable-mutations
```

See the [MCP Server](/mcp_server/) page for Streamable HTTP request details, tool discovery, resources, mutation mode, and error handling.

## JSON File Output

Many Reqvire commands support `--json` for machine-readable output. You can combine it with `--output <FILE>` to write the JSON directly to a file instead of stdout.

### Usage

```bash
# Write JSON output to a file
reqvire validate --json --output results.json

# Works with any command that supports --json
reqvire search --json --output search-results.json
reqvire lint --json --output lint-report.json
reqvire change-impact --json --output impact.json
```

When `--output` is used, a confirmation message is printed to stdout and the JSON content is written to the specified file. The file is created if it doesn't exist and overwritten if it does.

### Requirements

- `--output` requires `--json` to also be set. Using `--output` without `--json` produces an error.

### Supported Commands

The `--output` option is available on all commands that support `--json`: `format`, `validate`, `search`, `change-impact`, `traces`, `coverage`, `model`, `submodels`, `lint`, `add`, `rm`, `mv`, `rename`, `merge`, `mv-file`, `link`, `unlink`, `relink`, `mv-asset`, `rm-asset`, `containment`, `resources`, and `collect`.

The `ontologies` command also supports `--output <FILE>` for Turtle or JSON-LD output.

---

## File Exclusion Patterns

Reqvire automatically excludes files and directories from structured markdown processing based on ignore patterns defined in:

1. **`.gitignore`** - If your project is a git repository, reqvire reads the `.gitignore` file at the repository root
2. **`.reqvireignore`** - A reqvire-specific ignore file using the same pattern syntax as `.gitignore`
3. **Reserved filenames** - Certain common repository documentation files are always excluded from structured markdown processing

Both ignore files use standard gitignore pattern syntax to exclude files from being parsed as structured markdown (requirements/verifications). However, they differ in an important way:

- **`.gitignore`**: Files matching these patterns are **completely excluded** - they cannot be parsed as structured markdown AND cannot be referenced in file relations to elements
- **`.reqvireignore`**: Files matching these patterns are excluded from structured markdown parsing BUT **can still be referenced** in file relations to elements (useful for design documents, diagrams, or other supporting files that you want to link to but not parse)

### Reserved Filenames

The following filenames are considered reserved and are always excluded from structured markdown processing, as they are typically used for general repository documentation or AI assistant context:

- `README.md`
- `CHANGELOG.md`, `CHANGES.md`
- `CONTRIBUTING.md`
- `LICENSE.md`
- `CODE_OF_CONDUCT.md`
- `SECURITY.md`
- `AUTHORS.md`
- `ROADMAP.md`
- `CLAUDE.md`
- `AGENT.md`
- `AI.md`
- `PROMPT.md`
- `INSTRUCTIONS.md`
- `CONTEXT.md`
- `CURSOR.md`
- `COPILOT.md`

**Example `.reqvireignore`:**
```
# Exclude use case documents
Usecases.md

# Exclude logical and physical design documents
Logical*.md
Physical*.md

# Exclude TODO lists
TODO.md

# Exclude entire directories
tests/**
src/**
```

## Working with Model Elements

Reqvire is designed to work with a structured semantic engineering graph in Markdown files. Capabilities, requirements, ontology, refinements, verifications, and evidence are organized using **folders and files for physical containment** while relations define the logical model.

### Folder Structure

Reqvire supports flexible organization based on **capability and semantic decomposition**. The recommended structure separates capability-rooted subgraphs, shared ontology vocabulary, and verification evidence while keeping graph relations authoritative.

**Recommended model layout**

```
project/
├── requirements/
│   ├── Capabilities/
│   │   ├── Product/
│   │   │   └── Collaboration/
│   │   │       ├── Collaboration.md
│   │   │       ├── CollaborationRequirements.md
│   │   │       ├── CollaborationBehaviors.md
│   │   │       └── Architecture/
│   │   │           └── CollaborationServiceSpecifications.md
│   │   └── Platform/
│   │       └── Identity/
│   │           ├── Identity.md
│   │           └── IdentityRequirements.md
│   ├── Ontologies/
│   │   ├── Collaboration.md
│   │   └── Identity.md
│   └── Verifications/
│       ├── Collaboration/
│       │   └── CollaborationVerifications.md
│       └── Identity/
│           └── IdentityVerifications.md
└── src/
    ├── auth.rs
    └── collaboration.rs
```

This layout uses:

- `Capabilities/` for capability-rooted subgraphs, including the requirements and refinements owned by the capability.
- `Ontologies/` for reusable semantic vocabulary and domain meaning.
- `Verifications/` for verification elements and evidence references.
- Capability-local `Architecture/` folders for design specifications that refine that capability or its requirements.

**Co-located variant**

```
project/
└── src/
    ├── authentication/
    │   ├── Capabilities.md       # capability and local requirements
    │   ├── IdentityOntology.md   # local ontology only if intentionally scoped here
    │   └── auth.rs
    └── collaboration/
        ├── Capabilities.md
        ├── CollaborationRequirements.md
        └── collaboration.rs
```

Co-location can be useful for developers and AI coding assistants, but shared ontology should still be easy to discover and attach explicitly. Do not rely on paths to imply ownership; model ownership through `specify`, `refine`, `verify`, and attachments.

### Model Structure

Markdown files contain **capabilities**, **ontology elements**, **requirements**, **refinements**, and **verification elements** that together form the complete semantic engineering graph. These elements are connected through:

- **Containment**: The folder and file hierarchy provides the containment structure
- **Relations**: Elements are wired together using relations such as:
  - `derivedFrom` / `derive` - hierarchy inside the same family: capability-to-capability, requirement-to-requirement, or ontology-to-ontology
  - `specify` / `specifiedBy` - bridge from requirements to capabilities
  - `refine` / `refinedBy` - ownership of refinements by capabilities or requirements
  - `verify` / `verifiedBy` - verification of capabilities or requirements
  - `satisfiedBy` - implementation or evidence links for requirements and evidence-backed verifications
  - attachments - explicit cross-subgraph reuse of ontology or compatible requirement-owned contracts

The combination of containment structure and explicit relations creates the full semantic engineering graph.

### Requirement Hierarchy Levels

Requirements are organized in hierarchical levels within the markdown structure:

- **Capability roots** are product, capability, stakeholder, regulatory, or external areas that define why a part of the model exists and own source context while attaching ontology vocabulary for that part of the model.
- **Requirements** specify capabilities through `specify`/`specifiedBy` and state what the system shall do.
- **Child requirements** use `derivedFrom`/`derive` only within the requirement hierarchy when an obligation is decomposed into more specific obligations.

This structure keeps capability ownership in capabilities while keeping implementation-facing obligations in requirements.

### Requirements and general Markdown files format

**Important:** Model files must begin with either:
- `# Elements` for multi-element files
- `# Documents` for single-element document files

Files without a supported first heading are silently ignored during model parsing.

```markdown
# Elements

### My Requirement
...
```

```markdown
# Documents

## Metadata
  * type: specification

## Relations
  * refine: [Some Requirement](Requirements.md#some-requirement)

## MyDocumentElement
Any markdown content is allowed here.
```

For detailed specifications, read [SpecificationsRequirements.md](https://github.com/reqvire-org/reqvire/blob/main/requirements/SpecificationsRequirements.md)

### Requirement Governance Metadata

Requirement elements can declare governance metadata in `#### Metadata`:

```markdown
#### Metadata
  * type: requirement
  * status: review
  * priority: high
  * risk: medium
  * owner: Platform Team
```

Governance metadata applies only to capability and requirement elements:

| Key | Values | Default |
|-----|--------|---------|
| `status` | `draft`, `review`, `approved` | `approved` |
| `priority` | `low`, `medium`, `high`, `critical` | `medium` |
| `risk` | `low`, `medium`, `high`, `critical` | `low` |
| `owner` | free-form person, role, or team | unassigned |

Child requirements inherit missing governance fields from their nearest owning parent requirement through the requirement hierarchy, or from the owning capability when a top-level requirement uses `specify`. If no parent defines a field, Reqvire uses the default effective value. Only explicitly authored metadata is written back to Markdown; inherited and default values appear in structured outputs as effective metadata.

Refinement elements (`source`, `semantic-contract`, `specification`, `constraint`, `behavior`, `state`, and `input-output`) must not declare `status`, `priority`, `risk`, or `owner`. They receive governance context from the capability or requirement that owns them via `refinedBy` / `refine`.

## Element Manipulation

Reqvire provides commands to add, move, and remove elements directly from the command line. These operations maintain model consistency by validating relations and updating parent files automatically.

### Add Element

Add a new element to your model from Markdown content:

```bash
# Add element using --content flag
reqvire add "requirements/Requirements.md" --content '### New Security Requirement

The system SHALL enforce authentication for all API endpoints.

#### Metadata
  * type: requirement

#### Relations
  * derivedFrom: Requirements.md#user-authentication
'

# Add element using heredoc (stdin)
reqvire add "requirements/Requirements.md" <<'EOF'
### New Security Requirement

The system SHALL enforce authentication for all API endpoints.

#### Metadata
  * type: requirement

#### Relations
  * derivedFrom: Requirements.md#user-authentication
EOF

# Add from file using pipe
cat element.md | reqvire add requirements/Requirements.md

# Add from file using input redirection
reqvire add requirements/Requirements.md < element.md
```

The `--content` flag provides the element markdown directly as an argument, while stdin methods (heredoc, pipe, redirection) are also supported. When `--content` is provided, stdin is not read.

The element will be appended to the end of the file following the Element Ordering Behavior (parents before children, siblings alphabetically).

#### Preview Changes

Use `--dry-run` to preview the operation without applying changes:

```bash
reqvire add "specs/Reqs.md" --dry-run < element.md
```

#### JSON Output

Get structured output for programmatic processing:

```bash
reqvire add "specs/Reqs.md" --json < element.md
```

#### Override Existing Element

Use `--override` to replace an existing element with the same name. This removes the old element and adds the new one:

```bash
# Replace an existing element with updated content
reqvire add "requirements/Requirements.md" --override <<'EOF'
### Existing Requirement Name

Updated content with new details.

#### Details

New consolidated details after merge cleanup.

#### Metadata
  * type: requirement

#### Relations
  * derivedFrom: Requirements.md#parent-requirement
EOF
```

This is particularly useful for cleanup after `reqvire merge`:
1. Run `reqvire merge` to combine elements (creates "Merged Details" artifacts)
2. Read the merged element to see the raw output
3. Prepare a clean version with proper structure
4. Use `reqvire add --override` to replace with the clean version

### Remove Element

Remove an element by its name:

```bash
reqvire rm "Security Requirement"
```

This command:
- Accepts element name (globally unique in model)
- Removes the element from its parent file
- Removes all relations pointing to the deleted element
- Maintains model consistency automatically

#### Preview Removal

Use `--dry-run` to see what would be removed:

```bash
reqvire rm "Old Requirement" --dry-run
```

### Move Element

Move an element to a different location:

```bash
# Move to different file
reqvire mv "Auth Requirement" "specs/Security.md"
```

The move operation:
- Accepts element name (globally unique in model)
- Accepts target file path as second argument
- Updates the element's identifier to reflect new location
- Updates all relations pointing to the moved element
- Places element following Element Ordering Behavior (parents before children, siblings alphabetically)
- Maintains model consistency automatically

#### Preview Move

Use `--dry-run` to preview the operation:

```bash
reqvire mv "Auth Requirement" "specs/Reqs.md" --dry-run
```

### Rename Element

Rename an element while automatically updating all relations that reference it:

```bash
# Rename an element by name
reqvire rename "Old Name" "New Name"
```

The rename operation:
- Updates the element's heading text in the markdown file
- Updates the element's identifier in the registry
- Updates all relations (both forward and backward) pointing to the renamed element
- Maintains model consistency automatically

**Note:** Element names are globally unique in Reqvire, so you only need to provide the element name (not the full file path identifier).

#### Preview Rename

Use `--dry-run` to preview the operation:

```bash
reqvire rename "Old Name" "New Name" --dry-run
```

#### JSON Output

Get structured output for programmatic processing:

```bash
reqvire rename "Old Name" "New Name" --json
```

### Merge Elements

Merge multiple source elements into a target element, combining content, relations, and attachments:

```bash
# Merge single source into target
reqvire merge "Target Requirement" "Source Requirement"

# Merge multiple sources into target
reqvire merge "Target Requirement" "Source One" "Source Two" "Source Three"
```

The merge operation:
- Accepts target element name as first argument (must exist)
- Accepts one or more source element names as subsequent arguments (must exist)
- Validates type compatibility (requirements into requirements, verifications into verifications, etc.)
- Appends source main content to target's `#### Details` section
- Creates `#### Merged Details (Source Name)` subsections for each source's Details content
- Merges and deduplicates relations (same relation type + target = duplicate)
- Merges and deduplicates attachments
- Updates all relations pointing to source elements to point to target
- Deletes source elements after successful merge
- Removes empty source files when no elements remain
- Must preserve single-root hierarchy ownership for requirements; violations are rejected or reported by validation

**Type Compatibility:**
- Requirements can merge with other requirements (any subtype)
- Verifications can merge with other verifications (any subtype)
- Refinements (constraint, behavior, specification, state, input-output) can merge with other refinements
- Other element types can merge with other elements of the same main category

**Error Conditions:**
- Target element does not exist
- Any source element does not exist
- Source and target types are incompatible
- Attempting to merge an element into itself
- Operation would violate single-root hierarchy ownership

#### Preview Merge

Use `--dry-run` to preview the operation:

```bash
reqvire merge "Target" "Source One" "Source Two" --dry-run
```

#### JSON Output

Get structured output for programmatic processing:

```bash
reqvire merge "Target" "Source" --json
```

### Move File

Move an entire specification file with all its elements to a new location:

```bash
# Move file within specifications directory
reqvire mv-file "requirements/OldFile.md" "requirements/NewFile.md"

# Move file to different directory
reqvire mv-file "requirements/Auth.md" "security/Authentication.md"

# Works from subdirectories (paths relative to current directory)
cd submodule/
reqvire mv-file "specs/File.md" "requirements/File.md"
```

The mv-file operation:
- Moves all elements from source file to target file
- Updates all element identifiers to reflect new file path
- Updates all relations pointing to moved elements (from other files)
- Preserves all element content, metadata, and relations
- Deletes the source file after successful move
- Resolves paths relative to current working directory

**Note:** Unlike `mv` which moves a single element, `mv-file` moves the entire file with all its elements.

#### Squash Mode: Merge Files

When the target file already exists, use `--squash` to merge all elements from source into the target:

```bash
# Consolidate temporary specs into main file
reqvire mv-file "temp/Experiments.md" "requirements/Requirements.md" --squash

# Merge over-fragmented files
reqvire mv-file "specs/Small.md" "specs/Main.md" --squash
```

Squash behavior:
- All source elements are appended to target file
- Target file's existing elements remain unchanged
- Source file is deleted
- All relations are updated throughout the model

**When to use --squash:**
- Consolidating experimental or temporary specifications
- Merging over-fragmented files (files with only 1-2 elements)
- Simplifying model structure by reducing file count
- Combining related requirements into a single file

**Without --squash**, attempting to move to an existing file will fail with an error.

#### Preview File Move

Use `--dry-run` to preview the operation:

```bash
reqvire mv-file "specs/Old.md" "specs/New.md" --dry-run

# Preview squash operation
reqvire mv-file "temp/A.md" "specs/Main.md" --squash --dry-run
```

#### JSON Output

Get structured output with element mappings:

```bash
reqvire mv-file "specs/Old.md" "specs/New.md" --json
```

## Link and Unlink Commands

Reqvire provides unified commands to manage both relations and attachments between elements. The `link` command creates relations or attachments, while `unlink` removes them with auto-detection.

### Link

Create a relation between elements or attach refinement element identifiers:

```bash
# Link two elements with a relation type
reqvire link "<source>" "<relation-type>" "<target>"

# Examples - linking elements
reqvire link "Password Login Requirement" "derivedFrom" "Authentication Requirement"
reqvire link "Authentication" "specifiedBy" "Authentication Requirement"
reqvire link "Authentication Requirement" "verifiedBy" "Auth Test Case"

# Link to implementation file or external URL
reqvire link "System Requirement" "satisfiedBy" "src/auth/login.rs"
reqvire link "System Requirement" "refinedBy" "Auth Constraint"
reqvire link "Compliance Requirement" "trace" "https://example.com/spec.html"

# Attach refinement element identifier (use 'attaching' keyword)
reqvire link "<element>" attaching "<refinement-identifier>"
reqvire link "Performance Requirement" attaching "#rate-limiting-constraint"
reqvire link "API Endpoint Requirement" attaching "constraints/RateLimits.md#rate-limiting-constraint"
```

The link command:
- Accepts element names (globally unique in model)
- Adds the relation to the source element's Relations section
- Creates the Relations/Attachments section if it doesn't exist
- Calculates correct relative paths for cross-file links
- Fails with error if duplicate relation or attachment already exists
- Fails with error if target exists in the other section (cross-section duplicate)
- Must preserve single-root hierarchy ownership for hierarchical relations; violations are rejected or reported by validation

**Supported relation types:**

| Relation Type | Description |
|---------------|-------------|
| `derivedFrom` | Source derives from target inside the same hierarchy family |
| `derive` | Source has derived target inside the same hierarchy family |
| `specify` | Requirement specifies a capability |
| `specifiedBy` | Capability is specified by a requirement |
| `verifiedBy` | Source is verified by target |
| `verify` | Source verifies target |
| `satisfiedBy` | Source is satisfied by target (code implementations) |
| `satisfy` | Source satisfies target (code implementations) |
| `refinedBy` | Source owns target as a compatible refinement element |
| `refine` | Source refines a compatible capability or requirement owner (auto-generated) |
| `trace` | General traceability link |

**Target types for relations:**
- Element name (e.g., "Authentication Requirement")
- Internal file path (e.g., "src/auth/login.rs")
- External URL (e.g., "https://example.com/spec.html")

`refinedBy` constraint:
- `refinedBy` must target a compatible refinement element identifier: capability to `source`, or requirement to `semantic-contract`, `constraint`, `behavior`, `specification`, `state`, or `input-output`, including refinement elements defined in `# Documents` files.
- Plain file-path targets for `refinedBy` are rejected.

**Attaching (use `attaching` keyword instead of relation type):**
- Ontology element identifier (for capability ontology context)
- Same-file refinement identifier (e.g., `#rate-limiting-constraint`)
- Cross-file refinement identifier (e.g., `constraints/RateLimits.md#rate-limiting-constraint`)

**Attachment constraints for element attachments:**
- Ontology elements may be attached by capabilities.
- Requirement attachments may target compatible requirement-owned refinement elements.
- Requirement-owned refinements must have a `refine` relation (established via requirement's `refinedBy`)
- Attachments only allowed from requirements **outside** the owner's derivation hierarchy
- Requirements in the same hierarchy access refinements through the hierarchy, not attachments

#### Preview Link

Use `--dry-run` to preview the operation:

```bash
reqvire link "Password Login Requirement" "derivedFrom" "Authentication Requirement" --dry-run
reqvire link "Performance Requirement" attaching "#rate-limiting-constraint" --dry-run
```

#### JSON Output

Get structured output for programmatic processing or write it to a file:

```bash
reqvire link "Password Login Requirement" "derivedFrom" "Authentication Requirement" --json
reqvire link "Password Login Requirement" "derivedFrom" "Authentication Requirement" --dry-run --json --output link-result.json
```

### Unlink

Remove an existing relation or attachment (auto-detects type):

```bash
# Remove a relation or attachment between elements
reqvire unlink "<source>" "<target>"

# Examples
reqvire unlink "Password Login Requirement" "Authentication Requirement"
reqvire unlink "Requirement" "Test Case"
reqvire unlink "Performance Requirement" "#rate-limiting-constraint"
```

The unlink command:
- Accepts element names (globally unique in model)
- **Auto-detects** whether to remove a relation or attachment
- Searches relations first (by element name), then attachments
- Cleans up empty Relations/Attachments sections automatically
- Only removes user-created relations (not auto-generated inverse relations)

**Auto-detection behavior:**
1. First: searches for a relation from source to target element
2. Then: if no relation found, searches for an attachment matching the target
3. Only one relation per source-target pair is allowed, so no ambiguity

#### Preview Unlink

Use `--dry-run` to preview the operation:

```bash
reqvire unlink "Password Login Requirement" "Authentication Requirement" --dry-run
```

#### JSON Output

Get structured output for programmatic processing or write it to a file:

```bash
reqvire unlink "Password Login Requirement" "Authentication Requirement" --json
reqvire unlink "Password Login Requirement" "Authentication Requirement" --dry-run --json --output unlink-result.json
```

### Relink

Replace one relation target with another in a single operation:

```bash
reqvire relink "<source>" "<relation-type>" "<from-target>" "<to-target>"

# Example
reqvire relink "Child Requirement" "derivedFrom" "Old Parent" "New Parent"
```

The relink command:
- Replaces the specified relation while preserving relation type
- Performs operation atomically (no partial updates)
- Supports dry-run preview
- Enforces model constraints, including single-root hierarchy ownership for hierarchical relations

#### Preview Relink

```bash
reqvire relink "Child Requirement" "derivedFrom" "Old Parent" "New Parent" --dry-run
```

#### JSON Output

Get structured output for programmatic processing or write it to a file:

```bash
reqvire relink "Child Requirement" "derivedFrom" "Old Parent" "New Parent" --json
reqvire relink "Child Requirement" "derivedFrom" "Old Parent" "New Parent" --dry-run --json --output relink-result.json
```

### Move Asset

Move or rename an asset file and update all references (both Attachments and Relations) across the model:

```bash
reqvire mv-asset "old/path/doc.pdf" "new/path/doc.pdf"
```

This command:
- Finds all elements referencing the file in Attachments subsections
- Finds all elements with InternalPath relations (satisfiedBy, satisfy, trace) pointing to the file
- Updates all references to the new path
- Moves the physical file on disk

#### Preview Move

```bash
reqvire mv-asset "docs/spec.pdf" "documents/spec.pdf" --dry-run
```

#### JSON Output

Get structured output for programmatic processing or write it to a file:

```bash
reqvire mv-asset "docs/spec.pdf" "documents/spec.pdf" --json
reqvire mv-asset "docs/spec.pdf" "documents/spec.pdf" --dry-run --json --output mv-asset-result.json
```

### Remove Asset

Remove an asset file and remove all references from the model:

```bash
reqvire rm-asset "path/to/file.pdf"
```

This command:
- Finds all elements referencing the file in Attachments subsections
- Finds all elements with InternalPath relations pointing to the file
- Removes all attachment entries and relation lines
- Deletes the physical file from disk

#### Preview Removal

```bash
reqvire rm-asset "docs/obsolete.pdf" --dry-run
```

#### JSON Output

Get structured output for programmatic processing or write it to a file:

```bash
reqvire rm-asset "docs/obsolete.pdf" --json
reqvire rm-asset "docs/obsolete.pdf" --dry-run --json --output rm-asset-result.json
```

## Validation

Any functional reqvire command that needs to parse model will as a first step perform model validation and report any errors found.
Errors must be fixed before command can execute.
After mutating commands (`link`, `unlink`, `merge`, `mv`, `relink`, `mv-asset`, `rm-asset`, etc.), run `reqvire validate` to confirm post-change structural integrity, including single-root hierarchy ownership.

Why single-root hierarchy ownership matters:
- Every `requirement` must belong to exactly one capability root through `specify` or inherited `derivedFrom` ancestry.
- This keeps ownership unambiguous and prevents conflicting model boundaries.
- It also keeps coverage roll-up, `collect`, and change-impact results deterministic and explainable.

## Formatting

Formatting helps maintain consistent formatting and style.

### Check Formatting (Dry-Run)

Preview formatting issues without applying changes (safe default):

```bash
reqvire format
```

This shows what would be changed but doesn't apply any fixes.

### Apply Fixes

Apply automatic fixes to formatting issues:

```bash
reqvire format --fix
```

### Element Ordering

The format command reorders elements within each file following a hierarchical ordering principle:

- **Parents before children**: Elements are ordered so parent elements appear before their children based on file-local `derivedFrom` relations
- **Alphabetical siblings**: Elements at the same level (siblings) are sorted alphabetically by name
- **Root elements first**: Elements without file-local parents are treated as roots and sorted alphabetically

This ordering makes files more readable by ensuring you encounter parent requirements before their derived children.

### Relation Ordering

Relations within each element are sorted for deterministic output:

- **Primary sort**: Alphabetically by relation type name (e.g., `derivedFrom` before `satisfiedBy`)
- **Secondary sort**: Alphabetically by target identifier within the same relation type

### Include All Relations

By default, the format command only outputs user-created relations. To include auto-generated inverse relations (e.g., `derive` from `derivedFrom`, `verify` from `verifiedBy`):

```bash
reqvire format --fix --with-full-relations
```

This is useful for:
- Reviewing complete traceability in the model
- Generating documentation with bidirectional links
- Debugging relation issues

**Note:** HTML export always includes full relations automatically.

## Linting

The lint command analyzes model quality and detects issues in requirements relations, such as redundant verify relations and redundant hierarchical relations.

It is a model hygiene signal, not a model hard-stop: cross-submodel and other manual-review findings are surfaced for refactoring guidance and must be addressed intentionally.

### Analyze All Issues

Run lint to detect all model quality issues:

```bash
reqvire lint
```

This analyzes the model and reports all detected issues.

### Filter by Issue Type

```bash
# Show only auto-fixable issues
reqvire lint --fixable

# Show only issues requiring manual review
reqvire lint --auditable
```

### Apply Automatic Fixes

Apply automatic fixes for auto-fixable issues:

```bash
reqvire lint --fix
```

**Note:** Only auto-fixable issues (like redundant verify relations or redundant direct chain hierarchical relations) will be fixed. Issues marked as requiring manual review (auditable) must be addressed manually.

### Detected Issue Types

| Issue Type | Description | Auto-Fix |
|------------|-------------|----------|
| Redundant verify relations | Verification verifies both leaf and parent requirement | Yes |
| Redundant hierarchical relations | Direct derivedFrom when indirect path exists | Yes (single chain) / Manual (multi-branch) |
| Cross-submodel hierarchical relation | Hierarchical link between different ownership roots | No |

### JSON Output

Output results in JSON format for integration with other tools:

```bash
reqvire lint --json
```

## Traceability

Track relationships between requirements and verifications using traceability capabilities.

### Generate Verification Traces

```bash
reqvire traces
```

This generates upward trace trees from verifications to owning capability roots, showing how verifications link to capabilities, requirements, requirement parent chains, and capability context. It also identifies redundant verify relations - cases where the same verification verifies both an element and an ancestor already covered through the trace path, which indicates the model is not clean. Output is in Markdown format with Mermaid diagrams by default.

#### Output Format Options

```bash
# Generate verification traces in Markdown format with Mermaid diagrams (default)
reqvire traces

# Generate verification traces in JSON format
reqvire traces --json
```

#### Filtering Options

You can filter the verification traces using various criteria:

```bash
# Filter by specific verification ID
reqvire traces --filter-id="test-auth-001"

# Filter by verification name pattern (regex)
reqvire traces --filter-name="Authentication.*"

# Filter by verification type
reqvire traces --filter-type="test-verification"

# Combine multiple filters (AND logic)
reqvire traces --filter-type="test-verification" --filter-name="Login.*" --json
```

Supported verification types: `test-verification`, `analysis-verification`, `inspection-verification`, `demonstration-verification`

#### Relative Link Generation

By default, verification traces generate links relative to the directory where reqvire was executed. You can specify a different base folder for relative links:

```bash
# Generate traces with links relative to a specific folder
reqvire traces --from-folder="docs/specs"

# This makes generated links relative to docs/specs instead of the current directory,
# useful when the output will be placed in a different location
```

#### GitHub Blob Links

```bash
# Generate traces with GitHub blob links (useful for viewing from GitHub)
reqvire traces --links-with-blobs
```

By default, traces use relative links in Mermaid diagrams. Use `--links-with-blobs` to generate diagrams with GitHub blob URLs, which makes clickable links work properly when viewing trace diagrams directly in the GitHub web interface.

## Resources Report

The `resources` command lists files referenced by the model through relations. Attachment links use refinement element identifiers and are resolved as model elements rather than file resources.

### Basic Usage

```bash
# Generate resources report in text format (default)
reqvire resources

# Generate resources report in JSON format
reqvire resources --json
```

### Report Structure

The resources report has two sections:

**Relations Section**
Lists files referenced through InternalPath relations (satisfiedBy, trace, etc.):
- Files sorted alphabetically by path
- Each file shows the relation type and referencing element
- References sorted by relation type, then by element identifier

**Attachments Section**
Shows file-based attachment references when present. In identifier-only models, this section is empty.

### Example Output

```markdown
## Relations

### core/src/impl.rs
  * [Data Integrity](requirements/Requirements.md#data-integrity) (via satisfiedBy)
  * [System Performance](requirements/Requirements.md#system-performance) (via satisfiedBy)

### docs/design.md
  * [Architecture Overview](requirements/Architecture.md#architecture-overview) (via trace)

## Summary

- **Relation Files:** 2 (3 references)
- **Attachment Files:** 0 (0 references)
```

### HTML Export Integration

The resources report is also available in the HTML export as a dedicated "Resources" page accessible from the navigation bar. This provides a browsable view of all referenced files with clickable links to the referencing elements.

## Ontologies Report

The `ontologies` command collects ontology element `#### Ontology` Turtle blocks and requirement-owned semantic-contract `#### Shapes` Turtle blocks from the graph registry.

```bash
# Generate RDF/Turtle to stdout (default)
reqvire ontologies

# Write RDF/Turtle to a file
reqvire ontologies --output ontologies.ttl

# Generate JSON-LD
reqvire ontologies --jsonld

# Write JSON-LD to a file
reqvire ontologies --jsonld --output ontologies.jsonld

# Include Reqvire model context triples
reqvire ontologies --full
```

Turtle is the default format. Use `--jsonld` only when a JSON-LD representation is needed. Default mode exports authored ontology and SHACL artifacts. Use `--full` when downstream semantic tooling also needs Reqvire model context triples for elements, relations, attachments, concept references, ontology declarations, and shape references.

The command reuses the same semantic index used by validation, so Turtle blocks are parsed once and reused for diagnostics, ontology declarations, SHACL references, and export serialization. HTML export also writes `ontologies.ttl` and includes an "Ontologies" page in the navigation bar. That page renders a searchable ontology/SHACL graph from the parsed semantic-index quads, supports full-window graph expansion, collapses anonymous SHACL property-shape blank nodes and RDF list infrastructure into owning-shape inspector constraints, and keeps the collected Turtle blocks available below the graph for audit review.

The MCP server exposes the same semantic collection through the read-only `reqvire.ontologies` tool. Use `format: "turtle"` for the default Turtle serialization, `format: "jsonld"` for JSON-LD output, and `full: true` to include generated Reqvire model context triples.

## Submodels Report

The `submodels` command analyzes requirement **subgraphs** (independent hierarchies) and cross-submodel couplings.

```bash
# Full report (all top-level submodels)
reqvire submodels

# JSON output
reqvire submodels --json

# Scoped report from one root
reqvire submodels --from "Platform System"
```

What the report includes:
- `Submodels`: independent requirement hierarchies resolved through `derivedFrom`
- `Cross-Submodel Couplings`: requirement-to-requirement relations across different top roots
- `Summary`: totals for submodels, requirements, and couplings

Why this is useful:
- Identify unnecessary coupling between otherwise independent parts of the model
- Check whether boundaries are clean before large refactors
- Support attachment-boundary modeling by making cross-submodel relations explicit

`--from` behavior:
- The selected root defines the scope boundary
- The selected root itself is **not** listed as a submodel entry
- First-level scoped branches under the selected root become reported submodels
 
For deeper semantics and JSON structure, see [Submodels and Subgraphs](submodels.md).

## Collect Content

The `collect` command aggregates content from a requirement element and its related requirements, including attachment contents, with source citations. It supports traversal in two directions: upstream (ancestors) or downstream (descendants).

### Basic Usage

```bash
# Collect content upstream - ancestors via derivedFrom (default)
reqvire collect "Capability Requirement"

# Collect content downstream - descendants via derive
reqvire collect "Capability Requirement" --direction DOWNSTREAM

# Explicit upstream direction
reqvire collect "Capability Requirement" --direction UPSTREAM

# Collect content in JSON format
reqvire collect "Capability Requirement" --json
reqvire collect "Capability Requirement" --direction DOWNSTREAM --json
```

### How It Works

The collect command:
1. Starts from the specified requirement element
2. Traverses the requirement chain in the specified direction:
   - **UPSTREAM** (default): Follows derivedFrom relations from child to parents until root ancestors are reached
   - **DOWNSTREAM**: Follows derive relations from parent to children until leaf requirements are reached
3. Collects content from each element and their attachments
4. Outputs consolidated content with source citations

### Output Ordering

- **UPSTREAM**: Ancestors appear first (root at depth 0), starting element last
- **DOWNSTREAM**: Starting element appears first (depth 0), descendants at increasing depth
- Same-depth elements sorted alphabetically by name
- Attachments appear after their parent element

### Content Collection Rules

**Element Content:**
- Collects the main body text including Details section from each element

**Attachments:**
- Element identifier attachments: Referenced element's content is included

### Output Format

**Text Output:**
Each content block is followed by a source citation and separator:

```
[Content from element or attachment]

— Source: [Element Name](file.md#element-id)

---

```

**Citation Formats:**
| Source Type | Citation Format |
|-------------|-----------------|
| Element | `— Source: [Element Name](file.md#element-id)` |
| Attachment Element | `— Source: [Refinement Name](file.md#id) attached to [Element Name](file.md#element-id)` |
| RefinedBy Element | `— Source: [Refinement Name](file.md#id) refining [Element Name](file.md#element-id)` |

**JSON Output:**
```json
{
  "starting_element": "file.md#element-id",
  "items": [
    {
      "name": "Element Name",
      "identifier": "file.md#element-id",
      "file_path": "path/to/file.md",
      "element_type": "requirement",
      "content": "The collected content...",
      "depth": 0,
      "source_type": "element"
    }
  ],
  "metadata": {
    "element_count": 3,
    "attachment_count": 1,
    "total_items": 4
  }
}
```

### Output Ordering

- Ancestors appear first (root at depth 0)
- Same-level elements sorted alphabetically by name
- Attachments appear after their parent element

### Error Handling

The command will error if:
- Element name is not found
- Element is not a valid model element type.

## Search and Filtering

The `search` command provides powerful filtering and querying capabilities to explore your semantic engineering graph. It supports comprehensive filtering with over 10 filter types that can be combined using AND logic.

### Basic Search

```bash
# Search all elements (default text output)
reqvire search

# Search with JSON output
reqvire search --json

# Abbreviated output (one-line per element)
reqvire search --short
```

### Filtering by Metadata

Filter elements by their metadata properties:

```bash
# Filter by element type (supports comma-separated list)
reqvire search --filter-type="capability"
reqvire search --filter-type="requirement"
reqvire search --filter-type="test-verification"

# Filter by multiple types (OR logic)
reqvire search --filter-type="capability,requirement"
reqvire search --filter-type="requirement,test-verification,behavior"

# Filter by file path (glob pattern)
reqvire search --filter-file="requirements/**/*.md"

# Filter by element name (regex)
reqvire search --filter-name=".*authentication.*"
```

Requirement governance metadata can be filtered by effective values, including inherited and default values:

```bash
# Find requirements currently in review
reqvire search --filter-status=review

# Find high or critical priority requirements
reqvire search --filter-priority=high,critical

# Find high-risk requirements owned by a team name pattern
reqvire search --filter-risk=high --filter-owner="Platform|Safety"

# Combine governance filters with normal filters
reqvire search --filter-type=requirement \
               --filter-status=approved \
               --filter-priority=critical \
               --json
```

Full JSON search output includes `governance_metadata` for requirement-family elements. Each field reports the effective `value`, its `source` (`explicit`, `inherited`, or `default`), and `source_identifier` when inherited from a parent requirement.

Full text and JSON search summaries also include governance metadata counters for matched requirement-family elements. JSON exposes these under `global_counters.total_governance_metadata`; text output renders the same counts in the `Requirement Governance Metadata` summary section. Unassigned owners are summarized as `unassigned`.

### Filtering by Content

Filter elements based on their text content:

```bash
# Filter by element content
reqvire search --filter-content="SHALL.*authenticate"

# Filter by parent file frontmatter content
reqvire search --filter-page-content="architecture"
```

### Filtering by Relations

Filter elements based on their relations to other elements:

```bash
# Find elements that have ALL specified relations
reqvire search --have-relations="verifiedBy,satisfiedBy"

# Find elements that do NOT have ALL specified relations
reqvire search --not-have-relations="verifiedBy"

# Find unverified requirements
reqvire search --filter-type="requirement" --not-have-relations="verifiedBy"

# Find unsatisfied verifications
reqvire search --filter-type="test-verification" --not-have-relations="satisfiedBy"
```

### Combining Filters

All filters use AND logic - elements must match ALL specified criteria:

```bash
# Find capability roots in specifications
reqvire search --filter-type="capability" \
               --filter-file="requirements/*.md" \
               --have-relations="specifiedBy"

# Find security requirements mentioning encryption
reqvire search --filter-file="**/Security*.md" \
               --filter-content="encryption" \
               --filter-type="requirement"

# Complex query with JSON output
reqvire search --filter-file="requirements/*.md" \
               --filter-type="requirement" \
               --have-relations="verifiedBy" \
               --filter-content="SHALL" \
               --json
```

### Output Modes

```bash
# Full text output with all details
reqvire search --filter-file="**/Security*.md"

# Abbreviated text output (one line per element)
reqvire search --short --filter-file="**/Security*.md"

# JSON output for programmatic processing
reqvire search --json --filter-type="requirement"

# Abbreviated JSON (omits content and verbose fields)
reqvire search --json --short
```

## Model Commands

Reqvire provides two complementary model-structure commands:
- `model` for relation-centric nested structure visualization
- `submodels` for independent capability-rooted subgraph and coupling analysis

The `model` command generates a model-centric view starting from ontology roots and capability roots by default, then follows requirements, refinements, verifications, and implementation/evidence links through nested relations. It supports both forward traversal (model roots to leaves) and reverse traversal (leaves to capability roots and other owning model context).

### Generate Model-Centric Structure

Generate a complete model-centric structure starting from ontology roots and capability roots:

```bash
reqvire model
```

This generates a hierarchical structure showing:
- Ontology roots that define reusable model vocabulary
- Capability roots (level 1)
- Requirements that specify each capability through `specifiedBy`
- Nested child requirements and relations
- Complete forward relation chains (`derive`, `specifiedBy`, `satisfiedBy`, `refinedBy`, `verifiedBy`, `trace`)
- Mermaid diagrams for each element showing its relations

Each element in the output includes a Mermaid diagram visualizing its forward relations to other elements.

For raw Mermaid text without a Markdown wrapper, use:

```bash
reqvire model --mmd
```

This emits pure Mermaid flowchart text suitable for tools that expect `.mmd` content. It does not include Markdown headings or fenced code blocks.

When working from the Reqvire repository checkout, the helper script can render that stream to SVG or PNG:

```bash
cargo run -- model --mmd | scripts/render-mmd.sh -o /tmp/model.svg --scale 1
cargo run -- model --mmd | scripts/render-mmd.sh -o /tmp/model.png --scale 2
```

### Filter by Element Name

Generate a filtered model starting from a specific element by name:

```bash
# Show model structure starting from named element
reqvire model --from "User Authentication"

# Show nested structure for specific capability
reqvire model --from "Data Storage System"
```

This includes only the specified element and elements reachable by following forward relations (`derive`, `specifiedBy`, `satisfiedBy`, `refinedBy`, `verifiedBy`, `trace`) from it.

### Reverse Traversal

Use `--reverse` to traverse from leaf elements upward to owning capability roots:

```bash
# Traverse from verifications up to requirements and capability roots
reqvire model --reverse

# Reverse traversal with JSON output
reqvire model --reverse --json
```

In reverse mode:
- Starting elements are "leaf" elements (elements with backward relations but no forward children)
- Traversal follows backward relations: `derivedFrom`, `specify`, `satisfy`, `refine`, `verify`
- Useful for understanding how verifications trace back to requirements and owning capabilities
- Shows the upward path from implementation/evidence to capability-rooted capability context

### Filter by Element Type

Filter starting elements by type using `--filter-type`:

```bash
# Show model starting only from capabilities
reqvire model --filter-type="capability"

# Show model starting only from verifications
reqvire model --filter-type="test-verification"

# Combine with reverse to trace specific verification types upward
reqvire model --reverse --filter-type="test-verification"

# Multiple types (comma-separated, OR logic)
reqvire model --filter-type="capability,requirement"
reqvire model --filter-type="test-verification,analysis-verification"
```

**Valid element types:**
- `capability` (coherent operational, product, business, regulatory, or system abilities)
- `requirement` (system obligations)
- `ontology` (shared semantic vocabulary)
- `test-verification`, `formal-proof-verification`, `analysis-verification`, `inspection-verification`, `demonstration-verification` (verifications)
- `source`, `semantic-contract`, `constraint`, `behavior`, `specification`, `state`, `input-output` (refinements)
- `other-TYPENAME` for custom types (e.g., `other-use-case`)

### Output Format Options

```bash
# Generate model in text format with Mermaid diagrams (default)
reqvire model

# Generate filtered text output from specific element
reqvire model --from "Security Requirements"

# Generate pure Mermaid flowchart text
reqvire model --mmd

# Generate complete model structure in JSON format (nested)
reqvire model --json

# Generate filtered JSON starting from specific element
reqvire model --from "API Layer" --json

# Reverse traversal with type filter and JSON output
reqvire model --reverse --filter-type="test-verification" --json

# Include element size estimates in JSON output
reqvire model --json --with-size-estimates
```

The JSON output contains the nested model structure with elements containing their forward-related child elements, making it suitable for programmatic analysis and integration with other tools.

Use `--with-size-estimates` with `--json` when a downstream tool needs approximate context sizing for model elements. Each element payload includes:

```json
"size_estimate": {
  "content_bytes": 123,
  "rendered_context_bytes": 456,
  "estimated_tokens": 114
}
```

Size estimates are opt-in, are not written to Markdown files, and are available only for JSON model evidence. Running `reqvire model --with-size-estimates` without `--json` fails with a clear diagnostic.

## Change Impact Report

Generates change impact report comparing current git HEAD with a previous commit.

### Generate Change Impact Report

```bash
reqvire change-impact
```

This generates a report showing how changes affect related requirements. By default, it compares with HEAD~1 (the previous commit).

The report identifies:
- **Changed elements** - Content or relation changes
- **Relocated elements** - Elements moved between files (shown with old/new locations)
- **New elements** - Newly added elements
- **Removed elements** - Deleted elements
- **Impact scope** - Per-branch common parent requirements covering all impacted elements, giving reviewers a high-level summary of affected model areas
- **Invalidated verifications** - Verification elements that need re-review due to requirement changes

Elements that are both relocated and changed will appear in both the "Relocated" and "Changed" sections.

The impact scope computes the lowest common ancestors of all impacted requirements through the `derivedFrom` hierarchy. When sibling requirements are both affected, they merge into their parent. This provides a concise summary of which model areas are impacted.

#### Options

```bash
# Use default comparison with HEAD~1
reqvire change-impact

# Compare with a specific commit (hash or reference)
reqvire change-impact --git-commit=a1b2c3d4

# Output in JSON format for integration with other tools
reqvire change-impact --json
```

## Generating Documentation

Reqvire can export your model to browsable HTML documentation with complete traceability.

### Export to HTML

```bash
# Export to a temporary directory (path will be printed)
reqvire export

# Export to a specific output folder
reqvire export --output output_folder
```

This creates HTML files with navigation, properly formatted requirements, interactive diagrams, verification traces, and coverage reports. If no `--output` is specified, the export goes to a temporary directory and the path is printed for easy access.

The HTML export includes:
- **Interactive Diagrams**: All Mermaid diagrams include click handlers that navigate to element definitions
- **Color-Coded Elements**: Requirements and verifications are color-coded by type for easy identification
- **Pan and Zoom**: Diagrams support pan/zoom functionality with auto-centering
- **Model-Centric View**: Nested hierarchical structure showing forward relations
- **Complete Traceability**: Verification traces, coverage reports, and traceability matrices
- **Navigation**: Index page with links to all documentation sections

### Serve Documentation Locally

For local development and preview, use the serve command to start a local HTTP server:

```bash
reqvire serve
```

This command starts a local server serving your documentation. It's particularly useful for local development and for integrating with AI assistants via Playwright or Puppeteer MCP servers, giving them visual access to your documentation.

#### Command Options

```bash
# Start server on default host and port (localhost:8080)
reqvire serve

# Specify custom host and port
reqvire serve --host 0.0.0.0 --port 3000
```

**Options:**
- `--host <HOST>` - Bind address (default: `localhost`)
- `--port <PORT>` - Server port (default: `8080`)

The server will display the URL. Press `Ctrl-C` to stop the server.

## Diagrams

Reqvire automatically generates diagrams when exporting your model to HTML. Diagrams are embedded in the exported HTML documentation showing element relationships.

### Containment View

Analyze the physical structure of your model - how elements are organized across folders and files:

```bash
# View containment hierarchy as Mermaid diagram
reqvire containment

# Get JSON structure for programmatic analysis
reqvire containment --json
```

**What containment shows:**
- Folder hierarchy
- Files within each folder
- Elements within each file (filtered to show only top-level parents)

**Note:** Containment view filters elements to show only those without hierarchical parent relations in the same file. For actual element counts per file, use `reqvire search --json`.

**When to use containment:**
- Understanding overall model organization
- Planning file reorganization
- Identifying folder structure issues
- Before large-scale refactoring with mv-file

Use containment together with `submodels` and `search` when planning structure changes. A common target is a capability-centered model with `Capabilities/`, `Ontologies/`, and `Verifications/` planes, but this is guidance rather than a hard schema. Preserve valid project-specific naming when the graph remains clear.

**Example: Find files that need consolidation**

```bash
# Get element counts per file
reqvire search --json | jq -r '.files | to_entries[] | "\(.key): \(.value.total_elements) elements"'

# Find small files (candidates for consolidation with --squash)
reqvire search --json | jq -r '.files | to_entries[] | select(.value.total_elements < 3) | .path'

# Find large files (candidates for splitting)
reqvire search --json | jq -r '.files | to_entries[] | select(.value.total_elements > 20) | .path'
```

## GitHub Integration

Reqvire integrates with GitHub workflows to automate various tasks and provide additional functionality during pull requests and regular development.

### GitHub Actions

Reqvire includes several GitHub Actions workflows that can be used in your repository. You can easily install Reqvire in any GitHub Actions workflow using this oneliner:

```yaml
- name: Install Reqvire
  run: curl -fsSL https://raw.githubusercontent.com/reqvire-org/reqvire/main/scripts/install.sh | bash
```

#### PR Validation

The PR validation workflow runs automatically on every pull request to validate your Reqvire model:

```yaml
name: Validate Reqvire Model on PR

on:
  pull_request:
    branches:
      - main
    types: [opened, synchronize, reopened]  # Trigger on PR creation, updates, and reopening


jobs:
  validate:
    name: Validate Reqvire Model
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4
        
      - name: Install Reqvire
        run: curl -fsSL https://raw.githubusercontent.com/reqvire-org/reqvire/main/scripts/install.sh | bash
        
      - name: Validate requirements and generate report
        id: validate
        continue-on-error: true
        run: |
          mkdir -p reports
          reqvire validate | tee reports/validation_report.txt

      - name: Upload Validation Report if validation failed
        if: failure()
        uses: actions/upload-artifact@v4
        with:
          name: reqvire-validation-report
          path: reports/validation_report.txt
```

### GitHub Issue Comment Commands

Reqvire supports command-driven operations through GitHub issue comments. These commands can be used in pull request discussions to trigger specific Reqvire operations.

#### Available Commands

| Command | Description |
|---------|-------------|
| `/reqvire impact` | Triggers change impact analysis |

#### Setup Example

Here's how to set up the comment commands in your workflow:

```yaml
name: Reqvire PR Commands

on:
  issue_comment:
    types: [created]

jobs:
  run-reqvire:
    if: |
      github.event.issue.pull_request != null &&
      (
        contains(github.event.comment.body, '/reqvire impact') ||
        contains(github.event.comment.body, '/reqvire traces')
      )
    runs-on: ubuntu-latest
       
    permissions:
      pull-requests: write
      issues: write
      contents: read
      
    
    steps:
      - name: Checkout PR Branch
        uses: actions/checkout@v4
        with:
          fetch-depth: 0
      
      - name: React to trigger comment with
        uses: peter-evans/create-or-update-comment@v3
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          comment-id: ${{ github.event.comment.id }}
          reactions: '+1'
          
      - name: Checkout PR source branch
        uses: actions/checkout@v4
                    
      - name: Get PR Head Ref using gh
        id: get_pr_head
        env:
          GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        run: |
          HEAD_REF=$(gh pr view ${{ github.event.issue.number }} --json headRefName --jq '.headRefName')
          echo "Using PR head ref: $HEAD_REF"
          echo "HEAD_REF=$HEAD_REF" >> $GITHUB_ENV
              
      - name: Checkout PR source branch
        uses: actions/checkout@v4
        with:
          ref: ${{ env.HEAD_REF }}
          fetch-depth: 0  # needed for full commit history
              
      - name: Get Base Branch from PR using gh
        id: get_pr_base
        env:
          GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        run: |
          BASE_BRANCH=$(gh pr view ${{ github.event.issue.number }} --json baseRefName --jq '.baseRefName')
          echo "Using base branch: $BASE_BRANCH"
          echo "BASE_BRANCH=$BASE_BRANCH" >> $GITHUB_ENV
          
      - name: Compute merge base commit
        id: base_commit
        run: |
          git fetch origin "$BASE_BRANCH"
          BASE_COMMIT=$(git merge-base origin/"$BASE_BRANCH" HEAD)
          echo "Base commit: $BASE_COMMIT"
          echo "BASE_COMMIT=$BASE_COMMIT" >> $GITHUB_ENV

      - name: Ensure PR branch remains checked out
        run: |
          git checkout ${{ github.event.issue.pull_request.head.ref }}
         
                          
      - name: Install Reqvire
        run: curl -fsSL https://raw.githubusercontent.com/reqvire-org/reqvire/main/scripts/install.sh | bash
      
      - name: Run Reqvire Impact (if triggered)
        if: contains(github.event.comment.body, '/reqvire impact')
        id: run_impact
        run: |
          OUTPUT=$(reqvire change-impact --git-commit "$BASE_COMMIT" 2>&1 || echo "⚠️ reqvire impact failed.")
          echo "REQVIRE_OUTPUT<<EOF" >> $GITHUB_ENV
          echo "$OUTPUT" >> $GITHUB_ENV
          echo "EOF" >> $GITHUB_ENV

      - name: Run Reqvire Verification Traces (if triggered)
        if: contains(github.event.comment.body, '/reqvire traces')
        id: run_traces
        run: |
          OUTPUT=$(reqvire traces || echo "⚠️ reqvire traces failed.")
          echo "REQVIRE_OUTPUT<<EOF" >> $GITHUB_ENV
          echo "$OUTPUT" >> $GITHUB_ENV
          echo "EOF" >> $GITHUB_ENV
          
      - name: Comment on PR with result
        uses: peter-evans/create-or-update-comment@v3
        with:
          issue-number: ${{ github.event.issue.number }}
          body: |
            ${{ env.REQVIRE_OUTPUT }}            
```

These commands provide valuable insights during the pull request review process, helping reviewers understand the impact of changes on the semantic engineering graph.
