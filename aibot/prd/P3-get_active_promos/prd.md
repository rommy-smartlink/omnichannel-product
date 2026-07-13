# Promo Aktif (`get_active_promos`) — PRD

## Overview

Fitur promo aktif membantu customer mengetahui promo yang sedang berlaku di outlet yang dipilih. Customer bertanya dalam bahasa natural, bot menampilkan daftar promo dalam format ringkas. Jika tidak ada promo aktif, bot sampaikan dengan jelas.

Tujuan: mengurangi pertanyaan repetitif "ada promo apa?" ke CS dengan self-service yang informatif.

---

## User Stories

- As a **customer**, saya ingin **tanya promo aktif dalam bahasa natural** agar **tahu diskon atau penawaran tanpa harus datang/telepon**.
- As a **customer**, saya ingin **lihat nama dan deskripsi promo** agar **paham benefit yang bisa saya dapatkan**.
- As a **owner**, saya ingin **bot hanya menampilkan promo yang masih berlaku** agar **customer tidak kecewa karena promo sudah expired**.
- As a **customer**, saya ingin **tanya detail promo tertentu** agar **tahu syarat & ketentuan lengkap tanpa perlu tanya agen**.
- As a **owner**, saya ingin **bot bisa menjawab detail promo yang tersedia di sistem** agar **customer tidak perlu diarahkan ke agen untuk syarat sederhana**.

---

## Goals & Non-Goals

### Goals

- Intent `get_active_promos` aktif per outlet (toggleable via Control Panel, default ON).
- Slot wajib: **tidak ada** (outlet sudah di context). Bot langsung menjawab tanpa perlu tanya parameter tambahan.
- Tampilkan **nama promo** dan **deskripsi promo** untuk setiap promo aktif.
- **Periode berlaku** opsional — tampilkan jika tersedia di data promo.
- **Syarat singkat** opsional — tampilkan jika relevan dan ringkas.
- Jika tidak ada data promo aktif → "Saat ini belum ada promo aktif di Perkilo Premium Pejaten."
- Jika data promo tidak bisa diakses → fallback escalate ke agen.
- Lookup dibatasi outlet dari conversation context.
- Bahasa Indonesia, tone "Kak", format tanggal "DD MMMM YYYY".
- Patuh hierarki master switch + per-outlet intent toggle.

### Non-Goals (MVP)

- Detail promo per transaksi (promo yang sudah dipakai di transaksi tertentu).
- Kode promo redeem / klaim dari bot.
- History promo yang sudah lewat.
- Filter/sort promo (misal "promo diskon tertinggi").
- Multi-language.
- Bot verifikasi syarat promo (misal "apakah saya memenuhi syarat?"). Tanya detail → tampilkan info lengkap yang tersedia, tetapi keputusan final tetap di tangan customer & CS.

---

## Flow

### Alur Utama

1. Customer kirim pesan di suatu outlet (outlet & WABA sudah di context).
2. Master switch WABA check — OFF → silent handoff ke agent.
3. Per-outlet intent toggle `get_active_promos` check — OFF → "Fitur ini sedang tidak tersedia di outlet ini".
4. Bot mengenali kebutuhan customer:
   - `get_active_promos` → step 5a.
   - `get_detail_promo` → sub-flow detail promo.
   - other → handle intent masing-masing.
5a. Ambil daftar promo untuk outlet tersebut.
6a. Hanya promo yang periodenya masih berlangsung dan relevan untuk outlet ini yang boleh ditampilkan.
7a. Handling:
   - Ada promo → tampilkan sesuai Display Rules.
   - Data kosong → "Saat ini belum ada promo aktif di Perkilo Premium Pejaten."
   - Data promo tidak bisa diakses → escalate ke agent.
8a. Tanya "Ada yang ingin ditanyakan lagi?"

### Sub-Flow Detail Promo

5b. Customer menyebut nama promo spesifik. Bot cari promo tersebut dari daftar promo aktif.
   - Jika daftar promo aktif belum tersedia di sesi ini → ambil daftar promo dulu.
   - Jika nama tidak ditemukan → "Maaf Kak, promo dengan nama tersebut tidak ditemukan."
6b. Ambil detail promo dari sistem.
7b. Handling:
   - Sukses → tampilkan detail promo (lihat Display Rules — Detail Promo).
   - 404 → "Maaf Kak, promo sudah tidak tersedia."
   - Data promo tidak bisa diakses → escalate ke agent.
8b. Tanya "Ada yang ingin ditanyakan lagi?"

---

## Display Rules

