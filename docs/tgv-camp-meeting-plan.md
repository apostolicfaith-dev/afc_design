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

TGV is a working multilingual content platform deployed on Google Cloud Run. The following capabilities are **live and operational** — not planned, not prototyped.

**Translation Pipeline**
- 11 languages active: English, Spanish, French, Korean, Japanese, Yoruba, Romanian, Norwegian, Twi, Filipino, Vietnamese
- AI translation via Google Gemini 2.5-Flash with structured output
- AI revision pass for fluency and natural tone
- Theological glossary integration (consistent doctrinal terminology across languages)
- Bible verse auto-lookup in official translations per language

**Text-to-Speech (TTS)**
- 9 languages with TTS audio: en, es, fr, ko, ja, ro, nb, fil, vi
- Google Cloud TTS Neural2/WaveNet voices at 0.8x speaking rate
- Automated: translation completes → TTS generates → audio uploaded to S3

**Content Pipeline**
- Task queue with automatic retry, dead-letter handling, and stuck recovery
- Content ingestion from email (daily cron) — operational
- RSS ingestion route — built, pending feed reliability confirmation
- Change detection: content update → re-translate → re-generate audio

**Admin & Review**
- Admin dashboard: queue monitor, translation editor, system settings
- Volunteer review UI: side-by-side comparison with inline editing
- Role-based access: Admin, Editor, Volunteer, Viewer
- Public feedback form per devotional per language

**Quality Validation**
- Japanese: reviewed by pastor from Japan, reported 90%+ satisfaction
- Korean: reviewed by TP (native speaker), quality confirmed
- Spanish: shared with Sister Katie for distribution to Spanish-speaking pastors (feedback pending)
- Yoruba: translation functional, TTS disabled (open-source model quality insufficient)

**Infrastructure**
- Deployed on Google Cloud Run (us-west1), auto-scaling 0-3 instances
- PostgreSQL database on GCP Cloud SQL
- Audio files on AWS S3
- Monthly operational cost: ~$35-55

---

## Camp Meeting Goal

**Objective:** Launch Japanese and Korean public beta by Camp Meeting, with a live demo showcasing the full pipeline to world leaders.

**Why Japanese and Korean first:**
- Both are "low-resource" languages — no existing translation team, highest value-add from AI
- Japanese: already validated by the pastor from Japan (90%+ satisfaction)
- Korean: validated by TP (native speaker and developer)
- Both represent the Asia strategy TP discussed with Bro. Bill McKibben
- Success here demonstrates the model for expanding to other low-resource languages

**What "public beta" means:**
- Live site accessible to church members in Japan and Korea
- Daily and Daybreak devotionals auto-translated with TTS audio
- "Beta Translation" label on all translated content
- Feedback link visible — readers can flag issues
- Rollback plan ready (revert to English-only if critical issues arise)

**Camp Meeting showcase:**
- Live demo: enter English devotional → watch it translate + generate audio in real time
- Show Japanese and Korean pages with audio playback
- Present feedback dashboard — how reader corrections improve the system
- Invite world leaders to nominate their languages for the next phase

---

## Phase 1 Language Expansion Plan

Based on SG's direction (April 29 ad-hoc meeting), Phase 1 targets 6 languages covering ~73-80% of global church membership.

**Tier 1 — Beta at Camp Meeting (July 2026)**
- Japanese — validated, ready
- Korean — validated, ready

**Tier 2 — Beta by end of 2026**
- Spanish — Sister Katie + regional editorial team (DR, NY, Spain)
- French — regional editorial team (West Africa, France)
- Yoruba — editorial team (Lagos/WECA), TTS provider upgrade needed
- Portuguese — regional editorial team (Brazil, Portugal)

**Editorial Review Model (per SG direction):**
- Regional editorial teams review translations, not centralized HQ
- Reviewers provide feedback in natural language (style, doctrine, culture)
- Feedback stored per language and auto-injected into future translations
- System learns cumulatively — reviews get lighter over time
- TGV volunteer review UI already supports this workflow

---

## TTS Quality Assessment

| Provider | Cost (per 1M chars) | English Quality | Multi-language | Status |
|---|---|---|---|---|
| Google Cloud TTS Neural2 | ~$16 | Good (natural, clear) | 9 languages active | **Current provider** |
| Google Cloud TTS WaveNet | ~$16 | Good | Used for ro, nb | Active |
| ElevenLabs | ~$165 | Excellent (most natural) | Limited languages | Evaluated, cost prohibitive |
| Gemini TTS | Free (API) | Good | Experimental | Tested, viable backup |
| Facebook MMS | Free (open-source) | N/A | Yoruba only | Disabled (quality issues) |

