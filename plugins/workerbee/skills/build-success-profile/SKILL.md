---
name: build-success-profile
description: Build a Workerbee Success Profile — the reusable standard for what good looks like in a role — from a job description, top performers, and manager context, then tailor it to the company. Use when a customer says "I need to hire a [title]", pastes a JD, says "create a role", "build a success profile", "define what good looks like here", "set the standard for this role", or "open a req" — and when they want a profile tailored — "tailor this profile to the JD", "this profile is too broad", "narrow it down", "which skills actually matter for this role", "drop the irrelevant skills". Runs the quality gate (JD + 2–3 qualifying questions), reveals the AI-structured profile with non-negotiable, core, and nice-to-have capabilities, confidence, and provenance, then proposes a tailoring diff written to the profile only on the customer's confirmation. Calls the live Workerbee MCP tools create_job, get_job_context, get_success_profile, propose_success_profile, apply_success_profile_proposal, and update_capability_role.
---

# Build Success Profile

This skill's playbook lives on the Workerbee MCP server and is resolved to your current access scope. Always load it fresh — never follow a cached copy.

1. Call the **`get_skill_instructions`** tool with `skillKey: "build-success-profile"`.
2. Follow the returned instructions exactly — they are the authoritative, up-to-date playbook for this skill.
3. If it returns upgrade guidance instead of a playbook, relay that guidance to the user and do not run the workflow.
4. If any tool call returns a `FEATURE_NOT_IN_PLAN` or `DEMO_EXPIRED` error, relay its `message` to the user verbatim and stop — do not improvise or fabricate results.
