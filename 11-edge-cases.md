# Edge Cases & Error Handling

## AI Vision Failures

### Case 1: Low Confidence (< 0.8)

**Trigger:** GPT-4o Vision returns device identification with confidence 0.6.

**Flow:**
1. App shows: "Tidak pasti. Keyakinan AI rendah."
2. Displays the AI's best guess as a suggestion (not auto-selected)
3. Prompts staff to search catalog manually
4. Search shows filtered list based on AI's guess (e.g., "iPhone 12 or 13?")
5. Staff taps the correct model

**System behavior:**
- Ticket created with manually selected device
- AI confidence logged in ticket_history for improvement tracking
- No impact on ticket flow — just an extra tap

### Case 2: Device Not in Catalog

**Trigger:** AI identifies "Xiaomi Redmi Note 13" but catalog only has up to Note 12.

**Flow:**
1. App shows: "Peranti tidak dalam katalog. Tambah sekarang?"
2. Staff choices:
   - **Quick add**: Enter brand + model in a modal, save to catalog, continue
   - **Use nearest match**: Select "Redmi Note 12" with a warning banner
   - **Skip**: Create ticket with "Unknown" device, resolve later
3. Missing device logged for catalog expansion

**System behavior:**
- Quick add creates a bare-minimum device record (brand + model)
- Admin notified to complete the device profile later (add common issues, image, etc.)
- Ticket proceeds normally

### Case 3: Photo Too Blurry / Wrong Angle

**Trigger:** Staff takes photo of the screen (front) instead of back, or photo is blurry.

**Flow:**
1. AI returns confidence < 0.5 or error
2. App shows: "Gambar kurang jelas. Cuba ambil gambar bahagian belakang peranti."
3. Shows a visual guide overlay: outline of where to point camera
4. Staff retakes photo
5. After 2 failed attempts, prompt to skip and select manually

---

## WhatsApp Failures

### Case 1: Message Not Delivered

**Trigger:** WhatsApp API returns error (invalid number, blocked, not registered).

**Flow:**
1. Notification status marked as `failed` in DB
2. App shows alert to staff who triggered the notification:
   - "Gagal hantar WhatsApp ke +6012-345-6789"
   - Reason displayed (from API error)
   - Options: [Cuba Semula] [Hubungi Manual] [Kemaskini Nombor]
3. Staff manually calls customer
4. Staff can update customer phone if typo suspected
5. Staff can trigger retry after number update

### Case 2: Customer Replies with Unexpected Text

**Trigger:** Customer replies "maybe" or "how long exactly?" instead of YES/NO.

**Intent detection logic:**
```
YES intents: "YA", "YES", "OK", "BAIK", "JALAN", "TERUSKAN", "SETUJU"
NO intents: "TIDAK", "NO", "BATAL", "JANGAN", "TAK NAK"
QUESTION intents: message contains "?", or keywords "bila", "mana", "berapa", "how", "when", "where", "price"
OTHER: anything else
```

**Flow:**
1. If `QUESTION`: Auto-reply with relevant info (price, timeline, location) based on ticket data. Keep approval pending.
2. If `OTHER` (unrecognized): Forward to staff as a notification. Staff manually replies via app. Approval stays pending until explicit YES/NO.
3. If `YES`/`NO`: Standard approval/decline flow.

### Case 3: Meta WhatsApp Template Rejected

**Trigger:** Meta requires template approval before sending. Template may be rejected.

**Workaround:**
- Use "session messages" for first 24-hour window (can send free-form text)
- Beyond 24 hours, must use approved templates
- Submit all templates early; iterate on wording if rejected
- Fallback: Send SMS via Twilio if WhatsApp template unavailable
- SMS cost ~RM0.20/msg in Malaysia, minimal volume

---

## Pricing & Assessment Edge Cases

### Case 1: Assessment Drastically Different from Diagnostic Fee

**Trigger:** Triage/diagnostic fee was RM29. Actual diagnosis is RM289 motherboard repair.

**Flow:**
1. System detects: `estimate_total > diagnostic_fee × 3`
2. Assessment screen shows warning: "PERUBAHAN HARGA BESAR"
3. Technician must explicitly acknowledge: "Ya, saya pasti dengan diagnosis ini"
4. WhatsApp notification includes explanation: "Selepas pemeriksaan, kami dapati kerosakan motherboard (bukan bateri seperti yg disangka). Anggaran baru: RM289."
5. If customer already approved a lower quote, re-approval is required (status reset to ASSESSED, approval pending)

