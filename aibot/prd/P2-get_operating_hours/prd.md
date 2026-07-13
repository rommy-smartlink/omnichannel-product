# Intent Jam Operasional (`get_operating_hours`) — PRD

## Overview

Intent `get_operating_hours` memungkinkan customer menanyakan jadwal buka dan tutup outlet melalui percakapan natural di WhatsApp. Bot dapat menjawab jadwal umum, jadwal hari atau tanggal tertentu, status buka/tutup saat ini, waktu tutup hari ini, serta waktu buka berikutnya.

Jawaban bot wajib menggunakan konfigurasi Jam Operasional Outlet yang sudah valid. Perhitungan `sekarang`, `hari ini`, `besok`, dan pergantian tanggal mengikuti zona waktu outlet, bukan zona waktu customer atau server. Jadwal khusus pada satu tanggal maupun rentang tanggal selalu diprioritaskan dibanding jadwal mingguan. Satu tanggal direpresentasikan dengan tanggal mulai dan tanggal selesai yang sama.

Tujuan: mengurangi pertanyaan repetitif terkait jam buka outlet dengan jawaban yang akurat, ringkas, dan kontekstual.

---

## User Stories

- As a **customer**, saya ingin **menanyakan jam operasional dengan bahasa natural** agar **tidak perlu mencari informasi outlet secara manual**.
- As a **customer**, saya ingin **mengetahui apakah outlet sedang buka** agar **tidak datang ketika outlet tutup atau sedang jeda**.
- As a **customer**, saya ingin **mengetahui jam buka atau tutup hari tertentu** agar **dapat merencanakan kunjungan**.
- As a **customer**, saya ingin **mengetahui jadwal pada tanggal khusus** agar **informasi hari libur tidak keliru**.
- As a **customer**, saya ingin **mengetahui kapan outlet buka kembali** agar **punya arahan setelah mengetahui outlet sedang tutup**.
- As an **owner**, saya ingin **bot hanya menjawab dari jadwal yang telah dikonfigurasi** agar **bot tidak mengarang atau menggunakan asumsi**.
- As an **owner**, saya ingin **intent hanya dapat aktif setelah jadwal siap** agar **customer selalu mendapat informasi yang dapat dipertanggungjawabkan**.

---

## Goals & Non-Goals

### Goals

- Mengaktifkan intent `get_operating_hours` per outlet setelah prasyarat konfigurasi terpenuhi.
- Memahami variasi bahasa natural terkait buka, tutup, jam operasional, hari, tanggal, dan status saat ini.
- Menjawab pertanyaan jadwal umum outlet.
- Menjawab jadwal hari ini, besok, hari tertentu, dan tanggal tertentu.
- Menjawab apakah outlet sedang buka atau tutup berdasarkan waktu lokal outlet.
- Menjelaskan jam tutup saat outlet sedang buka.
- Menjelaskan waktu buka berikutnya saat outlet sedang tutup atau berada di antara dua sesi.
- Mendukung status Buka, Tutup, 24 Jam, dua sesi per hari, dan jadwal melewati tengah malam.
- Memprioritaskan jadwal khusus dibanding jadwal mingguan.
- Mengelompokkan hari dengan jadwal sama agar respons mingguan tetap ringkas.
- Memberikan fallback yang jelas jika data tidak tersedia atau tidak konsisten.
- Mengikuti master switch WABA dan toggle per-outlet dari Control Panel.

### Non-Goals (MVP)

- Membuat reservasi atau janji kunjungan.
- Mengubah jam operasional melalui percakapan customer.
- Memberi estimasi antrean atau kepadatan outlet.
- Menentukan batas terakhir penerimaan laundry jika berbeda dari jam tutup.
- Menjawab jadwal layanan spesifik yang berbeda dari jadwal outlet.
- Menampilkan jam kerja karyawan atau agent.
- Mengirim notifikasi otomatis sebelum outlet buka atau tutup.
- Menyarankan outlet alternatif yang sedang buka.
- Multi-language.

---

## Intent

```text
get_operating_hours
```

### Contoh Input Customer

