# Development Roadmap — 6 Months

> **Revision (June 2025):** Switched from Expo React Native + Next.js to single Next.js PWA.
> Eliminates the React Native learning curve. See `02b-revised-pwa-plan.md` for rationale.

## Month 1: Foundation + All Three Intake Tiers

### Week 1: Project Scaffold + Database
- Initialize monorepo (Turborepo + pnpm)
- Scaffold Next.js app with App Router (route groups for staff PWA + dashboard)
- Configure Tailwind CSS v4 + shadcn/ui + design tokens (Sharper Pop, Shop Edition)
- Set up Supabase project (local + cloud)
- Configure Drizzle ORM, write initial migration
- Seed device catalog (50 iPhone + Samsung models)
- Seed issue types (9 common issues with Malay names)
- Seed device_quick_issues (top-10 quick issues per device)

### Week 2: Auth + PWA Shell
- Supabase Auth integration (email + password login)
- Role-based access: `owner`, `manager`, `front_desk`, `technician`
- Auth middleware for all API routes
- Login page (mobile-optimized) + "remember me"
- WebAuthn biometric registration flow (Face ID / Touch ID)
- Staff PWA tab shell: Queue, New Ticket, History, Profile tabs
- PWA manifest + `@serwist/next` service worker + "Add to Home Screen" prompt
- Dark mode toggle + persistence (localStorage)
- Malay / English i18n system
- Language switcher (persisted to user profile)

### Week 3: Quick Repair + Voice Intake
- **Quick Repair screen**: typeahead device search (2-3 chars), quick-select issue chips (top-10), auto-filled price card, QR code receipt generation
- **Voice Intake**: MediaRecorder mic button, recording animation, `POST /api/ai/parse-voice-intake` (Whisper → GPT-4o field extraction), confirmation card with confidence display
- `POST /api/tickets/quick` endpoint
- `POST /api/tickets/voice` endpoint
- Ticket list view (queue) with TanStack Query
- Supabase Realtime subscription for live queue updates (new tickets appear instantly)
- Ticket status badge component (all 9 states)
- Offline banner component + basic online/offline detection

### Week 4: Full AI Intake
- Camera capture: system dialog (`<input capture>`) for v1, `getUserMedia` inline for v2
- Photo upload to Supabase Storage
- Photo quality gate: blur detection, lighting check, device-in-frame validation (local + API)
- Quality gate UI: Malay retry prompts ("Gambar kabur, sila ambil semula")
- `POST /api/ai/identify-device` (GPT-4o Vision: device model + pre-existing damage)
- Damage report overlay on intake photo
- Full AI intake flow: photo → AI match + damage → customer info → ticket created
- Confidence threshold handling (≥80% auto-select, <80% show top 3 matches for manual pick)
- "Not in catalog" handling + quick-add device flow
- Ticket detail view (all states, history log)
- `POST /api/tickets` endpoint (full AI)

**Month 1 Deliverable:** Staff can create tickets via all three intake tiers (Voice <5s,
Quick Repair <10s, Full AI 30-90s). Queue updates live via WebSocket. Device identification
via AI Vision works. Auth and roles in place. PWA installable on staff phones.

---

## Month 2: Assessment + WhatsApp + Payment

### Week 1: Ticket Lifecycle + Assessment
- State machine: all transitions including auto-approve for Quick Repair / Voice
- `PATCH /api/tickets/[id]/status` with validation
- `PATCH /api/tickets/[id]/assess` for assessment submission
- Queue filtering by outlet, status, technician
- Ticket assignment (front desk → technician)
- Ticket history logging (audit table, immutable)
- "Assess Now" prompt after Full AI ticket creation (for technician-role staff)

### Week 2: AI Diagnosis + Parts + Pricing
- `POST /api/ai/suggest-diagnosis` (GPT-4o: device + symptoms → ranked diagnosis list)
- Diagnosis suggestion chips UI in assessment screen
- Technician override: select from suggestions or type custom diagnosis
- Parts auto-population from diagnosis (if mapped in catalog)
- Parts catalog CRUD (admin dashboard)
- Pricing engine: parts cost + labor hours → estimate total
- Price override by technician (with audit trail: who, when, old price, new price, reason)
- Assessment auto-save (on-change, debounced 500ms, stored in IndexedDB draft)

### Week 3: WhatsApp Integration
- 360dialog account setup + WhatsApp Business API verification
- Message template creation + Meta approval process
- `POST /api/webhooks/whatsapp` webhook (receive + parse incoming messages)
- Customer notifications on key events (assessment complete, repair complete, ready for pickup)
- AI WhatsApp message drafting: `POST /api/ai/draft-whatsapp`
- Staff preview/edit screen: "Hantar" or "Edit Dahulu"
- YES/NO reply parsing for customer approval flow
- Auto-approve on "YA" / "YES" / "OK", decline on "TAK" / "NO", forward unknown to staff
- Staff WhatsApp notifications: ticket assigned, assessment needed, approval received, daily summary
- Malay WhatsApp templates (formal "Anda" tone)

