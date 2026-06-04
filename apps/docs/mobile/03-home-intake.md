# 03-home-intake.md
# Home / Intake Landing — Screen Specification (Mobile)

> **File**: `mobile/03-home-intake.md`
> **Type**: Functional Specification (Flow Entry / Root Tab)
> **Audience**: Mobile Engineers, Product, QA, UX
> **Status**: Draft / Ready for Development

---

## 1. Overview

The **Home / Intake Landing** screen is the default screen shown immediately after login for all user roles. It serves as the central hub from which all three primary repair-intake flows (New Ticket → AI Diagnosis, Assessment, Quick Repair) are launched. It also provides at-a-glance operational metrics and a preview of recent activity.

### Purpose
- **Primary**: Give users a single-screen launchpad for all repair-intake workflows.
- **Secondary**: Surface KPIs, recent ticket context, and common actions to reduce cognitive load.
- **Tertiary**: Establish and persist the "common flow" partner/branch selection so downstream screens have correct routing and access context.

### Entry Points
- **`Login → Success`** — routed here after successful authentication and role check.
- **`Background Resume`** — returns here when the app is resumed from background (unless deep-linking to a specific ticket).

### Exit Points
| Action | Target Screen |
|--------|--------------|
| Tap "Create Ticket" card | `New Ticket` |
| Tap "Assessment" card | `Assessment` |
| Tap "Quick Repair" card | `Quick Repair` |
| Tap "View All" (Recent Tickets) | `Jobs Queue` |
| Tap a recent ticket item | `Ticket Detail` |
| Bottom nav: Tugas | `Jobs Queue` |
| Bottom nav: Nilai | `Assessment` |
| Bottom nav: Sejarah | `History` |
| Bottom nav: Profil | `Profile` |

### Screen Relationships
- **Parent**: Root of the Common Flow — no parent, reached via bottom nav or post-login.
- **Children**: Any screen launched from the action cards or recent items.
- **Siblings**: All other tab screens (`Jobs Queue`, `Assessment`, `History`, `Profile`).

### Role-Based Visibility
| Role | Partner Select | All Action Cards | Stats Cards | Recent Tickets |
|------|----------------|------------------|-------------|----------------|
| Staff (teknikal) | ✅ Yes | ✅ All 3 | ✅ Yes | ✅ Yes |
| Kasir | ✅ Yes | ✅ All 3 (readonly) | ✅ Yes | ✅ Yes |
| Admin | ✅ Yes | ✅ All 3 | ✅ Yes | ✅ Yes |
| Partner | ❌ No (locked to own partner) | ✅ All 3 | ✅ Yes | ✅ Yes |

> **Note**: The `Nilai` (Assessment) tab is always visible in the bottom nav, so the Assessment card on Home is a convenience duplicate — it navigates to the same screen.

---

## 2. Screen Layout & Wireframe

```
+------------------------------------------------------------------+
|                        Status Bar                               |
| (time, battery, connectivity, Bluetooth, location)               |
+------------------------------------------------------------------+
|  Assalamualaikum, Zainab!              [Bell icon]              |
|  Teknisi, Revive IT                                               |
+------------------------------------------------------------------+
|                                                                   |
|  Total Tiket      Tertunggu         Selesai       Hasil Hari Ini|
|  [      156    ] [     23    ] [    133   ] [   RM 2,400   ]     |
|                                                                   |
+------------------------------------------------------------------+
|                                                                   |
|  +-----------------------+  +-----------------------+            |
|  |     Create Ticket      |  |     Assessment       |            |
|  |   +                 |  |   +                 |            |
|  |   Buat tiket baru    |  |   Penilaian kerosakan |            |
|  +-----------------------+  +-----------------------+            |
|                                                                   |
|  +-----------------------+                                      |
|  |     Quick Repair       |                                      |
|  |   +                 |                                      |
|  |   Baiki cepat        |                                      |
|  +-----------------------+                                      |
|                                                                   |
+------------------------------------------------------------------+
|  Tiket Terkini                                     [Lihat Semua]|
|                                                                   |
|  +---------------------------------------------------------+    |
|  | Ticket #A1934    iPhone 12 Pro  |  RM 280  | Selesai   |    |
|  +---------------------------------------------------------+    |
|  | Ticket #A1929    Samsung S23   |  RM 150  | Dalam Tini|    |
|  +---------------------------------------------------------+    |
|  | Ticket #A1921    Huawei P50    |  RM 320  | Menunggu  |    |
|  +---------------------------------------------------------+    |
|                                                                   |
+------------------------------------------------------------------+
|                                                                   |
|  [ Utama ]  [ Tugas ]  [ Nilai ]  [ Sejarah ]  [ Profil ]      |
|  (active)                                                        |
+------------------------------------------------------------------+
|                        Home Indicator                           |
+------------------------------------------------------------------+
```

