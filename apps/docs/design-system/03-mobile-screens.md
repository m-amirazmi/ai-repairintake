> **📁 Active Development Moved:** Mobile screen UI specs have been broken down into per-feature files in `apps/docs/mobile/`.  
> For focused, screen-level specifications (Auth, Queue, New Ticket, Assessment, Quick Repair, Voice Intake, Payment, Profile, History, etc.), see the files in that folder. This document is kept for historical reference.

---

# Mobile Screens & Flows

## Navigation Structure

### Tab Navigator (iOS-style bottom tabs)

| Tab | Icon                     | Label (BM) | Label (EN) | Visible To       |
| --- | ------------------------ | ---------- | ---------- | ---------------- |
| 1   | `Home` (Lucide)          | Utama      | Home       | All roles        |
| 2   | `ClipboardList` (Lucide) | Tugas      | Jobs       | All roles        |
| 3   | `Stethoscope` (Lucide)   | Nilai      | Assess     | Technicians only |
| 4   | `History` (Lucide)       | Sejarah    | History    | All roles        |
| 5   | `UserCircle` (Lucide)    | Profil     | Profile    | All roles        |

**Active state**: Filled icon, `--primary` color
**Inactive state**: Outline icon, `--muted-foreground`
**Tab bar**: `height=64px`, background `--card`, top border 1px `--border`, safe area padding bottom

### Stack Screens (pushed over tabs)

- `new-ticket` — New Ticket Flow (from Home "Create Ticket" action card)
- `ticket/[id]` — Ticket Detail
- `assessment/[id]` — Assessment Screen (from Home "Assessment" action card or ticket detail)
- `quick-repair` — Quick Repair (from Home "Quick Repair" action card)
- `voice-intake` — Voice Intake (from Quick Repair header mic)
- `forgot-password` — Password reset

---

## Screen A: Login

### Purpose

Staff authentication with optional biometric login.

### Layout

- `SafeAreaView`, centered content vertically
- Logo/icon at top: 80px, `--primary`
- Title: "Selamat Datang" (`heading-1`)
- Subtitle: "Log masuk ke akaun anda" (`body`, `--muted-foreground`)

### Fields

1. **Emel** — `email` type input, placeholder: "alamat@emel.com"
2. **Kata Laluan** — `password` type, placeholder: "Kata laluan", toggle visibility icon

### Actions

- `[Log Masuk]` — `primary` button, `lg` size, full width
- Divider with text "atau"
- `[Lupa kata laluan?]` — `ghost` button

### Biometric Login

- If Face ID / Touch ID is available and enrolled:
  - Show face/fingerprint icon below password field
  - Tap icon triggers biometric prompt
  - Label: "Log masuk dengan Face ID"
- Uses `expo-local-authentication`
- Fallback to password on biometric failure

### Bottom Section

- Language toggle: `[BM]` / `[EN]` segmented control
- Powered by text (optional, minimal)

### Keyboard Handling

- `KeyboardAvoidingView behavior="padding"`
- ScrollView to handle small screens
- Tap outside dismisses keyboard

### States

| State              | UI                                          |
| ------------------ | ------------------------------------------- |
| `idle`             | Fields empty, button disabled               |
| `typing`           | Button enabled when both fields valid       |
| `loading`          | Button shows spinner, "Sedang log masuk..." |
| `error`            | Toast: "Emel atau kata laluan tidak sah"    |
| `biometric-prompt` | Native iOS Face ID/Touch ID modal           |

---

## Screen B: Jobs Queue (Tab 1 — Home)

### Purpose

Primary work surface. Shows today's repair tickets with quick filtering.

### Layout

- Header: Menu hamburger (left), "Senarai Tugas" title, notification bell (right)
- Outlet selector: "Dungun 1" with chevron (managers can switch)
- Filter chips row (horizontal scroll):
  - `Semua` | `Diterima` | `Sedang Dinilai` | `Selesai`
  - Selected: filled `--primary`, text `--primary-foreground`
  - Unselected: outlined, `--foreground`

### Ticket List (Compact)

