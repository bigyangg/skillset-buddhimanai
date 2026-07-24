---
name: mcp-server
description: Build Model Context Protocol (MCP) servers that expose tools, resources, and prompts to AI agents. Use when a user says "MCP server", "Model Context Protocol", "build a tool for Claude", "expose my API to an AI", "MCP tools", "MCP resources", "MCP prompts", "Claude Desktop integration", or "agent tool server".
---

# MCP Server

## What This Does

Build, test, and deploy Model Context Protocol (MCP) servers — the open standard for connecting AI models to external tools, data sources, and custom capabilities. MCP servers expose **tools** (callable functions), **resources** (readable data), and **prompts** (reusable templates) to any MCP-compatible AI client.

## Core Concepts

| Primitive | Description | Analogy |
|-----------|-------------|---------|
| **Tool** | A function the AI can call with arguments | API endpoint |
| **Resource** | A URI-addressable piece of data the AI can read | REST GET request |
| **Prompt** | A reusable prompt template the AI can invoke | Macro / slash command |

## Non-Negotiables

- **Validate all tool inputs.** Use Zod (TS) or Pydantic (Python) schemas — never trust raw AI-provided arguments.
- **Return structured errors.** Use `isError: true` in tool responses for failures; don't throw exceptions.
- **Keep tools focused.** One tool, one job. Broad "do everything" tools confuse the AI.
- **Describe tools precisely.** Tool descriptions are what the AI reads to decide when to call them — write them like API docs.
- **Resources are read-only.** Resources must not mutate state. Only tools should have side effects.

## Setup (TypeScript SDK)

```bash
npm install @modelcontextprotocol/sdk zod
```

## Minimal MCP Server

```typescript
// server.ts
import { McpServer } from '@modelcontextprotocol/sdk/server/mcp.js'
import { StdioServerTransport } from '@modelcontextprotocol/sdk/server/stdio.js'
import { z } from 'zod'

const server = new McpServer({
  name:    'my-tools',
  version: '1.0.0',
})

// ─── Tool ─────────────────────────────────────────────────────────────────
server.tool(
  'get_weather',
  'Retrieve current weather conditions for a specific city and country.',
  {
    city:    z.string().describe('City name, e.g. "Tokyo"'),
    country: z.string().length(2).describe('ISO 3166-1 alpha-2 country code, e.g. "JP"').optional(),
  },
  async ({ city, country }) => {
    try {
      const location = country ? `${city},${country}` : city
      const response = await fetch(`https://wttr.in/${encodeURIComponent(location)}?format=3`)
      
      if (!response.ok) {
        return {
          isError: true,
          content: [{ type: 'text', text: `Weather API error: ${response.status}` }]
        }
      }

      const text = await response.text()
      return {
        content: [{ type: 'text', text }]
      }
    } catch (err) {
      return {
        isError: true,
        content: [{ type: 'text', text: `Failed to fetch weather: ${(err as Error).message}` }]
      }
    }
  }
)

// ─── Resource ─────────────────────────────────────────────────────────────
server.resource(
  'company-docs',
  'docs://company/overview',
  { mimeType: 'text/markdown' },
  async (uri) => {
    const content = await readFile('./docs/company-overview.md', 'utf-8')
    return {
      contents: [{ uri: uri.href, mimeType: 'text/markdown', text: content }]
    }
  }
)

// ─── Prompt ───────────────────────────────────────────────────────────────
server.prompt(
  'summarize',
  'Generate a concise summary of provided content in a given style.',
  {
    content: z.string().describe('The content to summarize'),
    style:   z.enum(['bullet-points', 'paragraph', 'tldr']).describe('Summary style'),
  },
  ({ content, style }) => ({
    messages: [
      {
        role: 'user',
        content: {
          type: 'text',
          text: `Summarize the following content in ${style} format:\n\n${content}`
        }
      }
    ]
  })
)

// ─── Start ────────────────────────────────────────────────────────────────
const transport = new StdioServerTransport()
await server.connect(transport)
```

## Full Database Tool Server

```typescript
import { McpServer }          from '@modelcontextprotocol/sdk/server/mcp.js'
import { StdioServerTransport } from '@modelcontextprotocol/sdk/server/stdio.js'
import { z }                   from 'zod'
import { Pool }                from 'pg'