```text
Jam operasional outlet ini bagaimana?
Buka jam berapa?
Tutup jam berapa?
Sekarang masih buka?
Hari Minggu buka tidak?
Besok buka jam berapa?
Tanggal 17 Agustus buka?
Kalau sekarang tutup, buka lagi kapan?
Hari ini ada jam istirahat?
Sabtu malam buka sampai jam berapa?
```

---

## Parameter

| Parameter | Wajib/Opsional | Sumber | Keterangan |
|---|---|---|---|
| Outlet | Wajib, otomatis | Conversation context | Outlet yang dipilih customer sebelum bot aktif. |
| Zona waktu outlet | Wajib, otomatis | Pengaturan Jam Operasional | Digunakan untuk seluruh interpretasi waktu. |
| Jenis pertanyaan | Otomatis | Pesan customer | `weekly_schedule`, `day_schedule`, `current_status`, `opening_time`, `closing_time`, atau `next_opening`. |
| Hari | Opsional | Pesan customer | Contoh: Minggu, Sabtu. |
| Tanggal | Opsional | Pesan customer | Contoh: 17 Agustus, 20 Juli 2026. |
| Waktu referensi | Opsional | Pesan customer | Contoh: sekarang, nanti malam, besok pagi. |
| Waktu saat ini | Otomatis | Waktu lokal outlet | Digunakan jika customer bertanya status saat ini atau tidak menyebut tanggal. |

Bot tidak meminta customer memasukkan zona waktu.

---

## Interpretasi Pertanyaan

| Pertanyaan Customer | Interpretasi Produk |
|---|---|
| “Jam operasionalnya bagaimana?” | Tampilkan ringkasan jadwal mingguan. |
| “Buka jam berapa?” | Tampilkan jadwal hari ini berdasarkan zona waktu outlet. |
| “Tutup jam berapa?” | Tampilkan jam tutup sesi yang relevan hari ini. Jika outlet belum buka, tampilkan keseluruhan sesi hari ini. |
| “Sekarang masih buka?” | Hitung status pada waktu lokal outlet saat pesan diterima. |
| “Besok buka?” | Gunakan tanggal besok berdasarkan zona waktu outlet. |
| “Hari Minggu buka?” | Gunakan kejadian Minggu terdekat yang belum lewat; jika tanggal itu memiliki jadwal khusus, gunakan jadwal khusus. |
| “Setiap Minggu buka?” | Gunakan jadwal mingguan reguler untuk hari Minggu. |
| “Tanggal 17 Agustus buka?” | Gunakan tanggal yang dimaksud; jadwal khusus mengalahkan jadwal mingguan. |
| “Buka lagi kapan?” | Cari sesi berikutnya pada hari yang sama atau hari operasional berikutnya. |
| “Nanti malam buka?” | Evaluasi waktu malam pada tanggal yang relevan; jika ambigu, gunakan konteks hari ini. |

Jika tanggal tanpa tahun masih ambigu karena tanggal tersebut sudah lewat dalam tahun berjalan, bot menggunakan kejadian terdekat yang akan datang dan menyebut tanggal lengkap pada respons.

---

## Flow

### Alur Utama

```text
1. Customer mengirim pesan pada conversation outlet X.
2. Sistem memeriksa master switch WABA:
   - OFF → bot tidak memproses intent; conversation mengikuti silent handoff.
   - ON → lanjut.
3. Sistem memeriksa toggle get_operating_hours outlet X:
   - OFF → tampilkan fallback fitur tidak tersedia.
   - ON → lanjut.
4. Sistem memeriksa kesiapan konfigurasi:
   - Jadwal valid + zona waktu tersedia → lanjut.
   - Tidak valid/tidak tersedia → tampilkan fallback data dan tawarkan agent.
5. Bot mengklasifikasikan jenis pertanyaan dan mengekstrak hari/tanggal/waktu jika ada.
6. Bot menentukan tanggal referensi berdasarkan zona waktu outlet.
7. Bot mengambil jadwal yang berlaku:
   a. Cek jadwal khusus pada tanggal referensi.
   b. Jika tidak ada, gunakan jadwal mingguan.
8. Bot mengevaluasi status atau jadwal sesuai pertanyaan customer.
9. Bot merender respons customer-friendly.
10. Bot menawarkan bantuan lanjutan jika diperlukan.
```

