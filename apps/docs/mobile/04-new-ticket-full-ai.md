# Feature: New Ticket — Full AI Intake

> **Screen-level UI spec only.**  
> For design tokens: `design-system/01-tokens-and-colors.md`  
> For reusable components: `design-system/02-components.md`

---

## Screen: New Ticket (`new-ticket.tsx`)

### Purpose

Front desk creates a new repair ticket. Camera-first, minimal typing. AI identifies the device from a photo and detects pre-existing damage. This is the "Full AI" path (30–90 seconds average).

### Layout

- `ScrollView` with section dividers (1px `--border` horizontal lines).
- `KeyboardAvoidingView` with `behavior="padding"`.
- Sections stack vertically with 16px gaps between them.

---

## Section 1: Camera Capture Area

### Position

Top of screen, most prominent element.

### Dimensions

- Full width minus 32px horizontal padding (16px each side).
- Aspect ratio: 4:5 (portrait).
- Dashed border: 2px dashed `--border`.
- Corner brackets: 4 small L-shaped lines at corners, `--muted-foreground`.

### States

| State                            | Visual                                                                                                                                                                                                                       |
| -------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Empty**                        | Camera icon (48px, `--muted-foreground`) centered. Label: "Ketuk untuk ambil gambar peranti" (`body`, `--muted-foreground`). Subtitle: "Pastikan keseluruhan bahagian belakang kelihatan" (`caption`, `--muted-foreground`). |
| **Preview**                      | Captured photo fills the area. Quality check overlay appears (see below). Retake button visible.                                                                                                                             |
| **Quality Warning**              | Yellow banner overlay on photo: "Gambar kabur — ambil semula" or "Pastikan keseluruhan peranti kelihatan". Banner bg: `--warning` at 20%, text `--warning`.                                                                  |
| **Loading (after quality pass)** | Semi-transparent overlay on photo. Spinner (`Spinner` component, `--primary`). Text: "Mengenal pasti peranti..." (`body`, `--primary-foreground`).                                                                           |
| **Identified**                   | Photo shown at top. Below: Device Card + Damage Report (Section 2).                                                                                                                                                          |

### Photo Quality Gate (Local, Before AI)

These checks run locally on the device _before_ sending to AI:

| Check           | UI Indicator                                                |
| --------------- | ----------------------------------------------------------- |
| Blur detection  | Real-time blur score overlay (green/yellow/red dot + label) |
| Lighting check  | Lighting indicator dot: 🟢 Baik / 🟡 Sederhana / 🔴 Kurang  |
| Device-in-frame | Guide brackets turn green when device fills ~70% of frame   |

- Quality gate runs on the captured photo (not live preview).
- If any check fails: show "Ambil Semula" (retake) and "Teruskan" (proceed anyway) buttons.
- "Teruskan" is `outline` style to discourage skipping quality gate.

### Retake Button

- Position: Top-right corner of the preview area, 36px circle.
- Background: `--card` with 80% opacity.
- Icon: `CameraRotate` (Lucide), 20px, `--foreground`.
- Tap → clears current photo, returns to Empty state.

---

## Section 2: Device Identification Result + Damage Report

### Appears

Below camera area, after AI response returns.

### 2A: Device Card

```
┌─────────────────────────────────────────┐
│  [📱 Device Image/Icon]                 │
│  iPhone 14 Pro                          │
│  Ungu Tua (Deep Purple)                 │
│  Keyakinan: 94% ✅                      │
└─────────────────────────────────────────┘
```

- **Device icon/image**: 48px, left-aligned or centered.
- **Model name**: `heading-3`, `--foreground`
- **Color/variant**: `body`, `--muted-foreground`
- **Confidence**: `caption`, `--secondary`. Format: "Keyakinan: 94%" + checkmark icon.
  - If confidence ≥ 80%: checkmark is green (`--success`).
  - If confidence 50–79%: checkmark is yellow (`--warning`).
  - If confidence < 50%: checkmark is red (`--destructive`).

