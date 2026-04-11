# Privacy Policy

**Effective Date:** April 11, 2026

## Overview

Mindex ("we", "our", "us") provides an AI-powered knowledge base service accessible via web dashboard and MCP (Model Context Protocol) server. This privacy policy explains how we handle your data.

## What We Collect

### Account Data
- Email address and name (for authentication)
- Organization name and settings
- OAuth profile data (if you sign in with Google or GitHub)

### Documents
- Text documents you upload to your namespaces
- Metadata you attach to documents (titles, tags, custom fields)
- AI-generated data: embeddings, tags, knowledge graph connections

### Usage Data
- API request counts and timestamps
- Storage usage per organization
- AI credit consumption

### Technical Data
- IP addresses (for rate limiting, not stored long-term)
- API key identifiers (hashed, never stored in plaintext)

## How We Use Your Data

- **Document processing:** We chunk, embed, and index your documents to enable semantic search and GraphRAG retrieval
- **AI enrichment:** We use LLMs to auto-tag and connect your documents (content is sent to the configured LLM provider)
- **Service operation:** Usage tracking, rate limiting, billing
- **Security:** Authentication, abuse prevention

## What We Do NOT Do

- We do **not** sell your data
- We do **not** use your documents to train AI models
- We do **not** share your documents with other users or organizations
- We do **not** access your documents unless required for debugging with your explicit consent

## Data Storage

- Documents are stored in encrypted object storage (S3-compatible)
- Embeddings are stored in a vector database (ChromaDB)
- Knowledge graph data is stored in Neo4j
- All infrastructure runs in Oracle Cloud (OCI)
- Data is stored in the US region

## Data Isolation

Each organization's data is fully isolated:
- Separate vector database collections
- Separate knowledge graph namespaces
- API keys are scoped to a single organization
- No cross-organization data access is possible

## MCP Server Data Flow

When you use Mindex as an MCP server:

1. Your AI tool (Claude, Cursor, etc.) sends requests to `api.usemindex.dev/mcp`
2. We authenticate using your API key
3. We process the request (search, context retrieval, or upload)
4. We return results directly to your AI tool
5. We do **not** log the content of search queries or results beyond standard request logging

## Data Retention

- **Active accounts:** Data is retained as long as your account is active
- **Deleted documents:** Removed from all storage backends (S3, vector DB, knowledge graph) within 24 hours
- **Deleted accounts:** All organization data is purged within 30 days
- **Logs:** Request logs are retained for 90 days

## Your Rights

You can at any time:
- **Export** your documents via the API or dashboard
- **Delete** individual documents, namespaces, or your entire organization
- **Revoke** API keys to immediately cut off MCP access
- **Request** a full data export by contacting us

## Third-Party Services

- **Stripe:** Payment processing (we do not store credit card numbers)
- **LLM providers:** Document enrichment (configurable — content is sent for AI processing)
- **Google/GitHub:** OAuth authentication (only profile data is shared)

## Security

- All traffic is encrypted via TLS (HTTPS)
- API keys are stored as SHA-256 hashes
- JWT tokens expire after 15 minutes
- Infrastructure is hardened (SSH key-only, firewall, security headers)

## Changes

We may update this policy. Significant changes will be communicated via email to account holders.

## Contact

For privacy-related questions: **privacy@usemindex.dev**