### Prioritas Data

```text
1. Jadwal khusus yang rentangnya mencakup tanggal referensi
2. Jadwal mingguan reguler
3. Fallback data tidak tersedia
```

Bot tidak boleh menggabungkan jadwal khusus dan jadwal mingguan pada tanggal yang sama. Jika jadwal khusus berstatus Tutup dan berlangsung beberapa hari, bot harus mencari jadwal buka pertama setelah tanggal selesai untuk menjawab kapan outlet kembali beroperasi.

---

## Decision Matrix

| Kondisi | Respons Utama |
|---|---|
| Outlet sedang berada di dalam sesi buka | Informasikan sedang buka dan waktu tutup sesi berjalan. |
| Outlet belum buka hari ini | Informasikan masih tutup dan jam buka sesi pertama hari ini. |
| Outlet berada di antara dua sesi | Informasikan sedang istirahat/tutup sementara dan jam buka sesi berikutnya. |
| Seluruh sesi hari ini sudah selesai | Informasikan sudah tutup dan waktu buka berikutnya. |
| Hari berstatus Tutup | Informasikan tutup pada hari tersebut dan waktu buka berikutnya jika relevan. |
| Hari berstatus 24 Jam | Informasikan buka 24 jam pada tanggal/hari tersebut. |
| Jadwal melewati tengah malam dan waktu masih dalam rentang sesi | Informasikan sedang buka serta waktu tutup pada hari berikutnya. |
| Jadwal khusus tersedia | Gunakan jadwal khusus dan abaikan jadwal mingguan untuk tanggal referensi. |
| Outlet berada dalam rentang penutupan sementara | Informasikan periode tutup dan waktu buka kembali setelah rentang berakhir. |
| Konfigurasi tidak tersedia atau tidak valid | Jangan mengarang; tawarkan agent. |
| Intent OFF | Informasikan fitur sedang tidak tersedia dan tawarkan agent. |

---

## Mode Tampilan

### Mode 1 — Jadwal Hari Ini

Digunakan untuk pertanyaan seperti “Buka jam berapa?” atau “Tutup jam berapa?” tanpa hari/tanggal.

```text
Jam operasional [Nama Outlet] hari ini, Senin 13 Juli 2026:
08.00–12.00
13.00–21.00

Zona waktu: WIB.
```

Jika tutup:

```text
Hari ini [Nama Outlet] tutup ya Kak.
Outlet buka kembali Selasa, 14 Juli 2026 pukul 08.00 WIB.
```

### Mode 2 — Status Saat Ini

Jika sedang buka:

```text
Saat ini [Nama Outlet] masih buka ya Kak.
Outlet tutup pukul 21.00 WIB.
```

Jika belum buka:

```text
Saat ini [Nama Outlet] masih tutup ya Kak.
Hari ini outlet buka pukul 08.00 WIB.
```

Jika berada di antara dua sesi:

```text
Saat ini [Nama Outlet] sedang tutup sementara.
Outlet buka kembali pukul 13.00 WIB dan tutup pukul 21.00 WIB.
```

Jika seluruh sesi selesai:

```text
Saat ini [Nama Outlet] sudah tutup ya Kak.
Outlet buka kembali besok, Selasa 14 Juli 2026 pukul 08.00 WIB.
```

### Mode 3 — Hari atau Tanggal Tertentu

```text
Untuk Minggu, 19 Juli 2026, [Nama Outlet] buka pukul 09.00–17.00 WIB.
```

Jika jadwal khusus tutup:

```text
Pada Senin, 17 Agustus 2026, [Nama Outlet] tutup ya Kak.
Outlet buka kembali Selasa, 18 Agustus 2026 pukul 08.00 WIB.
```

Jika memiliki catatan customer-facing:

```text
Pada Senin, 17 Agustus 2026, [Nama Outlet] tutup karena libur operasional.
Outlet buka kembali Selasa, 18 Agustus 2026 pukul 08.00 WIB.
```

