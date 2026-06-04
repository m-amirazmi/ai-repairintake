# Feature: Navigation Shell (Tab Bar, Stack Screens, Safe Area)

> **Screen-level UI spec only.**  
> For design tokens: `design-system/01-tokens-and-colors.md`  
> For reusable components: `design-system/02-components.md`

---

## Overview

This file defines the structural navigation container of the mobile app — the shell that wraps all screens. It does not define the _content_ of individual screens; those are in their respective feature files.

---

## Tab Navigator (iOS-Style Bottom Tabs)

### Tabs

| #   | Icon (Lucide)   | Label (BM) | Label (EN) | Visible To       | Route               |
| --- | --------------- | ---------- | ---------- | ---------------- | ------------------- |
| 1   | `Home`          | Utama      | Home       | All roles        | `(app)/home.tsx`    |
| 2   | `ClipboardList` | Tugas      | Jobs       | All roles        | `(app)/index.tsx`   |
| 3   | `Stethoscope`   | Nilai      | Assess     | Technicians only | `(app)/assess.tsx`  |
| 4   | `History`       | Sejarah    | History    | All roles        | `(app)/history.tsx` |
| 5   | `UserCircle`    | Profil     | Profile    | All roles        | `(app)/profile.tsx` |

### Tab Bar Styling

- **Height**: 64px
- **Background**: `--card`
- **Top border**: 1px solid `--border`
- **Safe area padding**: Bottom safe area inset (iPhone notch / Dynamic Island)
- **Active state**: Filled icon, color `--primary`
- **Inactive state**: Outline icon, color `--muted-foreground`
- **Label**: `caption` size, same color as icon
- **Tab switch animation**: None (instant) or subtle fade (150ms)

### Role-Based Tab Visibility

- `front_desk` → sees Tabs 1, 2, 4, 5 (no Assess tab)
- `technician` → sees all 5 tabs
- `manager` / `owner` → sees Tabs 1, 2, 4, 5 (no Assess tab — assessment is done on web dashboard or via ticket detail)

---

## Stack Screens (Pushed Over Tabs)

These screens slide in from the right (standard iOS push transition):

| Screen               | Route                 | Pushed From                                         |
| -------------------- | --------------------- | --------------------------------------------------- |
| New Ticket (Full AI) | `new-ticket.tsx`      | Home "Create Ticket" action card                    |
| Ticket Detail        | `ticket/[id].tsx`     | Tap on queue list item or recent ticket on Home     |
| Assessment           | `assessment/[id].tsx` | Home "Assessment" action card, or ticket detail "Buka Penilaian", or technician prompt |
| Quick Repair         | `quick-repair.tsx`    | Home "Quick Repair" action card                     |
| Voice Intake         | `voice-intake.tsx`    | Quick Repair header mic, or New Ticket customer notes mic |
| Forgot Password      | `forgot-password.tsx` | Login screen                                        |

### Stack Header (for pushed screens)

- **Modern header**: No back arrow. Left action is a text button (e.g., "Batal", "Kembali") or left empty.
- Title centered (or left-aligned if no left action)
- Optional right action (e.g., "Simpan Draf" on Assessment, vertical ellipsis menu)
- Background: `--card`. **No bottom border** — clean modern look.
- No large titles — keep compact (iOS standard navigation bar height)

---

## Home / Utama Tab (Default Landing)

The **Utama** (Home) tab is the default active tab after login. It serves as the central hub for all repair-intake workflows.

### Content

