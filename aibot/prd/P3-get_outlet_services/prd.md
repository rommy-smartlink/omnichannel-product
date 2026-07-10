# Info Layanan Outlet — PRD

## Overview

Fitur Info Layanan Outlet membantu customer mengetahui layanan yang tersedia di outlet yang sedang dipilih. Bot menjawab berdasarkan kebutuhan customer: layanan spesifik dijawab ya/tidak, pertanyaan umum dengan maksimal 20 layanan ditampilkan langsung, pertanyaan umum dengan 21 layanan atau lebih diarahkan agar customer menyebut layanan yang dicari, dan jika tidak tersedia, bot mengarahkan ke agen.

Tujuan: menjawab pertanyaan tentang layanan yang biasanya masuk ke CS, dengan respons yang ringkas dan relevan di WhatsApp.

---

## User Stories

- As a **customer**, saya ingin **menanyakan layanan yang tersedia di outlet** agar **tidak perlu menelepon atau datang untuk cek dulu**.
- As a **customer**, saya ingin **tahu apakah layanan spesifik tersedia** (misal "bisa laundry sepatu?") agar **cepat dapat jawaban ya/tidak**.
- As a **customer**, saya ingin **tidak disuguhi ratusan layanan sekaligus** agar **respons bot mudah dibaca di WhatsApp**.
- As a **customer**, saya ingin **dibantu menemukan layanan yang relevan** jika pertanyaan saya terlalu umum.
- As a **owner**, saya ingin **customer yang butuh layanan tidak tersedia diarahkan ke agen** agar **tidak ada ekspektasi yang salah**.
- As a **owner**, saya ingin **bot hanya membaca data layanan, tidak membuat/menambah layanan** agar **data tetap akurat sesuai sistem**.

---

## Goals & Non-Goals

### Goals

- Fitur Info Layanan Outlet aktif per outlet (toggleable via Control Panel, default ON).
- Respons adaptif: **layanan spesifik** → ya/tidak + konteks; **pertanyaan umum dengan ≤20 layanan** → tampilkan semua layanan; **pertanyaan umum dengan ≥21 layanan** → minta customer menyebut layanan/kebutuhan yang dicari.
- Jika customer bertanya terlalu umum dan outlet memiliki 21 layanan atau lebih → bot minta customer memperjelas.
- Layanan tidak tersedia → bot sampaikan "belum tersedia" dan arahkan ke agen jika perlu.
- Bot hanya membaca data layanan dari outlet yang sedang dipilih customer (via `idoutlet` di conversation context).
- Tidak ada membuat atau mengubah data layanan (read-only).
- Bahasa Indonesia, tone "Kak", format list yang nyaman dibaca di WhatsApp.

---

### Non-Goals (MVP)

- Browse/katalog lengkap semua layanan dalam satu respons.
- Order atau booking layanan dari bot.
- Harga per layanan (harga concern CS/owner, bukan bot scope).
- Rekomendasi layanan berdasarkan riwayat transaksi customer.
- Multi-language.

---

## Display Rules

- Bot tidak menampilkan raw response API ke customer.
- Bot hanya menjawab berdasarkan outlet yang sedang dipilih customer.
- Jika customer bertanya layanan spesifik, bot menjawab tersedia/belum tersedia.
- Jika customer bertanya umum dan total layanan outlet maksimal 20, bot menampilkan semua layanan.
- Jika customer bertanya umum dan total layanan outlet 21 atau lebih, bot meminta customer menyebut layanan/kebutuhan yang dicari.
- Untuk total layanan 21 atau lebih, bot boleh memberi contoh keyword umum maksimal 5 item, seperti cuci pakaian, setrika, express, sepatu, atau bed cover.
- Contoh keyword bukan daftar lengkap layanan dan bukan klaim semua contoh pasti tersedia.
- Jika response API terpaginated, bot harus mempertimbangkan semua halaman sebelum menyimpulkan layanan tidak tersedia.
- Jika `layanan.nama_layanan` kosong, item diabaikan.
- Jika `layanan.satuan.nama` kosong, bot tidak menyebut satuan.

### Pricing Display Rule

- Bot tidak menampilkan `harga` dari API ke customer pada MVP.
- Bot dapat menyebut layanan tersedia, tanpa menyebut nominal harga.
- Jika customer bertanya harga, bot mengarahkan ke agent outlet untuk harga detail.

---

## Flow Utama

```
Customer menanyakan layanan
↓
Bot klasifikasi intent Info Layanan Outlet
↓
Bot analisis: spesifik atau umum?
↓
Spesifik: fetch layanan, match keyword
  → Tersedia: "Ya Kak, [layanan] tersedia."
  → Tidak tersedia: "Maaf Kak, [layanan] belum tersedia."
Umum:
  → Total layanan ≤20: tampilkan semua layanan
  → Total layanan ≥21: minta customer menyebut layanan/kebutuhan yang dicari
↓
Customer jawab → bot lanjut atau eskalasi ke agen
```

---

## Alur Percakapan

### Tanya Layanan Spesifik (Tersedia)

```
Customer:
Bisa laundry sepatu?

Bot:
Ya Kak, laundry sepatu tersedia di Outlet Tebet (per pcs) ya.
Ada yang ingin ditanyakan lagi?
```

> Bot ambil `satuan.nama` dari API untuk konteks tambahan (pcs, kg, etc).

### Tanya Layanan Spesifik (Tidak Tersedia)

