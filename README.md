# scopeblind

Claude Code skill for the ScopeBlind trust stack — agent identity, MCP security, signed receipts, and offline verification.

## Install

```bash
claude plugin marketplace add tomjwxf/scopeblind-protect-skill
claude plugin install scopeblind
```

Or manually copy `SKILL.md` into your project directory.

## What it does

Teaches Claude Code to use the full ScopeBlind stack:

- **@scopeblind/passport** — create portable agent identity (Ed25519 keypairs, signed manifests, portable bundles)
- **protect-mcp** — wrap MCP servers with per-tool policies and signed receipts
- **@veritasacta/verify** — verify any receipt offline (MIT, no accounts)
- **@scopeblind/red-team** — benchmark policies against attack patterns

## Quick start

```bash
# Create an agent identity
npx @scopeblind/passport create --name "Luna" --runtime openclaw --policy shadow

# Wrap an MCP server (shadow mode — logs everything, blocks nothing)
npx protect-mcp -- node server.js

# Test a stricter policy
npx protect-mcp simulate --policy strict.json

# Verify receipts offline
npx @veritasacta/verify --self-test
```

## Links

- [npm: protect-mcp](https://www.npmjs.com/package/protect-mcp)
- [npm: @scopeblind/passport](https://www.npmjs.com/package/@scopeblind/passport)
- [MCP Docs](https://scopeblind.com/docs/mcp)
- [The Trust Stack](https://scopeblind.com/stack)
- [Verify receipts](https://scopeblind.com/verify)
- [BlindLLM](https://blindllm.com)
- [GitHub](https://github.com/tomjwxf/scopeblind-gateway)

## License

MIT
