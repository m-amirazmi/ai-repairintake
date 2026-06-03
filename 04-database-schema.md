# Database Schema

## Entity Relationship Diagram

```
┌─────────────┐       ┌─────────────┐       ┌─────────────┐
│   TENANTS   │1─────∞│   OUTLETS   │1─────∞│    USERS    │
│  (1 record) │       │  (5 records)│       │   (staff)   │
└─────────────┘       └──────┬──────┘       └──────┬──────┘
                             │                      │
                             │1                     │
                             │                     ∞│
                             │               ┌──────┴──────┐
                             │               │   TICKETS   │
                             │               │  (core)     │
                             │               └──┬──┬──┬──┬─┘
                             │                  │  │  │  │
                             │1             ∞│   │  │∞ │∞ │1
                      ┌──────┴──────┐   ┌────┴┐ │  ┌┴──┴──────┐ ┌───────────┐
                      │ TICKET_     │   │ CUS-│ │  │  TICKET   │ │ PAYMENTS  │
                      │ HISTORY     │   │TOME-│ │  │  HISTORY  │ │ (receipt) │
                      └─────────────┘   │ RS  │ │  └───────────┘ └───────────┘
                                        └─────┘ │
                    ┌───────────────────────────┘
                    │
           ┌───────┴────────┐
           │  NOTIFICATIONS │
           └────────────────┘

┌─────────┐     ┌─────────────┐     ┌─────────┐     ┌──────────────────┐
│ DEVICES │∞────│DEVICE_ISSUES│────∞│ ISSUES  │     │ DEVICE_QUICK_    │
└─────────┘     └─────────────┘     └─────────┘     │ ISSUES           │
     │                                               │ (top-10 per      │
     │∞                                              │  device, cached  │
     │                                               │  for Quick Fix)  │
┌─────────┐                                          └──────────────────┘
│  PARTS  │ (tenant-managed)
└─────────┘
```

## Tables

### tenants

