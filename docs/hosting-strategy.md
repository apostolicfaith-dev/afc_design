# ITAC Hosting Strategy — Consolidated Platform

> - Created: 2026-03-31
> - Last Updated: 2026-03-31
> - Status: Draft — Pending ITAC Review
> - Related: [integration-strategy.md](./integration-strategy.md)

## Summary

**Recommendation: Cloud-first (Azure or GCP) with nonprofit credits.** ITAC members all have day jobs — minimizing ops burden is the priority. Nonprofit credits cover most or all costs for the foreseeable future. Self-hosting reserved for dev/staging and GPU workloads only.

**Key numbers:**
- Google Cloud nonprofit credit: up to $10,000/year (highest)
- Azure nonprofit credit: ~$3,500/year
- AWS nonprofit credit: ~$2,000/year
- Estimated monthly spend (all ITAC initiatives): $50–150/mo initially → well within GCP credit range

**Recommendation: GCP as primary.** See evaluation below.

---

## ITAC Workloads

All current and planned initiatives that need hosting:

**1. Global Voice / AFC Platform**
- Web app (content entry UI, admin dashboard)
- Background workers (translation pipeline, TTS generation, Webflow sync)
- Database (content, mappings, translation records)
- Object storage (audio files, assets)
- Priority: High — Phase 1 target Beta mid-June 2026

**2. Camp Meeting Registration**
- Web app (public registration form, admin management)
- Database (registrations, payments, attendee data)
- Email notifications
- Priority: Medium — timeline TBD

**3. Communication System (Twilio-based)**
- API server (Twilio webhook handlers)
- Database (contacts, message logs)
- Scheduled jobs (automated messages)
- Priority: Medium — timeline TBD

**4. Shared Infrastructure**
- OAuth / authentication service (Phase 1: email-based, long-term: SSO)
- Shared database cluster or managed DB
- Inter-service API/RPC layer
- DNS management
- Priority: High — foundation for everything else

---

## Options Evaluated

### Option A: Cloud-First (Recommended)

One cloud provider as primary, nonprofit credits cover costs. Serverless-first architecture (Cloud Run / Azure Container Apps), managed DB, managed storage.

- **Pros:** Minimal ops, auto-scaling, built-in monitoring, HA by default, credits cover costs
- **Cons:** Vendor lock-in, GPU workloads expensive on cloud
- **Cost:** $50–150/mo → covered by nonprofit credits
- **Ops burden:** Low — managed services handle patching, backups, scaling

### Option B: Self-Hosting First

All services on owned/donated hardware. Cloud only for CDN/backup.

- **Pros:** Full control, no recurring fees (after hardware), local GPU for VibeVoice
- **Cons:** Single point of failure, maintenance burden, network/security management, no auto-scaling, downtime = ITAC member's weekend
- **Cost:** Hardware upfront + electricity + internet, $0 recurring cloud
- **Ops burden:** High — everything is manual

### Option C: Hybrid

Production on cloud, dev/staging + GPU workloads on self-hosted.

- **Pros:** Flexible, cost-optimized, local GPU available
- **Cons:** Two environments to manage, networking complexity, deployment pipeline complexity
- **Cost:** Cloud portion covered by credits, self-hosted portion = hardware cost
- **Ops burden:** Medium — cloud is managed, self-hosted needs attention

**Decision: Option A** with a note that self-hosted GPU (VibeVoice experiments) can run independently without affecting production architecture.

---

## Cloud Provider Comparison

### Azure

**For ITAC:**
- M365 Business Basic: up to 300 licenses free (email, Teams, SharePoint)
- Azure nonprofit credit: ~$3,500/year
- Azure Container Apps (serverless containers) — good fit for our workloads
- Azure Database for PostgreSQL — managed, free tier covers small workloads
- Azure Blob Storage — for audio/assets
- Azure Functions — event-driven tasks (Twilio webhooks, scheduled jobs)

**Advantages:**
- M365 licenses are a huge bonus (professional email, Teams for ITAC collaboration)
- Good nonprofit program, well-documented
- Container Apps is straightforward for deploying web apps + workers
- Active Directory integration if SSO needed later

**Disadvantages:**
- Azure portal UX can be overwhelming
- Container Apps less mature than Cloud Run