---

## 3. Component Inventory

### 3.1 Header Section

| Component | Type | Purpose |
|-----------|------|---------|
| Welcome Text | `Text` | Personalized greeting: "Assalamualaikum, {firstName}!" |
| Role & Partner Badge | `Text` | Shows role and partner name on second line (e.g., "Teknisi, Revive IT") |
| Notification Bell | `IconButton` | Opens notification drawer / list |

**States & Variants**
| Variant | Trigger | Visual |
|---------|---------|--------|
| Default | Normal load | Welcome text + role badge + bell icon |
| Notification Badge | Unread notifications > 0 | Red dot (8 px) on top-right of bell icon |

**Accessibility**
- Welcome text: `accessibilityLabel="Welcome, {firstName}"`
- Bell icon: `accessibilityLabel="Notifications, {count} unread"`

---

### 3.2 Stats Row

| Component | Type | Purpose |
|-----------|------|---------|
| Total Tickets | `StatCard` | Total lifetime / filtered tickets for selected partner |
| Pending | `StatCard` | Count of tickets in `menunggu` or `dalam_tindakan` status |
| Completed | `StatCard` | Count of tickets in `selesai` or `ditolak` status |
| Today's Revenue | `StatCard` | Sum of `total_price` for tickets with `updated_at >= today 00:00` |

**Visual Spec**
- 4-column grid, equal width, 8 px gap
- Background: `--color-surface-variant` with 1 px `--color-divider` border
- Value: 24 px bold (--font-body-large, --font-weight-bold)
- Label: 11 px, --color-on-surface-variant, uppercase
- Revenue value: prefix "RM "

**Data Source**
```
GET /api/v1/tickets/stats?partner_id={selectedPartnerId}
```

**States & Variants**
| Variant | Trigger | Visual |
|---------|---------|--------|
| Loading | `stats === null` | Skeleton pulse on all 4 cards |
| Empty | All values = 0 | "0" shown plainly (no special empty state) |
| Error | API failure | Error icon on card, tap to retry |
| Selected Partner Change | User switches partner | Smooth count animation to new values |

**Accessibility**
- Each card: `accessibilityLabel="{label}: {value}"` (e.g., "Total Tickets: 156")

---

### 3.3 Flow Action Cards

Three large tappable cards that branch into the three primary repair flows.

| Component | Type | Purpose |
|-----------|------|---------|
| Create Ticket Card | `ActionCard` | Launches New Ticket flow (common flow A) |
| Assessment Card | `ActionCard` | Launches Assessment flow (common flow B) |
| Quick Repair Card | `ActionCard` | Launches Quick Repair flow (common flow C) |

**Visual Spec**
- Full width, 16 px horizontal padding
- Height: 96 px each
- Background: `--color-surface`
- Border: 1 px `--color-divider`
- Border-radius: 12 px
- Icon: 40 px, placed left, color = flow accent color
- Title: 16 px, --font-weight-semi-bold
- Subtitle: 12 px, --color-on-surface-variant
- Chevron right (→) on far right, --color-on-surface-variant

**Color Coding**
| Card | Icon Color | Icon Name |
|------|-----------|-----------|
| Create Ticket | --color-primary | `plus-circle` |
| Assessment | --color-secondary | `clipboard-list` |
| Quick Repair | --color-tertiary | `zap` |

