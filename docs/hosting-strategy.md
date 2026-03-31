# ITAC Hosting Strategy — Consolidated Platform

> - Created: 2026-03-31
> - Last Updated: 2026-03-31
> - Status: Draft — Pending ITAC Review
> - Related: [integration-strategy.md](./integration-strategy.md)

## Summary

**Recommendation: Cloud-first with nonprofit credits.** ITAC members all have day jobs — minimizing ops burden is the priority. Nonprofit credits cover most or all costs for the foreseeable future. Self-hosting reserved for dev/staging and GPU workloads only.

**Key numbers:**
- Google Cloud nonprofit credit: up to $10,000/year
- Azure nonprofit credit: ~$3,500/year (+ M365 Business Basic: 300 licenses free)
- AWS nonprofit credit: ~$2,000/year
- Estimated monthly spend (all ITAC initiatives): $50–150/mo initially → within credit range for any provider

**Context:** AFC is already adopting Microsoft 365 nonprofit organization-wide. This is a significant factor — the cloud compute platform decision should align with (or at minimum not conflict with) the church's broader IT direction.

**Open decision: Azure vs. GCP for cloud compute.** Both are viable. See comparison below — ITAC should review and decide.

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

**Nonprofit offering:**
- M365 Business Basic: up to 300 licenses free (email, Teams, SharePoint)
- Azure credit: ~$3,500/year
- 501(c)(3) eligible, well-documented application process

**Compute stack:**
- Azure Container Apps (serverless containers) — web apps + workers
- Azure Database for PostgreSQL — managed DB
- Azure Blob Storage — audio/assets
- Azure Functions — event-driven tasks (Twilio webhooks, scheduled jobs)

**Advantages:**
- **AFC is already adopting M365 nonprofit** — Azure aligns with church-wide IT direction
- Entra ID (Azure AD) for SSO — natural fit if church moves to Entra for identity
- Good nonprofit program, well-documented
- Container Apps is straightforward for deploying web apps + workers

**Disadvantages:**
- Azure portal UX can be overwhelming
- Container Apps less mature than GCP's Cloud Run
- Lower cloud compute credit ($3,500/yr) — but M365 value offsets this

### Google Cloud Platform (GCP)

**Nonprofit offering:**
- Google Cloud: up to $10,000/year credit
- Google Maps Platform: $250/mo credit
- 501(c)(3) eligible, via Google for Nonprofits

**Compute stack:**
- Cloud Run — serverless containers for web apps + workers
- Cloud SQL (PostgreSQL) — managed DB
- Cloud Storage — audio/assets
- Cloud Functions — event-driven tasks

**Advantages:**
- Highest cloud compute credit ($10,000/yr) — most headroom for growth
- Cloud Run is simpler and more mature than Azure Container Apps
- Gemini AI integration is native (already using Gemini for translation)
- Firebase Auth available for SSO
- Simpler developer experience overall

**Disadvantages:**
- **Church is going M365, not Google Workspace** — adds a second ecosystem
- Fewer enterprise identity features vs Azure Entra ID
- Two vendor relationships to manage instead of one

### AWS

**Nonprofit offering:**
- $2,000/year credit (via TechSoup)
- Free tier: 12 months for many services

**Compute stack:**
- ECS/Fargate or App Runner — container hosting
- RDS PostgreSQL — managed DB
- S3 — gold standard for object storage

**Advantages:**
- Most services, most documentation, most Stack Overflow answers
- S3 is the best object storage
- Already considering AWS Polly for TTS

**Disadvantages:**
- Lowest nonprofit credit ($2,000)
- No collaboration suite
- Most complex to configure
- App Runner is limited vs Cloud Run / Container Apps

---

## Azure vs. GCP — ITAC Decision Required

Both are strong options. The key tradeoff:

**Case for Azure:**
- AFC is already going M365 nonprofit → one vendor ecosystem, one billing relationship
- Entra ID gives church-wide SSO potential (not just ITAC apps)
- M365 + Azure under one nonprofit account = simpler administration
- $3,500/yr cloud credit is enough for projected workloads ($360–840/yr estimated)

**Case for GCP:**
- $10,000/yr credit vs $3,500/yr — 3x headroom for growth or unexpected costs
- Cloud Run is a better developer experience for our serverless container workloads
- Native Gemini integration (already using Gemini for translation pipeline)
- M365 for collaboration + GCP for compute is a valid split (many orgs do this)

**Not recommended: AWS.** Lowest credit, no collaboration suite, most complex. Individual AWS services (S3, Polly) can be used standalone regardless of primary cloud choice.

**Key question for ITAC:** Does the organizational alignment of "everything Microsoft" outweigh GCP's technical and credit advantages for compute? There's no wrong answer — both work. This is a strategic alignment decision more than a technical one.

---

## Proposed Architecture (Provider-Agnostic)

The architecture is the same regardless of Azure or GCP. Service names differ but the pattern is identical: serverless containers + managed Postgres + object storage.