### Actions

| Button                  | Style           | Action                                         |
| ----------------------- | --------------- | ---------------------------------------------- |
| `[Betul]`               | `primary`, `sm` | Confirms device, proceeds to customer notes    |
| `[Tidak, pilih manual]` | `outline`, `sm` | Opens bottom sheet with searchable device list |

### Manual Selection Bottom Sheet

- Height: ~70% of screen.
- Search input at top: placeholder "Cari peranti...", with search icon.
- List: device name + brand, grouped by brand (iPhone / Samsung).
- Tap row → selects device, dismisses sheet, updates Device Card.
- Dismiss: swipe down or tap backdrop.

### 2B: Pre-Existing Damage Section

- Label: "Keadaan Peranti (Sebelum Pembaikan)" with `Shield` icon (Lucide), `caption`, `--muted-foreground`.
- Collapsible: tap header to expand/collapse. Default: expanded if damages detected.

#### Damage List Item

```
┌─────────────────────────────────────────┐
│ 🟡 Goresan halus                        │
│    Lokasi: Panel belakang               │
│    [Thumbnail]  [✕]                    │
│                                         │
│ 🔴 Retakan — Sederhana                  │
│    Lokasi: Skrin                        │
│    [Thumbnail]  [✕]                    │
│                                         │
│ ✅ Tiada kerosakan air                  │
└─────────────────────────────────────────┘
```

- **Severity indicator**: Small colored dot before damage type.
  - 🟢 `Ringan` / `Tiada` — green (`--success`)
  - 🟡 `Sederhana` — yellow (`--warning`)
  - 🔴 `Teruk` — red (`--destructive`)
- **Damage type**: `body`, `--foreground`
- **Location**: `caption`, `--muted-foreground`
- **Thumbnail**: Small square image (48px) showing bounding box overlay on intake photo. Tap → opens full photo modal.
- **Dismiss (✕)**: 24px circle, `--muted-foreground`. Tap → removes that damage entry. Haptic: light impact.

#### Manual Add Damage

- `[+ Tambah Kerosakan]` — `ghost` button, `sm`.
- Opens form:
  - Damage type dropdown: `Goresan` | `Retakan` | `Kemek` | `Kesan air` | `Lain-lain`
  - Location input: text, placeholder "Lokasi kerosakan"
  - Severity: segmented control `Ringan` | `Sederhana` | `Teruk`
  - `[Simpan]` / `[Batal]` buttons.

### Confirm Damage

- `[✓ Sahkan Keadaan]` — `primary` button, `lg`, full width.
- Disabled until device confirmed (either AI or manual selection).

---

## Section 3: Customer Notes (Text + Voice)

### Label

"Nota Pelanggan" (`heading-3`)

### Dual Input Mode

#### Text Mode (Default)

- Multiline `Input` / `TextArea`.
- Placeholder: "Apa masalah yang pelanggan nyatakan?"
- Auto-growing height, max 200px.
- Character count: optional, `overline`, `--muted-foreground`.

#### Voice Mode

- Microphone button (🎤) to the right of the text area or in the section header.
- Tap to start recording.

**Recording UI:**

- Red pulsing circle animation around mic icon.
- Timer: "00:04" (up to 30 seconds max).
- Waveform visualization: horizontal animated bars.
- Label: "Sedang mendengar..." (`caption`, `--destructive`)
- Tap again to stop.
- Haptic: light impact on start, medium impact on stop.

**After recording stops:**

- Transcript appears in the text area.
- Structured data badges auto-inserted below text area:
  - "Skrin — Pecah (sederhana)" — pill badge, `--warning` bg
  - "Sentuh — Tidak berfungsi" — pill badge, `--destructive` bg
- Voice memo playback: inline audio player with play/pause button, waveform scrubber.

---

## Section 4: Customer Information

### Label

"Maklumat Pelanggan" (`heading-3`)

### Fields

