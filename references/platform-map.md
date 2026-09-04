---
name: lordgen-platform-map
description: Which connected platform a given LordGen function actually runs on. Read before a skill reaches for a connector, or before hand-rolling something that's already sitting there unused.
---

# Platform map — what runs where

Read when: a skill needs to call an external system, or you're deciding whether something
needs new infrastructure or already has a home.

Most of the Revenue Loop runs on tools already connected to this Claude account, at zero
setup cost. This is the map so a new skill reaches for the connector that already exists
instead of duplicating it.

| Function | Platform | Status | Notes |
|---|---|---|---|
| Build agent, repo work, orchestration | **Claude Code** | Primary | This repo |
| CRM, deals, landing pages, marketing email, blog | **HubSpot** | Connected | Covers more than "CRM" — landing pages and email live here too |
| Prospecting, enrichment, outbound sequences | **Apollo.io** | Connected | The direct-outreach channel (see [`human-in-the-loop.md`](human-in-the-loop.md) for approval gates) |
| Backend/API hosting | **Render** | Connected | |
| Frontend hosting, domains, analytics | **Vercel** | Connected | Domain purchase needs explicit sign-off before use |
| Managed Postgres | **Supabase** | Connected | Optional — project creation carries a cost, confirm before creating |
| Landing pages / WordPress work | **WordPress.com** | Connected | |
| Brand and content assets | **Canva**, Adobe for creativity | Connected | See [`brand.md`](brand.md) for the identity these should draw from |
| Client/project tracking | **ClickUp** | Connected | |
| Email, calendar, docs | **Gmail**, **Google Calendar**, **Google Drive** | Connected | Follow-up, discovery-call scheduling, proposal storage |
| Workflow automation runtime | **n8n** | Proven pattern | `Newsletter Demo/` validated this end to end |
| Web scraping / research | **Firecrawl** | Connected | `Lordgen ai scraper/` validated this end to end |
| Upwork proposal submission | — | **Not connected, no official API** | Manual only — do not attempt browser automation to bypass platform controls, it risks the account |

Nothing here means "wire it all up today." A skill should check this table before assuming a
capability needs to be built from scratch.

## Changelog
- v1.0 — Initial, condensed from `LordGen_Master_Brief_v3.md` §6
