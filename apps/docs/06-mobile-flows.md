> **📁 Active Development Moved:** Mobile UI specs have been broken down into per-feature files in `apps/docs/mobile/`.  
> For screen-level UI specs (layouts, states, components, copy), see the files in that folder. This document is kept for historical reference and full flow narratives including API calls.

---

# Mobile App — Flows & Screens

## App Entry & Auth

```
┌─────────────────────────────────────────┐
│  🔧 Repair Intake                       │
│                                         │
│  Log masuk ke akaun anda                │
│  (Login to your account)                │
│                                         │
│  Emel:  [________________]              │
│  Kata laluan: [________**]              │
│                                         │
│       [  Log Masuk  ]                   │
│                                         │
│  ─────────── atau ───────────           │
│                                         │
│  Lupa kata laluan?                      │
│                                         │
└─────────────────────────────────────────┘
```

Role-based home redirect:

- All roles → Tab: **Utama (Home)** — the intake landing hub with stats, recent tickets, and flow action cards.

---

## Flow A: Front Desk Receives Device (from Home)

Launched from Home "Create Ticket" action card.

```
┌─────────────────────────────────────────┐
│  Batal           Tiket Baharu        Simpan │
│                                         │
│  ═════════════════════════════════════  │
│  ┌─────────────────────────────────┐    │
│  │                                 │    │
│  │      📷  KETUK UNTUK            │    │
│  │         AMBIL GAMBAR            │    │
│  │                                 │    │
│  │   (Bahagian belakang peranti)   │    │
│  │                                 │    │
│  └─────────────────────────────────┘    │
│                                         │
│  ── Peranti dikenal pasti ──            │
│  📱 iPhone 14 Pro                       │
│    Ungu Tua (Deep Purple)               │
│    Keyakinan: 94% ✅                    │
│                                         │
│  [Betul]  [Tidak, pilih manual]         │
│                                         │
│  ── Nota pelanggan ──                   │
│  ┌─────────────────────────────────┐    │
│  │ 🎤 [Tekan untuk rakam]          │    │
│  │ ┌─────────────────────────────┐ │    │
│  │ │ Skrin pecah, sentuh tak     │ │    │
│  │ │ berfungsi...                │ │    │
│  │ └─────────────────────────────┘ │    │
│  │                                 │    │
│  │  Kerosakan dikenal pasti:       │    │
│  │  • Skrin — Pecah (sederhana)    │    │
│  │  • Sentuh — Tidak berfungsi     │    │
│  └─────────────────────────────────┘    │
│                                         │
│  ── Maklumat Pelanggan ──               │
│  Nama:  [Ahmad bin Abdullah     ]       │
│  Telefon: [+6012-345-6789       ]       │
│  Emel:   [opsional              ]       │
│                                         │
│         [HANTAR KE TEKNIKAL]            │
│                                         │
│  (Tiada harga diberikan lagi)           │
│                                         │
└─────────────────────────────────────────┘

After submit:
┌─────────────────────────────────────────┐
│  ✅ Tiket #D1-042 Dicipta               │
│                                         │
│  Peranti: iPhone 14 Pro                 │
│  Pelanggan: Ahmad bin Abdullah          │
│  Cawangan: Dungun 1                     │
│                                         │
│  WhatsApp akan dihantar kepada          │
│  pelanggan: +6012-345-6789              │
│                                         │
│  ┌─────────────────────────────────┐    │
│  │ 📋 Draf AI:                     │    │
│  │ Salam Ahmad, tiket D1-042       │    │
│  │ dicipta. iPhone 14 Pro...       │    │
│  │ [Edit] [Hantar]                 │    │
│  └─────────────────────────────────┘    │
│                                         │
│       [Buka Tiket]  [Tiket Baharu]      │
└─────────────────────────────────────────┘
```

---

## Flow A.5: Photo Quality Gate + Damage Detection

After photo capture, before AI device ID:

