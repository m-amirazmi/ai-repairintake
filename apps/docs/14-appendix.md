# Appendix

## A. Seed Device Data (Sample)

Full seed catalog contains 50 device models. Here are the first 10 as reference:

```typescript
// packages/shared/constants/devices.ts

export const SEED_DEVICES = [
  // ─── iPhones ───
  {
    brand: "Apple",
    model: "iPhone 14 Pro",
    variants: ["Deep Purple", "Gold", "Silver", "Space Black"],
    model_numbers: ["A2890", "A2891", "A2892", "A2893"],
    category: "smartphone",
    release_year: 2022,
    common_issues: ["screen", "battery", "charging", "camera", "audio", "water", "motherboard", "back_glass"]
  },
  {
    brand: "Apple",
    model: "iPhone 14 Pro Max",
    variants: ["Deep Purple", "Gold", "Silver", "Space Black"],
    model_numbers: ["A2894", "A2895", "A2896"],
    category: "smartphone",
    release_year: 2022,
    common_issues: ["screen", "battery", "charging", "camera", "audio", "water", "motherboard", "back_glass"]
  },
  {
    brand: "Apple",
    model: "iPhone 14",
    variants: ["Midnight", "Starlight", "Blue", "Purple", "Red"],
    model_numbers: ["A2649", "A2881", "A2883", "A2884"],
    category: "smartphone",
    release_year: 2022,
    common_issues: ["screen", "battery", "charging", "camera", "audio", "water", "motherboard", "back_glass"]
  },
  {
    brand: "Apple",
    model: "iPhone 13 Pro",
    variants: ["Graphite", "Gold", "Silver", "Sierra Blue"],
    model_numbers: ["A2638", "A2639", "A2640"],
    category: "smartphone",
    release_year: 2021,
    common_issues: ["screen", "battery", "charging", "camera", "audio", "water", "motherboard", "back_glass"]
  },
  // ─── Samsung Galaxy S Series ───
  {
    brand: "Samsung",
    model: "Galaxy S24 Ultra",
    variants: ["Titanium Gray", "Titanium Black", "Titanium Violet", "Titanium Yellow"],
    model_numbers: ["SM-S928B", "SM-S928U", "SM-S9280"],
    category: "smartphone",
    release_year: 2024,
    common_issues: ["screen", "battery", "charging", "camera", "audio", "water", "motherboard", "back_glass"]
  },
  {
    brand: "Samsung",
    model: "Galaxy S23 Ultra",
    variants: ["Phantom Black", "Green", "Cream", "Lavender"],
    model_numbers: ["SM-S918B", "SM-S918U"],
    category: "smartphone",
    release_year: 2023,
    common_issues: ["screen", "battery", "charging", "camera", "audio", "water", "motherboard", "back_glass"]
  },
  // ─── Samsung Galaxy A Series ───
  {
    brand: "Samsung",
    model: "Galaxy A54",
    variants: ["Awesome Lime", "Awesome Graphite", "Awesome Violet", "Awesome White"],
    model_numbers: ["SM-A546B", "SM-A546E"],
    category: "smartphone",
    release_year: 2023,
    common_issues: ["screen", "battery", "charging", "camera", "audio", "water", "motherboard"]
  },
  {
    brand: "Samsung",
    model: "Galaxy A15",
    variants: ["Blue Black", "Light Blue", "Yellow"],
    model_numbers: ["SM-A156B", "SM-A156E"],
    category: "smartphone",
    release_year: 2024,
    common_issues: ["screen", "battery", "charging", "camera", "audio", "water"]
  },
  // ... up to 50 devices total
];
```

## B. Seed Issue Types

