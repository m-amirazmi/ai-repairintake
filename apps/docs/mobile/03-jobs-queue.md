# Feature: Jobs Queue (Tab 1 — Home / Tugas)

> **Screen-level UI spec only.**  
> For design tokens: `design-system/01-tokens-and-colors.md`  
> For reusable components: `design-system/02-components.md`

---

## Screen: Jobs Queue (`(app)/index.tsx`)

### Purpose

Primary work surface. Shows today's repair tickets with quick filtering. This is the screen users see immediately after login.

### Layout

- **Header** (sticky, top of screen):
  - Left: Hamburger menu icon (☰) — opens drawer or side menu (Month 3+).
  - Center: "Senarai Tugas" (`heading-2`)
  - Right: Notification bell icon with badge if unread notifications > 0.
- **Outlet Selector**: Below header, tap to open outlet switcher (managers/owners only; front desk/technicians see their assigned outlet as read-only label).
  - Text: "Dungun 1" with chevron-down icon.
  - Style: `caption`, `--primary` color, row with icon.
- **Filter Chips Row**: Horizontally scrollable.
  - Chips: `Semua` | `Diterima` | `Sedang Dinilai` | `Selesai`
  - Selected: filled `--primary`, text `--primary-foreground`, `pill` shape
  - Unselected: outlined, text `--foreground`, `pill` shape
  - Chip height: 32px, horizontal padding 12px
  - Gap between chips: 8px
- **Ticket List**: Vertical `ScrollView` or `FlatList`.
  - Pull-to-refresh enabled.
  - Empty state when no tickets match filter.

---

## Ticket List Item (Compact Card)

### Card Layout

```
┌─────────────────────────────────────────┐
│ #D1-042                        10m lalu │
│ iPhone 14 Pro         [Badge: Diterima] │
│ Ahmad bin Abdullah                      │
│ "Skrin pecah, sentuh rosak"             │
│                                     [▶] │
└─────────────────────────────────────────┘
```

### Dimensions & Styling

- **Card height**: ~80px (compact density)
- **Horizontal padding**: 16px
- **Vertical padding**: 12px
- **Background**: `--card`
- **Border**: 1px solid `--border` (subtle)
- **Border radius**: `radius-lg` (12px)
- **Margin bottom**: 8px between cards
- **Active press**: Scale to 0.98 + `--primary` at 5% opacity background overlay

### Content Structure

**Left side** (majority of width):

- Ticket number: `mono` font, `--primary` color, `caption` size
- Device model: `body`, `--foreground`
- Customer name: `caption`, `--muted-foreground`
- Notes snippet: `overline`, `--muted-foreground`, max 1 line, truncate with "..."

**Right side** (aligned right):

- Time ago: `overline`, `--muted-foreground` (e.g., "10m lalu", "1j", "5j")
- Status badge: uses `StatusBadge` component (see `02-components.md`)
- Chevron (▶): `muted-foreground`, 16px

### Status Badge Colors

| Status        | Badge Text (BM) | Badge BG                    | Badge Text Color     |
| ------------- | --------------- | --------------------------- | -------------------- |
| `RECEIVED`    | Diterima        | `--primary` at 15%          | `--primary`          |
| `ASSESSING`   | Sedang Dinilai  | `--warning` at 15%          | `--warning`          |
| `ASSESSED`    | Selesai Dinilai | `--secondary` at 15%        | `--secondary`        |
| `APPROVED`    | Diluluskan      | `--success` at 15%          | `--success`          |
| `IN_PROGRESS` | Dalam Proses    | `--accent` at 15%           | `--accent`           |
| `COMPLETED`   | Siap            | `--secondary` at 15%        | `--secondary`        |
| `PAID`        | Dibayar         | `--success` at 15%          | `--success`          |
| `PICKED_UP`   | Diambil         | `--muted-foreground` at 15% | `--muted-foreground` |
| `CANCELLED`   | Dibatalkan      | `--destructive` at 15%      | `--destructive`      |

### Swipe Actions