```
┌─────────────────────────────────────────┐
│  Tiket Baharu                           │
│                                         │
│  ┌─────────────────────────────────┐    │
│  │  📷 (preview with overlay)      │    │
│  │                                 │    │
│  │  [🟡 Kualiti sederhana]         │    │
│  │  "Cahaya kurang — masih OK"     │    │
│  │                                 │    │
│  │  [✅ Peranti dalam bingkai]     │    │
│  │                                 │    │
│  │  [Ambil Semula]   [Teruskan →]  │    │
│  └─────────────────────────────────┘    │
│                                         │
  → Tekan "Teruskan" → AI Vision runs
│                                         │
│  ── Keadaan Peranti (Sebelum) ──        │
│  🛡️ AI mengesan kerosakan sedia ada:    │
│                                         │
│  ┌─────────────────────────────────┐    │
│  │ 🟡 Goresan halus                │    │
│  │    Lokasi: Panel belakang       │    │
│  │    [Gambar thumbnail]  [✕]      │    │
│  │                                 │    │
│  │ 🔴 Retakan                      │    │
│  │    Lokasi: Skrin                │    │
│  │    Tahap: Sederhana             │    │
│  │    [Gambar thumbnail]  [✕]      │    │
│  │                                 │    │
│  │ ✅ Tiada kerosakan air          │    │
│  │                                 │    │
│  │ [+ Tambah Kerosakan Manual]     │    │
│  └─────────────────────────────────┘    │
│                                         │
│  [✓ Sahkan Keadaan]                     │
│                                         │
└─────────────────────────────────────────┘
```

---

## Flow B: Technician Queue & Assessment

### Queue Screen

```
┌─────────────────────────────────────────┐
│  ☰  Senarai Tugas                 🔔 5  │
│  Dungun 1                    [Tapis ▼]  │
│                                         │
│  ┌── #D1-042 ── iPhone 14 Pro ─── 10m ┐ │
│  │ Diterima | Ahmad bin Abdullah      │ │
│  │ "Skrin pecah, sentuh rosak"        │ │
│  │                               [▶]  │ │
│  └────────────────────────────────────┘ │
│                                         │
│  ┌── #D1-041 ── Samsung A54 ── 1j ────┐ │
│  │ Sedang Dinilai | Amir (Teknikal)   │ │
│  │ "Bateri cepat habis"               │ │
│  └────────────────────────────────────┘ │
│                                         │
│  ┌── #D1-040 ── iPhone 11 ── 2j ──────┐ │
│  │ Selesai Dinilai | Menanti lulus    │ │
│  │ "Penukaran skrin: RM199"           │ │
│  └────────────────────────────────────┘ │
│                                         │
│  ┌── #D1-039 ── iPhone 15 Pro ── 5j ──┐ │
│  │ Dalam Proses | Amir                │ │
│  │ "Penukaran skrin"                  │ │
│  └────────────────────────────────────┘ │
│                                         │
│  [ + Tiket Baharu ]                     │
└─────────────────────────────────────────┘
```

### Assessment Screen (Tap ticket from Home or Queue)

