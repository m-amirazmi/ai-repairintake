# Feature: Auth (Login, Biometric, Forgot Password, Role Redirect)

> **Screen-level UI spec only.**  
> For design tokens: `design-system/01-tokens-and-colors.md`  
> For reusable components: `design-system/02-components.md`

---

## Screens Covered

- `login.tsx` — Primary login screen
- `forgot-password.tsx` — Password reset request

---

## Screen: Login

### Purpose

Staff authentication with email + password, optional biometric login.

### Layout

- `SafeAreaView`, centered content vertically.
- Logo / app icon at top: 80px, color `--primary`.
- Title: "Selamat Datang" (`heading-1`)
- Subtitle: "Log masuk ke akaun anda" (`body`, `--muted-foreground`)

### Fields

| Field       | Type     | Placeholder       | Validation                   |
| ----------- | -------- | ----------------- | ---------------------------- |
| Emel        | email    | "alamat@emel.com" | Required, valid email format |
| Kata Laluan | password | "Kata laluan"     | Required, min 6 characters   |

- Password field has a toggle-visibility icon (eye / eye-off).
- Both fields use the app's standard `Input` component.

### Actions

| Button        | Style     | Size | Width      | State                            |
| ------------- | --------- | ---- | ---------- | -------------------------------- |
| `[Log Masuk]` | `primary` | `lg` | Full width | Disabled until both fields valid |

- Divider with text "atau" (or)
- `[Lupa kata laluan?]` — `ghost` button, navigates to forgot-password screen

### Biometric Login

- If Face ID / Touch ID is available and enrolled on the device:
  - Show face/fingerprint icon below the password field.
  - Tap icon triggers native biometric prompt.
  - Label: "Log masuk dengan Face ID" (or "Touch ID" depending on device).
- Uses `expo-local-authentication`.
- On biometric failure → fallback to password entry.
- On biometric success → attempt silent login with stored credentials.

### Bottom Section

- Language toggle: `[BM]` / `[EN]` segmented control.
  - Persists to AsyncStorage.
  - Immediate switch — no reload needed.
- Optional minimal "Powered by" text (very subtle, `--muted-foreground`).

### Keyboard Handling

- `KeyboardAvoidingView` with `behavior="padding"`.
- `ScrollView` to handle small screens.
- Tap outside input fields dismisses keyboard.

### States

| State              | UI                                                           |
| ------------------ | ------------------------------------------------------------ |
| `idle`             | Fields empty, login button disabled                          |
| `typing`           | Button enabled when both fields pass validation              |
| `loading`          | Button shows spinner, label changes to "Sedang log masuk..." |
| `error`            | Toast message: "Emel atau kata laluan tidak sah"             |
| `biometric-prompt` | Native iOS Face ID / Touch ID modal appears                  |

### Role-Based Home Redirect

After successful login, redirect based on user role:

| Role         | Default Tab | Focus / Note                                             |
| ------------ | ----------- | -------------------------------------------------------- |
| `front_desk` | Tab 1: Jobs | Highlight "Tiket Baharu" FAB or New Intake entry point   |
| `technician` | Tab 1: Jobs | Focus on Queue (assigned + unassigned)                   |
| `manager`    | Tab 1: Jobs | Same as owner but limited admin access                   |
| `owner`      | Tab 1: Jobs | Has admin access (web dashboard), mobile same as manager |

---

## Screen: Forgot Password

### Layout

- Back arrow (←) in top-left navigates back to login.
- Title: "Lupa Kata Laluan" (`heading-1`)
- Subtitle: "Masukkan emel anda untuk menerima pautan reset" (`body`, `--muted-foreground`)

### Fields

- Emel: email input, placeholder "alamat@emel.com", required.

### Actions

- `[Hantar Pautan Reset]` — `primary` button, `lg`, full width.
- Disabled until email is valid.

### States

| State     | UI                                                                         |
| --------- | -------------------------------------------------------------------------- |
| `idle`    | Field empty, button disabled                                               |
| `typing`  | Button enabled when email valid                                            |
| `loading` | Spinner, "Sedang menghantar..."                                            |
| `success` | Full-screen or inline success: "Pautan reset telah dihantar ke emel anda." |
| `error`   | Toast: "Emel tidak dijumpai. Sila cuba lagi."                              |

---

## iOS-Specific Notes

- **Haptics**: Light impact on button press, toggle, biometric prompt.
- **Status bar**: Dynamic based on theme (light mode = dark content, dark mode = light content).
- **Safe area**: Top and bottom safe area insets respected.

---

## Design Token References

| Element                | Token                      |
| ---------------------- | -------------------------- |
| Primary button bg      | `--primary`                |
| Primary button text    | `--primary-foreground`     |
| Ghost button text      | `--primary`                |
| Input border (default) | `--border`                 |
| Input border (focused) | `--primary`                |
| Input bg               | `--card`                   |
| Placeholder text       | `--muted-foreground`       |
| Divider line           | `--border`                 |
| Divider text           | `--muted-foreground`       |
| Error toast bg         | `--destructive`            |
| Error toast text       | `--destructive-foreground` |
| Background             | `--background`             |
| Card / surface         | `--card`                   |

---

## Components Used (from `design-system/02-components.md`)

- `Input` — email, password fields
- `Button` — primary, ghost, outline variants
- `SegmentedControl` — language toggle (BM / EN)
- `Toast` — error messages
- `Spinner` — loading states inside buttons

---

## Related Specs

- Navigation shell: `02-navigation-shell.md`
- Jobs queue (landing after login): `03-jobs-queue.md`