### Case 2: Parts Not in Stock

**Trigger:** Technician selects part with `stock_quantity = 0`.

**Flow:**
1. Assessment screen shows warning: "Bahagian tiada stok: Skrin OLED iPhone 14 Pro (0 unit)"
2. Options presented to technician:
   - **[1] Pesan dahulu**: Extends timeline by supplier lead time. Ticket status: AWAITING_PARTS
   - **[2] Guna alternatif**: Search compatible parts. If found, use alternative with disclaimer note
   - **[3] Maklumkan pelanggan**: Send WhatsApp explaining delay. Let customer decide
3. Auto-calculation: Lead time added to timeline estimate

### Case 3: Customer Declines Quote

**Trigger:** Customer replies "NO" or "TIDAK" to assessment WhatsApp.

**Flow:**
1. Ticket status → CANCELLED
2. WhatsApp auto-reply: "Baik, peranti anda boleh diambil di Dungun 1. Kos pemeriksaan: RM29."
3. Diagnostic fee is charged (if applicable)
4. Staff notified: "Tiket #D1-042 ditolak oleh pelanggan. Sila sedia peranti untuk diambil."
5. If customer returns later and reconsiders, staff reopens ticket: status → RECEIVED (keep assessment data intact)

### Case 4: Customer Never Responds

**Trigger:** 24 hours pass with no WhatsApp response to assessment.

**Flow:**
1. Auto-reminder sent at 24h mark
2. Second reminder at 48h
3. Third reminder at 72h +
4. If no response after 5 days, ticket status → CANCELLED
5. WhatsApp: "Peranti anda masih belum diambil. Tiket #D1-042 telah dibatalkan automatik."
6. Staff can override and manually re-open

---

## Quick Repair & Voice Intake Edge Cases

### Case 1: Quick Repair — Wrong Device Selected
**Trigger:** Technician typeaheads "iPh" and accidentally taps iPhone 14 instead of iPhone 14 Pro.

**Flow:**
1. Technician can immediately undo (soft delete within 30 seconds)
2. Or edit device on the ticket detail screen
3. If parts were auto-assigned for wrong device, they're cleared on device change
4. Ticket history logs the correction

### Case 2: Quick Repair — Inventory Below Minimum
**Trigger:** Technician selects "Penukaran Skrin" for iPhone 14 Pro, but Skrin OLED stock = 1 at Dungun 1, and min_stock_alert = 3.

**Flow:**
1. Quick Repair screen shows warning: "Stok rendah: Skrin OLED (1 unit)"
2. Technician can still proceed (they physically have the part)
3. After completion, stock decrements to 0
4. Low stock alert sent to manager notification
5. Alternative: if stock is 0, show error "Bahagian tiada stok" and block submission

### Case 3: Quick Repair — Price Override
**Trigger:** Technician changes auto-filled RM350 to RM320 (discount or different part used).

**Flow:**
1. Price field is editable — technician taps and types new amount
2. Difference logged in ticket_history: `{"override": "price", "from": 350.00, "to": 320.00}`
3. If override exceeds ±20% of catalog price, confirmation dialog: "Harga berbeza 20% dari katalog. Pastikan?"
4. Payment screen shows "Anggaran: RM350 | Dibayar: RM320" (difference visible)

### Case 4: Voice Intake — AI Misrecognition
**Trigger:** Voice says "iPhone empat belas pro" but AI extracts "iPhone 14 Pro Max".

**Flow:**
1. Confidence display shows: "Keyakinan: 72% — rendah"
2. Each extracted field has a tappable edit icon:
   - 📱 Peranti → tap to change device
   - 🔧 Masalah → tap to change issue
   - 👤 Pelanggan → tap to edit name
   - 💰 Harga → tap to edit price
3. Staff corrects the wrong field(s) and taps confirm
4. Correction logged: `{"corrected_field": "device", "ai_value": "iPhone 14 Pro Max", "user_value": "iPhone 14 Pro"}`
5. This feedback feeds into the AI Learning Loop (Month 4)

### Case 5: Voice Intake — Incomplete Information
**Trigger:** Voice says "iPhone skrin pecah" — missing customer name and price.

**Flow:**
1. AI extracts what it can: device + issue
2. Missing fields highlighted in yellow: "Maklumat tidak lengkap"
3. Staff fills in missing fields manually (customer name, phone, price)
4. Ticket created as `voice_intake` with partial AI extraction

