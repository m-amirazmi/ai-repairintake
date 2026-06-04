# Feature: Quick Repair (Baiki Cepat)

> **Screen-level UI spec only.**  
> For design tokens: `design-system/01-tokens-and-colors.md`  
> For reusable components: `design-system/02-components.md`

---

## Screen: Quick Repair (`quick-repair.tsx`)

### Purpose

Technician-at-counter creates a ticket for a known fix with a known price. No AI vision, no diagnosis flow — just typeahead device + quick-select issue + price confirmation. Ticket is auto-approved and goes straight to `IN_PROGRESS`.

### Layout

- `ScrollView`, minimal sections, compact vertical spacing.
- **Modern Header** (no back arrow): Left action area shows "Batal" text button (cancels, returns to Home). Center: "Baiki Cepat" (`heading-2`).
- Header right: 🎤 microphone button (voice mode shortcut). 36px circle, `--primary` bg, white mic icon.

---

## Section 1: Device (Typeahead)

### Label

"Peranti" (`heading-3`)

### Search Input

- Placeholder: "Cari peranti..."
- Search icon (Lucide `Search`) on left.
- Typeahead results appear inline below the input (not in a separate modal).

### Typeahead Results List

- Appears after 2+ characters typed.
- Each row: device icon (24px), model name (`body`), variant (`caption`, `--muted-foreground`).
- Highlight exact match or first result (e.g., typing "iPh" highlights "iPhone 14 Pro").
- Tap row → selects device, list dismisses, input shows selected device name (non-editable, with X to clear).
- Auto-select on exact match: if user types full model name, automatically select it after 300ms debounce.

### Selected Device Display

- After selection: input transforms to a card showing device image + name + color.
- `[✕]` clear button on right to reset and search again.

---

## Section 2: Issue (Quick-Select)

### Label

"Masalah" (`heading-3`)

### Quick-Select List

- Scrollable vertical list of top-10 common issues for the selected device.
- Data source: `device_quick_issues` table.

### Issue Option Card

```
┌─────────────────────────────────────────┐
│ Penukaran Skrin OLED              ▼     │
│ RM350 | 0.75 jam                        │
│ 1× Skrin OLED, 1× Gam perekat          │
└─────────────────────────────────────────┘
```

- **Issue name**: `body-medium`, `--foreground`
- **Price + time**: `caption`, `--muted-foreground` (e.g., "RM350 | 0.75 jam")
- **Parts summary**: `overline`, `--muted-foreground` (e.g., "1× Skrin OLED, 1× Gam perekat")
- **Expand chevron**: `ChevronDown` / `ChevronUp` (Lucide). Tap to expand/collapse parts detail.
- **Selected state**: border `--primary` (2px), left accent line 4px `--primary`.
- **Unselected state**: border `--border` (1px).

### Fallback Option

- `[+ Taip masalah lain]` — `ghost` button, `sm`.
- Opens free-text input for unlisted issues.
- When used: price and labor must be entered manually (no auto-fill).

### Behavior

- Tap issue card → selects it, auto-fills Price Card (Section 3).
- Haptic: light impact.

---

## Section 3: Price Card (Sticky)

### Position

Sticky at bottom or floating 16px above bottom bar.

### Content

```
┌─────────────────────────────────────────┐
│ Bahagian (1):    RM 339.00             │
│ Upah (0.75j):    RM  11.00             │
│                   ────────             │
│ JUMLAH:          RM 350.00  [Edit]    │
└─────────────────────────────────────────┘
```

- **Bahagian**: Sum of parts retail for selected issue.
- **Upah**: Calculated from labor time × tenant hourly rate.
- **JUMLAH**: Total. `heading-3`, `--primary`.
- `[Edit]` — `ghost` button, `sm`, icon `Pencil` (Lucide).

### Price Edit Mode

- Tap `[Edit]` → opens inline number inputs for each line (parts total, labor total).
- Technician can override any value.
- If change > 20% from original: confirmation dialog "Perubahan harga >20%. Sahkan?"
- `[Simpan]` / `[Batal]` buttons below inputs.

---

## Section 4: Customer (Optional)