```
┌─────────────────────────────────────────┐
│  ←  Tiket #D1-042 — Penilaian           │
│                                         │
│  ┌── Peranti ──┬── Pelanggan ─────────┐ │
│  │ iPhone 14P  │ Ahmad                │ │
│  │ Ungu Tua    │ +6012-345-6789       │ │
│  │             │                      │ │
│  │ [Lihat      │ [Hubungi di          │ │
│  │  gambar]    │  WhatsApp]           │ │
│  └─────────────┴──────────────────────┘ │
│                                         │
│  Nota pelanggan:                        │
│  🎤 "Skrin pecah, sentuh tak berfungsi" │
│  [Dengar semula]                        │
│                                         │
│  AI extraction:                         │
│  • Kerosakan: Skrin pecah               │
│  • Lokasi: Tidak dinyatakan             │
│  • Tahap: Tidak dinyatakan              │
│  • Simptom: Sentuh tidak berfungsi      │
│                                         │
│  ═══════════════════════════════        │
│  ── CADANGAN DIAGNOSIS ──               │
│  (AI berdasarkan iPhone 14 Pro + nota)  │
│                                         │
│  ┌───────────────────────────────────┐  │
│  │ 💡 CADANGAN PALING MUNGKIN:       │  │
│  │                                   │  │
│  │ [✓] Penukaran Skrin OLED          │  │
│  │     (95% keyakinan)               │  │
│  │     0.75 jam | RM89 (part)        │  │
│  │                                   │  │
│  │ [ ] Kerosakan Digitizer Sahaja    │  │
│  │     (4% keyakinan)                │  │
│  │                                   │  │
│  │ [ ] Kerosakan Motherboard         │  │
│  │     (1% keyakinan)                │  │
│  └───────────────────────────────────┘  │
│                                         │
│  Atau taip diagnosis sendiri:           │
│  ┌─────────────────────────────────┐    │
│  │ Cari atau taip diagnosis...     │    │
│  └─────────────────────────────────┘    │
│                                         │
│  ═══════════════════════════════        │
│  ── BAHAGIAN ──                         │
│  ┌─────────────────────────────────┐    │
│  │ Skrin OLED + Digitizer   x1     │    │
│  │          Kos: RM89  Harga: RM179│    │
│  └─────────────────────────────────┘    │
│  ┌─────────────────────────────────┐    │
│  │ Gam perekat              x1     │    │
│  │          Kos: RM5   Harga: RM10 │    │
│  └─────────────────────────────────┘    │
│  [+ Tambah bahagian]                    │
│                                         │
│  ── UPAH ──                             │
│  ┌─────────────────────────────────┐    │
│  │ Masa:   [ 0.75 ] jam            │    │
│  │ Kadar:  RM50.00 / jam           │    │
│  │ Jumlah: RM37.50                 │    │
│  └─────────────────────────────────┘    │
│                                         │
│  ── JUMLAH ──                           │
│  ┌─────────────────────────────────┐    │
│  │ Bahagian:       RM 94.00        │    │
│  │ Upah:           RM 37.50        │    │
│  │ Tambahan: [____] RM 0.00        │    │
│  │                ──────────       │    │
│  │ ANGGARAN:       RM 131.50       │    │
│  └─────────────────────────────────┘    │
│                                         │
│  ── TEMPOH ──                           │
│  ● 30-45 minit (tunggu)                 │
│  ○ 1-3 hari                             │
│  ○ 3-7 hari                             │
│                                         │
│  Nota teknikal:                         │
│  ┌─────────────────────────────────┐    │
│  │ Skrin pecah di penjuru kanan    │    │
│  │ bawah. Tiada kerosakan LCD.     │    │
│  │ Sentuh berfungsi 80%.           │    │
│  └─────────────────────────────────┘    │
│                                         │
│  ┌────────────┐  ┌──────────────────┐   │
│  │ SIMPAN     │  │ HANTAR & NOTIFY  │   │
│  │ DRAF       │  │ (WhatsApp)       │   │
│  └────────────┘  └──────────────────┘   │
│                                         │
└─────────────────────────────────────────┘
```

---

## Flow C: Device Dead / Unknown Diagnosis (from Assessment Edge-Case Trigger)

Reached via the edge-case trigger card on the Assessment screen (`06-assessment.md` §Section 2.5).

```
┌─────────────────────────────────────────┐
│  ←  Tiket #D1-045 — Penilaian           │
│                                         │
│  ⚠️ PERANTI TIDAK BOLEH DIHIDUPKAN      │
│                                         │
│  Diagnosis tidak boleh dibuat tanpa     │
│  pemeriksaan fizikal.                   │
│                                         │
│  Nota pelanggan: "Bateri rosak"         │
│  Nota kaunter: "Peranti mati, tak       │
│  boleh hidupkan"                        │
│                                         │
│  ── PILIHAN ──                          │
│  ┌─────────────────────────────────┐    │
│  │ [1] YURAN PEMERIKSAAN: RM29     │    │
│  │     Periksa peranti. Anggaran   │    │
│  │     akan dihantar kemudian.     │    │
│  │                                 │    │
│  │ [2] TUNGGU KELULUSAN            │    │
│  │     Maklumkan pelanggan dulu.   │    │
│  └─────────────────────────────────┘    │
│                                         │
│  ┌─────────────────────────────────┐    │
│  │ [SIMPAN SEBAGAI "SEDANG DINILAI"]│   │
│  └─────────────────────────────────┘    │
└─────────────────────────────────────────┘

After physical inspection:
┌─────────────────────────────────────────┐
│  ←  Tiket #D1-045 — Penilaian Lanjut    │
│                                         │
│  ── DAPATAN TEKNIKAL ──                 │
│  (Selepas pemeriksaan fizikal)          │
│                                         │
│  💡 CADANGAN AI (dikemaskini):          │
│  Berdasarkan iPhone 12 + "mati":        │
│  [ ] Kerosakan Bateri (30%)             │
│  [ ] Port Pengecas (15%)                │
│  [✓] Short Motherboard (45%)            │
│  [ ] Butang Power (10%)                 │
│                                         │
│  Diagnosis sebenar:                     │
│  ┌─────────────────────────────────┐    │
│  │ ✓ Kerosakan Motherboard         │    │
│  │   Capacitor terbakar (C321)     │    │
│  │   berhampiran CPU power rail    │    │
│  └─────────────────────────────────┘    │
│                                         │
│  ⚠️ PERUBAHAN HARGA BESAR               │
│  ┌─────────────────────────────────┐    │
│  │ Yuran semakan:       RM 29.00   │    │
│  │ Anggaran baru:       RM 289.00  │    │
│  │                                 │    │
│  │ Pelanggan perlu luluskan        │    │
│  │ semula.                         │    │
│  └─────────────────────────────────┘    │
│                                         │
│  [SIMPAN & HANTAR NOTIFIKASI]           │
└─────────────────────────────────────────┘
```

