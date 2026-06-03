# Component Primitives

All components are built with **NativeWind** (Tailwind CSS for React Native) and **React Native Reusables** patterns. Each component accepts a `className` prop for style overrides.

---

## Button

### Variants

| Variant | Background | Text | Border | Usage |
|---------|-----------|------|--------|-------|
| `default` / `primary` | `--primary` | `--primary-foreground` | none | Main CTAs: "Hantar", "Log Masuk", "Simpan" |
| `secondary` | `--secondary` | `--secondary-foreground` | none | Secondary: "Tambah Bahagian", "Hubungi" |
| `accent` | `--accent` | `--accent-foreground` | none | Highlights: "Tiket Baharu", "YES" approval |
| `destructive` | `--destructive` | `--destructive-foreground` | none | Danger: "Batal", "Padam", "NO" |
| `outline` | transparent | `--foreground` | 1px `--border` | Neutral: "Kemudian", "Tidak, pilih manual" |
| `ghost` | transparent | `--muted-foreground` | none | Low priority: "Lupa kata laluan?" |
| `link` | transparent | `--primary` | none | Inline links |

### Sizes

| Size | Height | Padding | Font | Icon |
|------|--------|---------|------|------|
| `sm` | 36px | 12px horizontal | `caption-medium` | 16px |
| `default` | 48px | 16px horizontal | `body` | 20px |
| `lg` | 56px | 24px horizontal | `body-medium` | 24px |
| `icon` | 44×44px | 0 | — | 20px centered |

### States

- **Default**: full opacity
- **Pressed**: opacity 0.9, scale 0.98
- **Disabled**: opacity 0.5, no press feedback
- **Loading**: spinner replaces text, disabled state

### Accessibility

- `accessibilityRole="button"`
- `accessibilityLabel` for icon-only buttons
- Minimum 44×44px touch target

---

## Input

### Spec

- **Height**: 48px (touch-friendly)
- **Border**: 1px `--input` (unfocused), 2px `--ring` (focused)
- **Background**: `--card`
- **Border radius**: `radius-md` (8px)
- **Padding**: 12px horizontal, 0 vertical (RN TextInput handles vertical centering)
- **Font**: `body` (16px)
- **Placeholder color**: `--muted-foreground`
- **Text color**: `--foreground`

### States

| State | Border | Background |
|-------|--------|-----------|
| Default | 1px `--input` | `--card` |
| Focused | 2px `--ring` | `--card` |
| Error | 2px `--destructive` | `--card` |
| Disabled | 1px `--input` | `--muted` (opacity 0.5) |

### Helper Text

- Appears below input
- `caption` size, `--muted-foreground` normally
- `--destructive` when in error state

### Types

- `text` (default)
- `email` (auto-capitalize none, keyboard email)
- `phone` (keyboard phone-pad, format as +60)
- `password` (secure text entry)
- `multiline` (auto-growing, min 80px height, max 200px)
- `number` (decimal pad)

---

## Card

### Spec

- **Background**: `--card`
- **Border**: 1px `--border`
- **Border radius**: `radius-lg` (12px)
- **Shadow**: `shadow-sm` (light mode only), none in dark mode
- **Padding**: 16px (`space-4`)

### Variants

| Variant | Usage |
|---------|-------|
| `default` | General content containers |
| `outlined` | No shadow, just border (preferred in dark mode) |
| `elevated` | `shadow-md`, used for floating elements |
| `interactive` | Pressable card with ripple/scale feedback |

---

## Badge / Status Chip

### Spec

- **Height**: 24px
- **Padding**: 8px horizontal
- **Border radius**: `radius-full` (pill)
- **Font**: `overline` (12px, weight 500)
- **Dot indicator**: 8px circle, optional, positioned left of text

### Status Badge Colors

Uses the status color table from `01-tokens-and-colors.md`. Background + text pairs.

### Variants

| Variant | Usage |
|---------|-------|
| `default` | Status labels |
| `outline` | Bordered only, no fill |
| `dot` | With animated pulse dot for active states |

---

## Camera Capture Area

### Spec

- **Aspect ratio**: 4:5 (portrait, optimal for phone back photos)
- **Border**: 2px dashed `--accent`
- **Border radius**: `radius-xl` (16px)
- **Background**: `--muted` with 50% opacity overlay
- **Tap area**: entire container

### Corner Brackets

Four L-shaped brackets at corners to guide framing:
- Size: 24×24px each
- Stroke: 3px `--accent`
- Positioned inset 12px from edges

### States

| State | Appearance |
|-------|-----------|
| `empty` | Dashed border, camera icon centered, "Ketuk untuk ambil gambar" label |
| `preview` | Photo fills area, retake button top-right |
| `loading` | Dashed border, spinner centered, "Mengenal pasti peranti..." |
| `identified` | Photo shown, device card slides in below |

