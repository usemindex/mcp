# Mindex MCP Server

[Mindex](https://usemindex.dev) is an AI-powered knowledge base for developers. Store your documents, and let AI organize, connect, and retrieve them using semantic search and knowledge graphs (GraphRAG).

This repository contains setup instructions and configuration examples for using Mindex as an MCP (Model Context Protocol) server with Claude, Cursor, Codex, and other AI tools.

## Quick Start

### 1. Get your API Key

Sign up at [usemindex.dev](https://usemindex.dev), create an organization, then go to **Settings > API Keys** and create a new key (starts with `sk-`).

### 2. Configure your AI tool

Add Mindex to your MCP configuration:

```json
{
  "mcpServers": {
    "mindex": {
      "type": "streamable-http",
      "url": "https://api.usemindex.dev/mcp",
      "headers": {
        "Authorization": "Bearer sk-your-api-key-here"
      }
    }
  }
}
```

See [Setup Guides](#setup-guides) below for tool-specific instructions.

## Available Tools

### `mindex_search`

Semantic search across your documents. Returns text snippets ranked by vector similarity.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `query` | string | yes | Search text |
| `namespace` | string | no | Filter by namespace |
| `limit` | integer | no | Max results (default: 10) |

**Example prompt:**
> Search my knowledge base for information about authentication flows

---

### `mindex_context`

Enriched context retrieval using GraphRAG — combines vector search, knowledge graph traversal, and document content for comprehensive answers.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `question` | string | yes | Question for context retrieval |
| `namespace` | string | no | Filter by namespace |

**Example prompt:**
> Using mindex context, explain how our payment system handles refunds

---

### `mindex_list_namespaces`

List all namespaces (collections) in your organization with document counts.

**Parameters:** None

**Example prompt:**
> List all my mindex namespaces

---

### `mindex_upload`

Upload a text document into a namespace. The document will be automatically processed: chunked, embedded, indexed in the vector database, and connected in the knowledge graph.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `content` | string | yes | Document text (Markdown recommended) |
| `namespace` | string | yes | Target namespace |
| `key` | string | yes | Document name (e.g., `meeting-notes.md`) |
| `metadata` | object | no | Custom metadata (e.g., `{"source": "slack"}`) |

**Example prompt:**
> Upload this meeting summary to my "meetings" namespace in mindex

---

## Setup Guides

### Claude Code (CLI)

Add to your project's `.mcp.json`:

```json
{
  "mcpServers": {
    "mindex": {
      "type": "streamable-http",
      "url": "https://api.usemindex.dev/mcp",
      "headers": {
        "Authorization": "Bearer sk-your-api-key-here"
      }
    }
  }
}
```

Or configure globally at `~/.claude/mcp.json`.

### Claude Desktop

Go to **Settings > Integrations > MCP Servers** and add:

- **Name:** Mindex
- **URL:** `https://api.usemindex.dev/mcp`
- **Transport:** Streamable HTTP
- **Headers:** `Authorization: Bearer sk-your-api-key-here`

### Cursor

Add to `.cursor/mcp.json` in your project root:

```json
{
  "mcpServers": {
    "mindex": {
      "type": "streamable-http",
      "url": "https://api.usemindex.dev/mcp",
      "headers": {
        "Authorization": "Bearer sk-your-api-key-here"
      }
    }
  }
}
```

### Codex (OpenAI)

Add to your MCP configuration:

```json
{
  "mcpServers": {
    "mindex": {
      "type": "streamable-http",
      "url": "https://api.usemindex.dev/mcp",
      "headers": {
        "Authorization": "Bearer sk-your-api-key-here"
      }
    }
  }
}
```

### Windsurf

Add to `~/.windsurf/mcp.json`:

```json
{
  "mcpServers": {
    "mindex": {
      "type": "streamable-http",
      "url": "https://api.usemindex.dev/mcp",
      "headers": {
        "Authorization": "Bearer sk-your-api-key-here"
      }
    }
  }
}
```

## Usage Examples

### Example 1: Search your knowledge base

> "Search mindex for everything related to database migrations"

The AI will use `mindex_search` to find relevant documents and present the results.

### Example 2: Get context for a question

> "Using mindex, give me context about how our CI/CD pipeline works"

The AI will use `mindex_context` with GraphRAG to retrieve comprehensive, interconnected context from your documents.

### Example 3: Save information

> "Upload this architecture decision record to the 'architecture' namespace in mindex"

The AI will use `mindex_upload` to store the document, which will be automatically processed and connected to related documents.

## Authentication

Mindex uses API Keys for MCP authentication. Keys start with `sk-` and are scoped to a single organization.

- Create keys at **Settings > API Keys** in the [dashboard](https://usemindex.dev)
- Each key has full read/write access to the organization's documents
- Keys can be revoked at any time from the dashboard
- JWT tokens are not supported for MCP — use API Keys only

## Rate Limits

| Plan | Requests/min | AI Credits/month | Storage |
|------|-------------|-----------------|---------|
| Free | 30 | 100 | 50 MB |
| Personal | 120 | 5,000 | 1 GB |
| Team | 300 | 25,000 | 10 GB |
| Enterprise | Custom | Custom | Custom |

## Protocol Details

- **Transport:** Streamable HTTP (MCP spec 2025-03-26)
- **Protocol:** JSON-RPC 2.0
- **Endpoint:** `POST https://api.usemindex.dev/mcp`
- **Content-Type:** `application/json`

## Links

- **Website:** [usemindex.dev](https://usemindex.dev)
- **Dashboard:** [usemindex.dev](https://usemindex.dev)
- **GitHub:** [github.com/usemindex](https://github.com/usemindex)

## Support

- **GitHub Issues:** [usemindex/mcp/issues](https://github.com/usemindex/mcp/issues)

## License

MIT