```
Customer:
Ada cuci kasur?

Bot:
Maaf Kak, untuk Outlet Tebet layanan cuci kasur belum tersedia ya.
Kakak bisa menanyakan layanan lain atau saya hubungkan ke agent jika perlu.
```

### Pertanyaan Umum (Maksimal 20 Layanan)

```
Customer:
Ada layanan apa saja?

Bot:
Di Outlet Menteng, layanan yang tersedia:

1. Cuci Kering
2. Setrika Saja
3. Express Wash
4. Laundry Sepatu
```

### Pertanyaan Umum (21 Layanan atau Lebih)

```
Customer:
Ada layanan apa saja?

Bot:
Layanan di Outlet Tebet cukup banyak, Kak.
Kakak sedang cari layanan apa? Bisa sebutkan jenisnya, misalnya cuci pakaian, setrika, express, sepatu, atau bed cover.
```

### Customer Perjelas Setelah Pertanyaan Umum

```
Customer:
Ada express?

Bot:
Ya Kak, layanan express tersedia di Outlet Tebet. Layanan ini biasanya siap dalam waktu 3–4 jam ya.
```

### Pertanyaan Layanan + Request Agen dalam 1 Pesan

```
Customer:
Ada laundry karpet? Terus hubungkan saya ke agen.

Bot:
Baik Kak, saya hubungkan ke agent Outlet Tebet ya.
Mohon tunggu sebentar.
```

---

## Trigger Phrases

| Kategori | Contoh Input |
| -------- | ------------ |
| Pertanyaan umum | "ada layanan apa saja?", "layanan apa aja?", "apa saja yang ada?", "daftar layanan" |
| Tanya spesifik | "bisa laundry sepatu?", "ada express?", "bisa cuci bed cover?", "ada setrika?" |
| Tanya ketersediaan | "bisa laundry karpet?", "masih ada laundry sprei?", "ada layanan steam?" |
| Tanya harga layanan | "harga laundry per kg?", "harga express berapa?" (jangan tampilkan harga; arahkan agen untuk detail harga) |

---

## Edge Cases

| Scenario | Bot Response |
| -------- | ------------ |
| Outlet tanpa layanan | "Maaf Kak, saat ini Outlet [Nama] belum memiliki layanan terdaftar. Saya bisa hubungkan ke agent untuk info lebih lanjut." |
| Outlet dengan maksimal 20 layanan, customer tanya umum | Tampilkan semua layanan. |
| Outlet dengan 21 layanan atau lebih, customer tanya umum | "Layanan di outlet ini cukup banyak, Kak. Kakak sedang cari layanan apa? Bisa sebutkan jenisnya, misalnya cuci pakaian, setrika, express, sepatu, atau bed cover." |
| Customer tanya harga | "Layanan tersebut tersedia di outlet ini, Kak. Untuk harga detail, saya hubungkan Kakak ke agent outlet ya." |
| Customer minta booking dari bot | "Maaf Kak, booking belum bisa dilakukan lewat bot. Saya bisa hubungkan Kakak ke agent ya." |
| Customer minta daftar lengkap semua layanan | "Maaf Kak, daftar lengkapnya cukup banyak. Kakak bisa ceritakan jenis layanan yang Kakak cari, ya?" |
| Customer tanya layanan outlet lain | Gunakan outlet dari conversation context — jangan pernah reveal outlet lain. |
| Keyword tidak match layanan manapun | "Maaf Kak, layanan yang Kakak maksud belum tersedia di outlet ini. Saya bisa hubungkan ke agent jika perlu." |
| Error saat fetch data layanan | "Mohon maaf, sistem sedang gangguan. Saya hubungkan Kakak ke agent ya." |
| Error authorization API | "Mohon maaf, sistem sedang gangguan. Saya hubungkan Kakak ke agent ya." |

---

## Master Switch Behavior

| Kondisi | Perilaku |
| ------- | -------- |
| Master switch OFF | Bot skip, langsung handoff ke agen. |
| Master switch ON + per-outlet OFF | Bot tampil "Fitur ini sedang tidak tersedia di outlet ini." |
| Master switch ON + per-outlet ON | Bot mulai flow Info Layanan Outlet. |

---

## Acceptance Criteria

- [ ] Customer dapat menanyakan layanan outlet dengan bahasa bebas.
- [ ] Bot menjawab "ya" atau "belum tersedia" untuk layanan spesifik yang customer sebut.
- [ ] Bot menampilkan semua layanan jika customer bertanya umum dan total layanan outlet maksimal 20.
- [ ] Bot tidak menampilkan semua layanan jika total layanan outlet 21 atau lebih.
- [ ] Bot meminta customer menyebut layanan/kebutuhan yang dicari jika pertanyaan umum dan total layanan outlet 21 atau lebih.
- [ ] Bot boleh memberi contoh keyword umum maksimal 5 item tanpa mengklaim sebagai daftar lengkap untuk total layanan 21 atau lebih.
- [ ] Bot mengarahkan ke agen jika layanan tidak tersedia.
- [ ] Bot mengarahkan ke agen jika customer minta harga detail atau booking.
- [ ] Bot tidak menampilkan nominal `harga` dari API ke customer.
- [ ] Outlet tanpa layanan mendapat fallback yang jelas + arahkan ke agen.
- [ ] Per-outlet toggle bekerja sesuai hierarki master switch + per-outlet.
- [ ] Bahasa Indonesia, tone "Kak".
- [ ] Bot hanya menampilkan data dari outlet yang sedang dipilih, tidak bocor outlet lain.