---

## Flow D: Technician Does Everything (No Handoff)

After staff (who is a technician) submits a new ticket:

```
┌─────────────────────────────────────────┐
│  ✅ Tiket #D1-042 Dicipta               │
│                                         │
│  ┌─────────────────────────────────┐    │
│  │  ASSESS SEKARANG?               │    │
│  │                                 │    │
│  │  Anda adalah teknikal.          │    │
│  │  Nilaikan peranti ini sekarang? │    │
│  │                                 │    │
│  │  [YA, NILAIKAN]                 │    │
│  │  [KEMUDIAN — tambah ke senarai] │    │
│  └─────────────────────────────────┘    │
│                                         │
│  → Tekan "YA" → Langsung ke             │
│    Assessment Screen (Flow B)           │
│                                         │
│  → Tekan "KEMUDIAN" → Kembali ke        │
│    Queue. Tiket dalam senarai.          │
└─────────────────────────────────────────┘
```

---

## Flow E: WhatsApp Conversation (Customer's Phone)

```
┌─────────────────────────────────────────┐
│  WHATSAPP CHAT                          │
│                                         │
│  ── Repair Intake ── 10:32 AM           │
│  👋 Salam Ahmad,                        │
│                                         │
│  Tiket #D1-042 telah dicipta.           │
│  Peranti: iPhone 14 Pro                 │
│  Cawangan: Dungun 1                     │
│                                         │
│  Kami akan nilaikan dan hubungi anda    │
│  sebentar lagi.                         │
│                                         │
│  ── Repair Intake ── 11:52 AM           │
│  🔧 Penilaian selesai!                  │
│                                         │
│  Peranti: iPhone 14 Pro                 │
│  Masalah: Penukaran skrin               │
│  Anggaran: RM132                        │
│  Tempoh: 1-3 hari                       │
│                                         │
│  Balas **YA** untuk luluskan.           │
│  Balas **TIDAK** untuk batal.           │
│                                         │
│  ── Anda ── 11:55 AM                    │
│  YA                                     │
│                                         │
│  ── Repair Intake ── 11:55 AM           │
│  ✅ Diterima! Pembaikan sedang          │
│  dijalankan. Kami akan hubungi anda     │
│  apabila siap.                          │
│                                         │
│  ── Repair Intake ── 1:47 PM            │
│  🎉 Peranti anda sudah siap!            │
│                                         │
│  iPhone 14 Pro — Penukaran skrin selesai│
│                                         │
│  Sila ambil di:                         │
│  Dungun 1, No. 23, Jalan Besar          │
│  Waktu operasi: 9 pagi - 7 malam        │
│                                         │
│  [Tunjuk tiket →]                       │
│                                         │
│  ── 3 hari kemudian ──                  │
│  ── Repair Intake ── 10:00 AM           │
│  ⏰ Peringatan: Peranti iPhone 14 Pro   │
│  anda masih belum diambil.              │
│  Tarikh akhir ambil: 28 Mei 2026        │
│                                         │
└─────────────────────────────────────────┘
```