### Overlay Text

- Font: `caption-medium`
- Color: `--accent-foreground` on semi-transparent `--accent` background
- Positioned at bottom center of capture area

---

## Suggestion Chips (AI Diagnosis)

### Spec

- **Container**: Horizontal `ScrollView`, `showsHorizontalScrollIndicator={false}`
- **Gap**: 8px between chips
- **Padding**: 12px horizontal on container

### Chip Spec

- **Height**: 36px
- **Padding**: 12px horizontal
- **Border radius**: `radius-full`
- **Font**: `caption-medium` (14px)

### States

| State | Background | Text | Border |
|-------|-----------|------|--------|
| `unselected` | `--muted` | `--foreground` | none |
| `selected` | `--primary` | `--primary-foreground` | none |
| `disabled` | `--muted` (opacity 0.5) | `--muted-foreground` | none |

### Confidence Display

- Shows below or beside the chip as `overline` text
- Format: "95% keyakinan"
- Color: `--muted-foreground`

---

## Bottom Sheet

### Spec

- **Entry**: Slides up from bottom
- **Max height**: 85% of screen
- **Border radius**: `radius-xl` (16px) on top corners
- **Background**: `--card`
- **Shadow**: `shadow-xl`

### Handle Bar

- Centered at top of sheet
- Size: 36×4px
- Color: `--muted-foreground` at 30% opacity
- Border radius: `radius-full`

### Backdrop

- `rgba(0,0,0,0.4)`
- Tap to dismiss (optional, configurable)
- Fades in over 200ms

### Snap Points

| Point | Usage |
|-------|-------|
| `25%` | Confirmation dialogs, simple pickers |
| `50%` | Part selection, customer search |
| `85%` | Full form, assessment details |

---

## Offline Banner

### Spec

- **Position**: Fixed at top of screen, below safe area
- **Height**: 32px
- **Background**: `--accent` (amber) in light mode, `--accent` (darker tint) in dark mode
- **Text**: "Tiada sambungan — data disimpan secara tempatan"
- **Font**: `overline`
- **Text color**: `--accent-foreground`
- **Icon**: Cloud-off icon, 16px, left of text
- **Animation**: Slides down from top on disconnect, slides up on reconnect
- **Z-index**: Above all content

---

## FAB (Floating Action Button)

### Spec

- **Size**: 56×56px
- **Shape**: Circle (`radius-full`)
- **Background**: `--accent`
- **Icon**: Plus, 24px, `--accent-foreground`
- **Shadow**: `shadow-lg`
- **Position**: Bottom-right, 24px from edges, above tab bar

### Press Behavior

- Scale to 0.95 on press
- Haptic feedback (light impact)

---

## Avatar

### Spec

- **Sizes**: `sm` (32px), `default` (40px), `lg` (48px)
- **Shape**: Circle (`radius-full`)
- **Fallback**: Initials in `--primary-foreground` on `--primary` background
- **Font**: `caption-medium`

---

## Divider

### Spec

- **Height**: 1px
- **Color**: `--border`
- **Margin**: 16px vertical (`space-4`)
- **Optional label**: Centered text, `overline`, `--muted-foreground`

---

## Toast

### Spec

- **Position**: Top of screen (below offline banner if present)
- **Border radius**: `radius-md`
- **Padding**: 12px horizontal, 10px vertical
- **Font**: `caption-medium`
- **Duration**: 3 seconds
- **Animation**: Slide down on enter, fade out on exit

### Variants

| Variant | Background | Text | Icon |
|---------|-----------|------|------|
| `default` | `--card` | `--foreground` | Info |
| `success` | `--secondary` | `--secondary-foreground` | Check |
| `error` | `--destructive` | `--destructive-foreground` | Alert |
| `warning` | `--accent` | `--accent-foreground` | Alert-triangle |

---

## Loading Skeleton

### Spec

- **Shape**: Rounded rectangles matching content shape
- **Color**: `--muted`
- **Animation**: Shimmer gradient sweep left to right, 1.5s loop
- **Used for**: Ticket list items while loading, AI results pending

---

## Empty State

### Spec

- **Layout**: Centered vertically and horizontally
- **Icon**: 64px, `--muted-foreground`
- **Title**: `heading-3`, `--foreground`
- **Description**: `body`, `--muted-foreground`
- **Action**: Optional `Button` below, `outline` variant
- **Padding**: 48px horizontal

---

## Quick Repair Card

### Spec

Used in the Quick Repair screen to display the issue quick-select options.

- **Background**: `--card`
- **Border**: 1px `--border`
- **Border radius**: `radius-lg` (12px)
- **Padding**: 16px
- **Width**: full width

