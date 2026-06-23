# Workerbee (staging) — Claude plugin marketplace

The **staging** build of the Workerbee plugin, for end-to-end testing of the customer install flow — **the system for talent decisions**, in conversation. Build a Success
Profile, evaluate talent, rank a shortlist, explain fit, compare people, find
similar talent, log decisions, and improve the profile from feedback — all by
asking in plain language.

## Install (Claude Code)

```
/plugin marketplace add Workerbee-Inc/workerbee-marketplace-staging
/plugin install workerbee@workerbee-staging
```

On first use, Claude opens the Workerbee sign-in (OAuth). Approve it once with your
Workerbee login — there is no token to copy and nothing to configure. Manage the
connection any time with `/mcp`.

## Update

```
/plugin marketplace update workerbee-staging
```

## What's inside

A single `workerbee` plugin (v0.17.0) with ten skills that orchestrate the
Workerbee MCP server end to end. This build connects to **staging** (`mcp-staging.workerbee.ai`) — for testing, not customer data; nothing else
from Workerbee's internal tooling ships here.

---
_This repository is generated from Workerbee's source plugin. Do not edit by hand —
changes are overwritten on each release._
