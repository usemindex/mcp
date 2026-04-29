# Mindex MCP Server

[Mindex](https://usemindex.dev) is an AI-powered knowledge base for developers. Store your documents, and let AI organize, connect, and retrieve them using semantic search and knowledge graphs (GraphRAG).

## Install (Recommended: Mindex CLI)

```bash
# Install the Mindex CLI
curl -fsSL https://raw.githubusercontent.com/usemindex/cli/main/install.sh | sh

# Authenticate
mindex auth

# Connect to your AI tool — one command
mindex mcp install claude-code
mindex mcp install cursor
mindex mcp install windsurf
mindex mcp install claude-desktop
```

> Get your API key at [usemindex.dev](https://usemindex.dev) — Settings > API Keys

## Alternative: Manual Setup

### Option 1: Claude CLI

```bash
claude mcp add --transport http mindex https://mcp.usemindex.dev \
  --header "Authorization: Bearer sk-your-api-key-here"
```

### Option 2: JSON config file

Add to `~/.claude.json` (Claude Code), `.cursor/mcp.json` (Cursor), or `~/.codeium/windsurf/mcp_config.json` (Windsurf):

```json
{
  "mcpServers": {
    "mindex": {
      "type": "http",
      "url": "https://mcp.usemindex.dev",
      "headers": {
        "Authorization": "Bearer ${MINDEX_API_KEY}"
      }
    }
  }
}
```

Set your key as an environment variable:

```bash
export MINDEX_API_KEY="sk-your-api-key-here"
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

### Claude Code

```bash
# Project-scoped (recommended)
claude mcp add --transport http mindex https://mcp.usemindex.dev \
  --header "Authorization: Bearer sk-your-api-key-here"

# Or globally (all projects)
claude mcp add --transport http --scope user mindex https://mcp.usemindex.dev \
  --header "Authorization: Bearer sk-your-api-key-here"
```

Or add to `.mcp.json` at your project root:

```json
{
  "mcpServers": {
    "mindex": {
      "type": "http",
      "url": "https://mcp.usemindex.dev",
      "headers": {
        "Authorization": "Bearer ${MINDEX_API_KEY}"
      }
    }
  }
}
```

### Claude Desktop

Go to **Settings > Integrations > MCP Servers** and add:

- **Name:** Mindex
- **URL:** `https://mcp.usemindex.dev`
- **Transport:** HTTP
- **Headers:** `Authorization: Bearer sk-your-api-key-here`

### Cursor

```bash
claude mcp add --transport http mindex https://mcp.usemindex.dev \
  --header "Authorization: Bearer sk-your-api-key-here"
```

Or add to `.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "mindex": {
      "type": "http",
      "url": "https://mcp.usemindex.dev",
      "headers": {
        "Authorization": "Bearer ${MINDEX_API_KEY}"
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
      "type": "http",
      "url": "https://mcp.usemindex.dev",
      "headers": {
        "Authorization": "Bearer ${MINDEX_API_KEY}"
      }
    }
  }
}
```

### Windsurf

Add to `~/.codeium/windsurf/mcp_config.json`:

```json
{
  "mcpServers": {
    "mindex": {
      "type": "http",
      "url": "https://mcp.usemindex.dev",
      "headers": {
        "Authorization": "Bearer ${MINDEX_API_KEY}"
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

### General API limits (all endpoints)

| Plan | Requests/min | Storage |
|------|-------------|---------|
| Free | 30 | 10 MB |
| Personal | 60 | 25 GB |
| Team | 120 | 500 GB |
| Enterprise | 300 | Custom |

### GraphRAG-specific limits

GraphRAG endpoints (`mindex_context`, `mindex_search`) carry an additional
rolling-window throttle on the Free plan to keep the service sustainable
for paid tiers. Paid plans are unlimited.

| Plan | GraphRAG calls per 5 hours |
|------|----------------------------|
| Free | 300 |
| Personal | Unlimited |
| Team | Unlimited |
| Enterprise | Unlimited |

Hitting the GraphRAG limit returns HTTP `429` with an error message
indicating the window and retry-after time. For sustained usage of
GraphRAG queries, Personal plan or higher is recommended.

## Protocol Details

- **Transport:** HTTP (Streamable HTTP, MCP spec 2025-03-26)
- **Protocol:** JSON-RPC 2.0
- **Endpoint:** `POST https://mcp.usemindex.dev`
- **Content-Type:** `application/json`

## Links

- **Website:** [usemindex.dev](https://usemindex.dev)
- **Dashboard:** [usemindex.dev](https://usemindex.dev)
## Support

- **Website:** [usemindex.dev](https://usemindex.dev)

## License

MIT
