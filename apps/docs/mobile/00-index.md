# Mobile UI Specs — Index & Build Order

> **This folder contains screen-level UI specifications for the Repair Intake mobile app.**  
> Each file focuses on a single feature or screen. No API contracts, no database schema, no backend logic.  
> For design tokens, colors, typography, spacing, and reusable component primitives, see:  
> `apps/docs/design-system/01-tokens-and-colors.md` and `02-components.md`  
> For the full end-to-end flows (including API, state machine, WhatsApp), see:  
> `apps/docs/06-mobile-flows.md` and `apps/docs/design-system/03-mobile-screens.md` (kept for historical reference).

---

## Build Order (Simplest → Most Complex)

| Order | File                        | Feature                                | Why This Order                         |
| ----- | --------------------------- | -------------------------------------- | -------------------------------------- |
| 1     | `01-auth.md`                | Login, Biometric, Language Toggle      | Entry point. No dependencies.          |
| 2     | `02-navigation-shell.md`    | Tab Bar, Stack Screens, Shell            | Structural backbone. 5-tab nav.        |
| 3     | `03-home-intake.md`         | Home / Intake Landing                  | Post-login hub. Stats + flow launchpad.  |
| 4     | `03-jobs-queue.md`          | Jobs / Home List                       | Primary work surface. Reads tickets.   |
| 5     | `10-profile-settings.md`    | Profile & Settings                     | Static read-only settings. Simple.     |
| 6     | `11-history.md`             | History / Past Tickets                 | Similar to queue, just filtered.       |
| 7     | `04-new-ticket-full-ai.md`  | New Ticket (Full AI)                   | First write operation. Camera + form.  |
| 8     | `05-ticket-detail.md`       | Ticket Detail                          | Read-only detail view.                 |
| 9     | `06-assessment.md`          | Technician Assessment                  | Complex form. AI suggestions + parts.  |
| 10    | `07-quick-repair.md`        | Quick Repair (Baiki Cepat)             | Fast path. Typeahead + quick-select.   |
| 11    | `08-voice-intake.md`        | Voice Intake (Ambil Suara)             | Mic UI + confirmation card.            |
| 12    | `09-payment-receipt.md`     | Payment & Receipt                      | Final step. Price + method + success.  |
| 13    | `12-dead-device-unknown.md` | Dead Device / Unknown Diagnosis        | Edge case flow. Warning + options.     |
| 14    | `13-qr-receipt.md`          | QR Code Receipt Display                | Anonymous customer receipt.            |
| 15    | `14-success-states.md`      | Shared Success & Confirmation Overlays | Reused across multiple flows.          |

---

## Cross-Reference Map

| Concern                                                          | Where to Look                                         |
| ---------------------------------------------------------------- | ----------------------------------------------------- |
| Colors, typography, spacing tokens                               | `design-system/01-tokens-and-colors.md`               |
| Reusable component primitives (Button, Input, Card, Badge, etc.) | `design-system/02-components.md`                      |
| Malay / English copy, tone, WhatsApp message templates           | `design-system/04-ui-copy.md`                         |
| Full ASCII wireframes (original monolithic doc)                  | `design-system/03-mobile-screens.md`                  |
| Complete flow narratives with API calls                          | `06-mobile-flows.md`                                  |
| State machine (ticket lifecycle)                                 | `07-mermaid-diagrams.md`                              |
| Tech stack (Expo, NativeWind, Reusables, etc.)                   | `03-tech-stack.md` & `design-system/05-tech-stack.md` |
| Database schema (for context only)                               | `04-database-schema.md`                               |

---

## Key Design Principles (Mobile UI)

1. **Camera-first**: The camera capture area is the most prominent element on the new ticket screen. No hunting through menus.
2. **Suggestion chips**: AI suggestions are tappable pills, not dropdown menus. Quick Repair uses typeahead + quick-select. Voice uses speech.
3. **Confidence display**: Every AI result shows a confidence percentage. Staff know when to trust or override.
4. **Autosave**: Assessment drafts autosave every 30 seconds (debounced 500ms on change). No lost work if the app backgrounds.
5. **Offline queue**: If no internet, tickets queue locally and sync when connection returns. Offline indicator banner at top of screen.
6. **QR receipt for anonymous repairs**: When no phone number provided, a QR code links to a public ticket page. Customer can subscribe to WhatsApp updates at any time via the QR link.
7. **Home-first navigation**: The Home / Utama tab is the post-login landing. All three intake flows (Full AI, Assessment, Quick Repair) are launched from action cards on the Home screen.
8. **Pull-to-refresh → Real-time**: Initially pull-to-refresh the queue. Upgrade to Supabase Realtime subscriptions (Month 3) for live updates without user action.
9. **Dark mode**: Day 1. Toggle in Profile screen. Context-based switching. Respects system preference on first launch.
10. **Malay tone**: Formal ("Anda"), not casual. Bahasa Melayu primary, English fallback.

---

## Naming Conventions Used in These Specs

- **Screens**: PascalCase (e.g., `NewTicketScreen`)
- **Components**: PascalCase (e.g., `CameraCapture`, `SuggestionChips`)
- **Hooks**: camelCase with `use` prefix (e.g., `useCameraPermissions`)
- **Design tokens**: Referenced as `--token-name` (e.g., `--primary`, `--card`, `--muted-foreground`)
- **Status values**: `RECEIVED`, `ASSESSING`, `ASSESSED`, `APPROVED`, `IN_PROGRESS`, `COMPLETED`, `PAID`, `PICKED_UP`, `CANCELLED`
- **Roles**: `front_desk`, `technician`, `manager`, `owner`
