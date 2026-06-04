# Feature: Profile & Settings

> **Screen-level UI spec only.**  
> For design tokens: `design-system/01-tokens-and-colors.md`  
> For reusable components: `design-system/02-components.md`

---

## Screen: Profile (`(app)/profile.tsx`)

### Purpose

View user info, manage settings (outlet, language, dark mode, notifications, biometric), and log out.

### Layout

- `ScrollView` with sections separated by 16px gaps and 1px `--border` dividers.
- Header: "Profil" (`heading-2`), left-aligned, sticky at top.

---

## Section 1: User Card

### Layout

Horizontal row, vertically centered, padding 16px.

```
┌─────────────────────────────────────────┐
│ [Avatar 48px]  Amir                    │
│                Teknikal  [badge]       │
│                Dungun 1                │
└─────────────────────────────────────────┘
```

- **Avatar**: 48px circle. If no avatar image, show initials (e.g., "A") in `--primary` bg, white text, `heading-3`.
- **Name**: `heading-3`, `--foreground`
- **Role badge**: `caption-medium`, `--primary` bg at 15% opacity, text `--primary`. Examples: "Teknikal", "Kaunter", "Pengurus", "Pemilik".
- **Outlet**: `body`, `--muted-foreground`

---

## Section 2: Settings List

### Layout

Vertical list of rows. Each row: icon (24px, `--muted-foreground`), label (`body`), value (`caption`, `--muted-foreground` or `--primary`), chevron (`ChevronRight`, 16px, `--muted-foreground`).

- Row height: 56px.
- Separator: 1px `--border` between rows.
- Tap feedback: bg `--muted` at 5% opacity.

### Settings Rows

| Row                 | Icon          | Label (BM)          | Value Display   | Action                             | Restricted               |
| ------------------- | ------------- | ------------------- | --------------- | ---------------------------------- | ------------------------ |
| Tukar Cawangan      | `Building`    | Tukar Cawangan      | "Dungun 1"      | Push outlet selector bottom sheet  | Managers/Owners only     |
| Bahasa              | `Globe`       | Bahasa              | "Bahasa Melayu" | Push language selector             | All                      |
| Mod Gelap           | `Moon`        | Mod Gelap           | Toggle switch   | Immediate toggle                   | All                      |
| Notifikasi          | `Bell`        | Notifikasi          | Toggle switch   | Opens system settings if disabled  | All                      |
| Log Masuk Biometrik | `Fingerprint` | Log Masuk Biometrik | Toggle switch   | Triggers Face ID enrollment prompt | All (if device supports) |

### Toggle Switch Style

- Active: bg `--primary`, thumb white, slides right.
- Inactive: bg `--border`, thumb `--muted-foreground`, slides left.
- Haptic: light impact on toggle.

### Outlet Selector (Managers/Owners)

- Bottom sheet, ~50% height.
- List of outlets with radio buttons.
- Current outlet has filled `--primary` radio.
- Tap → select, sheet dismisses, list refreshes.

### Language Selector

- Bottom sheet or push to new screen.
- Options: "Bahasa Melayu" | "English"
- Selected: checkmark + `--primary` highlight.
- Immediate switch — no reload. Persists to AsyncStorage.

### Dark Mode Toggle

- Immediate switch — no reload.
- Uses React context + AsyncStorage persistence.
- Respects system preference on first launch.
- All color tokens swap via NativeWind dark variant.

### Biometric Login Toggle

- If device supports Face ID / Touch ID:
  - Toggle ON → triggers native enrollment prompt.
  - Toggle OFF → removes stored credentials from SecureStore.
- If device does not support: row hidden or disabled with "Tidak disokong" label.

---

## Section 3: App Info

### Layout

Centered, minimal, bottom of scroll view.

- App version: `overline`, `--muted-foreground` (e.g., "Versi 1.0.0 (Build 42)")
- Optional: "Dikuasakan oleh Repair Intake" in very small text.

---

## Section 4: Log Out

### Button

- `[Log Keluar]` — `destructive` button, `lg`, full width.
- Position: bottom of screen, 16px above safe area.

### Confirmation Dialog

- On tap: confirmation dialog appears.
- Title: "Log Keluar?"
- Message: "Anda akan diarahkan ke skrin log masuk."
- Actions: `[Batal]` (outline) / `[Log Keluar]` (destructive).
- On confirm: clear session, clear AsyncStorage (except settings), navigate to login.

---

## Design Token References

| Element                 | Token                      |
| ----------------------- | -------------------------- |
| User card bg            | `--card`                   |
| Avatar fallback bg      | `--primary`                |
| Avatar fallback text    | white                      |
| Role badge bg           | `--primary` at 15%         |
| Role badge text         | `--primary`                |
| Settings row label      | `--foreground`             |
| Settings row value      | `--muted-foreground`       |
| Settings row chevron    | `--muted-foreground`       |
| Row pressed bg          | `--muted` at 5%            |
| Toggle active bg        | `--primary`                |
| Toggle inactive bg      | `--border`                 |
| Destructive button bg   | `--destructive`            |
| Destructive button text | `--destructive-foreground` |
| App version text        | `--muted-foreground`       |

---

## Components Used

- `UserCard` — avatar + name + role + outlet
- `SettingsRow` — icon + label + value + chevron / toggle
- `Toggle` — custom switch component
- `BottomSheet` — outlet selector, language selector
- `Button` — destructive (logout)
- `ConfirmDialog` — logout confirmation

---

## Related Specs

- Navigation shell (tab bar): `02-navigation-shell.md`
- Auth (login screen after logout): `01-auth.md`
- Jobs queue (Tab 1): `03-jobs-queue.md`
