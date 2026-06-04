# Feature: Ticket Detail

> **Screen-level UI spec only.**  
> For design tokens: `design-system/01-tokens-and-colors.md`  
> For reusable components: `design-system/02-components.md`

---

## Screen: Ticket Detail (`ticket/[id].tsx`)

### Purpose

View full ticket information. Access assessment (technicians). Trigger WhatsApp. Update status. This is a read-first screen with conditional write actions based on role and ticket status.

### Layout

- `ScrollView` with sections separated by 1px `--border` horizontal dividers.
- **Modern Header** (no back arrow): Left action area can show "Kembali" text or remain empty. Center: ticket number (`mono`, `heading-2`). Right: optional menu (vertical ellipsis `⋯`).
- Header bg: `--card`. **No bottom border** — clean modern look.

---

## Section 1: Device & Customer Card

### Layout

Two-column layout:

- **Left column** (~40%): Device image (or icon fallback), model name, color.
- **Right column** (~60%): Customer name, phone number, WhatsApp button.

### Device Side

- Device image: 80px square, rounded 8px, object-fit cover. Fallback: device icon (48px, `--muted-foreground`).
- Model name: `heading-3`, `--foreground`
- Color/variant: `body`, `--muted-foreground`
- `[Lihat gambar]` — `ghost` button, `sm`. Tap → opens intake photo in full-screen modal (zoomable, swipe to dismiss).

### Customer Side

- Customer name: `heading-3`, `--foreground`
- Phone number: `body`, `--primary` (tappable to call via `tel:` link)
- `[Hubungi di WhatsApp]` — `outline` button, `sm`, icon `MessageCircle` (Lucide). Tap → opens WhatsApp chat with pre-filled message (deep link).

---

## Section 2: Status Timeline

### Layout

Vertical timeline, left-aligned.

```
●────── Diterima
│         04 Jun 2026, 10:32 AM
│
○────── Sedang Dinilai
│
○────── Selesai Dinilai
│
○────── Dalam Proses
│
○────── Siap
│
○────── Dibayar / Diambil
```

- **Current status**: Filled dot (10px), `--primary` color. Label: `caption-medium`, `--primary`.
- **Past statuses**: Filled dot (8px), `--muted-foreground`. Label: `caption`, `--muted-foreground`.
- **Future statuses**: Empty dot (8px border 2px), `--border`. Label: `caption`, `--muted-foreground`.
- **Connector line**: 2px wide, `--border`, between dots.
- **Timestamp**: Below label, `overline`, `--muted-foreground`.

### Status Labels (BM)

| Status        | Timeline Label       |
| ------------- | -------------------- |
| `RECEIVED`    | Diterima             |
| `ASSESSING`   | Sedang Dinilai       |
| `ASSESSED`    | Selesai Dinilai      |
| `APPROVED`    | Diluluskan Pelanggan |
| `IN_PROGRESS` | Dalam Proses         |
| `COMPLETED`   | Siap                 |
| `PAID`        | Dibayar              |
| `PICKED_UP`   | Diambil              |
| `CANCELLED`   | Dibatalkan           |

---

## Section 3: Notes Section

### Customer Notes

- Label: "Nota Pelanggan" (`heading-3`) with `FileText` icon.
- Content card: bg `--card`, border 1px `--border`, radius 12px, padding 12px.
- Text: `body`, `--foreground`
- If voice memo exists: inline audio player below text (play/pause, waveform, duration).

### Technical Notes (if assessed)

- Label: "Nota Teknikal" (`heading-3`) with `Wrench` icon.
- Same card style as customer notes.
- Text: `body`, `--foreground`

---

## Section 4: Assessment Summary (if status >= ASSESSED)

Shown only if the ticket has been assessed.

### Parts List

- Label: "Bahagian" (`heading-3`)
- Each row:
  - Part name: `body`, `--foreground`
  - Quantity: `caption`, `--muted-foreground` (e.g., "x1")
  - Cost/price: `mono`, `--muted-foreground` (e.g., "Kos: RM89 | Harga: RM179")

### Labor

- Label: "Upah" (`heading-3`)
- Time: `caption`, `--foreground` (e.g., "0.75 jam")
- Rate: `caption`, `--muted-foreground` (e.g., "RM50.00 / jam")
- Labor total: `mono`, `--primary` (e.g., "RM37.50")

