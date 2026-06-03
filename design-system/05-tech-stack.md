# UI Tech Stack

## Overview

The mobile app uses **NativeWind** (Tailwind CSS for React Native) with **React Native Reusables** as the component foundation. This allows the same Tailwind styling syntax used in the web app, with shared design tokens across the monorepo.

---

## Core Dependencies

### Styling

| Package | Version | Purpose |
|---------|---------|---------|
| `nativewind` | ^4.0 | Tailwind CSS for React Native |
| `tailwindcss` | ^3.4 | Utility-first CSS framework |
| `react-native-reusables` | latest | Unstyled, composable RN primitives |
| `class-variance-authority` | ^0.7 | Component variant management |
| `clsx` | ^2.1 | Conditional className utility |
| `tailwind-merge` | ^2.3 | Smart Tailwind class merging |

### Animation

| Package | Version | Purpose |
|---------|---------|---------|
| `react-native-reanimated` | ^3.11 | Smooth animations, transitions, gestures |
| `react-native-gesture-handler` | ^2.16 | Gesture handling for bottom sheets, swipe |

### UI Components

| Package | Version | Purpose |
|---------|---------|---------|
| `@gorhom/bottom-sheet` | ^4.6 | Bottom sheets (part picker, dialogs) |
| `react-native-toast-message` | ^2.2 | Toast notifications |
| `lucide-react-native` | ^0.400 | Icon system (same as web) |
| `react-native-safe-area-context` | ^4.10 | Safe area handling |
| `react-native-screens` | ^3.31 | Native screen optimization |

### Forms

| Package | Version | Purpose |
|---------|---------|---------|
| `react-hook-form` | ^7.51 | Form state management |
| `@hookform/resolvers` | ^3.4 | Zod resolver for RHF |
| `zod` | ^3.23 | Schema validation |

### Camera & Media

| Package | Version | Purpose |
|---------|---------|---------|
| `expo-camera` | ~14.1 | Camera capture |
| `expo-image-picker` | ~15.0 | Image picker fallback |
| `expo-image-manipulator` | ~12.0 | Image resize/compression |
| `expo-av` | ~14.0 | Audio recording (voice intake, voice notes) |

### Native Features

| Package | Version | Purpose |
|---------|---------|---------|
| `expo-local-authentication` | ~14.0 | Face ID / Touch ID |
| `expo-haptics` | ~13.0 | Haptic feedback |
| `@react-native-async-storage/async-storage` | ^1.23 | Local storage |
| `expo-secure-store` | ~13.0 | Secure token storage |
| `react-native-qrcode-svg` | latest | QR code generation for receipts |
| `react-native-svg` | latest | SVG rendering (required by qrcode) |

---

## Why NativeWind + Reusables

### Alternative: Tamagui

Tamagui is powerful for sharing components between web and mobile. However:
- Has its own compiler, token system, and config
- Learning curve for someone new to mobile
- Our theme is Tailwind-native from Tweakcn
- NativeWind gives us the exact same syntax as web

**Verdict**: NativeWind is the faster path to productivity. Tamagui is a future option if we need heavy web/mobile component sharing.

### Alternative: React Native Paper

Material Design for RN. Very stable and accessible.
- But it looks Android-native
- Harder to customize to our exact theme
- iOS-only app should feel iOS-native

**Verdict**: Reusables + NativeWind gives us full control.

### Alternative: Gluestack / NativeBase

Higher-level component libraries.
- More opinionated styling
- Harder to match our exact "Sharper Pop" theme
- Heavier bundle size

**Verdict**: Start with primitives (Reusables), build our own component library.

---

## Shared Design Tokens Package

```
packages/ui/
├── theme/
│   ├── colors.ts          # All color values (light/dark/status)
│   ├── tokens.ts          # Spacing, radius, shadow, typography
│   └── index.ts           # Barrel export
├── types/
│   └── theme.ts           # TypeScript theme types
└── package.json
```

### colors.ts

Exports color objects for NativeWind config and runtime usage:

```typescript
export const lightTheme = {
  background: '#f7f9f3',
  foreground: '#111827',
  card: '#ffffff',
  // ... all tokens
};

export const darkTheme = {
  background: '#0f1115',
  foreground: '#ffffff',
  card: '#181b20',
  // ... all tokens
};

export const statusColors = {
  received: { bg: '#e0e7ff', text: '#4f46e5' },
  assessing: { bg: '#ccfbf1', text: '#14b8a6' },
  // ... all statuses
};
```

