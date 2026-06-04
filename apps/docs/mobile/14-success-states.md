# Feature: Shared Success & Confirmation States

> **Screen-level UI spec only.**  
> For design tokens: `design-system/01-tokens-and-colors.md`  
> For reusable components: `design-system/02-components.md`

---

## Overview

This file defines success screens, confirmation dialogs, and prompt modals that are reused across multiple flows. They are not tied to a single feature — rather, they appear after key state transitions.

---

## State 1: Ticket Created (Full AI Intake)

### Trigger

After front desk submits a new ticket via Full AI Intake (`04-new-ticket-full-ai.md`).

### Layout

Full-screen or full-modal overlay. Centered content.

```
┌─────────────────────────────────────────┐
│                                         │
│         ✅ (large checkmark, 80px)      │
│                                         │
│  Tiket #D1-042 Dicipta                  │
│         (mono, heading-1, --primary)   │
│                                         │
│  ┌─────────────────────────────────┐  │
│  │ Peranti: iPhone 14 Pro          │  │
│  │ Pelanggan: Ahmad bin Abdullah   │  │
│  │ Cawangan: Dungun 1               │  │
│  └─────────────────────────────────┘  │
│                                         │
│  WhatsApp akan dihantar kepada         │
│  pelanggan: +6012-345-6789             │
│                                         │
│  ┌─────────────────────────────────┐  │
│  │ 📋 Draf AI:                     │  │
│  │ Salam Ahmad, tiket D1-042       │  │
│  │ dicipta. iPhone 14 Pro...       │  │
│  │ [Edit] [Hantar]                 │  │
│  └─────────────────────────────────┘  │
│                                         │
│       [Buka Tiket]  [Tiket Baharu]      │
│                                         │
└─────────────────────────────────────────┘
```

### Elements

- **Checkmark icon**: 80px, `--secondary` color.
- **Title**: "Tiket #D1-042 Dicipta" (`heading-1`, `--primary`, `mono` font for number).
- **Details card**: bg `--card`, border `--border`, radius 12px, padding 16px.
  - Peranti: `body`, `--foreground`
  - Pelanggan: `body`, `--foreground`
  - Cawangan: `body`, `--foreground`
- **WhatsApp confirmation**: `body`, `--muted-foreground`.
- **AI Draft card** (optional, Month 2+): bg `--card`, border `--border`.
  - Shows AI-generated WhatsApp draft.
  - `[Edit]` — `ghost` button, `sm`.
  - `[Hantar]` — `primary` button, `sm`.
- **Actions**:
  - `[Buka Tiket]` — `outline` button, `md`. Push to Ticket Detail.
  - `[Tiket Baharu]` — `primary` button, `md`. Resets New Ticket form.

### Haptic

Medium impact on appear.

---

## State 2: Assessment Sent

### Trigger

After technician taps `[Hantar & Notify]` on Assessment screen (`06-assessment.md`).

### Layout

Same centered full-screen style as Ticket Created.

### Content

- **Icon**: Paper plane or checkmark, 80px, `--secondary`.
- **Title**: "Penilaian dihantar!" (`heading-1`, `--primary`)
- **Description**: "Pelanggan akan dimaklumkan melalui WhatsApp." (`body`, `--muted-foreground`)
- **Actions**:
  - `[Kembali ke Senarai]` — `primary` button, `md`. Pop to Jobs Queue.
  - `[Lihat Tiket]` — `outline` button, `md`. Push to Ticket Detail.

---

## State 3: Approval Received

### Trigger

When system receives customer YES reply via WhatsApp webhook, updating ticket status to `APPROVED`.

### Layout

Same centered full-screen style.

### Content

- **Icon**: Party popper or large checkmark, 80px, `--success`.
- **Title**: "Pelanggan telah luluskan!" (`heading-1`, `--success`)
- **Description**: "Pembaikan boleh dimulakan." (`body`, `--muted-foreground`)
- **Action**: `[Mula Pembaikan]` — `primary` button, `lg`, full width.
  - On tap: updates status to `IN_PROGRESS`, pops to Ticket Detail or Jobs Queue.

### Auto-Appear

- This can appear as a modal overlay while technician is in the app.
- If app is backgrounded: push notification (Month 3+) + in-app toast on next open.

---

## State 4: Technician "Assess Now?" Prompt (Flow D)

### Trigger

When a technician creates a ticket (via any path), show a prompt asking if they want to assess immediately.

### Layout

Modal overlay (not full screen). Backdrop dim: `--background` at 50% opacity.

```
┌─────────────────────────────────────────┐
│                                         │
│  ASSESS SEKARANG?                       │
│  heading-2                              │
│                                         │
│  Anda adalah teknikal.                  │
│  Nilaikan peranti ini sekarang?         │
│  body, --muted-foreground               │
│                                         │
│  [YA, NILAIKAN]      primary, lg        │
│  [KEMUDIAN — tambah ke senarai] outline │
│                                         │
└─────────────────────────────────────────┘
```

### Card Style

- Width: 90% of screen, max 360px.
- Background: `--card`
- Border: 1px `--border`
- Border radius: 16px
- Padding: 24px
- Centered on screen.

### Actions