const db     = new Pool({ connectionString: process.env.DATABASE_URL })
const server = new McpServer({ name: 'database-tools', version: '1.0.0' })

// List tables (resource)
server.resource(
  'tables',
  'db://schema/tables',
  { mimeType: 'application/json' },
  async () => {
    const { rows } = await db.query(
      "SELECT table_name FROM information_schema.tables WHERE table_schema = 'public'"
    )
    return {
      contents: [{
        uri:      'db://schema/tables',
        mimeType: 'application/json',
        text:     JSON.stringify(rows.map(r => r.table_name))
      }]
    }
  }
)

// Execute read-only query (tool)
server.tool(
  'query_database',
  'Execute a read-only SELECT query against the database. Only SELECT statements are allowed.',
  {
    sql:    z.string().describe('A SELECT SQL query to execute'),
    params: z.array(z.string()).optional().describe('Query parameters for parameterized queries'),
  },
  async ({ sql, params = [] }) => {
    // Safety: only allow SELECT
    if (!sql.trim().toUpperCase().startsWith('SELECT')) {
      return {
        isError: true,
        content: [{ type: 'text', text: 'Only SELECT queries are allowed.' }]
      }
    }

    try {
      const { rows } = await db.query(sql, params)
      return {
        content: [{
          type: 'text',
          text: JSON.stringify(rows, null, 2)
        }]
      }
    } catch (err) {
      return {
        isError: true,
        content: [{ type: 'text', text: `Query failed: ${(err as Error).message}` }]
      }
    }
  }
)

const transport = new StdioServerTransport()
await server.connect(transport)
```

## Package Configuration

```json
{
  "name": "my-mcp-server",
  "version": "1.0.0",
  "type": "module",
  "main": "dist/server.js",
  "scripts": {
    "build": "tsc",
    "start": "node dist/server.js",
    "dev":   "tsx watch src/server.ts"
  },
  "dependencies": {
    "@modelcontextprotocol/sdk": "^1.0.0",
    "zod": "^3.23.0"
  },
  "devDependencies": {
    "@types/node": "^22.0.0",
    "tsx":         "^4.0.0",
    "typescript":  "^5.5.0"
  }
}
```

```json
// tsconfig.json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "NodeNext",
    "moduleResolution": "NodeNext",
    "outDir": "./dist",
    "strict": true,
    "esModuleInterop": true
  },
  "include": ["src/**/*"]
}
```

## Claude Desktop Configuration

```json
// ~/Library/Application Support/Claude/claude_desktop_config.json  (macOS)
// %APPDATA%\Claude\claude_desktop_config.json  (Windows)
{
  "mcpServers": {
    "my-tools": {
      "command": "node",
      "args":    ["/absolute/path/to/dist/server.js"],
      "env": {
        "DATABASE_URL": "postgresql://localhost/mydb"
      }
    }
  }
}
```

## Testing

```bash
# Install MCP Inspector
npx @modelcontextprotocol/inspector node dist/server.js

# Opens http://localhost:5173 — interactive tool/resource tester
```

```typescript
// Unit test for a tool handler
import { describe, it, expect } from 'vitest'

describe('get_weather tool', () => {
  it('returns weather for a valid city', async () => {
    const result = await weatherHandler({ city: 'Tokyo', country: 'JP' })
    expect(result.isError).toBeFalsy()
    expect(result.content[0].text).toContain('Tokyo')
  })

  it('returns isError on bad city', async () => {
    const result = await weatherHandler({ city: '!!!invalid!!!', country: 'XX' })
    expect(result.isError).toBe(true)
  })
})
```

## Tool Design Checklist

- [ ] Tool name is a `snake_case` verb phrase: `get_X`, `create_X`, `list_X`, `search_X`
- [ ] Description is precise enough for the AI to know exactly when to use it
- [ ] All parameters have `z.describe()` annotations
- [ ] Tool validates inputs before any side effects
- [ ] Errors return `{ isError: true, content: [...] }` — never throw
- [ ] Read-only operations use Resources, not Tools
- [ ] Tool timeout handled (wrap slow calls with `Promise.race`)
