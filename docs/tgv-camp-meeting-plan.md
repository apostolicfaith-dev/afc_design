# The Global Voice — Camp Meeting Showcase Plan

> - Created: 2026-05-22
> - Author: Bro. Taiwoo Park (TP)
> - Status: Living Document — Updated post May 23 ITAC meeting and May 25 HQ visit
> - Related: [Integration Strategy](integration-strategy.md), [Hosting Strategy](hosting-strategy.md), [2026-04-10 Meeting Notes](meeting-2026-04-10.md)

---

## Revision Log

| Date | Update |
|---|---|
| 2026-05-22 | Initial draft for May 23 ITAC review |
| 2026-05-23 | ITAC meeting: tier structure approved; leader names confirmed; project naming discussion; English TTS quality noted |
| 2026-05-25 | Portland HQ visit (Sis. Catey Hinkle): tier alignment confirmed; pre-camp announcement direction agreed; mobile app modality discussion; Swahili noted; active feedback cycle gap identified |
| 2026-05-25 | Portland HQ visit (Sis. Rodica Musgrave): positive alignment on agentic church website migration approach |
| 2026-06-06 | Update feedback from Japan (Bro. Yoshiki), Korea (Bro. Jeongmin), and Philippines (Bro. Rocky); updated action items and Tagalog TTS plan |

---

## Executive Summary

The Global Voice (TGV) — working name, branding TBD — is a deployed multilingual platform that auto-translates AFC devotional content and generates text-to-speech audio. It currently supports 11 languages with 9 TTS voices, running on GCP Cloud Run at $50/mo.

**Camp Meeting plan:**

| | Detail |
|---|---|
| **Tier 1 beta** | Japanese (Bro. Yoshiki) — positive early feedback; congregation already using beta site; interest in translating additional materials |
| **Tier 2 beta** | Korean (Bro. Jeongmin), Filipino (Bro. Rocky) — leaders contacted, coordinating; currently travelling for camp meeting / regional activities |
| **Tier 2 candidate** | Swahili — no existing translation team; under consideration alongside Korean and Filipino |
| **Tier 3** | Spanish, French, Portuguese — dependent on Bro. Sola Adesope (SG) connecting regional editorial teams |
| **Camp Meeting showcase** | afc-dev.org demo is ready regardless of beta timeline; pre-camp announcement to relevant regional leadership recommended before public presentation |

**Key context on timeline:** Each country and region has its own schedule and capacity constraints. The formal Shadow → Dogfood → Beta phases may be condensed or adjusted per region. The system is ready; the pace is driven by people, not technology.

**Status as of May 25:** Tier 1/2 structure aligned with Sis. Catey Hinkle (Publishing Team Lead, Portland HQ). Pre-camp regional announcement approach agreed. Mobile app as delivery platform under review. Active translation feedback cycle not yet running — to be added.

---

## Purpose

This document defines what TGV will deliver by Camp Meeting (July 2026), how we get there, and the open decisions and action items that follow.

---

## Current State (as of May 2026)

TGV is a multilingual content platform deployed on Google Cloud Run. The demo site at afc-dev.org has been live and running continuously for approximately six weeks, automatically ingesting daily devotionals from headquarters each morning.

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
| **Known Gap** | Active translation feedback cycle (reader corrections → prompt updates) | Not yet implemented — planned |

**Early Quality Feedback**

| Language | Key Contact | Feedback |
|---|---|---|
| Japanese | Bro. Yoshiki (Japan pastor) | Very helpful (~90% satisfaction); shared via LINE; priority: doctrine & three deep experiences; requested mobile app integration (AFC app) for elderly accessibility; interest in Bible study, textbooks, and translated sermon audio |
| Korean | Bro. Jeongmin; TP (native speaker) | Overall satisfied; confirmed willingness for continued collaboration |
| Filipino | Bro. Rocky | Translation quality satisfactory; TTS audio has awkward intonation/accents in some parts (serious issue as it changes word meanings); needs testing of alternative TTS options keeping costs low |
| Spanish | Sis. Catey Hinkle (coordinating) | Shared for distribution to pastors; feedback pending |

