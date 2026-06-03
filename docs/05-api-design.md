# API Design

## Authentication

All endpoints require Bearer token (Supabase JWT).
Token is automatically attached by the Supabase client SDK on both mobile and web.

### POST /api/auth/session

Get current session. Returns user with role and outlet.

```json
// Response 200
{
  "user": {
    "id": "uuid",
    "name": "Amir Hassan",
    "email": "amir@repairshop.my",
    "role": "technician",
    "outlet_id": "uuid",
    "outlet_name": "Dungun 1",
    "tenant_id": "uuid",
    "tenant_name": "Brother's Repair"
  }
}
```

---

## Tickets

### POST /api/tickets

Create new ticket (front desk intake).

```json
// Request
{
  "outlet_id": "uuid (required)",
  "customer": {
    "name": "Ahmad bin Abdullah (required)",
    "phone": "+60123456789 (required)",
    "email": "ahmad@email.com (optional)",
    "preferred_lang": "ms"
  },
  "device_id": "uuid (optional — if known from AI)",
  "intake_photo_url": "https://storage.supabase.co/... (optional)",
  "intake_notes": "Pelanggan kata skrin pecah, sentuh tak berfungsi"
}

// Response 201
{
  "ticket": {
    "id": "uuid",
    "ticket_number": "D1-042",
    "status": "received",
    "customer": { ... },
    "device": { ... },
    "created_at": "2026-05-26T10:32:00+08:00"
  }
}
```

### GET /api/tickets

List tickets with filters.

Query params: `outlet_id`, `status`, `technician_id`, `date_from`, `date_to`, `page`, `limit`

```json
// Response 200
{
  "tickets": [
    {
      "id": "uuid",
      "ticket_number": "D1-042",
      "status": "received",
      "customer_name": "Ahmad bin Abdullah",
      "device_model": "iPhone 14 Pro",
      "outlet_name": "Dungun 1",
      "created_at": "2026-05-26T10:32:00+08:00",
      "waiting_minutes": 10
    }
  ],
  "total": 45,
  "page": 1,
  "limit": 20
}
```

### GET /api/tickets/:id

Get single ticket with all relations.

```json
// Response 200
{
  "ticket": {
    "id": "uuid",
    "ticket_number": "D1-042",
    "status": "assessed",
    "outlet": { "id": "uuid", "name": "Dungun 1" },
    "customer": { "id": "uuid", "name": "Ahmad bin Abdullah", "phone": "+60123456789" },
    "device": { "id": "uuid", "brand": "Apple", "model": "iPhone 14 Pro" },
    "received_by": { "id": "uuid", "name": "Raj Kumar" },
    "technician": { "id": "uuid", "name": "Amir Hassan" },
    "intake_notes": "Skrin pecah, sentuh tak berfungsi",
    "intake_photo_url": "https://storage...",
    "assessment": { ... },
    "estimated_price": 131.50,
    "history": [
      { "status_to": "received", "changed_by": "Raj Kumar", "created_at": "..." },
      { "status_to": "assessing", "changed_by": "Amir Hassan", "created_at": "..." },
      { "status_to": "assessed", "changed_by": "Amir Hassan", "created_at": "..." }
    ],
    "notifications": [
      { "type": "ticket_created", "status": "delivered", "sent_at": "..." },
      { "type": "assessment_complete", "status": "delivered", "sent_at": "..." }
    ]
  }
}
```

### PATCH /api/tickets/:id/assess

Submit technician assessment. Auto-calculates pricing from parts catalog.

```json
// Request
{
  "diagnosis": "Kerosakan motherboard — capacitor terbakar (C321)",
  "diagnosis_method": "ai_suggestion",
  "parts_needed": [
    { "part_id": "uuid", "quantity": 1 },
    { "part_id": "uuid", "quantity": 1 }
  ],
  "labor_hours": 2.5,
  "timeline_days": 3,
  "notes": "Periksa power rail, tiada kerosakan cascade",
  "notify_customer": true
}

// Response 200
{
  "ticket": { ... },
  "assessment": {
    "parts_total": 25.00,
    "labor_total": 125.00,
    "estimate_total": 150.00,
    "timeline_days": 3
  },
  "notification_sent": true
}
```

### PATCH /api/tickets/:id/status

Update ticket status. Triggers notifications based on new status.