### Case 6: Quick Repair — Customer Already in System
**Trigger:** Technician enters phone number that matches an existing customer.

**Flow:**
1. Typeahead on phone field shows existing customer: "Ahmad bin Abdullah (5 lawatan)"
2. Technician taps to auto-fill name
3. Customer's `total_visits` incremented on ticket completion
4. WhatsApp receipt sent to existing number automatically

### Case 7: Payment — Amount Doesn't Match Estimate
**Trigger:** Estimate was RM350, customer pays RM300.

**Flow:**
1. Payment screen shows original estimate and editable amount field
2. Technician enters 300.00
3. System shows: "Beza: -RM50.00"
4. Requires technician to select reason from dropdown:
   - Diskaun (Discount)
   - Harga Berbeza (Different Price)
   - Bayaran Sebahagian (Partial Payment)
5. Incomplete payment keeps ticket status as `completed` (not `paid`) with balance RM50
6. Customer can pay balance later — new payment record created, ticket → `paid` when balance = 0

### Case 8: QR Receipt — Customer Subscribes Hours Later
**Trigger:** Anonymous Quick Repair ticket #D1-043 created at 10am. Customer scans QR at 4pm and subscribes.

**Flow:**
1. Ticket already completed and paid by then
2. Customer enters phone → `customers` record created (or matched)
3. Ticket's `customer_id` and `customer_phone` updated
4. Retroactive WhatsApp sent: "Resit untuk Tiket #D1-043 (dari pagi tadi): iPhone 14 Pro — Penukaran Skrin RM350. Terima kasih."
5. All future tickets for this phone number linked to same customer

---

## Technical Edge Cases

### Case 1: No Internet During Intake

**Trigger:** Staff creates ticket but mobile has no connection.

**Flow:**
1. App detects offline (Network Info API)
2. Stores ticket in AsyncStorage with temporary ID (`TMP-001`)
3. Shows: "📡 Tiada Internet. Tiket disimpan secara tempatan."
4. Badge shows pending sync count
5. When internet restored:
   - Background sync begins
   - For each TMP ticket: POST to API, get real ticket ID, update local storage
   - AI vision skipped for offline tickets — device must be manually selected
   - Customer receives WhatsApp once synced
6. Conflict handling: If same customer + phone has another ticket created while offline, show merge dialog

### Case 2: Two Staff Edit Same Ticket

**Trigger:** Front desk staff changes status to "Dibatalkan" while technician is in the middle of assessment.

**Flow:**
1. System detects version conflict via `updated_at` comparison
2. On technician's save attempt: "⚠️ Tiket telah dikemaskini oleh Sarah (Kaunter)."
3. Shows what changed: "Status: Selesai Dinilai → Dibatalkan"
4. Options:
   - **Muat Semula**: Discard current changes, reload latest version
   - **Tulis Ganti**: Force-save current changes (with audit log)
5. Both versions logged in `ticket_history`

### Case 3: OpenAI API is Down

**Trigger:** OpenAI API returns 5xx errors.

**Fallback chain:**
1. First attempt: OpenAI GPT-4o → if fails...
2. Second attempt: Retry 1x after 500ms delay → if fails...
3. Final fallback: Return to client: "AI tidak tersedia buat masa ini. Sila pilih secara manual."
4. Client shows:
   - Vision: Manual device search (no AI)
   - Diagnosis: Standard list of all issues for that device (not ranked)
5. System logs: `ai_service_status = 'degraded'`
6. Monitor: Alert platform owner if OpenAI down > 5 minutes

### Case 4: Large Image Upload

**Trigger:** Photo taken at full iPhone resolution (12MP, ~3-5MB).

**Prevention:**
1. Client-side compression before upload:
   - Resize: max 800px longest edge
   - Format: JPEG, quality 0.7
   - Target size: < 200KB per photo
2. Server-side validation:
   - Reject if > 1MB (before processing)
   - Maximum 10 photos per ticket
3. AI API: Send compressed version (800px is more than enough for device ID)
4. Storage: Keep both original (optional) and compressed versions

### Case 5: WhatsApp Webhook Timeout

**Trigger:** WhatsApp API sends webhook, but your server takes > 3 seconds to process.

**Solution:**
1. Acknowledge webhook immediately (return 200 within 100ms)
2. Process in background (queue the message intent parsing)
3. WhatsApp retries if no 200 received within timeout
4. Idempotency: `external_message_id` prevents duplicate processing
