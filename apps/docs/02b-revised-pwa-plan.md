# Revised Plan: Single Next.js PWA (No Expo)

**Decision date:** June 2025
**Rationale:** Solo frontend web developer with zero mobile experience. Expo React Native
with camera, biometrics, offline queues, and push notifications as a first mobile project
would burn months on RN-specific problems instead of shipping product. A well-built PWA
on Next.js delivers the same experience in weeks, not months.

## What Changes

| Before (Expo) | After (PWA) |
|---|---|
| `apps/mobile` (Expo RN) + `apps/web` (Next.js) | `apps/web` (Next.js) — one app |
| Expo Camera | `getUserMedia` / `<input capture>` |
| `expo-local-authentication` (Face ID) | WebAuthn (`navigator.credentials`) |
| `expo-secure-store` | IndexedDB + Web Crypto |
| AsyncStorage offline queue | Service Worker + IndexedDB + Background Sync |
| `expo-av` (audio recording) | `MediaRecorder` API |
| NativeWind + RN Reusables | Tailwind CSS v4 + shadcn/ui |
| EAS Build / TestFlight | `vercel` deploy (instant) |
| Push notifications for staff | Supabase Realtime (in-app) + WhatsApp (out-of-app) |
| Two codebases to maintain | One codebase |

## What Stays the Same

- Next.js 15 App Router (API routes + web app)
- Supabase (DB, Auth, Storage, Realtime)
- Drizzle ORM
- OpenAI GPT-4o (Vision, Text, Whisper)
- 360dialog WhatsApp Business API
- Tailwind CSS v4 + shadcn/ui
- TanStack Query, React Hook Form, Zod
- All database schema, state machine, business logic

---

## Revised Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         SINGLE NEXT.JS APPLICATION                           │
│                                                                              │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │                          CLIENT LAYER                                 │   │
│  │                                                                       │   │
│  │  ┌─────────────────────────┐  ┌──────────────────────────────────┐   │   │
│  │  │   STAFF PWA             │  │   ADMIN DASHBOARD                │   │   │
│  │  │   (Mobile-First UI)     │  │   (Desktop Web)                  │   │   │
│  │  │                         │  │                                  │   │   │
│  │  │  Route group: (app)     │  │  Route group: (dashboard)        │   │   │
│  │  │  • Queue / Home         │  │  • Overview + reports            │   │   │
│  │  │  • New Ticket (3 tiers) │  │  • Ticket management             │   │   │
│  │  │  • Ticket detail        │  │  • Catalog (devices/parts)       │   │   │
│  │  │  • Assessment           │  │  • Staff management              │   │   │
│  │  │  • Payment / Receipt    │  │  • Outlet management             │   │   │
│  │  │  • History / Profile    │  │                                  │   │   │
│  │  │                         │  │                                  │   │   │
│  │  │  PWA features:          │  │                                  │   │   │
│  │  │  • Add to Home Screen   │  │                                  │   │   │
│  │  │  • Service Worker cache │  │                                  │   │   │
│  │  │  • Offline support      │  │                                  │   │   │
│  │  │  • WebAuthn biometrics  │  │                                  │   │   │
│  │  │  • Camera via browser   │  │                                  │   │   │
│  │  └───────────┬─────────────┘  └──────────────┬───────────────────┘   │   │
│  │              │                                │                       │   │
│  │  ┌───────────┴────────────────────────────────┴───────────────────┐   │   │
│  │  │                    PUBLIC TICKET VIEW                           │   │   │
│  │  │                    Route: /t/[token]                            │   │   │
│  │  │                    • View ticket status                         │   │   │
│  │  │                    • Subscribe to WhatsApp                      │   │   │
│  │  └────────────────────────────────────────────────────────────────┘   │   │
│  │              │                                │                       │   │
│  └──────────────┼────────────────────────────────┼───────────────────────┘   │
│                 │                                │                           │
│          ┌──────┴──────┐                         │                           │
│          │  API LAYER  │                         │                           │
│          │  Next.js    │◀────────────────────────┘                           │
│          │  Route      │                                                     │
│          │  Handlers   │                                                     │
│          └──────┬──────┘                                                     │
│                 │                                                             │
│  ┌──────────────┼──────────────────┐                                         │
│  ▼              ▼                  ▼                                         │
│  ┌───────────┐  ┌──────────────┐   ┌──────────────┐                          │
│  │ AI        │  │ CORE         │   │ EXTERNAL     │                          │
│  │ SERVICE   │  │ SERVICES     │   │ SERVICES     │                          │
│  │           │  │              │   │              │                          │
│  │ OpenAI    │  │ Auth         │   │ WhatsApp     │                          │
│  │ GPT-4o V  │  │ (Supabase)   │   │ Business API │                          │
│  │ GPT-4o T  │  │              │   │ (360dialog)  │                          │
│  │ Whisper   │  │ Real-time    │   │              │                          │
│  │           │  │ Storage      │   │              │                          │
│  └─────┬─────┘  └──────┬───────┘   └──────┬───────┘                          │
│        │               │                   │                                  │
│        └───────────────┼───────────────────┘                                  │
│                        ▼                                                      │
│                 ┌──────────────┐                                              │
│                 │  DATABASE    │                                              │
│                 │  PostgreSQL  │                                              │
│                 │  (Supabase)  │                                              │
│                 │              │                                              │
│                 │  Real-time   │                                              │
│                 │  subs        │                                              │
│                 └──────────────┘                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Staff Notification Strategy (No Push Notifications Required)