- Bot tidak menampilkan data mentah ke customer.
- Hanya promo yang periodenya masih berlangsung dan relevan untuk outlet ini yang boleh ditampilkan.
- Maksimal 5 promo ditampilkan dalam satu respons. Jika lebih dari 5, tambah "dan 3 promo lainnya" di akhir, sesuai jumlah sisa promo.
- Format per promo: **nama promo** + deskripsi benefit + periode berlaku (jika tersedia).
- Jika promo khusus member, tambahkan label "(Member)" setelah nama.
- **Field yang tidak ditampilkan di list:** ID promo, kuota, status internal, lokasi mentah, terms mentah, nominal diskon mentah, minimal transaksi mentah.

### Deskripsi Benefit

Generate deskripsi customer-friendly dari benefit promo, bukan data mentah.

Pola output:
- Diskon persen → "Diskon 20% untuk total belanja." atau "Diskon 5% untuk layanan."
- Potongan nominal → "Potongan Rp20.000 untuk total belanja."
- Diskon khusus member → "Diskon 5% khusus member."
- Hadiah gratis → "Bonus hadiah gratis."
- Layanan gratis → "Gratis layanan [nama layanan]."

Prioritas benefit: general dulu, member kedua. Jika keduanya ada, pakai general.

### Detail Promo Display Rules

Ketika customer pilih promo tertentu, tampilkan:
- **Nama promo**
- **Label Member** (jika khusus member)
- **Periode berlaku** format "DD MMMM YYYY"
- **Jam berlaku** format "HH:mm" (jika ada)
- **Deskripsi benefit** (sama dengan aturan di atas)
- **Syarat & Ketentuan** — generate daftar poin dari data syarat. Contoh poin:
   - Minimal belanja: Rp50.000
   - Minimal 3 item
  - Berlaku untuk layanan/produk tertentu
  - Bisa digabung dengan promo lain / wajib bayar lunas / wajib struk fisik / wajib KTP / wajib lampirkan bukti
- **Lokasi promo** — tampilkan: semua outlet / outlet tertentu / semua kecuali outlet tertentu
- **Metode pembayaran**
- **Sisa kuota** (jika ada dan > 0)

Bot skip field yang kosong/null.

### Pricing Display — MVP

- Tidak tampilkan nominal diskon mentah, maksimal diskon, atau minimal transaksi ke customer.
- Diskon persen → tulis "diskon X%" saja.
- Diskon nominal → tulis contoh seperti "potongan Rp20.000" dengan format Indonesia.

---

## Mode Tampilan

### Ada Promo Aktif

```text
Promo aktif di Perkilo Premium Pejaten:

MEMBER PEJA 5% (Member)
Diskon 5% untuk layanan.
Berlaku 01 Januari 2026 — 01 Januari 2027
```

### Ada Promo — Tipe Voucher (General)

```text
Promo aktif di Perkilo Premium Kuningan:

VOUCHER GOOGLE KUNI
Potongan Rp20.000 untuk total belanja.
Berlaku 24 November 2025 — 23 November 2026
```

### Tidak Ada Promo Aktif

```text
Saat ini belum ada promo aktif di Perkilo Premium Pejaten.
```

### Data Promo Tidak Bisa Diakses

```text
Mohon maaf, sistem sedang gangguan. Saya hubungkan Kakak ke agent ya.
```

### Detail Promo — General Discount

```text
HEMAT20
Diskon 20% untuk total belanja.

Berlaku: 01 Januari 2026 — 31 Desember 2026
Jam: 08:00 — 22:00
Berlaku di semua outlet
Pembayaran: QRIS, Cash

Syarat & Ketentuan:
• Minimal belanja: Rp50.000
• Bisa digabung dengan promo lain
```

### Detail Promo — Member Benefit

```text
MEMBER PEJA 5% (Member)
Diskon 5% khusus member.

Berlaku: 01 Januari 2026 — 01 Januari 2027
Outlet: Perkilo Premium Pejaten
Pembayaran: Cash, QRIS

Syarat & Ketentuan:
• Berlaku untuk layanan
• Wajib bayar lunas
```

### Detail Promo — Tidak Ditemukan

```text
Maaf Kak, promo dengan nama tersebut tidak ditemukan.
```

---

## Alur Percakapan

### Tanya Promo — Ada (General)

```
Customer:
Ada promo hari ini?

Bot:
Promo aktif di Perkilo Premium Kuningan:

VOUCHER GOOGLE KUNI
Potongan Rp20.000 untuk total belanja.
Berlaku 24 November 2025 — 23 November 2026

Ada yang ingin ditanyakan lagi?
```

### Tanya Promo — Ada (Member)

```
Customer:
Ada promo member?

Bot:
Promo aktif di Perkilo Premium Pejaten:

MEMBER PEJA 5% (Member)
Diskon 5% untuk layanan.
Berlaku 01 Januari 2026 — 01 Januari 2027

Ada yang ingin ditanyakan lagi?
```

