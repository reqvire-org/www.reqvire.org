---
layout: page
title: false
---

This user guide provides detailed instructions on how to install and use Reqvire effectively.

## Table of Contents

- [Installation](#installation)
- [Basic Commands](#basic-commands)
- [File Exclusion Patterns](#file-exclusion-patterns)
- [Working with Requirements](#working-with-requirements)
- [Element Manipulation](#element-manipulation)
- [Attachment Commands](#attachment-commands)
- [Relation Commands](#relation-commands)
- [Validation](#validation)
- [Formatting](#formatting)
- [Linting](#linting)
- [Generating Documentation](#generating-documentation)
- [Traceability](#traceability)
- [Resources Report](#resources-report)
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

Reqvire offers several core commands for managing your requirements:

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

## Working with Requirements

Reqvire is designed to work with a structured requirements hierarchy in Markdown files. Requirements are organized using **folders and files for logical containment** - representing subsystems, features, or functional areas.

### Folder Structure

Reqvire supports flexible organization based on **architectural decomposition** - structuring by subsystem/component rather than by artifact type. Requirements can be organized separately from implementation or co-located with code.

**Example 1: Requirements separate from implementation**

```
project/
├── Requirements.md
├── Authentication/
│   └── Requirements.md           # Authentication subsystem requirements
├── Storage/
│   └── Requirements.md           # Storage subsystem requirements
├── API/
│   └── Requirements.md           # API subsystem requirements
└── src/
    ├── auth.rs
    ├── storage.rs
    └── api.rs
```

**Example 2: Requirements co-located with implementation**

```
project/
├── Requirements.md
└── src/
    ├── authentication/
    │   ├── Requirements.md       # Authentication subsystem requirements
    │   └── auth.rs
    ├── storage/
    │   ├── Requirements.md       # Storage subsystem requirements
    │   └── storage.rs
    └── api/
        ├── Requirements.md       # API subsystem requirements
        └── api.rs
```

Both approaches organize by architectural decomposition. The co-location approach provides additional benefits for developers and AI coding assistants by placing requirements directly alongside the code they describe.

### Model Structure

Markdown files contain **requirements** and **verification elements** that together form the complete model structure. These elements are connected through:

- **Containment**: The folder and file hierarchy provides the containment structure
- **Relations**: Elements are wired together using relations such as:
  - `derivedFrom` - hierarchical relation showing how detailed requirements derive from higher-level ones
  - `verifies` - linking verifications to requirements they verify
  - `trace` - soft relations for traceability between elements

The combination of containment structure and explicit relations creates the full requirements model.

### Requirement Hierarchy Levels

Requirements are organized in hierarchical levels within the markdown structure:

- **Level 1 requirements** are root-level requirements that don't have parent relations. These are typically found at the top level of your main requirements file.
- **Level 2 requirements** (children of level 1) are usually **user requirements** - high-level stakeholder needs and system objectives.
- **Deeper levels** typically contain **system requirements** - detailed technical specifications that derive from and implement the user requirements above them.

This hierarchy naturally reflects the progressive refinement from high-level user needs down to detailed technical implementation.

### Requirements and general Markdown files format

**Important:** All specification files must begin with `# Elements` as the first level-1 heading. This header identifies the file as a Reqvire specification file. Files without this header are silently ignored during model parsing.

```markdown
# Elements

### My Requirement
...
```

For detailed specifications, read [SpecificationsRequirements.md](https://github.com/reqvire-org/reqvire/blob/main/requirements/SpecificationsRequirements.md)

## Element Manipulation

Reqvire provides commands to add, move, and remove elements directly from the command line. These operations maintain model consistency by validating relations and updating parent files automatically.

### Add Element

Add a new element to your model from Markdown content:

```bash
# Add element using heredoc
reqvire add "requirements/Requirements.md" <<'EOF'
### New Security Requirement

The system SHALL enforce authentication for all API endpoints.

#### Metadata
  * type: system-requirement

#### Relations
  * derivedFrom: Requirements.md#user-authentication
EOF

# Add from file using pipe
cat element.md | reqvire add requirements/Requirements.md

# Add from file using input redirection
reqvire add requirements/Requirements.md < element.md

# Add inline using echo and pipe
echo "### My Requirement..." | reqvire add requirements/Requirements.md
```

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

## Attachment Commands

Reqvire supports attaching external files and Refinement elements to any element. Attachments are stored in the element's `#### Attachments` subsection.

### Attach

Attach a file or Refinement element to an element:

```bash
# Attach a file
reqvire attach "docs/sla-document.pdf" "Performance Requirement"

# Attach a Refinement element (constraint, behavior, or specification)
reqvire attach "Rate Limiting Constraint" "API Endpoint Requirement"
```

**Auto-detection behavior:**
1. The system first checks if the attachment exists as a file (relative to cwd or git root)
2. If file exists: attaches as file path
3. If no file exists: looks up element by name (must be Refinement type: constraint, behavior, or specification)
4. Error if neither file nor element found

**Result:**
```markdown
#### Attachments
  * [sla-document.pdf](docs/sla-document.pdf)
  * [Rate Limiting Constraint](Behaviors.md#rate-limiting-constraint)
```

#### Preview Attachment

Use `--dry-run` to preview the operation:

```bash
reqvire attach "docs/spec.pdf" "My Requirement" --dry-run
```

### Detach

Remove an attachment from an element:

```bash
# Detach a file
reqvire detach "Performance Requirement" "docs/sla-document.pdf"

# Detach a Refinement element
reqvire detach "API Endpoint Requirement" "Rate Limiting Constraint"
```

The same auto-detection logic applies: file paths are checked first, then element names.

#### Preview Detachment

Use `--dry-run` to preview the operation:

```bash
reqvire detach "My Requirement" "docs/spec.pdf" --dry-run
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

## Relation Commands

Reqvire provides commands to link and unlink relations between elements directly from the command line. These operations allow you to establish and manage traceability relationships without manually editing markdown files.

### Link

Create a relation between two elements:

```bash
# Link two elements with a relation type
reqvire link "<source-element>" "<relation-type>" "<target-element>"

# Examples
reqvire link "Feature Requirement" "derivedFrom" "User Story"
reqvire link "System Requirement" "derive" "Feature Requirement"
reqvire link "Authentication Requirement" "verifiedBy" "Auth Test Case"
reqvire link "Element A" "trace" "Element B"
```

The link command:
- Accepts element names (globally unique in model)
- Adds the relation to the source element's Relations section
- Creates the Relations section if it doesn't exist
- Calculates correct relative paths for cross-file links
- Is idempotent (duplicate links are silently ignored)

**Supported relation types:**

| Relation Type | Description |
|---------------|-------------|
| `derivedFrom` | Source derives from target (child to parent) |
| `derive` | Source has derived target (parent to child) |
| `verifiedBy` | Source is verified by target |
| `verify` | Source verifies target |
| `satisfiedBy` | Source is satisfied by target |
| `satisfy` | Source satisfies target |
| `trace` | General traceability link |

#### Preview Link

Use `--dry-run` to preview the operation:

```bash
reqvire link "Feature Requirement" "derivedFrom" "User Story" --dry-run
```

#### JSON Output

Get structured output for programmatic processing:

```bash
reqvire link "Feature Requirement" "derivedFrom" "User Story" --json
```

### Unlink

Remove an existing relation between two elements:

```bash
# Remove a relation between elements
reqvire unlink "<source-element>" "<relation-type>" "<target-element>"

# Examples
reqvire unlink "Feature Requirement" "derivedFrom" "User Story"
reqvire unlink "Requirement" "verifiedBy" "Test Case"
```

The unlink command:
- Accepts element names (globally unique in model)
- Removes the specified relation from the source element
- Cleans up empty Relations sections automatically
- Only removes user-created relations (not auto-generated inverse relations)

#### Preview Unlink

Use `--dry-run` to preview the operation:

```bash
reqvire unlink "Feature Requirement" "derivedFrom" "User Story" --dry-run
```

#### JSON Output

Get structured output for programmatic processing:

```bash
reqvire unlink "Feature Requirement" "derivedFrom" "User Story" --json
```

## Validation

Any functional reqvire command that needs to parse model will as a first step perform model validation and report any errors found.
Errors must be fixed before command can execute.

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

The lint command analyzes model quality and detects issues in requirements relations, such as redundant verify relations and potentially redundant hierarchical relations.

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

**Note:** Only auto-fixable issues (like redundant verify or redundant direct chain hierarchical relations) will be fixed. Issues marked as requiring manual review (auditable) must be addressed manually.

### JSON Output

Output results in JSON format for integration with other tools:

```bash
reqvire lint --json
```

## Traceability

Track relationships between requirements and verifications using traceability features.

### Generate Verification Traces

```bash
reqvire traces
```

This generates upward trace trees from verifications to root requirements, showing how verifications link to requirements and their parent chains. It also identifies redundant verify relations - cases where the same verification verifies both a leaf requirement and its parent, which indicates the model is not clean. Output is in Markdown format with Mermaid diagrams by default.

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

The `resources` command lists all files referenced by the model through relations and attachments. This helps you understand which implementation files, design documents, and other assets are connected to your requirements.

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
Lists files referenced through FilePath attachments:
- Files sorted alphabetically by path
- Each file shows the referencing element
- References sorted by element identifier

### Example Output

```markdown
## Relations

### core/src/impl.rs
  * [Data Integrity](requirements/Requirements.md#data-integrity) (via satisfiedBy)
  * [System Performance](requirements/Requirements.md#system-performance) (via satisfiedBy)

### docs/design.md
  * [Architecture Overview](requirements/Architecture.md#architecture-overview) (via trace)

## Attachments

### docs/api-spec.md
  * [API Requirements](requirements/Requirements.md#api-requirements)

## Summary

- **Relation Files:** 2 (3 references)
- **Attachment Files:** 1 (1 references)
```

### HTML Export Integration

The resources report is also available in the HTML export as a dedicated "Resources" page accessible from the navigation bar. This provides a browsable view of all referenced files with clickable links to the referencing elements.

## Search and Filtering

The `search` command provides powerful filtering and querying capabilities to explore your requirements model. It supports comprehensive filtering with over 10 filter types that can be combined using AND logic.

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
# Filter by element type
reqvire search --filter-type="user-requirement"
reqvire search --filter-type="system-requirement"
reqvire search --filter-type="test-verification"

# Filter by file path (glob pattern)
reqvire search --filter-file="requirements/**/*.md"

# Filter by element name (regex)
reqvire search --filter-name=".*authentication.*"
```

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
# Find unverified user requirements in specifications
reqvire search --filter-type="user-requirement" \
               --filter-file="requirements/*.md" \
               --not-have-relations="verifiedBy"

# Find security requirements mentioning encryption
reqvire search --filter-file="**/Security*.md" \
               --filter-content="encryption" \
               --filter-type="system-requirement"

# Complex query with JSON output
reqvire search --filter-file="requirements/*.md" \
               --filter-type="user-requirement" \
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

The `model` command generates a model-centric view showing requirements and verifications with their nested relations as a hierarchical tree structure.

### Generate Model-Centric Structure

Generate a complete model-centric structure starting from root requirements:

```bash
reqvire model
```

This generates a hierarchical structure showing:
- Root requirements (level 1)
- Their nested child requirements and relations
- Complete forward relation chains (derive, satisfiedBy, verifiedBy, trace)
- Mermaid diagrams for each element showing its relations

Each element in the output includes a Mermaid diagram visualizing its forward relations to other elements.

### Filter by Element Name

Generate a filtered model starting from a specific element by name:

```bash
# Show model structure starting from named element
reqvire model --from "User Authentication"

# Show nested structure for specific feature
reqvire model --from "Data Storage System"
```

This includes only the specified element and elements reachable by following forward relations (derive, satisfiedBy, verifiedBy, trace) from it.

### Output Format Options

```bash
# Generate model in text format with Mermaid diagrams (default)
reqvire model

# Generate filtered text output from specific element
reqvire model --from "Security Requirements"

# Generate complete model structure in JSON format (nested)
reqvire model --json

# Generate filtered JSON starting from specific element
reqvire model --from "API Layer" --json
```

The JSON output contains the nested model structure with elements containing their forward-related child elements, making it suitable for programmatic analysis and integration with other tools.

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

Elements that are both relocated and changed will appear in both the "Relocated" and "Changed" sections.

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

The PR validation workflow runs automatically on every pull request to validate your requirements model:

```yaml
name: Validate Requirements on PR

on:
  pull_request:
    branches:
      - main
    types: [opened, synchronize, reopened]  # Trigger on PR creation, updates, and reopening


jobs:
  validate:
    name: Validate Requirements
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

These commands provide valuable insights during the pull request review process, helping reviewers understand the impact of changes on the requirements model.

