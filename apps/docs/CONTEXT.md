# Context for Next Session

## Project Overview
**Repair Intake System** — AI-native mobile-first repair intake for smartphone shops.
Starting as internal tool for brother's 5-outlet chain (Dungun/Kerteh, Malaysia),
then extracting into SaaS.

## Key Decisions Made
- **Scope**: Smartphones only (iPhone + Samsung Galaxy), 50 seed models
- **Platform**: Single Next.js PWA (web-first, mobile-optimized). Staff use via mobile browser with "Add to Home Screen." No Expo / React Native.
- **Tech Stack**: Next.js 15 (App Router) + Supabase (DB/auth/storage) + OpenAI GPT-4o (AI) + 360dialog (WhatsApp)
- **Architecture**: Two-stage intake (front desk receives, technician assesses), SaaS-ready from Day 1
- **Graduated Intake**: 3 tiers — Voice (<5s), Quick Repair (<10s), Full AI (30-90s). System adapts to job complexity.
- **POS Replacement**: Payment capture, receipt generation, parts inventory decrement — the system IS the POS for repairs
- **QR Receipt**: Anonymous repairs (no phone) get a QR code receipt linking to a public ticket web view where customer can subscribe to WhatsApp
- **Quick Repair**: Typeahead device + quick-select issue + auto-filled price. Ticket auto-approved. No photo, no AI vision.
- **Voice Intake**: Speak into phone → AI extracts device, issue, customer, price → confirm in one tap
- **Payment**: Recorded at completion (not intake). Cash / QR Pay / Bank Transfer. Parts decremented on payment.
- **Language**: Bahasa Melayu primary, English fallback
- **WhatsApp**: Centralized HQ number via 360dialog. Customer notifications (YES/NO approval) + staff notifications (ticket assigned, daily summary).
- **Pricing**: Static catalog managed by business owner (supplier-agnostic). Price override with audit trail.
- **Excluded**: E-invoicing, thermal printers, refurbished sales (deferred)
- **AI**: Day 1 — GPT-4o Vision for device ID, GPT-4o for diagnosis + voice intake parse, Whisper for voice transcription
- **Timeline**: 3 months to MVP (single Next.js PWA), 6 months total including SaaS extraction
- **Dark mode**: Day 1, toggle in Profile screen, localStorage persistence
- **Malay tone**: Formal ("Anda"), not casual
- **Biometric login**: Face ID / Touch ID via WebAuthn API
- **Offline indicator**: Banner at top of screen, IndexedDB sync queue + Service Worker
- **Camera**: System dialog (`<input capture>`) for v1, inline `getUserMedia` viewfinder for v2
- **Auto-save**: On-change (debounced 500ms) for assessment drafts (IndexedDB)
- **Ticket list**: Compact density (~80px cards), swipe actions
- **Theme**: "Sharper Pop" (Tweakcn) with softened "Shop Edition" for mobile
- **UI Library**: Tailwind CSS v4 + shadcn/ui

## Revised Platform Decision (June 2025)

**Original plan:** Expo React Native + Next.js (two codebases)
**Revised plan:** Single Next.js PWA (one codebase)

**Rationale:** Solo frontend web dev with zero mobile experience. Expo RN with camera,
biometrics, offline queues, and push notifications as a first mobile project would burn
months on RN-specific problems. A well-built PWA delivers the same experience in weeks.

**Staff notification strategy:**
1. **In-app (primary):** Supabase Realtime WebSocket — tickets appear instantly in queue
2. **Out-of-app (fallback):** WhatsApp via 360dialog — "Tiket baru #D1-042 di-assign."
3. **No native push notifications needed.**

**What stays the same:** All database schema, API design, business logic, state machine,
WhatsApp integration, AI services, pricing model, design tokens.

See `02b-revised-pwa-plan.md` for full details.

## What Was NOT Decided Yet
- WhatsApp fallback if 360dialog fails?
- iPad support (optimized layout or iPhone-only PWA viewport)?
- Which `@serwist/next` version for PWA service worker?
- Image optimization strategy for photos before Vision API call?

## Next Session Starting Point
1. Scaffold monorepo: `apps/web`, `packages/shared` (Turborepo + pnpm)
2. Initialize Next.js 15 app with App Router
3. Configure Tailwind CSS v4 + shadcn/ui + design tokens
4. Set up route groups: `(auth)`, `(app)` [staff PWA], `(dashboard)`, `/t/[token]`
5. Configure `@serwist/next` for PWA (service worker + manifest)
6. Set up Supabase project + Drizzle ORM
7. Write initial database migration
8. Seed device catalog (50 iPhone/Samsung models) + device_quick_issues
9. Seed issue types (9 common issues) with Malay names
10. Build login screen with Supabase Auth + WebAuthn biometric registration
11. Build PWA tab shell (Queue, New, History, Profile)
12. Build camera capture component (`<input capture>` v1)
13. Build Quick Repair screen (typeahead + quick-select issues + price card)
14. Build Voice Intake screen (MediaRecorder + extraction confirmation card)
15. Build Full AI Intake screen (photo → AI match → customer info → create)
16. Write `POST /api/ai/identify-device` endpoint
17. Write `POST /api/ai/parse-voice-intake` endpoint
18. Write `POST /api/tickets/quick` and `POST /api/tickets/voice` endpoints
19. Reference documentation: `/Users/amir/dev/ai/apps/docs/` (all docs including design system)

## Key Files
All documentation in `/Users/amir/dev/ai/apps/docs/`
- `01-overview.md` — start here for context
- `02-architecture.md` — revised architecture (PWA, not Expo)
- `02b-revised-pwa-plan.md` — full rationale for PWA over Expo
- `04-database-schema.md` — database schema to implement
- `10-roadmap.md` — revised timeline (3-month MVP)
- `15-summary.md` — executive summary

**Design System** (in `apps/docs/design-system/`):
- `01-tokens-and-colors.md` — color tokens, typography, spacing
- `02-components.md` — component primitive specs
- `03-mobile-screens.md` — all screen wireframes and flows
- `04-ui-copy.md` — Malay translations, tone, WhatsApp messages
- `05-tech-stack.md` — UI library choices, dependencies, architecture

## Open Questions for Brother
When observing the shops:
- What devices are most commonly repaired? (validate our 50-model seed list)
- What's the actual intake flow? (our assumptions vs reality)
- How do they handle dead devices today?
- What information do they actually write down?
- What's the average wait time for customers?
- Do they use WhatsApp to contact customers today? If so, what number?
- Parts supplier info (even if anonymous — just the process of ordering)