Jika outlet tutup dalam rentang beberapa hari:

```text
[Nama Outlet] sedang tutup sementara pada 20–26 Juli 2026 karena perbaikan outlet.
Outlet akan kembali buka Senin, 27 Juli 2026 pukul 08.00 WIB.
```

Jika customer bertanya tanggal di tengah periode penutupan:

```text
Pada 23 Juli 2026, [Nama Outlet] masih tutup sementara ya Kak.
Outlet akan kembali buka Senin, 27 Juli 2026 pukul 08.00 WIB.
```

### Mode 4 — Jadwal Mingguan

Hari dengan jadwal yang sama dikelompokkan.

```text
Jam operasional [Nama Outlet]:

Senin–Jumat: 08.00–21.00
Sabtu: 08.00–18.00
Minggu: Tutup

Zona waktu: WIB.
```

Jika ada dua sesi:

```text
Jam operasional [Nama Outlet]:

Senin–Sabtu:
08.00–12.00 dan 13.00–21.00

Minggu: Tutup
Zona waktu: WIB.
```

### Mode 5 — 24 Jam

```text
[Nama Outlet] buka 24 jam hari ini ya Kak.
Zona waktu outlet: WIB.
```

### Mode 6 — Fallback Data

```text
Maaf Kak, jam operasional [Nama Outlet] belum dapat dipastikan saat ini.
Saya bisa hubungkan Kakak ke agent outlet untuk konfirmasi.
```

---

## Aturan Response

- Wajib mencantumkan nama outlet.
- Untuk pertanyaan hari/tanggal, wajib mencantumkan tanggal lengkap jika sistem melakukan interpretasi relatif.
- Untuk respons berbasis waktu, wajib mencantumkan nama zona waktu umum seperti WIB, WITA, atau WIT.
- Format jam menggunakan `HH.mm`, contoh `08.00` dan `21.00`.
- Jangan menampilkan identifier teknis zona waktu kepada customer kecuali diperlukan; tampilkan `WIB`, bukan `Asia/Jakarta`.
- Respons harus ringkas dan mudah dibaca di WhatsApp.
- Jika outlet tutup, berikan waktu buka berikutnya jika dapat dihitung.
- Jika outlet memiliki dua sesi, jangan menyebut outlet buka sepanjang rentang yang mencakup jeda.
- Jika jadwal khusus memiliki catatan internal saja, bot tidak menampilkannya.
- Bot tidak boleh menebak jadwal berdasarkan alamat, histori, atau informasi lama.

---

## Conversation Context

Bot dapat mempertahankan konteks jam operasional dalam percakapan singkat.

Contoh:

```text
Customer: Hari Minggu buka?
Bot: Untuk Minggu, 19 Juli 2026, outlet buka 09.00–17.00 WIB.
Customer: Kalau jam 6 sore?
Bot: Pukul 18.00 WIB outlet sudah tutup ya Kak.
```

Konteks yang dapat dipertahankan:

- Outlet aktif.
- Tanggal atau hari terakhir yang dibahas.
- Jenis pertanyaan terakhir.
- Jadwal yang terakhir digunakan.

Konteks di-reset ketika customer memilih outlet lain atau conversation berpindah ke agent.

---

## Implikasi ke Pengaturan Jam Operasional

- Intent hanya dapat ON jika konfigurasi berstatus siap.
- Zona waktu wajib tersedia.
- Jadwal mingguan wajib lengkap untuk tujuh hari.
- Jadwal khusus harus dapat dibedakan dari jadwal reguler.
- Data yang belum disimpan tidak boleh digunakan bot.
- Menghapus konfigurasi aktif menyebabkan intent dinonaktifkan setelah konfirmasi owner.

---

## Implikasi ke Control Panel

- `get_operating_hours` default OFF sampai jadwal valid tersedia.
- Toggle ON ketika belum siap membuka dialog prasyarat, bukan langsung mengaktifkan intent.
- Jika jadwal sudah valid, intent dapat diaktifkan normal.
- Intent OFF tidak ditampilkan di greeting atau menu bantuan bot.
- Jika master switch OFF, intent tidak berjalan meskipun toggle outlet ON.