```json
// Request
{
  "status": "in_progress",
  "notes": "Memulakan pembaikan — pelanggan telah lulus"
}

// Response 200
{
  "ticket": { ... },
  "history_entry": { ... }
}
```

### POST /api/tickets/quick

Create Quick Repair ticket. Typeahead device + quick-select issue + optional customer.
Ticket auto-approved and set to `in_progress`.

```json
// Request
{
  "outlet_id": "uuid (required)",
  "device_id": "uuid (required)",
  "issue_id": "uuid (required)",
  "customer_name": "(optional)",
  "customer_phone": "(optional — if null, QR receipt generated)",
  "price": 350.00,  // auto-filled from catalog, editable by technician
  "notes": "Skrin pecah" (optional)
}

// Response 201
{
  "ticket": {
    "id": "uuid",
    "ticket_number": "D1-043",
    "intake_type": "quick_repair",
    "status": "in_progress",
    "customer_approval": "auto_approved",
    "qr_token": "abc123xy",
    "device": { ... },
    "receipt_url": "https://repairintake.my/t/abc123xy"
  }
}
```

### POST /api/tickets/voice

Create ticket from voice audio. AI transcribes and extracts structured fields.

```json
// Request (multipart/form-data)
{
  "audio": <audio_file.wav>,
  "outlet_id": "uuid (required)"
}

// Response 201
{
  "ticket": {
    "id": "uuid",
    "ticket_number": "D1-044",
    "intake_type": "voice_intake",
    "status": "in_progress",  // auto-approved if price is set
    "device": { "id": "uuid", "model": "iPhone 14 Pro" },
    "extracted": {
      "device_model": "iPhone 14 Pro",
      "issue": "Skrin pecah",
      "customer_name": "Ahmad",
      "price": 350.00,
      "confidence": 0.91
    },
    "transcript": "iPhone 14 Pro skrin pecah, Ahmad, tiga ratus lima puluh"
  }
}
```

### POST /api/tickets/:id/payment

Record payment for a completed ticket. Sends WhatsApp receipt or returns QR receipt URL.

```json
// Request
{
  "amount": 350.00,
  "method": "cash",
  "notes": "(optional)"
}

// Response 200
{
  "ticket": { ... },
  "payment": {
    "id": "uuid",
    "amount": 350.00,
    "method": "cash",
    "difference_from_estimate": 0
  },
  "receipt": {
    "sent_via": "whatsapp",  // or "qr_code" if no phone
    "qr_url": "https://repairintake.my/t/abc123xy"  // if no phone
  },
  "inventory_updated": true
}
```

### PATCH /api/tickets/:id/complete-with-payment

Mark ticket completed and record payment in one call. For Quick Repair flow.

```json
// Request
{
  "amount": 350.00,
  "method": "cash",
  "repair_notes": "Penukaran skrin selesai"
}

// Response 200
{
  "ticket": { ..., "status": "paid" },
  "payment": { ... },
  "receipt_sent": true
}
```

### GET /api/devices/:id/quick-issues

Top-10 common issues for Quick Repair quick-select dropdown.

```json
// Response 200
{
  "device": { "id": "uuid", "model": "iPhone 14 Pro" },
  "quick_issues": [
    {
      "issue_id": "uuid",
      "issue_name": "Penukaran Skrin OLED",
      "display_order": 1,
      "default_price": 350.00,
      "default_parts": [
        { "part_id": "uuid", "part_name": "Skrin OLED + Digitizer", "unit_retail": 339.00 }
      ],
      "labor_hours": 0.75
    },
    {
      "issue_id": "uuid",
      "issue_name": "Penukaran Bateri",
      "display_order": 2,
      "default_price": 150.00,
      "default_parts": [ ... ],
      "labor_hours": 0.5
    }
  ]
}
```

### GET /api/tickets/:token/public

Public ticket view (used by QR code link). Returns minimal info — no internal notes, no cost prices.

```json
// Response 200
{
  "ticket": {
    "ticket_number": "D1-043",
    "status": "in_progress",
    "device": { "model": "iPhone 14 Pro" },
    "issue": "Penukaran Skrin",
    "outlet": { "name": "Dungun 1", "address": "No. 23, Jalan Besar" },
    "estimated_completion": "2026-06-03T15:30:00+08:00",
    "can_subscribe": true  // if no phone linked yet
  }
}
```

### PATCH /api/tickets/:token/subscribe

Customer subscribes via public ticket page — links their phone number to the ticket.

