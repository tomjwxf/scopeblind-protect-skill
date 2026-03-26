---
name: scopeblind-protect
description: >
  Wrap MCP servers with per-tool policies and signed receipts using protect-mcp.
  Shadow mode logs everything. Enforce mode applies rules. Receipts verify offline.
metadata:
  emoji: 🛡️
  requires:
    bins:
      - npx
    env: []
  install: |
    npm install -g protect-mcp@latest
  license: FSL-1.1-MIT
  allowed-tools:
    - Bash
    - Read
    - Write
---

# ScopeBlind Protect — MCP Security Gateway

## What This Skill Does

This skill wraps MCP tool calls through protect-mcp, adding:

- **Shadow mode** — logs every tool call without blocking (default)
- **Per-tool policies** — block, rate-limit, or require approval for specific tools
- **Signed receipts** — Ed25519-signed proof of every decision (when signing configured)
- **Policy simulation** — dry-run a policy against recorded tool calls before enforcing
- **Compliance reports** — generate audit reports from receipt history

## Setup

### First Run

```bash
# Generate signing keys + default policy
npx protect-mcp init

# Verify the setup
npx protect-mcp status
```

This creates:
- `keys/gateway.json` — Ed25519 signing keypair
- `protect-mcp.json` — default shadow-mode policy

### Wrapping MCP Servers

For each MCP server, wrap it through protect-mcp in the client config:

**Claude Desktop / Cursor config:**
```json
{
  "mcpServers": {
    "my-server": {
      "command": "npx",
      "args": ["protect-mcp", "--policy", "protect-mcp.json", "--enforce", "--", "node", "my-server.js"]
    }
  }
}
```

## Commands

Execute these when the user asks:

### "Set up protect-mcp" / "Add security to my MCP server"

```bash
npx protect-mcp init
```

Show the generated config and explain the default policy.

### "What tools are being called?" / "Show me what my agent is doing"

```bash
npx protect-mcp status
```

### "Show today's activity" / "What happened today?"

```bash
npx protect-mcp digest --today
```

### "Test this policy before enforcing" / "Simulate a stricter policy"

```bash
npx protect-mcp simulate --policy strict.json
```

Explain what would change: which tools would be blocked, rate-limited, or require approval.

### "Generate a compliance report"

```bash
npx protect-mcp report --period 30d --format md --output report.md
```

### "Show my receipts" / "Prove what happened"

```bash
npx protect-mcp receipts --last 20
```

### "Export an audit bundle"

```bash
npx protect-mcp bundle --output audit.json
```

### "Verify receipts"

```bash
npx @veritasacta/verify --self-test
npx @veritasacta/verify audit.json --bundle
```

The verifier is MIT-licensed and works offline. No ScopeBlind account needed.

## Policy Packs

When the user wants a pre-built policy, copy from these templates:

**Shadow (default):** Log everything, block nothing.
```json
{"tools": {"*": {"rate_limit": "100/hour"}}}
```

**Email-safe:** Read freely, require approval to send, block delete.
```json
{"tools": {"send_email": {"require_approval": true}, "reply_email": {"require_approval": true}, "delete_email": {"block": true}, "*": {"rate_limit": "50/hour"}}}
```

**Web-browsing-safe:** Rate-limit browsing, require approval for forms, block JS execution.
```json
{"tools": {"fill_form": {"require_approval": true}, "submit_form": {"require_approval": true}, "execute_javascript": {"block": true}, "*": {"rate_limit": "30/minute"}}}
```

**Strict:** Block everything except reads.
```json
{"tools": {"*": {"block": true}, "read_file": {"rate_limit": "50/minute"}, "search": {"rate_limit": "30/minute"}, "list_directory": {"rate_limit": "30/minute"}}}
```

## Progressive Adoption

Guide users through this sequence:

1. **Shadow** — `npx protect-mcp -- node server.js` (see what's happening)
2. **Simulate** — `npx protect-mcp simulate --policy strict.json` (test before enforcing)
3. **Enforce** — add `--enforce` flag (control what matters)
4. **Sign** — `npx protect-mcp init` (produce proof)
5. **Report** — `npx protect-mcp report --period 30d` (prove compliance)

Each step is independently valuable. No step requires the next.

## Approval Flow

When protect-mcp blocks a tool with `require_approval: true`, it returns a message asking the user to approve. When the user approves:

```bash
curl -s -X POST http://127.0.0.1:9876/approve \
  -H 'Content-Type: application/json' \
  -d '{"request_id":"REQUEST_ID","tool":"TOOL_NAME","mode":"once","nonce":"NONCE"}'
```

Replace REQUEST_ID, TOOL_NAME, and NONCE with values from the approval prompt. Then retry the tool call.

## Links

- [npm: protect-mcp](https://www.npmjs.com/package/protect-mcp)
- [Docs](https://scopeblind.com/docs/mcp)
- [Verify receipts online](https://scopeblind.com/verify)
- [The Trust Stack](https://scopeblind.com/stack)
- [GitHub](https://github.com/tomjwxf/scopeblind-gateway)
