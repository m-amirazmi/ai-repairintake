# Tech Stack

## Frontend

| Layer | Technology | Version | Rationale |
|---|---|---|---|
| Mobile App | Expo (React Native) | SDK 52 | OTA updates without App Store review, single codebase, excellent camera API |
| Mobile Navigation | Expo Router | v3 | File-based routing, deep linking support |
| Admin Web | Next.js | 15 (App Router) | Same React ecosystem, API routes in same repo, Vercel deployment |
| Styling (Mobile) | NativeWind | v4 | Tailwind-compatible styling for React Native |
| Styling (Web) | Tailwind CSS | v4 | Utility-first CSS, shared design tokens with mobile |
| UI Components (Web) | shadcn/ui | latest | Accessible, customizable, works with Tailwind |
| State Management | TanStack Query | v5 | Server state management, caching, optimistic updates |
| Forms | React Hook Form + Zod | latest | Type-safe validation, minimal re-renders |
| Charts (Web) | Recharts | v2 | Simple charting for dashboard reports |

## Backend

| Layer | Technology | Rationale |
|---|---|---|
| API Framework | Next.js Route Handlers | TypeScript end-to-end, easy auth middleware, co-located with web app |
| ORM | Drizzle ORM | Type-safe SQL, lightweight, no codegen bloat, excellent Postgres support |
| Database | PostgreSQL (Supabase) | Managed, real-time subscriptions, built-in auth, file storage |
| Auth | Supabase Auth | Built-in role-based access, JWT tokens, works with mobile SDK |
| File Storage | Supabase Storage | Device photos, damage documentation, S3-compatible API |
| Background Jobs | BullMQ + Redis (Month 5+) | WhatsApp sending queues, report generation |
| Validation | Zod | Request/response validation, shared between frontend and backend |

## AI Services

| Service | Model | Use Case | Estimated Cost |
|---|---|---|---|
| Device Identification | GPT-4o Vision | "What device is in this photo?" | ~$0.005/image |
| Diagnosis Suggestion | GPT-4o | "Given device X + symptoms Y, suggest likely issues" | ~$0.01/query |
| Voice-to-Text (Month 5) | Whisper API | Transcribe customer notes from voice memo | ~$0.006/minute |
| Damage Assessment (Month 5) | GPT-4o Vision | Assess damage severity from repair photos | ~$0.005/image |

### AI Cost Estimate (50 tickets/day)

| Service | Daily | Monthly |
|---|---|---|
| Vision (device ID) | $0.25 | $7.50 |
| Diagnosis (per assessment) | $0.50 | $15.00 |
| **Total** | **$0.75** | **$22.50** |

Cost is negligible even at scale. The value delivered (speed, accuracy) far exceeds the cost.

## External APIs

| Service | Provider | Use Case | Cost |
|---|---|---|---|
| WhatsApp Business API | 360dialog | Customer notifications (Malaysia rates) | ~$50/month + ~RM0.05-0.10/msg |
| Stripe (Month 6) | Stripe | SaaS subscription billing | 2.9% + RM2.00/transaction |

## DevOps

| Layer | Technology | Rationale |
|---|---|---|
| Monorepo Manager | Turborepo | Shared packages, cached builds, parallel tasks |
| Package Manager | pnpm | Fast, disk-space efficient, strict dependency resolution |
| Type Checking | TypeScript | `strict` mode across all packages |
| Linting | ESLint + Prettier | Consistent code style |
| Testing | Vitest | Fast, native TypeScript support |
| Hosting (Web) | Vercel | Free tier, auto-deploy from Git, edge functions |
| Hosting (DB) | Supabase Cloud | Free tier: 500MB DB, 1GB storage, 50MB auth users |
| Mobile Builds | Expo EAS Build | Cloud builds for iOS, no local Xcode required for CI |
| Mobile Distribution | TestFlight (Apple) | Internal testing distribution |

## Development Environment

| Tool | Purpose |
|---|---|
| Xcode | iOS simulator (optional, only for debugging native issues) |
| Expo Go | Quick mobile testing during development |
| ngrok | Local tunnel for WhatsApp webhook testing |
| Hoppscotch / Bruno | API testing |
| TablePlus / DBeaver | Database GUI |
| VS Code | Primary IDE |

## Why This Stack (Solo Developer Perspective)

1. **TypeScript everywhere**: One language for mobile, web, and API. No context switching.
2. **Expo over bare React Native**: No native build step during development. OTA updates prevent App Store delays for bug fixes.
3. **Next.js over separate backend**: API routes co-located with admin dashboard. One deployment. One repo to manage.
4. **Supabase over self-hosted**: No database server to maintain. Auth, storage, and real-time included.
5. **Turborepo over Nx**: Lighter weight, simpler config for monorepos of this size.
6. **Drizzle over Prisma**: Better SQL control, no Rust binary dependency, simpler migration handling.

## NOT Using

| Technology | Why Not |
|---|---|
| GraphQL (Apollo) | Overkill for this use case. REST is simpler and sufficient. |
| React Native CLI | Expo covers all needed native features (camera, audio). |
| Separate backend (Express/Fastify) | Next.js API routes are sufficient. No need for separate service. |
| tRPC | Adds complexity. REST with Zod validation is clearer for this scale. |
| WebSockets (Socket.io) | Supabase Realtime handles this natively via Postgres LISTEN/NOTIFY. |
| Prisma ORM | Drizzle gives better SQL control and lighter footprint. |
| Firebase | Supabase provides Postgres (relational data) which is critical for this schema. |
