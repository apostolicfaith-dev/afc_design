# The Global Voice — Camp Meeting Showcase Plan

> - Created: 2026-05-22
> - Author: Bro. Taiwoo Park (TP)
> - Status: Draft — For ITAC Review (May 23, 2026)
> - Related: [Integration Strategy](integration-strategy.md), [Hosting Strategy](hosting-strategy.md), [2026-04-10 Meeting Notes](meeting-2026-04-10.md)

---

## Executive Summary

The Global Voice (TGV) is a deployed multilingual platform that auto-translates AFC devotional content and generates text-to-speech audio. It currently supports 11 languages with 9 TTS voices, running on GCP Cloud Run at ~$50/mo.

**Camp Meeting plan:**

| | Detail |
|---|---|
| **Tier 1 beta** | Japanese — positive early feedback from Japan pastor (~90% satisfaction) |
| **Tier 2 beta** | Korean, Filipino — church leaders contacted, coordination with existing voluntary translation teams in progress (summer schedules delaying response; reminders going out within days) |
| **Tier 3** | Spanish, French, Portuguese — dependent on SG connecting regional editorial teams |
| **Camp Meeting showcase** | Live demo is ready regardless of beta timeline — the platform is deployed and functional at afc-dev.org |

**Key context on timeline:** Each country and region has its own schedule and capacity constraints (summer conferences, camp meetings, etc.). The formal Shadow → Dogfood → Beta phases may be condensed or adjusted per region as coordination with local leaders progresses. This is expected — the system is ready, and the pace is driven by people, not technology. The demo site is live and can be showcased at Camp Meeting independent of the beta rollout timeline.

**Decisions needed May 23:** Approve tier structure and language targets, confirm editorial review model (regional teams per SG), approve camp meeting showcase plan, authorize GCP nonprofit credit application.

---

## Purpose

This document defines what TGV will deliver by Camp Meeting (July 2026), how we get there, and what the committee needs to decide at the May 23 meeting.

---

## Current State (as of May 2026)

TGV is a multilingual content platform deployed on Google Cloud Run.

| Area | Capability | Status |
|---|---|---|
| **Translation** | 11 languages (en, es, fr, ko, ja, yo, ro, nb, ak, fil, vi) | Deployed |
| | AI translation via Gemini 2.5-Flash with structured output | Deployed |
| | AI revision pass for fluency and natural tone | Deployed |
| | Theological glossary (consistent doctrinal terms across languages) | Deployed |
| | Bible verse auto-lookup in official translations per language | Deployed |
| **TTS Audio** | 9 languages with audio (en, es, fr, ko, ja, ro, nb, fil, vi) | Deployed |
| | Google Cloud TTS Neural2/WaveNet at 0.8x speaking rate | Deployed |
| | Automated: translation → TTS → audio upload to S3 | Deployed |
| **Content Pipeline** | Task queue with retry, dead-letter handling, stuck recovery | Deployed |
| | Content ingestion from email (daily cron) | Deployed |
| | RSS ingestion route | Built, pending feed testing |
| | Change detection → re-translate → re-generate audio | Deployed |
| **Admin & Review** | Dashboard: queue monitor, translation editor, settings | Deployed |
| | Volunteer review UI: side-by-side comparison with inline editing | Deployed |
| | Role-based access (Admin, Editor, Volunteer, Viewer) | Deployed |
| | Public feedback form per devotional per language | Deployed |

**Early Quality Feedback**

| Language | Reviewer | Feedback |
|---|---|---|
| Japanese | Pastor from Japan | Positive (~90% satisfaction, based on limited review) |
| Korean | TP (native speaker); coordination with Korean church leaders in progress | Positive initial impression, further testing needed |
| Spanish | Sister Katie | Shared for distribution to pastors, feedback pending |

**Infrastructure**

| Component | Detail |
|---|---|
| Hosting | Google Cloud Run (us-west1), auto-scaling 0–3 instances |
| Database | GCP Cloud SQL (PostgreSQL) |
| Audio Storage | AWS S3 |
| Monthly Cost | ~$50/mo for 11 languages (~$3–4 per language for AI translation + TTS; remainder is fixed hosting/DB) |

