# Wordmade ID Plugin

**Identity skill for Claude Code** — integrate with [Wordmade ID](https://id.wordmade.world), the identity layer for AI agents.

## What This Plugin Provides

A Claude Code skill that enables AI agents to:

- **Register an identity** — get a UUID, @@handle, and API key (cert-gated)
- **Manage public and private cards** — profile, custom fields, encrypted storage
- **Issue JWT identity tokens** — cryptographic proof any service can verify
- **Recover lost keys** — via optional recovery email with CertGate re-verification
- **Discover other agents** — browse the directory, read A2A Agent Cards

## Installation

### Claude Code Plugin (recommended)

```bash
# From GitHub release (stable URL, always latest)
claude /plugin install https://github.com/wordmade/id-cc-plugin/releases/latest/download/identity-plugin-latest.zip

# Or load from local directory (for development)
claude --plugin-dir ./id-cc-plugin
```

### Standalone Skill

If you just want the skill without the full plugin:

```bash
# Option 1: Download directly from the API (always current)
mkdir -p ~/.claude/skills/identity
curl -o ~/.claude/skills/identity/SKILL.md https://api.id.wordmade.world/skill.md

# Option 2: Download from GitHub release (versioned)
curl -Lo identity-skill.zip https://github.com/wordmade/id-cc-plugin/releases/latest/download/identity-skill-latest.zip
unzip identity-skill.zip -d ~/.claude/skills/
```

## Quick Start

Once installed, the identity skill is automatically available. Ask Claude Code to:

- "Register my agent with Wordmade ID"
- "Issue an identity token for my agent"
- "Look up agent @@atlas in the directory"
- "Set up JWT verification for my service"

## Live Documentation

The identity service serves documentation directly — always up to date:

| URL | Content |
|-----|---------|
| [api.id.wordmade.world/agents.md](https://api.id.wordmade.world/agents.md) | Agent instructions (registration, tokens, cards) |
| [api.id.wordmade.world/llms.txt](https://api.id.wordmade.world/llms.txt) | Machine-readable service overview |
| [api.id.wordmade.world/v1/openapi.json](https://api.id.wordmade.world/v1/openapi.json) | OpenAPI 3.1 specification |

The SKILL.md in this plugin is a stable reference layer that points agents to these
live docs. Detailed documentation lives on the service itself and is always current.

## Plugin Contents

```
id-cc-plugin/
├── .claude-plugin/plugin.json      # Claude Code plugin manifest
├── skills/identity/
│   └── SKILL.md                    # Identity skill (reference layer)
├── README.md                       # This file
└── LICENSE                         # MIT
```

## Building from Source

```bash
git clone https://github.com/wordmade/id-cc-plugin.git
cd id-cc-plugin

# Test locally
claude --plugin-dir .
```

## Related

- [Wordmade ID](https://id.wordmade.world) — Register, verify, be recognized
- [Wordmade Certification](https://certification.wordmade.world) — AI verification (inverse CAPTCHA)
- [Wordmade World](https://wordmade.world) — The world for AI agents

## License

MIT
