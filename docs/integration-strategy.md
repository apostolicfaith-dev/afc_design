# AFC Platform — Incremental Integration Strategy

> Created: 2026-03-14
> Last Updated: 2026-03-14
> Status: Draft — Pending ITAC Review
> Related: [website-structure-analysis.md](./website-structure-analysis.md), [maintenance-use-cases.md](./maintenance-use-cases.md)

## Summary

Instead of replacing the entire AFC website at once (risky and slow), we're building a behind-the-scenes platform where content goes in once and automatically gets translated into 3 languages, turned into audio, and published to the current Webflow website via API. We start with both daily devotionals (Daily + Daybreak) — targeting Beta by mid-June 2026 — then expand to Sunday school, magazine, and so on. All audio (including English) is generated via TTS by default. Each new feature goes through a release process: first only the team can see it (Shadow), then a small group tests it (Dogfood), then it goes out with a "beta" label, and finally it becomes the official way. This document needs alignment from ITAC, the Publishing Team, and HQ leadership before development begins.

---

## Stakeholders & Approval Chain

This strategy requires alignment and approval before implementation can proceed. The following action items must be completed in order.

### Key Stakeholders

- **Bro. Sola Adosope** — Superintendent General, AF HQ Portland. Final approval authority.
- **Bro. John Musgrave** — Treasurer, AF HQ Portland. Budget and resource approval.
- **Sis. Catey Hinkle** — Publishing Team Lead, AF HQ Portland. Content workflow owner. Her team will be the primary daily users of the platform.
- **ITAC** (Apostolic Faith IT Advancement Committee) — Provides IT direction and recommendations to HQ. This project's scope is co-owned by ITAC.

### Action Items

```
Step 1: ITAC Strategic Review
   │  - Review this document for technical feasibility, risks, and priorities
   │  - Align on Phase 1 scope and timeline
   │  - Identify resource needs (dev, infrastructure, API access)
   │  - Produce recommendation memo for HQ
   │  - Owner: ITAC
   │  - Target: By end of March 2026
   │
   ▼
Step 2: Publishing Team Workflow Review
   │  - Present current-vs-future workflow comparison to Publishing Team
   │  - Sis. Hinkle and team review what changes for their daily work
   │  - Collect feedback: pain points we missed, concerns, must-haves
   │  - Document agreed workflow changes
   │  - Owner: ITAC + Publishing Team
   │  - Target: First two weeks of April 2026
   │
   ▼
Step 3: HQ Leadership Approval
   │  - Present strategy + ITAC recommendation + Publishing Team feedback
   │  - Bro. Adosope: strategic alignment approval
   │  - Bro. Musgrave: budget/resource approval (hosting costs, API services, dev time)
   │  - Owner: ITAC
   │  - Target: Mid-April 2026
   │
   ▼
Step 4: Development Begins
   │  - Webflow API access secured (see Webflow Setup section below)
   │  - Development environment stood up
   │  - Phase 1 implementation starts
   │  - Owner: ITAC (development)
   │  - Target: Late April 2026
   │
   ▼
Step 5: Shadow + Dogfood Validation
   │  - Publishing Team validates output quality
   │  - Bilingual volunteers test translations
   │  - Owner: ITAC + Publishing Team
   │  - Target: May–June 2026
   │
   ▼
Step 6: Beta Launch (Phase 1)
     - Daily + Daybreak Devotionals go live with platform-generated content
     - Translated content marked "Beta Translation"
     - Owner: ITAC + Publishing Team
     - Target: Mid-June 2026
```

### What Each Stakeholder Needs to Do

**ITAC:**
- Review and refine this strategy document
- Assess technical risks (Webflow API limitations, translation quality, TTS reliability)
- Provide recommendation to HQ with risk assessment
- Lead development and integration work
- Support Publishing Team during transition

**Publishing Team (Sis. Hinkle):**
- Review the "What Changes for the Publishing Team" section below
- Provide feedback on workflow gaps or concerns
- Designate 1-2 team members for Dogfood testing
- Continue current manual workflow during Shadow/Dogfood (no disruption until Beta)

