# Feature: QR Code Receipt Display

> **Screen-level UI spec only.**  
> For design tokens: `design-system/01-tokens-and-colors.md`  
> For reusable components: `design-system/02-components.md`

---

## Screen: QR Receipt (`qr-receipt/[id].tsx` or modal)

### Purpose

For anonymous repairs (no phone number provided), display a large QR code that the customer can scan. Links to a public ticket web view where they can subscribe to WhatsApp updates and check status.

### When Shown

| Trigger                              | Context                             |
| ------------------------------------ | ----------------------------------- |
| After Quick Repair submit (no phone) | Full-screen success state           |
| After Voice Intake submit (no phone) | Full-screen success state           |
| After Payment recording (no phone)   | Full-screen success state           |
| From Ticket Detail                   | `[Kod QR]` action button (any time) |

### Layout

- Full screen or modal (slide up from bottom).
- Centered content vertically and horizontally.
- Background: `--background`.

---

## Content

```
┌─────────────────────────────────────────┐
│                                         │
│  Tiket #D1-043                          │
│  iPhone 14 Pro                          │
│  Penukaran Skrin OLED                   │
│                                         │
│  [Status: Dalam Proses]                 │
│                                         │
│                                         │
│        ████████████████                 │
│        ██  QR CODE  ██                 │
│        ████████████████                 │
│                                         │
│                                         │
│  Imbas untuk kemaskini                  │
│  WhatsApp & status                      │
│                                         │
│  Cawangan: Dungun 1                     │
│  No. 23, Jalan Besar                   │
│                                         │
│  [Buka Tiket]  [Tiket Baharu]           │
│                                         │
└─────────────────────────────────────────┘
```

### Ticket Info (Above QR)

- **Ticket number**: `mono`, `heading-3`, `--primary`
- **Device model**: `body`, `--foreground`
- **Issue**: `body`, `--muted-foreground`
- **Status badge**: `StatusBadge` component, current status.

### QR Code

- Size: 200×200px.
- Centered.
- White background square behind QR (ensures scanner readability).
- Error correction level: medium (M) or high (H).
- Links to: `https://repairintake.my/t/{token}`

### Text Below QR

- "Imbas untuk kemaskini WhatsApp & status" (`body`, `--muted-foreground`)
- Line height relaxed for readability.

### Outlet Info (Below QR Text)

- "Cawangan: Dungun 1" (`caption`, `--muted-foreground`)
- Address: `caption`, `--muted-foreground`

### Actions (Bottom)

| Button           | Style           | Action                                          |
| ---------------- | --------------- | ----------------------------------------------- |
| `[Buka Tiket]`   | `outline`, `md` | Push to Ticket Detail                           |
| `[Tiket Baharu]` | `primary`, `md` | Reset and create new ticket (context-dependent) |

- On Quick Repair success: `[Baiki Cepat Baru]` instead of `[Tiket Baharu]`.
- Buttons side by side, equal width, 12px gap.

---

## Subscription Prompt (on Public Web View)

This is the screen the customer sees after scanning the QR:

```
┌─────────────────────────────────────────┐
│  🔧 Repair Intake                       │
│                                         │
│  Tiket #D1-043                          │
│  iPhone 14 Pro                          │
│  Penukaran Skrin OLED                   │
│  Status: ✅ Selesai                     │
│  Cawangan: Dungun 1                     │
│  No. 23, Jalan Besar                    │
│                                         │
│  ── Mahu kemaskini WhatsApp? ──          │
│  Nama:    [Ahmad           ]             │
│  Telefon: [+6012-345-6789 ]             │
│                                         │
│  [Langgan WhatsApp]                     │
│                                         │
│  Kami akan hantar kemaskini status      │
│  melalui WhatsApp.                      │
└─────────────────────────────────────────┘
```

- This is a **web** screen (Next.js), not a mobile app screen.
- Included here for reference so mobile devs know what the QR links to.

### Subscription Success

```
┌─────────────────────────────────────────┐
│  ✅ Berjaya!                            │
│                                         │
│  WhatsApp kemaskini akan dihantar       │
│  ke +6012-345-6789                      │
│                                         │
│  Semak WhatsApp anda untuk kemaskini    │
│  seterusnya.                            │
└─────────────────────────────────────────┘
```

---

## Design Token References

| Element                      | Token                                 |
| ---------------------------- | ------------------------------------- |
| Screen bg                    | `--background`                        |
| Ticket number text           | `--primary`                           |
| Device/issue text            | `--foreground` / `--muted-foreground` |
| QR code bg                   | white (always, for contrast)          |
| QR instruction text          | `--muted-foreground`                  |
| Outlet info text             | `--muted-foreground`                  |
| Action button primary bg     | `--primary`                           |
| Action button outline border | `--border`                            |
| Web view card bg             | `--card`                              |
| Web view input border        | `--border`                            |
| Success checkmark            | `--secondary`                         |

---

## Components Used

- `QRCodeDisplay` — large QR code renderer (200px)
- `StatusBadge` — current status chip
- `TicketInfoHeader` — number + device + issue
- `Button` — primary, outline

---

## Related Specs

- Quick Repair (trigger): `07-quick-repair.md`
- Voice Intake (trigger): `08-voice-intake.md`
- Payment (trigger): `09-payment-receipt.md`
- Ticket Detail (trigger): `05-ticket-detail.md`
- Success states: `14-success-states.md`