**Card layout** (compact density):

```
┌─────────────────────────────────────────┐
│ #D1-042                        10m lalu │
│ iPhone 14 Pro         [Badge: Diterima] │
│ Ahmad bin Abdullah                      │
│ "Skrin pecah, sentuh rosak"             │
│                                     [▶] │
└─────────────────────────────────────────┘
```

- **Card height**: ~80px (compact)
- **Left side**: Ticket number (`mono`, `--primary`), device model (`caption`), customer (`caption`), notes truncated (`overline`, `--muted-foreground`)
- **Right side**: Time ago (`overline`), status badge, chevron
- **Swipe right**: Quick action "Buka" (open)
- **Swipe left**: Quick action "Hantar ke Teknikal" (if received)
- **Tap**: Push to Ticket Detail

### Empty State

- Icon: `ClipboardList`, 64px
- Title: "Tiada tugas hari ini"
- Description: "Semua tiket telah diselesaikan. Rehat sebentar!"
- Action: "[Tiket Baharu]" button

### Pull-to-Refresh

- Standard iOS pull gesture
- Spinner color: `--primary`

### Real-time (Month 3)

- Upgrade to Supabase Realtime subscription
- New tickets animate in at top of list
- Status changes animate badge color transition

---

## Screen C: New Ticket

### Purpose

Front desk creates a new repair ticket. Camera-first, minimal typing.

### Layout

- ScrollView with section dividers
- KeyboardAvoidingView

### Sections

#### 1. Camera Capture Area

- Inline camera (not modal)
- Aspect ratio 4:5, full width minus 32px horizontal padding
- Dashed border, corner brackets
- **Photo Quality Gate** (local, before AI):
  - Real-time blur detection overlay
  - Lighting indicator (green/yellow/red)
  - Device-in-frame guide brackets
- States:
  - **Empty**: Camera icon + "Ketuk untuk ambil gambar peranti"
  - **Preview**: Photo shown, quality check running
  - **Quality Warning**: Yellow banner "Gambar kabur — ambil semula" or "Pastikan keseluruhan peranti kelihatan"
  - **Loading** (after quality pass): Spinner + "Mengenal pasti peranti..."
  - **Identified**: Photo shown, device card + damage report below
- Retake button: top-right of preview area, 36px circle, `--card` bg, camera-rotate icon

#### 2. Device Identification Result + Damage Report

- Appears below camera after AI response
- **Device Card**:
  - Device icon/image
  - Model name: `heading-3`
  - Color/variant: `body`
  - Confidence: "Keyakinan: 94%" + checkmark icon in `--secondary`
- **Pre-Existing Damage Section** (collapsible):
  - Label: "Keadaan Peranti (Sebelum Pembaikan)" with shield icon
  - List of detected damages:
    - "Goresan halus — panel belakang" with bounding box thumbnail
    - "Retakan — skrin (penjuru kiri bawah)" with severity badge
  - Each item: type badge, location, severity color-coded
  - Technician can dismiss individual detections (tap X)
  - `[+ Tambah Kerosakan]` — manual addition
- Actions:
  - `[Betul]` — `primary` button, sm
  - `[Tidak, pilih manual]` — `outline` button, sm
- If manual: opens bottom sheet with searchable device list

#### 3. Customer Notes (Text + Voice)

- Label: "Nota Pelanggan"
- **Dual input mode**:
  - **Text**: Multiline input, placeholder: "Apa masalah yang pelanggan nyatakan?"
  - **Voice**: Microphone button, tap to record, tap to stop
    - Recording UI: red pulse animation, timer, waveform
    - On stop: upload to Whisper → transcript appears in text field
    - Structured data extracted: damage type, location, severity badges auto-inserted
- Auto-growing, max 200px height
- Voice memo playback: inline audio player with play/pause

#### 4. Customer Information

- Label: "Maklumat Pelanggan"
- **Nama**: text input, placeholder: "Nama penuh"
- **Telefon**: phone input, placeholder: "+6012-345-6789", auto-format as +60
- **Emel**: email input, placeholder: "alamat@emel.com (pilihan)"
- Phone is required; email is optional

