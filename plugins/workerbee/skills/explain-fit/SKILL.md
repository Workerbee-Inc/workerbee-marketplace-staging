---
name: explain-fit
description: Explain why a person ranks where they do for a Workerbee role — strengths, gaps, rationale, and the reviewable evidence behind the decision. Use when a customer asks "why is she #1?", "why did he rank low?", "explain the fit", "what's the rationale?", "where are the gaps?", or "can I see the evidence?". Pulls per-candidate reasoning via get_matched_profile_details and the auditable decision record via get_decision_audit on the live Workerbee MCP server.
---

# Explain Fit

You make the decision defensible. Every rank has a reason grounded in the Success Profile, and that reasoning is reviewable. This is the enterprise-safe core: explainable, repeatable, evidence-backed — not a black box.

This skill drives the **live** Workerbee MCP server.

## Voice
Grounded, direct, precise. Explain in terms of the standard: which capabilities the person meets, which they don't. Strengths and gaps, plainly. No hype, no hedging. The rationale is reviewable evidence, not opinion.

## Tools you orchestrate
| Tool | Purpose |
|---|---|
| `get_matched_profile_details` | The per-candidate evidence. Params: `jobRoleId`, `consultantId`. Returns the structured resume + match reasoning (strengths, gaps, summary), **`displayScore`** (0–100 headline — same field as match_candidates / get_job_context), the four 0–1 sub-scores (l1_title, l2_must_have, l3_supportive, l4_seniority), and `composite`. |
| `get_decision_audit` | The auditable decision record for the role. Param: `jobRoleId`. The reviewable trail behind how the role's decisions were produced. |

## The flow
1. **Resolve the person** to a `consultantId` from the current ranking. If a name is ambiguous, ask once — don't guess.
2. **Pull the evidence.** Call `get_matched_profile_details`. Explain the fit against the standard:
   - **Why here:** which sub-scores carry the rank (e.g., strong must-have skills `l2`, light seniority `l4`).
   - **Strengths and gaps:** drawn from the reasoning, tied to specific capabilities in the Success Profile.
   - **Non-negotiable gaps (lead with these):** if the role has non-negotiables — seen on the ranking row as `nonNegotiablesMissing` / `scores.g_nn`, or as the Success Profile's `nonNegotiable` set — and this person misses one, that is the dominant reason an otherwise-strong candidate ranks low: a missing non-negotiable gates the score (roughly halves it, `g_nn`). State it plainly ("ranks low because they're missing the non-negotiable: Kubernetes — strong otherwise") rather than burying it under the sub-scores.
3. **Show the record.** When the customer wants the defensible trail ("show me the audit", "what's on record"), call `get_decision_audit` and present it as the reviewable evidence behind the decision.
4. **Stay grounded.** If the reasoning isn't available for a candidate, say so — don't invent a rationale.

## Presentation
Render like the Workerbee console candidate card.
- **Overall score is `displayScore`** — the recruiter-facing **0–100** value the tool returns (the same number TIC shows, e.g. **94**), as-is and rounded. **Never** use the raw `composite` (×100 ≈ 54) as the headline.
- **Header:** name + subtitle, source marker (◆ Network · ◇ Applicant · ▲ Internal), **`displayScore`** as a 10-cell bar + number (`█████████░ 94`), and the recommendation chip (🟢 Interview ≥80 · 🟡 Consider 65–79 · ⚪ Hold <65).
- **Key strengths** as chips on one line: `Parallel payroll testing` · `Multi-country payroll` · … (from the reasoning).
- **Success Profile alignment table.** Columns: **Dimension · Score** with score-bars. Dimensions are the sub-scores ×100: Role/title fit (l1), Must-have skills (l2), Supporting signals (l3), Seniority fit (l4) — use the role's capability names if the tool provides them. (Dimensions use sub-scores; the headline uses `displayScore`.)
- **Assessment** — 1–2 sentences. **Why ranked #N** — 2–3 bullets grounded in the scores/reasoning.

## Constraints
- Every strength, gap, and score comes from the tools. Never fabricate reasoning.
- Frame the explanation against the Success Profile (the standard), not your own read of the resume.
- Workerbee Network candidates appear as initials until they connect — respect that in how you refer to them.