**Recommendation:** Continue with Google Cloud TTS Neural2 as the primary provider. The quality is strong across all supported languages, and the cost is ~10% of ElevenLabs. Audio samples are available for committee review upon request.

**Open item:** Yoruba TTS requires either a paid provider (Cloud TTS doesn't support Yoruba) or waiting for open-source model improvements.

---

## Timeline

### Now → May 23 (ITAC Meeting)

- [x] Draft this plan document
- [ ] Committee reviews and approves beta language selection (Japanese + Korean)
- [ ] Committee confirms editorial review model (regional teams per SG direction)

### May 26 → June 6 — Shadow (Internal Validation)

- [ ] Process 2 weeks of daily devotionals through full pipeline (all languages)
- [ ] TP validates Korean output daily
- [ ] Request Japanese pastor to validate Japanese output daily
- [ ] Stabilize content ingestion (email/RSS) for unattended daily operation
- [ ] Fix any pipeline issues discovered during shadow period
- [ ] Prepare TTS audio samples for committee review

### June 9 → June 20 — Dogfood (Expanded Review)

- [ ] Share with Sister Katie + Publishing Team for side-by-side comparison
- [ ] Share with 2-3 bilingual ministers per beta language
- [ ] Collect feedback via TGV feedback UI
- [ ] Incorporate feedback into translation prompts
- [ ] Gate: Publishing Team and language reviewers sign off on quality

### June 23 → July — Beta Launch + Camp Meeting Prep

- [ ] Deploy public beta (accessible URL, "Beta Translation" label)
- [ ] Prepare camp meeting demo script and presentation materials
- [ ] Set up email subscription for beta languages (if feasible)
- [ ] Document rollback procedure

### July — Camp Meeting

- [ ] Live demo to world leaders
- [ ] Collect interest from other language groups
- [ ] Gather feedback to prioritize Tier 2 languages

---

## GCP Infrastructure

TP has been nominated as GCP champion (May 2 meeting). Current status:

- TGV is already running on GCP Cloud Run (us-west1)
- Database migrated from Neon to GCP Cloud SQL (April 2026)
- Nonprofit credit application ($10K/yr) — **to be initiated**
- Goal: consolidate all ITAC initiatives under a single GCP nonprofit account

**Action:** TP to initiate GCP for Nonprofits application. Requirements: church EIN, organization details (same as Twilio registration). Coordinate with FO/SF who are already gathering these for Twilio.

---

## Budget (Phase 1 Monthly)

| Item | Cost |
|---|---|
| Google Cloud Run hosting | $0-20 (nonprofit credits) |
| GCP Cloud SQL (PostgreSQL) | $0-15 (nonprofit credits) |
| Google Cloud TTS | ~$8 |
| Gemini AI translation | <$5 |
| AWS S3 audio storage | <$2 |
| **Total** | **~$30-50/mo** |

With GCP nonprofit credits ($10K/yr), hosting and database costs drop to $0. Effective cost: ~$15/mo for AI and storage services.

---

## Risks and Mitigations

**Translation quality concerns**
- Mitigation: "Beta Translation" label, feedback UI, regional editorial review, rollback plan
- Evidence: Japanese pastor already validated quality at 90%+

**Content ingestion reliability**
- Mitigation: email ingestion is proven; RSS is backup; manual entry always available
- Shadow period will stress-test automated ingestion

**Editorial team availability**
- Mitigation: Tier 1 (ja, ko) doesn't need external editorial teams — TP validates Korean, Japanese pastor validates Japanese
- Tier 2 depends on SG's follow-up with regional contacts

**Camp meeting timeline pressure**
- Mitigation: Tier 1 scope is intentionally small (2 languages already validated)
- If shadow/dogfood reveals issues, beta can launch with Japanese only

---

## Decisions Needed (May 23 Meeting)

1. **Approve Japanese + Korean as Tier 1 beta languages** — both are validated, ready for shadow phase
2. **Confirm regional editorial team model** — per SG's direction from April 29
3. **Approve camp meeting showcase plan** — live demo + world leader engagement
4. **GCP nonprofit application** — TP to lead; need HQ organization details (EIN, etc.)

---

## Reference

- Live site: https://afc-dev.org
- Repository: `apostolicfaith-dev/the-global-voice`
- Integration Strategy: [integration-strategy.md](integration-strategy.md)
- Design docs site: https://apostolicfaith-dev.github.io/afc_design/