---

## Edge Cases

| # | Skenario | Perilaku Bot |
|---|---|---|
| 1 | Customer bertanya “sekarang buka?” tepat pada jam buka. | Status = buka. |
| 2 | Customer bertanya tepat pada jam tutup. | Status = tutup; cari waktu buka berikutnya. |
| 3 | Outlet memiliki sesi 08.00–12.00 dan 13.00–21.00; pesan pukul 12.30. | Informasikan tutup sementara dan buka kembali 13.00. |
| 4 | Jadwal Sabtu 18.00–02.00; pesan Minggu 01.00. | Masih dianggap berada dalam sesi Sabtu dan outlet buka sampai 02.00. |
| 5 | Hari reguler buka tetapi ada jadwal khusus tutup. | Gunakan jadwal khusus tutup. |
| 5A | Jadwal khusus Tutup berlaku 20–26 Juli dan customer bertanya pada 23 Juli. | Informasikan masih tutup sampai 26 Juli dan waktu buka pertama setelah periode selesai. |
| 5B | Jadwal khusus satu hari memiliki tanggal mulai dan selesai yang sama. | Perlakukan sebagai exception satu tanggal. |
| 5C | Penutupan sementara berakhir hari ini dan jadwal mingguan besok buka. | Informasikan outlet buka kembali besok sesuai jadwal mingguan. |
| 6 | Hari reguler tutup tetapi ada jadwal khusus buka. | Gunakan jadwal khusus buka. |
| 7 | Customer menyebut “Minggu” tanpa kata “setiap”. | Gunakan Minggu terdekat yang akan datang. |
| 8 | Customer menyebut “setiap Minggu”. | Gunakan jadwal mingguan reguler, bukan exception satu tanggal. |
| 9 | Customer menyebut tanggal tanpa tahun yang sudah lewat. | Gunakan kejadian terdekat yang akan datang dan tampilkan tanggal lengkap. |
| 10 | Customer bertanya tanggal lampau. | Jawab hanya jika jadwal historis tersedia; jika tidak, sampaikan tidak dapat dipastikan dan tawarkan agent. |
| 11 | Zona waktu outlet berbeda dengan customer. | Seluruh perhitungan mengikuti zona waktu outlet. |
| 12 | Jadwal invalid tetapi toggle secara tidak konsisten ON. | Jangan menjawab; fallback data dan tawarkan agent. |
| 13 | Intent OFF tetapi customer bertanya jam operasional. | Tampilkan fallback fitur tidak tersedia. |
| 14 | Master switch OFF. | Bot tidak mengklasifikasikan intent; conversation mengikuti handoff. |
| 15 | Customer pindah outlet. | Reset konteks dan gunakan jadwal outlet baru. |
| 16 | Outlet 24 jam tetapi memiliki penutupan khusus hari ini. | Jadwal khusus tutup mengalahkan status 24 jam reguler. |
| 17 | Customer bertanya “nanti malam” tanpa jam. | Evaluasi jadwal malam hari ini dan jawab rentang operasional yang relevan. |
| 18 | Tidak ada hari buka berikutnya dalam data yang valid. | Jangan mengarang; tawarkan agent. |

---

## Acceptance Criteria

### Functional

- [ ] Bot dapat mengklasifikasikan intent `get_operating_hours` dari variasi bahasa natural.
- [ ] Bot dapat membedakan pertanyaan jadwal mingguan, hari/tanggal, status saat ini, jam buka, jam tutup, dan waktu buka berikutnya.
- [ ] Bot menggunakan outlet dari conversation context.
- [ ] Bot menggunakan zona waktu outlet untuk semua perhitungan relatif.
- [ ] Bot memprioritaskan jadwal khusus yang mencakup tanggal referensi dibanding jadwal mingguan.
- [ ] Bot mendukung jadwal khusus satu hari ketika tanggal mulai dan selesai sama.
- [ ] Bot mendukung penutupan sementara dalam rentang beberapa hari.
- [ ] Saat customer bertanya di tengah periode tutup, bot menyebutkan periode penutupan dan waktu buka berikutnya.
- [ ] Setelah periode khusus berakhir, bot kembali menggunakan jadwal mingguan secara otomatis.
- [ ] Intent tetap aktif selama penutupan sementara jika toggle intent ON.
- [ ] Bot mendukung status Buka, Tutup, dan 24 Jam.
- [ ] Bot mendukung satu atau dua sesi operasional per hari.
- [ ] Bot mendukung sesi yang melewati tengah malam.
- [ ] Bot dapat mencari waktu buka berikutnya jika outlet tutup.
- [ ] Bot mengelompokkan hari dengan jadwal sama dalam respons mingguan.
- [ ] Bot tidak menjawab jika konfigurasi tidak tersedia atau tidak valid.