### Price Breakdown Card

- Same sticky-style card as Assessment screen:

```
Bahagian:       RM 94.00
Upah:           RM 37.50
Tambahan:       RM  0.00
─────────────────────────
ANGGARAN:       RM 131.50
```

- "ANGGARAN" in `heading-3`, `--primary`

### Timeline Estimate

- Label: "Anggaran Tempoh" (`heading-3`)
- Value: `body`, `--foreground` (e.g., "30–45 minit (tunggu)")

---

## Section 5: Actions

Actions vary by role and ticket status.

### For Technicians

| Status        | Available Actions                                            |
| ------------- | ------------------------------------------------------------ |
| `RECEIVED`    | `[Buka Penilaian]` — primary, pushes to Assessment           |
| `ASSESSING`   | `[Sambung Penilaian]` — primary, pushes to Assessment        |
| `ASSESSED`    | `[Kemaskini Penilaian]` — outline, pushes to Assessment      |
| `APPROVED`    | `[Mula Pembaikan]` — primary, updates status to IN_PROGRESS  |
| `IN_PROGRESS` | `[Selesai Pembaikan]` — primary, updates status to COMPLETED |
| `COMPLETED`   | `[Rekod Bayaran]` — primary, pushes to Payment screen        |

### For Front Desk

| Status      | Available Actions                                                                       |
| ----------- | --------------------------------------------------------------------------------------- |
| `RECEIVED`  | `[Hantar ke Teknikal]` — outline, assigns to technician                                 |
| `COMPLETED` | `[Rekod Bayaran]` — primary (if technician marks completed but hasn't recorded payment) |
| `PAID`      | `[Serah Peranti]` — primary, updates status to PICKED_UP                                |

### Shared Actions

- `[Hantar WhatsApp]` — `ghost` button. Sends manual status update to customer via WhatsApp. Only shown if customer phone exists.
- `[Kemaskini Status]` — `outline` button. Opens status dropdown (for authorized roles).

### Status Update Dropdown

- Triggered by "Kemaskini Status" or long-press menu.
- Options depend on current status and role.
- Each option: status label + description.
- Destructive option (Cancel): `--destructive` color with confirmation dialog.

---

## Header Menu (Vertical Ellipsis)

Tap `⋯` → opens action sheet:

| Option              | Role          | Condition                      |
| ------------------- | ------------- | ------------------------------ |
| Edit customer info  | front_desk    | status = RECEIVED              |
| Reassign technician | manager/owner | status = RECEIVED or ASSESSING |
| Cancel ticket       | manager/owner | status < IN_PROGRESS           |
| View audit log      | manager/owner | Always                         |
| Print ticket        | all           | Always (Month 3+)              |

- Action sheet style: iOS native bottom sheet.
- Destructive options in red.

---

## Design Token References

| Element                      | Token                |
| ---------------------------- | -------------------- |
| Section divider              | `--border`           |
| Device/customer card bg      | `--card`             |
| Timeline current dot         | `--primary`          |
| Timeline past dot            | `--muted-foreground` |
| Timeline future dot          | `--border`           |
| Timeline connector           | `--border`           |
| Notes card bg                | `--card`             |
| Price breakdown bg           | `--card`             |
| Price total text             | `--primary`          |
| Action button primary bg     | `--primary`          |
| Action button outline border | `--border`           |

---

## Components Used

- `DeviceCard` — compact device info (from New Ticket, reused)
- `CustomerCard` — name, phone, WhatsApp button
- `StatusTimeline` — vertical timeline component
- `NotesCard` — customer + technical notes display
- `PriceBreakdown` — parts + labor + total (shared with Assessment)
- `ActionSheet` — header menu, status update dropdown
- `AudioPlayer` — voice memo playback
- `Button` — primary, outline, ghost variants
- `StatusBadge` — status chip (reused from queue)

---

## Related Specs

- Jobs queue (entry point): `03-jobs-queue.md`
- Assessment (technician action): `06-assessment.md`
- Payment (completion): `09-payment-receipt.md`
- New Ticket (if editing): `04-new-ticket-full-ai.md`
- Success states: `14-success-states.md`