Three layers, zero native push:

### Layer 1: Real-Time Queue (primary — covers 90%)

Supabase Realtime WebSocket subscribed to `tickets` table changes. When the staff PWA
is open (queue view), new tickets and status changes appear instantly with animation
and an optional sound cue. Same as delivery drivers using tablets — the app IS the
notification surface.

```
STAFF PWA OPEN ──▶ Supabase Realtime WS ──▶ New ticket appears in queue instantly
                                           Status changes update live
```

### Layer 2: WhatsApp Fallback (out-of-app — covers remaining 10%)

Same 360dialog infrastructure used for customer messages. Additional staff notification
templates:

| Trigger | Message |
|---|---|
| Ticket assigned | *"Tiket baru #D1-042: iPhone 14 Pro — Skrin Pecah. Di-assign kepada anda."* |
| Assessment needed | *"Tiket #D1-042 menunggu penilaian. Sila semak sekarang."* |
| Approval received | *"Pelanggan #D1-042 telah meluluskan sebut harga RM350. Sila mula baiki."* |
| Daily summary | *"Ringkasan hari ini (Dungun 1): 12 tiket selesai, RM2,840 terkumpul."* |

Cost: fractions of a cent per message. Every Malaysian phone has WhatsApp.

### Layer 3: Web Push (future — optional)

PWAs on Android Chrome and iOS Safari 16.4+ support Web Push API. Skip for v1 — it's
fiddly on iOS and not needed given WhatsApp covers out-of-app notifications already.

---

## Camera Approach

### Option A: System Camera Dialog (v1 — simplest)

```tsx
<input
  type="file"
  accept="image/*"
  capture="environment"
  onChange={handleCapture}
/>
```

Opens the native camera app. Works on iOS Safari and Chrome. Simple, reliable, zero
permission headaches.

### Option B: Inline Camera (v2 — more polish)

```ts
const stream = await navigator.mediaDevices.getUserMedia({
  video: { facingMode: "environment", aspectRatio: 4/5 }
});
videoRef.current.srcObject = stream;
```

Inline viewfinder inside the page. Requires HTTPS (Vercel provides this). Camera
permission prompt on first use. Can overlay the AI guidelines / framing hints.

**Decision: Start with Option A (system dialog) for v1.** It ships faster, needs zero
custom UI, and actually gives better photo quality since it uses the native camera app
with stabilization and auto-focus. Switch to Option B if staff complain about the flow.

---

## Biometric Login

WebAuthn API replaces `expo-local-authentication`:

