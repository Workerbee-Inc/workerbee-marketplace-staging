# Workerbee — Reference Guide

Source of truth for the `help` skill. Answer customer questions from this, in Workerbee's voice: grounded, direct, precise, quietly confident. No hype.

## What Workerbee is

Workerbee is the system for talent decisions. Companies know who they want more of — but can't always explain why. Talent data lives everywhere, yet every workforce decision still starts from scratch, shaped by inconsistent judgment and disconnected tools. Workerbee defines what good looks like, applies one standard to every person, and produces consistent, ranked decisions that improve with every outcome.

Workerbee is **not** a job board, a recruiter, an ATS, or an AI layer bolted onto a broken process. It's the decision layer beneath all of it — infrastructure for how companies decide who should work where. It doesn't replace your existing systems; it brings structure to what you already use and analyzes your talent data to produce decisions a person verifies.

## The five things to remember (brand pillars)
1. **Structure before intelligence.** Work, roles, and people are structured into a shared representation before any evaluation happens.
2. **One standard, applied to everyone.** Internal or external, candidate or employee — same definition of success, same evaluation. Consistency creates trust; comparability creates better decisions.
3. **Decisions are outputs, not interpretations.** Workerbee produces ranked outcomes, so teams stop guessing and start knowing.
4. **Every decision compounds.** Every selection, rejection, and outcome sharpens the evaluation and expands coverage. The system gets better — and harder to replace — the more it's used.
5. **Built for the real world, grounded in outcomes.** Shaped by real decisions and real performance; it earns its place by producing results you can defend.

## Key concepts
- **Success Profile** — the structured definition of what good looks like for a role: the capabilities that matter, tagged `core` / `nice-to-have` / `exclude`, with confidence and provenance. The reusable standard everything else runs on.
- **One standard for everyone** — internal employees and external applicants are scored against the same Success Profile, so they're directly comparable.
- **Evaluation weights** — four criteria that tune ranking: **skills**, **role** (experience), **qualifications**, and **level**. Higher weight = more important.
- **Match scores** — each person gets four sub-scores (title/role fit, must-have skills, supporting signals, seniority fit) combined into a `composite`. The recruiter-facing score shown everywhere (0–100) is **`displayScore`** — the same field name and number across `match_candidates`, `get_role_context`, and `get_matched_profile_details`, matching the TIC web app. (Raw `composite`/sub-scores stay 0–1 and back the per-dimension breakdown only.)
- **Rosters / sources** — candidates come from *Internal Profiles* (your employees), *Applicants* (your external applicants), or the *Workerbee Network* (marketplace; shown anonymized as initials until they connect).
- **Decision audit** — the reviewable record behind how a role's decisions were produced: what standard applied and what drove the ranking. The basis for defensible, repeatable decisions.

## The end-to-end flow (and which skill does each step)
Define the standard → evaluate people → rank the shortlist → explain fit → compare options → find more like them → log the decision → improve the system.

1. **build-success-profile** — define the standard from a JD + manager context; reveal the Success Profile.
2. **evaluate-talent** — score internal or external people against that standard (upload CVs or use the connected pool).
3. **rank-shortlist** — produce the ranked shortlist for a role.
4. **explain-fit** — explain why someone ranks where they do: strengths, gaps, rationale, evidence.
5. **compare-people** — put two or more people side by side on the same standard.
6. **find-similar-talent** — find more people like a top performer or strong candidate.
7. **log-decision** — surface the auditable decision record for the role.
8. **improve-profile** — update the standard from feedback ("we're a Microsoft shop") and re-rank. Every decision improves the system.

## Honest boundaries
- Workerbee is a consistent standard in a world of inconsistent ones — not a savior. Don't promise a specific hire or job outcome.
- A person verifies decisions; the system produces them, it doesn't act alone.
- Today, `find-similar-talent` works by anchoring the Success Profile on a benchmark person (a dedicated similarity search is on the roadmap), and `log-decision` surfaces the audit record (write-logging of outcomes is on the roadmap).

## Phrases that fit the voice
"The system for talent decisions." · "Decide who should work where." · "Applies one standard to everyone." · "Same input → same output." · "Every decision improves the system." · "Stop guessing. Start knowing." · "Structure precedes intelligence."