**Infrastructure**

| Component | Detail |
|---|---|
| Hosting | Google Cloud Run (us-west1), auto-scaling 0–3 instances |
| Database | GCP Cloud SQL (PostgreSQL) |
| Audio Storage | AWS S3 |
| Monthly Cost | $50/mo for 11 languages ($3–4 per language for AI translation + TTS; remainder is fixed hosting/DB) |

---

## Camp Meeting Goal

**Objective:** Target Japanese for limited beta by Camp Meeting. Korean and Filipino to follow as coordination with local leaders and translation teams progresses. Live demo at Camp Meeting regardless of beta status.

**What "limited beta" means:**
- Site accessible to a small group of church members in the target language
- Daily and Daybreak devotionals auto-translated with TTS audio
- "Beta Translation" label on all translated content
- Feedback link visible — readers can flag issues
- Rollback plan in place (revert to English-only if critical issues arise)

**Camp Meeting showcase:**
- Share afc-dev.org — aligned with Sis. Catey Hinkle (May 25)
- Live demo: enter English devotional → translate + generate audio
- Show working pages with audio playback
- Invite world leaders to express interest for their languages

> **Pre-camp announcement (agreed May 25):** The camp meeting presentation will be a publicly available live stream or equivalent announcement. To avoid misunderstanding from regions that already operate translation teams, it is advisable to notify relevant regional leadership before the public presentation. TP to follow up with Sis. Catey Hinkle to coordinate the timing and scope of this pre-announcement.

---

## Language Tier Plan

Tier structure aligned with Sis. Catey Hinkle (May 25 HQ visit).