#### 5. Submit

- `[Hantar ke Teknikal]` — `primary` button, `lg`, full width
- Disabled until: device identified + customer name + phone filled
- Subtext: "Tiada harga diberikan lagi" (`overline`, `--muted-foreground`)

### Success State

After submit:

- Full-screen success overlay or push to success screen
- Ticket number: `mono`, large, `--primary`
- Details: device, customer, outlet
- WhatsApp confirmation: "WhatsApp akan dihantar kepada pelanggan"
- Actions:
  - `[Buka Tiket]` — opens ticket detail
  - `[Tiket Baharu]` — resets form for next ticket
- Technician shortcut: If user is technician, show prompt "Nilaikan sekarang?" (see Flow D)

---

## Screen D: Ticket Detail

### Purpose

View full ticket information. Access assessment (technicians).

### Layout

- ScrollView
- Header: Back arrow, ticket number (`mono`), optional menu (⋯)

### Content

#### Device & Customer Card

- Left: Device image (or icon), model name, color
- Right: Customer name, phone number (tap to call), WhatsApp button
- "[Lihat gambar]" — opens intake photo in modal
- "[Hubungi di WhatsApp]" — opens WhatsApp chat

#### Status Timeline

- Vertical timeline of status changes
- Current status: filled dot + `--primary`
- Past statuses: filled dot + `--muted-foreground`
- Future statuses: empty dot + `--border`
- Labels: `overline`

#### Notes Section

- **Nota pelanggan**: "Skrin pecah, sentuh tak berfungsi"
- **Nota teknikal**: (if assessed) technician's notes
- Displayed in card with light background

#### Assessment Summary (if assessed)

- Parts list with quantities and prices
- Labor hours and cost
- Total estimate
- Timeline estimate

#### Actions

- `[Buka Penilaian]` — technician only, pushes to Assessment
- `[Kemaskini Status]` — dropdown status selector
- `[Hantar WhatsApp]` — manual notification trigger

---

## Screen E: Assessment

### Purpose

Technician diagnoses the device, selects parts, calculates quote.

### Layout

- ScrollView with KeyboardAvoidingView
- Sections separated by dividers

### Header

- Back arrow
- Title: "Tiket #D1-042 — Penilaian"
- `[Simpan Draf]` — `outline` button (top right or floating)

### Sections

#### 1. Device & Customer (Collapsible)

- Compact card, collapsible to save space
- Same info as Ticket Detail

#### 2. AI Diagnosis Suggestions

- Label: "Cadangan Diagnosis" with sparkle icon
- Subtitle: "AI berdasarkan [Device Model] + nota pelanggan"
- Suggestion cards (radio-button style):
  ```
  ┌─────────────────────────────────────────┐
  │ [✓] Penukaran Skrin OLED                │
  │     95% keyakinan                       │
  │     0.75 jam | RM89 (part)              │
  └─────────────────────────────────────────┘
  ```
- Each card: border, 12px radius, tap to select
- Selected: border `--primary`, left accent line
- Unselected: border `--border`
- Confidence badge: `overline`, `--muted-foreground`

#### 3. Manual Diagnosis

- Label: "Atau taip diagnosis sendiri"
- Search input: placeholder "Cari atau taip diagnosis..."
- Dropdown of known issues from database
- Or free-text input

#### 4. Parts Selection

- Label: "Bahagian"
- List of selected parts:
  ```
  ┌─────────────────────────────────────────┐
  │ Skrin OLED + Digitizer            x1    │
  │          Kos: RM89    Harga:   RM179    │
  └─────────────────────────────────────────┘
  ```
- Each row: part name, quantity stepper (+/-), cost/price
- `[+ Tambah Bahagian]` — `secondary` button, opens bottom sheet

#### 5. Labor

- Label: "Upah"
- **Masa**: number input (hours), step 0.25
- **Kadar**: display only, from tenant settings (e.g., RM50/jam)
- **Jumlah**: calculated display, `mono`, `--primary`

#### 6. Price Breakdown (Sticky Card)