---

## Flow F: Quick Repair (Technician at Counter, from Home)

Launched from Home "Quick Repair" action card.

```
┌─────────────────────────────────────────┐
│  Batal           Baiki Cepat              │
│                                         │
│  ── Peranti ──                          │
│  ┌─────────────────────────────────┐    │
│  │ 🔍 iPh                           │    │
│  │    ↓                             │    │
│  │ 📱 iPhone 14 Pro                 │    │
│  │ 📱 iPhone 14 Pro Max             │    │
│  │ 📱 iPhone 14                     │    │
│  │    (typeahead, 2-3 chars)        │    │
│  └─────────────────────────────────┘    │
│                                         │
│  ── Masalah ──                          │
│  ┌─────────────────────────────────┐    │
│  │ 📋 Penukaran Skrin OLED    ▼    │    │
│  │     RM350 | 0.75 jam            │    │
│  │                                 │    │
│  │ 📋 Penukaran Bateri       ▼    │    │
│  │     RM150 | 0.5 jam             │    │
│  │                                 │    │
│  │ 📋 Port Pengecas           ▼    │    │
│  │     RM89  | 0.5 jam             │    │
│  │    (top-10 common issues)       │    │
│  └─────────────────────────────────┘    │
│                                         │
│  ── Pelanggan (Pilihan) ──              │
│  Nama:     [           ]                │
│  Telefon:  [+60_______ ]                │
│  (Jika kosong, resit dengan kod QR)     │
│                                         │
│  ── Harga ──                            │
│  ┌─────────────────────────────────┐    │
│  │ Bahagian (1):     RM 339.00     │    │
│  │ Upah (0.75j):     RM  11.00     │    │
│  │                   ────────      │    │
│  │ JUMLAH:           RM 350.00     │    │
│  │ [edit jika perlu]              │    │
│  └─────────────────────────────────┘    │
│                                         │
│  ┌─────────────────────────────────┐    │
│  │    [HANTAR & CETAK RESIT]       │    │
│  └─────────────────────────────────┘    │
│                                         │
│  Pembaikan akan dimulakan serta-merta.  │
└─────────────────────────────────────────┘

After submit (with phone):
┌─────────────────────────────────────────┐
│  ✅ Tiket #D1-043 — Baiki Cepat         │
│                                         │
│  iPhone 14 Pro — Penukaran Skrin        │
│  Harga: RM350                           │
│                                         │
│  WhatsApp resit dihantar ke             │
│  +6012-345-6789                         │
│                                         │
│  Status: Dalam Proses                   │
│  Anggaran siap: 3:30 PM                 │
│                                         │
│       [Buka Tiket]   [Baiki Cepat Baru] │
└─────────────────────────────────────────┘

After submit (no phone):
┌─────────────────────────────────────────┐
│  ✅ Tiket #D1-043 — Baiki Cepat         │
│                                         │
│  iPhone 14 Pro — Penukaran Skrin        │
│  Harga: RM350                           │
│                                         │
│  ┌─────────────────────────────────┐    │
│  │                                 │    │
│  │        ████████████████         │    │
│  │        ██  QR CODE  ██         │    │
│  │        ████████████████         │    │
│  │                                 │    │
│  │   Imbas untuk kemaskini         │    │
│  │   WhatsApp & status             │    │
│  │                                 │    │
│  └─────────────────────────────────┘    │
│                                         │
│  Status: Dalam Proses                   │
│                                         │
│       [Buka Tiket]   [Baiki Cepat Baru] │
└─────────────────────────────────────────┘
```

---

## Flow G: Voice Intake (from Quick Repair)

Launched from Quick Repair header mic button or New Ticket customer notes mic.

