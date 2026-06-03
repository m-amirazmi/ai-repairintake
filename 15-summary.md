# Repair Intake System — Executive Summary

## What This Is

An **AI-native** mobile-first repair intake system for smartphone repair shops in Malaysia.
Built for a real 5-outlet chain (Dungun/Kerteh), with a clear path to SaaS.

## The Problem It Solves

Repair shop intake is slow and error-prone:
- **Front desk staff aren't technicians** — they can't diagnose issues
- **Customers are vague** — "it's broken" is the most common description
- **Devices change hands** — information is lost between front desk and technician
- **Quotes are delayed** — customers wait without knowing the price
- **Staff retype the same info** — device model, customer details, notes

## The Solution

1. **AI identifies devices from a photo** — no manual model selection
2. **AI detects pre-existing damage** — documents scratches, cracks, dents before repair (dispute protection)
3. **AI suggests diagnoses** to the technician — ranked by probability for that device + symptoms
4. **Voice-to-structured notes** — staff speaks, AI transcribes and structures damage data
5. **AI drafts WhatsApp messages** — contextual, professional, in Malay tone
6. **WhatsApp handles all customer communication** — automatic notifications at every stage
7. **The ticket is the single source of truth** — front desk and technicians work on the same record

## Core Innovation

- **Two-stage intake**: Front desk receives (no diagnosis), technician assesses (with AI)
- **Role-aware unified flow**: Any user can advance any ticket through any state. Technician at the counter? Flow compresses into a single session.
- **AI-native from Day 1**: Not an add-on — AI powers device ID, damage detection, diagnosis, voice transcription, and messaging
- **Damage documentation**: AI catalogs pre-existing condition, protecting shops from false damage claims
- **Voice-first data entry**: Staff speak naturally, AI structures the data into ticket fields
- **Malaysia-first**: Bahasa Melayu (primary), WhatsApp (100% communication), MYR, local repair patterns

## Tech Stack

```
TypeScript everywhere:
  Expo (mobile) ← shared types → Next.js (web + API)
                      ↓
                PostgreSQL (Supabase)
                      ↓
              OpenAI GPT-4o (AI services)
                      ↓
            WhatsApp Business API (360dialog)
```

| Layer | Technology |
|---|---|
| Mobile App | Expo (React Native) + NativeWind |
| Admin Web | Next.js 15 + Tailwind + shadcn/ui |
| API | Next.js Route Handlers + Drizzle ORM |
| Database | PostgreSQL (Supabase) |
| Auth | Supabase Auth |
| AI | OpenAI GPT-4o Vision + GPT-4o + Whisper |
| Messaging | WhatsApp Business API (360dialog) |
| Language | Bahasa Melayu + English |

## Timeline

| Month | Focus | Key Deliverable |
|---|---|---|
| **1** | Foundation + AI Vision | Staff creates tickets with AI device identification + damage detection + photo quality gate |
| **2** | Assessment + Pricing + Voice | Technicians assess with AI suggestions, voice-to-structured notes, auto-quotes, AI WhatsApp drafting |
| **3** | Queue + Polish + Localization | Real-time queue, complete Malay localization, admin dashboard v1 |
| **4** | Reports + Inventory + Learning | Reporting system, parts inventory, repair outcome learning loop |
| **5** | Advanced AI + Offline | Smart quote flagging, demand forecasting, full offline mode |
| **6** | SaaS Extraction | Multi-tenant, public signup, Stripe billing, white-label |

## Key Numbers

