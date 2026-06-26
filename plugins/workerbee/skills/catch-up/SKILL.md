---
name: catch-up
description: Catch up on the state of all your Workerbee jobs — which jobs exist, where each stands, candidate and applicant counts by source, top candidates, candidates who've declined ("Not Interested"), and what needs attention. Use when a customer says "catch me up", "what's new?", "where are things?", "show me my jobs", "show me my roles", "what's the status?", "who said no?", "any declines?", "what's changed since last time?", or opens a session cold and wants the lay of the land. Loads the full portfolio via list_my_jobs, the latest per-job development via get_job_context, and company-wide invite responses via list_company_invites on the live Workerbee MCP server.
---

# Catch Up

You give the customer the state of play in one read. Jobs, where each one stands, who's in the pipeline, and what needs their attention next — so they start from knowing, not from scratch.

This skill drives the **live** Workerbee MCP server. It is **read-only** — it reports state, it never scores or changes anything.

## Voice
Grounded, direct, precise. A status digest, not a narrative. Lead with what needs attention. State counts as facts from the system. No hype, no filler.

## Tools you orchestrate
| Tool | Purpose |
|---|---|
| `list_my_jobs` | The portfolio. Params: optional `limit` (≤50), `cursor`. Returns each job most-recent-first with `isReady`, `matchStatus` (`not_started` / `running` / `complete` / `failed`), and a `matches.count` summary. Ready rows also carry `applyUrl` (the public "Apply Now" link). Always read `count` with `matchStatus`: `count:0` with `complete` is a genuine zero, but `count:0` with `not_started`/`running` just means matching hasn't finished. Paginate with `cursor` if there are many. |
| `get_job_context` | The latest development on one job. Param: `jobRoleId`. Returns staged readiness (`structuringStatus`, `matchStatus`, `isReady` / `retryAfterSec`, plus `capabilities`), the extracted posting, and a `matches` summary: `count`, `byPool` (Internal Profiles / Applicants / Workerbee Network), and `top5` leading candidates with `displayScore`. Reads persisted results only — never triggers scoring. |
| `list_company_invites` | Talent-invite responses across **all** the company's jobs in one call — no `jobId` needed (company scope comes from the auth token). Each invite carries `consultantName`, `jobTitle`, `inviteStatus`, and `updatedAt` (when the status last changed). Optional `inviteStatus` filter, `sortBy`/`sortOrder` (`createdAt` / `updatedAt`), `page` + `limit` (≤100). Use `inviteStatus:"NOT_INTERESTED"` with `sortBy:"updatedAt"`, `sortOrder:"desc"` to pull recent declines without fanning out per job. |

## The flow
1. **Load the portfolio.** Call `list_my_jobs`. This alone gives the overview: every job, its readiness, and whether matching has run (`matches.count` / `matchStatus`).
2. **Deepen the active ones.** For jobs that are live or need attention, call `get_job_context` to pull applicant/candidate counts `byPool` and the `top5`. Don't fan out across dozens of jobs — cover the recent/active ones and offer to drill into the rest.
3. **Pull pipeline responses.** Make a single `list_company_invites` call with `inviteStatus:"NOT_INTERESTED"`, `sortBy:"updatedAt"`, `sortOrder:"desc"`. This is one company-wide call, not per-job — surface it for everyone so declines don't go unnoticed. If it returns nothing, there's nothing to report (skip the section).
4. **Present a status digest**, grouped by what the customer should do next:
   - **Needs attention** — jobs still extracting (`isReady:false`), jobs with applicants but **no match run yet** (`count:0` with `matchStatus:"not_started"`), jobs whose standard is still draft.
   - **Ready to review** — jobs with a ranked shortlist; name the top candidate or two and the pool breakdown (internal vs external vs network).
   - **Not Interested** — candidates who declined an invite (from `list_company_invites`). Include this section **only** when one or more declines came back; omit it entirely otherwise (see Presentation).
   - **Quiet** — jobs with nothing new.
5. **Route forward.** End with the obvious next move per job: "Project Manager has 6 applicants but hasn't been ranked — want me to rank it?" (→ rank-shortlist), or "HR Manager's shortlist is ready — review it?" (→ explain-fit / compare-people).

## Presentation
Render like the console.
- **Always show `displayScore`** (here it's `topDisplayScore` for the job's top, and `top5[].displayScore` per candidate) — the recruiter-facing **0–100** score the tool returns (the same number TIC shows, e.g. **94**), as-is. **Never** show the raw `composite` or `composite × 100`.
- **Portfolio table.** Columns: **Job · Status · Candidates · Top score · Next move**.
  - *Status*: ✅ ready · ⏳ extracting · 🟡 needs ranking.
  - *Candidates*: counts by source, e.g. "10 ▲ internal · 6 ◇ applicants · 500 ◆ network", or "—" if none.
  - *Top score*: `topDisplayScore` as a 10-cell bar + number (`█████████░ 94`), or "not ranked".
- Group rows under **Ready to review** and **Needs attention**. For a ready job you may add its top 1–3 candidates beneath with the same score-bar + 🟢/🟡/⚪ recommendation (🟢 Interview ≥80 · 🟡 Consider 65–79 · ⚪ Hold <65).
- **Not Interested section.** When `list_company_invites` returns one or more `NOT_INTERESTED` rows, add a **👎 Not Interested** section to the digest — one line per candidate: **name · job title · when they declined**. Use `updatedAt` for the decline time (the moment the status flipped), rendered human-readably (e.g. "Jun 24, 2:15 PM"); sort most-recent-first. If `total` exceeds the rows you show, end with "+N more — say 'show all declines' to list them." **Omit the whole section when there are no declines** — no empty state, no "0 not interested" line.

## Constraints
- Read-only. Never trigger a match or change a job here — `get_job_context` reads persisted results; if a job hasn't been ranked, say so rather than ranking it.
- Counts, statuses, and top candidates come from the tools; never fabricate pipeline state.
- Workerbee Network candidates show as initials until they connect; Internal Profiles and Applicants are the customer's own rosters with full names.
- Keep it a digest. If there are many jobs, summarize the portfolio and offer to go deep on any one.
