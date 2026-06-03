# Design Tokens & Colors

## Theme Overview

We use the **"Sharper Pop"** theme (from [Tweakcn](https://tweakcn.com/themes/cmkcjz6s0000104l574rqdux5)) as the brand foundation, with a **"Shop Edition"** softening for mobile staff use. The mobile app is used 8+ hours/day under Malaysian shop fluorescent lighting, so we reduce eye strain while keeping brand identity strong on CTAs and badges.

**Web dashboard** uses the original bold theme. **Mobile app** uses the softened variant below.

## Fonts

- **Sans**: `DM Sans` (Google Fonts) — weights 400, 500, 600, 700
- **Mono**: `Space Mono` (Google Fonts) — weight 400

Both are loaded via `expo-font` at app startup. Fallback to system fonts if load fails.

---

## Color Tokens

### Light Mode — Mobile (Softened)

| Token | Hex | Usage |
|-------|-----|-------|
| `--background` | `#f7f9f3` | App background, subtle green tint |
| `--foreground` | `#111827` | Primary text (softened from pure black) |
| `--card` | `#ffffff` | Card surfaces |
| `--card-foreground` | `#111827` | Text on cards |
| `--popover` | `#ffffff` | Bottom sheets, dropdowns |
| `--popover-foreground` | `#111827` | Text in popovers |
| `--primary` | `#4f46e5` | Main CTAs, active states, links |
| `--primary-foreground` | `#ffffff` | Text on primary buttons |
| `--secondary` | `#14b8a6` | Secondary actions, success hints |
| `--secondary-foreground` | `#ffffff` | Text on secondary buttons |
| `--muted` | `#f3f4f6` | Subtle backgrounds, unselected chips |
| `--muted-foreground` | `#6b7280` | Secondary text, placeholders, captions |
| `--accent` | `#f59e0b` | Highlights, FABs, warnings, new ticket button |
| `--accent-foreground` | `#000000` | Text on accent buttons |
| `--destructive` | `#ef4444` | Errors, cancel, delete |
| `--destructive-foreground` | `#ffffff` | Text on destructive buttons |
| `--border` | `#e5e7eb` | Card borders, input borders, dividers |
| `--input` | `#d1d5db` | Input field borders (focused: `--ring`) |
| `--ring` | `#a5b4fc` | Focus rings, selected borders |
| `--sidebar` | `#f7f9f3` | Sidebar/nav background (if any) |
| `--sidebar-foreground` | `#111827` | Sidebar text |
| `--sidebar-primary` | `#4f46e5` | Sidebar active item |
| `--sidebar-primary-foreground` | `#ffffff` | Sidebar active text |
| `--sidebar-accent` | `#f59e0b` | Sidebar accent |
| `--sidebar-accent-foreground` | `#000000` | Sidebar accent text |
| `--sidebar-border` | `#e5e7eb` | Sidebar divider |
| `--sidebar-ring` | `#a5b4fc` | Sidebar focus ring |

### Dark Mode — Mobile (Softened)

| Token | Hex | Usage |
|-------|-----|-------|
| `--background` | `#0f1115` | **Lifted from pure black** for less eye strain |
| `--foreground` | `#ffffff` | Primary text |
| `--card` | `#181b20` | Slightly lifted from `#1a212b` for depth |
| `--card-foreground` | `#ffffff` | Text on cards |
| `--popover` | `#1c1f24` | Consistent with card lift |
| `--popover-foreground` | `#ffffff` | Text in popovers |
| `--primary` | `#818cf8` | Lighter indigo for dark readability |
| `--primary-foreground` | `#000000` | Text on primary buttons |
| `--secondary` | `#2dd4bf` | Brighter teal |
| `--secondary-foreground` | `#000000` | Text on secondary buttons |
| `--muted` | `#333333` | Subtle backgrounds |
| `--muted-foreground` | `#cccccc` | Secondary text |
| `--accent` | `#fcd34d` | Yellow for dark mode contrast |
| `--accent-foreground` | `#000000` | Text on accent buttons |
| `--destructive` | `#f87171` | Light red for dark mode |
| `--destructive-foreground` | `#000000` | Text on destructive buttons |
| `--border` | `#374151` | Softer than `#545454` |
| `--input` | `#4b5563` | Proper input border |
| `--ring` | `#818cf8` | Focus ring matches primary |
| `--sidebar` | `#0f1115` | Sidebar background |
| `--sidebar-foreground` | `#ffffff` | Sidebar text |
| `--sidebar-primary` | `#818cf8` | Sidebar active |
| `--sidebar-primary-foreground` | `#000000` | Sidebar active text |
| `--sidebar-accent` | `#fcd34d` | Sidebar accent |
| `--sidebar-accent-foreground` | `#000000` | Sidebar accent text |
| `--sidebar-border` | `#ffffff` | Sidebar divider |
| `--sidebar-ring` | `#818cf8` | Sidebar focus ring |

### Chart Colors (Light & Dark)

| Token | Light | Dark |
|-------|-------|------|
| `--chart-1` | `#4f46e5` | `#818cf8` |
| `--chart-2` | `#14b8a6` | `#2dd4bf` |
| `--chart-3` | `#f59e0b` | `#fcd34d` |
| `--chart-4` | `#ec4899` | `#f472b6` |
| `--chart-5` | `#22c55e` | `#4ade80` |

---

## Status Colors (Ticket States)

Each status has a **background + text** pair. Backgrounds are used for badges; text for labels.

| Status | Light BG | Light Text | Dark BG | Dark Text |
|--------|----------|-----------|---------|-----------|
| `received` | `#e0e7ff` | `#4f46e5` | `#1e1b4b` | `#818cf8` |
| `assessing` | `#ccfbf1` | `#14b8a6` | `#134e4a` | `#2dd4bf` |
| `assessed` | `#fef3c7` | `#f59e0b` | `#451a03` | `#fcd34d` |
| `approved` | `#dcfce7` | `#22c55e` | `#14532d` | `#4ade80` |
| `in_progress` | `#dbeafe` | `#3b82f6` | `#1e3a8a` | `#60a5fa` |
| `completed` | `#f3e8ff` | `#a855f7` | `#581c87` | `#c084fc` |
| `cancelled` | `#fee2e2` | `#ef4444` | `#7f1d1d` | `#f87171` |
| `picked_up` | `#f0fdf4` | `#15803d` | `#14532d` | `#86efac` |

---

## Typography Scale

Base: `1rem = 16px`

| Name | Size | Weight | Line Height | Usage |
|------|------|--------|-------------|-------|
| `display` | 32px / 2rem | 700 | 1.2 | Empty states, large metrics |
| `heading-1` | 24px / 1.5rem | 700 | 1.3 | Screen titles |
| `heading-2` | 20px / 1.25rem | 600 | 1.35 | Section headers |
| `heading-3` | 18px / 1.125rem | 600 | 1.4 | Card titles |
| `body` | 16px / 1rem | 400 | 1.5 | Primary text, inputs |
| `body-medium` | 16px / 1rem | 500 | 1.5 | Emphasized body |
| `caption` | 14px / 0.875rem | 400 | 1.5 | Secondary text, descriptions |
| `caption-medium` | 14px / 0.875rem | 500 | 1.5 | Button text, labels, badges |
| `overline` | 12px / 0.75rem | 500 | 1.4 | Status labels, timestamps |
| `mono` | 14px / 0.875rem | 400 | 1.3 | Ticket numbers, IMEI, prices |

---

## Spacing Scale

Base unit: `4px` (`0.25rem`)

| Token | Value | Usage |
|-------|-------|-------|
| `space-0` | 0px | — |
| `space-1` | 4px | Icon gaps, inline spacing |
| `space-2` | 8px | Tight padding, badge gaps |
| `space-3` | 12px | Button padding horizontal, small gaps |
| `space-4` | 16px | Card padding, section gaps |
| `space-5` | 20px | Medium section padding |
| `space-6` | 24px | Large card padding, screen edge padding |
| `space-8` | 32px | Section separators, modal padding |
| `space-10` | 40px | Large vertical gaps |
| `space-12` | 48px | Between major sections |
| `space-16` | 64px | Empty state vertical centering |

---

## Radius Scale

| Token | Value | Usage |
|-------|-------|-------|
| `radius-sm` | 6px | Buttons, badges, small inputs |
| `radius-md` | 8px | Inputs, medium cards |
| `radius-lg` | 12px | Large cards, modals |
| `radius-xl` | 16px | Camera preview, bottom sheets |
| `radius-2xl` | 20px | Hero cards, feature blocks |
| `radius-full` | 9999px | Pills, avatars, chips, FAB |

---

## Shadow Scale

Light mode only — dark mode relies on borders for elevation.

| Token | Value |
|-------|-------|
| `shadow-sm` | `0 1px 2px 0 rgba(0,0,0,0.05)` |
| `shadow-md` | `0 4px 6px -1px rgba(0,0,0,0.07)` |
| `shadow-lg` | `0 10px 15px -3px rgba(0,0,0,0.08)` |
| `shadow-xl` | `0 20px 25px -5px rgba(0,0,0,0.08)` |

---

## Touch Targets

- **Minimum**: 44×44px (Apple HIG)
- **Buttons**: 48px height (default), 56px (large CTA)
- **List items**: 56px minimum height
- **Input fields**: 48px height
- **Icon buttons**: 44×44px
- **Spacing between tappable elements**: min 8px

---

## Safe Area & Layout

- Respect `SafeAreaView` on all screens
- Screen horizontal padding: 16px (`space-4`)
- Content max width on iPad: 480px (centered)
- Keyboard avoiding: `KeyboardAvoidingView` with `behavior="padding"` on all forms
- Bottom sheet max height: 85% of screen