**HQ Leadership (Bro. Adosope, Bro. Musgrave):**
- Review ITAC recommendation and Publishing Team feedback
- Approve strategic direction
- Approve budget for infrastructure (estimated: hosting, AI API costs, cloud storage)
- No action needed until Step 3

---

## What Changes for the Publishing Team

This section is specifically for the Publishing Team to understand what the platform means for their daily work.

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
3. Platform automatically: translates to 3 languages, generates TTS audio in all 4 languages (including English), creates/updates Webflow pages, publishes
4. **Time: ~15-20 minutes per devotional** (content entry only — everything else is automated)

**What stays the same:**
- The editorial process for writing devotionals doesn't change
- The apostolicfaith.org website looks the same to visitors
- The Publishing Team still controls what gets published and when

**What's new:**
- Both Daily and Daybreak devotionals handled through the same platform
- A dashboard showing pipeline status (translation progress, sync status)
- Ability to update content and have changes automatically propagate
- Audio generated automatically via TTS for all languages (including English)

**Phase 1 scope boundaries:**
- **In scope:** Main devotional content (title, scripture, body text), translations, TTS audio, Webflow sync
- **Out of scope:** Supplementary study materials (Bible Book, Introduction, Outline, Maps, Timeline) — these continue to be managed manually in Webflow during Phase 1
- **Out of scope (future):** Manual audio override — the ability to replace TTS audio with a human recording for selected entries. Not in Phase 1, but the data model should accommodate this.

---

## Vision

Build an internal content management platform ("AFC Platform") that becomes the single source of truth for all AFC content. Webflow remains the public-facing rendering layer initially, fed by our system via API. Over time, the platform absorbs more responsibilities until Webflow can be fully replaced.

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

---

## Core Principles

1. **Platform is the source of truth** — Content lives in our DB, not Webflow
2. **Webflow as rendering layer** — Push content to Webflow via CMS API, don't manage content there
3. **Content-Webflow mapping** — Every platform entry stores its Webflow collection item ID for sync
4. **Change detection triggers pipeline** — When source content changes, re-run translation → audio → publish
5. **Incremental rollout** — One content type at a time, starting with Daily + Daybreak Devotionals
6. **Translation: beta mode first** — 3 languages, auto-approve with beta label, expand later
7. **TTS-first audio** — All audio (including English) generated via TTS by default; manual audio override as a future option

---

## Phase 1 Timeline — Daily + Daybreak Devotionals (Target: Mid-June 2026)

Working backward from a Beta launch target of June 15, 2026. Both devotionals share the same pipeline — the Daybreak has more structured sections but the translation/TTS/sync flow is identical.

### Week 1–2: Mar 17 – Mar 28 — Stakeholder Alignment

- [ ] ITAC reviews this strategy document
- [ ] ITAC produces risk assessment and recommendation memo
- [ ] Webflow API access requested (see Webflow Setup below)
- [ ] Confirm Webflow plan is CMS ($23/mo) or higher (required for API access)
- [ ] Audit existing Webflow CMS collections for both Daily and Daybreak Devotional structure
- **Gate:** ITAC alignment complete before proceeding

### Week 3–4: Mar 31 – Apr 11 — Publishing Team Review + HQ Approval

- [ ] Present strategy and workflow changes to Sis. Hinkle and Publishing Team
- [ ] Collect and incorporate Publishing Team feedback
- [ ] Present to Bro. Adosope and Bro. Musgrave for approval
- [ ] Finalize Phase 1 scope based on all feedback
- **Gate:** HQ approval before development begins

### Week 5–6: Apr 14 – Apr 25 — Architecture & Foundation

- [ ] Finalize data model (devotional schema for both Daily and Daybreak, Webflow mapping, translation records)
- [ ] Design audio model to support TTS-generated audio with future manual override capability
- [ ] Set up development environment (database, hosting, CI/CD)
- [ ] Implement Webflow API client (auth, create/update/publish items)
- [ ] Build content entry UI for both devotional types (adapt from the-global-voice)
- [ ] Verify Webflow API can create devotional pages matching current site format

