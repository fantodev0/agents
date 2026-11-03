---
name: mcporter-mcp
description: Universal bridge for the Model Context Protocol (MCP). Use this to discover, call, and manage external MCP tools (GitHub, Linear, local scripts) via the mcporter CLI or runtime.
license: MIT
compatibility: Requires Node.js (npx) and local MCP configs (Claude Desktop, Cursor, or Windsurf).
metadata:
  author: steipete
  repo: https://github.com/steipete/mcporter
  version: "0.7.0"
---

# MCPorter MCP Bridge

This skill enables an agent to communicate with any MCP server. It acts as a router, allowing the agent to use tools that aren't natively built into its environment by leveraging the `mcporter` utility.

## Operational Workflow

### 1. Discovery

Always list tools first to understand the available "catalog" on the user's system.

- **Command**: `npx mcporter list`
- **Goal**: Identify the `<server>` and `<tool>` names and their required parameters.

### 2. Execution

Execute tools using the `call` command. Use the function-call syntax to ensure complex strings or nested objects are parsed correctly.

- **Syntax**: `npx mcporter call '<server>.<tool>(<args>)'`
- **Example**: `npx mcporter call 'linear.create_issue(title: "API Error", team: "BACKEND")'`

### 3. Debugging & Logs

If a tool fails without a clear error message, use the log tailing feature to see the MCP server's internal stderr.

- **Command**: `npx mcporter call <server>.<tool> --tail-log`

## Common Use Cases

| Task            | Command Example                                                           |
| :-------------- | :------------------------------------------------------------------------ |
| **Search Docs** | `npx mcporter call 'context7.get-library-docs(libraryName: "react")'`     |
| **Browser Ops** | `npx mcporter call chrome-devtools.take_snapshot`                         |
| **Web Scrape**  | `npx mcporter call 'firecrawl.scrape(url: "https://docs.anthropic.com")'` |

## Troubleshooting

- **Auth Errors**: If a tool returns a 401 or "Unauthorized," instruct the user to run `npx mcporter auth <server>`.
- **Missing Tools**: If `list` doesn't show a tool, verify the server is defined in the user's `config.json` for their IDE (Cursor/Claude).

## Constraints

- Do not guess arguments. Always use `npx mcporter list <server>` to see the exact JSON schema.
- Maintain shell safety by wrapping the entire call string in single quotes: `'server.tool(arg: "val")'`.
