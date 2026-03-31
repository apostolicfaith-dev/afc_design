# AFC Service Design

Service design and planning repository for Apostolic Faith Church digital platform modernization.

## Purpose

The Apostolic Faith Church (headquartered in Portland, Oregon) operates [apostolicfaith.org](https://apostolicfaith.org/) — serving 3,000+ churches across 60+ countries. The current website runs on Webflow CMS, requiring extensive manual work for content publishing, multilingual translation (9 languages), audio production, and event management.

This repository documents the design and strategy for building an **AFC Platform** — an internal content management system that automates translation, audio generation, and publishing. The platform will initially integrate with Webflow via API, then incrementally replace it.

## Related Repositories

- [`apostolicfaith-dev/the-global-voice`](https://github.com/apostolicfaith-dev/the-global-voice) — Translation pipeline prototype (Next.js + Gemini AI + TTS)
- [`apostolicfaith-dev/afcgeomap`](https://github.com/apostolicfaith-dev/afcgeomap) — Interactive map of AFC magazine articles by geographic location ([live site](https://apostolicfaith-dev.github.io/afcgeomap/))

## Documents

### [Website Structure Analysis](docs/website-structure-analysis.md)
Full breakdown of apostolicfaith.org — navigation, content sections (devotionals, magazine, curriculum, media, events), multilingual support, and identified limitations.

### [Maintenance Use Cases](docs/maintenance-use-cases.md)
Who does what, how often. Maps 8 roles across daily/weekly/monthly/annual tasks. Identifies translation (~10,000 items/year) and audio production as the highest-effort workflows. Lists 8 automation opportunities.

### [Integration Strategy](docs/integration-strategy.md)
The master plan. Platform as source of truth, Webflow as rendering layer, 6-phase incremental rollout from Daily Devotional to full Webflow replacement. Includes stakeholder approval chain, Phase 1 timeline (target Beta by mid-June 2026), Webflow API setup guide, release process (Shadow → Dogfood → Beta → GA), risk assessment, and key architectural decisions.

## Update Log

Updates are listed newest-first.

**2026-03-31 — Post-ITAC review update**
- Added Executive Summary (1-pager with direction, roadmap, budget)
- Added budget breakdown: dev costs covered by AI subscription donations, ops ~$35-55/mo
- Added VibeVoice (Microsoft, open-source) to TTS provider comparison
- Added formal TTS quality comparison as roadmap milestone
- Hosting platform evaluation noted as parallel workstream (separate doc)
- Language targets softened to TBD (pending confirmation)
- Added internal prototype review target (~Apr 15) to timeline

**2026-03-14 — Phase 1 scope refinements & design decisions**
- Added email subscription / RSS publish workflow to Phase 1 scope
- Expanded Key Design Decisions: content entry format trade-offs (markdown vs structured JSON), email-based auth for Phase 1, Webflow locale confirmation action item for Sis. Hinkle

**2026-03-14 — Integration strategy major update**
- Added stakeholder engagement section (ITAC, Publishing Team, HQ leadership)
- Added approval chain with action items and owners
- Added "What Changes for the Publishing Team" before/after comparison
- Added Phase 1 detailed timeline (13 weeks, target Beta mid-June 2026)
- Added Webflow API setup guide (who does what, step by step)
- Added risk assessment (technical, operational, resource)
- Migrated repos and project board to `apostolicfaith-dev` GitHub org

**2026-03-14 — Initial design documents**
- Created website structure analysis from apostolicfaith.org crawl
- Mapped maintenance use cases across 8 roles and 4 cadences
- Drafted incremental integration strategy (6 phases)
- Added release process: Shadow → Dogfood → Beta → GA per phase
- Reviewed the-global-voice prototype as reference for translation pipeline