### tokens.ts

```typescript
export const spacing = {
  0: '0px',
  1: '4px',
  2: '8px',
  // ... up to 16
};

export const radius = {
  sm: '6px',
  md: '8px',
  lg: '12px',
  xl: '16px',
  '2xl': '20px',
  full: '9999px',
};

export const typography = {
  display: { size: '32px', weight: '700', lineHeight: '1.2' },
  heading1: { size: '24px', weight: '700', lineHeight: '1.3' },
  // ... all scales
};
```

### tailwind.config.ts (in mobile app)

```typescript
import { lightTheme, darkTheme } from '@repair-intake/ui/theme';

export default {
  content: ['./app/**/*.{js,jsx,ts,tsx}'],
  theme: {
    extend: {
      colors: {
        background: lightTheme.background,
        foreground: lightTheme.foreground,
        // Map all tokens...
      },
    },
  },
  darkMode: 'class', // or media, but we'll use context-based
};
```

---

## Web App Alignment

The web dashboard (Next.js) uses **shadcn/ui** with the same Tailwind theme. The `packages/ui/theme` package provides the shared color values.

```
apps/web/
├── app/
│   └── globals.css     # Imports theme colors, same tokens
├── components/
│   └── ui/             # shadcn/ui components
├── tailwind.config.ts  # References @repair-intake/ui/theme
```

Both apps share:
- Color values
- Border radius
- Shadow definitions
- Status colors

Web-specific additions:
- Sidebar layout tokens
- Data table spacing
- Chart colors

---

## Component Architecture

### Primitive → Composed → Screen

1. **Primitives** (React Native Reusables / custom)
   - `Button`, `Input`, `Card`, `Badge`
   - Accept `className` for style overrides
   - Handle accessibility, haptics, press states

2. **Composed Components** (App-specific)
   - `CameraCapture`, `TicketListItem`, `SuggestionChips`
   - Built from primitives
   - Business logic via hooks

3. **Screen Components**
   - `LoginScreen`, `NewTicketScreen`, `AssessmentScreen`
   - Layout, navigation, state management
   - Compose lower-level components

### Example: Button

```tsx
// Primitive
import { cn } from "@repair-intake/ui/utils";

interface ButtonProps extends PressableProps {
  variant?: "default" | "secondary" | "accent" | "destructive" | "outline" | "ghost";
  size?: "sm" | "default" | "lg" | "icon";
}

export function Button({ className, variant, size, ...props }: ButtonProps) {
  return (
    <Pressable
      className={cn(
        "flex items-center justify-center rounded-md font-medium",
        variant === "default" && "bg-primary text-primary-foreground",
        size === "default" && "h-12 px-4",
        className
      )}
      {...props}
    />
  );
}
```

---

## Dark Mode Implementation

### React Context

```tsx
// apps/mobile/lib/theme.tsx
const ThemeContext = createContext({
  theme: "light" | "dark",
  toggleTheme: () => {},
});

// Reads from AsyncStorage, respects system preference on first launch
// Wraps app, provides to all components
```

### NativeWind Dark Mode

Option 1: `class` strategy — toggle `dark` class on root View
Option 2: Manual color switching via context

**Chosen approach**: Context-based manual switching for immediate control. NativeWind `class` strategy as fallback.

```tsx
// In root layout
<View className={theme === "dark" ? "dark" : ""}>
  <Slot />
</View>
```

With Tailwind config:
```
darkMode: "class",
```

---

## Icon System

**Lucide React Native** (`lucide-react-native`)

Same icon library as web app. Tree-shakeable, consistent naming.

Common icons:
- `ClipboardList` — Jobs tab
- `Stethoscope` — Assess tab
- `History` — History tab
- `UserCircle` — Profile tab
- `Camera` — Camera capture
- `Check` — Success states
- `AlertTriangle` — Warnings
- `X` — Close, delete
- `Plus` — FAB, add
- `ChevronRight` — List navigation
- `ChevronLeft` — Back
- `Search` — Search fields
- `Bell` — Notifications
- `Settings` — Settings
- `LogOut` — Logout
- `Smartphone` — Device placeholder
- `Wrench` — Repair icon
- `Sparkles` — AI suggestion
- `CloudOff` — Offline indicator

