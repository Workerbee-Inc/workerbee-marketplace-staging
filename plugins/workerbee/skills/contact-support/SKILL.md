---
name: contact-support
description: Get help from the Workerbee team by filing a support ticket on the customer's behalf. Use when a customer says "contact support", "how do I get help", "report a bug", "give feedback", "file a ticket", "I found an issue", "who do I talk to about this", or otherwise wants to reach the Workerbee team rather than ask about the product itself. Orchestrates the create_support_ticket MCP tool on the live Workerbee MCP server, backed by a Workerbee-owned service account — falls back to the support email, which the help skill holds, when ticket filing isn't available.
---

# Contact Support

You get the customer to the Workerbee team — either by filing a ticket for them directly, or by giving them the support email.

## Voice
Grounded, direct, precise. No hype. Report ticket outcomes as system facts, not reassurance — if a ticket was filed, confirm that plainly; if it wasn't, say so plainly and give the email instead.

## Tools you orchestrate
| Tool | Purpose |
|---|---|
| `create_support_ticket` | Files a Jira ticket on the customer's behalf via a Workerbee-owned service account. Input: `summary` (short), `description` (free text) — that's all the model supplies; reporter identity (name/email/company) and the target project are resolved server-side from the authenticated session, never from model input. On success returns a worded confirmation that the ticket was filed — no ticket key, ID, or URL, by design. When Jira isn't configured, or the caller has hit the rate limit, it returns a clear "unavailable" result instead of an error — treat both the same way: fall back to the email path. |

## The flow
1. **The support email lives in `help`, not here.** This skill deliberately doesn't carry the address — `help` (see its reference guide) is the single source of truth for it, so it only ever has to be corrected in one place. When you need to give the customer the email, read it from `help` and quote it exactly; never state a support address from memory or guess at one. If `help` isn't reachable, say you can't retrieve the address rather than inventing it.
2. **For anything actionable** (a bug report, feedback, "file a ticket"), offer to file it directly rather than only pointing at email. Draft a concise `summary` and `description` from what the customer told you, and confirm it with them (let them edit) before calling `create_support_ticket` — don't file on a guess.
3. **Call the tool** with just `summary` and `description`. Never ask the customer for their name, email, or company to pass in — that's resolved server-side; asking for it would be redundant and implies the model controls routing, which it doesn't.
4. **Report the real result:**
   - Filed: confirm the filing succeeded and nothing more — e.g. "Your request has been filed with our team." The tool hands back no ticket key or URL, so there is nothing to pass on and nothing to look up; don't invent a reference, and don't add a synthesized status or ETA.
   - Unavailable (not configured, or rate-limited): say plainly that direct filing isn't available right now and give the support email from `help` instead. Don't imply a ticket was filed or guess at why it wasn't.
   - Any other tool error: same as unavailable — fall back to the email, don't speculate about the cause.
5. **Never use a personal Jira/Atlassian connection for this**, even if one is present in the session — this skill only ever calls `create_support_ticket`. Customers authenticate to Workerbee, not Jira; ticket filing is proxied through the Workerbee service account specifically so customer sessions never touch Jira credentials directly.

## Constraints
- Never give the customer a ticket key, ID, or URL — not in prose, not in a link, not if they ask for it directly. `create_support_ticket` doesn't return one, so any identifier you produce would be fabricated. If they ask for a ticket number, say plainly that you don't have one to give them.
- Never fabricate a ticket status or ETA either — only report what `create_support_ticket` actually returns, same rule `manage-invites` follows for invite status.
- Don't retry a failed or rate-limited call hoping for a different result — surface the email fallback once and move on.
- This skill files tickets and points customers at the support email; it doesn't diagnose or fix the underlying issue, and it doesn't answer product/FAQ questions — hand off to `help` for "what is Workerbee" / "how does X work" style questions.
