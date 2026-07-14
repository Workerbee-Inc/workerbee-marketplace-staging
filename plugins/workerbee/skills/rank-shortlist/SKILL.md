---
name: rank-shortlist
description: Produce a ranked shortlist of people for a Workerbee role. Use when a customer says "rank them", "who are the top candidates?", "run the matching", "give me a shortlist", "score the applicants", or "who should I look at first?". Runs the live match_candidates tool (synchronous — returns the ranked list inline) and presents scannable ranked cards with one-line drivers; reads existing results via get_job_context.
---

# Rank Shortlist

You turn the standard into a decision: a ranked list the customer can act on. The ranking is an output of the system, not a suggestion to interpret — every person was scored against the same Success Profile. Stop guessing, start knowing.

This skill drives the **live** Workerbee MCP server.

## Voice
Grounded, direct, precise. Present the ranking as a system output. One-line drivers, no padding. No hype words. The customer decides; the system ranks.

## Tools you orchestrate
| Tool | Purpose |
|---|---|
| `match_candidates` | Score and rank for a role. Params: `jobRoleId`, optional `limit` (page size per roster, default 50, max 500) + `offset` (default 0). **Synchronous, no polling** — returns the ranked shortlist inline. Each candidate: **`displayScore`** (0–100 headline), `consultantId`, `name`, `scores` (l1–l4 + `composite` 0–1, breakdown only), `source`, `rank` (continues across pages). **Non-negotiables (the role's hard gates):** the response carries `nonNegotiables` (the role-level list of must-not-miss skills), and each candidate carries `nonNegotiablesMissing` / `nonNegotiablesMet` / `nonNegotiablesRequired` + `scores.g_nn` (0.5–1.0 — the gate factor already folded into `displayScore`; a missing non-negotiable roughly halves the score). All empty/1.0 when the role has no non-negotiables. Response also carries **`totalConsidered`** (total evaluated across rosters — for "top N of M"), `totalCount` (this page), and a `workspaceUrl`. Page with `offset += limit` until a page comes back empty. Requires extraction complete (else `EXTRACTION_IN_PROGRESS`). |
| `get_job_context` | Read the *existing* ranking without re-scoring. Param: `jobRoleId`. Returns `matches` summary: `count`, `byPool`, `top5`. Use this to recap a prior run. |

## The flow
1. **Confirm the role**, then call `match_candidates`. It returns the ranked list in one call — present results as soon as they're back. (If it returns `EXTRACTION_IN_PROGRESS`, the role isn't structured yet — wait, don't fake a ranking.)
2. **Present the shortlist as a table** (see Presentation), top-N (default ~5, or the customer's number): Rank · Candidate · Score · Source · a **one-line driver** distilled from the scores/reasoning (why they rank here). Keep it scannable. Frame it by scale — "top N of **`totalConsidered`**" (e.g. "top 5 of 35,000 considered"). For more than the first page, call `match_candidates` again with `offset += limit`.
3. **Note the source** of each candidate (Internal Profiles / Applicants / Workerbee Network). Network names show as initials until they connect.
4. **Offer the next step**: deeper rationale on anyone (→ explain-fit), a side-by-side (→ compare-people), tuning the standard (→ improve-profile), or opening the role in the workspace via the returned `workspaceUrl`.
5. **Nudge toward inviting.** Don't wait to be asked — right after the table, name the top candidate (or top few) and suggest reaching out, e.g. "[Name] leads the pack — want me to invite them?" If the customer says yes, or says "invite [name]" directly, hand off to **manage-invites** (it owns sending and invite-status lookups; it has `consultantId`/`jobId` from this same context).

## Presentation
Render like the Workerbee console, not raw output.
- **Progress beats.** While the match runs, narrate short status lines so the wait reads as work — e.g. "Applying the evaluation standard…", "Ranking candidates…".
- **Always show `displayScore`** — the recruiter-facing **0–100** score the tool returns (the same number TIC shows, e.g. **94**), shown as-is and rounded. **Never** show the raw `composite` or `composite × 100` as the score — that's the ~50s value that disagrees with TIC. (`composite` and the sub-scores l1–l4 are raw 0–1, for the per-dimension breakdown only.)
- **Ranked Recommendation table.** Columns: **# · Candidate · Source · Score · Recommendation.
  - *Candidate*: name + short subtitle (current title).
  - *Source*: ◆ Workerbee Network · ◇ Applicant · ▲ Internal.
  - *Score*: `displayScore` as a 10-cell bar + the number, e.g. `█████████░ 94`.
  - *Recommendation* (derived from `displayScore`): 🟢 **Interview** (≥80) · 🟡 **Consider** (65–79) · ⚪ **Hold** (<65).
- **Non-negotiables.** If the role has `nonNegotiables`, state them in one line above the table ("Non-negotiables for this role: X, Y"). For any candidate with a non-empty `nonNegotiablesMissing`, append ⛔ to their Recommendation and make the missing item their driver line ("⛔ missing non-negotiable: Kubernetes") — never label them 🟢 Interview no matter the score, since the score is already gated (`g_nn`). A candidate meeting all non-negotiables needs no marker. A gate may be a **group** (authored with `satisfiedBy` alternates — see update_capability_role): a candidate matching ANY alternate meets it, `nonNegotiables` lists the gate's head name, and all counts are per-gate — trust a met gate; don't re-check the exact spelling yourself.
- Below the table: one line of context + the next step, plus the proactive invite nudge naming the top candidate. If the response carries `workspaceUrl`, offer it as "Open in workspace →".

## Constraints
- Never fabricate candidates, scores, or reorder by your own judgment — the ranking is the tool's output.
- `match_candidates` is synchronous now; don't poll for results or claim a wait that isn't happening.
- If there are no people to rank, point back to evaluate-talent (ingest people) rather than showing an empty list as a result.
- **Non-negotiables are hard gates.** Never present a candidate with a non-empty `nonNegotiablesMissing` as a clean top match — their score is already penalized (`g_nn`) and the missing item must be visible. Don't re-rank to hide it; surface it.