```ts
// Register biometric credential on first login
const credential = await navigator.credentials.create({
  publicKey: {
    challenge: new Uint8Array(32),
    rp: { name: "Repair Intake" },
    user: { id: userId, name: email, displayName: name },
    pubKeyCredParams: [{ type: "public-key", alg: -7 }],
    authenticatorSelection: {
      authenticatorAttachment: "platform",
      userVerification: "required"
    }
  }
});
// Store credential ID in IndexedDB

// On subsequent logins, trigger Face ID / Touch ID
const assertion = await navigator.credentials.get({
  publicKey: { challenge, allowCredentials: [{ id: storedCredId, type: "public-key" }] }
});
// Verify assertion on server, return Supabase session
```

**Flow:**
1. First login: email + password (Supabase Auth)
2. After login success, prompt: "Log masuk lebih pantas dengan Face ID?"
3. If yes → register WebAuthn credential → store in IndexedDB + users table
4. Next login: tap "Log Masuk dengan Face ID" → OS biometric prompt → verified → session

Supported on iOS Safari 14+ and Android Chrome. Works with Face ID, Touch ID, and
Android fingerprint/face unlock.

---

## Offline Mode

### Approach: Service Worker + IndexedDB + Background Sync

```
                  ┌─────────────┐
                  │  App Shell  │◀── Service Worker caches HTML/CSS/JS
                  │  (cached)   │
                  └──────┬──────┘
                         │
              ┌──────────┴──────────┐
              │                     │
         ONLINE?               OFFLINE?
              │                     │
              ▼                     ▼
    ┌─────────────────┐    ┌─────────────────┐
    │ API call        │    │ Store in        │
    │ (fetch)         │    │ IndexedDB       │
    └────────┬────────┘    │ (offline queue) │
             │             └────────┬────────┘
             │                      │
             │             ┌───────▼────────┐
             │             │ Background     │
             │             │ Sync registers │
             │             └───────┬────────┘
             │                     │
             │        ┌────────────▼────────┐
             │        │ Connection restored │
             │        │ → Sync queue to API │
             │        └────────────┬────────┘
             │                     │
             └─────────────────────┘
                           │
                           ▼
                  ┌─────────────────┐
                  │ UI updates      │
                  │ (TanStack Query │
                  │  cache inval)   │
                  └─────────────────┘
```

### What works offline:
- View cached ticket list (last fetched)
- Create Quick Repair & Voice Intake tickets (stored locally, synced later)
- View cached device catalog

### What requires online:
- AI calls (Vision, Diagnosis, Voice parse) — obviously
- WhatsApp sending (queued for when online)
- Supabase Realtime updates

### UI Indicators:
- Offline banner at top (same design as original spec)
- Pending sync badge showing queued items count
- "Dicipta di luar talian" label on offline-created tickets

---

## Revised Project Structure

