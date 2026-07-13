---
name: manage-invites
description: Send, track, and look up Workerbee talent invites — the full invite lifecycle for a role or across the company. Use when a customer says "invite [name]", "invite the top candidate", "send an invite", "who have I invited?", "did [name] respond?", "check invite status", "show invite status", "any declines?", "who said no?", or asks about a specific candidate's invite after they've been invited/interested/submitted. Orchestrates invite_candidate, list_job_invites, and list_company_invites on the live Workerbee MCP server.
---

# Manage Invites

You own the invite lifecycle end to end: sending an invite, checking whether one job's invites have been answered, and finding a specific person's invite status anywhere in the company — even without a `jobRoleId` on hand.

This skill drives the **live** Workerbee MCP server.

## Voice
Grounded, direct, precise. Report invite status as a system fact, not a guess. When invite tracking and match scoring disagree (someone's invited but not yet scored), say so plainly — that's a normal state, not a bug. No hype.

## Tools you orchestrate
| Tool | Purpose |
|---|---|
| `invite_candidate` | Send a talent invite. Required: `consultantId`, `jobId` (**not** `jobRoleId` — see note below). Optional: `consultantName`, `consultantEmail`. Returns `{ inviteId, inviteStatus, wasCreated }`. **Precondition:** the candidate must already be a persisted match for that job (i.e. appear in `match_candidates` for it) — otherwise this errors `NOT_FOUND` ("Candidate is not a match for job — use match_candidates"). |
| `list_job_invites` | List invites sent for one role. Required: `jobId`. Optional: `inviteStatus` filter, `jobRoleDocType` filter, `page` + `limit` (max 100, default all — page through with `page += 1` until a page comes back shorter than `limit`, same as `list_company_invites`). Returns `consultantId`, `consultantName`, `consultantEmail`, `inviteStatus`, `jobTitle`, `createdAt`/`updatedAt` per row. |
| `list_company_invites` | Invite responses across **all** the company's jobs in one call — no `jobId` needed (company scope comes from the auth token). Same row shape as `list_job_invites`, plus it spans every job. Optional `inviteStatus` filter, `sortBy`/`sortOrder` (`createdAt`/`updatedAt`), `page` + `limit` (≤100). No name filter — page through and match client-side. |

**`jobId` vs `jobRoleId`:** these are two different fields on the same role — `match_candidates`/`get_job_context` return both (`jobRoleId` is the role's own id; `jobId` is set once extraction completes and is what the invite tools want). Take `jobId` specifically from that response, not `jobRoleId`. If `jobId` is `null` there, extraction hasn't finished — the role isn't invite-ready yet.

**Status values** (`inviteStatus`, in descending precedence): `HIRED` > `SUBMITTED` > `APPLIED` > `INTERESTED` > `AWAITING_RESPONSE` > `LIKE` > `DECLINED` > `NOT_INTERESTED` = `EXPIRED`. `NOT_INTERESTED` is the **candidate** declining; `DECLINED` is the **customer** removing them from the pipeline — different actors, don't conflate them. "Any declines?" / "who said no?" means `NOT_INTERESTED` specifically. A candidate can hold more than one invite row for the same job (one per pool/doc-type) — if statuses conflict, the higher-precedence one is authoritative.

**Identity reveal:** a row's `consultantName`/`consultantEmail` are shown in full only when that specific row's `inviteStatus` is `INTERESTED` or higher (`INTERESTED`/`APPLIED`/`SUBMITTED`/`HIRED`), or the candidate is from an owned roster (Internal/Applicants). Below that, name comes back as initials and email as `null`. This check is currently **per row, not per candidate** (known quirk, WB-1484) — the same person can show full name on one row and initials on another for the same job if they hold rows in multiple pools. Applies to both the single-lookup answer and the Invite Status table: if you render two rows for what's clearly one person with different reveal states, say they hold multiple invite records for this role rather than presenting it as a contradiction.

## The flow
1. **Sending an invite.** When the customer says "invite [name]" from a ranking or lookup already on screen, resolve `consultantId` + `jobId` from that context — don't re-fetch if you already have them. If no ranking/lookup is in context to resolve them from, ask which role first rather than guessing. Call `invite_candidate`. On success, report the returned `inviteStatus` plainly: if it's `AWAITING_RESPONSE`, confirm *"Invite sent to [Name] — invite ID: `[inviteId]`."*; if it's already past that (e.g. `INTERESTED`), say the candidate already has a response on file rather than implying a fresh invite just went out. (Don't build messaging on `wasCreated` — it's currently unreliable, WB-1533.) If the call errors `NOT_FOUND` because the candidate isn't currently a persisted match for this job, say so plainly and suggest re-running `match_candidates` first — don't retry blindly. For any other error, surface what the tool actually returned rather than assuming `NOT_FOUND` is the only failure mode. If the candidate's name appears as initials (not yet revealed), warn that the invite will go to an unverified profile and confirm before sending.
2. **Checking one role's invites.** "show invite status for [job]" / "who have I invited to [job]?" → resolve `jobId`, call `list_job_invites` (paging through fully), present the Invite Status table (see Presentation). Offer to filter by status if the list is long.
3. **Looking someone up by name, anywhere.** "did [name] respond?" / "check on [name]" with no `jobId` in hand → call `list_company_invites`, paging through (`page`/`limit`) and matching the name case-insensitively (substring is fine) until found or all pages are exhausted. If exactly one match, report their `inviteStatus`, `jobTitle`, and `updatedAt`. If **more than one** distinct person or job matches (e.g. multiple people with that name, or the same person invited to several roles), list all the matches and ask which one before reporting a status. If nothing matches after a full page-through, say plainly you found no invite under that name — don't guess at a different tool.
4. **Company-wide pulse.** "who have I invited?" / "any declines?" / "who said no?" with no name → call `list_company_invites`, always passing `sortBy:"updatedAt"`, `sortOrder:"desc"` (for the full list too, not just filtered pulls) so results are newest-first; add `inviteStatus:"NOT_INTERESTED"` specifically for declines. Present as a table.
5. **The invite ≠ match distinction.** Invite tracking and match scoring are separate systems. A candidate can be `INTERESTED`/`SUBMITTED`/even `HIRED` and still have no `match_candidates` row — their documents may never have gone through the matcher, or they were matched at invite time and have since dropped out of a re-scored pool. If asked for a match score or comparison on someone who's invited/responded but absent from `match_candidates`, say exactly that, then point to **rank-shortlist** to re-run matching or **evaluate-talent** to upload their resume if it was never ingested — don't call those tools yourself, hand off; don't imply something is broken.

## Presentation
- **Invite Status table.** Columns: **Candidate · Job** (omit Job when scoped to one role) **· Status · Since**. Status badges: 🏆 Hired · ✅ Submitted · 📝 Applied · 🟢 Interested · ⌛ Awaiting Response · 👍 Liked · ✗ Declined · ⛔ Not Interested · ⏰ Expired. *Since* = `updatedAt`, rendered human-readably (e.g. "Jun 24, 2:15 PM"), most-recent-first.
- **Single lookup.** One line: name, status badge, job title, and how long ago (`updatedAt`). If multiple matches, list each on its own line before asking which.
- Never fabricate a status, timestamp, or count — everything comes from the tools.

## Constraints
- `list_company_invites` and `list_job_invites` have no server-side name filter — always page through fully before reporting "not found."
- `invite_candidate` only works on candidates already surfaced by `match_candidates` for that job — don't retry a `NOT_FOUND` with the same args; re-rank first if the customer wants to invite someone outside the current matched pool.
- Workerbee Network candidates are anonymized (initials only) until they connect; warn before inviting or confirming an action against an unverified identity.
- This skill sends and reads invites; it never scores or ranks candidates, and never calls `match_candidates`/`create_upload_session` itself — hand off to `rank-shortlist` / `evaluate-talent` for those.