```typescript
// packages/shared/constants/issues.ts

export const SEED_ISSUES = [
  {
    slug: "screen_replacement",
    name: "Penukaran Skrin",
    name_en: "Screen Replacement",
    category: "screen",
    description: "Penukaran skrin penuh (OLED/LCD + digitizer). Termasuk ujian sentuh selepas pemasangan.",
    symptoms: [
      "skrin pecah", "skrin retak", "sentuh tak berfungsi",
      "warna pelik", "garis pada skrin", "skrin gelap tapi telefon hidup",
      "LCD bleeding", "dead pixels"
    ],
    typical_parts: [
      { name: "Skrin OLED + Digitizer", name_en: "OLED + Digitizer Assembly" },
      { name: "Gam Perekat", name_en: "Adhesive" }
    ]
  },
  {
    slug: "battery_replacement",
    name: "Penukaran Bateri",
    name_en: "Battery Replacement",
    category: "battery",
    description: "Penukaran bateri dalaman. Termasuk ujian kesihatan bateri selepas pemasangan.",
    symptoms: [
      "bateri cepat habis", "telefon mati tiba-tiba",
      "bateri kembung", "percentage tak tepat",
      "perlukan cas sepanjang masa", "telefon panas semasa cas"
    ],
    typical_parts: [
      { name: "Bateri", name_en: "Battery Pack" },
      { name: "Pelekat Bateri", name_en: "Battery Adhesive Strips" }
    ]
  },
  {
    slug: "charging_port",
    name: "Port Pengecas / Flex Cable",
    name_en: "Charging Port / Flex Cable",
    category: "charging",
    description: "Pembaikan atau penukaran port pengecas. Termasuk ujian pengecasan selepas pemasangan.",
    symptoms: [
      "tak boleh cas", "cas lambat", "kabel longgar",
      "cas terputus-putus", "tak mengesan cas",
      "perlu adjust kabel untuk cas"
    ],
    typical_parts: [
      { name: "Port Pengecas Flex Cable", name_en: "Charging Port Flex Cable" },
      { name: "Skru", name_en: "Screws" }
    ]
  },
  {
    slug: "rear_camera",
    name: "Kamera Belakang",
    name_en: "Rear Camera",
    category: "camera",
    description: "Penukaran modul kamera belakang. Termasuk ujian kamera selepas pemasangan.",
    symptoms: [
      "kamera kabur", "kamera hitam", "goyang kamera",
      "tak boleh fokus", "gambar ada tompok", "flash tak berfungsi"
    ],
    typical_parts: [
      { name: "Modul Kamera Belakang", name_en: "Rear Camera Module" }
    ]
  },
  {
    slug: "front_camera",
    name: "Kamera Depan / Face ID",
    name_en: "Front Camera / Face ID",
    category: "camera",
    description: "Penukaran kamera depan atau sensor Face ID. Termasuk ujian selepas pemasangan.",
    symptoms: [
      "Face ID tak berfungsi", "kamera depan hitam",
      "True Tone hilang", "eartopus tak berfungsi",
      "sensor proximity rosak"
    ],
    typical_parts: [
      { name: "Modul Kamera Depan + Sensor", name_en: "Front Camera Sensor Assembly" }
    ]
  },
  {
    slug: "speaker_microphone",
    name: "Speaker / Mikrofon",
    name_en: "Speaker / Microphone",
    category: "audio",
    description: "Pembaikan atau penukaran komponen audio.",
    symptoms: [
      "suara tak keluar", "suara pecah", "panggilan tak dengar",
      "mikrofon tak berfungsi", "bunyi gletar",
      "loudspeaker rosak", "earpiece rosak"
    ],
    typical_parts: [
      { name: "Speaker Earpiece", name_en: "Earpiece Speaker" },
      { name: "Loudspeaker", name_en: "Loudspeaker" },
      { name: "Mikrofon Flex", name_en: "Microphone Flex Cable" }
    ]
  },
  {
    slug: "water_damage",
    name: "Kerosakan Air",
    name_en: "Water Damage",
    category: "water",
    description: "Rawatan kerosakan akibat air. Termasuk pembersihan ultrasonik dan pemeriksaan komponen.",
    symptoms: [
      "terjatuh dalam air", "telefon basah",
      "tak boleh hidup selepas hujan",
      "karat pada port", "LDI merah"
    ],
    typical_parts: [
      { name: "Pembersihan Ultrasonik", name_en: "Ultrasonic Cleaning" },
      { name: "Rawatan Karat", name_en: "Corrosion Treatment" }
    ]
  },
  {
    slug: "motherboard",
    name: "Motherboard / IC",
    name_en: "Motherboard / IC Repair",
    category: "motherboard",
    description: "Pembaikan motherboard dan penggantian IC. Memerlukan microsoldering.",
    symptoms: [
      "telefon mati total", "tak boleh hidup langsung",
      "restart berulang", "hang logo",
      "panas berlebihan", "tiada isyarat", "IMEI null"
    ],
    typical_parts: [
      { name: "IC", name_en: "Various ICs" },
      { name: "Kapasitor", name_en: "Capacitors" },
      { name: "Thermal Paste", name_en: "Thermal Paste" },
      { name: "Flux", name_en: "Flux" }
    ]
  },
  {
    slug: "back_glass",
    name: "Kaca Belakang",
    name_en: "Back Glass",
    category: "screen",
    description: "Penukaran kaca belakang peranti. Mungkin menjejaskan wireless charging jika ada.",
    symptoms: [
      "kaca belakang pecah", "kaca belakang retak",
      "wireless charging tak berfungsi"
    ],
    typical_parts: [
      { name: "Panel Kaca Belakang", name_en: "Back Glass Panel" },
      { name: "Gam Perekat", name_en: "Adhesive" }
    ]
  }
];
```

