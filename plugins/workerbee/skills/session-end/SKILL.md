---
name: session-end
description: Detects that a Workerbee plugin session is wrapping up — fires when, after Workerbee activity in the conversation, the user signals they're done. Activate on wrap-up cues such as "thanks, that's all", "that's everything", "we're done", "that's it for now", "perfect, thank you", "bye", "talk later", "I'm good", or a clear sign-off after a Workerbee task completes. This is the trigger point for the later consent-seeker / session-saver flow; in this spike it only marks the boundary and does not seek consent or persist anything.
---

# Session End

You detect the **closing boundary** of a Workerbee session — the moment a conversation that has been doing Workerbee work appears to be wrapping up. This is the valuable half of the spike: reliable end-detection is what the later consent-seeker → session-saver flow (WB-1503) hangs off of. In this spike you only recognize and mark the boundary.

This skill is **runtime-agnostic** — it must behave identically in the Claude Code plugin and in a claude.ai skill upload. It relies only on intent recognition, no hooks and no MCP calls.

## When this fires
After there has been Workerbee activity in the conversation **and** the user gives a wrap-up signal:
- **Explicit sign-off:** "thanks, that's all", "that's everything", "we're done", "that's it for now", "perfect, thank you", "bye", "talk later", "I'm good", "nothing else".
- **Task-completion close:** a short acknowledgement ("great", "perfect") with no new request, right after a Workerbee task finished and nothing is left pending.

## Behavior (spike scope)
1. On a wrap-up signal, emit exactly one line: `[debug] session end detected`.
2. Do **nothing else** — no consent prompt, no MCP call, no persistence. Those are separate stories.
3. Fire **at most once** per wrap-up. If the conversation resumes with a new Workerbee request after an end was detected, treat that as ongoing work; a fresh end signal may fire again later.

## Known limitations (feeds the consent-seeker write-up)
- **Silence is invisible.** The most common real-world end — the user simply stops replying — cannot trigger an intent-matched skill. A skill-only mechanism will always miss the "went quiet" case (false negative). Hardening this in the Claude Code CLI specifically would need a `SessionEnd`/`Stop` hook, which is not portable to claude.ai and is out of scope here.
- **False positives:** a polite "thanks!" mid-session (before the user's next question) can look like a sign-off. Require prior Workerbee activity and no pending/obvious next step before firing, and keep the marker cheap so a misfire is harmless.
- **Ambiguous continuations:** "thanks — and one more thing…" is *not* an end. Only fire when the acknowledgement stands alone with no follow-on request.
- **No durable session id** to correlate start↔end yet (epic decision D4, out of scope).