| Direction   | Action                                    | Condition                   |
| ----------- | ----------------------------------------- | --------------------------- |
| Swipe right | "Buka" (Open) — push to Ticket Detail     | Always                      |
| Swipe left  | "Hantar ke Teknikal" (Send to Technician) | Only if status = `RECEIVED` |
| Swipe left  | "Arkib" (Archive)                         | Optional, Month 3+          |

- Swipe action buttons: 72px wide, icon + label stacked vertically.
- "Buka" button: bg `--primary`, text `--primary-foreground`.
- "Hantar ke Teknikal" button: bg `--accent`, text `--accent-foreground`.
- Haptic: medium impact on swipe reveal.

### Tap Behavior

- Tap card → push to Ticket Detail (`ticket/[id].tsx`).
- Haptic: light impact.

---

## Empty State

- **Icon**: `ClipboardList` (Lucide), 64px, `--muted-foreground`
- **Title**: "Tiada tugas hari ini" (`heading-3`)
- **Description**: "Semua tiket telah diselesaikan. Rehat sebentar!" (`body`, `--muted-foreground`)
- **Action**: `[Tiket Baharu]` — `primary` button, `md` size
- **Alignment**: Centered vertically and horizontally in available space.

---

## Pull-to-Refresh

- Standard iOS pull gesture.
- Spinner color: `--primary`.
- On refresh: re-fetch ticket list.
- **Month 3 upgrade**: Supabase Realtime subscription replaces pull-to-refresh as primary update mechanism. Pull-to-refresh remains as manual fallback.

---

## Real-Time Queue (Month 3)

- New tickets animate in at the top of the list with a subtle slide-down + fade-in (300ms).
- Status badge color transitions animate smoothly (200ms).
- "New ticket" toast appears at bottom: "Tiket baharu: #D1-043 — iPhone 14 Pro".

---

## Filter Logic

| Filter Chip      | Shows Tickets With Status          |
| ---------------- | ---------------------------------- |
| `Semua`          | All non-archived tickets for today |
| `Diterima`       | `RECEIVED`                         |
| `Sedang Dinilai` | `ASSESSING`, `ASSESSED`            |
| `Selesai`        | `COMPLETED`, `PAID`, `PICKED_UP`   |

- Default filter: `Semua`.
- Filter state persists per session (not persisted to storage).
- "Today" defined as tickets created after 00:00 of current day in tenant timezone.

---

## Outlet Selector (Managers/Owners)

- Tap outlet name → opens bottom sheet with list of outlets.
- Each row: outlet name, address snippet, radio button (selected outlet has filled `--primary` circle).
- Bottom sheet height: ~50% of screen.
- Dismiss: swipe down or tap backdrop.
- On selection: list refreshes with new outlet's tickets.

---

---

## Design Token References

| Element                              | Token                  |
| ------------------------------------ | ---------------------- |
| List card bg                         | `--card`               |
| List card border                     | `--border`             |
| Ticket number text                   | `--primary`            |
| Time ago / notes                     | `--muted-foreground`   |
| Header bg                            | `--background`         |
| Filter chip selected bg              | `--primary`            |
| Filter chip selected text            | `--primary-foreground` |
| Filter chip unselected border        | `--border`             |
| Filter chip unselected text          | `--foreground`         |
| Empty state icon                     | `--muted-foreground`   |
| Swipe action "Buka" bg               | `--primary`            |
| Swipe action "Hantar ke Teknikal" bg | `--accent`             |

---

## Components Used

- `TicketListItem` — compact card (custom, defined in this spec)
- `StatusBadge` — status color chip
- `FilterChip` — horizontal scrollable filter pills
- `EmptyState` — icon + title + description + action
- `HomeButton` — quick navigation to Home tab
- `BottomSheet` — outlet selector

---

## Related Specs

- Navigation shell (tab bar): `02-navigation-shell.md`
- Home (Tab 1, entry point): `03-home-intake.md`
- New Ticket (Home card): `04-new-ticket-full-ai.md`
- Ticket Detail (list tap): `05-ticket-detail.md`
- Quick Repair (Home card): `07-quick-repair.md`
- Voice Intake (Quick Repair mic): `08-voice-intake.md`
- Profile (Tab 5): `10-profile-settings.md`
- History (Tab 4): `11-history.md`
