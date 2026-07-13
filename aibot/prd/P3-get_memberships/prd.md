# Membership Aktif (`get_memberships`) — PRD

## Overview

Fitur membership aktif membantu customer mengetahui paket member yang tersedia di outlet yang dipilih. Customer bertanya dalam bahasa natural, bot fetch daftar membership dari endpoint, lalu tampilkan ringkas: nama member, biaya daftar, dan masa aktif.

Tujuan: mengurangi pertanyaan repetitif "ada member apa?", "biaya member berapa?", dan "member berlaku berapa lama?" ke CS.

---

## User Stories

- As a **customer**, saya ingin **tanya pilihan member dalam bahasa natural** agar **tahu membership yang bisa saya daftar**.
- As a **customer**, saya ingin **lihat biaya daftar member** agar **bisa menyiapkan biaya sebelum datang/daftar**.
- As a **customer**, saya ingin **lihat masa aktif member** agar **paham durasi benefit member**.
- As a **owner**, saya ingin **bot hanya menampilkan membership aktif** agar **customer tidak melihat paket yang sudah nonaktif**.
- As a **customer**, saya ingin **tanya detail member tertentu** agar **tahu deskripsi, lokasi berlaku, dan informasi lengkapnya tanpa perlu tanya agen**.

---

## Goals & Non-Goals

### Goals

- Intent `get_memberships` aktif per outlet (toggleable via Control Panel, default ON).
- Slot wajib: **tidak ada** (outlet sudah di context). Bot langsung fetch tanpa perlu tanya parameter tambahan.
- Tampilkan **nama member**, **biaya daftar**, dan **masa aktif** untuk setiap membership aktif.
- Jika data membership dari API kosong → bot sampaikan bahwa saat ini belum ada membership aktif untuk outlet di conversation context.
- Jika API error → fallback escalate ke agen.
- Lookup dibatasi outlet dari conversation context.
- Detail membership bisa ditampilkan berdasarkan nama membership dari customer.
- Bahasa Indonesia, tone "Kak", format nominal rupiah Indonesia.
- Patuh hierarki master switch + per-outlet intent toggle.

### Non-Goals (MVP)

- Registrasi membership langsung dari bot.
- Pembayaran biaya daftar membership dari bot.
- Cek status membership customer yang sudah terdaftar.
- Menampilkan daftar customer pengguna membership.
- Menampilkan benefit promo turunan dari membership, kecuali tersedia sebagai deskripsi/detail dari API.
- Bot menentukan apakah customer eligible daftar member. Keputusan final tetap di tangan customer & CS.
- Multi-language.

---

## Flow

### Alur Utama

1. Customer kirim pesan di suatu outlet (outlet & WABA sudah di context).
2. Master switch WABA check — OFF → silent handoff ke agent.
3. Per-outlet intent toggle `get_memberships` check — OFF → "Fitur ini sedang tidak tersedia di outlet ini".
4. LLM klasifikasi intent:
   - `get_memberships` → step 5a.
   - `get_detail_membership` → sub-flow detail membership.
   - other → handle intent masing-masing.
5a. Fetch daftar membership untuk outlet tsb dari API.
6a. Filter response API: hanya membership `active = true` dan relevan untuk outlet ini.
7a. Handling:
   - Ada membership → tampilkan sesuai Display Rules.
   - Data kosong → bot sampaikan bahwa saat ini belum ada membership aktif untuk outlet di conversation context.
   - API error → escalate ke agent.
8a. Tanya "Ada yang ingin ditanyakan lagi?"

### Sub-Flow Detail Membership

5b. Customer menyebut nama membership spesifik. Bot cari `id` dari daftar membership aktif (hasil fetch di sesi ini).
   - Jika daftar membership aktif belum di-fetch di sesi ini → fetch dulu.
   - Jika nama tidak ditemukan → "Maaf Kak, membership dengan nama tersebut tidak ditemukan."
6b. Fetch detail membership dari API.
7b. Handling:
   - Sukses → tampilkan detail membership (lihat Display Rules — Detail Membership).
   - 404 → "Maaf Kak, membership sudah tidak tersedia."
   - API error → escalate ke agent.
8b. Tanya "Ada yang ingin ditanyakan lagi?"

---

## Display Rules

