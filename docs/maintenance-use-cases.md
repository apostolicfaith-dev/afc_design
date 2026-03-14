# AFC Website & Content — Maintenance Use Cases

> - Created: 2026-03-14
> - Status: Draft (high-level)
> - Purpose: Map who does what, how often, and where automation can reduce manual effort

## Summary

Running the AFC website takes at least 8 different roles — editors, translators, media producers, curriculum coordinators, and more. Every day, someone spends 1.5-2.5 hours just copy-pasting two devotionals into Webflow and attaching audio. Every week, 6+ Sunday school documents get manually uploaded. Every quarter, a magazine issue means creating 10-15 article pages, then repeating that in up to 9 languages. Translation alone could mean 8,000-10,000 content items per year if they fully served all languages. Right now, every step — writing, translating, recording audio, publishing, distributing — is a manual handoff with no unified system connecting them.

---

## Roles & Permissions

### R1. System Administrator
- Webflow site structure, DNS, hosting, integrations
- User account management (minister logins, staff access)
- Deployment, backups, platform-level changes

### R2. Content Editor (HQ Editorial Team)
- Daily devotionals (Daily + Daybreak)
- Magazine articles
- World Report entries
- General page content updates

### R3. Curriculum Coordinator
- Sunday School materials (Primary Pals, Answer, Search)
- Bible study series (Discipleship, Heritage, etc.)
- Daybreak & Discovery study cycle (3-year rotation)
- Teacher/student material packaging

### R4. Translation Coordinator
- Coordinates translation across 9 languages
- Reviews translated content
- Manages translator workforce (likely volunteer-heavy)

### R5. Media Producer
- Audio recording / TTS for devotionals
- Sermon recording and editing
- Video production and upload (Vimeo)
- Podcast distribution
- Live webcast operation

### R6. Event Coordinator
- Camp Meeting registration and logistics
- Portland calendar events
- Special events (Christmas on the Campground, etc.)

### R7. Minister Resources Manager
- Gated content for ministers (documents, videos, training)
- Minister account approval (application review, 24hr turnaround)
- Minister-specific communications

### R8. Subscription / Communications Manager
- Email newsletter lists (devotional, global updates, Portland news)
- Social media posting (Instagram, Facebook, YouTube, Vimeo)

---

## Maintenance Cadence

### DAILY

| Task | Role | Current Process (estimated) | Pain Level |
|------|------|-----------------------------|------------|
| **Publish Daily Devotional** | R2 | Write/receive content → copy-paste into Webflow → upload images → format → attach study materials links → publish | High |
| **Publish Daybreak Devotional** | R2 | Write/receive content (Overview, Background, Outline, Closer Look, Conclusion) → copy-paste into Webflow → format sections → publish | High |
| **Produce devotional audio** | R5 | Record or run TTS → edit audio → upload to Webflow/hosting → link to devotional page | High |
| **Live webcast (Sun, Tue, Fri)** | R5 | Start/stop stream → verify quality → archive recording | Medium |

**Daily total:** ~2-4 pieces of content manually built in Webflow, plus audio production.

Estimated time per devotional in Webflow:
- Write/edit content: handled upstream (editorial)
- Create new Webflow CMS item or static page: 15-30 min
- Format, add images, link study aids: 10-20 min
- Produce and attach audio: 15-30 min
- **Per devotional: ~45-80 min → two devotionals/day: ~1.5-2.5 hours**

### WEEKLY

| Task | Role | Current Process (estimated) | Pain Level |
|------|------|-----------------------------|------------|
| **Publish "This Week's Lessons"** | R3 | Gather lessons for all age groups (Primary Pals, Answer, Search) → create/update Webflow page → upload PDFs/images for student and teacher editions | Very High |
| **Upload sermon recordings** | R5 | Edit sermon audio/video → upload to Vimeo → update video archive page → distribute to podcast platforms | Medium |
| **Update podcast feeds** | R5 | New sermon + devotional episodes → upload to podcast host → verify feeds | Medium |
| **Social media scheduling** | R8 | Create posts for upcoming services/events → schedule across platforms | Low-Medium |

**Weekly lesson publishing detail:**
- 3 age groups × 2 editions (student + teacher) = 6 documents minimum
- Each needs: PDF upload, thumbnail, description, proper categorization
- Plus Daybreak & Discovery weekly group study material
- **Estimated: 2-4 hours of manual Webflow work per week**

### MONTHLY / QUARTERLY

| Task | Role | Current Process (estimated) | Pain Level |
|------|------|-----------------------------|------------|
| **Publish Apostolic Faith Magazine** (quarterly) | R2 | Receive final articles → create individual Webflow pages per article → format with images, pull quotes → create volume landing page → link all articles → update archive | Very High |
| **Translate magazine to 9 languages** | R4 | Send articles to translators → receive translations → for each language: create Webflow pages, format, upload → link to Foreign Languages section | Extreme |
| **Translate devotionals / curriculum** | R4 | Batch translations → create pages per language → audio production per language (if applicable) | Extreme |
| **World Report updates** | R2 | Receive reports from regional HQs → write/edit articles → upload photos → tag by region → publish to World Report section | Medium-High |
| **Calendar updates** | R6 | Add/remove events from calendar page manually | Medium |
| **Minister resource updates** | R7 | Upload new documents/videos → create or update gated pages | Medium |

