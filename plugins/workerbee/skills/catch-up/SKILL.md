---
name: catch-up
description: Catch up on the state of all your Workerbee jobs — which jobs exist, where each stands, candidate and applicant counts by source, top candidates, candidates who've declined ("Not Interested"), and what needs attention. Use when a customer says "catch me up", "what's new?", "where are things?", "show me my jobs", "show me my roles", "what's the status?", "who said no?", "any declines?", "what's changed since last time?", or opens a session cold and wants the lay of the land. Loads the full portfolio via list_my_jobs, the latest per-job development via get_job_context, and company-wide invite responses via list_company_invites on the live Workerbee MCP server.
---

# Catch Up

This skill's playbook lives on the Workerbee MCP server and is resolved to your current access scope. Always load it fresh — never follow a cached copy.

1. Call the **`get_skill_instructions`** tool with `skillKey: "catch-up"`.
2. Follow the returned instructions exactly — they are the authoritative, up-to-date playbook for this skill.
3. If it returns upgrade guidance instead of a playbook, relay that guidance to the user and do not run the workflow.
4. If any tool call returns a `FEATURE_NOT_IN_PLAN` or `DEMO_EXPIRED` error, relay its `message` to the user verbatim and stop — do not improvise or fabricate results.