- Background: `--card`, border `--border`, radius `radius-lg`
- Always visible or sticky at bottom while scrolling
- ```
  Bahagian:       RM 94.00
  Upah:           RM 37.50
  Tambahan: [___] RM  0.00
  ───────────────────────
  ANGGARAN:       RM 131.50
  ```
- `ANGGARAN` in `heading-3`, `--primary`

#### 7. Timeline

- Label: "Tempoh"
- Radio group:
  - `● 30-45 minit (tunggu)`
  - `○ 1-3 hari`
  - `○ 3-7 hari`
- Selected: filled circle, `--primary`

#### 8. Technical Notes

- Multiline input
- Placeholder: "Nota teknikal (pilihan)"

### Actions

- `[Simpan Draf]` — `outline` button
- `[Hantar & Notify]` — `primary` button, `lg`
  - Triggers WhatsApp notification to customer
  - Changes status to `assessed`

### Auto-save

- Saves draft on every field change
- Debounced: 500ms after last change
- Stored in AsyncStorage with ticket ID key
- Indicator: "Draf disimpan" toast, fades after 2s

---

## Screen F: Profile (Tab 4)

### Layout

- ScrollView
- Header: "Profil"

### User Card

- Avatar (48px), name (`heading-3`), role badge (`caption-medium`), outlet (`body`, `--muted-foreground`)

### Settings List

- Rows with chevron (→), separated by dividers

| Row                 | Value           | Action                 | Restrictions  |
| ------------------- | --------------- | ---------------------- | ------------- |
| Tukar Cawangan      | "Dungun 1"      | Push outlet selector   | Managers only |
| Bahasa              | "Bahasa Melayu" | Push language selector | All           |
| Mod Gelap           | Toggle          | Immediate toggle       | All           |
| Notifikasi          | Toggle          | System settings        | All           |
| Log Masuk Biometrik | Toggle          | Face ID prompt         | All           |

### Bottom

- `[Log Keluar]` — `destructive` button, full width
- App version: `overline`, `--muted-foreground`, centered

### Dark Mode Toggle

- Immediate switch, no reload needed
- Uses React context + AsyncStorage persistence
- Respects system preference on first launch

---

## Screen G: History (Tab 3)

### Layout

- Search bar at top (sticky)
- Filter chips: date range, status, outlet
- Same compact list as Jobs Queue

### Search

- Placeholder: "Cari tiket, pelanggan, atau peranti"
- Debounced search (300ms)
- Results update in real-time

### Empty State

- Icon: `History`
- Title: "Tiada sejarah lagi"
- Description: "Tiket yang selesai akan muncul di sini"

---

## Screen H: Success / Confirmation States

### Ticket Created

- Full-screen or modal overlay
- Large checkmark icon, `--secondary`
- "Tiket #D1-042 Dicipta"
- Details card: device, customer, outlet
- "WhatsApp akan dihantar kepada pelanggan"
- Actions: `[Buka Tiket]` `[Tiket Baharu]`

### Assessment Sent

- Similar layout
- "Penilaian dihantar!"
- "Pelanggan akan dimaklumkan melalui WhatsApp"
- Actions: `[Kembali ke Senarai]` `[Lihat Tiket]`

### Approval Received

- "Pelanggan telah luluskan!"
- "Pembaikan boleh dimulakan"
- Action: `[Mula Pembaikan]`

---

## Screen I: Assessment Prompt (Flow D — Technician)

### Modal Overlay

- When a technician creates a ticket
- Card centered on screen with backdrop
- "Anda adalah teknikal. Nilaikan peranti ini sekarang?"
- Actions:
  - `[YA, NILAIKAN]` — `primary` — pushes to Assessment
  - `[KEMUDIAN]` — `outline` — dismisses, ticket appears in queue

---

## Screen J: Device Dead / Unknown (Flow C)

### Warning Card

- Background: `--destructive` at 10% opacity
- Icon: Alert-triangle
- Title: "Peranti Tidak Boleh Dihidupkan"
- Description: "Diagnosis tidak boleh dibuat tanpa pemeriksaan fizikal."

