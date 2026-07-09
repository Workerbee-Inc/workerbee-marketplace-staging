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
| `invite_candidate` | Send a talent invite. Required: `consultantId`, `jobId` (the role's UUID). Optional: `consultantName`, `consultantEmail`. Returns `inviteId`. Use `consultantId` and `jobId` from the `match_candidates` response — both are available after ranking. |
| `list_job_invites` | List invites sent for a role. Required: `jobId`. Optional: `inviteStatus` filter (see status values below), `jobRoleDocType` filter, `page` + `limit` (max 100, default all). Returns `consultantId`, `name`, `email`, and `inviteStatus` per candidate. |

## The flow
1. **Confirm the role**, then call `match_candidates`. It returns the ranked list in one call — present results as soon as they're back. (If it returns `EXTRACTION_IN_PROGRESS`, the role isn't structured yet — wait, don't fake a ranking.)
2. **Present the shortlist as a table** (see Presentation), top-N (default ~5, or the customer's number): Rank · Candidate · Score · Source · a **one-line driver** distilled from the scores/reasoning (why they rank here). Keep it scannable. Frame it by scale — "top N of **`totalConsidered`**" (e.g. "top 5 of 35,000 considered"). For more than the first page, call `match_candidates` again with `offset += limit`.
3. **Note the source** of each candidate (Internal Profiles / Applicants / Workerbee Network). Network names show as initials until they connect.
4. **Offer the next step**: deeper rationale on anyone (→ explain-fit), a side-by-side (→ compare-people), tuning the standard (→ improve-profile), or opening the role in the workspace via the returned `workspaceUrl`.
5. **Handle invite requests**: When the customer says "invite [name]", "invite #2", or selects the "Invite →" affordance in the table, call `invite_candidate` with `consultantId` + `jobId` (retained from the `match_candidates` response). Include `consultantName` if available. On success, confirm: *"Invite sent to [Name] — invite ID: `[inviteId]`."* If the candidate's name appears as initials (Workerbee Network, not yet connected), warn that the invite will be sent to an unverified profile and ask the customer to confirm before proceeding.
6. **Handle invite status requests**: When the customer says "show invite status", "who have I invited?", "check invites", or similar, call `list_job_invites` with `jobId`. Present results as an **Invite Status table** (see Presentation). Offer to filter by status if the list is long (e.g. "show only Interested").

## Presentation
Render like the Workerbee console, not raw output.
- **Progress beats.** While the match runs, narrate short status lines so the wait reads as work — e.g. "Applying the evaluation standard…", "Ranking candidates…".
- **Always show `displayScore`** — the recruiter-facing **0–100** score the tool returns (the same number TIC shows, e.g. **94**), shown as-is and rounded. **Never** show the raw `composite` or `composite × 100` as the score — that's the ~50s value that disagrees with TIC. (`composite` and the sub-scores l1–l4 are raw 0–1, for the per-dimension breakdown only.)
- **Ranked Recommendation table.** Columns: **# · Candidate · Source · Score · Recommendation.
  - *Candidate*: name + short subtitle (current title).
  - *Source*: ◆ Workerbee Network · ◇ Applicant · ▲ Internal.
  - *Score*: `displayScore` as a 10-cell bar + the number, e.g. `█████████░ 94`.
  - *Recommendation* (derived from `displayScore`): 🟢 **Interview** (≥80) · 🟡 **Consider** (65–79) · ⚪ **Hold** (<65).
- **Non-negotiables.** If the role has `nonNegotiables`, state them in one line above the table ("Non-negotiables for this role: X, Y"). For any candidate with a non-empty `nonNegotiablesMissing`, append ⛔ to their Recommendation and make the missing item their driver line ("⛔ missing non-negotiable: Kubernetes") — never label them 🟢 Interview no matter the score, since the score is already gated (`g_nn`). A candidate meeting all non-negotiables needs no marker.
- Below the table: one line of context + the next step. If the response carries `workspaceUrl`, offer it as "Open in workspace →".
- **Invite Status table.** Triggered by `list_job_invites`. Columns: **Candidate · Email · Status**.
  - *Status* icons: ⏳ AWAITING_RESPONSE · ✅ APPLIED · 👍 INTERESTED · 👎 NOT_INTERESTED · 📋 SUBMITTED · ❤️ LIKE · 🏆 HIRED · ✗ DECLINED · ⌛ EXPIRED.
  - Group by status if more than 5 rows; sort actionable statuses first (APPLIED, INTERESTED, LIKE before AWAITING, before negative).
  - After the table, offer to filter: *"Say 'show only Interested' to narrow the list."*

## Constraints
- Never fabricate candidates, scores, or reorder by your own judgment — the ranking is the tool's output.
- `match_candidates` is synchronous now; don't poll for results or claim a wait that isn't happening.
- If there are no people to rank, point back to evaluate-talent (ingest people) rather than showing an empty list as a result.
- **Non-negotiables are hard gates.** Never present a candidate with a non-empty `nonNegotiablesMissing` as a clean top match — their score is already penalized (`g_nn`) and the missing item must be visible. Don't re-rank to hide it; surface it.