```
repair-intake/
├── apps/
│   └── web/                              # Single Next.js app
│       ├── app/
│       │   ├── (auth)/                   # Auth pages (no layout frame)
│       │   │   ├── layout.tsx
│       │   │   ├── login/page.tsx
│       │   │   └── forgot-password/page.tsx
│       │   │
│       │   ├── (app)/                    # Staff PWA (mobile-first, tab nav)
│       │   │   ├── layout.tsx            # Tab bar + auth gate
│       │   │   ├── page.tsx              # Queue / Home
│       │   │   ├── new/
│       │   │   │   ├── page.tsx          # Intake type picker
│       │   │   │   ├── quick/page.tsx    # Quick Repair (<10s)
│       │   │   │   ├── voice/page.tsx    # Voice Intake (<5s)
│       │   │   │   └── full/page.tsx     # Full AI Intake (30-90s)
│       │   │   ├── ticket/[id]/page.tsx  # Ticket detail
│       │   │   ├── assessment/[id]/page.tsx  # Technician assessment
│       │   │   ├── payment/[id]/page.tsx # Payment / receipt
│       │   │   ├── history/page.tsx      # Past tickets
│       │   │   └── profile/page.tsx      # Profile + settings
│       │   │
│       │   ├── (dashboard)/              # Admin dashboard (desktop web)
│       │   │   ├── layout.tsx            # Sidebar + header
│       │   │   ├── page.tsx              # Overview (redirect)
│       │   │   ├── overview/page.tsx
│       │   │   ├── tickets/
│       │   │   │   ├── page.tsx          # All tickets + filters
│       │   │   │   └── [id]/page.tsx     # Ticket detail
│       │   │   ├── devices/
│       │   │   │   ├── page.tsx          # Device catalog
│       │   │   │   └── [id]/page.tsx
│       │   │   ├── issues/page.tsx       # Issue types
│       │   │   ├── parts/
│       │   │   │   ├── page.tsx          # Parts catalog
│       │   │   │   └── [id]/page.tsx
│       │   │   ├── outlets/
│       │   │   │   ├── page.tsx          # Outlet list
│       │   │   │   └── [id]/page.tsx
│       │   │   ├── staff/
│       │   │   │   ├── page.tsx          # Staff list
│       │   │   │   └── [id]/page.tsx
│       │   │   ├── reports/page.tsx      # Reports hub
│       │   │   └── settings/page.tsx     # Tenant settings
│       │   │
│       │   ├── t/[token]/page.tsx        # Public ticket view (no auth)
│       │   │
│       │   ├── api/                      # API routes (unchanged)
│       │   │   ├── auth/session/route.ts
│       │   │   ├── tickets/
│       │   │   │   ├── route.ts          # GET + POST /api/tickets
│       │   │   │   ├── quick/route.ts    # POST /api/tickets/quick
│       │   │   │   ├── voice/route.ts    # POST /api/tickets/voice
│       │   │   │   └── [id]/
│       │   │   │       ├── route.ts
│       │   │   │       ├── assess/route.ts
│       │   │   │       ├── status/route.ts
│       │   │   │       ├── approve/route.ts
│       │   │   │       └── payment/route.ts
│       │   │   ├── ai/
│       │   │   │   ├── identify-device/route.ts
│       │   │   │   ├── parse-voice-intake/route.ts
│       │   │   │   ├── suggest-diagnosis/route.ts
│       │   │   │   ├── transcribe/route.ts
│       │   │   │   └── draft-whatsapp/route.ts
│       │   │   ├── webhooks/whatsapp/route.ts
│       │   │   ├── notifications/send/route.ts
│       │   │   └── admin/
│       │   │       ├── overview/route.ts
│       │   │       ├── reports/route.ts
│       │   │       ├── devices/route.ts
│       │   │       ├── issues/route.ts
│       │   │       ├── parts/route.ts
│       │   │       └── users/route.ts
│       │   │
│       │   ├── manifest.ts              # PWA manifest (generateMetadata)
│       │   └── layout.tsx               # Root layout (providers, theme)
│       │
│       ├── components/
│       │   ├── staff/                    # Staff PWA components
│       │   │   ├── TabBar.tsx            # Bottom tab navigation
│       │   │   ├── QueueList.tsx         # Ticket queue (live)
│       │   │   ├── TicketCard.tsx        # Queue item (~80px compact)
│       │   │   ├── CameraCapture.tsx     # Camera via getUserMedia
│       │   │   ├── VoiceRecorder.tsx     # MediaRecorder for voice
│       │   │   ├── DeviceTypeahead.tsx   # Quick Repair device search
│       │   │   ├── IssueSelector.tsx     # Quick-select issue chips
│       │   │   ├── PriceCard.tsx         # Auto-filled price display
│       │   │   ├── StatusBadge.tsx       # Status color chip
│       │   │   ├── AssessmentForm.tsx    # Diagnosis + parts + pricing
│       │   │   ├── SuggestionChips.tsx   # AI diagnosis suggestions
│       │   │   ├── PaymentForm.tsx       # Payment method + amount
│       │   │   ├── QRReceipt.tsx         # QR code receipt display
│       │   │   ├── OfflineBanner.tsx     # Offline indicator banner
│       │   │   └── EmptyState.tsx        # No tickets, no results
│       │   │
│       │   ├── dashboard/                # Admin dashboard components
│       │   │   ├── DashboardShell.tsx
│       │   │   ├── StatCard.tsx
│       │   │   ├── TicketTable.tsx
│       │   │   ├── DeviceForm.tsx
│       │   │   ├── PartForm.tsx
│       │   │   ├── StaffForm.tsx
│       │   │   └── ReportChart.tsx
│       │   │
│       │   └── shared/                   # Shared between staff + dashboard
│       │       ├── Button.tsx
│       │       ├── Input.tsx
│       │       ├── Card.tsx
│       │       ├── ConfirmDialog.tsx
│       │       └── Toast.tsx
│       │
│       ├── hooks/
│       │   ├── useAuth.ts               # Supabase session + WebAuthn
│       │   ├── useTickets.ts            # TanStack Query hooks
│       │   ├── useRealtime.ts           # Supabase Realtime subscription
│       │   ├── useCamera.ts             # getUserMedia hook
│       │   ├── useVoiceRecorder.ts      # MediaRecorder hook
│       │   ├── useOffline.ts            # Online status + sync queue
│       │   ├── useBiometrics.ts         # WebAuthn credential mgmt
│       │   └── useLanguage.ts           # ms/en switching
│       │
│       ├── lib/
│       │   ├── db.ts                    # Drizzle ORM client
│       │   ├── auth.ts                  # Supabase auth helpers
│       │   ├── api.ts                   # API client (fetch + auth)
│       │   ├── ai.ts                    # OpenAI client
│       │   ├── whatsapp.ts             # 360dialog client
│       │   ├── offline.ts              # IndexedDB queue manager
│       │   ├── sw.ts                    # Service worker registration
│       │   ├── i18n.ts                  # Translation system
│       │   └── utils.ts                 # Formatters, helpers
│       │
│       ├── public/
│       │   ├── sw.js                    # Service worker (built by next-pwa)
│       │   ├── icons/                   # PWA icons (192, 512)
│       │   └── screenshots/             # PWA install screenshots
│       │
│       ├── next.config.ts               # PWA plugin config
│       ├── tailwind.config.ts           # Tailwind v4 + design tokens
│       └── package.json
│
├── packages/
│   └── shared/                          # Types, constants, utils (unchanged)
│       ├── types/
│       │   ├── database.ts
│       │   ├── api.ts
│       │   └── ticket.ts
│       ├── constants/
│       │   ├── devices.ts               # 50-model seed data
│       │   └── issues.ts                # 9 common issues
│       └── utils/
│           ├── formatters.ts
│           └── validators.ts
│
├── supabase/
│   ├── migrations/
│   └── seed.sql
│
├── turbo.json
├── package.json
├── pnpm-workspace.yaml
└── .env.example
```