**Tap Behavior**
- Haptic: `Haptics.selection()` (light)
- Navigation: Push to respective screen via `navigate()`

**Accessibility**
- Card: `accessibilityRole="button"`, `accessibilityLabel="{title}. {subtitle}"`

---

### 3.4 Recent Tickets Preview

| Component | Type | Purpose |
|-----------|------|---------|
| Section Title | `Text` | "Tiket Terkini" |
| View All Link | `TextButton` | "Lihat Semua" → navigates to Jobs Queue |
| Ticket Item | `ListItem` | Compact preview of recent tickets |

**Visual Spec**
- Section padding: 16 px horizontal, 24 px top
- Ticket items: full width, 48 px height, 12 px padding
- Left: ticket ID (mono) + device model (truncated to 20 chars)
- Right: price (RM) + status chip (mini variant)
- Divider: 0.5 px `--color-divider` between items
- Max 5 items shown; "Lihat Semua" only shown if total > 5

**Data Source**
```
GET /api/v1/tickets?partner_id={selectedPartnerId}&limit=5&sort=-updated_at
```

**States & Variants**
| Variant | Trigger | Visual |
|---------|---------|--------|
| Loading | Initial load | Skeleton: 5 rows of placeholder |
| Empty | No tickets for partner | Empty state illustration + "Tiada tiket terkini" |
| Error | API failure | Inline error with retry button |

**Accessibility**
- Ticket item: `accessibilityLabel="Ticket {id}, {device}, {status}, {price}"`

---

### 3.5 Partner Selector (Common Flow Context)

A persistent, non-intrusive partner/branch selector that establishes the routing context for all flows launched from this screen.

| Component | Type | Purpose |
|-----------|------|---------|
| Partner Chip | `Chip` | Displays currently selected partner; tappable to open selector |

**Visual Spec**
- Placed inline after the role badge in the header area
- Chip style: outlined, small, with dropdown chevron
- Background: `--color-surface-variant`

**Behavior**
- Tap: Opens bottom sheet with partner list
- On selection: Updates `commonFlowPartnerId` in global state
- All downstream API calls and stats automatically re-fetch

**Data Source**
```
GET /api/v1/partners?accessible=true
```

**States & Variants**
| Variant | Trigger | Visual |
|---------|---------|--------|
| Single Partner | User has access to only 1 partner | Chip is non-interactive, no chevron |
| Multiple Partners | User has access to >1 partner | Chip is tappable, shows chevron |
| Loading | Partners list loading | Chip shows skeleton |

> **Note**: The partner selector on Home sets the `commonFlowPartnerId`. This ID is persisted in local state and used by all flow screens that don't have their own explicit partner selection step.

---

## 4. Detailed Behavior & Logic

### 4.1 Lifecycle & State Management

#### Screen Lifecycle
```
Mount → Load user info + partner context → Fetch stats → Fetch recent tickets
```

#### State Requirements

**Screen-Local State**
```
- stats: TicketStats | null
- recentTickets: Ticket[] | null
- isLoading: boolean
- isRefreshing: boolean
- selectedPartnerId: string | null (mirrors global commonFlowPartnerId)
```

**Global State Dependencies**
- `auth.user` — for welcome text and role badge
- `commonFlowPartnerId` — for partner context
- `notifications.unreadCount` — for bell badge

---

### 4.2 Business Logic & Rules

#### Stats Calculation Rules
- **Total Tickets**: Count of all tickets where `partner_id === selectedPartnerId` (no date filter)
- **Pending**: Count where `status IN ('menunggu', 'dalam_tindakan', 'sedang_dinilaikan')`
- **Completed**: Count where `status IN ('selesai', 'ditolak', 'dibatalkan')`
- **Today's Revenue**: Sum of `total_price` where `updated_at >= startOfDay(localTime)` AND `status === 'selesai'`

#### Recent Tickets Ordering
- Primary sort: `updated_at DESC` (most recently updated first)
- If `updated_at` is equal, secondary sort: `created_at DESC`

