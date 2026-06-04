# Feature: Technician Assessment

> **Screen-level UI spec only.**  
> For design tokens: `design-system/01-tokens-and-colors.md`  
> For reusable components: `design-system/02-components.md`

---

## Screen: Assessment (`assessment/[id].tsx`)

### Purpose

Technician diagnoses the device, selects parts, calculates quote, sets timeline, adds technical notes. AI suggests diagnoses based on device model + customer notes.

### Layout

- `ScrollView` with `KeyboardAvoidingView behavior="padding"`.
- Sections separated by 1px `--border` horizontal dividers.
- Sticky bottom bar or floating card for price breakdown (always visible while scrolling).

### Header

- **Modern Header** (no back arrow): Left action area shows "Batal" text button (cancels assessment, returns to previous screen). Center: "Tiket #D1-042 — Penilaian" (`heading-2`).
- Top right: `[Simpan Draf]` — `outline` button, `sm`.
- Header bg: `--card`. **No bottom border** — clean modern look.

---

## Section 1: Device & Customer (Collapsible)

### Layout

Compact card, collapsible to save vertical space.

- Tap header → expand/collapse.
- Expanded: same info as Ticket Detail device/customer card (model, color, customer name, phone, WhatsApp button).
- Collapsed: single row: device model + customer name + status badge.

---

## Section 2: AI Diagnosis Suggestions

### Label

"Cadangan Diagnosis" (`heading-3`) with `Sparkles` icon (Lucide), `--secondary` color.

### Subtitle

"AI berdasarkan [Device Model] + nota pelanggan" (`caption`, `--muted-foreground`)

### Suggestion Cards (Radio-Button Style)

```
┌─────────────────────────────────────────┐
│ [✓] Penukaran Skrin OLED                │
│     95% keyakinan                       │
│     0.75 jam | RM89 (part)              │
└─────────────────────────────────────────┘
```

- **Card border**: 1px solid, 12px radius.
- **Selected**: border `--primary`, left accent line 4px `--primary`.
- **Unselected**: border `--border`.
- **Radio circle**: 20px, left side. Selected: filled `--primary` with checkmark. Unselected: empty circle 2px `--border`.
- **Diagnosis name**: `body-medium`, `--foreground`
- **Confidence**: `overline`, `--muted-foreground` (e.g., "95% keyakinan")
- **Time + cost**: `caption`, `--muted-foreground` (e.g., "0.75 jam | RM89 (part)")

### Behavior

- Tap card → selects that diagnosis.
- On selection: auto-fills Parts Section (Section 4) with mapped parts.
- Auto-fills Labor Section (Section 5) with suggested hours.
- Technician can still edit parts and labor after selection.
- Haptic: light impact on selection.

### No Suggestions State

- If AI returns no suggestions: "Tiada cadangan AI. Sila taip diagnosis sendiri." (`body`, `--muted-foreground`).

---

## Section 2.5: Edge-Case Trigger (Dead Device / Unknown Diagnosis)

### Purpose

Provide an escape hatch for cases where the device won't turn on or the problem is un-diagnosable by AI. This trigger card appears after the AI suggestions section and allows the technician to branch into the Dead Device / Unknown Diagnosis flow.

### Position

Immediately after the AI Suggestions section (Section 2), before the Manual Diagnosis section (Section 3).

### Appearance

- Full-width card, 12px radius.
- Background: `--surface-variant` (muted background).
- Border: 2px **dashed** `--border`.
- Padding: 16px.
- Icon: `AlertTriangle` (Lucide), 24px, `--muted-foreground`.

### Content

```
┌─────────────────────────────────────────┐
│ ⚠️ Peranti tidak boleh dihidupkan?       │
│                                         │
│ Jika peranti mati atau masalah tidak     │
│ dikenal pasti, teruskan ke penilaian      │
│ khas.                                    │
│                                         │
│ [Teruskan ke Penilaian Khas →]           │
└─────────────────────────────────────────┘
```

- **Title**: "Peranti tidak boleh dihidupkan?" (`body-medium`, `--foreground`)
- **Description**: "Jika peranti mati atau masalah tidak dikenal pasti, teruskan ke penilaian khas." (`caption`, `--muted-foreground`)
- **Action**: `[Teruskan ke Penilaian Khas →]` — `ghost` button, `sm`, right-aligned.

### Behavior

- Tap card or action button → navigates to the Dead Device / Unknown Diagnosis variant of the Assessment screen.
- Haptic: light impact on tap.
- This card is **only shown when**:
  1. AI suggestions return zero results, **OR**
  2. Customer notes contain keywords like "mati", "tidak boleh hidup", "dead", "no power", **OR**
  3. Technician taps a "flag" option in the AI suggestion cards indicating the device is unresponsive.

### Entry Point for Dead Device Flow

This trigger is the **canonical entry point** for the Dead Device / Unknown Diagnosis flow (`12-dead-device-unknown.md`). Previously, the flow was triggered automatically based on ticket data; now it is an explicit user-initiated branch from the Assessment screen.

---

---

## Section 3: Manual Diagnosis

### Label

"Atau taip diagnosis sendiri" (`heading-3`)

### Search Input

- Placeholder: "Cari atau taip diagnosis..."
- Typeahead dropdown appears after 2+ characters.
- Dropdown items: known issues from database (Malay names).
- Each item: issue name + category icon.
- Tap item → fills diagnosis, auto-fills parts if mapped.
- Free-text fallback: if no dropdown match, user can submit custom text.

---

## Section 4: Parts Selection

### Label

"Bahagian" (`heading-3`)

### Selected Parts List

```
┌─────────────────────────────────────────┐
│ Skrin OLED + Digitizer            x1    │
│          Kos: RM89    Harga:   RM179    │
└─────────────────────────────────────────┘
```