---

## Revised Tech Stack

| Layer | Before | After |
|---|---|---|
| **Staff app** | Expo React Native | Next.js PWA (mobile-first Tailwind) |
| **Admin dashboard** | Next.js | Next.js (unchanged) |
| **API** | Next.js Route Handlers | Next.js Route Handlers (unchanged) |
| **Styling** | NativeWind + Tailwind | Tailwind CSS v4 only |
| **UI Components** | RN Reusables + shadcn/ui | shadcn/ui only |
| **Camera** | expo-camera | `getUserMedia` / `<input capture>` |
| **Biometrics** | expo-local-authentication | WebAuthn API |
| **Secure storage** | expo-secure-store | IndexedDB + Web Crypto |
| **Offline queue** | AsyncStorage | IndexedDB + Service Worker + Background Sync |
| **Voice recording** | expo-av | MediaRecorder API |
| **PWA** | — | `@serwist/next` (next-pwa successor) |
| **Build/deploy** | EAS Build + Vercel | Vercel only |
| **Testing** | Xcode simulator | Chrome DevTools mobile view + real device |
| **Everything else** | Supabase, Drizzle, OpenAI, 360dialog | Unchanged |

---

## Revised Timeline (3 Months to MVP)

### Month 1: Foundation + All Three Intake Tiers