| Button                           | Style                       | Action                                                                      |
| -------------------------------- | --------------------------- | --------------------------------------------------------------------------- |
| `[YA, NILAIKAN]`                 | `primary`, `lg`, full width | Pushes to Assessment screen for this ticket. Status updates to `ASSESSING`. |
| `[KEMUDIAN — tambah ke senarai]` | `outline`, `lg`, full width | Dismisses modal. Ticket remains in queue with status `RECEIVED`.            |

### Haptic

Medium impact on appear.

---

## State 5: Quick Repair / Voice Success (With Phone)

### Trigger

After Quick Repair (`07-quick-repair.md`) or Voice Intake (`08-voice-intake.md`) submit with phone number.

### Layout

Same centered full-screen style as Ticket Created.

### Content

- **Icon**: Checkmark, 80px, `--secondary`.
- **Title**: "Tiket #D1-043 — Baiki Cepat" (`heading-1`, `--primary`)
- **Details**:
  - Device + issue: `heading-3`, `--foreground`
  - Price: `heading-2`, `--primary` (e.g., "Harga: RM350")
- **WhatsApp confirmation**: "WhatsApp resit dihantar ke +6012-345-6789" (`body`, `--muted-foreground`)
- **Status**: "Status: Dalam Proses" (`body`, `--foreground`) + `StatusBadge`.
- **ETA**: "Anggaran siap: 3:30 PM" (`caption`, `--muted-foreground`)
- **Actions**:
  - `[Buka Tiket]` — `outline`, `md`
  - `[Baiki Cepat Baru]` — `primary`, `md`

---

## State 6: Quick Repair / Voice / Payment Success (Without Phone — QR)

### Trigger

After Quick Repair, Voice Intake, or Payment submit without phone number.

### Layout

Same centered full-screen style, but QR code is the hero element.

### Content

- **Icon**: Checkmark, 64px, `--secondary`.
- **Title**: "Tiket #D1-043 — Baiki Cepat" or "Pembayaran Diterima" (`heading-1`, `--primary`)
- **Details**: device + issue + price (if applicable).
- **QR Code**: 200×200px, centered, white bg.
- **Text below QR**: "Imbas untuk kemaskini WhatsApp & status" (`body`, `--muted-foreground`)
- **Actions**:
  - `[Buka Tiket]` — `outline`, `md`
  - `[Baiki Cepat Baru]` / `[Tiket Baharu]` — `primary`, `md`

See also `13-qr-receipt.md` for detailed QR display spec.

---

## State 7: Payment Success (With Phone)

### Trigger

After payment is recorded and customer has a phone number.

### Layout

Same centered full-screen style.

### Content

- **Icon**: Checkmark, 80px, `--secondary`.
- **Title**: "Pembayaran Diterima" (`heading-1`, `--success`)
- **Amount + method**: `heading-3`, `--foreground` (e.g., "RM350 — QR Pay")
- **Ticket info**: "Tiket #D1-043 — Selesai" (`body`, `--muted-foreground`)
- **WhatsApp confirmation**: "WhatsApp resit dihantar kepada +6012-345-6789" (`body`, `--muted-foreground`)
- **Receipt preview card**: bg `--card`, border `--border`, shows WhatsApp message content.
- **Action**: `[Kembali ke Senarai]` — `primary` button, `lg`.

See also `09-payment-receipt.md` for detailed payment spec.

---

## Shared Design Patterns

### Background

- Full-screen overlays: bg `--background`.
- Modal overlays: backdrop dim `--background` at 50% opacity, card bg `--card`.

### Animation

- Appear: fade in (200ms) + slight scale up (0.95 → 1.0, 250ms).
- Dismiss: fade out (150ms).
- Modal slide up: translateY(100px → 0, 300ms, ease-out).

### Haptics

- Medium impact on success screen appear.
- Heavy impact on error / cancellation (if applicable).

---

## Design Token References

| Element               | Token                      |
| --------------------- | -------------------------- |
| Success icon          | `--secondary`              |
| Success title         | `--primary` or `--success` |
| Details card bg       | `--card`                   |
| Details card border   | `--border`                 |
| Backdrop dim          | `--background` at 50%      |
| Modal card bg         | `--card`                   |
| Modal card border     | `--border`                 |
| Description text      | `--muted-foreground`       |
| Primary action bg     | `--primary`                |
| Outline action border | `--border`                 |
| Approval icon         | `--success`                |

---

## Components Used

- `SuccessScreen` — full-screen centered layout (reusable wrapper)
- `ModalCard` — centered modal with backdrop (reusable wrapper)
- `CheckmarkIcon` — animated or static large checkmark
- `DetailsCard` — ticket info summary card
- `ReceiptPreviewCard` — WhatsApp message preview
- `QRCodeDisplay` — large QR code (200px)
- `Button` — primary, outline
- `StatusBadge` — status chip

---

## Related Specs

- New Ticket (triggers State 1 & 4): `04-new-ticket-full-ai.md`
- Assessment (triggers State 2): `06-assessment.md`
- Quick Repair (triggers State 5 & 6): `07-quick-repair.md`
- Voice Intake (triggers State 5 & 6): `08-voice-intake.md`
- Payment (triggers State 6 & 7): `09-payment-receipt.md`
- QR Receipt (State 6 detail): `13-qr-receipt.md`
- Jobs Queue (returns to): `03-jobs-queue.md`
- Ticket Detail (opens from): `05-ticket-detail.md`