#### Partner Selector Rules
- On mount: if `commonFlowPartnerId` is set, use it; otherwise select the first available partner
- If the user has only 1 partner, auto-select it and disable the selector
- On partner change: invalidate and re-fetch stats + recent tickets
- Persist selection to `commonFlowPartnerId` in global state + `AsyncStorage`

---

### 4.3 API Integration

#### Fetch Stats
```
GET /api/v1/tickets/stats?partner_id={selectedPartnerId}
```

**Request Params**
| Param | Type | Required | Description |
|-------|------|----------|-------------|
| partner_id | string | Yes | Selected partner context |

**Expected Response**
```json
{
  "total": 156,
  "pending": 23,
  "completed": 133,
  "today_revenue": 2400.00
}
```

#### Fetch Recent Tickets
```
GET /api/v1/tickets?partner_id={selectedPartnerId}&limit=5&sort=-updated_at
```

**Expected Response**
```json
{
  "data": [
    {
      "id": "A1934",
      "device_model": "iPhone 12 Pro",
      "total_price": 280.00,
      "status": "selesai",
      "updated_at": "2026-06-04T14:32:00Z"
    }
  ]
}
```

---

### 4.4 Animations & Transitions

| Interaction | Animation Type | Spec |
|-------------|---------------|------|
| Screen entrance | Fade + slight slide up | Duration: 200 ms, Easing: ease-out |
| Stats value change | Count up animation | Duration: 400 ms, Easing: ease-out |
| Card tap | Scale down to 0.97 | Duration: 100 ms, Easing: ease-in-out |
| Card tap release | Scale back to 1.0 | Duration: 150 ms, Easing: spring |
| Pull-to-refresh | Standard platform spinner | — |
| Partner switch | Crossfade on stats + list | Duration: 150 ms |

---

### 4.5 Edge Cases & Error Handling

| Case | Behavior |
|------|----------|
| No internet on load | Show cached data if available; if none, show offline empty state with retry button |
| Stats API fails | Show inline error on stats row; "Tap to retry" on each failed card |
| Recent tickets API fails | Show inline error in recent tickets section with retry |
| User has 0 partners | Show error banner: "Tiada akses ke mana-mana cawangan. Hubungi pentadbir." |
| User is suspended | Redirect to login with error message |
| Deep-link to ticket while on Home | Allow navigation; do not block |
| Background refresh | On app resume, silently refresh stats and recent tickets (no loading spinner) |

---

## 5. Accessibility

### Screen Reader Support (VoiceOver/TalkBack)
- Full screen: `accessibilityLabel="Home, Repair Intake Dashboard"`
- Stats row: Each card read as "{label}, {value}"
- Action cards: Read as "{title} button, {subtitle}"
- Recent tickets: Each item read with all metadata
- "Lihat Semua" link: `accessibilityHint="Navigates to full jobs queue"`

### Color & Contrast
- All text meets WCAG AA (4.5:1 normal, 3:1 large)
- Status chips use distinct colors + text labels (not color alone)

### Dynamic Type / Font Scaling
- All text scales with system font size
- Stats values: max font size capped at 32 px to prevent overflow
- Action cards: title wraps to 2 lines if needed; subtitle truncates with ellipsis

### Reduced Motion
- If enabled: disable count-up animation, use instant transitions for card taps

---

## 6. Offline & Performance

### Caching Strategy
- **Stats**: Cache for 5 minutes (`stale-while-revalidate`)
- **Recent Tickets**: Cache for 2 minutes
- **Partner List**: Cache indefinitely (invalidate on login)

### Skeleton Loading
- Stats row: 4 rectangular skeletons (pulse animation)
- Recent tickets: 5 row skeletons
- Action cards: never show skeleton (static content)

### Data Freshness
- On mount: fetch fresh data
- On pull-to-refresh: fetch fresh data, invalidate cache
- On partner change: fetch fresh data
- On app resume (background → foreground): silently re-fetch (no UI change unless data differs)

---

## 7. Security & Privacy

