# scopeblind-protect

Claude Code skill for [protect-mcp](https://www.npmjs.com/package/protect-mcp) — MCP server security gateway.

## Install

```bash
claude plugin marketplace add tomjwxf/scopeblind-protect-skill
claude plugin install scopeblind-protect
```

Or manually copy `SKILL.md` into your project directory.

## What it does

Teaches Claude Code to use protect-mcp commands:

- **Shadow mode** — wrap any MCP server and see every tool call
- **Simulate** — dry-run a policy against recorded calls
- **Enforce** — apply per-tool policies (block, rate-limit, require approval)
- **Sign** — generate Ed25519 keys for signed receipts
- **Report** — generate compliance reports from receipts
- **Verify** — `npx @veritasacta/verify --self-test` (MIT, offline)

## Progressive adoption

```bash
npx protect-mcp -- node server.js          # 1. See what's happening
npx protect-mcp simulate --policy strict.json  # 2. Test before enforcing
npx protect-mcp --policy strict.json --enforce -- node server.js  # 3. Enforce
npx protect-mcp init                        # 4. Generate signing keys
npx protect-mcp report --period 30d         # 5. Prove compliance
```

## Links

- [npm: protect-mcp](https://www.npmjs.com/package/protect-mcp)
- [Docs](https://scopeblind.com/docs/mcp)
- [The Trust Stack](https://scopeblind.com/stack)
- [Verify receipts](https://scopeblind.com/verify)
- [GitHub](https://github.com/tomjwxf/scopeblind-gateway)

## License

MIT
