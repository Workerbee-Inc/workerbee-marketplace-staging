# Workerbee (staging) — Claude + ChatGPT plugin marketplace

The **staging** build of the Workerbee plugin, for end-to-end testing of the customer install flow — **the system for talent decisions**, in conversation. Build a Success
Profile, evaluate talent, rank a shortlist, invite the top picks, explain fit,
compare people, find similar talent, log decisions, and improve the profile from
feedback — plus catch up on the state of play, get help, and reach the Workerbee
team — all by asking in plain language.

## Install (Claude Code)

```
/plugin marketplace add Workerbee-Inc/workerbee-marketplace-staging
/plugin install workerbee@workerbee-staging
```

On first use, Claude opens the Workerbee sign-in (OAuth). Approve it once with your
Workerbee login — there is no token to copy and nothing to configure. Manage the
connection any time with `/mcp`.

Update with `/plugin marketplace update workerbee-staging`.

## Install (ChatGPT / Codex)

The same plugin. Add this repo as a marketplace, then install from it:

```
codex plugin marketplace add Workerbee-Inc/workerbee-marketplace-staging
```

The plugin then appears as a selectable source in the Plugins directory — in the
ChatGPT desktop app, restart it first, since marketplaces are read at startup.
Sign-in is the same OAuth flow, run at install time.

Update with `codex plugin marketplace upgrade workerbee-staging`.

**Surface limits (OpenAI's, not ours):** plugins work in ChatGPT Work on the web,
ChatGPT Work and Codex in the desktop app, and the Codex CLI. They are not
available in regular Chat, the IDE extension, or mobile — on those surfaces
connect the Workerbee MCP server on its own and the tools still work.

## What's inside

A single `workerbee` plugin (v0.23.0) with 15 skills that
orchestrate the Workerbee MCP server end to end. One bundle serves both
platforms: the skills and the MCP config are shared, and each platform reads
its own manifest (`.claude-plugin/plugin.json` for Claude,
`.codex-plugin/plugin.json` for Codex). This build connects to **staging** (`mcp-staging.workerbee.ai`) — for testing, not customer data;
nothing else from Workerbee's internal tooling ships here.

---
_This repository is generated from Workerbee's source plugin. Do not edit by hand —
changes are overwritten on each release._
