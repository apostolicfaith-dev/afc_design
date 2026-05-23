# The Global Voice — Camp Meeting Showcase Plan

> - Created: 2026-05-22
> - Author: Bro. Taiwoo Park (TP)
> - Status: Draft — For ITAC Review (May 23, 2026)
> - Related: [Integration Strategy](integration-strategy.md), [Hosting Strategy](hosting-strategy.md), [2026-04-10 Meeting Notes](meeting-2026-04-10.md)

---

## Purpose

This document defines what The Global Voice (TGV) will deliver by Camp Meeting (July 2026), how we get there, and what the committee needs to decide at the May 23 meeting.

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
| Japanese | Pastor from Japan | Positive (reported ~90% satisfaction, based on limited review) |
| Korean | TP (native speaker) | Positive initial impression, further testing needed |
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

**Objective:** Target Japanese and Korean for limited beta by Camp Meeting, with a live demo showcasing the pipeline to world leaders.

**Why Japanese and Korean first:**

| Factor | Japanese | Korean |
|---|---|---|
| Translation team exists? | No — high value-add from AI | No — high value-add from AI |
| Early feedback | Pastor from Japan (~90%, limited review) | TP (native speaker, initial review) |
| Asia strategy alignment | Yes (Bro. Bill McKibben engagement) | Yes |
| Further testing needed | Yes — shadow period + expanded review | Yes — shadow period + expanded review |

**What "limited beta" means:**
- Site accessible to a small group of church members in Japan and Korea
- Daily and Daybreak devotionals auto-translated with TTS audio
- "Beta Translation" label on all translated content
- Feedback link visible — readers can flag issues
- Rollback plan in place (revert to English-only if critical issues arise)

**Camp Meeting showcase:**
- Live demo: enter English devotional → translate + generate audio
- Show Japanese and Korean pages with audio playback
- Present feedback dashboard — how reader corrections improve the system
- Invite world leaders to express interest for their languages

---

## Phase 1 Language Expansion Plan

Based on SG's direction (April 29 ad-hoc), Phase 1 targets languages covering ~73–80% of global church membership.

| Tier | Language | Target | Editorial Review | TTS | Notes |
|---|---|---|---|---|---|
| 1 | Japanese | Camp Meeting (July 2026) | TP + Japanese pastor | Cloud TTS Neural2 | Positive early feedback |
| 1 | Korean | Camp Meeting (July 2026) | TP (native speaker) | Cloud TTS Neural2 | Initial review done, more testing needed |
| 2 | Spanish | End of 2026 | Regional team (TBD, per SG direction) | Cloud TTS Neural2 | Sister Katie coordinating |
| 2 | French | End of 2026 | Regional team (TBD, per SG direction) | Cloud TTS Neural2 | West Africa + France contacts needed |
| 2 | Portuguese | End of 2026 | Regional team (TBD, per SG direction) | Cloud TTS Neural2 | Brazil/Portugal contacts needed |

> **Note on Yoruba:** Yoruba is a high-resource language with existing translation teams in the church. AI-assisted translation adds less value here compared to low-resource languages. Yoruba is not prioritized in Phase 1 but can be revisited based on editorial team interest.

**Editorial Review Model (per SG direction):**
- Regional editorial teams review translations — not centralized HQ
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
| **ITAC Review** | May 23 | Committee reviews this plan; approves beta languages and editorial model | Alignment on scope and approach |
| **Shadow** | May 26 – Jun 6 | Process 2 weeks of devotionals through full pipeline; TP reviews Korean daily; request Japanese pastor to review Japanese; stabilize content ingestion; prepare TTS samples | Team confident in output quality |
| **Dogfood** | Jun 9 – Jun 20 | Share with Sister Katie + Publishing Team; share with 2–3 bilingual ministers per language; collect feedback via TGV UI; incorporate into translation prompts | Language reviewers comfortable with quality |
| **Beta Prep** | Jun 23 – Jul | Deploy beta (accessible URL, "Beta Translation" label); prepare demo script and presentation; set up email subscription if feasible; document rollback procedure | Ready for camp meeting |
| **Camp Meeting** | July | Live demo to world leaders; collect interest from other language groups; gather feedback for Tier 2 prioritization | — |

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
| Translation quality not sufficient for beta | Medium | "Beta Translation" label, feedback UI, rollback plan. Japanese pastor's early feedback is encouraging but more testing is needed. |
| Content ingestion unreliable | Low | Email ingestion tested and working; RSS as backup; manual entry always available. Shadow period will stress-test. |
| Editorial reviewers not available for Tier 1 | Low | Tier 1 (ja, ko) relies on TP and Japanese pastor — no external editorial team needed. |
| Editorial teams not available for Tier 2 | Medium | Depends on SG's follow-up with regional contacts. Tier 2 timeline (end of 2026) provides buffer. |
| Camp meeting timeline too tight | Medium | Tier 1 scope is intentionally small (2 languages with early positive feedback). If issues arise, beta can start with Japanese only. |

---

## Decisions Needed (May 23 Meeting)

1. **Approve Japanese + Korean as Tier 1 beta candidates** — both have received positive early feedback; shadow phase would confirm readiness
2. **Confirm regional editorial team model** — per SG's direction from April 29
3. **Approve camp meeting showcase approach** — live demo + invite world leaders to express interest
4. **GCP nonprofit application** — TP to lead; need HQ organization details (EIN, etc.)

---

## Reference

- Live site: https://afc-dev.org
- Repository: `apostolicfaith-dev/the-global-voice`
- Integration Strategy: [integration-strategy.md](integration-strategy.md)
- Design docs site: https://apostolicfaith-dev.github.io/afc_design/