**Quarterly magazine publishing detail:**
- ~10-15 articles per issue
- Each article: Webflow page creation, formatting, images
- Volume landing page with all article links
- Then multiply by 9 languages (partial — not all articles translated)
- **Estimated: 1-2 weeks of cumulative work per quarterly issue across languages**

### ANNUALLY / SEASONAL

| Task | Role | Current Process (estimated) | Pain Level |
|------|------|-----------------------------|------------|
| **Camp Meeting setup** | R6 + R1 | Update registration forms → build schedule pages → meal info → guardian policies → housing info → photo gallery setup | High |
| **Camp Meeting registration management** | R6 | Monitor registrations → handle exceptions → communicate with registrants → manage meal plans | High |
| **Camp Meeting media** | R5 | Daily recordings during 2-week event → rapid upload → photo gallery management | Very High |
| **Curriculum cycle rollover** | R3 | 3-year Daybreak cycle planning → prepare next year's materials → archive previous | Medium |
| **Christmas on the Campground** | R6 | Event pages → registration → photo/video → schedule | Medium |
| **Year-end reporting** | R7 + R8 | Statistics gathering from global churches → annual report compilation | Medium |
| **Minister account review** | R7 | Audit active minister accounts → deactivate as needed | Low |

---

## Translation Workflow — Special Attention

This is the single highest-effort, most repetitive process in the entire operation.

**Current flow (estimated):**
1. English content finalized (devotional, article, lesson, etc.)
2. Content sent to translator (email? shared doc?)
3. Translator returns translated text
4. Someone creates a new Webflow page in the target language section
5. Copy-paste translated content, format, add images
6. If audio needed: record or TTS in target language → upload → link
7. Repeat for each language (up to 9 times per piece of content)

**Scale of the problem:**
- 365 Daily Devotionals × 9 languages = 3,285 translated pages/year
- 365 Daybreak Devotionals × 9 languages = 3,285 translated pages/year
- ~52 weekly lessons × multiple age groups × 9 languages
- ~40-60 magazine articles × 9 languages
- World Report articles × relevant languages
- **Conservative estimate: 8,000-10,000+ translated content items per year**

Even if only a fraction is actually translated today, the aspiration to serve all languages creates massive operational overhead.

---

## Audio Production Workflow — Special Attention

**Current flow (estimated):**
1. Content finalized in language
2. Either: professional recording session OR TTS generation
3. Audio editing (intro/outro, quality check)
4. Upload to hosting
5. Link audio player to the correct page in Webflow
6. Distribute to podcast feed if applicable

**Scale:**
- 730 devotionals/year (Daily + Daybreak) in English alone
- Multiply by each language that gets audio
- Sermons: 150-200 recordings/year (Sun AM, Sun PM, Tue, Fri)
- Camp Meeting: ~30 services in 2 weeks

---

## Content Lifecycle Summary

```
[Author/Editorial] → [Translation] → [Audio Production] → [CMS Publishing] → [Distribution]
                           ↓                   ↓                  ↓                  ↓
                      9 languages         TTS/Recording      Webflow manual      Email, Social,
                      volunteer mgmt      per language        copy-paste          Podcast, App
```

Every arrow in this chain is currently a manual handoff. There is no unified pipeline.

---

## Identified Automation Opportunities (High Level)

### A1. Content Management API / Headless CMS
Replace or supplement Webflow with a structured content system where devotionals, lessons, articles are entered once and published everywhere (web, app, email, podcast feed).

### A2. Translation Pipeline
- Auto-draft translations (LLM-based) for translator review
- Structured bilingual editor (English source ↔ target language side by side)
- One-click publish translated version once approved

### A3. Audio Generation
- Automated TTS pipeline per language with quality presets
- Auto-attach audio to content items
- Auto-distribute to podcast feeds

### A4. Curriculum Packaging
- Upload source files (lesson content, images, PDFs) once
- System generates pages for all age groups, teacher/student editions
- Auto-publish weekly schedule

### A5. Event Management
- Integrated registration system (not just a form)
- Attendee management dashboard
- Meal plan tracking
- Automated confirmation/reminder emails

### A6. Minister Portal
- Self-service resource library with search and filtering
- Automated account provisioning
- Role-based access (pastor, associate, evangelist, etc.)

### A7. Publishing Automation
- Content enters system once → auto-generates Webflow pages (or replaces Webflow entirely)
- Magazine issue builder: drag articles into issue → auto-generates volume page + individual article pages
- World Report: regional editors submit directly → editorial review → publish

### A8. Distribution Hub
- Single publish action triggers: website, email newsletter, podcast feed, app push notification, social media draft
- Per-content-type distribution rules (devotional → email + podcast + app; magazine article → email + web)

---

## Next Steps

1. Validate these assumptions with actual AFC staff workflows
2. Prioritize automation opportunities by impact vs. effort
3. Design the target system architecture
4. Define MVP scope — likely A1 (content API) + A2 (translation) + A3 (audio) as the highest-impact trio