**Week 1: Scaffold + Database**
- Initialize monorepo (Turborepo + pnpm)
- Scaffold Next.js app with App Router
- Configure Tailwind v4 + shadcn/ui + design tokens
- Set up Supabase (local dev + cloud)
- Configure Drizzle ORM, write initial migration
- Seed device catalog (50 models) + issues (9 types)
- Seed device_quick_issues (top-10 per device)

**Week 2: Auth + PWA Shell**
- Supabase Auth (email + password)
- Role-based middleware (`owner`, `manager`, `front_desk`, `technician`)
- Login page with "remember me" + WebAuthn registration
- Staff PWA tab shell (Queue, New, History, Profile tabs)
- PWA manifest + service worker + install prompt
- Dark mode toggle + persistence
- Malay/English i18n system

**Week 3: Quick Repair + Voice Intake**
- Quick Repair screen: typeahead device + issue selector + price card
- Voice Intake: MediaRecorder → Whisper → field extraction → confirm
- `POST /api/ai/parse-voice-intake`
- `POST /api/tickets/quick` + `POST /api/tickets/voice`
- Ticket list view (queue) with TanStack Query
- Supabase Realtime subscription for live queue

**Week 4: Full AI Intake**
- Camera capture component (system dialog + optional inline)
- Photo upload to Supabase Storage
- Photo quality gate (blur/lighting/device-in-frame)
- `POST /api/ai/identify-device` (GPT-4o Vision + damage detection)
- Full AI intake flow: photo → AI match → customer info → ticket created
- Ticket detail view
- "Not in catalog" + quick-add flow

**Month 1 Deliverable:** Staff can create tickets via all three intake tiers.
Queue updates live. Device identification works. Auth and roles in place.

---

### Month 2: Assessment + WhatsApp + Payment

**Week 1: Assessment Flow**
- State machine transitions (PATCH status)
- Assessment screen: AI diagnosis suggestions → confirm/adjust → parts → pricing
- `POST /api/ai/suggest-diagnosis`
- `PATCH /api/tickets/[id]/assess`
- Auto-calculated estimate from parts + labor
- Assessment auto-save (debounced 500ms)

**Week 2: WhatsApp Integration**
- 360dialog setup + message templates
- `POST /api/webhooks/whatsapp` webhook
- Customer notifications on status changes
- AI WhatsApp message drafting (`POST /api/ai/draft-whatsapp`)
- YES/NO reply parsing for customer approval
- Staff WhatsApp notifications (assignment, reminders)
- `POST /api/tickets/[id]/approve`

**Week 3: Payment + Receipt**
- Payment screen: amount, method (cash/qr/bank), confirm
- Parts inventory auto-decrement on payment
- WhatsApp receipt for customers with phone
- QR code receipt for anonymous customers
- Public ticket view (`/t/[token]`)
- Subscribe-to-WhatsApp flow from QR page

**Week 4: Admin Dashboard v1**
- Overview: 4 stat cards + revenue chart
- Ticket list with search/filter/sort
- Device/issue/parts catalog management
- Staff + outlet management
- Export to CSV

**Month 2 Deliverable:** End-to-end repair flow working. Assessment with AI.
WhatsApp notifications. Payment + receipt. Admin dashboard for owner.

---

### Month 3: Polish + Edge Cases + Offline

**Week 1: Real-Time Polish**
- Queue animations (new tickets slide in)
- Sound cue for new tickets
- Pull-to-refresh fallback
- Loading skeletons + empty states
- Error toasts + retry on failure

**Week 2: Localization + Accessibility**
- Complete Malay translations (staff + customer)
- Language switcher (persisted to user profile)
- Screen reader support (aria labels)
- Touch targets ≥44px everywhere
- Keyboard avoiding on forms

