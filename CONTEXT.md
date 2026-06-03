# Context for Next Session

## Project Overview
**Repair Intake System** — AI-native mobile-first repair intake for smartphone shops.
Starting as internal tool for brother's 5-outlet chain (Dungun/Kerteh, Malaysia),
then extracting into SaaS.

## Key Decisions Made
- **Scope**: Smartphones only (iPhone + Samsung Galaxy), 50 seed models
- **Platform**: iOS only (Expo React Native), all staff use iPhones
- **Tech Stack**: Expo (mobile) + Next.js (web/API) + Supabase (DB/auth/storage) + OpenAI GPT-4o (AI)
- **Architecture**: Two-stage intake (front desk receives, technician assesses), SaaS-ready from Day 1
- **Language**: Bahasa Melayu primary, English fallback
- **WhatsApp**: Centralized HQ number via 360dialog, YES/NO reply approval flow
- **Pricing**: Static catalog managed by business owner (supplier-agnostic)
- **Excluded**: E-invoicing, thermal printers, refurbished sales (deferred)
- **AI**: Day 1 — GPT-4o Vision for device ID, GPT-4o for diagnosis suggestions
- **Timeline**: 6 months solo build, Month 1-3 = MVP, Month 4-6 = SaaS extraction
- **Dark mode**: Day 1, toggle in Profile screen, context-based switching
- **Malay tone**: Formal ("Anda"), not casual
- **Biometric login**: Face ID / Touch ID via `expo-local-authentication`
- **Offline indicator**: Banner at top of screen, AsyncStorage sync queue
- **Camera**: Inline in New Ticket screen (not modal), 4:5 aspect ratio
- **Auto-save**: On-change (debounced 500ms) for assessment drafts
- **Ticket list**: Compact density (~80px cards), swipe actions
- **Theme**: "Sharper Pop" (Tweakcn) with softened "Shop Edition" for mobile
- **UI Library**: NativeWind + React Native Reusables (Tailwind for RN)

## What Was NOT Decided Yet
- WhatsApp fallback if 360dialog fails?
- Push notifications vs only WhatsApp for customer updates?
- iPad support (optimized layout or iPhone-only)?

## Next Session Starting Point
1. Scaffold monorepo: `apps/mobile`, `apps/web`, `packages/shared` (Turborepo + pnpm)
2. Initialize Expo mobile app with Expo Router
3. Initialize Next.js web app with App Router
4. Set up Supabase project + Drizzle ORM
5. Write initial database migration
6. Seed device catalog (50 iPhone/Samsung models)
7. Build login screen (mobile + web) with Supabase Auth
8. Build camera capture component for mobile
9. Write `POST /api/ai/identify-device` endpoint
10. Reference documentation: `/Users/amir/dev/ai/` (all docs including design system)

## Key Files
All documentation in `/Users/amir/dev/ai/`
- `01-overview.md` — start here for context
- `04-database-schema.md` — database schema to implement
- `10-roadmap.md` — timeline and priorities
- `15-summary.md` — executive summary

**Design System** (in `design-system/`):
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