### Google Cloud Platform (GCP)

**For ITAC:**
- Google Workspace: free for nonprofits (custom domain email, Drive, Meet)
- Google Cloud: up to $10,000/year nonprofit credit (+ $250/mo Google Maps credit)
- Cloud Run — excellent serverless container platform
- Cloud SQL (PostgreSQL) — managed DB
- Cloud Storage — for audio/assets
- Cloud Functions — event-driven tasks

**Advantages:**
- Cloud Run is arguably the best serverless container platform (simpler than Azure Container Apps)
- Google Workspace is very polished (email, calendar, Drive)
- Gemini AI integration is native (already using Gemini for translation)
- Firebase Auth available for SSO later

**Disadvantages:**
- Nonprofit credit amount less transparent (varies)
- Fewer enterprise features vs Azure

### AWS

**For ITAC:**
- $2,000/year nonprofit credit (via TechSoup)
- Free tier: 12 months for many services
- ECS/Fargate or App Runner — container hosting
- RDS PostgreSQL — managed DB
- S3 — gold standard for object storage

**Advantages:**
- Most services, most documentation, most Stack Overflow answers
- S3 is the best object storage
- Already using AWS Polly for TTS consideration

**Disadvantages:**
- Lowest nonprofit credit ($2,000 vs Azure's $3,500)
- No free email/collaboration suite
- Most complex to configure
- App Runner is limited vs Cloud Run

---

## Recommendation: GCP as Primary

**Why GCP over Azure:**

1. **Cloud Run** is simpler and more mature for our use case (containerized web apps + background workers)
2. **Google Workspace** covers collaboration needs (email, Drive, Meet) — equivalent to Azure's M365 advantage
3. **Gemini integration** — we're already using Gemini for translation. Native GCP integration reduces friction
4. **Simpler developer experience** — GCP console is more intuitive than Azure portal
5. **Cloud Run + Cloud SQL + Cloud Storage** is a clean, minimal stack that covers all four workloads

**Why not Azure:** M365 is attractive but ITAC doesn't need 300 email licenses. Azure's strength is enterprise — we're a small team with lightweight workloads.

**Why not AWS:** Lowest nonprofit credit, no free collaboration suite, most complex setup.

**Use AWS for:** S3 storage (if GCP Storage pricing isn't competitive) and AWS Polly (if chosen as TTS provider). These are standalone services that don't require migrating the whole stack.

---

## Proposed Architecture

```
                    ┌─────────────────────────────────────┐
                    │         Google Cloud (GCP)           │
                    │         Nonprofit Credits            │
                    │                                      │
                    │  ┌──────────┐  ┌──────────────────┐  │
                    │  │Cloud Run │  │  Cloud Run        │  │
                    │  │(Web Apps)│  │  (Workers)        │  │
                    │  │          │  │                   │  │
                    │  │• AFC     │  │• Translation      │  │
                    │  │  Platform│  │• TTS Generation   │  │
                    │  │• Camp    │  │• Webflow Sync     │  │
                    │  │  Meeting │  │• Scheduled Jobs   │  │
                    │  │• Comms   │  │                   │  │
                    │  │  API     │  │                   │  │
                    │  └────┬─────┘  └────────┬──────────┘  │
                    │       │                 │             │
                    │       ▼                 ▼             │
                    │  ┌──────────┐  ┌──────────────────┐  │
                    │  │Cloud SQL │  │  Cloud Storage    │  │
                    │  │(Postgres)│  │  (Audio, Assets)  │  │
                    │  └──────────┘  └──────────────────┘  │
                    │                                      │
                    │  ┌──────────────────────────────────┐ │
                    │  │  Shared Services                 │ │
                    │  │  • Firebase Auth (SSO)           │ │
                    │  │  • Cloud Tasks (job queue)       │ │
                    │  │  • Secret Manager                │ │
                    │  │  • Cloud Monitoring              │ │
                    │  └──────────────────────────────────┘ │
                    └─────────────────────────────────────┘
                                    │
                    ┌───────────────┼───────────────┐
                    ▼               ▼               ▼
              ┌──────────┐  ┌───────────┐  ┌─────────────┐
              │ Webflow  │  │ Twilio    │  │ External    │
              │ CMS API  │  │           │  │ AI APIs     │
              └──────────┘  └───────────┘  │ (Gemini,    │
                                           │  Polly/TTS) │
                                           └─────────────┘
```

### Per-Workload Mapping

**Global Voice / AFC Platform:**
- Cloud Run (web app) — content entry UI, admin dashboard
- Cloud Run (workers) — translation, TTS, Webflow sync pipelines
- Cloud SQL — content DB, Webflow mapping, translation records
- Cloud Storage — audio files, generated assets

**Camp Meeting:**
- Cloud Run (web app) — registration form, admin panel
- Cloud SQL — shared DB, separate schema
- Cloud Tasks — email notifications

**Communication System:**
- Cloud Run (API) — Twilio webhook handlers
- Cloud SQL — contacts, message logs
- Cloud Scheduler — automated message triggers

**Shared Infrastructure:**
- Firebase Auth — unified authentication across all apps
- Cloud SQL — single managed instance, separate databases per service
- Secret Manager — API keys, credentials
- Cloud Monitoring + Alerting — uptime, error tracking

---

## Cost Estimate (Monthly)

| Service | Estimate | Notes |
|---------|----------|-------|
| Cloud Run (3 services) | $10–30 | Serverless, pay per use. Low traffic = low cost |
| Cloud SQL (1 instance) | $15–30 | Smallest managed Postgres instance |
| Cloud Storage | $2–5 | Audio files, assets |
| Cloud Tasks / Scheduler | <$1 | Low volume |
| Firebase Auth | $0 | Free for <50K MAU |
| Secret Manager | <$1 | |
| Monitoring | $0 | Free tier covers our needs |
| **Subtotal (GCP)** | **$30–70/mo** | |
| External: Webflow CMS | $23/mo | Required for API access |
| External: AI/TTS APIs | $10–15/mo | Gemini translation + TTS provider |
| **Total** | **$65–110/mo** | Well within nonprofit credit range |

**Nonprofit credit coverage:** GCP portion ($30–70/mo = $360–840/yr) is comfortably within GCP's $10,000/yr nonprofit credits — using under 10% of the annual allowance. External costs (Webflow, AI APIs) are paid separately but small.

---

## Migration Path

**Phase 1 (Now → June 2026):**
1. Apply for Google Cloud nonprofit credits (requires 501(c)(3) verification)
2. Apply for Google Workspace nonprofit (free email/collaboration)
3. Set up GCP project with proper IAM for ITAC members
4. Deploy Global Voice / AFC Platform on Cloud Run
5. Set up Cloud SQL (shared instance)
6. Configure CI/CD (GitHub Actions → Cloud Run)

**Phase 2 (Post-GA):**
7. Deploy Camp Meeting registration on same infrastructure
8. Deploy Communication System
9. Implement Firebase Auth as shared SSO
10. Evaluate: migrate DNS to Cloud DNS if needed

---

## Self-Hosted GPU Addendum

VibeVoice (English-only open-source TTS) requires GPU. If ITAC decides to use VibeVoice:
- Run on a self-hosted machine with GPU (donated hardware)
- Expose as an internal API, consumed by Cloud Run workers
- This is isolated — doesn't affect the cloud-first architecture
- Only relevant if TTS comparison favors VibeVoice for English

---

## Action Items

- [ ] Verify AFC 501(c)(3) status documentation is ready for nonprofit applications
- [ ] Apply for Google Cloud for Nonprofits (via Google for Nonprofits / Goodstack)
- [ ] Apply for Google Workspace for Nonprofits
- [ ] Also apply for Azure nonprofit (M365 + Azure credits) as backup / secondary
- [ ] Set up GCP project and IAM roles for ITAC members
- [ ] ITAC review and approve this strategy

---

## Reference

- [Google Cloud for Nonprofits](https://www.google.com/nonprofits/)
- [Microsoft Nonprofits](https://www.microsoft.com/en-us/nonprofits/)
- [AWS Nonprofit Credit Program](https://aws.amazon.com/government-education/nonprofits/nonprofit-credit-program/)
- [Integration Strategy](./integration-strategy.md)
- [Webflow API Setup & Costs](./webflow-api-setup.md)