- Welcome message + user info
- Stats row (Total Tickets, Pending, Completed, Today's Revenue)
- Flow action cards:
  - **Create Ticket** → pushes to New Ticket (Full AI)
  - **Assessment** → pushes to Assessment
  - **Quick Repair** → pushes to Quick Repair
- Recent tickets preview
- Partner/branch selector

See full spec: `03-home-intake.md`

---

## Global UI Elements

### Offline Indicator Banner

- **Position**: Top of screen, below status bar.
- **Shown when**: No internet connection detected.
- **Appearance**: Full width, height ~36px, bg `--destructive` at 15% opacity, text `--destructive`.
- **Text**: "Tiada sambungan. Tiket akan disegerakkan apabila online." (BM) / "No connection. Tickets will sync when online." (EN)
- **Dismiss**: Auto-dismiss when connection restored. Can be manually swiped up.

### Pending Sync Badge

- **Position**: Overlaid on Profile tab icon (small red dot with number).
- **Shown when**: AsyncStorage queue has unsynced tickets.
- **Text**: Number of pending tickets (e.g., "4").
- **Badge style**: 16px circle, bg `--destructive`, text white, `caption` size.

### Toast System

- Position: Bottom of screen, 16px above tab bar (or 80px above FAB area).
- Duration: 3 seconds, auto-dismiss with fade.
- Types:
  - `success` — bg `--secondary`, text `--secondary-foreground`
  - `error` — bg `--destructive`, text `--destructive-foreground`
  - `info` — bg `--primary`, text `--primary-foreground`

---

## Safe Area & Status Bar

### Status Bar

- Light mode: `dark-content`
- Dark mode: `light-content`
- Dynamic based on current theme context

### Notch / Dynamic Island

- Avoid placing interactive elements in top safe area.
- Use `SafeAreaView` on all tab screens.
- Stack screens use `SafeAreaView` with top inset on content area only (header is in safe area).

### Home Indicator

- Bottom safe area respected by tab bar and FAB.
- ScrollViews should have `contentInsetAdjustmentBehavior="automatic"` (iOS).

---

## Dark Mode Handling

- Toggle source: Profile screen toggle + system preference on first launch.
- Immediate switch — no app reload.
- All color tokens swap automatically via React context + CSS variables (NativeWind).
- Persist preference to AsyncStorage.

---

## Haptics (Global Patterns)

| Action                  | Haptic        |
| ----------------------- | ------------- |
| Button press (standard) | Light impact  |
| Toggle switch           | Light impact  |
| Submit success          | Medium impact |
| Status change           | Medium impact |
| Error / destructive     | Heavy impact  |
| Picker wheel selection  | Selection     |
| Home action card tap    | Light impact  |
| Stepper (+/-)           | Light impact  |

---

## Gestures (Global Patterns)

| Gesture                  | Action                                                              | Screen               |
| ------------------------ | ------------------------------------------------------------------- | -------------------- |
| Swipe right on list item | Quick action "Buka" (Open)                                          | Jobs Queue, History  |
| Swipe left on list item  | Quick action "Hantar ke Teknikal" (if status = RECEIVED) or "Arkib" | Jobs Queue           |
| Pull down                | Refresh list                                                        | Jobs Queue, History  |
| Long press on list item  | Context menu (ticket options)                                       | Jobs Queue, History  |
| Tap outside input        | Dismiss keyboard                                                    | All forms            |
| Tap outside modal / menu | Dismiss modal / menu                                                | All modals           |

---

## Transitions

| Type                     | Animation                  | Duration             |
| ------------------------ | -------------------------- | -------------------- |
| Push (tab → stack)       | Slide from right           | iOS default (~300ms) |
| Pop (stack → tab)        | Slide to right             | iOS default (~300ms) |
| Modal (payment, success) | Slide up from bottom       | ~350ms               |
| Dismiss modal            | Swipe down or tap backdrop | ~250ms               |
| Tab switch               | Instant or subtle fade     | 150ms                |

---

## Design Token References

| Element             | Token                          |
| ------------------- | ------------------------------ |
| Tab bar bg          | `--card`                       |
| Tab bar border      | `--border`                     |
| Tab active icon     | `--primary`                    |
| Tab inactive icon   | `--muted-foreground`           |
| Home card bg        | `--card`                       |
| Home card border    | `--border`                     |
| Stack header bg     | `--card`                       |
| Stack header border | `--border`                     |
| Offline banner bg   | `--destructive` at 15% opacity |
| Offline banner text | `--destructive`                |
| Toast success bg    | `--secondary`                  |
| Toast error bg      | `--destructive`                |
| Toast info bg       | `--primary`                    |
| Backdrop dim        | `--background` at 50% opacity  |

---

## Components Used

- `TabBar` — custom or Expo Router tab bar
- `HomeScreen` — intake landing hub (see `03-home-intake.md`)
- `Toast` — global toast system
- `OfflineBanner` — top connectivity banner
- `SyncBadge` — profile tab badge

---

## Related Specs

- Auth (entry point): `01-auth.md`
- Home (Tab 1): `03-home-intake.md`
- Jobs queue (Tab 2): `03-jobs-queue.md`
- Profile (Tab 5): `10-profile-settings.md`
- New Ticket (Home card): `04-new-ticket-full-ai.md`
- Quick Repair (Home card): `07-quick-repair.md`
- Voice Intake (Quick Repair mic): `08-voice-intake.md`
