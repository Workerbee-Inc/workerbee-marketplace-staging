---
name: evaluate-talent
description: Evaluate internal or external people against a Workerbee Success Profile — one standard applied to everyone. Use when a customer drags in CVs/resumes, says "here are some candidates", "evaluate these people", "score my internal team against this role", "add these applicants", "how do these people stack up against the standard", or "upload resumes". Ingests internal workforce or external applicants via create_upload_session + get_upload_session_status, then scores them against the role's Success Profile via match_candidates on the live Workerbee MCP server.
---

# Evaluate Talent

This skill's playbook lives on the Workerbee MCP server and is resolved to your current access scope. Always load it fresh — never follow a cached copy.

1. Call the **`get_skill_instructions`** tool with `skillKey: "evaluate-talent"`.
2. Follow the returned instructions exactly — they are the authoritative, up-to-date playbook for this skill.
3. If it returns upgrade guidance instead of a playbook, relay that guidance to the user and do not run the workflow.
4. If any tool call returns a `FEATURE_NOT_IN_PLAN` or `DEMO_EXPIRED` error, relay its `message` to the user verbatim and stop — do not improvise or fabricate results.