### Tanya Promo — Multi Promo (>5)

```
Customer:
Promo apa aja?

Bot:
Promo aktif di Perkilo Premium Kuningan:

VOUCHER GOOGLE KUNI
Potongan Rp20.000 untuk total belanja.

...dan 5 promo lainnya.

Ada yang ingin ditanyakan lagi?
```

### Tanya Promo — Tidak Ada

```
Customer:
Ada promo apa aja?

Bot:
Saat ini belum ada promo aktif di Outlet Menteng.
Ada yang ingin ditanyakan lagi?
```

### Tanya Detail Promo

```
Customer:
Syarat promo HEMAT20 apa?

Bot:
HEMAT20
Diskon 20% untuk total belanja.

Berlaku: 01 Januari 2026 — 31 Desember 2026
Jam: 08:00 — 22:00
Berlaku di semua outlet
Pembayaran: QRIS, Cash

Syarat & Ketentuan:
• Minimal belanja: Rp50.000
• Bisa digabung dengan promo lain

Ada yang ingin ditanyakan lagi?
```

### Tanya Detail Promo — Promo Tidak Ditemukan

```
Customer:
Detail promo BONUS100

Bot:
Maaf Kak, promo dengan nama tersebut tidak ditemukan.
Ada yang ingin ditanyakan lagi?
```

### Tanya Detail Promo — Data Promo Tidak Bisa Diakses

```
Customer:
Syarat promo HEMAT20 apa?

Bot:
Mohon maaf, sistem sedang gangguan. Saya hubungkan Kakak ke agent ya.
```

---

## Trigger Phrases

| Kategori | Contoh Input |
| -------- | ------------ |
| Tanya promo aktif | "ada promo?", "promo apa aja?", "diskon hari ini?", "ada penawaran?" |
| Tanya promo member | "promo member ada?", "diskon untuk member?" |
| Tanya voucher | "voucher apa yang aktif?", "ada kode promo?" |
| Tanya detail promo | "syarat promo HEMAT20?", "detail promo HEMAT20?", "promo HEMAT20 syaratnya apa?", "HEMAT20 syaratnya?" |

---

## Edge Cases

| Scenario | Bot Response |
| -------- | ------------ |
| Tidak ada promo aktif | "Saat ini belum ada promo aktif di Perkilo Premium Pejaten." |
| 1 promo aktif | Tampilkan 1 promo. |
| >5 promo aktif | Tampilkan 5 promo + "dan 3 promo lainnya", sesuai jumlah sisa promo. |
| Customer tanya detail syarat promo | Bot cari promo dari daftar aktif, lalu tampilkan detail lengkap. |
| Customer tanya detail promo yang tidak ada di daftar aktif | "Maaf Kak, promo dengan nama tersebut tidak ditemukan." |
| Customer tanya detail promo — promo sudah tidak tersedia | "Maaf Kak, promo sudah tidak tersedia." |
| Customer tanya detail promo — data promo tidak bisa diakses | "Mohon maaf, sistem sedang gangguan. Saya hubungkan Kakak ke agent ya." |
| Customer tanya promo outlet lain | Gunakan outlet dari conversation context — jangan reveal data outlet lain. |
| Data daftar promo tidak bisa diakses | "Mohon maaf, sistem sedang gangguan. Saya hubungkan Kakak ke agent ya." |
| Master switch OFF | Silent handoff ke agent. |
| Per-outlet toggle OFF | "Fitur ini sedang tidak tersedia di outlet ini." |

---

## Acceptance Criteria

- [ ] Bot dapat menjawab promo aktif outlet berdasarkan natural language customer.
- [ ] Bot hanya menampilkan promo yang masih berlaku (periode aktif).
- [ ] Tidak ada promo → fallback "belum ada promo aktif".
- [ ] Maksimal 5 promo per respons; lebih dari 5 tambah "dan N promo lainnya".
- [ ] Field sensitif (diskon nominal, minimal transaksi, dll) tidak ditampilkan.
- [ ] Customer tanya detail syarat promo tertentu → bot tampilkan detail promo tersebut.
- [ ] Detail promo mencakup: nama, benefit, periode, jam, lokasi, metode bayar, syarat & ketentuan.
- [ ] Promo tidak ditemukan → "Maaf Kak, promo dengan nama tersebut tidak ditemukan."
- [ ] Detail promo sudah tidak tersedia → "Maaf Kak, promo sudah tidak tersedia."
- [ ] Data promo tidak bisa diakses → escalate ke agent.
- [ ] Patuh master switch + per-outlet toggle.
- [ ] Bahasa Indonesia, tone "Kak".
- [ ] Tidak ada slot filling — outlet dari context.
