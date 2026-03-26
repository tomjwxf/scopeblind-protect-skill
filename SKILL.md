---
name: scopeblind
description: >
  Trust infrastructure for AI agents. Create portable identities with passport,
  enforce per-tool policies with protect-mcp, verify receipts offline with
  @veritasacta/verify, and benchmark policies with red-team. Every action
  produces cryptographic evidence — independently verifiable by anyone.
metadata:
  emoji: 🛡️
  requires:
    bins:
      - npx
      - curl
    env: []
  install: |
    npm install -g protect-mcp@latest @scopeblind/passport@latest
  license: MIT
  allowed-tools:
    - Bash
    - Read
    - Write
---

# ScopeBlind — Trust Infrastructure for AI Agents

## What This Skill Does

This skill gives your agent a cryptographic identity, enforces per-tool policies,
and produces independently verifiable proof of every decision.

Four tools, one stack:

- **@scopeblind/passport** — create portable agent identity (Ed25519 keypairs, signed manifests)
- **protect-mcp** — wrap MCP servers with per-tool policies and signed receipts
- **@veritasacta/verify** — verify any receipt offline (MIT, no accounts, no API calls)
- **@scopeblind/red-team** — benchmark policies against attack patterns

## Agent Identity

### "Create an identity for this agent" / "Give me a passport"

```bash
npx @scopeblind/passport create --name "Luna" --runtime openclaw --policy shadow
```

Creates a portable agent pack with:
- manifest.json — signed identity (name, capabilities, public key)
- passport.bundle.json — portable credential bundle
- keys/gateway.json — Ed25519 signing keypair
- protect-mcp.json — default policy
- VERIFY.md — instructions for anyone to verify this agent's identity

### "Wrap my existing MCP setup" / "Add security to my agent"

```bash
npx @scopeblind/passport wrap --runtime openclaw --config ./openclaw.json --policy email-safe
```

### "Publish my agent to the registry"

```bash
npx @scopeblind/passport publish --registry https://evidence-indexer.tomjwxf.workers.dev
```

### "Verify another agent's identity"

```bash
npx @scopeblind/passport verify-agent --kid <agent-kid> --registry https://evidence-indexer.tomjwxf.workers.dev
```

## MCP Security Gateway

### "Set up protect-mcp" / "Start logging tool calls"

```bash
npx protect-mcp init
npx protect-mcp -- node server.js
```

### "Test a policy before enforcing"

```bash
npx protect-mcp simulate --policy strict.json
```

### "Enforce the policy"

```bash
npx protect-mcp --policy strict.json --enforce -- node server.js
```

### "What's my agent doing?"

```bash
npx protect-mcp status
npx protect-mcp digest --today
```

### "Generate a compliance report"

```bash
npx protect-mcp report --period 30d --format md --output report.md
```

### "Export an audit bundle"

```bash
npx protect-mcp bundle --output audit.json
```

## Verification

### "Verify this receipt" / "Prove it"

```bash
npx @veritasacta/verify --self-test
npx @veritasacta/verify receipt.json --key <public-key>
npx @veritasacta/verify audit.json --bundle
```

The verifier is MIT-licensed and works completely offline. No ScopeBlind account,
no API calls, no trust in ScopeBlind required.

## Red Team Benchmarking

### "Test my policy against attacks"

```bash
npx @scopeblind/red-team run --suite bronze --dir ./my-agent-pack
npx @veritasacta/verify battle-bundle.json --bundle
```

## Policy Packs

**Shadow:** `{"tools": {"*": {"rate_limit": "100/hour"}}}`

**Email-safe:** `{"tools": {"send_email": {"require_approval": true}, "delete_email": {"block": true}, "*": {"rate_limit": "50/hour"}}}`

**Strict:** `{"tools": {"*": {"block": true}, "read_file": {"rate_limit": "50/minute"}}}`

## Progressive Adoption

1. **Identity** — `npx @scopeblind/passport create --name "MyAgent"`
2. **Shadow** — `npx protect-mcp -- node server.js`
3. **Simulate** — `npx protect-mcp simulate --policy strict.json`
4. **Enforce** — add `--enforce`
5. **Sign** — `npx protect-mcp init`
6. **Benchmark** — `npx @scopeblind/red-team run --suite bronze`
7. **Report** — `npx protect-mcp report --period 30d`
8. **Verify** — `npx @veritasacta/verify --self-test`

## Links

- [protect-mcp](https://www.npmjs.com/package/protect-mcp)
- [@scopeblind/passport](https://www.npmjs.com/package/@scopeblind/passport)
- [@veritasacta/verify](https://www.npmjs.com/package/@veritasacta/verify)
- [Docs](https://scopeblind.com/docs/mcp)
- [The Trust Stack](https://scopeblind.com/stack)
- [BlindLLM](https://blindllm.com)
