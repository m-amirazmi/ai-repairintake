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