### States

| State | Border | Indicator |
|-------|--------|-----------|
| `unselected` | 1px `--border` | none |
| `selected` | 2px `--primary` | 4px left accent `--primary` |
| `pressed` | 2px `--primary` | opacity 0.95 |

### Content Layout
```
┌──────────────────────────────────────┐
│ ┌──────────────────────────────────┐ │
│ │ 📋 Penukaran Skrin OLED          │ │
│ │    RM350 | 0.75 jam              │ │
│ │                                  │ │
│ │ Bahagian: Skrin OLED ×1          │ │
│ │           Gam Perekat  ×1        │ │
│ └──────────────────────────────────┘ │
└──────────────────────────────────────┘
```

- Issue name: `body-medium`, `--foreground`
- Price + time: `caption`, `--muted-foreground`
- Parts summary: `overline`, `--muted-foreground`, collapsed by default, expands on tap

---

## Voice Input Button

### Spec

- **Size**: 80×80px (main), 48×48px (inline/mini)
- **Shape**: Circle (`radius-full`)
- **Background**: `--primary` (idle), `--destructive` (recording)
- **Icon**: Microphone, 32px (main), 20px (mini), `--primary-foreground` / `--destructive-foreground`
- **Shadow**: `shadow-lg` (main), none (mini)

### States

| State | Background | Animation | Label |
|-------|-----------|-----------|-------|
| `idle` | `--primary` | none | "Ketuk untuk rakam" |
| `recording` | `--destructive` | Red pulse ring (scale 1→1.1, opacity 0.6→0, 1s loop) | Timer + waveform |
| `processing` | `--muted` | Spinner overlay | "Mengenal pasti..." |
| `result` | `--secondary` | none | Confirmation card slides in |

### Recording UI
- Timer: `mono`, top-right of button area, "00:04"
- Waveform: horizontal bars below button, `--primary`, 24 bars, dynamic height
- Max duration: 30 seconds (auto-stops)
- Tap to stop (manual stop)

### Mini Variant (inline in header / form)
- 48×48px, `outline` button style
- Used in: Quick Repair header, New Ticket customer notes section
- Same mic icon, no pulse animation (just recording state indicator dot)

---

## Payment Method Selector

### Spec

- **Layout**: Horizontal row of 3 options, equal width
- **Gap**: 8px

### Option Card

- **Size**: flexible width, 80px height
- **Background**: `--muted` (unselected), `--primary` (selected)
- **Border**: 1px `--border` (unselected), 2px `--primary` (selected)
- **Border radius**: `radius-md` (8px)
- **Icon**: 24px, centered top
- **Label**: `caption-medium`, centered below icon
- **Text color**: `--foreground` (unselected), `--primary-foreground` (selected)

### Icons
- Tunai: 💵 (Banknote icon, Lucide)
- QR Pay: 📱 (Smartphone icon, Lucide)
- Bank Transfer: 🏦 (Building icon, Lucide)

### Press Behavior
- Scale 0.95, haptic feedback (light impact)
- Radio-style: selecting one deselects others

---

## Receipt Card

### Spec

Displayed in success states (WhatsApp receipt preview) and in the payment screen price recap.

- **Background**: `--card`
- **Border**: 1px `--border`
- **Border radius**: `radius-lg`
- **Padding**: 16px
- **Font**: `mono` for amounts, `body` for labels

### Layout
```
┌────────────────────────────────────┐
│ 🔧 Repair Intake                  │
│                                    │
│ Tiket #D1-043                      │
│ iPhone 14 Pro                     │
│ Penukaran Skrin OLED              │
│                                    │
│ Bahagian (1):      RM 339.00      │
│ Upah (0.75j):      RM  11.00      │
│                     ────────      │
│ Jumlah:            RM 350.00      │
│                                    │
│ Kaedah: QR Pay                     │
│ Cawangan: Dungun 1                │
│ Tarikh: 03 Jun 2026, 3:30 PM      │
│                                    │
│ Terima kasih!                     │
└────────────────────────────────────┘
```

---

## QR Code Component

### Spec

- **Size**: 200×200px (default), can scale down to 160×160px for inline
- **Background**: White (`#FFFFFF`) with 16px padding
- **Border radius**: `radius-md`
- **Border**: 1px `--border`
- **Centered** in container

### Loading State
- Placeholder with spinner while QR is generated
- Text: "Menjana kod QR..."

### Generated By
- QR code encodes: `https://repairintake.my/t/{qr_token}`
- Generated on server side at ticket creation (or on first access to receipt screen)
- Stored as `qr_token` on ticket

### Context Text
- Above QR: "Imbas untuk kemaskini WhatsApp"
- Below QR: outlet name and address
- Font: `caption`, `--muted-foreground`
