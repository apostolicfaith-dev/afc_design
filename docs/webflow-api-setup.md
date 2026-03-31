# Webflow CMS API — Setup & Cost Reference

> - Created: 2026-03-14
> - Last Updated: 2026-03-31
> - Parent: [integration-strategy.md](./integration-strategy.md)

This document covers the technical setup steps and cost details for Webflow API integration. See the [Integration Strategy](./integration-strategy.md) for overall direction and scope.

---

## What the API Can Do

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

## Plan Requirement

CMS API access requires **Webflow CMS plan ($23/mo) or higher**. Starter and Basic plans do not include API access.

- **CMS plan ($23/mo):** 2,000 CMS items, 60 requests/minute — sufficient for Phase 1
- **Business plan ($39/mo):** 20,000 CMS items, 120 requests/minute — needed if scaling to all content types
- **Action item:** Confirm apostolicfaith.org is on CMS plan or higher. If on Basic/Starter, a plan upgrade is needed before API integration can begin. This is a **potential blocker** — must be verified in Week 1.

---

## Setup Steps

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
- Identify the collections used for both Daily and Daybreak Devotionals (fields, structure, slugs)
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

---

## API Rate Limits

- **CMS plan:** 60 RPM — Phase 1 (2 devotionals/day × 4 languages = 8 items + publish calls) is well within limits
- **Business plan:** 120 RPM — needed at scale

---

## TTS Provider Cost Comparison (per 1 million characters)

- **AWS Polly Standard:** $4/M chars (free tier: 5M chars/month for 12 months)
- **AWS Polly Neural:** $16/M chars (free tier: 1M chars/month for 12 months)
- **Google Cloud TTS Standard:** $4/M chars (free tier: 4M chars/month)
- **Google Cloud TTS WaveNet:** $16/M chars (free tier: 1M chars/month)
- **Google Cloud TTS Neural2/Journey:** $16/M chars (free tier: 1M chars/month)
- **Azure Neural TTS:** $15/M chars (free tier: 0.5M chars/month)
- **ElevenLabs (Scale plan):** $180/M chars — highest quality but 10-45x more expensive
- **ElevenLabs (Pro plan):** $240/M chars
- **Gemini TTS:** Included with Gemini API usage, minimal additional cost
- **VibeVoice (Microsoft, open-source):** $0 license (MIT), self-hosted. English only officially supported; other languages experimental. Requires GPU hardware for inference.

A formal quality comparison across providers is planned before final TTS selection.

### Phase 1 Cost Estimate

- Average devotional: ~2,000 characters
- 2 devotionals/day × 4 languages = ~16,000 chars/day = ~480,000 chars/month
- At AWS Polly Neural: ~$7.70/month
- At Google Cloud WaveNet: ~$7.70/month
- At ElevenLabs Scale: ~$86/month

### Phase 1.5 Backfill Cost Estimate

- ~1,200 days of archived devotionals × 2 types × 4 languages × ~2,000 chars = ~19.2M characters
- At AWS Polly Neural: ~$307 one-time
- At ElevenLabs Scale: ~$3,456 one-time
- This is why Backfill timing is budget-dependent.