### Options

1. **Yuran Pemeriksaan** — `primary` button
   - "RM29 — Periksa peranti. Anggaran akan dihantar kemudian."
2. **Tunggu Kelulusan** — `outline` button
   - "Maklumkan pelanggan dulu."

### After Physical Inspection

- Shows updated AI suggestions
- Technician inputs actual diagnosis
- If estimate changes significantly, shows warning: "Pelanggan perlu luluskan semula"

---

## Screen K: Quick Repair (Baiki Cepat)

### Purpose

Technician-at-counter creates a ticket for a known fix with known price. No AI vision, no diagnosis flow — just typeahead + quick-select + price confirmation. Ticket auto-approved.

### Layout

- Header: "Baiki Cepat" with back arrow. 🎤 mic button (right) for voice mode.
- ScrollView, minimal sections.

### Sections

#### 1. Device (Typeahead)
- Text input with search icon
- Placeholder: "Cari peranti..."
- Typeahead results appear inline below input (not a modal)
- Results show: device icon, model name, variant
- Auto-select on exact match (e.g., "iPh" → iPhone 14 Pro highlighted)

#### 2. Issue (Quick-Select)
- Dropdown or scrollable chip list
- Top-10 common issues for selected device (from `device_quick_issues`)
- Each option shows:
  - Issue name (e.g., "Penukaran Skrin OLED")
  - Default price (e.g., "RM350")
  - Labor time (e.g., "0.75 jam")
  - Parts summary on expand (e.g., "1× Skrin OLED, 1× Gam")
- Selected option highlighted with `--primary` border + left accent
- Fallback: `[+ Taip masalah lain]` for unlisted issues

#### 3. Price Card (Sticky)
- Background: `--card`, border `--border`
- Shows:
  ```
  Bahagian (1):    RM 339.00
  Upah (0.75j):    RM  11.00
                    ────────
  JUMLAH:          RM 350.00  [Edit]
  ```
- Tapping [Edit] opens inline number input
- Override confirmation if change > 20%

#### 4. Customer (Optional)
- Label: "Pelanggan (Pilihan)"
- **Nama**: text input, placeholder: "Nama pelanggan"
- **Telefon**: phone input, placeholder: "+6012-345-6789"
- Typeahead on phone for existing customers
- Helper text: "Jika kosong, resit dengan kod QR akan dijana"

#### 5. Submit
- `[HANTAR & CETAK RESIT]` — `accent` button, `lg`, full width
- Subtext: "Pembaikan akan dimulakan serta-merta"

### Success States

| Phone Provided | No Phone |
|---|---|
| WhatsApp resit dihantar ke [nombor] | QR code displayed full-screen |
| Status: Dalam Proses | "Imbas untuk kemaskini WhatsApp" |
| [Buka Tiket] [Baiki Cepat Baru] | [Buka Tiket] [Baiki Cepat Baru] |

---

## Screen L: Voice Intake (Ambil Suara)

### Purpose

One-tap voice intake for any repair. Staff speaks, AI extracts, confirm in one tap.

### Layout

- Centered, minimal UI
- Large microphone button (80px) in the center
- Hero prompt text below

### States

#### idle
- 🎤 icon, 80px, `--primary`
- Text: "Ketuk untuk rakam"
- Subtitle: "Sebut: peranti, masalah, pelanggan, harga"
- Example: "iPhone 14 Pro skrin pecah, Ahmad, tiga ratus lima puluh"

#### recording
- 🔴 Red pulsing circle animation around mic
- Timer: "00:04" (up to 30 seconds)
- Waveform visualization (horizontal bars)
- Label: "Sedang mendengar..."
- Tap again to stop

#### extracting
- Spinner + "Mengenal pasti..."
- Transcript appears character by character
- ~1-2 seconds (Whisper + GPT-4o extraction)

