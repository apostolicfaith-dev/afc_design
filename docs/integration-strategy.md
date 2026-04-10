# AFC Platform — Incremental Integration Strategy

> - Created: 2026-03-14
> - Last Updated: 2026-03-31
> - Status: Draft — Post-ITAC Review, Pending HQ Approval
> - Related: [website-structure-analysis.md](./website-structure-analysis.md), [maintenance-use-cases.md](./maintenance-use-cases.md)

## Executive Summary

**Direction:** Build an internal platform where devotional content goes in once and automatically gets translated, turned into audio, and published to apostolicfaith.org via Webflow API — plus email subscription and RSS feeds. Start with Daily + Daybreak devotionals, then expand phase by phase until the platform can fully replace Webflow.

**Phase 1 Scope (Daily + Daybreak Devotionals):**
- Content entry → AI translation (target languages TBD) → TTS audio (all languages incl. English) → Webflow publish + email/RSS
- Target: internal prototype review by mid-April, Beta launch by mid-June 2026
- Release process: Shadow → Dogfood → Beta → GA

**Roadmap:**
- Phase 0: Foundation — done (the-global-voice prototype)
- Phase 1: Daily + Daybreak Devotionals (target Beta mid-June 2026)
- Phase 1.5: Content Backfill (optional, cost-dependent)
- Phase 2: Sunday School / Curriculum
- Phase 3: Magazine
- Phase 4: World Report & Events
- Phase 5: Full Webflow Replacement

**Budget:**
- Development: ~~traditional dev costs ($12K est.)~~ → eliminated by agentic AI coding tools. ITAC leads donate AI service subscriptions ($100/mo per person) to cover tooling.
- Operations (Phase 1 monthly estimate):
  - Webflow CMS plan: $23/mo (required for API access)
  - Cloud TTS (AWS Polly / Google Cloud): ~$8/mo
  - AI translation (Gemini): <$5/mo
  - Hosting (Vercel/AWS free tiers): $0–20/mo
  - S3 storage: negligible
  - **Total: ~$35–55/mo**