| Field   | Type  | Placeholder                 | Required | Validation                                         |
| ------- | ----- | --------------------------- | -------- | -------------------------------------------------- |
| Nama    | text  | "Nama penuh"                | Yes      | Min 2 characters                                   |
| Telefon | phone | "+6012-345-6789"            | Yes      | Auto-format as +60 prefix. Valid MY mobile format. |
| Emel    | email | "alamat@emel.com (pilihan)" | No       | Valid email if provided                            |

- Phone input: auto-prefix "+60" if user types numbers without country code. Format display with dashes.
- Email helper: "Pilihan — untuk resit emel" (`caption`, `--muted-foreground`).

---

## Section 5: Submit

### Submit Button

- `[Hantar ke Teknikal]` — `primary` button, `lg`, full width.
- Disabled until: device identified + customer name + phone filled.
- Subtext below button: "Tiada harga diberikan lagi" (`overline`, `--muted-foreground`).

### Submit States

| State     | UI                                                        |
| --------- | --------------------------------------------------------- |
| `idle`    | Button enabled (if validation passes)                     |
| `loading` | Spinner + "Sedang menghantar..."                          |
| `success` | Push to Success State screen (see `14-success-states.md`) |
| `error`   | Toast: "Gagal menghantar tiket. Sila cuba lagi."          |

---

## Success State

See `14-success-states.md` for full spec. Summary:

- Full-screen success overlay or push to success screen.
- Large checkmark icon, `--secondary`.
- "Tiket #D1-042 Dicipta" (`heading-1`)
- Details card: device model, customer name, outlet name.
- "WhatsApp akan dihantar kepada pelanggan" (`body`)
- Actions:
  - `[Buka Tiket]` — opens Ticket Detail (`05-ticket-detail.md`)
  - `[Tiket Baharu]` — resets form for next ticket
- **Technician shortcut**: If logged-in user is `technician`, show prompt: "Nilaikan sekarang?" with `[YA, NILAIKAN]` and `[KEMUDIAN]` buttons. See `14-success-states.md` §Technician Prompt.

---

## Design Token References

| Element                         | Token                  |
| ------------------------------- | ---------------------- |
| Camera area border (empty)      | `--border` dashed      |
| Camera area bg                  | `--card`               |
| Quality warning banner bg       | `--warning` at 20%     |
| Quality warning text            | `--warning`            |
| Loading overlay                 | `--background` at 60%  |
| Device card bg                  | `--card`               |
| Confidence text (high)          | `--success`            |
| Confidence text (med)           | `--warning`            |
| Confidence text (low)           | `--destructive`        |
| Damage severity dot — ringan    | `--success`            |
| Damage severity dot — sederhana | `--warning`            |
| Damage severity dot — teruk     | `--destructive`        |
| Voice recording pulse           | `--destructive`        |
| Submit button bg                | `--primary`            |
| Submit button text              | `--primary-foreground` |

---

## Components Used

- `CameraCapture` — inline camera viewfinder + capture + preview
- `PhotoQualityGate` — blur/lighting/frame overlay
- `DeviceCard` — device info + confidence display
- `DamageReport` — collapsible list of detected damages
- `DamageItem` — severity dot + type + location + thumbnail + dismiss
- `BottomSheet` — manual device selection, add damage form
- `Input` / `TextArea` — customer notes, customer info
- `VoiceRecorder` — mic button + recording UI + waveform
- `Badge` / `Pill` — structured damage badges
- `Button` — primary, outline, ghost variants
- `AudioPlayer` — voice memo playback inline

---

## Related Specs

- Navigation shell (FAB, tab bar): `02-navigation-shell.md`
- Jobs queue (after submit): `03-jobs-queue.md`
- Ticket detail ("Buka Tiket"): `05-ticket-detail.md`
- Assessment (technician prompt): `06-assessment.md`
- Quick Repair (alternative path): `07-quick-repair.md`
- Voice Intake (alternative path): `08-voice-intake.md`
- Success states: `14-success-states.md`
