# AFC Platform — Incremental Integration Strategy

> Created: 2026-03-14
> Status: Draft
> Related: [website-structure-analysis.md](./website-structure-analysis.md), [maintenance-use-cases.md](./maintenance-use-cases.md)

## TL;DR

Instead of replacing the entire AFC website at once (risky and slow), we're building a behind-the-scenes platform where content goes in once and automatically gets translated into 3 languages, turned into audio, and published to the current Webflow website via API. We start with just the Daily Devotional, prove it works, then expand to Daybreak, Sunday school, magazine, and so on. Each new feature goes through a release process — first only the team can see it (Shadow), then a small group tests it (Dogfood), then it goes out with a "beta" label, and finally it becomes the official way. Eventually, the platform replaces Webflow entirely.

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
5. **Incremental rollout** — One content type at a time, starting with Daily Devotional
6. **Translation: beta mode first** — 3 languages, auto-approve with beta label, expand later

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

- **Phase 1 (Daily Devotional):** Shadow → Dogfood → Beta → GA
- **Phase 2 (Daybreak):** Same cycle, but faster — pipeline is proven
- **Phase 3 (Curriculum):** Shadow → Dogfood → Beta → GA (new content model, needs full cycle)
- **Phase 4 (Magazine):** Shadow → Dogfood → Beta → GA (high-visibility, quarterly cadence)
- **Phase 5 (Events):** Shadow → Beta → GA (less translation-dependent, simpler cycle)
- **Phase 6 (Full Replacement):** Extended Beta with A/B testing before full DNS cutover

---

## Webflow CMS API — What We Can Do

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

### Phase 1: Daily Devotional Integration
**Goal:** Daily Devotional enters our platform once → auto-publishes to Webflow in English + 3 languages

Scope:
- Content entry UI for Daily Devotional (adapt from the-global-voice)
- Webflow CMS API integration (create/update collection items)
- Content ↔ Webflow mapping table
- Translation pipeline (English → ES, FR, KO) with beta auto-approve
- TTS generation per language
- Change detection: content update → re-translate → re-generate audio → re-sync Webflow
- Admin dashboard: pipeline status, sync status per entry

Success criteria:
- Editor types devotional once → it appears on apostolicfaith.org in 4 languages with audio
- Content update triggers automatic re-processing and Webflow update

### Phase 2: Daybreak Devotional
**Goal:** Same pipeline for Daybreak (more structured content with Overview, Background, Outline, etc.)

Additional scope:
- Structured content model (sections within a devotional)
- Section-level translation (maintain structure across languages)
- Study material attachments (PDFs, maps, timelines)

### Phase 3: Sunday School / Curriculum
**Goal:** Weekly lesson publishing automated

Additional scope:
- Age-group variants (Primary Pals, Answer, Search)
- Student/Teacher edition management
- PDF generation from structured content
- "This Week's Lessons" page auto-update

### Phase 4: Magazine (The Apostolic Faith)
**Goal:** Quarterly magazine issue building and multilingual publishing

Additional scope:
- Issue builder (group articles into volume)
- Volume landing page auto-generation
- Article-level translation and audio
- Archive management

### Phase 5: World Report & Events
**Goal:** Regional reporting and event management

Additional scope:
- Regional editor submissions
- Camp Meeting registration system
- Calendar integration
- Photo/media gallery management

### Phase 6: Full Platform (Webflow Replacement)
**Goal:** Our platform serves the public website directly

Additional scope:
- Public-facing frontend (Next.js or similar)
- SEO migration strategy
- Custom domain and DNS transition
- Legacy URL redirects
- Full CMS for all page types

---

## Translation Strategy

### Beta Mode (Phases 1-3)
- 3 target languages: Spanish, French, Korean
- Auto-approve AI translations (no volunteer review gate)
- Mark translated content with "Beta Translation" indicator
- Collect feedback mechanism for translation quality
- Theological guardrail still runs (flag issues but don't block)

### Production Mode (Phase 4+)
- Expand to all 9 languages (Arabic, Italian, Portuguese, Russian, Romanian, Vietnamese + Indian languages)
- Optional volunteer review for high-visibility content (magazine articles)
- Quality metrics tracking per language
- Translator feedback loop to improve AI prompts

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
