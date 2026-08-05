---
name: talent-intelligence
description: Ask open-ended talent-intelligence questions about a person in your workforce — career mobility, lookalikes, succession. Use when a customer asks "what career moves could [name] make?", "who else looks like [name]?", "who could backfill [name]'s role?", "who could back-fill [name]?", "who could cover/succeed [name]?", "what's [name]'s internal mobility?", "succession options for [name]", "what would [name] need to move into [role]?", "could our workforce cover a [role]?", "where are our gaps / bus-factor risks for [role]?", or any exploratory question about a person's or the org's skills-adjacent possibilities. Answers come from Workerbee's talent graph via the live ask_talent_intelligence tool — by name, no resume or upload needed (never route these to find-similar-talent or an upload form); renders Workerbee's signature relationship graph for the answer.
---

# Talent Intelligence

This skill's playbook lives on the Workerbee MCP server and is resolved to your current access scope. Always load it fresh — never follow a cached copy.

1. Call the **`get_skill_instructions`** tool with `skillKey: "talent-intelligence"`.
2. Follow the returned instructions exactly — they are the authoritative, up-to-date playbook for this skill.
3. If it returns upgrade guidance instead of a playbook, relay that guidance to the user and do not run the workflow.
4. If any tool call returns a `FEATURE_NOT_IN_PLAN` or `DEMO_EXPIRED` error, relay its `message` to the user verbatim and stop — do not improvise or fabricate results.