**Week 3: Edge Cases + Hardening**
- Dead device / unknown diagnosis flow
- Cancelled ticket + device return
- Price override with audit trail
- Duplicate customer detection
- Ticket re-open flow
- 30-day warranty tracking

**Week 4: Offline Mode**
- Service Worker caching (app shell + API responses)
- IndexedDB offline queue for ticket creation
- Background Sync for queued tickets
- Offline banner + pending sync badge
- Cached device catalog for offline typeahead

**Month 3 Deliverable:** Production-ready MVP. 5-outlet deployment. Staff can
run daily operations end-to-end. Offline mode for unreliable connections.

---

### Months 4-6: SaaS Extraction (unchanged from original roadmap)

- Multi-tenant isolation (RLS, middleware)
- Public signup + onboarding wizard
- Stripe subscription billing (MYR)
- White-label (logo, colors, WhatsApp profile)
- Reporting + AI learning loop
- Beta with 2-3 friendly shops → public launch

---

## What We Give Up vs Expo

| Concern | Reality |
|---|---|
| **App Store presence** | Not needed for staff tool. Home screen shortcut is faster. No App Store review delays for bug fixes. |
| **Buttery animations** | CSS animations + view transitions API are 90% of native. Staff using a business tool don't care about 60fps scroll physics. |
| **Native push notifications** | WhatsApp is better — it's already open on every Malaysian phone. Staff are in the app during work hours anyway. |
| **Inline camera viewfinder** | System camera dialog is simpler and gives better photos (native stabilization, auto-focus). Can add inline viewfinder later with `getUserMedia` if needed. |
| **Biometrics** | WebAuthn works on iOS Safari 14+ and triggers Face ID / Touch ID natively. Same OS prompt, same user experience. |
| **Offline reliability** | Service Workers + IndexedDB are battle-tested. More reliable than Expo's AsyncStorage sync (which often needs native modules). |

## What We Gain

| Gain | Impact |
|---|---|
| **Ship in weeks, not months** | Eliminates the React Native learning curve entirely |
| **One codebase** | No dual-platform bugs. One set of components, hooks, styles |
| **Instant deployment** | `git push` → Vercel deploys in seconds. No EAS build queue, no TestFlight review, no OTA update headaches |
| **Debugging** | Chrome DevTools instead of React Native Debugger. Console.log actually works |
| **Third-party libs** | Any React/web library works. No "does this work in React Native?" research |
| **Testing on real devices** | Open `https://...` on any phone. No sideloading, no TestFlight invites |
| **Updates** | Staff refresh the page (or service worker auto-updates). No app store review, no OTA rollouts |

## Why This Works for This Specific Product

1. **Repair shop staff aren't consumers.** They're doing a job. They don't care about
   app store ratings or buttery scroll. They care about speed and reliability.

2. **The app is used during work hours only.** Staff open it at 9am, close at 6pm.
   It's a tool, not a "check 50 times a day" consumer app. Always-on PWA in a browser
   tab is the natural usage pattern.

3. **WhatsApp is the universal notification channel.** In Malaysia, everyone has WhatsApp
   open. Pushing staff notifications through WhatsApp means zero adoption friction.
   No "enable push notifications" prompt, no "I didn't see the notification" complaints.

4. **The admin dashboard was already Next.js.** The API was already Next.js. Now
   everything is one app. No mental overhead of "is this a mobile bug or an API bug?"

5. **SaaS customers onboard via web anyway.** When you extract to SaaS, shop owners
   sign up on a website. Their staff open a URL. Nobody needs an app store.

---

## Migration Path to Native (If Ever Needed)

If App Store presence becomes a business requirement later:

1. **Capacitor wrapper** — Wrap the PWA in Capacitor for App Store deployment.
   Same web code, thin native shell. Gets camera, biometrics, push via Capacitor plugins.
   Zero code changes to the web app.

2. **Expo rebuild** — By then you have revenue, validated product, and can hire
   a React Native developer. They build against your proven API. You already know
   exactly what screens and flows are needed.

Both paths start from a working, battle-tested product — not from speculation.