- Bot tidak menampilkan raw response API ke customer.
- Filter response: hanya membership aktif dan relevan untuk outlet ini.
- Maksimal 5 membership ditampilkan dalam satu respons. Jika ada 6 membership aktif, tampilkan 5 item lalu tambah "dan 1 membership lainnya" di akhir. Sesuaikan angka dengan sisa item yang tidak ditampilkan.
- Format per membership: **nama membership** + biaya daftar + masa aktif.
- Field yang tidak ditampilkan di list: identifier internal membership, created_by, created_at, updated_at, count_customer_usage, count_promo, status internal.

### Biaya Daftar

- `register_fee = 0` → "Biaya daftar: Gratis"
- `register_fee > 0` → tampilkan biaya daftar dalam format rupiah Indonesia, misalnya "Biaya daftar: Rp1.500"
- Jika detail punya `register_fee_enable = false` → "Biaya daftar: Gratis" atau skip jika API tidak memberi biaya valid.

### Masa Aktif

Generate label customer-friendly dari `period_type` / `active_period_type`, `active_period_value`, dan `active_period_day`.

Pola output list:
- `period_type = unlimited` → "Masa aktif: Tidak terbatas"
- `period_type = monthly`, `active_period_value = 1` → "Masa aktif: 1 bulan"
- `period_type = monthly`, `active_period_value > 1` → tampilkan masa aktif sesuai data, misalnya "Masa aktif: 4 bulan"
- `period_type = yearly`, `active_period_value = 12` → "Masa aktif: 1 tahun"
- `period_type = yearly`, nilai lain → tampilkan masa aktif sesuai data, misalnya "Masa aktif: 24 bulan"
- Field tidak jelas/null → skip masa aktif

Pola output detail:
- Jika `active_period_day` tersedia dan > 0, boleh tambahkan estimasi hari, misalnya "(±30 hari)".

### Lokasi Membership

- `member_location.type = all` → "Berlaku di semua outlet"
- `member_location.type = only` → tampilkan outlet sesuai data, misalnya "Outlet: Perkilo Premium Pejaten"
- `member_location.type = except` → "Berlaku di semua outlet kecuali outlet tertentu"
- Jika lokasi kosong/null → skip lokasi

### Detail Membership Display Rules

Ketika customer pilih membership tertentu, tampilkan:
- **Nama membership**
- **Status aktif** (hanya jika tidak aktif atau data detail berbeda dari list)
- **Biaya daftar**
- **Masa aktif**
- **Deskripsi**
- **Lokasi membership**

Bot skip field yang kosong/null.

---

## Mode Tampilan

### Ada Membership Aktif

```text
Membership aktif di Perkilo Premium Pejaten:

MEMBER AREMANIA
Biaya daftar: Rp1.500
Masa aktif: 1 bulan

ROMY GERUNK
Biaya daftar: Rp5.000
Masa aktif: 1 tahun
```

### Ada Membership Gratis

```text
Membership aktif di Perkilo Premium Kuningan:

MEMBER HITAM
Biaya daftar: Gratis
Masa aktif: Tidak terbatas
```

### Membership Lebih Dari 5

```text
Membership aktif di Perkilo Premium Kuningan:

MEMBER HITAM
Biaya daftar: Gratis
Masa aktif: Tidak terbatas

REAL WASH ONLY
Biaya daftar: Rp1
Masa aktif: Tidak terbatas

MEMBER GOLD
Biaya daftar: Gratis
Masa aktif: 4 bulan

MEMBER TERBARU
Biaya daftar: Gratis
Masa aktif: Tidak terbatas

MEMBER AREMANIA
Biaya daftar: Rp1.500
Masa aktif: 1 bulan

...dan 1 membership lainnya.
```

### Tidak Ada Membership Aktif

```text
Saat ini belum ada membership aktif di Perkilo Premium Pejaten.
```

### API Error

```text
Mohon maaf, sistem sedang gangguan. Saya hubungkan Kakak ke agent ya.
```

### Detail Membership

```text
MEMBER AREMANIA
Biaya daftar: Rp1.500
Masa aktif: 1 bulan (±30 hari)
Berlaku di semua outlet

Deskripsi:
isuk isuk tuku lengo nyangking botol
```

### Detail Membership — Tidak Ditemukan

```text
Maaf Kak, membership dengan nama tersebut tidak ditemukan.
```

---

## Alur Percakapan

### Tanya Membership — Ada

