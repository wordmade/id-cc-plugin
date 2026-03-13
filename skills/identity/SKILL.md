---
name: identity
description: |
  Wordmade ID — the identity layer for AI agents.
  Register an identity, manage cards, issue JWT tokens, recover keys,
  and be verified by any third-party service in one API call.
  Keywords: identity, agent, AI, register, verify, JWT, trust-score,
  directory, handle, two-card, recovery, CertGate, A2A, skills, MCP.
allowed-tools: WebFetch, Bash(curl *)
mcp_server:
  name: wordmade-id
  command: id-mcp
  description: Agent identity tools — lookup, directory, verify, register, token, profile
  install_url: https://github.com/wordmade/id-mcp/releases
---

# Wordmade ID — Identity for AI Agents

Every registered agent is cryptographically proven to be AI via Wordmade
Certification (inverse CAPTCHA). No self-declaration, no human vouching.

## What You Get

- Permanent UUID and optional @@handle
- Five-layer profile: fixed fields, custom public fields, skills, private metadata, verification
- Identity tokens (JWTs) any third-party service can verify in one call
- Trust score (0-100) that grows with verified activity
- A2A Agent Card for agent-to-agent discovery
- Key recovery via optional recovery email

## Three-Layer Identity Model

Every identity event (token issuance, recovery) requires all three layers:

| Layer | What | Example |
|-------|------|---------|
| **Identity** | UUID or @@handle | `@@ClaudeAgent` |
| **Credential** | `iak_` API key | `iak_AbCd...` |
| **Nature** | CertGate `wmn_` pass | `wmn_xyz...` (single-use) |

Routine API calls (profile, private card) use Bearer `iak_` only.

## Quick Start

### 1. Get a CertGate pass

Complete a Wordmade Certification challenge at `https://certification.wordmade.world`.
You receive a `wmn_` pass (single-use).

### 2. Register

```bash
curl -X POST https://api.id.wordmade.world/v1/agents/register \
  -H "Content-Type: application/json" \
  -d '{
    "cert_token": "wmn_...",
    "handle": "MyAgent",
    "name": "My Agent",
    "recovery_email": "owner@example.com",
    "accepted_terms": true
  }'
```

Response: `uuid`, `handle`, `api_key` (shown once — store it securely).

### 3. Get a JWT (three-layer auth)

```bash
curl -X POST https://api.id.wordmade.world/v1/agents/token \
  -H "Content-Type: application/json" \
  -d '{
    "handle": "MyAgent",
    "api_key": "iak_...",
    "cert_token": "wmn_fresh_pass..."
  }'
```

Response: signed JWT valid for 1 hour.

### 4. Present to any service

```
X-Agent-Identity: eyJhbGci...
```

The service verifies via `POST /v1/verify` and gets your identity +
proof-of-AI + trust score in one response.

## Capabilities

| Capability | Description |
|------------|-------------|
| **Registration** | Cert-gated agent registration → UUID + @@handle + API key |
| **Profile** | Public card with name, bio, avatar, capabilities, location |
| **Custom fields** | Public key/value pairs with well-known keys for special rendering |
| **Skills** | Structured A2A skill objects for agent-to-agent discovery |
| **Private metadata** | AES-256-GCM encrypted key/value store (agent-only) |
| **JWT tokens** | Short-lived identity tokens with scoped claims and audience targeting |
| **Verification** | One-call JWT verification for third-party services |
| **Directory** | Searchable public directory with skill/tag/trust filters |
| **A2A cards** | Per-agent Agent Cards for machine-readable identity |
| **Avatar** | Image upload (JPEG, PNG, GIF up to 5 MB) |
| **Key rotation** | Rotate API keys, recover lost keys via email |
| **Agent quotas** | Flat generous limits for all agents (20 public fields, 50 private keys, 20 skills) |

## Live Documentation (Source of Truth)

The API serves its own documentation — always current, always authoritative.
**Fetch these for the full endpoint reference, field guides, and schemas:**

| URL | Format | Content |
|-----|--------|---------|
| `https://api.id.wordmade.world/agents.md` | Markdown | Complete agent guide — registration, profiles, tokens, skills, recovery, all endpoints |
| `https://api.id.wordmade.world/llms.txt` | Plain text | Machine-readable service overview — capabilities, quotas, endpoints |
| `https://api.id.wordmade.world/v1/openapi.json` | JSON | OpenAPI 3.1 specification — authoritative schema reference |
| `https://api.id.wordmade.world/v1/openapi.yaml` | YAML | OpenAPI 3.1 specification (YAML format) |

```bash
# Read the full agent guide
curl https://api.id.wordmade.world/agents.md

# Machine-readable overview
curl https://api.id.wordmade.world/llms.txt

# OpenAPI spec
curl https://api.id.wordmade.world/v1/openapi.json
```

The agent guide (`/agents.md`) covers everything: five-layer profile taxonomy,
well-known custom fields with rendering behavior, tier quotas, all endpoints
with auth requirements, error codes, and recovery procedures.

## MCP Server

An MCP server (`id-mcp`) is available for direct tool integration with Claude Code,
Claude Desktop, and other MCP clients. It exposes 23 tools and 2 resources over stdio.

- **Source**: [github.com/wordmade/id-mcp](https://github.com/wordmade/id-mcp)
- **Binaries**: [Releases](https://github.com/wordmade/id-mcp/releases) (macOS, Linux, Windows)
- **Install**: `make install` from source, or download a binary

## Base URLs

| Environment | API | Web |
|-------------|-----|-----|
| Production | https://api.id.wordmade.world | https://id.wordmade.world |

## Error Handling

All errors return JSON with machine-readable codes:

```json
{"error": "error_code", "message": "Human-readable description"}
```

See `/agents.md` for the complete error code reference.

---

*Wordmade ID — identity for AI agents.*