---

## Camp Meeting Goal

**Objective:** Target Japanese for limited beta by Camp Meeting. Korean and Filipino to follow as coordination with local leaders and translation teams progresses. Live demo of the full pipeline at Camp Meeting regardless of beta status.

**What "limited beta" means:**
- Site accessible to a small group of church members in the target language
- Daily and Daybreak devotionals auto-translated with TTS audio
- "Beta Translation" label on all translated content
- Feedback link visible — readers can flag issues
- Rollback plan in place (revert to English-only if critical issues arise)

**Camp Meeting showcase:**
- Live demo: enter English devotional → translate + generate audio
- Show working pages with audio playback
- Present feedback dashboard — how reader corrections improve the system
- Invite world leaders to express interest for their languages

> **Note:** The demo site (afc-dev.org) is live and operational. The showcase does not depend on completing the beta rollout — it demonstrates the system's capabilities directly.

---

## Language Tier Plan

| Tier | Language | Target | Review / Coordination | TTS | Status |
|---|---|---|---|---|---|
| 1 | Japanese | Camp Meeting (July 2026) | Japan pastor (positive early feedback) | Cloud TTS Neural2 | Most advanced; initial feedback received |
| 2 | Korean | Post-Camp Meeting | Korean church leaders + voluntary translation team (reported) | Cloud TTS Neural2 | Leaders contacted; coordinating (summer schedules causing delay; reminders within days) |
| 2 | Filipino | Post-Camp Meeting | Filipino church leaders + voluntary translation team (reported) | Cloud TTS Neural2 | Leaders contacted; coordinating (same as Korean) |
| 3 | Spanish | 2026 H2 | Regional editorial team (TBD, per SG direction) | Cloud TTS Neural2 | Depends on SG follow-up with regional contacts |
| 3 | French | 2026 H2 | Regional editorial team (TBD, per SG direction) | Cloud TTS Neural2 | Depends on SG follow-up with regional contacts |
| 3 | Portuguese | 2026 H2 | Regional editorial team (TBD, per SG direction) | Cloud TTS Neural2 | Depends on SG follow-up with regional contacts |

**Tier rationale:**
- **Tier 1 (Japanese):** Most concrete feedback to date. Pastor reviewed and reported positive feedback.
- **Tier 2 (Korean, Filipino):** Both are reported to have voluntary translation teams in-country. AI translation can augment these teams — handling the initial draft so translators focus on review and refinement rather than translating from scratch. Leaders are contacted; timeline depends on their availability.
- **Tier 3 (Spanish, French, Portuguese):** Editorial team coordination depends on SG's follow-up with regional contacts. This is an external dependency outside ITAC's direct control.

> **Note on Yoruba:** Yoruba has established translation teams within the church. AI-assisted translation adds less incremental value here. Not prioritized in Phase 1 but can be revisited based on editorial team interest.

**Editorial Review Model (per SG direction):**
- Regional editorial teams and existing voluntary translation teams review AI translations — not centralized HQ
- AI generates the initial draft; human reviewers refine and correct
- Reviewers provide feedback in natural language (style, doctrine, culture)
- Feedback stored per language and applied to future translations
- System improves cumulatively — reviews should get lighter over time
- TGV volunteer review UI supports this workflow

---

## TTS Provider Comparison

| Provider | Cost (per 1M chars) | Quality vs. Cloud TTS | Multi-language | Status |
|---|---|---|---|---|
| Google Cloud TTS Neural2 | ~$16 | Baseline (natural, clear) | 9 languages | **Current provider** |
| Google Cloud TTS WaveNet | ~$16 | Comparable | Used for ro, nb | Active |
| ElevenLabs | ~$165 | Better (more natural) | Limited languages | Evaluated, cost prohibitive (~10x) |
| Gemini TTS | Free (API) | Comparable | Experimental | Tested, potential backup |
| Facebook MMS | Free (open-source) | Lower | Yoruba only | Disabled (quality issues) |

**Recommendation:** Continue with Google Cloud TTS Neural2. Quality is reasonable across supported languages at ~10% of ElevenLabs cost. Audio samples available for committee review upon request.

---

## Timeline