```text
Customer:
Ada member apa aja?

Bot:
Membership aktif di Perkilo Premium Pejaten:

MEMBER AREMANIA
Biaya daftar: Rp1.500
Masa aktif: 1 bulan

ROMY GERUNK
Biaya daftar: Rp5.000
Masa aktif: 1 tahun

Ada yang ingin ditanyakan lagi?
```

### Tanya Membership — Gratis

```text
Customer:
Member gratis ada?

Bot:
Membership aktif di Perkilo Premium Kuningan:

MEMBER HITAM
Biaya daftar: Gratis
Masa aktif: Tidak terbatas

Ada yang ingin ditanyakan lagi?
```

### Tanya Membership — Tidak Ada

```text
Customer:
Ada paket member?

Bot:
Saat ini belum ada membership aktif di Perkilo Premium Pejaten.
Ada yang ingin ditanyakan lagi?
```

### Tanya Detail Membership

```text
Customer:
Detail member Aremania apa?

Bot:
MEMBER AREMANIA
Biaya daftar: Rp1.500
Masa aktif: 1 bulan (±30 hari)
Berlaku di semua outlet

Deskripsi:
isuk isuk tuku lengo nyangking botol

Ada yang ingin ditanyakan lagi?
```

### Tanya Detail Membership — Tidak Ditemukan

```text
Customer:
Detail member platinum

Bot:
Maaf Kak, membership dengan nama tersebut tidak ditemukan.
Ada yang ingin ditanyakan lagi?
```

---

## Trigger Phrases

| Kategori | Contoh Input |
| -------- | ------------ |
| Tanya membership aktif | "ada member?", "member apa aja?", "membership apa yang tersedia?", "paket member ada?" |
| Tanya biaya member | "biaya daftar member berapa?", "member bayar berapa?", "daftar member gratis?" |
| Tanya masa aktif member | "member berlaku berapa lama?", "masa aktif member berapa bulan?" |
| Tanya detail membership | "detail member Aremania", "member Aremania apa?", "syarat member Aremania apa?", "member Aremania detailnya apa?" |

---

## Edge Cases

| Scenario | Bot Response |
| -------- | ------------ |
| Tidak ada membership aktif | Bot sampaikan bahwa saat ini belum ada membership aktif untuk outlet di conversation context. |
| 1 membership aktif | Tampilkan 1 membership. |
| >5 membership aktif | Tampilkan 5 membership + jumlah sisa, misalnya "dan 1 membership lainnya." |
| Customer tanya detail membership | Bot cari `id` dari daftar aktif → fetch detail membership → tampilkan detail lengkap. |
| Customer tanya detail membership yang tidak ada di daftar aktif | "Maaf Kak, membership dengan nama tersebut tidak ditemukan." |
| Customer tanya detail membership — API 404 | "Maaf Kak, membership sudah tidak tersedia." |
| Customer tanya detail membership — API error / timeout | "Mohon maaf, sistem sedang gangguan. Saya hubungkan Kakak ke agent ya." |
| Customer tanya membership outlet lain | Gunakan outlet dari conversation context — jangan reveal data outlet lain. |
| API list error / timeout | "Mohon maaf, sistem sedang gangguan. Saya hubungkan Kakak ke agent ya." |
| Master switch OFF | Silent handoff ke agent. |
| Per-outlet toggle OFF | "Fitur ini sedang tidak tersedia di outlet ini." |

---

## Acceptance Criteria

- [ ] Bot dapat menjawab membership aktif outlet berdasarkan natural language customer.
- [ ] Bot hanya menampilkan membership aktif.
- [ ] Tidak ada membership → fallback "belum ada membership aktif".
- [ ] Maksimal 5 membership per respons; lebih dari 5 tambah jumlah sisa, misalnya "dan 1 membership lainnya".
- [ ] List membership mencakup nama, biaya daftar, dan masa aktif jika tersedia.
- [ ] Field internal tidak ditampilkan.
- [ ] Customer tanya detail membership tertentu → bot fetch detail membership dan tampilkan detail.
- [ ] Detail membership mencakup nama, biaya daftar, masa aktif, deskripsi, dan lokasi jika tersedia.
- [ ] Membership tidak ditemukan → "Maaf Kak, membership dengan nama tersebut tidak ditemukan."
- [ ] Detail membership API 404 → "Maaf Kak, membership sudah tidak tersedia."
- [ ] API error → escalate ke agent.
- [ ] Patuh master switch + per-outlet toggle.
- [ ] Bahasa Indonesia, tone "Kak".
- [ ] Tidak ada slot filling — outlet dari context.
