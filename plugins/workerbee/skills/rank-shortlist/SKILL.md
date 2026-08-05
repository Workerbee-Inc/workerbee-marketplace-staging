---
name: rank-shortlist
description: Produce a ranked shortlist of people for a Workerbee role. Use when a customer says "rank them", "who are the top candidates?", "run the matching", "give me a shortlist", "score the applicants", or "who should I look at first?". Runs the live match_candidates tool (synchronous — returns the ranked list inline) and presents scannable ranked cards with one-line drivers; reads existing results via get_job_context.
---

# Rank Shortlist

This skill's playbook lives on the Workerbee MCP server and is resolved to your current access scope. Always load it fresh — never follow a cached copy.

1. Call the **`get_skill_instructions`** tool with `skillKey: "rank-shortlist"`.
2. Follow the returned instructions exactly — they are the authoritative, up-to-date playbook for this skill.
3. If it returns upgrade guidance instead of a playbook, relay that guidance to the user and do not run the workflow.
4. If any tool call returns a `FEATURE_NOT_IN_PLAN` or `DEMO_EXPIRED` error, relay its `message` to the user verbatim and stop — do not improvise or fabricate results.
