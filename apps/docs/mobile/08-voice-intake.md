# Feature: Voice Intake (Ambil Suara)

> **Screen-level UI spec only.**  
> For design tokens: `design-system/01-tokens-and-colors.md`  
> For reusable components: `design-system/02-components.md`

---

## Screen: Voice Intake (`voice-intake.tsx`)

### Purpose

One-tap voice intake for any repair. Staff speaks naturally, AI extracts device, issue, customer, and price. Confirm in one tap. This is the fastest path (<5 seconds average).

### Layout

- Centered, minimal UI. Vertical stack.
- No header back button needed if opened from FAB menu — use swipe-down or close button.
- Background: `--background`.

---

## States

### State 1: Idle

```
┌─────────────────────────────────────────┐
│                                         │
│           🎤                            │
│         (80px icon)                     │
│                                         │
│    Ketuk untuk rakam                    │
│                                         │
│  "iPhone 14 Pro skrin pecah,            │
│   Ahmad, tiga ratus lima puluh"         │
│   (example subtitle)                    │
│                                         │
└─────────────────────────────────────────┘
```

- **Mic icon**: 80px, `--primary` color.
- **Prompt text**: "Ketuk untuk rakam" (`heading-2`, `--foreground`)
- **Subtitle**: "Sebut: peranti, masalah, pelanggan, harga" (`body`, `--muted-foreground`)
- **Example**: `caption`, `--muted-foreground` in italics.

### State 2: Recording

```
┌─────────────────────────────────────────┐
│                                         │
│    🔴 (pulsing red circle)              │
│    00:04                                │
│    [waveform bars]                     │
│                                         │
│    Sedang mendengar...                  │
│                                         │
│    [Tap to stop]                       │
│                                         │
└─────────────────────────────────────────┘
```

- **Red pulsing circle**: 80px, `--destructive` color, animated pulse (scale 1.0 → 1.2, opacity 1.0 → 0.6, loop 1s).
- **Timer**: `heading-1`, `--destructive`, monospaced (e.g., "00:04").
- **Waveform**: Horizontal animated bars (10–15 bars), height varies with simulated amplitude, color `--destructive` at 50% opacity.
- **Label**: "Sedang mendengar..." (`heading-3`, `--destructive`)
- **Tap to stop**: tap anywhere on screen or dedicated stop button.
- **Max duration**: 30 seconds. Auto-stops at 30s.
- Haptic: light impact on start recording.

### State 3: Extracting

```
┌─────────────────────────────────────────┐
│                                         │
│         (spinner)                       │
│                                         │
│    Mengenal pasti...                    │
│                                         │
│    "iPhone 14 Pro skrin pecah,          │
│     Ahmad, tiga ratus..."              │
│    (transcript appears char by char)   │
│                                         │
└─────────────────────────────────────────┘
```

- **Spinner**: `Spinner` component, `--primary`, 48px.
- **Label**: "Mengenal pasti..." (`heading-2`, `--primary`)
- **Transcript**: `body`, `--muted-foreground`, appears character by character (typewriter effect).
- Duration: ~1–2 seconds (actual API call time).

### State 4: Result (Confirmation Card)

```
┌─────────────────────────────────────────┐
│                                         │
│  ✅ Peranti:    iPhone 14 Pro           │
│  ✅ Masalah:    Skrin pecah             │
│  ✅ Pelanggan:  Ahmad                   │
│  ✅ Harga:      RM350                   │
│                                         │
│  Keyakinan: 91%                         │
│                                         │
│  Transkrip:                             │
│  "iPhone 14 Pro skrin pecah,            │
│   Ahmad, tiga ratus lima puluh"        │
│                                         │
│  [HANTAR TIKET]                         │
│  [Rakam Semula]                         │
│                                         │
└─────────────────────────────────────────┘
```

- **Field rows**: icon + label + value.
  - Icon: checkmark (`Check` Lucide) in `--success` for confirmed fields.
  - Icon: question mark (`HelpCircle`) in `--warning` for low-confidence or missing fields.
  - Label: `caption`, `--muted-foreground` (e.g., "Peranti:")
  - Value: `body-medium`, `--foreground` (e.g., "iPhone 14 Pro")
  - Each row has an edit icon (`Pencil`, 16px, `--muted-foreground`) on the right. Tap → opens inline edit for that field.
- **Confidence**: `overline`, `--muted-foreground` (e.g., "Keyakinan: 91%"). If < 80%, color changes to `--warning`.
- **Transcript**: Collapsible section. Label "Transkrip:", text in `caption`, `--muted-foreground`.

### Edit Field Inline

- Tap pencil on any row → input field replaces the value.
- `[Simpan]` / `[Batal]` buttons appear below.
- Device field: typeahead search (same as Quick Repair).
- Issue field: dropdown of common issues.
- Customer field: text input.
- Price field: number input with RM prefix.

### Actions in Result State

| Button           | Style                       | Action                                                                             |
| ---------------- | --------------------------- | ---------------------------------------------------------------------------------- |
| `[HANTAR TIKET]` | `primary`, `lg`, full width | Creates ticket, auto-approved → `IN_PROGRESS`. Success state same as Quick Repair. |
| `[Rakam Semula]` | `outline`, `lg`, full width | Resets to Idle state.                                                              |

---

## Access Points

| Entry Point         | Trigger                                 | Role             |
| ------------------- | --------------------------------------- | ---------------- |
| Home screen FAB     | Long press → "Ambil Suara"              | Technicians only |
| Quick Repair screen | 🎤 mic button in header                 | Technicians only |
| New Ticket screen   | 🎤 mic button in customer notes section | All roles        |

---

## Success States

Same as Quick Repair (`07-quick-repair.md`):

- **With phone**: WhatsApp receipt sent, "Status: Dalam Proses".
- **Without phone**: QR code displayed.

See `14-success-states.md` for detailed layout.

---

## Design Token References

| Element                | Token                  |
| ---------------------- | ---------------------- |
| Mic icon (idle)        | `--primary`            |
| Recording pulse        | `--destructive`        |
| Timer text             | `--destructive`        |
| Waveform bars          | `--destructive` at 50% |
| Extracting spinner     | `--primary`            |
| Extracting label       | `--primary`            |
| Result checkmark       | `--success`            |
| Low-confidence warning | `--warning`            |
| Confidence text        | `--muted-foreground`   |
| Transkrip text         | `--muted-foreground`   |
| Submit button bg       | `--primary`            |
| Retake button border   | `--border`             |

---

## Components Used

- `VoiceRecorder` — mic button + recording animation
- `PulsingCircle` — animated red circle during recording
- `Waveform` — horizontal animated bars
- `Timer` — monospaced countdown
- `ExtractionCard` — spinner + typewriter transcript
- `ConfirmationCard` — field rows with edit icons
- `InlineEdit` — tap-to-edit field row
- `Button` — primary, outline

---

## Related Specs

- Navigation shell (FAB long-press): `02-navigation-shell.md`
- Quick Repair (alternative fast path): `07-quick-repair.md`
- New Ticket (notes mic button): `04-new-ticket-full-ai.md`
- Success states: `14-success-states.md`
- QR Receipt (anonymous): `13-qr-receipt.md`
