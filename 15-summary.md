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

- **Graduated intake (3 tiers)**: Voice (<5s), Quick Repair (<10s), Full AI (30-90s). The system adapts to the job, not vice versa. Voice and Quick Repair are the defense against the "old POS is faster" problem.
- **Two-stage intake**: Front desk receives (no diagnosis), technician assesses (with AI)
- **The system IS the POS**: Payment capture, receipt generation, and inventory decrement replace the old transactional POS for repairs
- **Role-aware unified flow**: Any user can advance any ticket through any state. Technician at the counter? Quick Repair or Voice path compresses intake + assess + pay into a single flow
- **AI-native from Day 1**: Not an add-on — AI powers device ID, damage detection, diagnosis, voice transcription, voice intake parsing, and messaging
- **Damage documentation**: AI catalogs pre-existing condition, protecting shops from false damage claims
- **QR receipt for anonymous repairs**: Cash-and-go customers don't need to share phone numbers. QR links to public ticket page where they can optionally subscribe
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
|---|---|---|---|
| **1** | Foundation + AI Vision + Quick Intake | Staff creates tickets via photo+AI, typeahead quick-select, or voice. AI identifies devices. Graduated intake operational. |
| **2** | Assessment + Pricing + Payment | Technicians assess with AI suggestions, auto-quotes, payment capture + WhatsApp/QR receipts, inventory decrement, AI WhatsApp drafting |
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
| Android app (fully tested) | Already runs via Expo — iOS gets first-class polish | Post-launch |
| Multi-brand beyond iPhone/Samsung | Covers 90% of repairs | Add on demand |

## How It Works

### Tier 1: Voice Intake (<5 seconds, fastest path)
```
1. TECHNICIAN TAPS 🎤 → speaks "iPhone 14 skrin pecah, Ahmad, RM350"
2. AI EXTRACTS → device, issue, customer, price
3. TAPS CONFIRM → ticket created, auto-approved, in progress
```
**Average time: 5 seconds.** For known fixes, no typing, no photo, no menu.

### Tier 2: Quick Repair (<10 seconds, for common fixes)
```
1. TECHNICIAN TAPS "Baiki Cepat" → types "iPh" (3 chars)
2. SELECTS ISSUE → "Penukaran Skrin" from top-10 list
3. PRICE AUTO-FILLED → reviews (editable), taps submit
4. TICKET CREATED → auto-approved, QR receipt generated
```
**Average time: 10 seconds.** Faster than any existing POS. Optional phone for WhatsApp receipt.

### Tier 3: Full AI Intake (30-90 seconds, for unknown problems)
```
1. STAFF OPENS PHONE → taps "New Ticket"
2. SNAPS 1 PHOTO → AI identifies device + detects pre-existing damage
3. SPEAKS OR TYPES COMPLAINT → AI structures notes
4. ENTERS CUSTOMER INFO → name + phone number
5. TAPS SUBMIT → ticket created, WhatsApp sent
```
**Average time: 30 seconds.** No manual device selection. No menu hunting.

Then (Full AI only):
```
6. TECHNICIAN OPENS TICKET → AI suggests diagnosis
7. TAPS SUGGESTION → parts pre-filled, price auto-calculated
8. REVIEWS + SAVES → WhatsApp quote sent to customer
9. CUSTOMER REPLIES "YA" → ticket auto-approved
```
**Average time: 1-2 minutes.** AI does the heavy lifting. Technician just validates.

### Payment & Receipt (all tiers)
```
10. TECHNICIAN MARKS COMPLETED → payment screen slides up
11. RECORDS PAYMENT (cash/QR/bank) → parts inventory decremented
12. WHATSAPP RECEIPT SENT (or QR receipt if no phone)
```

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