- Never log selected partner ID in analytics without hashing
- Stats API response must respect user's partner access scope (backend-enforced)
- Do not cache ticket data containing customer PII to disk; use encrypted storage only
- Revenue figures must be accurate to 2 decimal places; do not round in UI

---

## 8. Analytics & Metrics

| Event | Trigger | Properties |
|-------|---------|-----------|
| `home_viewed` | Screen mount | partner_id (hashed), role |
| `home_create_ticket_tapped` | Tap "Create Ticket" card | partner_id (hashed) |
| `home_assessment_tapped` | Tap "Assessment" card | partner_id (hashed) |
| `home_quick_repair_tapped` | Tap "Quick Repair" card | partner_id (hashed) |
| `home_view_all_tickets_tapped` | Tap "Lihat Semua" | partner_id (hashed) |
| `home_ticket_tapped` | Tap a recent ticket | ticket_id (hashed), status |
| `home_partner_changed` | Partner selector used | from_partner (hashed), to_partner (hashed) |
| `home_notification_tapped` | Tap bell icon | unread_count |
| `home_stats_loaded` | Stats API success | duration_ms |
| `home_stats_error` | Stats API failure | error_code |

---

## 9. Implementation Notes

### Platform-Specific
- **iOS**: Use `UICollectionView` for stats row (supports animations); use `UITableView` for recent tickets
- **Android**: Use `RecyclerView` for both; `GridLayoutManager` (4 cols) for stats, `LinearLayoutManager` for tickets
- **React Native**: Use `FlatList` for recent tickets; stats row is a simple `View` with `flexDirection: 'row'`

### Component Reusability
- `StatCard` → reusable across Home and other dashboard screens
- `ActionCard` → reusable for any large tappable action with icon + title + subtitle + chevron
- `PartnerChip` → shared with other screens that need partner context switching

### Navigation
```
// React Navigation example
navigation.navigate('NewTicket', { partnerId: selectedPartnerId });
navigation.navigate('Assessment', { partnerId: selectedPartnerId });
navigation.navigate('QuickRepair', { partnerId: selectedPartnerId });
navigation.navigate('JobsQueue');
navigation.navigate('TicketDetail', { ticketId: ticket.id });
```

### Common Pitfalls
- **Do not** fetch stats on every `focus` event — only on mount, refresh, partner change, or app resume
- **Do not** block the UI while loading stats; show skeletons or cached data immediately
- **Ensure** the partner selector is accessible even when the keyboard is open (if any input exists on Home — currently none)

---

## 10. Related Specifications

| Document | Relationship |
|----------|-------------|
| `02-navigation-shell.md` | Defines the 5-tab bottom nav; this screen is the "Utama" tab |
| `04-new-ticket-full-ai.md` | Target of "Create Ticket" card |
| `06-assessment.md` | Target of "Assessment" card |
| `07-quick-repair.md` | Target of "Quick Repair" card |
| `05-ticket-detail.md` | Target of recent ticket tap |
| `03-jobs-queue.md` | Target of "Lihat Semua" link |
| `06-mobile-flows.md` | Defines the common flow starting from this screen |
| `00-index.md` | Build order: this screen is #3 (after splash/login and nav shell) |
| `../../design-system/03-mobile-screens.md` | Visual tokens for cards, chips, and typography |

---

## 11. Changelog

| Date | Author | Change |
|------|--------|--------|
| 2026-06-04 | Amir | Initial draft. Created new Home / Intake Landing spec as primary post-login screen. Replaces FAB as the main entry point for all repair flows. |

---

## 12. Questions / Open Items

1. **Stats Refresh Interval**: Should stats auto-refresh every 30 seconds when the screen is visible? Or only on explicit actions?
2. **Recent Tickets Limit**: Is 5 the right number, or should it be configurable per partner?
3. **Revenue Scope**: Should "Today's Revenue" include only completed tickets, or also in-progress tickets with partial payments?
4. **Notification Bell**: What does the notification list/drawer look like? Is it a separate screen or a bottom sheet?
5. **Home Customization**: Should partners be able to customize which action cards appear or their order?

---

*End of Document*
