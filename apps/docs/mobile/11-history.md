# Feature: History (Sejarah)

> **Screen-level UI spec only.**  
> For design tokens: `design-system/01-tokens-and-colors.md`  
> For reusable components: `design-system/02-components.md`

---

## Screen: History (`(app)/history.tsx`)

### Purpose

View past (completed) tickets. Search by ticket number, customer name, or device model. Filter by date range, status, and outlet.

### Layout

- `ScrollView` or `FlatList`.
- Search bar at top (sticky).
- Filter chips row below search.
- Ticket list same compact cards as Jobs Queue.
- Empty state when no results.

---

## Section 1: Search Bar

### Position

Sticky at top of screen, below header.

### Appearance

- Search icon (`Search`, Lucide) on left, inside input.
- Placeholder: "Cari tiket, pelanggan, atau peranti" (`body`, `--muted-foreground`)
- Clear button (X): appears when text entered. 24px circle, `--muted-foreground`.
- Background: `--card`, border 1px `--border`, radius `radius-lg` (12px).
- Height: 44px.

### Behavior

- Debounced search: 300ms after last keystroke.
- Results update in real-time (locally filtered if data cached, or API call if paginated).
- Haptic: light impact on first result load.

---

## Section 2: Filter Chips

### Layout

Horizontally scrollable row, same style as Jobs Queue filter chips.

### Chips

| Chip     | Options                                | Default        |
| -------- | -------------------------------------- | -------------- | ------------ | --------- | -------------- | -------- |
| Tarikh   | `Hari ini`                             | `Semalam`      | `7 hari`     | `30 hari` | `Pilih tarikh` | `7 hari` |
| Status   | `Semua`                                | `Selesai`      | `Dibatalkan` | `Dibayar` | `Diambil`      | `Semua`  |
| Cawangan | Outlet names (managers/owners see all) | Current outlet |

- `Pilih tarikh`: opens native date picker (iOS) or calendar modal (custom).
- Chip selected: filled `--primary`, text `--primary-foreground`.
- Chip unselected: outlined, text `--foreground`.

---

## Section 3: Ticket List

### Same as Jobs Queue

- Compact cards, ~80px height.
- Same content: ticket number, device model, customer name, notes snippet, time ago, status badge, chevron.
- Same swipe actions: swipe right = "Buka", swipe left = "Arkib" (archive).

### Date Grouping (Optional)

- Tickets grouped by date with sticky section headers.
- Header: "Hari ini", "Semalam", "02 Jun 2026", etc.
- Header style: `caption-medium`, `--muted-foreground`, bg `--background`, padding 8px 16px.

---

## Empty State

- **Icon**: `History` (Lucide), 64px, `--muted-foreground`
- **Title**: "Tiada sejarah lagi" (`heading-3`)
- **Description**: "Tiket yang selesai akan muncul di sini" (`body`, `--muted-foreground`)
- No action button needed (passive screen).

---

## Pull-to-Refresh

- Same as Jobs Queue: standard iOS pull gesture.
- Spinner color: `--primary`.

---

## Design Token References

| Element                       | Token                  |
| ----------------------------- | ---------------------- |
| Search bar bg                 | `--card`               |
| Search bar border             | `--border`             |
| Search placeholder            | `--muted-foreground`   |
| Filter chip selected bg       | `--primary`            |
| Filter chip selected text     | `--primary-foreground` |
| Filter chip unselected border | `--border`             |
| Filter chip unselected text   | `--foreground`         |
| List card bg                  | `--card`               |
| List card border              | `--border`             |
| Date header text              | `--muted-foreground`   |
| Date header bg                | `--background`         |
| Empty state icon              | `--muted-foreground`   |

---

## Components Used

- `SearchBar` — sticky search input with debounce
- `FilterChip` — horizontal scrollable pills
- `TicketListItem` — same compact card as Jobs Queue
- `EmptyState` — icon + title + description
- `DatePicker` / `CalendarModal` — custom date range selection

---

## Related Specs

- Jobs queue (same list component): `03-jobs-queue.md`
- Ticket Detail (list tap): `05-ticket-detail.md`
- Navigation shell (tab bar): `02-navigation-shell.md`