Top-level organization. Currently 1 record (brother's business).
On SaaS conversion, each new shop gets a tenant record.

| Column | Type | Constraints |
|---|---|---|
| id | uuid | PK, DEFAULT gen_random_uuid() |
| name | varchar(255) | NOT NULL |
| business_reg_no | varchar(50) | |
| settings | jsonb | DEFAULT '{"currency":"MYR","timezone":"Asia/Kuala_Lumpur","lang":"ms"}' |
| is_active | boolean | DEFAULT true |
| created_at | timestamptz | DEFAULT now() |
| updated_at | timestamptz | DEFAULT now() |

### outlets

Physical shop locations under a tenant.

| Column | Type | Constraints |
|---|---|---|
| id | uuid | PK, DEFAULT gen_random_uuid() |
| tenant_id | uuid | FK → tenants.id, NOT NULL |
| name | varchar(255) | NOT NULL (e.g., "Dungun 1", "Kerteh 2") |
| address | text | |
| phone | varchar(20) | |
| is_active | boolean | DEFAULT true |
| created_at | timestamptz | DEFAULT now() |

### users

Staff accounts with role-based access.

| Column | Type | Constraints |
|---|---|---|
| id | uuid | PK |
| tenant_id | uuid | FK → tenants.id, NOT NULL |
| outlet_id | uuid | FK → outlets.id (nullable — owners/managers see all outlets) |
| email | varchar(255) | UNIQUE, NOT NULL |
| phone | varchar(20) | |
| name | varchar(255) | NOT NULL |
| role | enum | `owner`, `manager`, `front_desk`, `technician` |
| avatar_url | text | |
| is_active | boolean | DEFAULT true |
| last_login_at | timestamptz | |
| created_at | timestamptz | DEFAULT now() |

### devices

Device model catalog. Seed data for common models.
Each tenant can extend and customize.

| Column | Type | Constraints |
|---|---|---|
| id | uuid | PK, DEFAULT gen_random_uuid() |
| tenant_id | uuid | FK → tenants.id (nullable — NULL = system-level seed data) |
| brand | varchar(100) | NOT NULL |
| model | varchar(100) | NOT NULL |
| variant | varchar(100) | (e.g., "Deep Purple") |
| model_number | varchar(50) | (e.g., "A2890") |
| category | enum | `smartphone`, `tablet`, `laptop`, `tv` |
| release_year | integer | |
| image_url | text | |
| is_active | boolean | DEFAULT true |
| created_at | timestamptz | DEFAULT now() |

### issues

Common repair issue definitions. Shared across tenants.

| Column | Type | Constraints |
|---|---|---|
| id | uuid | PK, DEFAULT gen_random_uuid() |
| name | varchar(255) | NOT NULL (e.g., "Penukaran Skrin") |
| name_en | varchar(255) | English translation |
| slug | varchar(100) | UNIQUE, NOT NULL (e.g., "screen_replacement") |
| category | enum | `screen`, `battery`, `charging`, `camera`, `audio`, `water`, `motherboard`, `other` |
| description | text | |
| symptoms | text[] | Array of common customer descriptions |
| is_active | boolean | DEFAULT true |
| created_at | timestamptz | DEFAULT now() |

### device_issues

Junction table linking which issues apply to which devices.

| Column | Type | Constraints |
|---|---|---|
| device_id | uuid | FK → devices.id, NOT NULL |
| issue_id | uuid | FK → issues.id, NOT NULL |
| labor_hours_base | decimal(3,1) | NOT NULL (base labor hours for this repair) |
| difficulty | enum | `easy`, `medium`, `hard` |
| is_common | boolean | DEFAULT false |
| **PRIMARY KEY** | (device_id, issue_id) | |

### parts

Parts catalog managed by the business owner. Tenant-scoped.

| Column | Type | Constraints |
|---|---|---|
| id | uuid | PK, DEFAULT gen_random_uuid() |
| tenant_id | uuid | FK → tenants.id, NOT NULL |
| name | varchar(255) | NOT NULL |
| description | text | |
| sku | varchar(100) | |
| cost_price | decimal(10,2) | NOT NULL DEFAULT 0 |
| retail_price | decimal(10,2) | NOT NULL DEFAULT 0 |
| labor_hours | decimal(3,1) | DEFAULT 0 |
| stock_quantity | integer | DEFAULT 0 |
| min_stock_alert | integer | DEFAULT 5 |
| supplier_name | varchar(255) | |
| supplier_part_number | varchar(100) | |
| device_compatibility | jsonb | Array of device UUIDs this part fits |
| is_active | boolean | DEFAULT true |
| created_at | timestamptz | DEFAULT now() |
| updated_at | timestamptz | DEFAULT now() |

### payments

Payment records for completed tickets. One ticket can have one payment (MVP).

| Column | Type | Constraints |
|---|---|---|
| id | uuid | PK, DEFAULT gen_random_uuid() |
| ticket_id | uuid | FK → tickets.id, UNIQUE, NOT NULL |
| amount | decimal(10,2) | NOT NULL |
| method | enum | `cash`, `qr_pay`, `bank_transfer`, NOT NULL |
| notes | text | |
| received_by | uuid | FK → users.id, NOT NULL |
| difference_from_estimate | decimal(10,2) | (e.g., -10.00 if discounted) |
| created_at | timestamptz | DEFAULT now() |

### device_quick_issues

Pre-cached top common issues per device for the Quick Repair quick-select dropdown.
Curated by the business owner via admin dashboard.

| Column | Type | Constraints |
|---|---|---|
| id | uuid | PK, DEFAULT gen_random_uuid() |
| device_id | uuid | FK → devices.id, NOT NULL |
| issue_id | uuid | FK → issues.id, NOT NULL |
| display_order | integer | NOT NULL DEFAULT 0 (sort order in quick-select list) |
| default_parts | jsonb | Pre-selected parts for this fix (see below) |
| default_price | decimal(10,2) | Cached total (parts + labor) for instant display |
| default_labor_hours | decimal(3,1) | DEFAULT 0 |
| is_active | boolean | DEFAULT true |
| tenant_id | uuid | FK → tenants.id, NOT NULL |
| created_at | timestamptz | DEFAULT now() |
| updated_at | timestamptz | DEFAULT now() |

**UNIQUE INDEX** on `(device_id, issue_id, tenant_id)`.

### device_quick_issues.default_parts JSONB schema

```json
[
  {
    "part_id": "uuid",
    "part_name": "Skrin OLED + Digitizer",
    "quantity": 1,
    "unit_retail": 339.00
  },
  {
    "part_id": "uuid",
    "part_name": "Gam perekat",
    "quantity": 1,
    "unit_retail": 11.00
  }
]
```

### customers

Customer records. Deduplicated by phone number within a tenant.
**NOTE:** Anonymous Quick Repair tickets may have `customer_name` and `customer_phone` on the ticket directly without a `customers` record. If customer later subscribes via QR, a customer record is created and linked.

| Column | Type | Constraints |
|---|---|---|
| id | uuid | PK, DEFAULT gen_random_uuid() |
| tenant_id | uuid | FK → tenants.id, NOT NULL |
| name | varchar(255) | NOT NULL |
| phone | varchar(20) | NOT NULL |
| email | varchar(255) | |
| preferred_lang | enum | `ms`, `en`, DEFAULT `ms` |
| total_visits | integer | DEFAULT 0 (incremented on each completed ticket) |
| created_at | timestamptz | DEFAULT now() |
| updated_at | timestamptz | DEFAULT now() |

**UNIQUE INDEX** on `(tenant_id, phone)` to prevent duplicates.

### notifications

WhatsApp message tracking log.

| Column | Type | Constraints |
|---|---|---|
| id | uuid | PK, DEFAULT gen_random_uuid() |
| ticket_id | uuid | FK → tickets.id, NOT NULL |
| type | enum | `ticket_created`, `assessment_complete`, `approval_confirmed`, `repair_complete`, `payment_receipt`, `ready_for_pickup`, `reminder` |
| recipient_phone | varchar(20) | NOT NULL |
| message_body | text | NOT NULL |
| status | enum | `pending`, `sent`, `delivered`, `read`, `failed` |
| external_message_id | varchar(255) | WhatsApp message ID (from API response) |
| error_message | text | If failed |
| sent_at | timestamptz | |
| delivered_at | timestamptz | |
| read_at | timestamptz | |
| created_at | timestamptz | DEFAULT now() |
| updated_at | timestamptz | DEFAULT now() |

**UNIQUE INDEX** on `(tenant_id, phone)` to prevent duplicates.

### tickets

The core table. Every repair job is a ticket.

| Column | Type | Constraints |
|---|---|---|
| id | uuid | PK, DEFAULT gen_random_uuid() |
| tenant_id | uuid | FK → tenants.id, NOT NULL |
| outlet_id | uuid | FK → outlets.id, NOT NULL |
| ticket_number | varchar(20) | UNIQUE, NOT NULL (format: `D1-042` or `042`) |
| customer_id | uuid | FK → customers.id (nullable — anonymous Quick Repair has no customer) |
| customer_name | varchar(255) | (for anonymous tickets without customer record) |
| customer_phone | varchar(20) | (for anonymous tickets; nullable) |
| device_id | uuid | FK → devices.id, NOT NULL |
| intake_type | enum | `full_ai`, `quick_repair`, `voice_intake`, DEFAULT `full_ai` |
| status | enum | `received`, `assessing`, `assessed`, `approved`, `in_progress`, `completed`, `paid`, `picked_up`, `cancelled` |
| received_by | uuid | FK → users.id (staff who created the ticket) |
| received_at | timestamptz | DEFAULT now() |
| intake_notes | text | Customer's reported problem |
| intake_photo_url | text | Photo of device (from Supabase Storage; null for Quick Repair) |
| intake_voice_url | text | Voice memo URL |
| intake_voice_transcript | text | Transcribed voice memo (Whisper) |
| intake_voice_structured | jsonb | Structured data extracted from voice (see below) |
| damage_detected | jsonb | AI pre-existing damage report (null for Quick Repair) |
| photo_quality_check | jsonb | Photo quality gate results (null for Quick Repair) |
| assessment | jsonb | See assessment schema below |
| technician_id | uuid | FK → users.id (technician assigned) |
| assessed_at | timestamptz | |
| estimated_cost | decimal(10,2) | Parts cost (calculated) |
| estimated_price | decimal(10,2) | Total retail price (auto-calculated) |
| final_price | decimal(10,2) | Actual price paid (may differ from estimate; for Quick Repair, this is the set price) |
| customer_approval | enum | `pending`, `approved`, `declined`, `auto_approved` (auto_approved for Quick Repair / Voice) |
| approved_at | timestamptz | |
| repair_notes | text | |
| repair_photos | text[] | Array of photo URLs |
| ai_whatsapp_drafts | jsonb[] | AI-generated WhatsApp message drafts |
| started_at | timestamptz | When repair began |
| completed_at | timestamptz | When repair finished |
| paid_at | timestamptz | When payment was recorded |
| payment_method | enum | `cash`, `qr_pay`, `bank_transfer` |
| qr_token | varchar(64) | UNIQUE, short token for public ticket web view (`/t/{token}`) |
| receipt_sent_via | enum | `whatsapp`, `qr_code`, `none` |
| picked_up_at | timestamptz | When customer collected device |
| created_at | timestamptz | DEFAULT now() |
| updated_at | timestamptz | DEFAULT now() |

### intake_voice_structured JSONB schema

```json
{
  "damage_type": "screen_crack",
  "damage_location": "bottom_right_corner",
  "damage_severity": "moderate",
  "symptoms": ["screen_crack", "touch_unresponsive"],
  "device_condition": "powered_on",
  "confidence": 0.92,
  "transcript_ms": "Skrin pecah di penjuru kanan bawah, sentuh tak berfungsi sepenuhnya",
  "transcript_en": "Screen cracked at bottom right corner, touch not fully working"
}
```

### damage_detected JSONB schema

```json
{
  "photo_quality": "good",
  "quality_warnings": [],
  "damages": [
    {
      "type": "scratch",
      "location": "back_panel",
      "severity": "minor",
      "description": "Goresan halus di panel belakang, berhampiran kamera",
      "bounding_box": { "x": 120, "y": 200, "width": 80, "height": 40 }
    },
    {
      "type": "crack",
      "location": "screen",
      "severity": "moderate",
      "description": "Retakan dari penjuru kiri bawah ke tengah skrin",
      "bounding_box": { "x": 50, "y": 400, "width": 300, "height": 2 }
    }
  ],
  "water_damage_indicators": {
    "detected": false,
    "confidence": 0.95
  },
  "ai_confidence": 0.88,
  "analyzed_at": "2026-01-15T10:30:00Z"
}
```

### photo_quality_check JSONB schema

```json
{
  "passed": true,
  "blur_score": 0.92,
  "lighting_score": 0.85,
  "device_in_frame": true,
  "device_coverage": 0.78,
  "warnings": [],
  "retake_reason": null
}
```

### assessment JSONB schema

```json
{
  "diagnosis": "Kerosakan motherboard — capacitor terbakar (C321)",
  "diagnosis_en": "Motherboard damage — burnt capacitor (C321)",
  "diagnosis_method": "ai_suggestion | manual",
  "suggested_issues": [
    {
      "issue_id": "uuid",
      "confidence": 0.40,
      "issue_name": "Battery Failure"
    },
    {
      "issue_id": "uuid",
      "confidence": 0.20,
      "issue_name": "Motherboard Short"
    }
  ],
  "notes": "Periksa power rail, tiada kerosakan cascade",
  "parts_used": [
    {
      "part_id": "uuid",
      "part_name": "Capacitor pack C1234",
      "quantity": 1,
      "unit_cost": 12.00,
      "unit_retail": 25.00,
      "line_total": 25.00
    }
  ],
  "labor_hours": 2.5,
  "labor_rate": 50.00,
  "labor_total": 125.00,
  "parts_total": 25.00,
  "estimate_total": 150.00,
  "timeline_days": 3,
  "timeline_label": "3-5 hari"
}
```

### ticket_history

Immutable audit log. One row per status change.

| Column | Type | Constraints |
|---|---|---|
| id | uuid | PK, DEFAULT gen_random_uuid() |
| ticket_id | uuid | FK → tickets.id, NOT NULL |
| status_from | enum | (nullable — null on first entry) |
| status_to | enum | NOT NULL |
| changed_by | uuid | FK → users.id, NOT NULL |
| notes | text | |
| metadata | jsonb | Extra context (e.g., AI confidence, WhatsApp message ID, voice transcript ID) |
| created_at | timestamptz | DEFAULT now() |

### notifications

WhatsApp message tracking log.

| Column | Type | Constraints |
|---|---|---|
| id | uuid | PK, DEFAULT gen_random_uuid() |
| ticket_id | uuid | FK → tickets.id, NOT NULL |
| type | enum | `ticket_created`, `assessment_complete`, `approval_confirmed`, `repair_complete`, `ready_for_pickup`, `reminder` |
| recipient_phone | varchar(20) | NOT NULL |
| message_body | text | NOT NULL |
| status | enum | `pending`, `sent`, `delivered`, `read`, `failed` |
| external_message_id | varchar(255) | WhatsApp message ID (from API response) |
| error_message | text | If failed |
| sent_at | timestamptz | |
| delivered_at | timestamptz | |
| read_at | timestamptz | |
| created_at | timestamptz | DEFAULT now() |

## Indexes

```sql
-- Tickets: fast lookup by outlet + status
CREATE INDEX idx_tickets_outlet_status ON tickets(outlet_id, status);

-- Tickets: by technician
CREATE INDEX idx_tickets_technician ON tickets(technician_id, status);

-- Tickets: by customer
CREATE INDEX idx_tickets_customer ON tickets(customer_id);

-- Tickets: by intake_type
CREATE INDEX idx_tickets_intake_type ON tickets(intake_type);

-- Tickets: by qr_token for public web view
CREATE UNIQUE INDEX idx_tickets_qr_token ON tickets(qr_token);

-- Customers: phone dedup
CREATE UNIQUE INDEX idx_customers_tenant_phone ON customers(tenant_id, phone);

-- Parts: by tenant
CREATE INDEX idx_parts_tenant ON parts(tenant_id);

-- Ticket history: for audit trail
CREATE INDEX idx_ticket_history_ticket ON ticket_history(ticket_id);

-- Notifications: by ticket
CREATE INDEX idx_notifications_ticket ON notifications(ticket_id);

-- Payments: by ticket (one-to-one)
CREATE UNIQUE INDEX idx_payments_ticket ON payments(ticket_id);

-- Device quick issues: by device
CREATE INDEX idx_device_quick_issues_device ON device_quick_issues(device_id, display_order);
```

## State Machine

Ticket lifecycle with allowed transitions:

```
QUICK REPAIR / VOICE:
  IN_PROGRESS ──▶ COMPLETED ──▶ PAID ──▶ PICKED_UP

FULL AI:
  RECEIVED ──▶ ASSESSING ──▶ ASSESSED ──▶ APPROVED ──▶ IN_PROGRESS ──▶ COMPLETED ──▶ PAID ──▶ PICKED_UP
      │            │            │              │                │              │
      │            │            │              │                │              │
      └────────────┴────────────┴──────▶ CANCELLED ◀─────────────────────────────┘
      (from RECEIVED, ASSESSING, or ASSESSED)     (from any state before PAID)
```

**Business Rules:**
- **Quick Repair / Voice Intake**: Automatically jumps to `in_progress` (skips assessment, auto-approved). `customer_approval` = `auto_approved`. `assessed_at` = `received_at`.
- Only RECEIVED → ASSESSING is allowed for Full AI (must be assessed first)
- Only ASSESSED → APPROVED (cannot approve without assessment)
- Only APPROVED → IN_PROGRESS (customer must approve first for Full AI)
- **PAID** status added between COMPLETED and PICKED_UP — payment required before handoff
- CANCELLED from any state before PAID (no cancellation after payment)
- PICKED_UP only from PAID