- A formal TTS quality comparison (cloud TTS vs. ElevenLabs vs. open-source options) is planned before final provider selection. See [cost details](./webflow-api-setup.md#tts-provider-cost-comparison-per-1-million-characters).

**Open Decisions:**
- Target languages for Phase 1 (TBD)
- TTS provider (pending quality comparison)
- Hosting platform — see [Hosting Strategy](./hosting-strategy.md) (cloud-first with nonprofit credits, Azure vs. GCP pending ITAC decision)
- Webflow locale setup (pending Publishing Team confirmation)

---

## Stakeholders & Approval Chain

### Key Stakeholders

- **Bro. Sola Adosope** — Superintendent General, AF HQ Portland. Final approval authority.
- **Bro. John Musgrave** — Treasurer, AF HQ Portland. Budget and resource approval.
- **Sis. Catey Hinkle** — Publishing Team Lead, AF HQ Portland. Content workflow owner and primary daily user of the platform.
- **ITAC** (Apostolic Faith IT Advancement Committee) — IT direction and recommendations. This project's scope is co-owned by ITAC.

### Approval Steps

```
Step 1: ITAC Strategic Review ─── Target: End of March 2026
   ▼
Step 2: Publishing Team Workflow Review ─── Target: First two weeks of April
   ▼
Step 3: HQ Leadership Approval ─── Target: Mid-April 2026
   ▼
Step 4: Development Begins ─── Target: Late April 2026
   ▼
Step 5: Shadow + Dogfood Validation ─── Target: May–June 2026
   ▼
Step 6: Beta Launch (Phase 1) ─── Target: Mid-June 2026
```

**ITAC:** Review strategy, assess risks, produce recommendation, lead development.
**Publishing Team:** Review workflow changes, designate Dogfood testers, continue manual process until Beta.
**HQ Leadership:** Approve direction and budget after Steps 1-2. No action until Step 3.

---

## What Changes for the Publishing Team

### Daily + Daybreak Devotionals — Before vs. After

**Before (current manual process):**
1. Receive devotional content from editorial
2. Open Webflow, create a new CMS item
3. Copy-paste title, scripture reference, body content
4. Format the content (headings, paragraphs, pull quotes)
5. Upload images and attach study material links
6. Record or generate audio, upload to hosting, link to page
7. Publish the page
8. Repeat for each foreign language version (if translated)
9. **Time: ~45-80 minutes per devotional, 1.5-2.5 hours/day for both devotionals**

**After (with platform):**
1. Enter devotional content in the platform (similar editor, but purpose-built)
2. Click "Publish"
3. Platform automatically: translates, generates TTS audio (all languages incl. English), syncs to Webflow, publishes to email subscription and RSS feed
4. **Time: ~15-20 minutes per devotional** (content entry only — everything else is automated)

**What stays the same:** editorial process, website appearance, Publishing Team controls what/when to publish.

**What's new:** unified platform for both devotionals, status dashboard, automatic change propagation, TTS audio for all languages.

**Phase 1 scope boundaries:**
- **In scope:** Main devotional content, translations, TTS audio, Webflow sync, email/RSS
- **Out of scope:** Supplementary study materials (Bible Book, Outline, Maps, Timeline) — manual in Webflow during Phase 1
- **Out of scope (future):** Manual audio override (data model supports it, UI deferred)

---

## Vision & Core Principles

Build an internal content management platform ("AFC Platform") that becomes the single source of truth for all AFC content. Webflow remains the public-facing rendering layer initially, fed via API. Over time, the platform absorbs more responsibilities until Webflow can be fully replaced.

```
┌─────────────────────────────────────────────────────────────────┐
│                        AFC Platform                             │
│                   (Source of Truth)                              │
│                                                                 │
│  Content    ──→  Translation  ──→  Audio/TTS  ──→  Publishing   │
│  Entry           Pipeline          Pipeline        Engine       │
│                                                                 │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │  Content DB: devotionals, lessons, articles, reports      │  │
│  │  Mapping DB: platform entry ↔ Webflow page ID/slug        │  │
│  │  Asset Store: audio files, images, PDFs (S3)              │  │
│  └───────────────────────────────────────────────────────────┘  │
└────────┬────────────────┬────────────────┬──────────────────────┘
         │                │                │
         ▼                ▼                ▼
   ┌──────────┐    ┌───────────┐    ┌───────────────┐
   │ Webflow  │    │ Mobile    │    │ Email/Podcast  │
   │ (current │    │ App       │    │ Distribution   │
   │  website)│    │ (future)  │    │                │
   └──────────┘    └───────────┘    └───────────────┘
```

**Core Principles:**
1. **Platform is the source of truth** — Content lives in our DB, not Webflow
2. **Webflow as rendering layer** — Push via CMS API, don't manage content there
3. **Content-Webflow mapping** — Every entry stores its Webflow collection item ID
4. **Change detection triggers pipeline** — Content change → re-translate → re-generate audio → re-sync
5. **Incremental rollout** — One content type at a time
6. **Translation: beta mode first** — target languages TBD, auto-approve with beta label
7. **TTS-first audio** — All languages (including English) via TTS by default

---

## Why Devotionals First

> Added 2026-04-09. This section addresses the question of why daily devotionals are the best starting point for the translation system, particularly in response to concerns about workload, upcoming content revisions, and alternative starting points.

### The Goal Is the System, Not the Artifacts

The primary objective is not to produce translated devotional texts — it's to build a translation system that understands our doctrine, aligns with the voice and tone of each target language, and can be continuously refined. The translated devotionals are a byproduct of training and validating that system.

Once the system is calibrated, **any content can be processed** — devotionals, the magazine, the 교역자 매뉴얼 (Minister's Manual), Sunday School materials. Starting with devotionals doesn't lock us in; it gives us the fastest path to a production-ready translation engine.

### Why Devotionals Win on Feedback Loop Speed

| Factor | Daily Devotional | Magazine | 교역자 매뉴얼 |
|---|---|---|---|
| New content frequency | Daily (365/yr) | 28 articles/yr | Rarely updated |
| Average length | 300-500 words | 2,000-5,000 words | 10,000+ words |
| Reviewer time per piece | 5-10 minutes | 30-60 minutes | Hours |
| Feedback loop speed | Daily | Monthly | Yearly |
| Pipeline validation | Every day | Once a month | Once |

**Key insight:** With devotionals, we discover translation problems *every day* and fix them *every day*. With the magazine, we discover problems once a month and wait another month to verify fixes. The system improves 30x faster with daily content.

### Revisions Are a Feature, Not a Problem

Concern: "Daybreak is about to undergo a major revision. Should we wait?"

Answer: **Revisions are exactly why the system needs to exist.** When content is revised:
1. Editor updates the English text in the platform
2. Click "Re-translate All Languages" — one button
3. System automatically re-translates, re-generates audio, re-publishes
4. Total time: minutes, not weeks

The question isn't "will there be revisions?" — there will always be revisions. The question is "how painful are revisions?" With the system in place, they're trivial. Without it, every revision means manually re-translating into every language.

### Reviewer Workload: Short Content = Light Reviews

Concern: "365 devotionals per year is too many to review."

Response: Consider the actual review experience:

- **Devotional (400 words):** Reviewer reads side-by-side with English. 5-10 minutes. Can give feedback like "use '사도의 신앙 교회' instead of 'Apostolic Faith Church'" and the system learns it permanently.
- **Magazine article (3,000 words):** Reviewer needs to carefully check doctrinal nuances across dozens of paragraphs. 30-60 minutes. Every correction is specific to that article.
- **교역자 매뉴얼 chapter (10,000+ words):** Requires expert theological review. Hours per chapter. Corrections may conflict across sections.

Additionally, the review process is designed to be **cumulative, not repetitive**:
- Feedback becomes translation reference notes (per language)
- System learns from corrections ("this term should always be translated as X")
- Each review improves all future translations
- Over time, reviews become lighter as the system gets smarter

### The UX of Giving Feedback

Reviewers don't need to manually edit translated text. They can provide feedback in natural language:

- **Style:** "Korean should use 하십시오체 (formal polite) consistently"
- **Doctrine:** "'Sanctification' must be translated as '성화', never '거룩하게 됨'"
- **Culture:** "This American metaphor doesn't work in French — add a brief contextual note"

These notes are stored per language and automatically injected into future translations. The reviewer teaches the system once; it remembers forever.

### What About 교역자 매뉴얼?

Starting with 교역자 매뉴얼 was suggested because ministers would be "more forgiving of bumps." This is true, but:

1. 교역자 매뉴얼 is also undergoing revision (global committee review), so the same revision concern applies
2. The content is long and doctrinally dense — review workload is much heavier per piece
3. Feedback loops are slow (manual rarely updated → can't iterate quickly)
4. If the translation system makes a doctrinal error in a minister's manual, the consequences are more serious than in a daily devotional

Devotionals are the safer, faster proving ground. Once the system is calibrated on devotionals, expanding to 교역자 매뉴얼 is straightforward — same pipeline, same language notes, same quality controls.

### Agentic Development: Rapid Iteration at Near-Zero Cost

A key enabler of this project is **agentic AI-assisted development** — AI agents that write, test, and deploy code with human oversight. This fundamentally changes the cost-benefit calculus:

**Speed of change:** When a reviewer says "Korean translations should use 하십시오체 (formal polite)," we don't file a ticket and wait for a developer. The AI agent updates the translation prompt, tests it, and deploys — often within the same conversation. The change is live in minutes.

**Cost of new features:** Adding a new content type (e.g., 교역자 매뉴얼) doesn't require weeks of development. The structured fields, parsing logic, translation templates, and TTS configuration can be added in a single session. The pipeline architecture is designed to be content-type agnostic.

**What this means concretely:**
- "We need Yoruba support" → added in one session (locale files, DB, translation pipeline, admin UI)
- "The Bible verse format is wrong in Korean" → prompt updated, all future translations fixed
- "We want to start translating magazine articles" → new content type, same pipeline, same day
- "The TTS speaks too fast" → speaking rate changed from 1.0x to 0.8x, all audio regenerated in an hour
- "Daybreak needs structured sections" → 5 new DB fields + edit form + rendering + TTS templates, built and deployed in one session

**This is not theoretical.** Every example above actually happened during the development of The Global Voice. The system is designed to be modified as easily as it is used — because the same AI tools that built it can extend it on demand.

This means the Publishing Team's feedback doesn't go into a backlog. It becomes a system improvement the same week — sometimes the same day.

---

## Phase 1 — Scope & Timeline

### Scope

**Goal:** Both devotionals enter the platform once → auto-publish to Webflow in English + translated languages with TTS audio, plus email/RSS distribution.

- Content entry UI for Daily Devotional and Daybreak Devotional (adapt from the-global-voice)
- Daybreak supports structured sections (Overview, Background, Amplified Outline, Closer Look, Conclusion)
- Webflow CMS API integration — see [setup guide](./webflow-api-setup.md)
- Content ↔ Webflow mapping table
- Translation pipeline (target languages TBD, likely ES/FR/KO) with beta auto-approve
- TTS audio generation for all languages including English — provider TBD pending [quality comparison](./webflow-api-setup.md#tts-provider-cost-comparison-per-1-million-characters)
- Email subscription / RSS publish workflow migration
- Change detection: content update → re-translate → re-generate audio → re-sync
- Admin dashboard: pipeline status, sync status per entry

**Out of scope:** Supplementary study materials, manual audio override UI, historical backfill (see Phase 1.5).

**Success criteria:** Editor types devotional once → appears on apostolicfaith.org in all languages with TTS audio and email/RSS distribution. Content updates trigger automatic re-processing.

### Timeline (Target: Beta by Mid-June 2026)

**Week 1–2 (Mar 17–28) — Stakeholder Alignment**
- [ ] ITAC reviews strategy, produces recommendation memo
- [ ] Webflow API access requested; confirm CMS plan ($23/mo+)
- [ ] Audit Webflow CMS collections for both devotional types
- **Gate:** ITAC alignment complete

**Week 3–4 (Mar 31–Apr 11) — Publishing Team Review + HQ Approval**
- [ ] Present workflow changes to Sis. Hinkle and Publishing Team
- [ ] Internal prototype review (~Apr 15)
- [ ] Present to Bro. Adosope and Bro. Musgrave for approval
- **Gate:** HQ approval before development

**Week 5–6 (Apr 14–25) — Architecture & Foundation**
- [ ] Finalize data model (devotional schema, Webflow mapping, translation records)
- [ ] Set up dev environment (database, hosting, CI/CD)
- [ ] Implement Webflow API client; build content entry UI
- [ ] Verify API-created pages match current site format

**Week 7–8 (Apr 28–May 9) — Core Pipeline**
- [ ] Integrate translation pipeline (Gemini AI)
- [ ] TTS quality comparison: cloud TTS vs. ElevenLabs vs. VibeVoice (English)
- [ ] Integrate TTS generation (provider TBD based on comparison)
- [ ] Implement content ↔ Webflow sync + change detection
- [ ] Build admin dashboard

**Week 9–10 (May 12–23) — Shadow**
- [ ] Process 1-2 weeks of devotionals through full pipeline
- [ ] Team reviews translation/TTS/sync quality
- **Gate:** Team confident in output quality

**Week 11–12 (May 26–Jun 6) — Dogfood**
- [ ] Expand to Publishing Team + bilingual volunteers
- [ ] Side-by-side comparison, collect feedback
- **Gate:** Publishing Team signs off

**Week 13 (Jun 9–15) — Beta Launch**
- [ ] Switch to platform-generated publishing
- [ ] "Beta Translation" label on translated content
- [ ] Rollback plan ready

**Post-Beta (Jun 16+)**
- [ ] Monitor quality, iterate on translation prompts
- [ ] When stable (<2% reported issues): remove Beta label → GA

---

## Release Process

Every phase follows: **Shadow → Dogfood → Beta → GA**. No unvalidated content reaches the public.

- **Shadow:** Team-only testing against a staging environment. Verify pipeline correctness.
- **Dogfood:** Expand to small trusted group (ministers, bilingual volunteers). Side-by-side comparison with manual output. 1-2 weeks minimum.
- **Beta:** Live on apostolicfaith.org with "Beta Translation" label. Feedback link visible. Runs until <2% reported issues.
- **GA:** Beta label removed. Platform becomes the official process. Old manual workflow retired.

---

## Alternative Path: TGV as Standalone Multilingual Site

> Added 2026-04-09. The original strategy assumes Webflow API integration (Phases 1-4) before the platform can serve content publicly. However, the-global-voice prototype has advanced significantly beyond "prototype" status and may already be viable as a standalone public-facing site — bypassing the need for Webflow API integration entirely.

### What TGV Already Has (as of April 2026)

| Capability | Status |
|---|---|
| 6-language support (en, es, fr, ko, ja, yo) | ✅ Production |
| AI translation pipeline (Gemini, structured output) | ✅ Production |
| TTS audio in 5 languages (Cloud TTS Neural2, 0.8x) | ✅ Production |
| Bible verse lookup (5 translations via getbible.net + local nkrv) | ✅ Production |
| Structured Daybreak fields (background, outline, closer look, conclusion) | ✅ Production |
| Daily + Daybreak devotional pages with audio player | ✅ Production |
| Universal task queue (auto-translation, auto-TTS after ingest) | ✅ Production |
| Admin dashboard (queue monitor, translation editor, settings) | ✅ Production |
| Public pages: Our Faith, Portland, Calendar, Camp Meeting, etc. | ✅ Production |
| Content ingestion from email (daily cron) | ✅ Production |
| RSS ingestion route (ready, pending feed reliability) | ✅ Ready |

### The Alternative

Instead of building Webflow API integration (Phase 1 core work), TGV could:

1. **Consume content from apostolicfaith.org** — via email or RSS, no API access needed
2. **Translate + generate audio automatically** — the pipeline already does this
3. **Serve directly at a subdomain** — e.g., `global.apostolicfaith.org` or standalone domain
4. **Skip Phases 1-4 entirely** — go straight to Phase 5 (TGV as the public site)

```
Current Strategy:
  Platform → Webflow API → apostolicfaith.org (public)
  [Phase 1-4 needed]

Alternative:
  apostolicfaith.org (email/RSS) → TGV → TGV serves directly (public)
  [Already working]
```

### Trade-offs

**Pros:**
- No Webflow CMS plan needed ($23/mo saved)
- No Webflow API integration work (Weeks 5-8 eliminated)
- Already deployed and functional on Vercel
- Faster time to value — multilingual devotionals are live TODAY
- Publishing Team workflow is simpler (content enters via existing email, platform handles the rest)

**Cons:**
- Not integrated with apostolicfaith.org main site (separate URL)
- Doesn't replace Webflow — runs alongside it
- SEO: separate domain means building authority from scratch
- Dependent on email/RSS ingestion (not direct content entry by Publishing Team)
- No Webflow CMS mapping — content lives in two places

### Recommendation

**Start with the Alternative Path for Phase 1.** TGV is already serving multilingual devotionals with audio. Deploy it publicly and gather real user feedback while the Webflow API integration is evaluated separately.

If the standalone site proves sufficient, Phases 1-4 can be deprioritized or eliminated. If Webflow integration is still desired (e.g., for SEO continuity or unified site management), it can proceed in parallel without blocking the multilingual launch.

This is not an either/or — it's a **"launch now, integrate later"** approach.

---

## Future Phases

### Phase 0: Foundation (Done)
- [x] Translation pipeline prototype (the-global-voice)
- [x] AI translation with theological guardrails (Gemini)
- [x] TTS generation (ElevenLabs / Gemini → Cloud TTS)
- [x] Website structure analysis

### Phase 1.5: Content Backfill (Optional, After Phase 1 GA)
Migrate historical devotionals into the platform. Timing depends on budget — batch TTS for ~19.2M characters has significant cost. See [cost estimates](./webflow-api-setup.md#phase-15-backfill-cost-estimate).

### Phase 2: Sunday School / Curriculum
Age-group variants (Primary Pals, Answer, Search), student/teacher editions, PDF generation, supplementary study materials moved from manual Webflow to platform.

### Phase 3: Magazine (The Apostolic Faith)
Quarterly issue builder, volume landing pages, article-level translation and audio, archive management.

### Phase 4: World Report & Events
Regional editor submissions, Camp Meeting registration, calendar integration, photo/media galleries.

### Phase 5: Full Platform (Webflow Replacement)
Public-facing frontend (Next.js or similar), SEO migration, DNS transition, legacy URL redirects, full CMS, manual audio override UI.

> **Note:** With the Alternative Path, Phase 5 is essentially what TGV already is. The remaining work would be DNS transition, SEO migration, and legacy URL redirects.

---

## Translation Strategy

**Beta Mode (Phases 1-2):** Target languages TBD (likely Spanish, French, Korean). Auto-approve AI translations with "Beta Translation" indicator. Theological guardrail flags issues but doesn't block. Feedback mechanism for quality.

**Production Mode (Phase 3+):** Expand to all 9 languages (Arabic, Italian, Portuguese, Russian, Romanian, Vietnamese + Indian languages). Optional volunteer review for high-visibility content. Quality metrics tracking per language.

---

## Risk Assessment

### Technical Risks
- **Webflow plan/API access:** CMS plan required. Potential blocker if on Starter/Basic. Verify in Week 1.
- **Webflow API limitations:** Rate limits or feature gates could block integration. Audit early.
- **Translation quality:** AI may have theological inaccuracies. Mitigation: guardrails, beta label, feedback loop.
- **TTS voice quality:** Quality comparison planned before final selection. Data model supports future manual override.
- **Webflow structure changes:** Sync may break if page structure changes. Mitigation: explicit mapping table, monitoring alerts.

### Operational Risks
- **Publishing Team adoption:** Involve early, dogfood period, ensure platform is genuinely easier.
- **Dual workflow burden:** Shadow/Dogfood run in parallel without disrupting current process. Switch only at Beta.
- **Scope creep:** Strict phase gates. Phase 1 must reach GA before expanding.

### Resource Risks
- **Development capacity:** Agentic AI tools handle implementation. ITAC leads donate AI subscriptions (~$100/mo/person).
- **API costs:** Cloud TTS ~$8/mo vs ElevenLabs ~$86/mo. Backfill deferred until budget confirmed.

---

## Key Design Decisions

1. **Evolve the-global-voice or build new?**
   Recommendation: Evolve — the core pipeline logic is proven.

2. **Webflow locale system vs. separate collections per language?**
   Need to audit current setup. Sis. Catey Hinkle should confirm how foreign language content is currently structured in Webflow. This affects the API integration approach.

3. **Where does the platform run?**
   Cloud-first with nonprofit credits. Azure vs. GCP pending ITAC decision — see [Hosting Strategy](./hosting-strategy.md) for full evaluation. Self-hosted GPU reserved for VibeVoice experiments only.

4. **Content entry format**
   Rich text (TipTap) for free-form prose — flexible but data entry person owns formatting risk. Structured JSON forms for fixed-section content — less error-prone but more UI maintenance. Recommendation: rich text for devotionals/articles, structured forms for curriculum. Daybreak (fixed sections) may benefit from a hybrid.

5. **Authentication**
   Phase 1: email-based auth (passcode via email, 30-day session). Long-term: evaluate SSO for all AFC digital services.

---

## Content Change Re-pipeline

```
Editor updates English devotional in Platform
        │
        ├── Content changed? ──→ Re-translate → Re-generate TTS → Update Webflow
        ├── Metadata only? ──→ Update Webflow metadata only
        └── No meaningful change ──→ No action
```

Each platform entry stores: `webflowItemId`, `webflowCollectionId`, `lastSyncedAt`, `syncStatus`, `contentHash`.

---

## Reference

- the-global-voice repo: `apostolicfaith-dev/the-global-voice` (translation pipeline prototype)
- afcgeomap repo: `apostolicfaith-dev/afcgeomap` (magazine article geographic map)
- Webflow CMS API setup & cost details: [webflow-api-setup.md](./webflow-api-setup.md)
- Webflow CMS API docs: https://developers.webflow.com/data/reference/cms
- Current website: https://apostolicfaith.org/
- Design docs site: https://apostolicfaith-dev.github.io/afc_design/