#### result
- Confirmation card slides in from bottom:
  ```
  ✅ Peranti:    iPhone 14 Pro
  ✅ Masalah:    Skrin pecah
  ✅ Pelanggan:  Ahmad
  ✅ Harga:      RM350
  
  Keyakinan: 91%
  
  Transkrip: "iPhone 14 Pro skrin pecah,
  Ahmad, tiga ratus lima puluh"
  ```
- Each field has an edit icon (pencil) for correction
- `[HANTAR TIKET]` — `primary` button, `lg`
- `[Rakam Semula]` — `outline` button

### Access Points
- Home screen FAB → long-press → "Ambil Suara"
- Quick Repair screen → 🎤 icon in header
- New Ticket screen → 🎤 in customer notes section

---

## Screen M: Payment & Receipt

### Purpose

Record payment when a ticket is marked completed. Sends WhatsApp receipt or displays QR code receipt.

### Trigger
- Technician taps "Selesai" on a ticket → payment screen slides up
- Or from ticket detail: `[Rekod Bayaran]` action button

### Layout

#### Header
- Back/Cancel button
- Title: "Pembayaran — Tiket #D1-043"
- Ticket summary: device model, issue, outlet

#### Price Recap Card
- Background: `--card`, border `--border`
- Parts breakdown (if from assessment)
- Labor breakdown
- Total displayed prominently in `heading-2`, `--primary` color

#### Payment Form
- **Amaun**: number input, auto-filled with estimate total, editable
  - If edited lower: shows "Beza: -RM50.00" + reason dropdown (Diskaun / Harga Berbeza / Bayaran Sebahagian)
- **Kaedah**: radio group (Tunai / QR Pay / Bank Transfer)
  - Visual indicators: 💵 for Tunai, 📱 for QR Pay, 🏦 for Bank Transfer
- **Nota**: optional text input

#### Submit
- `[SAHKAN BAYARAN & SELESAI]` — `primary` button, `lg`, full width
- Subtext: "Stok akan dikemaskini secara automatik"
- Shows parts being decremented: "Stok: Skrin OLED -1 (1 → 0)"

### Success State

| With Phone | Without Phone |
|---|---|
| ✅ Pembayaran Diterima | ✅ Pembayaran Diterima |
| WhatsApp resit dihantar ke +60... | QR code displayed, 200px centered |
| Resit WhatsApp preview card | "Tunjukkan QR ini kepada pelanggan" |
| [Kembali ke Senarai] | [Kembali ke Senarai] |

### Receipt WhatsApp Message Format
```
🔧 Repair Intake — Resit
Tiket #D1-043
iPhone 14 Pro — Penukaran Skrin OLED
Jumlah: RM350.00 (Tunai)
Cawangan: Dungun 1
Tarikh: 03 Jun 2026, 3:30 PM

Terima kasih! Sila simpan resit ini.
```

---

## Screen N: QR Code Receipt Display

### Purpose

For anonymous Quick Repair / Voice tickets — a screen the technician shows to the customer containing a QR code that links to the public ticket web view.

### Layout

- Large QR code (200×200px), centered
- Ticket number above
- Device + issue summary
- Status badge
- Text: "Imbas untuk kemaskini WhatsApp & status"
- Outlet name and address below
- `[Buka Tiket]` and `[Tiket Baharu]` buttons

### When Shown
- After Quick Repair submit (no phone)
- After Voice Intake submit (no phone)
- After payment recording (no phone)
- Accessible from ticket detail at any time via `[Kod QR]` button

---

## iOS-Specific Patterns

### Haptics

- Light impact: button presses, toggles
- Medium impact: submit success, status change
- Heavy impact: error, destructive action
- Selection: picker wheels, steppers

### Gestures

- Swipe right on list item: quick action
- Swipe left on list item: delete/archive (optional)
- Pull down: refresh
- Long press: context menu (ticket options)

### Transitions

- Push: standard iOS slide from right
- Modal: slide up from bottom, dismiss swipe down
- Tab switch: no animation (instant) or subtle fade

### Status Bar

- Light mode: dark content
- Dark mode: light content
- Dynamic based on theme

### Notch / Dynamic Island

- Avoid placing interactive elements in top safe area
- Use `SafeAreaView` on all screens
