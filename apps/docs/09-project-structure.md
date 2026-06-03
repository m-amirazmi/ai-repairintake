# Project Structure

## Monorepo Layout

```
repair-intake/                          # Monorepo root
├── .github/
│   └── workflows/
│       └── ci.yml                       # Lint, type-check, build on PR
│
├── apps/
│   ├── mobile/                          # Expo (React Native)
│   │   ├── app/
│   │   │   ├── (auth)/
│   │   │   │   ├── _layout.tsx          # Auth stack layout
│   │   │   │   ├── login.tsx            # Login screen
│   │   │   │   └── forgot-password.tsx  # Forgot password
│   │   │   │
│   │   │   ├── (app)/
│   │   │   │   ├── _layout.tsx          # Main tab navigator
│   │   │   │   ├── index.tsx            # Home / Queue (Jobs tab)
│   │   │   │   ├── new-ticket.tsx       # New ticket flow (camera + customer)
│   │   │   │   ├── ticket/
│   │   │   │   │   └── [id].tsx         # Ticket detail view
│   │   │   │   ├── assessment/
│   │   │   │   │   └── [id].tsx         # Technician assessment screen
│   │   │   │   ├── history.tsx          # Past tickets (History tab)
│   │   │   │   └── profile.tsx          # User profile (Profile tab)
│   │   │   │
│   │   │   └── _layout.tsx              # Root layout (auth gate)
│   │   │
│   │   ├── components/
│   │   │   ├── CameraCapture.tsx        # Camera viewfinder + capture
│   │   │   ├── DeviceCard.tsx           # Device info display
│   │   │   ├── TicketListItem.tsx       # Queue list item
│   │   │   ├── StatusBadge.tsx          # Status color chip
│   │   │   ├── AssessmentForm.tsx       # Diagnosis input
│   │   │   ├── SuggestionChips.tsx      # AI diagnosis suggestion pills
│   │   │   ├── PartsList.tsx            # Parts selection list
│   │   │   ├── PriceBreakdown.tsx       # Parts + labor calculator
│   │   │   ├── TimelineSelector.tsx     # ETA selector
│   │   │   ├── CustomerForm.tsx         # Customer info input
│   │   │   ├── ConfirmDialog.tsx        # Reusable confirmation modal
│   │   │   └── EmptyState.tsx           # Empty queue/list state
│   │   │
│   │   ├── hooks/
│   │   │   ├── useAuth.ts               # Auth session management
│   │   │   ├── useTickets.ts            # Ticket queries (TanStack Query)
│   │   │   ├── useDeviceIdentification.ts # AI vision hook
│   │   │   ├── useDiagnosis.ts           # AI diagnosis hook
│   │   │   ├── useNotifications.ts       # Real-time updates (Supabase)
│   │   │   └── useLanguage.ts            # i18n language hook
│   │   │
│   │   ├── lib/
│   │   │   ├── api.ts                   # API client (fetch wrapper + auth)
│   │   │   ├── ai.ts                    # OpenAI API helpers
│   │   │   ├── storage.ts               # AsyncStorage / SecureStore
│   │   │   ├── i18n.ts                  # Translation system
│   │   │   ├── offline.ts              # Offline queue manager
│   │   │   └── constants.ts             # App constants
│   │   │
│   │   ├── assets/
│   │   │   ├── images/
│   │   │   └── fonts/
│   │   │
│   │   ├── app.json                     # Expo config
│   │   ├── eas.json                     # EAS Build config
│   │   ├── tailwind.config.ts           # NativeWind config
│   │   └── package.json
│   │
│   └── web/                             # Next.js (Admin Dashboard + API)
│       ├── app/
│       │   ├── (auth)/
│       │   │   ├── login/
│       │   │   │   └── page.tsx         # Login page
│       │   │   └── layout.tsx           # Auth layout
│       │   │
│       │   ├── (dashboard)/
│       │   │   ├── layout.tsx           # Dashboard layout (sidebar + header)
│       │   │   ├── page.tsx             # Overview (redirect to /overview)
│       │   │   ├── overview/
│       │   │   │   └── page.tsx         # Owner dashboard home
│       │   │   ├── tickets/
│       │   │   │   ├── page.tsx         # All tickets list
│       │   │   │   └── [id]/
│       │   │   │       └── page.tsx     # Ticket detail view
│       │   │   ├── devices/
│       │   │   │   ├── page.tsx         # Device catalog
│       │   │   │   └── [id]/
│       │   │   │       └── page.tsx     # Device edit
│       │   │   ├── issues/
│       │   │   │   └── page.tsx         # Issue types
│       │   │   ├── parts/
│       │   │   │   ├── page.tsx         # Parts catalog
│       │   │   │   └── [id]/
│       │   │   │       └── page.tsx     # Part edit
│       │   │   ├── outlets/
│       │   │   │   ├── page.tsx         # Outlet list
│       │   │   │   └── [id]/
│       │   │   │       └── page.tsx     # Outlet detail + staff
│       │   │   ├── staff/
│       │   │   │   ├── page.tsx         # Staff list
│       │   │   │   └── [id]/
│       │   │   │       └── page.tsx     # Staff detail
│       │   │   ├── reports/
│       │   │   │   └── page.tsx         # Reports hub
│       │   │   ├── settings/
│       │   │   │   └── page.tsx         # Tenant settings
│       │   │   └── help/
│       │   │       └── page.tsx         # Help / documentation
│       │   │
│       │   ├── api/                     # Next.js Route Handlers (backend)
│       │   │   ├── auth/
│       │   │   │   └── session/
│       │   │   │       └── route.ts     # GET /api/auth/session
│       │   │   ├── tickets/
│       │   │   │   ├── route.ts         # GET + POST /api/tickets
│       │   │   │   └── [id]/
│       │   │   │       ├── route.ts     # GET /api/tickets/:id
│       │   │   │       ├── assess.ts    # PATCH /api/tickets/:id/assess
│       │   │   │       ├── status.ts    # PATCH /api/tickets/:id/status
│       │   │   │       └── approve.ts   # PATCH /api/tickets/:id/approve
│       │   │   ├── ai/
│       │   │   │   ├── identify-device/
│       │   │   │   │   └── route.ts     # POST /api/ai/identify-device
│       │   │   │   ├── suggest-diagnosis/
│       │   │   │   │   └── route.ts     # POST /api/ai/suggest-diagnosis
│       │   │   │   └── transcribe/
│       │   │   │       └── route.ts     # POST /api/ai/transcribe
│       │   │   ├── webhooks/
│       │   │   │   └── whatsapp/
│       │   │   │       └── route.ts     # POST /api/webhooks/whatsapp
│       │   │   ├── notifications/
│       │   │   │   └── send/
│       │   │   │       └── route.ts     # POST /api/notifications/send
│       │   │   └── admin/
│       │   │       ├── overview/
│       │   │       │   └── route.ts     # GET /api/admin/overview
│       │   │       ├── reports/
│       │   │       │   └── route.ts     # GET /api/admin/reports
│       │   │       ├── devices/
│       │   │       │   └── route.ts     # CRUD /api/admin/devices
│       │   │       ├── issues/
│       │   │       │   └── route.ts     # CRUD /api/admin/issues
│       │   │       ├── parts/
│       │   │       │   └── route.ts     # CRUD /api/admin/parts
│       │   │       └── users/
│       │   │           └── route.ts     # CRUD /api/admin/users
│       │   │
│       │   └── layout.tsx               # Root layout
│       │
│       ├── components/
│       │   ├── DashboardShell.tsx        # Sidebar + header layout
│       │   ├── StatCard.tsx              # Metric cards
│       │   ├── TicketTable.tsx           # Data table for tickets
│       │   ├── DeviceForm.tsx            # Add/edit device form
│       │   ├── PartForm.tsx              # Add/edit part form
│       │   ├── StaffForm.tsx             # Add/edit staff form
│       │   ├── StatusBadge.tsx           # Status color chip
│       │   ├── PriceCalculator.tsx       # Parts + labor calculator
│       │   └── ReportChart.tsx           # Recharts wrappers
│       │
│       ├── hooks/
│       │   ├── useAuth.ts               # Auth session
│       │   ├── useTickets.ts             # Ticket queries
│       │   ├── useDashboard.ts           # Overview metrics
│       │   └── useReports.ts             # Report data
│       │
│       ├── lib/
│       │   ├── api.ts                   # API client
│       │   ├── auth.ts                  # Auth helpers
│       │   ├── db.ts                    # Drizzle ORM client
│       │   └── utils.ts                 # General utilities
│       │
│       └── package.json
│
├── packages/
│   ├── shared/                          # Shared code between apps
│   │   ├── types/
│   │   │   ├── database.ts              # Zod schemas for all tables
│   │   │   ├── api.ts                   # API request/response types
│   │   │   ├── ticket.ts                # Ticket state machine types
│   │   │   └── index.ts                 # Barrel export
│   │   ├── constants/
│   │   │   ├── devices.ts               # Seed device data (50 models)
│   │   │   ├── issues.ts                # Common issues (9 types)
│   │   │   └── index.ts                 # Barrel export
│   │   ├── utils/
│   │   │   ├── formatters.ts            # Price/date/money formatters
│   │   │   ├── validators.ts            # Shared Zod validators
│   │   │   └── index.ts                 # Barrel export
│   │   └── package.json
│   │
│   └── ui/                              # Shared UI primitives (optional)
│       ├── components/
│       │   ├── Button.tsx
│       │   ├── Input.tsx
│       │   └── Card.tsx
│       └── package.json
│
├── supabase/
│   ├── migrations/                      # Drizzle or Supabase migrations
│   │   ├── 0000_initial.sql             # Initial schema
│   │   ├── 0001_add_outlets.sql         # Outlets migration
│   │   ├── 0002_add_notifications.sql   # Notifications migration
│   │   └── ...
│   └── seed.sql                         # Seed data for development
│
├── turbo.json                           # Turborepo configuration
├── package.json                         # Root workspace config
├── pnpm-workspace.yaml                  # pnpm workspace definition
├── .env.example                         # Environment variables template
└── README.md                            # Project README
```

## Naming Conventions

| Convention | Example |
|---|---|
| Tables | `snake_case`: `repair_tickets`, `device_issues` |
| Columns | `snake_case`: `customer_id`, `created_at` |
| TypeScript types | `PascalCase`: `Ticket`, `Customer` |
| API routes | `kebab-case`: `/api/ai/identify-device` |
| React components | `PascalCase`: `TicketListItem`, `CameraCapture` |
| Hooks | `use` prefix: `useTickets`, `useAuth` |
| Files (components) | `PascalCase.tsx`: `StatusBadge.tsx` |
| Files (utilities) | `camelCase.ts`: `formatters.ts` |

## Package Scripts

```json
{
  "scripts": {
    "dev": "turbo dev",
    "dev:mobile": "turbo dev --filter mobile",
    "dev:web": "turbo dev --filter web",
    "build": "turbo build",
    "build:mobile": "turbo build --filter mobile",
    "build:web": "turbo build --filter web",
    "lint": "turbo lint",
    "typecheck": "turbo typecheck",
    "test": "turbo test",
    "db:generate": "turbo db:generate",
    "db:migrate": "turbo db:migrate",
    "db:seed": "turbo db:seed",
    "clean": "turbo clean && rm -rf node_modules"
  }
}
```
