# System Architecture

## High-Level Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              CLIENT LAYER                                   │
│  ┌──────────────────────┐  ┌──────────────────────┐  ┌────────────────────┐ │
│  │   MOBILE APP         │  │   ADMIN DASHBOARD    │  │   CUSTOMER WEB     │ │
│  │   (Expo / iOS)       │  │   (Next.js / Web)    │  │   (Lightweight)    │ │
│  │                      │  │                      │  │                    │ │
│  │  Front desk staff    │  │  Owner/Manager       │  │  View quote        │ │
│  │  Technicians         │  │  Reports             │  │  Approve/Reject    │ │
│  │  Queue view          │  │  Catalog management  │  │                    │ │
│  │  Assessment          │  │  Staff management    │  │                    │ │
│  └──────────┬───────────┘  └──────────┬───────────┘  └──────────┬─────────┘ │
│             │                         │                       │             │
│             └─────────────┬───────────┴───────────────────────┘             │
│                           │                                                 │
│                    ┌──────┴──────┐                                          │
│                    │  API LAYER  │                                          │
│                    │  Next.js    │                                          │
│                    │  Route      │                                          │
│                    │  Handlers   │                                          │
│                    └──────┬──────┘                                          │
│                           │                                                 │
│        ┌──────────────────┼──────────────────┐                              │
│        ▼                  ▼                  ▼                              │
│  ┌──────────┐     ┌──────────────┐     ┌──────────────┐                    │
│  │  AI       │     │  CORE        │     │  EXTERNAL    │                    │
│  │  SERVICE  │     │  SERVICES    │     │  SERVICES    │                    │
│  │           │     │              │     │              │                    │
│  │ OpenAI    │     │ Auth         │     │ WhatsApp     │                    │
│  │ GPT-4o    │     │ (Supabase)   │     │ Business API │                    │
│  │ Vision    │     │              │     │ (360dialog)  │                    │
│  │ GPT-4o    │     │ Notifications│     │              │                    │
│  │ Text      │     │ (Queue)      │     │              │                    │
│  │ Whisper   │     │ File Storage │     │              │                    │
│  │ (later)   │     │ (Supabase)   │     │              │                    │
│  └──────────┘     └──────┬───────┘     └──────────────┘                    │
│        │                  │                   │                              │
│        └──────────────────┼───────────────────┘                              │
│                           ▼                                                 │
│                    ┌──────────────┐                                         │
│                    │   DATABASE   │                                         │
│                    │  PostgreSQL  │                                         │
│                    │  (Supabase)  │                                         │
│                    │              │                                         │
│                    │  Real-time   │                                         │
│                    │  subscriptions                                         │
│                    └──────────────┘                                         │
└─────────────────────────────────────────────────────────────────────────────┘
```

## Data Flow: New Ticket Creation

```
STAFF     ──▶   PHOTO   ──▶   AI     ──▶   MATCH   ──▶   TICKET
OPENS          CAPTURE       VISION        CATALOG        CREATED
APP                           API

                                                          │
CUSTOMER  ◀──  WHATSAPP  ◀──  NOTIF   ◀──  SAVE    ◀─────┘
GETS            API            SERVICE       TO DB
WHATAPP
```

## Data Flow: Technician Assessment

```
TECH     ──▶   OPEN    ──▶   AI      ──▶   TECH    ──▶   SAVE    ──▶  WHATSAPP
OPENS          TICKET        DIAGNOSE      SELECTS       ASSESS       TO CUST
QUEUE                        API           DIAGNOSIS     MENT         (QUOTE)

                                                                │
                                                        ┌───────▼────────┐
                                                        │ CUSTOMER        │
                                                        │ REPLIES YES/NO │
                                                        └────────────────┘
```

## Real-Time Queue (Month 3+)

Supabase Realtime broadcasts ticket status changes to all connected clients:

```
┌──────────────┐         ┌──────────────┐         ┌──────────────┐
│  TECHNICIAN  │◀────────│  SUPABASE    │◀────────│  FRONT DESK  │
│  PHONE       │  WS PUSH│  REALTIME    │  UPDATE │  CREATES     │
│  (listens)   │         │  (Postgres   │         │  TICKET      │
│              │         │   changes)   │         │              │
└──────────────┘         └──────────────┘         └──────────────┘
```

## Role-Aware Unified Flow

Any user can advance a ticket through any state based on their role.
No enforced separate workflows — the ticket lifecycle is the single source of truth.

```
┌──────────┐     ┌──────────┐     ┌──────────┐
│ RECEIVED │────▶│ASSESSING │────▶│ ASSESSED │
│ (intake) │     │ (tech)   │     │ (priced) │
└────┬─────┘     └──────────┘     └────┬─────┘
     │                                 │
     │  Front desk staff               │  Customer approves via WhatsApp
     │  does this part                 │
     │                                 ▼
     │                          ┌──────────────┐
     │  ──────────────────────▶ │  IN PROGRESS │
     │  If tech is at counter,  │  (repairing) │
     │  they flow straight      └──────┬───────┘
     │  through both                   │
     │                                 ▼
     │                          ┌──────────────┐
     │                          │  COMPLETED   │
     │                          │  (done)      │
     │                          └──────┬───────┘
     │                                 │
     │                                 ▼
     │                          ┌──────────────┐
     │                          │  PICKED UP   │
     │                          │  (collected) │
     │                          └──────────────┘
     │
     └──▶ Anyone with a phone can receive, anyone with tech role can assess.
          The app shows the right UI for the right role at the right time.
```

## Compressed Flow (Technician as Front Desk)

When a technician handles intake directly:

```
TECHNICIAN AT COUNTER:
    1. Opens app → taps "New Ticket"
    2. Snaps photo → AI identifies device
    3. Enters customer info + notes
    4. Submits (status: RECEIVED)
    5. App prompts: "Assess now or add to queue?"
    6. Taps "Assess Now"
    7. Diagnoses, parts, pricing all in one flow
    8. Saves assessment (status: ASSESSED)
    9. WhatsApp quote sent to customer

    Total time: 1-3 minutes for the entire intake + assessment cycle.
```

## Key Architecture Principles

1. **Stateless API**: Each request is self-contained; no server-side sessions.
2. **Database as source of truth**: All ticket state in PostgreSQL; real-time broadcasts for live updates.
3. **AI as a service**: OpenAI calls are isolated behind `/api/ai/*` endpoints; easy to swap models later.
4. **File storage separation**: Photos stored in Supabase Storage, DB holds URLs only.
5. **Idempotent notifications**: WhatsApp message deduplication via external_message_id.
6. **Graceful degradation**: If AI API is down, fall back to manual device selection and diagnosis entry.
