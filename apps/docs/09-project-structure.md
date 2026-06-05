# Project Structure

> **Revision (June 2025):** Single Next.js app replaces `apps/mobile` (Expo) + `apps/web` (Next.js).
> See `02b-revised-pwa-plan.md` for rationale.

## Monorepo Layout

```
repair-intake/                          # Monorepo root
├── .github/
│   └── workflows/
│       └── ci.yml                       # Lint, type-check, build on PR
│
├── apps/
│   └── web/                             # Single Next.js application
│       ├── app/
│       │   ├── (auth)/                  # Auth pages (minimal layout)
│       │   │   ├── layout.tsx           # Auth layout (centered card, no nav)
│       │   │   ├── login/page.tsx       # Email + password login
│       │   │   └── forgot-password/page.tsx
│       │   │
│       │   ├── (app)/                   # Staff PWA (mobile-first, bottom tab nav)
│       │   │   ├── layout.tsx           # Tab bar (Queue | New | History | Profile)
│       │   │   ├── page.tsx             # Queue / Home (live ticket list)
│       │   │   ├── new/
│       │   │   │   ├── page.tsx         # Intake type picker (3 tiers)
│       │   │   │   ├── quick/page.tsx   # Quick Repair (<10s)
│       │   │   │   ├── voice/page.tsx   # Voice Intake (<5s)
│       │   │   │   └── full/page.tsx    # Full AI Intake (30-90s)
│       │   │   ├── ticket/
│       │   │   │   └── [id]/page.tsx    # Ticket detail view
│       │   │   ├── assessment/
│       │   │   │   └── [id]/page.tsx    # Technician assessment screen
│       │   │   ├── payment/
│       │   │   │   └── [id]/page.tsx    # Payment + receipt screen
│       │   │   ├── history/page.tsx     # Past tickets (scrollable)
│       │   │   └── profile/page.tsx     # User profile + settings
│       │   │
│       │   ├── (dashboard)/             # Admin dashboard (desktop, sidebar nav)
│       │   │   ├── layout.tsx           # Sidebar + header layout
│       │   │   ├── page.tsx             # Redirect to /overview
│       │   │   ├── overview/page.tsx    # Owner dashboard home
│       │   │   ├── tickets/
│       │   │   │   ├── page.tsx         # All tickets + filters + search
│       │   │   │   └── [id]/page.tsx    # Ticket detail
│       │   │   ├── devices/
│       │   │   │   ├── page.tsx         # Device catalog (table)
│       │   │   │   └── [id]/page.tsx    # Device edit form
│       │   │   ├── issues/page.tsx      # Issue types management
│       │   │   ├── parts/
│       │   │   │   ├── page.tsx         # Parts catalog (table)
│       │   │   │   └── [id]/page.tsx    # Part edit form
│       │   │   ├── outlets/
│       │   │   │   ├── page.tsx         # Outlet list
│       │   │   │   └── [id]/page.tsx    # Outlet detail + staff
│       │   │   ├── staff/
│       │   │   │   ├── page.tsx         # Staff list
│       │   │   │   └── [id]/page.tsx    # Staff detail + edit
│       │   │   ├── reports/page.tsx     # Reports hub
│       │   │   └── settings/page.tsx    # Tenant settings
│       │   │
│       │   ├── t/
│       │   │   └── [token]/page.tsx     # Public ticket view (no auth)
│       │   │
│       │   ├── api/                     # Next.js Route Handlers (backend)
│       │   │   ├── auth/session/route.ts     # GET current session
│       │   │   ├── tickets/
│       │   │   │   ├── route.ts              # GET + POST /api/tickets
│       │   │   │   ├── quick/route.ts        # POST quick repair ticket
│       │   │   │   ├── voice/route.ts        # POST voice intake ticket
│       │   │   │   └── [id]/
│       │   │   │       ├── route.ts          # GET + PATCH ticket
│       │   │   │       ├── assess/route.ts   # PATCH assessment
│       │   │   │       ├── status/route.ts   # PATCH status transition
│       │   │   │       ├── approve/route.ts  # PATCH customer approval
│       │   │   │       └── payment/route.ts  # POST payment
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
│       │   ├── manifest.ts             # PWA manifest (next/metadata)
│       │   └── layout.tsx              # Root layout (providers, theme, fonts)
│       │
│       ├── components/
│       │   ├── staff/                   # Staff PWA components
│       │   │   ├── TabBar.tsx           # Bottom tab navigation
│       │   │   ├── QueueList.tsx        # Live ticket queue
│       │   │   ├── TicketCard.tsx       # Queue item (~80px compact, swipe)
│       │   │   ├── CameraCapture.tsx    # getUserMedia / input capture
│       │   │   ├── VoiceRecorder.tsx    # MediaRecorder mic button
│       │   │   ├── DeviceTypeahead.tsx  # Quick Repair device search
│       │   │   ├── IssueSelector.tsx    # Quick-select issue chips
│       │   │   ├── PriceCard.tsx        # Auto-filled price display
│       │   │   ├── QualityGate.tsx      # Photo quality check overlay
│       │   │   ├── DamageOverlay.tsx    # AI damage annotations on photo
│       │   │   ├── StatusBadge.tsx      # Status color pill
│       │   │   ├── AssessmentForm.tsx   # Diagnosis + parts + pricing
│       │   │   ├── SuggestionChips.tsx  # AI diagnosis suggestion pills
│       │   │   ├── PartsPicker.tsx      # Parts multi-select
│       │   │   ├── PriceBreakdown.tsx   # Parts + labor calculator
│       │   │   ├── PaymentForm.tsx      # Payment method + amount
│       │   │   ├── QRReceipt.tsx        # QR code + public link display
│       │   │   ├── CustomerForm.tsx     # Name + phone input (Full AI)
│       │   │   ├── OfflineBanner.tsx    # Offline indicator banner
│       │   │   ├── EmptyState.tsx       # Empty queue/list placeholder
│       │   │   └── ConfirmDialog.tsx    # Reusable confirmation modal
│       │   │
│       │   ├── dashboard/               # Admin dashboard components
│       │   │   ├── DashboardShell.tsx   # Sidebar + header layout
│       │   │   ├── Sidebar.tsx          # Navigation sidebar
│       │   │   ├── Header.tsx           # Top bar (breadcrumb + user menu)
│       │   │   ├── StatCard.tsx         # Metric card (icon + value + trend)
│       │   │   ├── TicketTable.tsx      # Data table with filters
│       │   │   ├── DeviceForm.tsx       # Add/edit device
│       │   │   ├── PartForm.tsx         # Add/edit part
│       │   │   ├── StaffForm.tsx        # Add/edit staff
│       │   │   └── ReportChart.tsx      # Recharts wrapper
│       │   │
│       │   └── shared/                  # Shared UI primitives
│       │       ├── Button.tsx
│       │       ├── Input.tsx
│       │       ├── Card.tsx
│       │       ├── Toast.tsx
│       │       └── Icons.tsx            # Lucide icon wrappers
│       │
│       ├── hooks/
│       │   ├── useAuth.ts              # Supabase session + WebAuthn
│       │   ├── useTickets.ts           # TanStack Query hooks
│       │   ├── useTicket.ts            # Single ticket query
│       │   ├── useRealtime.ts          # Supabase Realtime subscription
│       │   ├── useCamera.ts            # getUserMedia hook
│       │   ├── useVoiceRecorder.ts     # MediaRecorder hook
│       │   ├── useDeviceSearch.ts      # Typeahead search hook
│       │   ├── useIssueQuickSelect.ts  # Top-10 issues per device
│       │   ├── useOffline.ts           # Online status + sync queue
│       │   ├── useBiometrics.ts        # WebAuthn credential management
│       │   ├── useLanguage.ts          # ms/en switching
│       │   ├── useDashboard.ts         # Overview metrics
│       │   └── useReports.ts           # Report data
│       │
│       ├── lib/
│       │   ├── db.ts                   # Drizzle ORM client + schema
│       │   ├── auth.ts                 # Supabase auth helpers + middleware
│       │   ├── api.ts                  # Client-side fetch wrapper (+ auth)
│       │   ├── ai.ts                   # OpenAI client (GPT-4o, Whisper)
│       │   ├── whatsapp.ts             # 360dialog client
│       │   ├── offline.ts              # IndexedDB queue manager
│       │   ├── sw.ts                   # Service worker registration
│       │   ├── i18n.ts                 # Translation system (ms/en)
│       │   ├── constants.ts            # App constants
│       │   └── utils.ts                # Formatters, cn(), etc.
│       │
│       ├── public/
│       │   ├── sw.js                   # Service worker (auto-generated)
│       │   ├── workbox-*.js            # Workbox runtime
│       │   ├── icons/
│       │   │   ├── icon-192.png        # PWA icon small
│       │   │   └── icon-512.png        # PWA icon large
│       │   ├── screenshots/
│       │   │   ├── queue.png           # PWA install screenshot
│       │   │   └── new-ticket.png      # PWA install screenshot
│       │   └── favicon.ico
│       │
│       ├── next.config.ts              # PWA plugin + config
│       ├── tailwind.config.ts          # Tailwind v4 + design tokens
│       └── package.json
│
├── packages/
│   └── shared/                         # Shared code
│       ├── types/
│       │   ├── database.ts             # Zod schemas for all tables
│       │   ├── api.ts                  # API request/response types
│       │   ├── ticket.ts               # Ticket state machine types
│       │   └── index.ts                # Barrel export
│       ├── constants/
│       │   ├── devices.ts              # Seed device data (50 models)
│       │   ├── issues.ts               # Common issues (9 types)
│       │   └── index.ts                # Barrel export
│       ├── utils/
│       │   ├── formatters.ts           # Price/date/money formatters
│       │   ├── validators.ts           # Shared Zod validators
│       │   └── index.ts                # Barrel export
│       └── package.json
│
├── supabase/
│   ├── migrations/
│   │   ├── 0000_initial.sql            # Initial schema
│   │   ├── 0001_seed_devices.sql       # Device catalog seed
│   │   └── ...
│   └── seed.sql                        # Development seed data
│
├── turbo.json                          # Turborepo configuration
├── package.json                        # Root workspace config
├── pnpm-workspace.yaml                 # pnpm workspace definition
├── .env.example                        # Environment variables template
└── README.md                           # Project README
```

