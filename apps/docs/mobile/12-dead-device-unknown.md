# Feature: Dead Device / Unknown Diagnosis

> **Screen-level UI spec only.**  
> For design tokens: `design-system/01-tokens-and-colors.md`  
> For reusable components: `design-system/02-components.md`

---

## Screen: Dead Device / Unknown (`assessment/[id].tsx` — variant)

### Purpose

Handle cases where the device won't turn on or the problem is unknown. The technician cannot make a diagnosis without physical inspection. This is an edge-case flow within the Assessment screen.

### Trigger

- **Primary**: Technician taps the "Edge-Case Trigger" card on the Assessment screen (`06-assessment.md` §Section 2.5).
- **Secondary**: Technician opens assessment for a ticket where customer notes say "won't turn on", "dead", "no power", etc., and the edge-case trigger card is shown.
- **Tertiary**: Device shows no signs of life during physical inspection, and technician manually initiates this flow via the edge-case trigger.

### Layout

- Same Assessment screen structure (`06-assessment.md`), but reached via the edge-case trigger. Initial state shows a Warning Card instead of AI suggestions.
- ScrollView with sections.

---

## Section 1: Warning Card

### Appearance

- Full-width card.
- Background: `--destructive` at 10% opacity.
- Border: 1px `--destructive` at 20% opacity.
- Border radius: 12px.
- Padding: 16px.
- Icon: `AlertTriangle` (Lucide), 32px, `--destructive`.

### Content

```
⚠️ PERANTI TIDAK BOLEH DIHIDUPKAN

Diagnosis tidak boleh dibuat tanpa
pemeriksaan fizikal.
```

- Title: "Peranti Tidak Boleh Dihidupkan" (`heading-3`, `--destructive`)
- Description: "Diagnosis tidak boleh dibuat tanpa pemeriksaan fizikal." (`body`, `--destructive`)

### Additional Context (if available)

- Customer notes: "Bateri rosak" (`body`, `--muted-foreground`)
- Front desk notes: "Peranti mati, tak boleh hidupkan" (`body`, `--muted-foreground`)

---

## Section 2: Options

### Layout

Two large tappable cards, stacked vertically, 12px gap.

### Option 1: Diagnostic Fee

```
┌─────────────────────────────────────────┐
│ [1] YURAN PEMERIKSAAN: RM29             │
│                                         │
│ Periksa peranti. Anggaran akan          │
│ dihantar kemudian.                      │
└─────────────────────────────────────────┘
```

- **Number badge**: "[1]" in `--primary` bg circle, white text, 24px.
- **Title**: "YURAN PEMERIKSAAN: RM29" (`heading-3`, `--foreground`)
- **Description**: `body`, `--muted-foreground`
- **Card style**: bg `--card`, border 1px `--border`, radius 12px. Tap → border becomes `--primary` (2px).
- **Haptic**: medium impact on tap.

### Option 2: Wait for Approval

```
┌─────────────────────────────────────────┐
│ [2] TUNGGU KELULUSAN                    │
│                                         │
│ Maklumkan pelanggan dulu.               │
└─────────────────────────────────────────┘
```

- Same card style as Option 1.
- **Title**: "TUNGGU KELULUSAN" (`heading-3`, `--foreground`)
- **Description**: "Maklumkan pelanggan dulu." (`body`, `--muted-foreground`)

### Behavior

- Tap an option → selects it (highlighted border).
- Both options can be selected? No — single select (radio behavior).
- Proceed button appears below after selection.

### Proceed Button

- `[SIMPAN SEBAGAI "SEDANG DINILAI"]` — `primary` button, `lg`, full width.
- On tap: updates ticket status to `ASSESSING` with a flag `waiting_for_inspection: true`.
- Shows toast: "Status dikemaskini. Pemeriksaan fizikal diperlukan."

---

## Section 3: After Physical Inspection

This section appears after the technician has physically inspected the device and tapped "Update Assessment" or returns to the ticket.

### Updated AI Suggestions

- Label: "Cadangan AI (dikemaskini)" (`heading-3`) with `Sparkles` icon.
- Subtitle: "Berdasarkan [Device Model] + 'mati'" (`caption`, `--muted-foreground`)
- Same suggestion card style as normal Assessment (`06-assessment.md`).
- Example options:
  - `[ ] Kerosakan Bateri (30%)`
  - `[ ] Port Pengecas (15%)`
  - `[✓] Short Motherboard (45%)`
  - `[ ] Butang Power (10%)`

### Actual Diagnosis Input

- Label: "Diagnosis sebenar" (`heading-3`)
- Multiline `TextArea`.
- Example: "Kerosakan Motherboard. Capacitor terbakar (C321) berhampiran CPU power rail."

### Large Price Change Warning

```
┌─────────────────────────────────────────┐
│ ⚠️ PERUBAHAN HARGA BESAR               │
│                                         │
│ Yuran semakan:       RM 29.00           │
│ Anggaran baru:       RM 289.00          │
│                                         │
│ Pelanggan perlu luluskan semula.        │
└─────────────────────────────────────────┘
```

- **Card bg**: `--warning` at 10% opacity.
- **Border**: `--warning` at 20%.
- **Icon**: `AlertTriangle`, `--warning`.
- **Title**: "PERUBAHAN HARGA BESAR" (`heading-3`, `--warning`)
- **Breakdown**: `body`, `--foreground`
- **Warning text**: "Pelanggan perlu luluskan semula." (`body`, `--destructive`)

### Submit

- `[SIMPAN & HANTAR NOTIFIKASI]` — `primary` button, `lg`.
- Sends WhatsApp notification to customer with new estimate.
- Customer must reply YES again to approve.

---

## Design Token References

| Element                         | Token                  |
| ------------------------------- | ---------------------- |
| Warning card bg                 | `--destructive` at 10% |
| Warning card border             | `--destructive` at 20% |
| Warning icon                    | `--destructive`        |
| Warning title                   | `--destructive`        |
| Option card bg                  | `--card`               |
| Option card border (unselected) | `--border`             |
| Option card border (selected)   | `--primary` (2px)      |
| Number badge bg                 | `--primary`            |
| Number badge text               | white                  |
| Updated suggestion subtitle     | `--muted-foreground`   |
| Price change warning bg         | `--warning` at 10%     |
| Price change warning border     | `--warning` at 20%     |
| Price change warning text       | `--destructive`        |
| Proceed button bg               | `--primary`            |

---

## Components Used

- `WarningCard` — destructive alert card
- `SelectableCard` — option 1 / option 2 (radio behavior)
- `SuggestionCard` — AI diagnosis suggestions (reused from Assessment)
- `TextArea` — actual diagnosis input
- `PriceChangeWarning` — large price difference alert
- `Button` — primary, outline
- `Toast` — status update confirmation

---

## Related Specs

- Assessment (normal flow): `06-assessment.md`
- Ticket Detail (source screen): `05-ticket-detail.md`
- Payment (after approval): `09-payment-receipt.md`
- Success states: `14-success-states.md`