```
                    ┌──────────────────────────────────────┐
                    │     Cloud Platform (Azure or GCP)     │
                    │         Nonprofit Credits             │
                    │                                       │
                    │  ┌───────────┐  ┌──────────────────┐  │
                    │  │ Container │  │  Container        │  │
                    │  │ Services  │  │  Workers          │  │
                    │  │ (Web Apps)│  │                   │  │
                    │  │           │  │• Translation      │  │
                    │  │• AFC      │  │• TTS Generation   │  │
                    │  │  Platform │  │• Webflow Sync     │  │
                    │  │• Camp     │  │• Scheduled Jobs   │  │
                    │  │  Meeting  │  │                   │  │
                    │  │• Comms API│  │                   │  │
                    │  └─────┬─────┘  └────────┬─────────┘  │
                    │        │                 │            │
                    │        ▼                 ▼            │
                    │  ┌───────────┐  ┌──────────────────┐  │
                    │  │ Managed   │  │  Object Storage   │  │
                    │  │ Postgres  │  │  (Audio, Assets)  │  │
                    │  └───────────┘  └──────────────────┘  │
                    │                                       │
                    │  ┌──────────────────────────────────┐  │
                    │  │  Shared Services                 │  │
                    │  │  • Auth / SSO                    │  │
                    │  │  • Job Queue                     │  │
                    │  │  • Secrets Management            │  │
                    │  │  • Monitoring & Alerts           │  │
                    │  └──────────────────────────────────┘  │
                    └──────────────────────────────────────┘
                                     │
                     ┌───────────────┼───────────────┐
                     ▼               ▼               ▼
              ┌──────────┐   ┌───────────┐   ┌─────────────┐
              │ Webflow  │   │ Twilio    │   │ External    │
              │ CMS API  │   │           │   │ AI APIs     │
              └──────────┘   └───────────┘   │ (Gemini,    │
                                             │  Polly/TTS) │
                                             └─────────────┘
```

**Service mapping (either provider works):**

**Azure →** Container Apps, Azure Database for PostgreSQL, Blob Storage, Entra ID, Azure Functions, Key Vault, Azure Monitor

**GCP →** Cloud Run, Cloud SQL, Cloud Storage, Firebase Auth, Cloud Tasks, Secret Manager, Cloud Monitoring

### Per-Workload Mapping

**Global Voice / AFC Platform:**
- Container service (web app) — content entry UI, admin dashboard
- Container service (workers) — translation, TTS, Webflow sync pipelines
- Managed Postgres — content DB, Webflow mapping, translation records
- Object storage — audio files, generated assets

**Camp Meeting:**
- Container service (web app) — registration form, admin panel
- Managed Postgres — shared instance, separate schema
- Job queue — email notifications

**Communication System:**
- Container service (API) — Twilio webhook handlers
- Managed Postgres — contacts, message logs
- Scheduled jobs — automated message triggers

**Shared Infrastructure:**
- Auth service — unified authentication across all apps (Entra ID or Firebase Auth)
- Managed Postgres — single instance, separate databases per service
- Secrets management — API keys, credentials
- Monitoring + alerting — uptime, error tracking

---

## Cost Estimate (Monthly)

Cloud compute costs are similar on Azure and GCP for this scale of workload:

- Container services (3 apps + workers): $10–30/mo — serverless, pay per use
- Managed Postgres (1 instance): $15–30/mo — smallest tier
- Object storage: $2–5/mo — audio files, assets
- Job queue / scheduler: <$1/mo
- Auth service: $0 — free tier
- Secrets / monitoring: <$1/mo
- **Cloud subtotal: $30–70/mo** ($360–840/yr)

External costs (regardless of cloud provider):
- Webflow CMS plan: $23/mo (required for API access)
- AI/TTS APIs (Gemini, Polly, etc.): $10–15/mo

**Total: $65–110/mo**

**Credit coverage:**
- Azure ($3,500/yr): covers cloud portion comfortably, ~10–24% utilization
- GCP ($10,000/yr): covers cloud portion with large headroom, ~4–8% utilization
- Both providers: external costs (Webflow, AI APIs) paid separately, ~$35–40/mo

---

## Migration Path

**Phase 1 (Now → June 2026):**
1. ITAC decides on primary cloud provider (Azure vs. GCP)
2. Apply for nonprofit credits on chosen provider (requires 501(c)(3))
3. Set up cloud project/subscription with proper access for ITAC members
4. Deploy Global Voice / AFC Platform (container service + managed DB)
5. Configure CI/CD (GitHub Actions → chosen platform)

**Phase 2 (Post-GA):**
6. Deploy Camp Meeting registration on same infrastructure
7. Deploy Communication System
8. Implement shared auth/SSO (Entra ID or Firebase Auth)
9. Evaluate secondary provider credits for backup/cost optimization

---

## Self-Hosted GPU Addendum

VibeVoice (English-only open-source TTS) requires GPU. If ITAC decides to use VibeVoice:
- Run on a self-hosted machine with GPU (donated hardware)
- Expose as an internal API, consumed by Cloud Run workers
- This is isolated — doesn't affect the cloud-first architecture
- Only relevant if TTS comparison favors VibeVoice for English

---

## Action Items

- [ ] ITAC review this document and decide: Azure or GCP as primary cloud
- [ ] Verify AFC 501(c)(3) documentation is ready for nonprofit applications
- [ ] Apply for nonprofit credits on chosen primary provider
- [ ] Apply for nonprofit credits on secondary provider (backup / cost optimization)
- [ ] Set up cloud project with access roles for ITAC members
- [ ] Prototype deployment of one service to validate the platform choice

---

## Reference

- [Google Cloud for Nonprofits](https://www.google.com/nonprofits/)
- [Microsoft Nonprofits](https://www.microsoft.com/en-us/nonprofits/)
- [AWS Nonprofit Credit Program](https://aws.amazon.com/government-education/nonprofits/nonprofit-credit-program/)
- [Integration Strategy](./integration-strategy.md)
- [Webflow API Setup & Costs](./webflow-api-setup.md)
