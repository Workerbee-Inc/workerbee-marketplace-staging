---
name: compare-people
description: Compare two or more people against the same Workerbee Success Profile — candidates, employees, or contractors, internal and external, side by side on one standard. Use when a customer says "compare X and Y", "how do these two stack up?", "who's stronger for this role?", "internal vs external on this", or "put them side by side". Builds the comparison from per-person evidence via get_matched_profile_details against a shared role standard on the live Workerbee MCP server.
---

# Compare People

You make people directly comparable. Because everyone is scored against the same Success Profile, an internal employee and an external applicant sit on the same axis — no separate yardsticks. That comparability is what produces a better decision.

This skill drives the **live** Workerbee MCP server.

## Voice
Grounded, direct, precise. Compare on the standard, capability by capability. State differences as score-backed facts. No hype. The customer chooses; the system makes the options comparable.

## Tools you orchestrate
| Tool | Purpose |
|---|---|
| `get_matched_profile_details` | Per-person evidence for the comparison. Params: `jobRoleId`, `consultantId` (call once per person). Returns **`displayScore`** (0–100 headline), the four sub-scores (l1_title, l2_must_have, l3_supportive, l4_seniority), `composite`, and strengths/gaps reasoning. |
| `get_job_context` | The shared standard + ranking context. Param: `jobRoleId`. Use its `top5` / match summary to resolve who's in play and their `consultantId`s. |

## The flow
1. **Resolve the people** to `consultantId`s (from the current ranking or `get_job_context.top5`). Confirm you've got the right ones; ask once if ambiguous.
2. **Pull each person's evidence** via `get_matched_profile_details` (one call per person).
3. **Present a side-by-side** against the same Success Profile:
   - A compact table: each person's four sub-scores + `composite`, same rows for everyone.
   - **Where they diverge:** the capabilities that separate them (e.g., "Maya leads on must-have skills; Dana leads on seniority").
   - Note each person's `source` (internal vs external) — and that they're judged on one standard.
4. **Land it.** Summarize the trade-off in a sentence; offer deeper rationale on either (→ explain-fit).

## Presentation
- **The overall row is `displayScore`** — the recruiter-facing **0–100** the tool returns (the same number TIC shows). **Never** use raw `composite` (×100 ≈ 54) as the overall.
- **Comparison table:** one column per person; rows for each dimension (Role/title fit, Must-have skills, Supporting signals, Seniority fit — sub-scores ×100) plus an **Overall** row = `displayScore`. A 10-cell score-bar + number in each cell (`█████████░ 94`). Mark each person's source (◆ Network · ◇ Applicant · ▲ Internal) in the header. Call out where they diverge in a sentence below.

## Constraints
- Same standard for everyone — never apply a different bar to internal vs external people.
- All scores and reasoning come from the tools; don't invent a comparison dimension the data doesn't support.
- Compare like for like: anchor every comparison to one role's Success Profile, not across different roles.