```json
// Request
{
  "name": "Ahmad",
  "phone": "+6012-345-6789"
}

// Response 200
{
  "subscribed": true,
  "message": "WhatsApp kemaskini akan dihantar ke +6012-345-6789"
}
```

### POST /api/ai/parse-voice-intake

Parse voice recording to extract ticket fields (device, issue, customer, price).

```json
// Request
{
  "audio_url": "https://storage.supabase.co/.../voice.wav",
  "outlet_id": "uuid"
}

// Response 200
{
  "transcript": "iPhone 14 Pro skrin pecah, Ahmad, tiga ratus lima puluh",
  "transcript_ms": "iPhone 14 Pro skrin pecah, Ahmad, tiga ratus lima puluh",
  "extracted": {
    "device_model": "iPhone 14 Pro",
    "device_id": "uuid",
    "device_confidence": 0.94,
    "issue": "Skrin pecah",
    "issue_id": "uuid",
    "customer_name": "Ahmad",
    "price": 350.00,
    "price_confidence": 0.85,
    "overall_confidence": 0.91
  }
}
```

### PATCH /api/tickets/:id/approve

Customer approval endpoint (called from WhatsApp webhook).

```json
// Request
{
  "approved": true,
  "responded_via": "whatsapp",
  "customer_message": "YA"
}

// Response 200
{
  "ticket": { ... },
  "next_notification": {
    "type": "approval_confirmed",
    "recipient": "+60123456789"
  }
}
```

---

## AI Services

### POST /api/ai/identify-device

Vision-based device identification. Accepts an image URL (already uploaded to storage).

```json
// Request
{
  "image_url": "https://storage.supabase.co/.../photo.jpg"
}

// Response 200
{
  "brand": "Apple",
  "model": "iPhone 14 Pro",
  "variant": "Deep Purple",
  "confidence": 0.94,
  "model_number": "A2890",
  "matched_device_id": "uuid (from catalog, or null if no match)",
  "catalog_match": true,
  "suggestions": []  // If no match, list closest catalog entries
}
```

### POST /api/ai/suggest-diagnosis

AI-assisted diagnosis for technician assessment screen.

```json
// Request
{
  "device_id": "uuid",
  "customer_notes": "Phone won't turn on, customer thinks battery is the problem",
  "triage_notes": "Device completely dead, cannot power on"
}

// Response 200
{
  "suggestions": [
    {
      "issue_id": "uuid",
      "issue_name": "Battery Failure",
      "issue_name_ms": "Kerosakan Bateri",
      "confidence": 0.40,
      "typical_parts": [
        { "part_id": "uuid", "name": "Battery Pack", "estimated_cost": 45.00 }
      ],
      "labor_hours": 0.5,
      "timeline_days": 1
    },
    {
      "issue_id": "uuid",
      "issue_name": "Motherboard Short Circuit",
      "issue_name_ms": "Litar Pintas Motherboard",
      "confidence": 0.20,
      "typical_parts": [
        { "part_id": "uuid", "name": "Capacitor pack", "estimated_cost": 12.00 },
        { "part_id": "uuid", "name": "Thermal paste", "estimated_cost": 5.00 }
      ],
      "labor_hours": 2.5,
      "timeline_days": 3
    },
    {
      "issue_id": "uuid",
      "issue_name": "Charging Port Flex Cable",
      "issue_name_ms": "Kabel Flex Port Pengecas",
      "confidence": 0.25,
      "typical_parts": [],
      "labor_hours": 0.75,
      "timeline_days": 1
    }
  ],
  "recommendation": "Mula dengan ujian bateri. Jika voltan OK, periksa power rail motherboard untuk tanda kerosakan."
}
```

### POST /api/ai/transcribe (Month 5)

Voice-to-text for customer notes.

```json
// Request (multipart/form-data)
{
  "audio": <audio_file.wav>
}

// Response 200
{
  "transcript": "Pelanggan kata skrin pecah, sentuh tak berfungsi",
  "confidence": 0.95,
  "language": "ms"
}
```

---

## WhatsApp Webhooks

### POST /api/webhooks/whatsapp

Incoming WhatsApp messages from 360dialog/Twilio.
Handles customer replies to notification messages.