## C. Environment Variables Template

```bash
# .env.example

# ==================== DATABASE ====================
DATABASE_URL=postgresql://postgres:[PASSWORD]@db.[PROJECT_REF].supabase.co:6543/postgres
DIRECT_URL=postgresql://postgres:[PASSWORD]@db.[PROJECT_REF].supabase.co:5432/postgres

# ==================== SUPABASE ====================
NEXT_PUBLIC_SUPABASE_URL=https://[PROJECT_REF].supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJ...[ANON-KEY]
SUPABASE_SERVICE_ROLE_KEY=eyJ...[SERVICE-ROLE-KEY]

# ==================== OPENAI ====================
OPENAI_API_KEY=sk-[YOUR-KEY]
OPENAI_MODEL_VISION=gpt-4o
OPENAI_MODEL_TEXT=gpt-4o

# ==================== WHATSAPP (360dialog) ====================
WHATSAPP_API_URL=https://waba.360dialog.io/v1
WHATSAPP_API_KEY=[YOUR-API-KEY]
WHATSAPP_PHONE_NUMBER_ID=[PHONE-NUMBER-ID]
WHATSAPP_BUSINESS_ACCOUNT_ID=[BUSINESS-ACCOUNT-ID]

# ==================== APP ====================
NEXT_PUBLIC_APP_URL=http://localhost:3000
NEXT_PUBLIC_API_URL=http://localhost:3000/api

# ==================== STRIPE (Month 6) ====================
STRIPE_SECRET_KEY=sk_test_[KEY]
STRIPE_WEBHOOK_SECRET=whsec_[KEY]
STRIPE_PRICE_BASIC=price_[ID]
STRIPE_PRICE_PRO=price_[ID]
```

## D. Core Malay Translations

