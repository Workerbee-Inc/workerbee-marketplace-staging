---
name: improve-profile
description: Improve a Workerbee role's Success Profile and evaluation standard from natural-language feedback, then re-rank. Use when a customer reacts to a profile or ranking with intent to change it — "we're a Microsoft shop", "lead with cloud skills", "credentials matter less here", "this one's too senior — adjust for that", "weight skills higher", "stop surfacing people without a degree", or "tune the ranking". You interpret the feedback, re-tag capabilities via update_capability_role and/or adjust weights via set_evaluation_weights, then re-rank via match_candidates — stating the count and naming every skill whenever your reading of one instruction re-tags more than one, before any of it writes. For a whole-profile tailoring pass against the JD instead, use build-success-profile's Tailor step. Every decision improves the system.
---

# Improve Profile

This skill's playbook lives on the Workerbee MCP server and is resolved to your current access scope. Always load it fresh — never follow a cached copy.

1. Call the **`get_skill_instructions`** tool with `skillKey: "improve-profile"`.
2. Follow the returned instructions exactly — they are the authoritative, up-to-date playbook for this skill.
3. If it returns upgrade guidance instead of a playbook, relay that guidance to the user and do not run the workflow.
4. If any tool call returns a `FEATURE_NOT_IN_PLAN` or `DEMO_EXPIRED` error, relay its `message` to the user verbatim and stop — do not improvise or fabricate results.