### Week 4: Payment + Receipt + Admin Dashboard v1
- `POST /api/tickets/[id]/payment` endpoint (amount, method, received_by)
- Payment screen: auto-filled amount, select method (cash/qr/bank), confirm
- Parts inventory auto-decrement on payment confirmation
- WhatsApp receipt for customers with phone number
- QR code receipt for anonymous customers (generated, displayed, scannable)
- Public ticket view: `/t/[token]` (no auth, shows status, subscribe-to-WhatsApp button)
- Subscribe flow: enter phone → link customer to ticket → send WhatsApp confirmation
- **Admin Dashboard v1:**
  - Overview page: 4 stat cards (today's revenue, tickets, avg time, pending)
  - Revenue chart (last 30 days, by outlet)
  - Ticket list with search, filter (status, outlet, technician), sort
  - Outlet performance cards
  - Common issues chart
  - Export to CSV

**Month 2 Deliverable:** End-to-end repair flow working. Technician assesses with AI
diagnosis suggestions, gets auto-calculated quotes. System sends WhatsApp to customers
with YES/NO approval. Payment captured, receipts generated. Admin dashboard for owner.

---

## Month 3: Polish + Edge Cases + Offline

### Week 1: Real-Time Polish
- Queue animations: new tickets slide in from top
- Optional sound cue for new ticket assignment
- Pull-to-refresh fallback (when WebSocket reconnects)
- Loading skeletons for all views (ticket list, detail, assessment)
- Empty states: no tickets, no results, no history (helpful Malay messaging)
- Error toasts with retry action (network failures, API errors)
- Rate limiting on API (per-user, per-IP)
- Graceful degradation: if AI API is down, manual fallback flows

### Week 2: Localization + Accessibility
- Complete Malay translations for all screens (staff PWA + admin dashboard)
- Customer-facing Malay: WhatsApp messages, public ticket page, QR receipt page
- English fallback for all strings (i18n key system)
- Screen reader support (aria-labels, role attributes, semantic HTML)
- Touch targets ≥44px everywhere (Apple HIG compliance)
- Keyboard avoiding on all forms (iOS Safari `visualViewport` handling)
- Color contrast ratios meet WCAG AA (checked against design tokens)

### Week 3: Edge Cases + Hardening
- Dead device flow: diagnostic fee ticket, no AI device identification needed
- Cancelled ticket + device return tracking (signed pickup)
- 30-day warranty tracking (repair outcome learning loop)
- Customer search + history lookup (by name, phone, device IMEI)
- Duplicate customer detection (by phone, within tenant)
- Ticket re-open flow (customer returns with same issue, links to original ticket)
- Price override audit trail visible in ticket detail
- Bulk status update (mark multiple as picked_up at end of day)

### Week 4: Offline Mode
- Service Worker caching: app shell (HTML/CSS/JS), API response cache (stale-while-revalidate)
- IndexedDB offline queue: create Quick Repair / Voice Intake tickets while offline
- Background Sync API: auto-sync queued tickets when connection restores (Chrome)
- IndexedDB fallback sync: on app open, check for pending items + flush (Safari)
- Offline banner component: top of screen, persistent, Malay text
- Pending sync badge: count of queued items, visible on Queue tab
- Cached device catalog for offline typeahead (50 models = ~10KB stored)
- "Dicipta di luar talian" label on offline-created tickets
- Conflict resolution: last-write-wins with server timestamp check

**Month 3 Deliverable:** Production-ready MVP. 5-outlet deployment to brother's shops.
Staff can run daily operations end-to-end. WhatsApp customer communication working.
Offline mode for shops with unreliable internet (common in rural Dungun/Kerteh).

---

## Month 4: Reporting + Inventory + AI Loop

### Week 1: Reporting System
- Daily report: tickets created, completed, revenue, per outlet
- Weekly report: trends, comparisons, busy days/hours
- Monthly report: revenue trends, issue distribution, technician performance
- Technician metrics: tickets completed, avg assessment time, customer satisfaction (approval rate)
- Common issues drill-down: by device model, by outlet, by time period
- Export to CSV and PDF (server-side PDF generation)
- Scheduled WhatsApp reports: daily summary to owner (optional)

### Week 2: AI Learning Loop
- Technician confirms actual diagnosis at repair completion (was AI right?)
- AI compares predicted vs actual diagnosis
- Per-tenant calibration: confidence scores adjust to shop-specific repair patterns
- Dashboard: "AI Accuracy: 87% this month" (vs global baseline)
- Smart Quote Flagging: detect unusually high/low quotes, flag for review
- Damage detection false positive tracking (technician dismisses → feedback to model)

### Week 3: Inventory Management
- Real-time stock tracking per part (auto-decrement on payment)
- Low stock alerts: dashboard badge + WhatsApp to owner
- Stock adjustment: manual add, discrepancy logging, audit trail
- Supplier tracking per part (supplier name, supplier part number, lead time)
- Stock history log (every change: who, when, old qty, new qty, reason)
- Purchase order generation (MVP: export list, not automated ordering)

### Week 4: Photo Documentation + Edge Cases
- Before/after repair photos (internal documentation, not shown to customer)
- Multiple photo upload per ticket (gallery view in ticket detail)
- AI damage severity tagging from repair photos
- Quality check photos for completed repairs
- Warranty claim tracking (customer returns within 30 days, links to original)
- Customer notification when warranty is about to expire (WhatsApp reminder)

---

## Month 5: Advanced AI + Offline Enhancement

### Week 1: Advanced Voice Features
- Voice memo attached to assessment (MediaRecorder for technician dictation)
- Auto-transcription on upload (Whisper, Malay support tested on Malaysian accents)
- Transcription attached to ticket notes (searchable)
- Voice memo playback in ticket detail (staff + dashboard)

### Week 2: Advanced AI Damage Assessment
- Secondary photo analysis for damage severity (beyond initial intake)
- Cracked screen: location, spread pattern, severity grade
- Liquid damage indicator (LDI) color recognition
- Bent/dropped frame detection
- AI damage report generated at repair start (protects shop from "you broke it" claims)

### Week 3: Technician Time Tracking
- Start/stop timer per repair (tap to start on assessment, stop on completion)
- Actual vs estimated time comparison (was quote accurate?)
- Time log per repair step (assessment, diagnosis, repair, testing)
- Technician efficiency metrics: avg repair time by issue type
- Labor cost auto-recalculation based on actual time (with override)

### Week 4: Full Offline Enhancement
- Offline assessment (full form cached, syncs when online)
- Background sync with progress indicator
- Conflict resolution: two staff editing same ticket offline
- Cached photo queue (photos taken offline, uploaded on reconnect)
- Offline device catalog with search
- Connection quality indicator (WiFi vs cellular, signal strength warning)

---

## Month 6: SaaS Extraction + Launch

### Week 1: Multi-Tenant Preparation
- Tenant isolation audit: RLS policies on all tables, API middleware check
- Per-tenant data separation verification (no cross-tenant data leaks)
- Tenant onboarding flow (admin signs up → wizard → first outlet → invite staff)
- Default seed data for new tenants (devices, issues, quick-issues)
- Tenant-level settings: currency, timezone, language, WhatsApp config, branding

### Week 2: Public Signup + Onboarding
- Landing page for repair shops (Malay + English)
- Signup form: business name, reg number, admin email, password
- Phone verification via WhatsApp OTP
- Welcome wizard: 4 steps
  1. Add first outlet (name, address, phone)
  2. Invite staff (email, role)
  3. Select WhatsApp number (or use provided HQ number)
  4. Ready to go checklist
- First-time setup guide with sample data

### Week 3: Subscription Billing
- Stripe integration (Malaysia: MYR, supports FPX online banking)
- Plan tiers: Starter (1 outlet), Pro (3-10 outlets), Enterprise (unlimited)
- 14-day free trial (no credit card required)
- Billing dashboard for platform owner (you): MRR, churn, active tenants
- Invoice generation + email delivery
- Graceful downgrade (over outlet limit → read-only until upgrade)

### Week 4: White-Label + Beta + Launch
- Logo upload per tenant (appears on PWA, dashboard, WhatsApp header)
- Primary color customization (derived from tenant logo)
- Custom WhatsApp business profile name per tenant
- Documentation: user guide (Malay), FAQ, troubleshooting, video walkthrough
- Beta testing: 2-3 friendly shops, 2-week trial, collect feedback
- Bug fixes from beta feedback
- Public launch: social media, repair shop forums, direct outreach

**Month 6 Deliverable:** SaaS platform ready for public launch. Other repair shops can
sign up, configure, and start using the system independently. Subscription billing live.

---

## Risk Matrix

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Brother's shops resist new system | Medium | High | Work there for 2 weeks observing; build exactly what they need; involve them in testing from Week 3 |
| WhatsApp API approval delayed | Medium | Medium | Start approval process in Week 1 Month 2; manual WhatsApp fallback (copy-paste messages) |
| AI API cost exceeds estimates | Low | Low | Token caching; compress images before Vision; rate-limit per ticket; Quick Repair bypasses Vision entirely |
| Voice transcription accuracy (Malay) | Medium | Medium | Test Whisper on Malaysian Malay accents early; fallback to manual edit; confidence threshold display |
| PWA "Add to Home Screen" not adopted by staff | Low | Medium | Onboarding screen shows how to install; persistent banner; it's faster than searching for an app |
| iOS Safari PWA limitations | Low | Medium | Test on real iPhones from Week 1; known limitations documented; Workbox polyfills for missing APIs |
| Damage detection false positives | Medium | Medium | Confidence threshold (≥80%); technician can dismiss individual detections; feedback loop improves accuracy |
| Data migration issues | Low | High | Export/import tools from day 1; never lock data in; Supabase backups enabled |
| Solo burnout | Medium | High | Strict scope control; ship MVP at Month 3; take weekends off; 3-tier intake means partial features still deliver value |
| WebAuthn biometric adoption | Low | Low | Optional feature; email+password always works; biometric is convenience, not requirement |