```
┌─────────────────────────────────────────┐
│  Batal           Ambil Suara            │
│                                         │
│  ┌─────────────────────────────────┐    │
│  │                                 │    │
│  │           🎤                    │    │
│  │                                 │    │
│  │    Ketuk untuk rakam            │    │
│  │                                 │    │
│  │  "iPhone 14 Pro skrin pecah,    │    │
│  │   Ahmad, tiga ratus lima puluh" │    │
│  │                                 │    │
│  └─────────────────────────────────┘    │
│                                         │
│  ── Sedang mendengar... ──              │
│  ┌─────────────────────────────────┐    │
│  │  🔴 Merakam...  00:04           │    │
│  │  ─────────────────────────────   │    │
│  └─────────────────────────────────┘    │
│                                         │
│  ── Dikenal pasti ──                    │
│  ┌─────────────────────────────────┐    │
│  │ ✅ Peranti:  iPhone 14 Pro       │    │
│  │ ✅ Masalah:  Skrin pecah         │    │
│  │ ✅ Pelanggan: Ahmad              │    │
│  │ ✅ Harga:    RM350               │    │
│  │                                  │    │
│  │ Keyakinan: 91%                   │    │
│  │                                  │    │
│  │ "iPhone 14 Pro skrin pecah,      │    │
│  │  Ahmad, tiga ratus lima puluh"   │    │
│  └─────────────────────────────────┘    │
│                                         │
│  ┌─────────────────────────────────┐    │
│  │    [HANTAR TIKET]               │    │
│  └─────────────────────────────────┘    │
│                                         │
│  [Rakam Semula]                         │
└─────────────────────────────────────────┘

Accessible from:
  - Quick Repair screen (🎤 button in header)
  - Full AI New Ticket screen (🎤 button in customer notes section)
```

---

## Flow H: Payment & Receipt (at Completion)

```
┌─────────────────────────────────────────┐
│  ←  Tiket #D1-043 — Pembayaran          │
│                                         │
│  ┌─────────────────────────────────┐    │
│  │ iPhone 14 Pro                   │    │
│  │ Penukaran Skrin OLED            │    │
│  │                                 │    │
│  │ Bahagian (1):    RM 339.00      │    │
│  │ Upah (0.75j):   RM  11.00       │    │
│  │                   ────────      │    │
│  │ JUMLAH:          RM 350.00      │    │
│  └─────────────────────────────────┘    │
│                                         │
│  ── Bayaran ──                          │
│  ┌─────────────────────────────────┐    │
│  │ Amaun:  [ 350.00 ]              │    │
│  │                                 │    │
│  │ Kaedah:                         │    │
│  │  ○ Tunai                        │    │
│  │  ● QR Pay                       │    │
│  │  ○ Bank Transfer                │    │
│  │                                 │    │
│  │ Nota: [____________________]    │    │
│  └─────────────────────────────────┘    │
│                                         │
│  ┌─────────────────────────────────┐    │
│  │  [SAHKAN BAYARAN & SELESAI]     │    │
│  └─────────────────────────────────┘    │
│                                         │
│  Stok dikemaskini: -1 Skrin OLED        │
└─────────────────────────────────────────┘

After payment (phone exists):
┌─────────────────────────────────────────┐
│  ✅ Pembayaran Diterima                 │
│                                         │
│  RM350 — QR Pay                         │
│  Tiket #D1-043 — Selesai                │
│                                         │
│  WhatsApp resit dihantar kepada         │
│  +6012-345-6789                         │
│                                         │
│  ┌─────────────────────────────────┐    │
│  │ 📋 Resit WhatsApp:              │    │
│  │                                  │    │
│  │ 🔧 Repair Intake                │    │
│  │ Tiket #D1-043                   │    │
│  │ iPhone 14 Pro — Penukaran Skrin │    │
│  │ Jumlah: RM350 (QR Pay)          │    │
│  │ Dungun 1                        │    │
│  │ Terima kasih!                   │    │
│  └─────────────────────────────────┘    │
│                                         │
│       [Kembali ke Senarai]              │
└─────────────────────────────────────────┘

After payment (no phone — QR receipt):
┌─────────────────────────────────────────┐
│  ✅ Pembayaran Diterima                 │
│                                         │
│  ┌─────────────────────────────────┐    │
│  │                                 │    │
│  │        ████████████████         │    │
│  │        ██  QR CODE  ██         │    │
│  │        ████████████████         │    │
│  │                                 │    │
│  │   Tunjukkan QR ini kepada       │    │
│  │   pelanggan                     │    │
│  │                                 │    │
│  └─────────────────────────────────┘    │
│                                         │
│       [Kembali ke Senarai]              │
└─────────────────────────────────────────┘
```