| Tier | Language | Target | Review / Coordination | TTS | Status |
|---|---|---|---|---|---|
| 1 | Japanese | Camp Meeting (July 2026) | Bro. Yoshiki + congregation | Cloud TTS Neural2 | Beta underway; congregation sharing; interest in magazine |
| 2 | Korean | Post-Camp Meeting | Bro. Jeongmin + voluntary translation team (reported) | Cloud TTS Neural2 | Follow-up pending (travelling) |
| 2 | Filipino | Post-Camp Meeting | Bro. Rocky + voluntary translation team (reported) | Cloud TTS Neural2 | Follow-up pending (travelling) |
| 2 (candidate) | Swahili | TBD | No existing translation team — AI-assisted approach may be well-suited | Cloud TTS (to verify) | Under consideration; easy to add technically |
| 3 | Spanish | 2026 H2 | Regional editorial team (TBD, per SG's direction) | Cloud TTS Neural2 | Sis. Catey: flexibility to allow time for engagement before accelerating |
| 3 | French | 2026 H2 | Regional editorial team (TBD, per SG's direction) | Cloud TTS Neural2 | Depends on SG follow-up with regional contacts |
| 3 | Portuguese | 2026 H2 | Regional editorial team (TBD, per SG's direction) | Cloud TTS Neural2 | Depends on SG follow-up with regional contacts |

**Tier rationale:**
- **Tier 1 (Japanese):** Most concrete feedback to date. Bro. Yoshiki has already distributed the beta link to the congregation.
- **Tier 2 (Korean, Filipino):** Both are reported to have voluntary translation teams in-country. AI translation can augment these teams — handling the initial draft so translators focus on review and refinement. Leaders are contacted; timeline depends on their availability.
- **Tier 2 candidate (Swahili):** No existing translation team; AI-assisted approach may be particularly well-suited. Easy to add technically. Being considered alongside Korean and Filipino for Tier 2.
- **Tier 3 (Spanish, French, Portuguese):** For languages with relatively richer existing translation resources, Sis. Catey indicated there is flexibility to allow time for proper engagement with regional leaders before accelerating. External dependency on SG's regional contacts.

> **Note on Yoruba:** Yoruba has established translation teams within the church. AI-assisted translation adds less incremental value here. Not prioritized in Phase 1 but can be revisited based on editorial team interest.

**Editorial Review Model (per SG's direction):**
- Regional editorial teams and existing voluntary translation teams review AI translations — not centralized HQ
- AI generates the initial draft; human reviewers refine and correct
- Reviewers provide feedback in natural language (style, doctrine, culture)
- Feedback stored per language and applied to future translations
- System improves cumulatively — reviews should get lighter over time
- TGV volunteer review UI supports this workflow

---

## Service Modality (Under Review)

*Direction discussed with Sis. Catey Hinkle, May 25, 2026.*

The current implementation delivers content via web (afc-dev.org) and email subscription. During the May 25 HQ visit, Sis. Catey requested consideration of a **literature-focused mobile app** as a more effective delivery medium.

**Proposed direction:**
- A mobile app covering devotional, Sunday school, and magazine content
- Per-user language and notification settings
- Integration pathway: either within the existing AFC mobile app, or as a standalone app (to be decided)

**Why mobile app over web + email:**
- Push notifications are more effective than email for daily content
- Language preference set once at account level, not per-visit
- Eventual home for all AFC literature content across languages
- Aligns with feedback from Bro. Yoshiki (Japan): sharing link via LINE works but requires too many taps for elderly; direct integration into the AFC mobile app would resolve daily access friction.

**Status:** Under review. No implementation commitment yet. Will inform Phase 2+ roadmap and the longer-term platform direction described in [Integration Strategy](integration-strategy.md).

---

## TTS Provider Comparison

| Provider | Cost (per 1M chars) | Quality vs. Cloud TTS | Multi-language | Status |
|---|---|---|---|---|
| Google Cloud TTS Neural2 | $16 | Baseline (natural, clear) | 9 languages | **Current provider** |
| Google Cloud TTS WaveNet | $16 | Comparable | Used for ro, nb | Active |
| ElevenLabs | $165 | Better (more natural) | Limited languages | Evaluated, cost prohibitive (~10x) |
| Gemini TTS | Free (API) | Comparable | Experimental | Tested, potential backup |
| Facebook MMS | Free (open-source) | Lower | Yoruba only | Disabled (quality issues) |

**Recommendation:** Continue with Google Cloud TTS Neural2. Quality is reasonable across supported languages at ~10% of ElevenLabs cost. Audio samples available for committee review upon request.

**Open item — English TTS quality:** Current English TTS voice is below the quality of existing HQ recordings (noted by BE at April 11 meeting; confirmed May 23). A side-by-side quality comparison is needed before expanding to additional languages. This will be conducted once Tier 1/2 language reviewers are engaged.

**Open item — Filipino (Tagalog) TTS intonation/accents:** Feedback from Bro. Rocky indicates the current Tagalog TTS voice has awkward intonation and accents in places. In Filipino, incorrect accentuation can alter the meaning of words. Different Google Cloud TTS Tagalog voices/models and alternative low-cost providers must be evaluated in June to resolve this issue without significantly increasing operating costs.

**Note on voice quality evaluation:** A meaningful TTS quality comparison depends on the target languages being confirmed. Once Tier 1 and Tier 2 languages are active, voice quality feedback will be collected from native-speaker reviewers in those languages to determine whether the current provider meets the standard for public beta.

---

## Timeline

| Phase | Dates | Key Activities | Gate |
|---|---|---|---|
| **ITAC Review** | May 23 ✓ | Tier structure approved; leader names confirmed | Done |
| **HQ Alignment** | May 25 ✓ | Sis. Catey Hinkle: tiers confirmed; pre-camp announcement approach agreed; mobile app direction discussed. Sis. Rodica Musgrave: positive alignment on agentic website migration approach | Done |
| **Shadow** | May 26 – Jun 6 | Process devotionals through full pipeline for Tier 1; follow up with Bro. Jeongmin and Bro. Rocky; coordinate pre-camp announcement with Sis. Catey | Tier 1 quality acceptable |
| **Dogfood** | Jun 9 – Jun 20 | Share with bilingual ministers for feedback; incorporate corrections into translation prompts | Reviewers comfortable with quality |
| **Beta Prep** | Jun 23 – Jul | Deploy beta with "Beta Translation" label; send pre-camp regional announcement; prepare demo script and materials | Ready for camp meeting |
| **Camp Meeting** | July 6 | Live demo; collect interest from world leaders; gather feedback for Tier 2/3 | — |

> **Timeline flexibility:** Each region has its own schedule constraints. The Shadow → Dogfood → Beta phases may be condensed or adjusted as coordination with local leaders progresses. Tier 2 languages will enter the pipeline as their respective leaders and translation teams become available — this may extend beyond Camp Meeting. The system is ready; the pace is set by people, not technology.

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
| Google Cloud TTS | $8 | $3–4 per language (currently 9 TTS languages) |
| Gemini AI translation | <$5 | Scales with language count |
| AWS S3 audio storage | <$2 | Grows slowly |
| **Total (current, 11 languages)** | **$50/mo** | Based on actual spend |
| **Total (with nonprofit credits)** | **$15/mo** | Hosting/DB covered by credits |

Per-language variable cost (translation + TTS): approximately **$3–4/mo per language**.

---

## Risks and Mitigations

| Risk | Likelihood | Mitigation |
|---|---|---|
| Translation quality not sufficient for beta | Medium | "Beta Translation" label, feedback UI, rollback plan. Bro. Yoshiki's early feedback is encouraging but more testing needed. |
| Content ingestion unreliable | Low | Email ingestion tested and working; RSS as backup; manual entry available. Shadow period will stress-test. |
| Tier 2 leader coordination delayed | Medium | Bro. Jeongmin and Bro. Rocky currently travelling. Follow-up scheduled within days. Tier 2 timeline is flexible. |
| Active feedback cycle absent | Medium | Not yet implemented. Reader corrections are not currently feeding back into translation prompts. Planned for near-term development. |
| Regional misunderstanding at camp meeting | Medium | Pre-camp announcement to relevant regional leadership agreed with Sis. Catey. TP to coordinate timing. |
| English TTS quality below HQ standard | Medium | Side-by-side comparison planned before expanding languages. Current English audio acceptable for demo purposes. |
| Tagalog (Filipino) TTS intonation errors alter word meaning | High | Test alternative Google Cloud TTS Tagalog voices and other low-cost models. Perform basic audio quality tests across all target languages. |
| Editorial teams not available for Tier 3 | Medium | Depends on SG's follow-up with regional contacts. Tier 3 timeline (2026 H2) provides buffer. |

---

## Open Action Items

| Action | Owner | Due |
|---|---|---|
| Send bullet point summary to SF for camp meeting presentation | TP | This weekend (May 25) |
| Follow up with Bro. Jeongmin (Korean) and Bro. Rocky (Filipino) | TP | Done (June 6) |
| Ping Sis. Catey regarding TGV announcement to world leadership | TP | Coming week (mid-June) |
| Conduct basic audio quality tests for target languages (JA, KO, FI) | TP | June 2026 |
| Evaluate alternative Tagalog TTS voices to fix intonation issues (low-cost) | TP | June 2026 |
| Complement/improve the feedback verification loop | TP | June 2026 |
| Revisit naming, domain, and public-facing branding (AF/AFC Global Voice) | TP / TS | Before public beta launch |
| Coordinate pre-camp regional announcement with Sis. Catey | TP | Before camp meeting prep phase |
| Implement active translation feedback cycle | TP | Near-term |
| Initiate GCP for Nonprofits application | TP | Ongoing |
| English TTS side-by-side quality comparison | TP | Before expanding to additional languages |
| Evaluate Swahili as Tier 2 candidate | TP | With Tier 2 review |

---

## Project Naming

"The Global Voice" is the current working code name. At the May 23 ITAC meeting, TS suggested renaming to "AF Global Voice" or "AFC Global Voice" to make the Apostolic Faith affiliation explicit. TP agreed the naming, domain, and public-facing branding will be revisited ahead of the public beta launch. No change to this document or the codebase until a name is confirmed.

---

## Reference

- Live site: https://afc-dev.org
- Repository: `apostolicfaith-dev/the-global-voice`
- Integration Strategy: [integration-strategy.md](integration-strategy.md)
- Design docs site: https://apostolicfaith-dev.github.io/afc_design/
