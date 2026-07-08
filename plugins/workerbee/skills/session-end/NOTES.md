# WB-1504 — Session boundary detection: approach & limitations

Spike deliverable for WB-1504 (parent WB-1503, Claude Plugin – Usage Analytics). Covers both
`session-start` and `session-end`. This write-up feeds the follow-up consent-seeker story.

## Mechanism

Both boundaries are detected purely through **skill-trigger intent matching** — the same mechanism
every other Workerbee skill uses (a skill's `description` is matched against the user's intent). No
hooks and no MCP calls are involved.

**Why intent-matching and not hooks:** the skills must run in *both* the Claude Code plugin and the
claude.ai skills upload. claude.ai has no hook system, so the only detection mechanism common to both
runtimes is the skill description. Claude Code *does* expose real `SessionStart` / `SessionEnd` / `Stop`
hooks, which would make end-detection far more reliable in the CLI — but that path is not portable to
claude.ai and is therefore out of scope for this spike. It's the obvious hardening step if end-detection
ever needs to be CLI-grade reliable (see limitations).

## Session start

- **Fires on:** the first Workerbee-flavored intent in a conversation — recruiter openers ("catch me
  up", "I need to hire a …", "rank them") or worker openers ("update my passport", "update my work
  experience").
- **Once per conversation:** re-firing is suppressed after the first mark; later Workerbee requests are
  mid-session, not new starts.
- **Reliability:** high. A session genuinely begins with a real user utterance, and that utterance is
  exactly what intent-matching is good at.

## Session end

- **Fires on:** a wrap-up signal *after* prior Workerbee activity — explicit sign-offs ("thanks, that's
  all", "we're done", "bye") or a standalone acknowledgement with no follow-on request once a task
  finished.
- **Reliability:** moderate — this is the hard, valuable case.

## Known false-positives / false-negatives

| Case | Type | Handling |
|---|---|---|
| User simply stops replying ("went quiet") | **False negative** | Unavoidable with a skill-only mechanism — silence can't match an intent. Would need a Claude Code `SessionEnd`/`Stop` hook (not portable, out of scope). This is the main gap. |
| Polite "thanks!" mid-session, before the next question | **False positive** | Require prior Workerbee activity + no pending next step before firing; keep the marker cheap so a misfire is harmless. |
| "thanks — and one more thing…" | **False positive** | Only fire when the acknowledgement stands alone with no follow-on request. |
| Session-start opener also triggers the task skill | Co-activation | Debug line is a lightweight prefix, never a blocker; task skill runs as normal. Runtime does not guarantee ordering. |
| No durable session id to correlate start↔end | Limitation | Epic decision D4, out of scope. "Once per conversation" is scoped to the current conversation only. |

## Spike behavior (temporary)

Each skill emits a single debug line (`[debug] session start detected` / `[debug] session end
detected`) and does nothing else — no consent, no persistence. Consent-seeker, session-saver MCP tool,
and GCS write are separate stories.

## Recommendation for the consent-seeker story

- Build the consent prompt off `session-end` — it's the intended trigger point.
- Accept that "went quiet" ends will be missed in claude.ai; if CLI-grade reliability is needed there,
  add a Claude-Code-only `SessionEnd`/`Stop` hook as an augmentation, keeping the skill as the portable
  baseline.