### Bot Behavior

- [ ] Respons menggunakan Bahasa Indonesia dengan sapaan yang konsisten.
- [ ] Nama outlet selalu dicantumkan.
- [ ] Respons relatif seperti besok atau Minggu menyertakan tanggal lengkap hasil interpretasi.
- [ ] Zona waktu ditampilkan menggunakan WIB, WITA, atau WIT jika relevan.
- [ ] Format jam menggunakan `HH.mm`.
- [ ] Saat tutup, bot memberikan waktu buka berikutnya jika tersedia.
- [ ] Saat berada di antara dua sesi, bot menjelaskan bahwa outlet tutup sementara.
- [ ] Bot tidak menampilkan catatan internal jadwal.
- [ ] Bot tidak mengarang jadwal dari alamat atau data lain.
- [ ] Follow-up customer dapat menggunakan hari/tanggal terakhir dalam konteks percakapan.

### Control & Readiness

- [ ] Master switch OFF selalu mengalahkan status intent outlet.
- [ ] Intent OFF tidak diproses dan tidak ditampilkan dalam greeting/menu.
- [ ] Intent tidak dapat diaktifkan sebelum pengaturan jam operasional valid.
- [ ] Jika data menjadi invalid saat intent ON, bot tidak memberikan jawaban asumsi.
- [ ] Perpindahan outlet mereset konteks intent.

### Performance & Reliability

- [ ] Respons jadwal yang datanya tersedia diberikan tanpa jeda percakapan yang tidak perlu.
- [ ] Evaluasi status konsisten pada batas jam buka, jam tutup, dan pergantian hari.
- [ ] Jika terjadi kegagalan membaca data, bot memberikan fallback dan menawarkan agent.

### Privacy

- [ ] Bot hanya menampilkan jadwal customer-facing.
- [ ] Catatan internal, identitas pengubah, dan histori perubahan tidak ditampilkan.

---

## Metrics

| Metrik | Tujuan |
|---|---|
| Jumlah penggunaan intent | Mengukur kebutuhan informasi jam operasional. |
| Persentase pertanyaan status saat ini | Mengetahui pola utama penggunaan. |
| Persentase jawaban berhasil | Mengukur kesiapan konfigurasi outlet. |
| Fallback karena data tidak siap | Mengidentifikasi outlet yang perlu memperbaiki jadwal. |
| Handoff setelah jawaban jam operasional | Mengukur apakah jawaban sudah cukup jelas. |
| Pertanyaan follow-up per conversation | Mengukur kualitas dan kelengkapan respons. |
| Distribusi pertanyaan hari/tanggal khusus | Mengukur pentingnya jadwal khusus. |

---

## Out of Scope (Future)

- Rekomendasi outlet alternatif yang sedang buka.
- Reminder ketika outlet akan buka.
- Estimasi antrean dan kepadatan.
- Cut-off order atau penerimaan laundry.
- Jadwal per layanan.
- Sinkronisasi eksternal.
- Voice note understanding.
- Multi-language.
- Jawaban berbasis lokasi customer.

---

## Open Questions

1. Apakah judul/catatan customer-facing perlu selalu disebut ketika outlet tutup, atau hanya ketika customer menanyakan alasannya?
2. Apakah pertanyaan tanggal lampau perlu didukung jika histori jadwal tersedia pada fase berikutnya?
3. Apakah fase berikutnya perlu menawarkan outlet terdekat yang sedang buka ketika outlet pilihan customer tutup?
