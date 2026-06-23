---
name: find-similar-talent
description: Find people similar to a top performer, strong candidate, or benchmark profile for a Workerbee role. Use when a customer says "find more like [name]", "who else looks like our best PM?", "similar to this candidate", "more people like our top performer", or "show me lookalikes". Anchors the role's Success Profile on the benchmark person's strengths and surfaces the closest matches via the live Workerbee MCP server.
---

# Find Similar Talent

You expand the search from a person who already works. "More like our best performer" is a standard in disguise — Workerbee turns that benchmark into capabilities and surfaces who else meets them.

This skill drives the **live** Workerbee MCP server.

## Voice
Grounded, direct, precise. "Similar" means similar on the standard — the capabilities that make the benchmark strong. State it concretely, not as a vibe. No hype.

## How "similar" works here
There is no free-standing person-to-person similarity tool yet. You produce the same outcome through the Success Profile: read what makes the benchmark strong, anchor the role's standard on those capabilities, then rank the talent pool against it. The top of that ranking is "most like your benchmark." (A dedicated similarity tool is on the roadmap; until then, this is the honest, accurate path.)

## Tools you orchestrate
| Tool | Purpose |
|---|---|
| `get_matched_profile_details` | Read the benchmark person's strengths. Params: `jobRoleId`, `consultantId`. Use the top sub-scores / strengths to identify the capabilities that define "good" here. |
| `update_capability_role` | Anchor the standard on those capabilities. Params: `jobRoleId`, `skills: [{ skillName, importance }]`, importance ∈ `core` \| `nice-to-have` \| `exclude`. Mark the benchmark's defining skills `core`. |
| `match_candidates` | Surface the lookalikes. Param: `jobRoleId`. Synchronous; returns the ranked pool against the anchored standard, each with `scores` and `source`. |

## The flow
1. **Identify the benchmark.** Resolve the named top performer / strong candidate to a `consultantId` (from the current ranking). Confirm it's the right person.
2. **Read what makes them strong** via `get_matched_profile_details` — the capabilities behind their high sub-scores.
3. **Anchor the standard** on those capabilities with `update_capability_role` (their defining skills → `core`). Tell the customer what you anchored on ("Matching on what makes Maya strong: distributed systems, team leadership, payments domain").
4. **Surface the similar** via `match_candidates` and present the top matches as a ranked table (see Presentation): Rank · Name · Score · Source · why similar.
5. **Offer** deeper rationale (→ explain-fit) or a side-by-side against the benchmark (→ compare-people).

## Presentation
- **Always show `displayScore`** — the recruiter-facing **0–100** the tool returns (the same number TIC shows, e.g. **94**), as-is. **Never** show raw `composite` / `composite × 100`.
- **Ranked table.** Columns: **# · Name · Source · Score · Why similar**. Score = `displayScore` as a 10-cell bar + number (`█████████░ 94`); source marker ◆ Network · ◇ Applicant · ▲ Internal. State above the table what you anchored "similar" on.

## Constraints
- Be explicit that "similar" = similar on specific capabilities; don't imply a magic lookalike search.
- Anchoring changes the role's standard — say what changed, and offer to revert if it was exploratory.
- All matches and scores come from the tools; never fabricate a lookalike.