- Each row: part name (`body`), quantity stepper (`caption`), cost/price (`caption`, `--muted-foreground`).
- Stepper: `[−]` `[1]` `[+]` buttons. Min 1, max 99.
- Swipe left on row: "Buang" (remove) action, bg `--destructive`.

### Add Part Button

- `[+ Tambah Bahagian]` — `secondary` button, `sm`.
- Opens bottom sheet: searchable parts list filtered by device compatibility.
- Each part row: name, stock level (e.g., "Stok: 5"), cost, retail price.
- Tap → adds to selected parts list, dismisses sheet.

### Empty State (No Parts)

- "Tiada bahagian dipilih" (`caption`, `--muted-foreground`).

---

## Section 5: Labor

### Label

"Upah" (`heading-3`)

### Fields

| Field  | Type                    | Value                 | Editable                  |
| ------ | ----------------------- | --------------------- | ------------------------- |
| Masa   | number input, step 0.25 | e.g., 0.75            | Yes                       |
| Kadar  | display only            | e.g., "RM50.00 / jam" | No (from tenant settings) |
| Jumlah | calculated display      | e.g., "RM37.50"       | Auto-calculated           |

- "Masa" input: numeric keyboard, allows decimals (0.25 increments).
- "Jumlah": `mono`, `--primary`, `heading-3`.

---

## Section 6: Price Breakdown (Sticky Card)

### Position

- Sticky at bottom of screen while scrolling, or floating card 16px from bottom (above keyboard).
- On small screens: collapses to "ANGGARAN: RM131.50" with expand chevron.

### Content

```
Bahagian:       RM  94.00
Upah:           RM  37.50
Tambahan: [___] RM   0.00
─────────────────────────
ANGGARAN:       RM 131.50
```

- **Bahagian**: Sum of selected parts retail prices.
- **Upah**: Calculated from labor section.
- **Tambahan**: Optional number input (e.g., misc fees, diagnostic fee). Default 0.
- **ANGGARAN**: Total = parts + labor + additional. `heading-3`, `--primary`.
- Background: `--card`, border 1px `--border`, radius `radius-lg`.
- Shadow: subtle elevation.

---

## Section 7: Timeline (ETA)

### Label

"Tempoh" (`heading-3`)

### Radio Group

| Option | Label (BM)           |
| ------ | -------------------- |
| `●`    | 30–45 minit (tunggu) |
| `○`    | 1–3 hari             |
| `○`    | 3–7 hari             |

- Selected: filled circle `--primary`, label `body-medium`.
- Unselected: empty circle 2px `--border`, label `body`.
- Tap to select. Haptic: light impact.

---

## Section 8: Technical Notes

### Label

"Nota Teknikal" (`heading-3`)

### Input

- Multiline `TextArea`.
- Placeholder: "Nota teknikal (pilihan)"
- Auto-growing, max 200px.
- Example content: "Skrin pecah di penjuru kanan bawah. Tiada kerosakan LCD. Sentuh berfungsi 80%."

---

## Actions (Bottom Bar)

Fixed bottom bar or two floating buttons above sticky price card:

| Button              | Style           | Action                                                                                       |
| ------------------- | --------------- | -------------------------------------------------------------------------------------------- |
| `[Simpan Draf]`     | `outline`, `lg` | Saves assessment as draft, stays on screen                                                   |
| `[Hantar & Notify]` | `primary`, `lg` | Submits assessment, triggers WhatsApp notification to customer, updates status to `ASSESSED` |

- If `[Hantar & Notify]` tapped without parts selected: confirmation dialog "Tiada bahagian dipilih. Teruskan?"
- If price difference from initial estimate > 20%: warning dialog "Perubahan harga besar. Pastikan pelanggan dimaklumkan."

---

## Auto-Save

- Saves draft on every field change.
- Debounced: 500ms after last change.
- Stored in AsyncStorage with key: `assessment-draft-{ticketId}`.
- Indicator: small toast at bottom — "Draf disimpan" — fades after 2 seconds.
- On screen mount: check AsyncStorage for existing draft, prompt "Ada draf tersimpan. Muat semula?" if found.

---

## Design Token References

| Element                           | Token          |
| --------------------------------- | -------------- |
| Suggestion card selected border   | `--primary`    |
| Suggestion card unselected border | `--border`     |
| Suggestion left accent            | `--primary`    |
| Price breakdown bg                | `--card`       |
| Price breakdown border            | `--border`     |
| Price total text                  | `--primary`    |
| Labor total text                  | `--primary`    |
| ETA selected circle               | `--primary`    |
| ETA unselected circle             | `--border`     |
| Bottom bar bg                     | `--background` |
| Draft saved toast bg              | `--secondary`  |

---

## Components Used

- `CollapsibleCard` — device/customer info (expand/collapse)
- `SuggestionCard` — AI diagnosis option (radio style)
- `SuggestionList` — list of AI suggestions
- `SearchInput` — manual diagnosis typeahead
- `PartsList` — selected parts with steppers
- `PartRow` — individual part row with swipe remove
- `BottomSheet` — add part search
- `Stepper` — quantity +/-
- `PriceBreakdown` — sticky total card (shared with Ticket Detail)
- `RadioGroup` — timeline selector
- `TextArea` — technical notes
- `Button` — primary, outline, secondary, ghost
- `Toast` — draft saved indicator
- `ConfirmDialog` — unsaved changes, price change warning

---

## Related Specs

- Ticket detail (source screen): `05-ticket-detail.md`
- New Ticket (creates the ticket): `04-new-ticket-full-ai.md`
- Payment (after completion): `09-payment-receipt.md`
- Success states (after submit): `14-success-states.md`
- Dead device (edge case): `12-dead-device-unknown.md`
