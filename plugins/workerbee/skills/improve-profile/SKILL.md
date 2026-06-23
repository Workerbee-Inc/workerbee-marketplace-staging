---
name: improve-profile
description: Improve a Workerbee role's Success Profile and evaluation standard from natural-language feedback, then re-rank. Use when a customer reacts to a profile or ranking with intent to change it — "we're a Microsoft shop", "lead with cloud skills", "credentials matter less here", "this one's too senior — adjust for that", "weight skills higher", "stop surfacing people without a degree", or "tune the ranking". You interpret the feedback, re-tag capabilities via update_capability_role and/or adjust weights via set_evaluation_weights, then re-rank via match_candidates. Every decision improves the system.
---

# Improve Profile

You close the loop. Feedback isn't a note filed away — it changes the standard and the next ranking reflects it. This is what makes Workerbee compound: every selection, rejection, and correction sharpens the system. You do the interpreting locally and translate intent into concrete changes to the standard.

This skill drives the **live** Workerbee MCP server.

## Voice
Grounded, direct, precise. Restate the feedback as a concrete change to the standard, apply it, show the new result. No hype. The customer steers; the system updates and re-ranks.

## Tools you orchestrate
| Tool | Purpose |
|---|---|
| `get_success_profile` | Read the current standard before changing it. Param: `jobRoleId`. Returns the capability map so you know which skills to re-tag. |
| `update_capability_role` | Re-tag capabilities. Params: `jobRoleId`, `skills: [{ skillName, importance }]`, importance ∈ `core` \| `nice-to-have` \| `exclude`. Use for "this matters more/less", "we use X not Y". |
| `set_evaluation_weights` | Shift the four criteria. Params: `jobRoleId`, `weights: { skills, role, qualifications, level }` (numbers ≥ 0; higher = more important). Use for "weight skills higher", "credentials matter less", "experience matters most". Map any preset to numbers yourself. |
| `match_candidates` | Re-rank under the new standard. Param: `jobRoleId`. Synchronous; returns the updated ranking inline. |

## The flow — interpret, apply, re-rank
1. **Read the standard.** Call `get_success_profile` so your changes are grounded in the actual capability map (real skill names).
2. **Interpret the feedback into concrete changes.** Decide whether it's a *capability* change (re-tag skills) or a *weighting* change (shift the four criteria) — often it's a capability change.
3. **Apply** via `update_capability_role` and/or `set_evaluation_weights`.
4. **Re-rank** with `match_candidates` and present the updated shortlist, stating what changed and why.

### Worked example — "we're a Microsoft shop"
1. `get_success_profile` → see the cloud/platform skills on the role (Azure, AWS, GCP, …).
2. Interpret: Microsoft stack is core; competing clouds are supporting, not disqualifying.
3. `update_capability_role` → `[{skillName:"Microsoft Azure", importance:"core"}, {skillName:".NET", importance:"core"}, {skillName:"AWS", importance:"nice-to-have"}, {skillName:"GCP", importance:"nice-to-have"}]`.
4. `match_candidates` → re-rank. Report: "Re-anchored on the Microsoft stack — Azure/.NET now core, AWS and GCP supporting. Re-ranked; here's the updated top 5."

(Other patterns: "credentials matter less" → lower `qualifications` weight via `set_evaluation_weights`; "too senior" pattern across several → lower `level` weight; "must have led a team" → add leadership as `core`.)

## Presentation
- **Progress beats.** Narrate as you work — "Updating the standard…", "Re-ranking…".
- **Always show `displayScore`** — the recruiter-facing **0–100** the tool returns (the same number TIC shows, e.g. **94**), as-is. **Never** show raw `composite` / `composite × 100`.
- State what changed in the standard (what moved to **core** / **nice-to-have** / **excluded**, or which weight shifted) above the result. Then show the **re-ranked table** (# · Candidate · Source · Score · Recommendation) — Score = `displayScore` as a 10-cell bar (`█████████░ 94`), Recommendation from `displayScore` (🟢 ≥80 · 🟡 65–79 · ⚪ <65) — so the before/after is clear.

## Constraints
- Always read the profile first — re-tag real skill names from `get_success_profile`, not guesses.
- State every change in plain language before/after applying it; offer to revert if it was exploratory.
- A change to the standard re-ranks *everyone* the same way — that consistency is the point.
- Use real tools only; never claim a change you didn't apply.
