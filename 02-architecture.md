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
│             │                         │                         │           │
│             └─────────────┬───────────┴─────────────────────────┘           │
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
│  ┌───────────┐     ┌──────────────┐     ┌──────────────┐                    │
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
│  └───────────┘     └──────┬───────┘     └──────────────┘                    │
│        │                  │                   │                             │
│        └──────────────────┼───────────────────┘                             │
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

## Data Flow: Graduated Intake (3 Tiers)

### Tier 1: Voice Intake (<5 seconds)

```
STAFF     ──▶   TAP 🎤   ──▶   SPEAK    ──▶   AI        ──▶   CONFIRM   ──▶   TICKET
OPENS           MIC          "iPhone          PARSE         (1 tap)         CREATED
APP             BUTTON        14 Pro,         VOICE TO                      (auto-
                              skrin           FIELDS                        approved)
                              pecah,
                              RM350"
```

### Tier 2: Quick Repair (<10 seconds)

```
STAFF     ──▶   TYPE     ──▶   SELECT   ──▶   TAP       ──▶   TICKET
OPENS          "iPh..."        "Skrin         QUICK            CREATED
APP            (3 chars)       Pecah"         SUBMIT           (auto-
                → iPhone       (top-10        (price           approved,
                14 Pro         common         auto-filled      in_progress)
                auto-match)    issues)        from catalog)
```

### Tier 3: Full AI Intake (30-90 seconds) — existing flow

```
STAFF     ──▶   PHOTO   ──▶   AI     ──▶   MATCH   ──▶   TICKET
OPENS          CAPTURE       VISION        CATALOG        CREATED
APP                           API
                              │
CUSTOMER  ◀──  WHATSAPP  ◀──  NOTIF   ◀──  SAVE    ◀────┘
GETS            API            SERVICE       TO DB
WHATAPP
```

## Data Flow: Payment & Receipt (at Completion)

```
TECH       ──▶   TAP        ──▶   PAYMENT   ──▶   CONFIRM   ──▶   PARTS
MARKS            "SELESAI"        SCREEN          PAYMENT         DECREMENTED
COMPLETE         (status:         SLIDES UP       (cash/QR/       INVENTORY
                 completed)       auto-filled     bank)
                                  amount,
                                  select method
                                                        │
                                          ┌─────────────┴──────────────┐
                                          │                            │
                                    PHONE PROVIDED?              NO PHONE?
                                          │                            │
                                    ┌─────▼─────┐              ┌──────▼──────┐
                                    │ WHATSAPP  │              │ QR RECEIPT  │
                                    │ RECEIPT   │              │ SCREEN      │
                                    │ SENT      │              │ (scan to    │
                                    └───────────┘              │  subscribe) │
                                                               └─────────────┘

```

## Data Flow: Technician Assessment

```
TECH      ──▶   OPEN    ──▶   AI       ──▶   TECH     ──▶   SAVE     ──▶   WHATSAPP
OPENS           TICKET         DIAGNOSE        SELECTS        ASSESS          TO CUST
QUEUE                          API             DIAGNOSIS      MENT            (QUOTE)

                                                                                   │
                                                                          ┌────────┴────────┐
                                                                          │ CUSTOMER         │
                                                                          │ REPLIES YES/NO   │
                                                                          └─────────────────┘
```

## Real-Time Queue (Month 3+)

Supabase Realtime broadcasts ticket status changes to all connected clients:

```
┌─────────────────┐         ┌──────────────────┐         ┌─────────────────┐
│  TECHNICIAN     │◀────────│    SUPABASE      │◀────────│   FRONT DESK    │
│  PHONE          │  WS PUSH│    REALTIME      │  UPDATE │   CREATES       │
│  (listens)      │         │  (Postgres       │         │   TICKET        │
│                 │         │   changes)       │         │                 │
└─────────────────┘         └──────────────────┘         └─────────────────┘
```

## Role-Aware Unified Flow (Updated)

Any user can advance a ticket through any state based on their role.
Quick Repair and Voice Intake tickets skip assessment and auto-approve.

```mermaid
stateDiagram-v2
    [*] --> RECEIVED: Full AI intake (front desk)
    [*] --> IN_PROGRESS: Quick Repair / Voice (auto-approved)

    RECEIVED --> ASSESSING: Technician starts assessment
    ASSESSING --> ASSESSED: Technician saves diagnosis + quote
    ASSESSED --> APPROVED: Customer replies YES (WhatsApp)
    ASSESSED --> CANCELLED: Customer replies NO
    RECEIVED --> CANCELLED: Customer changes mind
    ASSESSING --> CANCELLED: Device beyond repair

    APPROVED --> IN_PROGRESS: Technician starts repair
    IN_PROGRESS --> COMPLETED: Repair done
    COMPLETED --> PAID: Payment recorded
    PAID --> PICKED_UP: Customer collects device

    CANCELLED --> [*]
    PICKED_UP --> [*]
```

**Quick Repair / Voice Intake path:**

- Skips `received` → `assessing` → `assessed` → approval
- Goes directly to `in_progress` (auto-approved, technician already verified at counter)
- Payment recorded at `completed`, then `paid`, then `picked_up`

**Full AI path:**

- Front desk staff receives device, technician assesses, customer must approve via WhatsApp before repair begins

## Compressed Flow (Technician as Front Desk)

When a technician handles intake directly, the flow adapts to how well they already know the fix:

| Fix Known?                   | Path                  | Steps                                                                                                                                                          | Time    |
| ---------------------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------- |
| Yes (known fix, known price) | Quick Repair or Voice | 1. Tap "Baiki Cepat" or 🎤 → 2. Speak/typeahead device + issue → 3. Confirm → ticket auto-approved, in progress                                                | <10 sec |
| No (needs diagnosis)         | Full AI               | 1. Snap photo → AI ID → 2. Enter customer info → 3. Submit → 4. "Assess Now?" → 5. AI-suggested diagnosis → 6. Parts + pricing → 7. Save → WhatsApp quote sent | 1-3 min |

## Key Architecture Principles

1. **Stateless API**: Each request is self-contained; no server-side sessions.
2. **Database as source of truth**: All ticket state in PostgreSQL; real-time broadcasts for live updates.
3. **AI as a service**: OpenAI calls are isolated behind `/api/ai/*` endpoints; easy to swap models later.
4. **File storage separation**: Photos stored in Supabase Storage, DB holds URLs only.
5. **Idempotent notifications**: WhatsApp message deduplication via external_message_id.
6. **Graceful degradation**: If AI API is down, fall back to manual device selection and diagnosis entry.
