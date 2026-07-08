---
name: session-start
description: Detects the beginning of a Workerbee plugin session — fires on the first Workerbee-flavored request in a conversation, whether from a recruiter (customer) or a worker (CareerBee). Activate when a conversation's first Workerbee intent appears: a recruiter says "catch me up", "what's new?", "I need to hire a …", "build a success profile", "rank them", "show me my jobs", or otherwise opens Workerbee cold; or a worker says "update my passport", "align my skills", "update my work experience", or otherwise starts a Workerbee task. Marks the leading edge of the session for later usage-analytics work; it does not do the requested task itself.
---

# Session Start

You detect the **opening boundary** of a Workerbee session — the moment a conversation first turns into Workerbee work. This is instrumentation for the usage-analytics epic (WB-1503), not a task skill. You recognize the boundary, mark it once, and get out of the way so the real skill can run.

This skill is **runtime-agnostic** — it must behave identically in the Claude Code plugin and in a claude.ai skill upload. It relies only on intent recognition, no hooks and no MCP calls.

## When this fires
The **first** time in a conversation that a Workerbee intent appears — the leading edge of any other Workerbee skill's territory. Typical openers:
- **Recruiter / customer:** "catch me up", "what's new?", "where are things?", "show me my jobs/roles", "I need to hire a …", "build a success profile", "rank them", "who are the top candidates?", "compare X and Y", "why is she ranked #1?".
- **Worker / CareerBee:** "update my passport", "align my skills", "update my work experience", "add that I worked at …".

## Behavior (spike scope)
1. On the first Workerbee intent, emit exactly one line: `[debug] session start detected`.
2. Then **stop and hand off** — do nothing else. The user's actual request is handled by the appropriate Workerbee skill (`catch-up`, `build-success-profile`, `update-work-experience`, …), which runs as normal.
3. Fire **at most once per conversation.** If the session-start marker has already been emitted in this conversation, do not emit it again — subsequent Workerbee requests are mid-session, not new starts.

Consent, persistence, and the session-saver handoff are **out of scope** (separate stories). Do not prompt for consent or write anything anywhere yet.

## Known limitations
- **Co-activation:** the same opener that starts the session also triggers the real task skill. Order isn't guaranteed by the runtime — treat the debug line as a lightweight prefix, never as a blocker for the task.
- **False negatives:** a session that opens with pure small-talk ("hey") before any Workerbee intent won't be marked until the first real Workerbee request — which is the intended behavior.
- **No cross-conversation memory:** "once per conversation" is scoped to the current conversation only; there's no durable session id yet (epic decision D4, out of scope).
