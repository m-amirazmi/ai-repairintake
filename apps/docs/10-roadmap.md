# Development Roadmap — 6 Months

## Month 1: Foundation + AI Vision Intake

### Week 1: Project Scaffold + Database
- Initialize monorepo (Turborepo + pnpm)
- Configure shared packages (`types`, `constants`, `utils`)
- Set up Expo mobile app with Expo Router
- Set up Next.js web app with App Router
- Set up Supabase project (local + cloud)
- Configure Drizzle ORM, write initial migration
- Seed device catalog (50 iPhone + Samsung models)
- Seed issue types (9 common issues)

### Week 2: Authentication + Roles
- Supabase Auth integration (mobile + web)
- Role-based access: `owner`, `manager`, `front_desk`, `technician`
- Login screen (mobile) — email + password
- Login page (web) — email + password
- Auth middleware for all API routes
- Session persistence (mobile: SecureStore)

### Week 3: Basic Ticket Creation + Photo Quality Gate
- Camera capture component (Expo Camera)
- Photo upload to Supabase Storage
- **Photo Quality Gate** (local): blur detection, lighting check, device-in-frame validation
- Quality gate UI: retake prompts with Malay feedback
- Basic ticket form (manual device selection, customer info)
- `POST /api/tickets` endpoint
- `GET /api/tickets` with filters
- Ticket list view (mobile)
- Ticket detail view (mobile)
- Ticket creation from web dashboard

### Week 4: AI Vision Integration + Damage Detection + Quick Repair
- OpenAI GPT-4o Vision integration: `POST /api/ai/identify-device`
- **Pre-existing Damage Detection**: same Vision call detects scratches, cracks, dents, water damage indicators
- Damage report overlay on intake photo with bounding boxes
- Device identification flow in mobile app (photo → matched device + damage report)
- Confidence threshold handling (≥80% auto-select, <80% manual fallback)
- "Not in catalog" handling + "quick add" flow
- Test against 20+ real device photos
- Polish intake UI: camera → quality gate → AI result + damage → confirm/edit → customer info
- **Quick Repair screen**: Typeahead device selection, quick-select issue dropdown, auto-filled pricing, QR code receipt generation
- **Voice Intake**: Microphone recording, `POST /api/ai/parse-voice-intake`, field extraction (device, issue, customer, price), confidence display
- `POST /api/tickets/quick` and `POST /api/tickets/voice` endpoints
- Malay translations for intake screens

**Month 1 Deliverable:** Staff can create a ticket via any of three paths: photo+AI (Full AI), typeahead+quick-select (Quick Repair), or voice (Voice Intake). Device identification via Vision. Customer info captured. Basic list and detail views work.

---

## Month 2: Technician Assessment + Pricing Engine

### Week 1: Ticket Lifecycle + Payment & Receipt
- Implement state machine transitions (including `paid` status, auto-approve for Quick Repair/Voice)
- `PATCH /api/tickets/:id/status` with validation
- `PATCH /api/tickets/:id/assess` for assessment submission
- `POST /api/tickets/:id/payment` and `PATCH /api/tickets/:id/complete-with-payment` endpoints
- Payment recording UI: amount, method (cash/QR/bank), auto-decrement parts inventory
- WhatsApp receipt generation + QR code receipt for anonymous tickets
- `GET /api/tickets/:token/public` — public ticket web view
- `PATCH /api/tickets/:token/subscribe` — customer subscribes via QR link
- Ticket history logging (audit table)
- Queue view for technicians (filter by outlet, status)
- "Assess Now" prompt after ticket creation (for technician-role staff)

### Week 2: AI Diagnosis Suggestions + Voice-to-Structured Notes
- `POST /api/ai/suggest-diagnosis` endpoint
- GPT-4o integration: device + symptoms → ranked diagnosis list
- Suggestion chips UI in assessment screen
- Technician override: select from suggestions or type custom diagnosis
- Parts auto-population from diagnosis (if mapped)
- **Voice-to-Structured Notes**:
  - `expo-av` audio recording in New Ticket + Assessment screens
  - `POST /api/ai/transcribe-voice` (Whisper API, Malay support)
  - `POST /api/ai/structure-notes` (GPT-4o extracts damage type, location, severity, symptoms)
  - Auto-fills customer notes with structured data
  - Voice memo playback in ticket detail