### Week 7–8: Apr 28 – May 9 — Core Pipeline

- [ ] Integrate translation pipeline (Gemini AI, English → ES/FR/KO)
- [ ] Integrate TTS generation for all 4 languages including English (ElevenLabs/Gemini)
- [ ] Implement content ↔ Webflow mapping and sync for both Daily and Daybreak collections
- [ ] Implement change detection (content hash → re-translate → re-generate → re-sync)
- [ ] Build admin dashboard (pipeline status, sync status per entry)

### Week 9–10: May 12 – May 23 — Shadow Deployment

- [ ] Deploy to staging environment
- [ ] Process 1-2 weeks of both daily devotionals through full pipeline
- [ ] ITAC reviews: translation quality, TTS audio quality, Webflow page accuracy
- [ ] Publishing Team compares platform output vs. manually published pages
- [ ] Fix issues, iterate
- **Gate:** Team confident in output quality

### Week 11–12: May 26 – Jun 6 — Dogfood

- [ ] Expand access to Publishing Team + select bilingual volunteers
- [ ] Side-by-side comparison with current manual process
- [ ] Collect feedback via simple form
- [ ] Address critical feedback
- **Gate:** Publishing Team signs off on quality

### Week 13: Jun 9 – Jun 15 — Beta Launch

- [ ] Switch Daily + Daybreak publishing to platform-generated
- [ ] Translated content displays "Beta Translation" label
- [ ] Feedback link visible on translated pages
- [ ] Publishing Team monitors daily
- [ ] Rollback plan ready (revert to manual if critical issues)

### Post-Beta: Jun 16+ — Monitoring & GA Transition

- [ ] Monitor quality metrics (reported issues, translation accuracy, TTS feedback)
- [ ] Iterate on translation prompts based on feedback
- [ ] When stable (<2% reported issues): remove Beta label → GA
- [ ] Begin Phase 2 (Sunday School / Curriculum) planning

---

## Release Process — Shadow → Dogfood → Beta → GA

Every phase follows a 4-stage release process before going live. No unvalidated content reaches the public.

```
Shadow ──→ Dogfood ──→ Beta ──→ GA
(team only)  (small group)  (public, labeled)  (public, official)
```

### Shadow (Internal Only)
- Platform generates content and syncs to a **shadow environment** (separate Webflow staging site or hidden collection)
- Only the development and content team can see the output
- Purpose: verify pipeline correctness — are translations accurate? Does audio sound right? Does Webflow sync work?
- Duration: until the team is confident in output quality
- Access: private URL, not linked from the live site

### Dogfood (Controlled Group)
- Expand access to a small trusted group (e.g., select ministers, editorial staff, bilingual volunteers)
- They use the platform-generated content as if it were live and provide feedback
- Side-by-side comparison: platform output vs. current manual output
- Purpose: catch edge cases, get real-user feedback on translation quality and UX
- Duration: 1-2 weeks minimum per content type
- Feedback mechanism: simple form or inline "report issue" button