```json
// Incoming (from WhatsApp API)
{
  "messages": [
    {
      "id": "wamid.xxx",
      "from": "+60123456789",
      "text": { "body": "YA" },
      "timestamp": "1716702920"
    }
  ]
}

// Processing logic:
// 1. Look up customer by phone
// 2. Find latest pending ticket for that customer
// 3. Parse message intent:
//    - "YA" / "YES" / "OK" / "BAIK" → approve ticket
//    - "TIDAK" / "NO" / "BATAL" → decline ticket
//    - Question (contains "?") → forward to staff notification
//    - Other → forward to staff for manual review

// Response 200 (always return 200 to acknowledge receipt)
{ "status": "processed" }
```

---

## Admin Dashboard

### GET /api/admin/overview

Metrics for the owner dashboard. Supports `outlet_id` and `date_range` query params.

```json
// Response 200
{
  "today": {
    "tickets_created": 12,
    "tickets_completed": 8,
    "tickets_pending": 4,
    "revenue": 2450.00,
    "avg_turnaround_hours": 4.2
  },
  "this_week": {
    "tickets_created": 58,
    "tickets_completed": 45,
    "revenue": 11200.00
  },
  "by_outlet": [
    { "outlet_id": "uuid", "outlet_name": "Dungun 1", "tickets_today": 5, "revenue_today": 1100.00 },
    { "outlet_id": "uuid", "outlet_name": "Dungun 2", "tickets_today": 3, "revenue_today": 650.00 }
  ],
  "common_issues": [
    { "issue_name": "Penukaran skrin", "count": 38 },
    { "issue_name": "Penukaran bateri", "count": 24 },
    { "issue_name": "Port pengecas", "count": 15 }
  ],
  "technicians": [
    { "technician_id": "uuid", "name": "Amir Hassan", "completed_today": 3, "avg_assessment_minutes": 12 }
  ]
}
```

### GET /api/admin/reports

Generate reports with flexible date ranges.

Query params: `type` (daily|weekly|monthly), `outlet_id`, `date_from`, `date_to`

```json
// Response 200
{
  "type": "daily",
  "period": { "from": "2026-05-26", "to": "2026-05-26" },
  "summary": {
    "total_tickets": 12,
    "total_revenue": 2450.00,
    "avg_ticket_price": 204.17
  },
  "breakdown": [
    { "outlet": "Dungun 1", "tickets": 5, "revenue": 1100.00, "avg_price": 220.00 },
    { "outlet": "Dungun 2", "tickets": 3, "revenue": 650.00, "avg_price": 216.67 }
  ],
  "by_issue": [
    { "issue": "Penukaran skrin", "count": 6, "revenue": 1074.00 },
    { "issue": "Penukaran bateri", "count": 3, "revenue": 207.00 }
  ]
}
```

## CRUD Catalog Endpoints

All follow REST conventions. Admin role required.

| Method | Path | Description |
|---|---|---|
| GET | `/api/admin/devices` | List devices (with search, filter by brand/category) |
| POST | `/api/admin/devices` | Add new device |
| PATCH | `/api/admin/devices/:id` | Update device |
| DELETE | `/api/admin/devices/:id` | Soft-delete device |
| GET | `/api/admin/issues` | List issue types |
| POST | `/api/admin/issues` | Add issue type |
| PATCH | `/api/admin/issues/:id` | Update issue type |
| GET | `/api/admin/parts` | List parts (with search, filter by device compatibility) |
| POST | `/api/admin/parts` | Add part |
| PATCH | `/api/admin/parts/:id` | Update part |
| DELETE | `/api/admin/parts/:id` | Soft-delete part |
| GET | `/api/admin/users` | List users |
| POST | `/api/admin/users` | Create user |
| PATCH | `/api/admin/users/:id` | Update user (role, outlet, active status) |
| DELETE | `/api/admin/users/:id` | Deactivate user |

## API Conventions

- All timestamps in ISO 8601 with timezone offset (`+08:00` for Malaysia)
- Currency in MYR, formatted as `decimal(10,2)`
- Pagination: cursor-based for tickets (`?cursor=uuid&limit=20`), page-based for admin lists
- Errors: standard JSON error shape
  ```json
  { "error": { "code": "TICKET_NOT_FOUND", "message": "Tiket #D1-042 tidak dijumpai." } }
  ```
- HTTP status codes: 200 (OK), 201 (Created), 400 (Bad Request), 401 (Unauthorized), 403 (Forbidden), 404 (Not Found), 500 (Internal)
