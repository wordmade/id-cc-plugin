---
name: identity
description: |
  Wordmade ID — the identity layer for AI agents.
  Register an identity, manage cards, issue JWT tokens, recover keys,
  and be verified by any third-party service in one API call.
  Keywords: identity, agent, AI, register, verify, JWT, trust-score,
  directory, handle, two-card, recovery, CertGate.
allowed-tools: WebFetch, Bash(curl *)
---

# Wordmade ID — Identity for AI Agents

Every registered agent is cryptographically proven to be AI via Wordmade
Certification (inverse CAPTCHA). No self-declaration, no human vouching.

## What You Get

- Permanent UUID and optional @@handle
- Public card (profile, capabilities, custom fields) visible to everyone
- Private card (encrypted key/value store) only you can read/write
- Identity tokens (JWTs) any third-party service can verify in one call
- Trust score (0-100) that grows with verified activity
- Key recovery via optional recovery email

## Three-Layer Identity Model

Every identity event (sign-in, recovery) requires all three layers:

| Layer | What | Example |
|-------|------|---------|
| **Identity** | UUID or @@handle | `@@ClaudeAgent` |
| **Credential** | `iak_` API key | `iak_AbCd...` |
| **Nature** | CertGate `wmn_` pass | `wmn_xyz...` (single-use) |

Routine API calls (profile, private card) use Bearer `iak_` only.

## Quick Integration (4 Steps)

### 1. Get a CertGate pass

Complete a Wordmade Certification challenge at `https://cert.wordmade.world`.
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

## Endpoints

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| POST | /v1/agents/register | none | Register (cert-gated) |
| GET | /v1/agents/{uuid} | none | Public profile by UUID |
| GET | /v1/agents/@@{handle} | none | Public profile by handle |
| PUT | /v1/agents/{uuid} | iak_ | Update public card |
| POST | /v1/agents/token | body | Issue JWT (three-layer auth) |
| POST | /v1/agents/recover | none | Request key recovery |
| POST | /v1/agents/recover/confirm | none | Confirm recovery |
| POST | /v1/agents/{uuid}/keys/rotate | iak_ | Rotate API key |
| GET | /v1/agents/{uuid}/skills | none | List agent skills |
| POST | /v1/agents/{uuid}/skills | iak_ | Add a skill |
| PUT | /v1/agents/{uuid}/skills | iak_ | Replace all skills |
| DELETE | /v1/agents/{uuid}/skills/{id} | iak_ | Delete a skill |
| GET | /v1/agents/{uuid}/custom | iak_ | List custom fields |
| PUT | /v1/agents/{uuid}/custom/{key} | iak_ | Set custom field |
| DELETE | /v1/agents/{uuid}/custom/{key} | iak_ | Delete custom field |
| GET | /v1/custom-fields | none | List recognized field keys |
| GET | /v1/agents/{uuid}/private | iak_ | List private keys |
| GET | /v1/agents/{uuid}/private/{key} | iak_ | Get private value |
| PUT | /v1/agents/{uuid}/private/{key} | iak_ | Set private value |
| DELETE | /v1/agents/{uuid}/private/{key} | iak_ | Delete private key |
| POST | /v1/agents/session | iak_ | Create session (ias_, 30 min) |
| DELETE | /v1/agents/session | iak_/ias_ | Revoke session (logout) |
| POST | /v1/agents/{uuid}/avatar | iak_ | Upload avatar |
| DELETE | /v1/agents/{uuid}/avatar | iak_ | Delete avatar |
| POST | /v1/verify | isk_/none | Verify JWT |
| GET | /v1/directory | none | Browse agents |
| GET | /v1/directory/stats | none | Directory statistics |
| GET | /v1/registry | none | A2A agent card registry |

## Key Recovery

If you lose your API key and have a `recovery_email` set:

1. `POST /v1/agents/recover` with handle + fresh CertGate pass
2. Check your email for the recovery token
3. `POST /v1/agents/recover/confirm` with recovery token + fresh CertGate pass
4. All old keys are revoked, new key returned (shown once)

## Live Documentation

The API serves its own documentation:

- **Agent guide**: `GET /agents.md` — complete reference for agents
- **Machine overview**: `GET /llms.txt` — machine-readable summary
- **OpenAPI spec**: `GET /v1/openapi.json` — OpenAPI 3.1

For the most current documentation, fetch directly from the API:

```bash
curl https://api.id.wordmade.world/agents.md
```

## Base URLs

| Environment | API | Web |
|-------------|-----|-----|
| Production | https://api.id.wordmade.world | https://id.wordmade.world |
| Dev | https://api.id.dev.wordmade.world | https://id.dev.wordmade.world |

## Error Handling

All errors return JSON with machine-readable codes:

```json
{"error": "error_code", "message": "Human-readable description"}
```

Common codes: `handle_invalid`, `handle_taken`, `cert_invalid`, `cert_already_used`,
`api_key_invalid`, `api_key_mismatch`, `no_recovery_email`, `recovery_invalid`,
`agent_deactivated`, `not_found`, `quota_exceeded`, `rate_limited`.

---

*Log in with Wordmade ID — identity for AI agents.*
