# Tech Stack

## Frontend

| Layer | Technology | Version | Rationale |
|---|---|---|---|
| Staff App | Next.js PWA | 15 (App Router) | Mobile-first responsive UI, Service Worker offline, add to home screen |
| Admin Dashboard | Next.js | 15 (App Router) | Same React ecosystem, API routes in same repo, Vercel deployment |
| API | Next.js Route Handlers | 15 | TypeScript end-to-end, easy auth middleware, co-located with app |
| Styling | Tailwind CSS | v4 | Utility-first CSS, shared design tokens across PWA + dashboard |
| UI Components | shadcn/ui | latest | Accessible, customizable, works with Tailwind |
| PWA | `@serwist/next` | latest | Service Worker generation, offline caching, Background Sync |
| State Management | TanStack Query | v5 | Server state management, caching, optimistic updates |
| Forms | React Hook Form + Zod | latest | Type-safe validation, minimal re-renders |
| Charts (Dashboard) | Recharts | v2 | Simple charting for dashboard reports |

## Backend

| Layer | Technology | Rationale |
|---|---|---|
| API Framework | Next.js Route Handlers | TypeScript end-to-end, easy auth middleware, co-located with web app |
| ORM | Drizzle ORM | Type-safe SQL, lightweight, no codegen bloat, excellent Postgres support |
| Database | PostgreSQL (Supabase) | Managed, real-time subscriptions, built-in auth, file storage |
| Auth | Supabase Auth | Built-in role-based access, JWT tokens |
| File Storage | Supabase Storage | Device photos, damage documentation, S3-compatible API |
| Background Jobs | BullMQ + Redis (Month 5+) | WhatsApp sending queues, report generation |
| Validation | Zod | Request/response validation, shared between frontend and backend |

## AI Services

| Service | Model | Use Case | Estimated Cost |
|---|---|---|---|
| Device Identification | GPT-4o Vision | "What device is in this photo?" | ~$0.005/image |
| Diagnosis Suggestion | GPT-4o | "Given device X + symptoms Y, suggest likely issues" | ~$0.01/query |
| Voice-to-Text | Whisper API | Transcribe voice intake memo (Malay + English) | ~$0.006/minute |
| Voice Parse | GPT-4o | Extract structured data from voice transcript | ~$0.005/call |
| Damage Assessment | GPT-4o Vision | Assess damage severity from repair photos | ~$0.005/image |
| WhatsApp Drafting | GPT-4o | Generate contextual WhatsApp messages (Malay) | ~$0.003/draft |

### AI Cost Estimate (50 tickets/day, mixed intake)

| Service | Daily | Monthly |
|---|---|---|
| Vision (device ID + damage) | $0.25 | $7.50 |
| Diagnosis suggestions | $0.35 | $10.50 |
| Voice transcription + parse | $0.15 | $4.50 |
| WhatsApp drafts | $0.05 | $1.50 |
| **Total** | **$0.80** | **$24.00** |

Cost is negligible even at scale. Quick Repair and Voice Intake tickets avoid Vision calls
(only Full AI uses Vision), keeping costs lower than if every ticket went through Vision.

## External APIs

| Service | Provider | Use Case | Cost |
|---|---|---|---|
| WhatsApp Business API | 360dialog | Customer + staff notifications (Malaysia rates) | ~$50/month + ~RM0.05-0.10/msg |
| Stripe (Month 6) | Stripe | SaaS subscription billing | 2.9% + RM2.00/transaction |

## PWA Technologies

| API | Use Case | Browser Support |
|---|---|---|
| Service Worker | Offline caching, Background Sync | iOS Safari 15+, all Chrome |
| Web App Manifest | Add to Home Screen, fullscreen mode | iOS Safari, all Chrome |
| IndexedDB | Offline ticket queue, cached data | Universal |
| `getUserMedia` | Inline camera capture (v2) | iOS Safari 14+, all Chrome |
| `<input capture>` | System camera dialog (v1) | Universal |
| WebAuthn | Face ID / Touch ID biometric login | iOS Safari 14+, Android Chrome |
| MediaRecorder | Voice memo recording | iOS Safari 14.5+, all Chrome |
| Background Sync | Sync offline tickets when online | Chrome, not Safari (IndexedDB fallback) |
| Web Push (future) | Push notifications | Safari 16.4+, Chrome |

## DevOps

| Layer | Technology | Rationale |
|---|---|---|
| Monorepo Manager | Turborepo | Shared packages, cached builds, parallel tasks |
| Package Manager | pnpm | Fast, disk-space efficient, strict dependency resolution |
| Type Checking | TypeScript | `strict` mode across all packages |
| Linting | ESLint + Prettier | Consistent code style |
| Testing | Vitest | Fast, native TypeScript support |
| Hosting | Vercel | Free tier, auto-deploy from Git, HTTPS, edge functions |
| DB Hosting | Supabase Cloud | Free tier: 500MB DB, 1GB storage, 50MB auth users |

## Development Environment

| Tool | Purpose |
|---|---|
| Chrome DevTools | Mobile device emulation, PWA debugging, Service Worker inspection |
| Physical iPhone/Android | Real device testing (open `https://...` directly) |
| ngrok | Local tunnel for WhatsApp webhook testing |
| Hoppscotch / Bruno | API testing |
| TablePlus / DBeaver | Database GUI |
| VS Code | Primary IDE |

## Why This Stack (Solo Frontend Web Developer Perspective)

1. **One language, one framework**: TypeScript + Next.js for everything. No React Native learning curve. No dual-platform bugs.
2. **PWA over native app**: Ships in weeks, not months. Staff use it as a tool (9am-6pm), not a consumer app. Add to home screen = fullscreen, no URL bar. No app store review delays.
3. **WhatsApp as notification channel**: Every Malaysian phone has WhatsApp open. Staff get assigned-ticket notifications where they already are. Zero push notification infrastructure needed.
4. **Supabase over self-hosted**: No database server to maintain. Auth, storage, and real-time included.
5. **Vercel for everything**: One `git push` deploys the PWA, admin dashboard, and API. Instant HTTPS (required for PWA features). No separate mobile build pipeline.
6. **Drizzle over Prisma**: Better SQL control, no Rust binary dependency, simpler migration handling.

## NOT Using

| Technology | Why Not |
|---|---|
| Expo / React Native | Solo dev has zero mobile experience. PWA delivers same experience faster. |
| GraphQL (Apollo) | Overkill for this use case. REST is simpler and sufficient. |
| Separate backend (Express/Fastify) | Next.js API routes are sufficient. No need for separate service. |
| tRPC | Adds complexity. REST with Zod validation is clearer for this scale. |
| WebSockets (Socket.io) | Supabase Realtime handles this natively via Postgres LISTEN/NOTIFY. |
| Prisma ORM | Drizzle gives better SQL control and lighter footprint. |
| Firebase | Supabase provides Postgres (relational data) which is critical for this schema. |
| NativeWind / RN Reusables | Tailwind v4 + shadcn/ui works directly on web. No RN-compatible alternatives needed. |

## Migration Path to Native (If Ever Needed)

If App Store presence becomes necessary later:

1. **Capacitor** — Wrap the PWA in a Capacitor shell. Same web code, thin native layer.
   Gets camera, biometrics, push via Capacitor plugins. Zero code changes.

2. **Expo rebuild** — By then you have revenue, validated product, proven API.
   Hire a React Native developer. They build against a battle-tested backend.