## Route Group Strategy

Three route groups in a single Next.js app, each with its own layout:

| Route Group | Path | Layout | Auth | Users |
|---|---|---|---|---|
| `(auth)` | `/login`, `/forgot-password` | Centered card, no nav | None | Unauthenticated |
| `(app)` | `/`, `/new/*`, `/ticket/*`, `/assessment/*`, `/history`, `/profile` | Bottom tab bar, mobile-first | Required (all roles) | Staff (front desk, technician) |
| `(dashboard)` | `/overview`, `/tickets`, `/devices`, `/parts`, `/outlets`, `/staff`, `/reports`, `/settings` | Sidebar + header, desktop | Required (owner, manager) | Owner, manager |
| Public | `/t/[token]` | Minimal, no nav | None | Customers (via QR link) |

## Naming Conventions

| Convention | Example |
|---|---|
| Tables | `snake_case`: `tickets`, `device_quick_issues`, `notifications` |
| Columns | `snake_case`: `customer_id`, `created_at`, `intake_type` |
| TypeScript types | `PascalCase`: `Ticket`, `Customer`, `IntakeType` |
| API routes | `kebab-case`: `/api/ai/identify-device` |
| React components | `PascalCase`: `TicketCard`, `CameraCapture` |
| Hooks | `use` prefix: `useTickets`, `useAuth`, `useOffline` |
| Files (components) | `PascalCase.tsx`: `StatusBadge.tsx` |
| Files (utilities) | `camelCase.ts`: `formatters.ts` |

## Package Scripts

```json
{
  "scripts": {
    "dev": "turbo dev",
    "dev:web": "turbo dev --filter web",
    "build": "turbo build",
    "build:web": "turbo build --filter web",
    "lint": "turbo lint",
    "typecheck": "turbo typecheck",
    "test": "turbo test",
    "db:generate": "drizzle-kit generate",
    "db:migrate": "drizzle-kit migrate",
    "db:seed": "tsx supabase/seed.ts",
    "clean": "turbo clean && rm -rf node_modules"
  }
}
```
