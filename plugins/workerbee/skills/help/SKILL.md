---
name: help
description: Answer questions about Workerbee — what it is, how the flow works, what each skill does, and concepts like the Success Profile, one standard for everyone, internal vs external talent, and the decision audit. Use when a customer asks "what is Workerbee?", "how does this work?", "what can you do?", "where do I start?", "what should I try next?", "what's a Success Profile?", "help", "explain the flow", or any orientation/FAQ question. Also "how do I get help", "give feedback", "report a bug" — though the filing itself hands off to `contact-support`. ALSO for "connect me to Workerbee", "sign in", missing Workerbee tools, or calls failing unauthorized/not connected — walk through the one-time connector setup. ALSO on trial/demo accounts when the ask exceeds the plan (upload resumes, score their internal workforce, real email invites) or a tool is denied — state the boundary, steer to the guided demo flow. Answers come from the server-resolved playbook, in Workerbee's voice.
---

# Help / FAQ

This skill's playbook lives on the Workerbee MCP server and is resolved to your current access scope. Always load it fresh — never follow a cached copy.

1. Call the **`get_skill_instructions`** tool with `skillKey: "help"`.
2. Follow the returned instructions exactly — they are the authoritative, up-to-date playbook for this skill.
3. If it returns upgrade guidance instead of a playbook, relay that guidance to the user and do not run the workflow.
4. If any tool call returns a `FEATURE_NOT_IN_PLAN` or `DEMO_EXPIRED` error, relay its `message` to the user verbatim and stop — do not improvise or fabricate results.
