---
name: find-similar-talent
description: Find people similar to a top performer, strong candidate, or benchmark profile for a Workerbee role. Use when a customer says "find more like [name]", "who else looks like our best PM?", "similar to this candidate", "more people like our top performer", or "show me lookalikes". Do NOT use for "who could back-fill/succeed/cover [name]'s role?" about an existing employee — that is a talent-intelligence question answered from the workforce graph (use the talent-intelligence skill; it needs no resume). Anchors the role's Success Profile on the benchmark person's strengths and surfaces the closest matches via the live Workerbee MCP server.
---

# Find Similar Talent

This skill's playbook lives on the Workerbee MCP server and is resolved to your current access scope. Always load it fresh — never follow a cached copy.

1. Call the **`get_skill_instructions`** tool with `skillKey: "find-similar-talent"`.
2. Follow the returned instructions exactly — they are the authoritative, up-to-date playbook for this skill.
3. If it returns upgrade guidance instead of a playbook, relay that guidance to the user and do not run the workflow.
4. If any tool call returns a `FEATURE_NOT_IN_PLAN` or `DEMO_EXPIRED` error, relay its `message` to the user verbatim and stop — do not improvise or fabricate results.
