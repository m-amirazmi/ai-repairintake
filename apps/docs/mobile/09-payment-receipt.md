# Feature: Payment & Receipt

> **Screen-level UI spec only.**  
> For design tokens: `design-system/01-tokens-and-colors.md`  
> For reusable components: `design-system/02-components.md`

---

## Screen: Payment (`payment/[id].tsx`)

### Purpose

Record payment when a ticket is marked completed. Sends WhatsApp receipt or displays QR code receipt for anonymous customers. Decrements parts inventory on confirm.

### Trigger

- Technician taps "Selesai" on a ticket → payment screen slides up as modal.
- Or from Ticket Detail: `[Rekod Bayaran]` action button.

### Layout

- Modal presentation: slide up from bottom, 90% screen height.
- `ScrollView` with `KeyboardAvoidingView`.
- Close button (X) top right or swipe down to dismiss (if not in required flow).

---

## Header

- **Modern Header** (no back arrow): Left action area shows "Batal" text button (dismisses modal). Center: "Pembayaran — Tiket #D1-043" (`heading-2`).
- **Ticket summary**: device model, issue, outlet name (`caption`, `--muted-foreground`).

---

## Section 1: Price Recap Card

### Layout

Full-width card, bg `--card`, border 1px `--border`, radius 12px, padding 16px.

### Content

```
iPhone 14 Pro
Penukaran Skrin OLED

Bahagian (1):    RM 339.00
Upah (0.75j):    RM  11.00
─────────────────────────
JUMLAH:          RM 350.00
```

- Device + issue: `heading-3`, `--foreground`
- Parts breakdown: `body`, `--foreground`
- Labor breakdown: `body`, `--foreground`
- JUMLAH: `heading-2`, `--primary`

---

## Section 2: Payment Form

### Field: Amaun (Amount)

- Number input, prefix "RM".
- Auto-filled with estimate total from assessment.
- Editable.
- If edited to a lower amount:
  - Shows difference: "Beza: -RM50.00" in `--warning`.
  - Shows reason dropdown: `Diskaun` | `Harga Berbeza` | `Bayaran Sebahagian`.
- If difference > 20%: confirmation dialog required before submit.

### Field: Kaedah (Method)

Radio group, vertical stack:

| Option | Icon | Label (BM)    |
| ------ | ---- | ------------- |
| `○`    | 💵   | Tunai         |
| `○`    | 📱   | QR Pay        |
| `○`    | 🏦   | Bank Transfer |

- Selected: filled circle `--primary`, label `body-medium`, icon at full opacity.
- Unselected: empty circle 2px `--border`, label `body`, icon at 50% opacity.
- Visual indicators: emoji or custom icon next to label.
- Haptic: light impact on selection.

### Field: Nota (Note)

- Multiline `TextArea`.
- Placeholder: "Nota pembayaran (pilihan)".
- Max 200 characters.

---

## Section 3: Inventory Decrement Preview

- Shown below payment form.
- Label: "Stok akan dikemaskini:" (`caption`, `--muted-foreground`)
- List of parts to be decremented:
  - "Skrin OLED: 1 → 0" (`caption`, `--foreground`)
  - "Gam perekat: 1 → 0" (`caption`, `--foreground`)
- If any part would go below zero: highlight in `--destructive` with warning text "Stok tidak mencukupi!"

---

## Submit

### Button

- `[SAHKAN BAYARAN & SELESAI]` — `primary` button, `lg`, full width.
- Subtext: "Stok akan dikemaskini secara automatik" (`overline`, `--muted-foreground`).

### Submit States

| State     | UI                                          |
| --------- | ------------------------------------------- |
| `idle`    | Enabled when amount > 0 and method selected |
| `loading` | Spinner + "Sedang memproses..."             |
| `success` | Push to Success State (see below)           |
| `error`   | Toast: "Pembayaran gagal. Sila cuba lagi."  |

---

## Success States

### With Phone Number

```
┌─────────────────────────────────────────┐
│ ✅ Pembayaran Diterima                  │
│                                         │
│ RM350 — QR Pay                          │
│ Tiket #D1-043 — Selesai                 │
│                                         │
│ WhatsApp resit dihantar kepada          │
│ +6012-345-6789                          │
│                                         │
│ [WhatsApp Resit Preview Card]           │
│                                         │
│ [Kembali ke Senarai]                    │
└─────────────────────────────────────────┘
```

- **WhatsApp Receipt Preview Card**:

```
📋 Resit WhatsApp:

🔧 Repair Intake
Tiket #D1-043
iPhone 14 Pro — Penukaran Skrin
Jumlah: RM350 (QR Pay)
Cawangan: Dungun 1
Terima kasih!
```

- Bg `--card`, border `--border`, radius 12px.
- Text: `body`, `--foreground`.

### Without Phone Number (Anonymous)

```
┌─────────────────────────────────────────┐
│ ✅ Pembayaran Diterima                  │
│                                         │
│ [Large QR Code — 200px]                 │
│                                         │
│ Tunjukkan QR ini kepada                 │
│ pelanggan                               │
│                                         │
│ [Kembali ke Senarai]                    │
└─────────────────────────────────────────┘
```

- QR code: 200×200px, centered, white bg.
- Text: "Tunjukkan QR ini kepada pelanggan" (`body`, `--muted-foreground`).
- Action: `[Kembali ke Senarai]` — `primary` button.

---

## Receipt WhatsApp Message Format

```
🔧 Repair Intake — Resit
Tiket #D1-043
iPhone 14 Pro — Penukaran Skrin OLED
Jumlah: RM350.00 (QR Pay)
Cawangan: Dungun 1
Tarikh: 03 Jun 2026, 3:30 PM

Terima kasih! Sila simpan resit ini.
```

---

## Design Token References

| Element                   | Token           |
| ------------------------- | --------------- |
| Price recap card bg       | `--card`        |
| Price recap border        | `--border`      |
| JUMLAH text               | `--primary`     |
| Amount difference warning | `--warning`     |
| Payment method selected   | `--primary`     |
| Payment method unselected | `--border`      |
| Inventory warning         | `--destructive` |
| Submit button bg          | `--primary`     |
| Success checkmark         | `--secondary`   |
| QR code bg                | white (always)  |
| Receipt preview card bg   | `--card`        |

---

## Components Used

- `PriceRecapCard` — device + issue + breakdown
- `NumberInput` — amount with RM prefix
- `RadioGroup` — payment method selector
- `TextArea` — optional note
- `InventoryPreview` — stock decrement list
- `QRCodeDisplay` — large QR for anonymous
- `ReceiptPreviewCard` — WhatsApp message preview
- `Button` — primary, outline
- `ConfirmDialog` — price difference >20%
- `Toast` — error messages

---

## Related Specs

- Ticket Detail (trigger source): `05-ticket-detail.md`
- Assessment (precedes payment): `06-assessment.md`
- QR Receipt (anonymous): `13-qr-receipt.md`
- Success states: `14-success-states.md`