### Beta (Public with Label)
- Content goes live on the actual website (apostolicfaith.org)
- Translated content marked with a visible **"Beta Translation"** indicator
- Users can report translation issues via feedback link
- Theological guardrail still flags (but doesn't block) potential issues
- English content has no beta label (it's authored by humans)
- Purpose: real-world validation at scale
- Duration: until quality metrics meet threshold (e.g., <2% reported issues)

### GA (General Availability)
- Beta label removed
- Platform-generated content is the official process
- Old manual workflow retired for that content type
- Monitoring continues (quality dashboards, error alerts)
- Rollback plan: can revert to manual Webflow publishing if critical issues arise

### Per-Phase Release Targets

- **Phase 1 (Daily + Daybreak Devotionals):** Shadow → Dogfood → Beta → GA
- **Phase 2 (Curriculum):** Shadow → Dogfood → Beta → GA (new content model, needs full cycle)
- **Phase 3 (Magazine):** Shadow → Dogfood → Beta → GA (high-visibility, quarterly cadence)
- **Phase 4 (World Report & Events):** Shadow → Beta → GA (less translation-dependent, simpler cycle)
- **Phase 5 (Content Backfill):** Migrate historical content (past devotionals, archived articles) into the platform
- **Phase 6 (Full Replacement):** Extended Beta with A/B testing before full DNS cutover

---

## Webflow CMS API — Setup & Integration

### What the API Can Do

Based on Webflow's Data API:

- **Collections**: List, create, get details, manage fields
- **Items (Staged/Draft)**: Create, update, delete, publish — bulk operations (up to 100 items)
- **Items (Live/Published)**: Create, update, unpublish — bulk operations
- **Multi-locale**: Create items across multiple locales via `cmsLocaleIds`
- **Webhooks**: Get notified on item create/update/delete/publish/unpublish
- **Rich text fields**: Supported (HTML content)

Key capabilities for our use:
- Programmatically create a devotional page with title, content (rich text), audio URL, date, scripture reference
- Update existing pages when content changes
- Publish/unpublish programmatically
- Handle multilingual versions via Webflow's locale system or separate collections per language

### Webflow Plan Requirement

CMS API access requires **Webflow CMS plan ($23/mo) or higher**. The Starter and Basic plans do not include API access.

- **CMS plan ($23/mo):** 2,000 CMS items, 60 requests/minute — sufficient for Phase 1
- **Business plan ($39/mo):** 20,000 CMS items, 120 requests/minute — needed if scaling to all content types
- **Action item:** Confirm apostolicfaith.org is on CMS plan or higher. If on Basic/Starter, a plan upgrade is needed before API integration can begin. This is a **potential blocker** — must be verified in Week 1.

### Who Needs to Do What

**Step 0: Verify Webflow Plan (Owner: Publishing Team / Site Admin)**
- Check current Webflow plan at Dashboard → Account Settings → Billing
- If on Starter or Basic: upgrade to CMS plan ($23/mo) minimum — requires Bro. Musgrave's approval
- If already on CMS or Business: proceed to Step 1

**Step 1: Get Webflow API Access (Owner: Publishing Team / Site Admin)**
- Someone with Webflow admin access to apostolicfaith.org must generate an API token
- Go to Webflow Dashboard → Site Settings → Integrations → API Access → Generate API Token
- The token needs CMS read/write permissions at minimum
- Share the token securely with ITAC (not via email — use a password manager or secure channel)

**Step 2: Audit Existing Collections (Owner: ITAC)**
- Using the API token, list all CMS collections on the site
- Identify the collection used for Daily Devotionals (fields, structure, slugs)
- Document the field mapping: which Webflow fields map to which platform fields
- Check if Webflow multi-locale is enabled on the site
- Check if foreign-language devotionals use separate collections or locale variants

**Step 3: Set Up Staging (Owner: ITAC)**
- Option A: Create a hidden/draft collection in the live Webflow site for shadow testing
- Option B: Duplicate the Webflow site to a staging subdomain (preferred if Webflow plan allows)
- Test creating, updating, and publishing a devotional page via API

**Step 4: Validate Field Mapping (Owner: ITAC + Publishing Team)**
- Create a test devotional page via API
- Publishing Team compares it against a manually created page
- Confirm: formatting matches, audio player works, images display, links are correct
- Iterate until API-created pages are indistinguishable from manual ones

### API Rate Limits & Costs

- Webflow CMS API: 60 RPM on CMS plan, 120 RPM on Business plan
- For Phase 1 (2 devotionals/day × 4 languages = 8 items + publish calls): well within limits even on CMS plan
- AI translation (Gemini): minimal cost per devotional (~$0.01-0.05 per translation)
- TTS (ElevenLabs): ~$0.30-1.00 per devotional per language depending on length and plan
- S3 storage: negligible (~$0.023/GB/month)

---

## Content Change → Re-pipeline Flow

```
Editor updates English devotional in Platform
        │
        ▼
Platform detects change (diff against previous version)
        │
        ├── Content changed? ──→ Re-run translation for all languages
        │                              │
        │                              ▼
        │                     Re-run TTS for changed translations
        │                              │
        │                              ▼
        │                     Update Webflow pages (English + all languages)
        │
        ├── Metadata only? ──→ Update Webflow page metadata only
        │   (date, scripture ref)
        │
        └── No meaningful change ──→ No action
```

Each platform entry stores:
- `webflowItemId` — the Webflow CMS collection item ID
- `webflowCollectionId` — which Webflow collection it belongs to
- `lastSyncedAt` — timestamp of last successful Webflow sync
- `syncStatus` — `synced` | `pending` | `error`
- `contentHash` — hash of content for change detection

---

## Phased Rollout

### Phase 0: Foundation (Current)
- [x] Translation pipeline prototype (the-global-voice)
- [x] AI translation with theological guardrails (Gemini)
- [x] TTS generation (ElevenLabs / Gemini)
- [x] Role-based access (Admin, Editor, Volunteer)
- [x] Website structure analysis

### Phase 1: Daily + Daybreak Devotional Integration
**Goal:** Both devotionals enter our platform once → auto-publish to Webflow in English + 3 languages with TTS audio

Scope:
- Content entry UI for Daily Devotional and Daybreak Devotional (adapt from the-global-voice)
- Daybreak supports structured sections (Overview, Background, Amplified Outline, Closer Look, Conclusion)
- Webflow CMS API integration (create/update collection items)
- Content ↔ Webflow mapping table
- Translation pipeline (English → ES, FR, KO) with beta auto-approve
- TTS audio generation for all 4 languages (including English)
- Change detection: content update → re-translate → re-generate audio → re-sync Webflow
- Admin dashboard: pipeline status, sync status per entry

Explicitly out of scope for Phase 1:
- Supplementary study materials (Bible Book, Introduction, Outline, Maps, Timeline) — continue manual Webflow management
- Manual audio override (replace TTS with human recording) — data model should support it, but UI/workflow deferred
- Historical content backfill — only new devotionals going forward

Success criteria:
- Editor types devotional once → it appears on apostolicfaith.org in 4 languages with TTS audio
- Content update triggers automatic re-processing and Webflow update
- Both Daily and Daybreak handled through the same workflow

See **Phase 1 Timeline** section above for detailed schedule.

### Phase 2: Sunday School / Curriculum
**Goal:** Weekly lesson publishing automated

Additional scope:
- Age-group variants (Primary Pals, Answer, Search)
- Student/Teacher edition management
- PDF generation from structured content
- "This Week's Lessons" page auto-update
- Devotional supplementary study materials (Bible Book, Maps, Timeline) — moved from manual Webflow to platform

### Phase 3: Magazine (The Apostolic Faith)
**Goal:** Quarterly magazine issue building and multilingual publishing

Additional scope:
- Issue builder (group articles into volume)
- Volume landing page auto-generation
- Article-level translation and audio
- Archive management

### Phase 4: World Report & Events
**Goal:** Regional reporting and event management

Additional scope:
- Regional editor submissions
- Camp Meeting registration system
- Calendar integration
- Photo/media gallery management

### Phase 5: Content Backfill
**Goal:** Migrate historical content into the platform

Scope:
- Import past Daily Devotionals (archive back to Nov 2022) into platform DB
- Import past Daybreak Devotionals
- Import past magazine articles and volumes
- Generate translations and TTS for historical content (batch processing)
- Map imported content to existing Webflow pages
- Optional: re-generate Webflow pages from platform for consistency

### Phase 6: Full Platform (Webflow Replacement)
**Goal:** Our platform serves the public website directly

Additional scope:
- Public-facing frontend (Next.js or similar)
- SEO migration strategy
- Custom domain and DNS transition
- Legacy URL redirects
- Full CMS for all page types
- Manual audio override UI (replace TTS with human recordings for selected entries)

---

## Translation Strategy

### Beta Mode (Phases 1-2)
- 3 target languages: Spanish, French, Korean
- Auto-approve AI translations (no volunteer review gate)
- Mark translated content with "Beta Translation" indicator
- Collect feedback mechanism for translation quality
- Theological guardrail still runs (flag issues but don't block)

### Production Mode (Phase 3+)
- Expand to all 9 languages (Arabic, Italian, Portuguese, Russian, Romanian, Vietnamese + Indian languages)
- Optional volunteer review for high-visibility content (magazine articles)
- Quality metrics tracking per language
- Translator feedback loop to improve AI prompts

---

## Risk Assessment

### Technical Risks

- **Webflow plan/API access:** CMS API requires CMS plan ($23/mo) or higher. If apostolicfaith.org is on Starter/Basic, a plan upgrade is needed — potential budget blocker. Mitigation: verify plan in Week 1, request upgrade approval early if needed.
- **Webflow API limitations:** Rate limits, field type restrictions, or feature gates could block integration. Mitigation: audit API early (Week 1-2), confirm capabilities before committing.
- **Translation quality:** AI translations may have theological inaccuracies. Mitigation: theological guardrail catches major issues; beta label sets expectations; feedback loop improves quality over time.
- **TTS voice quality:** All audio (including English) will be TTS-generated. May sound unnatural in certain languages. Mitigation: test multiple TTS providers and voices; data model supports future manual audio override (swap TTS with human recording per entry) but override UI is not in Phase 1 scope.
- **Webflow site structure changes:** If the Publishing Team changes Webflow page structure, sync may break. Mitigation: mapping table makes dependencies explicit; monitoring alerts on sync failures.

### Operational Risks

- **Publishing Team adoption:** Team may resist workflow change or find the platform harder to use. Mitigation: involve them early (Step 2), dogfood period for hands-on experience, ensure the platform is genuinely easier than Webflow.
- **Dual workflow burden:** During transition, team may need to maintain both manual and platform processes. Mitigation: Shadow/Dogfood phases run in parallel without requiring Publishing Team to change anything; switch only at Beta.
- **Scope creep:** Pressure to add more content types before Phase 1 is solid. Mitigation: strict phase gates; Phase 1 must reach GA before expanding scope.

### Resource Risks

- **Development capacity:** ITAC members have other responsibilities. Mitigation: realistic timeline with buffer; Phase 1 scoped tightly.
- **API costs:** Translation and TTS have per-use costs. Mitigation: costs are low for Phase 1 volume (2 devotionals/day × 4 languages × 2 services = ~$5-10/day); monitor and project before scaling.

---

## Key Design Decisions to Make

1. **Evolve the-global-voice or build new?**
   - TGV is a solid prototype with working pipeline
   - Could refactor to add Webflow integration, or start fresh with lessons learned
   - Recommendation: Evolve — the core pipeline logic is proven

2. **Webflow locale system vs. separate collections per language?**
   - Webflow supports multi-locale CMS items
   - But AFC may have language-specific pages in different URL structures
   - Need to audit current Webflow setup for foreign language content

3. **Where does the platform run?**
   - Vercel (current TGV setup) — good for Next.js, serverless
   - AWS (already using S3) — more control, could consolidate
   - Hybrid: Vercel for app, AWS for storage/processing

4. **Content entry format**
   - Rich text editor (TipTap, already in TGV)
   - Markdown
   - Structured JSON (for curriculum with fixed sections)
   - Recommendation: Rich text for devotionals/articles, structured for curriculum

5. **Authentication unification**
   - TGV has its own auth (NextAuth)
   - Webflow has minister login system
   - Long-term: single SSO for all AFC digital services

---

## Reference

- the-global-voice repo: `apostolicfaith-dev/the-global-voice` (translation pipeline prototype)
- afcgeomap repo: `apostolicfaith-dev/afcgeomap` (magazine article geographic map)
- Webflow CMS API: https://developers.webflow.com/data/reference/cms
- Current website: https://apostolicfaith.org/
- Design docs site: https://apostolicfaith-dev.github.io/afc_design/
