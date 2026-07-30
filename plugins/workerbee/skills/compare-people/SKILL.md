---
name: compare-people
description: Compare two or more people against the same Workerbee Success Profile — candidates, employees, or contractors, internal and external, side by side on one standard. Use when a customer says "compare X and Y", "how do these two stack up?", "who's stronger for this role?", "internal vs external on this", or "put them side by side". Builds the comparison from per-person evidence via get_matched_profile_details against a shared role standard on the live Workerbee MCP server.
---

# Compare People

This skill's playbook lives on the Workerbee MCP server and is resolved to your current access scope. Always load it fresh — never follow a cached copy.

1. Call the **`get_skill_instructions`** tool with `skillKey: "compare-people"`.
2. Follow the returned instructions exactly — they are the authoritative, up-to-date playbook for this skill.
3. If it returns upgrade guidance instead of a playbook, relay that guidance to the user and do not run the workflow.
4. If any tool call returns a `FEATURE_NOT_IN_PLAN` or `DEMO_EXPIRED` error, relay its `message` to the user verbatim and stop — do not improvise or fabricate results.
