---
name: log-decision
description: Surface the decision and audit record for a Workerbee role — the reviewable trail behind how candidates were ranked and decisions were produced. Use when a customer says "log the decision", "show the decision audit", "what's on record for this role?", "give me the audit trail", "what can I show compliance?", or "why did the system decide this way?". Retrieves the auditable decision record via get_decision_audit on the live Workerbee MCP server.
---

# Log Decision

This skill's playbook lives on the Workerbee MCP server and is resolved to your current access scope. Always load it fresh — never follow a cached copy.

1. Call the **`get_skill_instructions`** tool with `skillKey: "log-decision"`.
2. Follow the returned instructions exactly — they are the authoritative, up-to-date playbook for this skill.
3. If it returns upgrade guidance instead of a playbook, relay that guidance to the user and do not run the workflow.
4. If any tool call returns a `FEATURE_NOT_IN_PLAN` or `DEMO_EXPIRED` error, relay its `message` to the user verbatim and stop — do not improvise or fabricate results.