| Key | Malay (Bahasa Melayu) | English |
|---|---|---|
| `app.name` | Repair Intake | Repair Intake |
| `app.tagline` | Sistem Intake Pembaikan AI | AI-Powered Repair Intake System |
| `auth.login` | Log Masuk | Login |
| `auth.logout` | Keluar | Logout |
| `auth.email` | Emel | Email |
| `auth.password` | Kata Laluan | Password |
| `ticket.new` | Tiket Baharu | New Ticket |
| `ticket.receive` | Terima Peranti | Receive Device |
| `ticket.submit` | Hantar ke Teknikal | Submit to Technician |
| `ticket.assess` | Penilaian | Assessment |
| `ticket.diagnosis` | Diagnosis | Diagnosis |
| `ticket.parts` | Bahagian | Parts |
| `ticket.labor` | Upah | Labor |
| `ticket.total` | Jumlah Anggaran | Total Estimate |
| `ticket.timeline` | Tempoh | Timeline |
| `device.photo_hint` | Ambil gambar bahagian belakang peranti | Take photo of device back |
| `device.confidence` | Keyakinan | Confidence |
| `device.confirm` | Betul | Correct |
| `device.manual_select` | Pilih manual | Select manually |
| `customer.name` | Nama Pelanggan | Customer Name |
| `customer.phone` | Nombor Telefon | Phone Number |
| `customer.notes` | Nota Pelanggan | Customer Notes |
| `customer.notes_hint` | Apakah yang pelanggan katakan? | What did the customer say? |
| `status.received` | Diterima | Received |
| `status.assessing` | Sedang Dinilai | Assessing |
| `status.assessed` | Selesai Dinilai | Assessed |
| `status.approved` | Diluluskan | Approved |
| `status.in_progress` | Dalam Proses | In Progress |
| `status.completed` | Selesai | Completed |
| `status.cancelled` | Dibatalkan | Cancelled |
| `status.picked_up` | Diambil | Picked Up |
| `notify.assessment_complete` | Penilaian selesai! Anggaran: RM{price}. Balas YA untuk luluskan. | Assessment complete! Estimate: RM{price}. Reply YES to approve. |
| `notify.ready_for_pickup` | Peranti anda sudah siap! Sila ambil di {outlet}. | Your device is ready! Pick up at {outlet}. |
| `error.offline` | Tiada sambungan internet | No internet connection |
| `error.ai_unavailable` | AI tidak tersedia. Sila pilih manual. | AI unavailable. Please select manually. |
| `error.whatsapp_failed` | Gagal hantar WhatsApp | Failed to send WhatsApp |
| `role.owner` | Pemilik | Owner |
| `role.manager` | Pengurus | Manager |
| `role.front_desk` | Kaunter | Front Desk |
| `role.technician` | Teknikal | Technician |
| `outlet` | Cawangan | Outlet |
| `save` | Simpan | Save |
| `cancel` | Batal | Cancel |
| `edit` | Edit | Edit |
| `delete` | Padam | Delete |
| `search` | Cari | Search |
| `filter` | Tapis | Filter |
| `export` | Eksport | Export |
| `print` | Cetak | Print |
| `loading` | Memuat... | Loading... |
| `success` | Berjaya | Success |
| `error` | Ralat | Error |
| `warning` | Amaran | Warning |
| `yes` | Ya | Yes |
| `no` | Tidak | No |
| `submit` | Hantar | Submit |
| `close` | Tutup | Close |
| `back` | Kembali | Back |
| `next` | Seterusnya | Next |
| `today` | Hari Ini | Today |
| `this_week` | Minggu Ini | This Week |
| `this_month` | Bulan Ini | This Month |
| `revenue` | Jualan | Revenue |
| `tickets` | Tiket | Tickets |
| `completed` | Selesai | Completed |
| `pending` | Menunggu | Pending |
| `report` | Laporan | Report |
| `outlets` | Cawangan | Outlets |
| `staff` | Kakitangan | Staff |
| `devices` | Peranti | Devices |
| `issues` | Masalah | Issues |
| `parts_catalog` | Katalog Bahagian | Parts Catalog |
| `settings` | Tetapan | Settings |
| `help` | Bantuan | Help |
| `estimated` | Anggaran | Estimated |
```

## E. Glossary

| Term | Definition |
|---|---|
| **Tiket** (Ticket) | Unique repair job record. Every device intake creates one ticket. |
| **Penilaian** (Assessment) | Technician's physical inspection, diagnosis, and quote. |
| **Kaunter** (Front Desk) | Non-technical staff who receive devices from customers at the counter. |
| **Teknikal** (Technician) | Staff member who diagnoses and repairs devices. |
| **Pemilik** (Owner) | Business owner with full system access. |
| **Pengurus** (Manager) | Outlet-level manager. |
| **Cawangan** (Outlet) | Physical shop location. |
| **Bahagian** (Part) | Replacement component (screen, battery, port, etc.). |
| **Upah** (Labor) | Work charged for repair, calculated as hours × hourly rate. |
| **Anggaran** (Estimate/Quote) | Total price given to customer before repair begins. |
| **Diluluskan** (Approved) | Customer has agreed to the quoted price. |
| **Dalam Proses** (In Progress) | Repair work has begun. |
| **WhatsApp Business API** | Meta's API for sending automated WhatsApp messages from a business number. |
| **360dialog** | WhatsApp Business Solution Provider (BSP) serving Southeast Asia. |
| **Supabase Realtime** | PostgreSQL LISTEN/NOTIFY-based real-time updates. |
| **EAS** | Expo Application Services — cloud build and update service for Expo apps. |
| **RLS** | Row-Level Security — PostgreSQL feature for fine-grained access control. |
| **PDPA** | Malaysia's Personal Data Protection Act 2010. |
| **LHDN** | Lembaga Hasil Dalam Negeri — Malaysia's Inland Revenue Board. |