### Week 3: Parts Catalog + Pricing
- Parts CRUD (admin dashboard)
- Device-part compatibility mapping
- Part add/edit form with cost, retail price, stock quantity
- Pricing engine: calculate total from parts cost + labor hours
- Price override by technician (with audit trail)
- Estimate calculation and display in assessment screen

### Week 4: WhatsApp Setup + AI Drafting
- 360dialog account setup and WhatsApp Business API verification
- Message template creation + Meta approval
- `POST /api/webhooks/whatsapp` endpoint
- Trigger notifications on ticket status changes
- Send notification on `ticket_created` and `assessment_complete`
- **AI WhatsApp Message Drafting**:
  - `POST /api/ai/draft-whatsapp` endpoint
  - GPT-4o generates contextual messages: device, status, estimate, Malay tone
  - Staff sees preview: "Hantar" or "Edit Dahulu"
  - Context-aware: new vs returning customer, formal vs friendly
- Basic customer reply handling (`YES`/`NO` → auto-approve/decline)
- Malay WhatsApp message templates

**Month 2 Deliverable:** Technician can assess device with AI suggestions, get auto-calculated quotes, and system sends WhatsApp notifications to customers with approval flow.

---

## Month 3: Queue + Polish + Localization

### Week 1: Real-Time Queue
- Supabase Realtime integration (Postgres LISTEN/NOTIFY)
- Live queue updates (no pull-to-refresh needed)
- Live status badges
- New ticket alert for all devices in outlet
- Staff assignment tracking (who's working on what)

### Week 2: Customer Approval + WhatsApp Completion
- Robust WhatsApp reply parsing (YES/NO/questions/unknown)
- Auto-approve endpoint (`PATCH /api/tickets/:id/approve`)
- Approval confirmation notification
- Decline + cancellation handling
- Forward questions to staff notification
- Link-based approval fallback (web page for non-WhatsApp users)

### Week 3: Admin Dashboard v1
- Owner overview page: 4 stat cards, revenue chart, outlet performance
- Ticket list with search, filter, sort
- Outlet performance cards
- Common issues chart
- Revenue by outlet chart
- Export to CSV

### Week 4: Localization + Accessibility + Polish
- Complete Malay translations (mobile + web)
- Language switcher (persisted to user profile)
- Accessibility: proper labels, contrast ratios, screen reader support
- Performance: image compression, list virtualization
- Error handling: toast messages, retry on network failure
- Loading states: skeletons, progress indicators
- Empty states: helpful messaging when no tickets

**Month 3 Deliverable:** Complete MVP with real-time queue, WhatsApp approval flow, admin dashboard, full Malay localization. Shop can run daily operations end-to-end.

---

## Month 4: Reporting + Inventory

### Week 1: Reporting System + AI Learning Loop
- Daily report: tickets, revenue, per outlet
- Weekly report: trends, comparisons
- Monthly report: revenue trends, issue distribution
- Technician performance: tickets completed, avg assessment time, customer satisfaction
- Common issues drill-down: by device model, by outlet
- Export to CSV and PDF
- **Repair Outcome Learning Loop**:
  - Technician confirms actual diagnosis at repair completion
  - AI compares predicted vs actual diagnosis
  - Per-tenant calibration: confidence scores adjust to shop-specific patterns
  - Dashboard: "AI Accuracy: 87% this month" (vs global baseline)

### Week 2: Inventory Management
- Real-time stock tracking per part
- Auto-decrement stock on completed repair
- Low stock alerts (dashboard + mobile notification)
- Stock adjustment: manual add, discrepancy logging
- Supplier tracking per part
- Stock history log

### Week 3: Photo Documentation
- Before/after repair photos (internal documentation)
- Multiple photo upload per ticket
- Photo gallery view in ticket detail
- Damage severity tagging via AI
- Quality check photos for completed repairs

### Week 4: Edge Cases + Workflow Refinement
- Device dead / unknown diagnosis flow (diagnostic fee)
- Cancelled ticket + device return tracking
- Warranty tracking (30-day warranty on repairs)
- Customer search and history lookup
- Duplicate customer detection (by phone number)
- Ticket re-open flow (customer returns with same issue)

**Month 4 Deliverable:** Enhanced operational system with reporting, inventory alerts, photo documentation, and handling of all common edge cases.

---

## Month 5: Advanced AI + Offline Support

### Week 1: Voice-to-Text (Whisper)
- Whisper API integration
- Voice memo record button in mobile app
- Auto-transcription on upload
- Malay language support (Whisper supports Malay)
- Transcription attached to ticket notes

### Week 2: AI Damage Assessment
- Secondary photo analysis for damage severity
- Cracked screen detection: location, spread, severity
- Liquid damage indicator detection (LDI color recognition)
- Bent/dropped frame detection
- Combine with diagnosis suggestion for higher accuracy

### Week 3: Technician Time Tracking
- Start/stop timer per repair (tap to start, tap to stop)
- Actual vs estimated time comparison
- Time log per repair step
- Technician efficiency metrics
- Labor cost auto-recalculation based on actual time

### Week 4: Full Offline Mode
- Offline ticket creation (AsyncStorage queue)
- Offline assessment (save locally, sync later)
- Background sync when internet restored
- Conflict resolution (two staff editing same ticket)
- Pending sync badge and status display
- Offline device catalog (cached subset)

**Month 5 Deliverable:** Voice input for notes, AI damage analysis, technician time tracking, and robust offline support for shops with unreliable internet.

---

## Month 6: SaaS Extraction + Launch

### Week 1: Multi-Tenant Preparation
- Tenant isolation audit (RLS policies, API middleware)
- Per-tenant data separation
- Tenant onboarding flow (admin dashboard)
- Default seed data for new tenants
- Tenant-level settings (currency, timezone, language, WhatsApp config)

### Week 2: Public Signup + Onboarding
- Landing page for repair shops
- Signup form: business name, reg number, admin email, password
- Phone verification (WhatsApp OTP)
- Welcome wizard: add first outlet, invite staff, import device catalog
- First-time setup checklist

### Week 3: Subscription Billing
- Stripe integration (Malaysia: supports MYR, FPX online banking)
- Plan tiers: Basic (1-2 outlets), Pro (3-10), Enterprise (unlimited)
- Per-outlet or per-ticket pricing models
- Trial period: 14-day free trial
- Billing dashboard for platform owner (you)

### Week 4: White-Label + Cleanup
- Logo upload per tenant
- Primary color customization
- Custom WhatsApp business profile name
- Documentation: user guide, FAQ, troubleshooting
- Beta testing with 2-3 friendly shops
- Bug fixes from beta feedback

**Month 6 Deliverable:** SaaS platform ready for public launch. Other repair shops can sign up, configure, and start using the system independently.

---

## Risk Matrix

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Brother's shops resist new system | Medium | High | Work there for 2 weeks observing; build exactly what they need |
| WhatsApp API approval delayed | Medium | Medium | Start approval process in Week 1; use SMS fallback |
| AI API cost exceeds estimates | Low | Low | Token caching; compress images; rate-limit per ticket; photo quality gate reduces bad calls |
| Voice transcription accuracy (Malay) | Medium | Medium | Test Whisper on Malaysian Malay accents; fallback to manual edit |
| Damage detection false positives | Medium | Medium | Confidence threshold (≥80%); technician can dismiss individual detections |
| App Store rejection (SaaS phase) | Low | Medium | Target TestFlight initially; follow Apple guidelines strictly |
| Data migration issues | Low | High | Export/import tools from day 1; never lock data in |
| Solo burnout | Medium | High | Strict scope control; ship MVP at Month 3; take weekends off |