---

## Flow I: Public Ticket Web View (Customer QR Link)

```
Customer scans QR code → opens repairintake.my/t/abc123xy

┌─────────────────────────────────────────┐
│  🔧 Repair Intake                       │
│                                         │
│  ┌─────────────────────────────────┐    │
│  │                                 │    │
│  │ Tiket #D1-043                   │    │
│  │ iPhone 14 Pro                   │    │
│  │ Penukaran Skrin OLED            │    │
│  │                                 │    │
│  │ Status: ✅ Selesai              │    │
│  │ Cawangan: Dungun 1              │    │
│  │ No. 23, Jalan Besar             │    │
│  │                                 │    │
│  └─────────────────────────────────┘    │
│                                         │
│  ── Mahu kemaskini WhatsApp? ──         │
│  ┌─────────────────────────────────┐    │
│  │ Nama:    [Ahmad           ]     │    │
│  │ Telefon: [+6012-345-6789 ]     │    │
│  │                                 │    │
│  │ [Langgan WhatsApp]              │    │
│  └─────────────────────────────────┘    │
│                                         │
│  Kami akan hantar kemaskini status      │
│  melalui WhatsApp.                      │
└─────────────────────────────────────────┘

After subscribing:
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

## Navigation Structure (Updated)

```
┌──────────────────────────────────────────────────────────────┐
│                                                              │
│   ┌────┐  ┌────┐  ┌────────┐  ┌────────┐  ┌──────┐          │
│   │ 🏠 │  │ 📋 │  │  🔧    │  │  📊    │  │ ⚙️   │          │
│   │Home│  │Jobs│  │Assess  │  │History │  │Profile│         │
│   └────┘  └────┘  └────────┘  └────────┘  └──────┘          │
│                                                              │
│  Tab 1: UTAMA / HOME (default after login)                   │
│  ├── Welcome + stats row                                     │
│  ├── Flow action cards: Create Ticket | Assessment | Quick   │
│  ├── Recent tickets preview                                  │
│  └── Partner/branch selector                                 │
│                                                              │
│  Tab 2: TUGAS / JOBS                                         │
│  ├── Today's queue (live list)                               │
│  ├── Filter chips: Semua | Diterima | Sedang Dinilai | Selesai│
│  └── My assigned tickets (technician)                        │
│                                                              │
│  Tab 3: NILAI / ASSESS (technicians only)                    │
│  └── Tickets waiting for assessment                          │
│                                                              │
│  Tab 4: SEJARAH / HISTORY                                    │
│  ├── Completed tickets                                       │
│  ├── Search by ticket #, customer, device                    │
│  └── Date filter                                             │
│                                                              │
│  Tab 5: PROFIL / PROFILE                                     │
│  ├── Name, role, outlet                                      │
│  ├── Switch outlet (managers only)                           │
│  ├── Language toggle (BM / EN)                               │
│  └── Logout                                                  │
└──────────────────────────────────────────────────────────────┘
```

## Key Design Patterns (Updated)

1. **Home-first navigation**: The Home / Utama tab is the post-login landing with three action cards (Create Ticket, Assessment, Quick Repair) as the canonical entry points for all intake flows. The Nilai (Assess) tab remains for technician convenience.

2. **Camera-first**: The camera capture area is the most prominent element on the new ticket screen. No hunting through menus.

3. **Suggestion chips**: AI suggestions are tappable pills, not dropdown menus. Quick Repair uses typeahead + quick-select, Voice uses speech. Faster than typing or scrolling.

4. **Confidence display**: Every AI result shows a confidence percentage. Staff know when to trust or override.

5. **Autosave**: Assessment drafts autosave every 30 seconds. No lost work if the app backgrounds.

6. **Pull-to-refresh → Real-time**: Initially pull-to-refresh the queue. Upgrade to Supabase Realtime subscriptions (Month 3) for live updates without user action.

7. **Offline queue**: If no internet, tickets queue locally in AsyncStorage and sync when connection returns.

8. **QR receipt for anonymous repairs**: When no phone number provided, a QR code links to a public ticket page. Customer can subscribe to WhatsApp updates at any time via the QR link.