---

## Animation Strategy

### Reanimated Priorities

1. **Screen transitions**: Subtle fade/slide (Expo Router handles most)
2. **Bottom sheets**: Slide up + backdrop fade (Gorhom handles)
3. **AI result reveal**: Fade in + slide up on camera capture
4. **Status badge change**: Color transition (layout animation)
5. **List item appearance**: Fade in on new ticket (layout animation)
6. **Button press**: Scale 0.98 + opacity (Reanimated or Pressable)
7. **Toast**: Slide down from top, auto-dismiss fade
8. **Skeleton shimmer**: Gradient sweep (Reanimated)

### Haptics Strategy

- `expo-haptics` with `Haptics.ImpactFeedbackStyle`
- Light: standard button presses, toggles
- Medium: form submit, status transitions
- Heavy: errors, destructive actions
- Selection: picker, stepper changes

---

## Camera Implementation

### Inline Camera (Screen C)

```tsx
import { CameraView, useCameraPermissions } from "expo-camera";

// 4:5 aspect ratio, inline in ScrollView
// Tap to capture
// On capture: show preview, trigger AI API
// Retake: reset to empty state
```

### Image Pipeline

1. Capture with Expo Camera (max 1080p for speed)
2. Resize/compress with `expo-image-manipulator` (target: 800×1000, ~200KB)
3. Upload to Supabase Storage (ticket folder: `intake/{ticket_id}/photo.jpg`)
4. Send URL to `/api/ai/identify-device`
5. Display result

---

## Form Handling

### React Hook Form + Zod

Same pattern as web app. Mobile-specific considerations:
- `Controller` for each input
- `zod` schemas in `packages/shared/validators.ts`
- Error display: inline below field, `caption` size, `--destructive`
- Submit on return key (TextInput `onSubmitEditing`)

### Example

```tsx
const form = useForm({
  resolver: zodResolver(newTicketSchema),
});

<Controller
  name="customerName"
  control={form.control}
  render={({ field, fieldState }) => (
    <View>
      <Input
        placeholder="Nama pelanggan"
        value={field.value}
        onChangeText={field.onChange}
        error={fieldState.error?.message}
      />
      {fieldState.error && (
        <Text className="text-destructive text-xs mt-1">
          {fieldState.error.message}
        </Text>
      )}
    </View>
  )}
/>
```

---

## Offline Handling

### Offline Banner

- Fixed position below safe area
- Background `--accent` (amber)
- Slides down on disconnect, up on reconnect
- Z-index above all content

### AsyncStorage Sync Queue

- Ticket creation: save to AsyncStorage, attempt API call
- On success: remove from queue
- On failure: retry on reconnect
- Assessment drafts: always saved to AsyncStorage first, then synced

### NetInfo

```tsx
import NetInfo from "@react-native-community/netinfo";

// Subscribe to connection state
// Show/hide offline banner
// Trigger sync queue when online
```

---

## Biometric Login

### Implementation

```tsx
import * as LocalAuthentication from "expo-local-authentication";

async function promptBiometric() {
  const enrolled = await LocalAuthentication.isEnrolledAsync();
  if (!enrolled) return false;

  const result = await LocalAuthentication.authenticateAsync({
    promptMessage: "Log masuk dengan Face ID",
    fallbackLabel: "Guna kata laluan",
  });

  return result.success;
}
```

### Flow

1. User logs in normally once
2. App asks: "Hidupkan log masuk Face ID?" (toggle in Profile)
3. Next time: show face/fingerprint icon on login screen
4. Tap icon → biometric prompt → auto-login with stored refresh token
5. Fallback to password on failure

---

## Performance Guidelines

- **Image optimization**: Compress intake photos to 800px width max
- **List virtualization**: `FlashList` (from @shopify/flash-list) for ticket queues > 50 items
- **Re-render prevention**: Memo composed components, use `React.memo` on list items
- **Asset loading**: Preload fonts and icons at app startup (Expo SplashScreen)
- **Bundle size**: Tree-shake Lucide icons, lazy load heavy screens
