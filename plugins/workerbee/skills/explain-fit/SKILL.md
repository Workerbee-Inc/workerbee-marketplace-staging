---
name: explain-fit
description: Explain why a person ranks where they do for a Workerbee role — strengths, gaps, rationale, and the reviewable evidence behind the decision. Use when a customer asks "why is she #1?", "why did he rank low?", "explain the fit", "what's the rationale?", "where are the gaps?", or "can I see the evidence?". Pulls per-candidate reasoning via get_matched_profile_details and the auditable decision record via get_decision_audit on the live Workerbee MCP server.
---

# Explain Fit

This skill's playbook lives on the Workerbee MCP server and is resolved to your current access scope. Always load it fresh — never follow a cached copy.

1. Call the **`get_skill_instructions`** tool with `skillKey: "explain-fit"`.
2. Follow the returned instructions exactly — they are the authoritative, up-to-date playbook for this skill.
3. If it returns upgrade guidance instead of a playbook, relay that guidance to the user and do not run the workflow.
4. If any tool call returns a `FEATURE_NOT_IN_PLAN` or `DEMO_EXPIRED` error, relay its `message` to the user verbatim and stop — do not improvise or fabricate results.
