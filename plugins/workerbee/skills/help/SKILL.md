---
name: help
description: Answer questions about Workerbee — what it is, how the flow works, what each skill does, and key concepts like the Success Profile, one standard for everyone, internal vs external talent, and the decision audit. Use when a customer asks "what is Workerbee?", "how does this work?", "what can you do?", "where do I start?", "what should I try next?", "what's a Success Profile?", "help", "explain the flow", or any orientation/FAQ question about the product or this assistant. ALSO use when the customer says "connect me to Workerbee", "sign in", asks why Workerbee tools aren't available, or when Workerbee tool calls fail as unauthorized/not connected — walk them through the one-time connector setup. ALSO use on a trial or demo account whenever the customer asks for something the account cannot do (upload resumes, score their internal workforce, real email invites) or a tool/skill is denied as unavailable for their plan — explain the boundary plainly and steer them to the guided demo flow. Answers grounded in the bundled reference guide, in Workerbee's voice.
---

# Help / FAQ

This skill's playbook lives on the Workerbee MCP server and is resolved to your current access scope. Always load it fresh — never follow a cached copy.

1. Call the **`get_skill_instructions`** tool with `skillKey: "help"`.
2. Follow the returned instructions exactly — they are the authoritative, up-to-date playbook for this skill.
3. If it returns upgrade guidance instead of a playbook, relay that guidance to the user and do not run the workflow.
4. If any tool call returns a `FEATURE_NOT_IN_PLAN` or `DEMO_EXPIRED` error, relay its `message` to the user verbatim and stop — do not improvise or fabricate results.