### Label

"Pelanggan (Pilihan)" (`heading-3`)

### Fields

| Field   | Type  | Placeholder      | Required |
| ------- | ----- | ---------------- | -------- |
| Nama    | text  | "Nama pelanggan" | No       |
| Telefon | phone | "+6012-345-6789" | No       |

- Phone typeahead: as user types, search existing customers by phone. Show matches in dropdown.
- If existing customer selected: auto-fills name field.
- Helper text: "Jika kosong, resit dengan kod QR akan dijana" (`caption`, `--muted-foreground`).

---

## Section 5: Submit

### Submit Button

- `[HANTAR & CETAK RESIT]` — `accent` button, `lg`, full width.
- Subtext: "Pembaikan akan dimulakan serta-merta" (`overline`, `--muted-foreground`).

### Submit Validation

- Device must be selected.
- Issue must be selected (or custom text + manual price entered).

### Submit States

| State     | UI                                  |
| --------- | ----------------------------------- |
| `idle`    | Button enabled if validation passes |
| `loading` | Spinner + "Sedang menghantar..."    |
| `success` | Push to Success State (see below)   |
| `error`   | Toast: "Gagal. Sila cuba lagi."     |

---

## Success States

### With Phone Number Provided

```
┌─────────────────────────────────────────┐
│ ✅ Tiket #D1-043 — Baiki Cepat          │
│                                         │
│ iPhone 14 Pro — Penukaran Skrin         │
│ Harga: RM350                            │
│                                         │
│ WhatsApp resit dihantar ke              │
│ +6012-345-6789                          │
│                                         │
│ Status: Dalam Proses                    │
│ Anggaran siap: 3:30 PM                  │
│                                         │
│ [Buka Tiket]  [Baiki Cepat Baru]        │
└─────────────────────────────────────────┘
```

### Without Phone Number (Anonymous)

```
┌─────────────────────────────────────────┐
│ ✅ Tiket #D1-043 — Baiki Cepat          │
│                                         │
│ iPhone 14 Pro — Penukaran Skrin         │
│ Harga: RM350                            │
│                                         │
│ [Large QR Code]                         │
│ "Imbas untuk kemaskini                  │
│  WhatsApp & status"                     │
│                                         │
│ Status: Dalam Proses                    │
│                                         │
│ [Buka Tiket]  [Baiki Cepat Baru]        │
└─────────────────────────────────────────┘
```

- QR code: 200×200px centered.
- Text below QR: "Imbas untuk kemaskini WhatsApp & status" (`body`, `--muted-foreground`).
- Actions:
  - `[Buka Tiket]` — push to Ticket Detail
  - `[Baiki Cepat Baru]` — reset form for next quick repair

---

## Design Token References

| Element                      | Token                 |
| ---------------------------- | --------------------- |
| Selected issue card border   | `--primary` (2px)     |
| Selected issue left accent   | `--primary` (4px)     |
| Unselected issue card border | `--border`            |
| Price card bg                | `--card`              |
| Price card border            | `--border`            |
| JUMLAH text                  | `--primary`           |
| Submit button bg             | `--accent`            |
| Submit button text           | `--accent-foreground` |
| QR code bg                   | white (always)        |
| Success checkmark            | `--secondary`         |

---

## Components Used

- `SearchInput` — device typeahead with inline results
- `DeviceTypeaheadResult` — inline dropdown row
- `IssueCard` — quick-select issue option (expandable)
- `PriceCard` — sticky total with edit mode
- `CustomerForm` — optional name + phone (mini version)
- `QRCodeDisplay` — large QR for anonymous success
- `Button` — accent, primary, outline, ghost
- `Toast` — error messages
- `ConfirmDialog` — price override confirmation

---

## Related Specs

- Navigation shell (FAB long-press menu): `02-navigation-shell.md`
- Voice Intake (header mic button): `08-voice-intake.md`
- Payment (after completion): `09-payment-receipt.md`
- QR Receipt (anonymous): `13-qr-receipt.md`
- Success states: `14-success-states.md`
- Ticket Detail ("Buka Tiket"): `05-ticket-detail.md`