| Phase | Dates | Key Activities | Gate |
|---|---|---|---|
| **ITAC Review** | May 23 | Committee reviews this plan; approves tier structure and editorial model | Alignment on scope and approach |
| **Shadow** | May 26 – Jun 6 | Process devotionals through full pipeline for Tier 1 language; coordinate with Korean and Filipino leaders on review engagement | Tier 1 quality acceptable |
| **Dogfood** | Jun 9 – Jun 20 | Share with bilingual ministers for feedback; incorporate corrections into translation prompts | Reviewers comfortable with quality |
| **Beta Prep** | Jun 23 – Jul | Deploy beta with "Beta Translation" label; prepare demo script and materials; document rollback procedure | Ready for camp meeting |
| **Camp Meeting** | July | Live demo to world leaders; collect interest from other language groups; gather feedback for Tier 2/3 | — |

> **Timeline flexibility:** Each region has its own schedule constraints (summer conferences, camp meetings, local church events). The Shadow → Dogfood → Beta phases may be condensed or adjusted as coordination with local leaders progresses. Tier 2 languages (Korean, Filipino) will enter the pipeline as their respective leaders and translation teams become available — this may extend beyond Camp Meeting. The system is ready; the pace is set by people, not technology.

---

## GCP Infrastructure

TP has been nominated as GCP champion (May 2 meeting).

| Item | Status |
|---|---|
| TGV on Cloud Run (us-west1) | Running |
| Database on Cloud SQL | Migrated (April 2026) |
| GCP for Nonprofits application ($10K/yr credit) | To be initiated |
| Consolidate ITAC projects under single GCP account | Planned |

**Action:** TP to initiate GCP for Nonprofits application. Requirements: church EIN, organization details (same documents as Twilio registration). Coordinate with FO/SF.

---

## Budget

| Item | Monthly Cost | Notes |
|---|---|---|
| Google Cloud Run | $0–20 | Covered by nonprofit credits |
| GCP Cloud SQL | $0–15 | Covered by nonprofit credits |
| Google Cloud TTS | ~$8 | ~$3–4 per language (currently 9 TTS languages) |
| Gemini AI translation | <$5 | Scales with language count |
| AWS S3 audio storage | <$2 | Grows slowly |
| **Total (current, 11 languages)** | **~$50/mo** | Based on actual spend |
| **Total (with nonprofit credits)** | **~$15/mo** | Hosting/DB covered by credits |

Per-language variable cost (translation + TTS): approximately **$3–4/mo per language**. Reducing to 5 languages would bring the variable portion to ~$15–20/mo.

---

## Risks and Mitigations

| Risk | Likelihood | Mitigation |
|---|---|---|
| Translation quality not sufficient for beta | Medium | "Beta Translation" label, feedback UI, rollback plan. Early feedback from Japan pastor is encouraging but more testing needed. |
| Content ingestion unreliable | Low | Email ingestion tested and working; RSS as backup; manual entry always available. Shadow period will stress-test. |
| Tier 2 leader coordination delayed | Medium | Summer schedules are causing delays. Reminders going out within days. Tier 2 timeline is flexible — system is ready when leaders are. |
| Editorial teams not available for Tier 3 | Medium | Depends on SG's follow-up with regional contacts. Tier 3 timeline (2026 H2) provides buffer. |
| Camp meeting showcase not ready | Low | Demo site is live and functional today. Showcase does not depend on beta rollout completion. |

---

## Decisions Needed (May 23 Meeting)

1. **Approve language tier structure** — Tier 1: Japanese, Tier 2: Korean + Filipino, Tier 3: Spanish + French + Portuguese
2. **Confirm editorial review model** — regional teams and existing voluntary translation teams, per SG's direction from April 29
3. **Approve camp meeting showcase approach** — live demo of deployed system + invite world leaders to express interest
4. **GCP nonprofit application** — TP to lead; need HQ organization details (EIN, etc.)

---

## Reference

- Live site: https://afc-dev.org
- Repository: `apostolicfaith-dev/the-global-voice`
- Integration Strategy: [integration-strategy.md](integration-strategy.md)
- Design docs site: https://apostolicfaith-dev.github.io/afc_design/
