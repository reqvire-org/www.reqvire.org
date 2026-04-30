---
layout: page
nav_order: 4
title: MCP Server
---

# MCP Server

Reqvire can run as a Model Context Protocol (MCP) server so MCP-capable coding assistants can inspect and operate on the Reqvire model through structured tools instead of shell commands.

The MCP server is a Reqvire CLI subcommand:

```bash
reqvire mcp
```

By default, the server uses stdio transport and advertises read/report tools only. Mutation tools are not advertised unless explicitly enabled.

## Startup

Start the default stdio server from the repository that contains the Reqvire model:

```bash
reqvire mcp
```

Start it for a specific repository from another working directory:

```bash
reqvire --workspace /path/to/repository mcp
```

Reqvire validates the model before the MCP server starts. If startup validation fails, the server exits with the same kind of Reqvire validation diagnostics that CLI users already see.

## Element Size Estimates

Start the MCP server with `--with-size-estimates` when MCP clients need approximate context sizing for model evidence:

```bash
reqvire mcp --with-size-estimates
```

When enabled, tools that return model elements, such as `reqvire.read_element` and `reqvire.model`, include an element-level `size_estimate` record:

```json
"size_estimate": {
  "content_bytes": 123,
  "rendered_context_bytes": 456,
  "estimated_tokens": 114
}
```

The flag is a server startup option, not a per-tool argument. Without it, MCP model evidence omits size estimates. The active state is reported as `size_estimates_enabled` in `reqvire.workspace_status` and in the tool contract resource.

## HTTP Transport

Reqvire can also expose MCP over HTTP:

```bash
reqvire mcp --transport http --host 127.0.0.1 --port 8081
```

The HTTP endpoint is fixed:

```text
http://127.0.0.1:8081/mcp
```

HTTP requests use JSON-RPC POST bodies.

Example HTTP request:

```bash
curl -sS \
  -H 'Content-Type: application/json' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list","params":{}}' \
  http://127.0.0.1:8081/mcp
```

For browser-originated HTTP requests, Reqvire accepts missing `Origin` headers and loopback origins such as `localhost`, `127.x.x.x`, and `::1`. Non-loopback origins are rejected.

## Protocol Handshake

Reqvire reports MCP protocol version `2025-11-25`.

Example initialize request:

```json
{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2025-11-25"}}
```

The initialize response advertises `tools` and `resources` capabilities. Unsupported capabilities are not advertised.

## Tool Discovery

Use `tools/list` to discover the active tool contract:

```json
{"jsonrpc":"2.0","id":2,"method":"tools/list","params":{}}
```

Each tool definition includes:

- `inputSchema`
- `outputSchema`
- `annotations`

Tool call results include text content for clients that render messages and `structuredContent` for clients that consume machine-readable results.

You can also inspect the same contract through:

```text
reqvire://tools/contract
```

## Read Tools

Default mode advertises read/report tools:

- `reqvire.workspace_status`
- `reqvire.tool_contract`
- `reqvire.model_revision`
- `reqvire.read_element`
- `reqvire.search`
- `reqvire.model`
- `reqvire.containment`
- `reqvire.collect`
- `reqvire.submodels`
- `reqvire.lint`
- `reqvire.coverage`
- `reqvire.traces`
- `reqvire.resources`
- `reqvire.change_impact`
- `reqvire.format`

In default mode, `reqvire.format` is preview-only. Its `fix` argument is constrained to `false`.

Example search call:

```json
{"jsonrpc":"2.0","id":3,"method":"tools/call","params":{"name":"reqvire.search","arguments":{"filter_type":"requirement","short":true}}}
```

## Resources

Reqvire exposes these MCP resources:

- `reqvire://workspace/status`
- `reqvire://workspace/model-revision`
- `reqvire://tools/contract`

Read a resource with `resources/read`:

```json
{"jsonrpc":"2.0","id":4,"method":"resources/read","params":{"uri":"reqvire://workspace/status"}}
```

## Mutation Tools

Mutation tools are disabled by default. Enable them explicitly:

```bash
reqvire mcp --enable-mutations
```

For HTTP:

```bash
reqvire mcp --transport http --enable-mutations
```

When mutation mode is enabled, Reqvire advertises additional tools:

- `reqvire.add_element`
- `reqvire.remove_element`
- `reqvire.move_element`
- `reqvire.rename_element`
- `reqvire.merge_elements`
- `reqvire.move_file`
- `reqvire.link`
- `reqvire.unlink`
- `reqvire.relink`
- `reqvire.move_asset`
- `reqvire.remove_asset`

Mutation tools use Reqvire core operations and return structured diffs. Most mutation tools support `dry_run`; use tool discovery to inspect exact arguments.

When mutation mode is enabled, `reqvire.format` can also apply formatting with `fix: true`.

Example dry-run mutation:

```json
{
  "jsonrpc": "2.0",
  "id": 5,
  "method": "tools/call",
  "params": {
    "name": "reqvire.add_element",
    "arguments": {
      "file": "requirements/Requirements.md",
      "content": "### New Requirement\n\nRequirement text.\n\n#### Metadata\n  * type: requirement\n",
      "dry_run": true
    }
  }
}
```

HTTP mutation requests are serialized by the server so concurrent clients cannot interleave filesystem writes.

## Error Handling

Tool execution errors are returned as MCP tool results with `isError: true`. The structured payload includes a stable Reqvire error code, message, tool name, recoverability hint, and related validation errors when available.

Common error codes include:

- `validation_failed`
- `duplicate_element`
- `element_not_found`
- `invalid_relation_type`
- `invalid_element_type_for_relation`
- `attachment_contract_violation`
- `single_root_ownership_violation`
- `filesystem_error`
- `git_error`
- `serialization_error`
- `reqvire_error`