- **50 device models** in seed catalog (iPhone + Samsung Galaxy)
- **9 issue types** covering ~90% of smartphone repairs
- **5 outlets** to start (brother's chain in Dungun/Kerteh)
- **~RM135/month** AI cost at 50 tickets/day (expanded AI stack)
- **6 months** to full SaaS from solo developer
- **~RM1,500** total POC cash investment (developer-funded)
- **1 codebase** for all roles (front desk, technician, manager, owner)
- **1 WhatsApp number** centralized for all 5 outlets

## Architecture Philosophy

- **Stateless API** — each request self-contained, no server-side sessions
- **Database as source of truth** — all state in PostgreSQL, real-time broadcasts for live updates
- **AI as a service** — isolated behind `/api/ai/*` endpoints, easy to swap models later
- **Graceful degradation** — if AI API is down, fall back to manual input
- **SaaS-ready from Day 1** — single-tenant architecture that becomes multi-tenant with one config change

## What's NOT Being Built (Yet)

| Feature | Reason | When |
|---|---|---|
| E-invoicing (LHDN MyInvois) | Complex regulatory integration | After SaaS launch |
| Thermal printer integration | Digital tickets only for MVP | If shops demand it |
| Refurbished phone sales/POS | Different module entirely | Phase 2 |
| Android mobile app | All staff use iPhones | Phase 2 |
| Multi-brand beyond iPhone/Samsung | Covers 90% of repairs | Add on demand |

## How It Works (The 30-Second Flow)

```
1. STAFF OPENS PHONE → taps "New Ticket"
2. SNAPS 1 PHOTO → AI identifies device
3. TYPES CUSTOMER INFO → name + phone number
4. TAPS SUBMIT → ticket created, WhatsApp sent to customer
```

**Average time: 30 seconds.** No manual device selection. No menu hunting. No price guessing.

Then:
```
5. TECHNICIAN OPENS TICKET → AI suggests diagnosis
6. TAPS SUGGESTION → parts pre-filled, price auto-calculated
7. REVIEWS + SAVES → WhatsApp quote sent to customer
8. CUSTOMER REPLIES "YA" → ticket auto-approved
```

**Average time: 1-2 minutes.** AI does the heavy lifting. Technician just validates.

## Open Decisions (Before Building)

| # | Decision | Recommendation |
|---|---|---|
| 1 | **Database hosting** | Supabase Cloud (managed, free tier to start) |
| 2 | **Offline support** | Basic AsyncStorage from Day 1, full offline in Month 5 |
| 3 | **Photo quality** | Compress to 800px for AI (saves 60% cost, sufficient for device ID) |
| 9 | **Voice transcription** | Whisper API, Malay support, structured extraction |
| 4 | **Customer approval** | WhatsApp reply YES/NO (simpler, natural for Malaysia) |
| 5 | **Malay tone** | Semi-formal (use `anda` for notifications, conversational for UI) |
| 6 | **Dark mode** | Skip for MVP, add in Month 3 if time allows |
| 7 | **Biometric login** | Skip for MVP, add later for convenience |
| 8 | **Device catalog source** | Manual seed (50 models) + admin dashboard for additions |

## Next Steps

Ready to start building? The order of operations:

1. **Scaffold monorepo**: `apps/mobile`, `apps/web`, `packages/shared`
2. **Set up Supabase project** + initial migration
3. **Seed device catalog** (50 models) and issue types (9 issues)
4. **Build login screen** (mobile + web)
5. **Build camera capture** + AI device identification flow
6. **Write `POST /api/ai/identify-device`** endpoint
7. **Test with real device photos** from brother's shop

---

## Document Index

All documentation is available in this directory:

| File | Content |
|---|---|
| `01-overview.md` | Project vision, core philosophy, scope |
| `02-architecture.md` | System architecture, data flows, role-aware flows |
| `03-tech-stack.md` | Technology choices and rationale |
| `04-database-schema.md` | Complete database schema with ER diagram |
| `05-api-design.md` | REST API endpoints and contracts |
| `06-mobile-flows.md` | Mobile app UI wireframes (ASCII) |
| `07-mermaid-diagrams.md` | Flow diagrams (Mermaid) |
| `08-admin-dashboard.md` | Admin web dashboard wireframes (ASCII) |
| `09-project-structure.md` | Monorepo folder structure |
| `10-roadmap.md` | 6-month development roadmap |
| `11-edge-cases.md` | Edge cases and error handling |
| `12-security.md` | Security, authentication, PDPA compliance |
| `13-deployment.md` | Deployment strategy and CI/CD |
| `14-appendix.md` | Seed data, translations, environment variables |
| `README.md` | Documentation index and quick start |
