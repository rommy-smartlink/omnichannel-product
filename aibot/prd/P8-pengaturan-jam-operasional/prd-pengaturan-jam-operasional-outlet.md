# Pengaturan Jam Operasional Outlet — PRD

## Overview

Fitur Pengaturan Jam Operasional Outlet memungkinkan owner menetapkan jadwal operasional yang berlaku untuk masing-masing outlet. Pengaturan ini menjadi sumber data resmi untuk menentukan kapan outlet buka, tutup, beristirahat, buka 24 jam, atau memiliki perubahan jadwal pada tanggal tertentu.

Fitur ini merupakan prasyarat bagi intent bot `get_operating_hours`. Intent tersebut tidak boleh diaktifkan sebelum outlet memiliki zona waktu dan jadwal operasional yang valid. Dengan demikian, bot tidak menjawab berdasarkan asumsi atau jadwal yang belum dikonfirmasi oleh owner.

Tujuan: menyediakan sumber data jam operasional yang akurat, mudah dikelola, dan dapat digunakan secara konsisten oleh Bot Omnichannel maupun channel customer-facing lain pada fase berikutnya.

---

## User Stories

- As an **owner**, saya ingin **mengatur jam operasional setiap outlet** agar **informasi waktu buka dan tutup sesuai dengan kondisi outlet tersebut**.
- As an **owner**, saya ingin **menentukan zona waktu outlet** agar **status buka atau tutup dihitung berdasarkan waktu lokal outlet**.
- As an **owner**, saya ingin **menandai suatu hari sebagai buka, tutup, atau buka 24 jam** agar **jadwal mingguan dapat mewakili pola operasional outlet**.
- As an **owner**, saya ingin **mengatur dua sesi operasional dalam satu hari** agar **outlet yang memiliki jam istirahat tetap dapat direpresentasikan dengan benar**.
- As an **owner**, saya ingin **mengatur jadwal khusus pada tanggal tertentu** agar **hari libur atau perubahan jam tidak menggunakan jadwal mingguan yang salah**.
- As an **owner**, saya ingin **menyimpan jadwal tanpa langsung mengaktifkan bot** agar **saya dapat menyiapkan data terlebih dahulu**.
- As an **owner**, saya ingin **menyimpan sekaligus mengaktifkan intent jam operasional** agar **tidak perlu kembali ke Control Panel setelah konfigurasi selesai**.
- As an **owner**, saya ingin **mematikan intent tanpa menghapus jadwal** agar **jadwal dapat digunakan kembali ketika intent diaktifkan lagi**.

---

## Goals & Non-Goals

### Goals

- Menyediakan halaman pengaturan jam operasional per outlet.
- Menetapkan zona waktu outlet melalui pilihan berbasis wilayah, bukan input bebas GMT.
- Mendukung jadwal reguler Senin–Minggu.
- Mendukung status per hari: **Buka**, **Tutup**, atau **24 Jam**.
- Mendukung maksimal dua sesi operasional per hari.
- Mendukung jadwal yang selesai pada hari berikutnya.
- Mendukung jadwal khusus berdasarkan tanggal atau rentang tanggal.
- Memvalidasi kelengkapan jadwal sebelum intent `get_operating_hours` dapat diaktifkan.
- Menyediakan aksi **Simpan Saja** dan **Simpan & Aktifkan** ketika intent masih OFF.
- Menyediakan preview jadwal sebelum disimpan.
- Memastikan perubahan yang belum disimpan tidak memengaruhi jawaban bot.
- Menjaga jadwal tetap tersimpan ketika intent dimatikan.

### Non-Goals (MVP)

- Penjadwalan otomatis untuk mengaktifkan atau mematikan intent bot.
- Sinkronisasi otomatis dengan Google Business Profile atau platform eksternal.
- Pembuatan jadwal berbeda berdasarkan layanan di dalam outlet.
- Pengaturan kapasitas, jumlah antrean, atau cut-off penerimaan laundry.
- Pengaturan jam kerja karyawan atau shift kasir.
- Persetujuan berlapis sebelum perubahan jadwal berlaku.
- Riwayat versi lengkap dan pemulihan ke versi jadwal sebelumnya.
- Role outlet manager atau kasir untuk mengubah jadwal.
- Notifikasi otomatis kepada customer ketika jadwal berubah.

---

## Pengguna & Hak Akses

| Pengguna | Akses |
|---|---|
| Owner | Melihat, membuat, mengubah, menyimpan, dan mengaktifkan jam operasional outlet miliknya. |
| Outlet manager | Tidak memiliki akses pada MVP. |
| Customer | Tidak memiliki akses ke halaman pengaturan. |

Hanya outlet milik owner yang dapat dipilih dan dikonfigurasi.

---

## Status Konfigurasi

| Status | Definisi | Dampak ke Intent |
|---|---|---|
| Belum dikonfigurasi | Zona waktu atau jadwal belum pernah disimpan. | `get_operating_hours` wajib OFF. |
| Belum lengkap | Sebagian data sudah diisi tetapi belum memenuhi validasi. | Tidak dapat diaktifkan. |
| Siap diaktifkan | Jadwal valid dan tersimpan, tetapi intent masih OFF. | Dapat diaktifkan dari halaman ini atau Control Panel. |
| Aktif | Jadwal valid dan intent ON. | Bot dapat menjawab jam operasional. |

Status konfigurasi dan status intent adalah dua hal berbeda. Jadwal dapat berada dalam status **Siap diaktifkan** walaupun intent masih OFF.

---

## Struktur Pengaturan

### 1. Identitas Outlet

Informasi read-only:

- Nama outlet.
- Kode outlet jika tersedia.
- Kota atau wilayah outlet.
- Status intent jam operasional saat ini.

### 2. Zona Waktu Outlet

Zona waktu wajib menggunakan dropdown berbasis wilayah.

Format tampilan:

```text
[Nama zona waktu] — [Wilayah] ([UTC offset saat ini])
```

Pilihan utama untuk Indonesia:

```text
WIB — Asia/Jakarta (UTC+07:00)
WITA — Asia/Makassar (UTC+08:00)
WIT — Asia/Jayapura (UTC+09:00)
```

Aturan:

- Zona waktu wajib dipilih sebelum jadwal dapat disimpan sebagai konfigurasi valid.
- Sistem dapat menyarankan zona waktu berdasarkan alamat outlet.
- Owner tetap dapat mengubah pilihan tersebut.
- Dropdown dapat dicari melalui kata seperti `Jakarta`, `WIB`, `UTC+7`, atau `Indonesia`.
- Tidak menyediakan input bebas seperti `GMT +7`.
- Offset UTC ditampilkan sebagai penjelas; identitas utama tetap wilayah zona waktu.

### 3. Jadwal Mingguan

Owner mengatur Senin sampai Minggu.

Status yang tersedia untuk setiap hari:

| Status | Perilaku |
|---|---|
| Buka | Wajib memiliki minimal satu sesi jam buka dan tutup. |
| Tutup | Tidak membutuhkan input waktu. |
| 24 Jam | Tidak membutuhkan input waktu dan berlaku sepanjang hari lokal outlet. |

Untuk status **Buka**, owner dapat menambahkan maksimal dua sesi.

Contoh:

```text
Senin
08.00–12.00
13.00–21.00
```

Dua sesi digunakan untuk outlet yang memiliki jam istirahat. Jika hanya satu sesi, sesi kedua tidak perlu ditampilkan.

### 4. Jadwal Melewati Tengah Malam

Jadwal dapat berakhir pada hari berikutnya.

Contoh:

```text
Sabtu: 18.00–02.00 (+1 hari)
```

Sistem wajib menampilkan indikator `+1 hari` agar owner memahami bahwa waktu tutup berada pada hari kalender berikutnya.

### 5. Jadwal Khusus

Jadwal khusus digunakan untuk membuat pengecualian terhadap jadwal mingguan pada satu tanggal atau beberapa tanggal berurutan. Seluruh jadwal khusus memakai **satu model rentang tanggal** agar input satu hari dan beberapa hari tetap konsisten.

Aturan rentang tanggal:

- `Tanggal mulai = tanggal selesai` berarti jadwal khusus berlaku untuk satu tanggal.
- `Tanggal selesai > tanggal mulai` berarti jadwal khusus berlaku untuk seluruh tanggal di dalam rentang tersebut.
- Saat tanggal mulai dipilih, tanggal selesai otomatis mengikuti tanggal mulai dan dapat diubah jika jadwal berlaku lebih dari satu hari.
- Tanggal selesai tidak boleh lebih awal daripada tanggal mulai.

Contoh satu tanggal:

```text
Tanggal mulai: 17 Agustus 2026
Tanggal selesai: 17 Agustus 2026
Status: Tutup
Judul: Hari Kemerdekaan
```

Contoh beberapa hari:

```text
Tanggal mulai: 20 Juli 2026
Tanggal selesai: 26 Juli 2026
Status: Tutup
Judul: Perbaikan outlet
```

Tipe/status jadwal khusus:

| Status | Contoh Penggunaan |
|---|---|
| Tutup | Hari libur nasional, perbaikan outlet, renovasi, atau kegiatan internal. |
| Buka dengan jam khusus | Outlet buka lebih singkat atau lebih lama dibanding jadwal mingguan. |
| 24 Jam | Outlet beroperasi 24 jam selama tanggal atau rentang yang dipilih. |

Data jadwal khusus:

| Field | Status | Keterangan |
|---|---|---|
| Status jadwal | Wajib | Tutup, Buka dengan jam khusus, atau 24 Jam. |
| Tanggal mulai | Wajib | Tanggal pertama jadwal berlaku. |
| Tanggal selesai | Wajib | Default sama dengan tanggal mulai; dapat diubah untuk periode beberapa hari. |
| Jam buka dan tutup | Kondisional | Wajib jika status Buka dengan jam khusus. Mendukung maksimal dua sesi. |
| Judul | Wajib | Nama singkat seperti `Hari Kemerdekaan` atau `Perbaikan outlet`. |
| Catatan customer-facing | Opsional | Penjelasan aman untuk customer, misalnya `Outlet tutup sementara karena perbaikan.` |

**Prioritas jadwal:**

```text
Jadwal khusus yang aktif pada tanggal referensi
>
Jadwal mingguan
```

Jika suatu tanggal masuk ke dalam rentang jadwal khusus, jadwal mingguan pada tanggal tersebut tidak digunakan. Setelah tanggal selesai terlewati, jadwal mingguan otomatis berlaku kembali tanpa tindakan owner.

Untuk status **Tutup**, intent `get_operating_hours` tetap dapat ON. Penutupan outlet bukan berarti capability dimatikan; capability tetap dibutuhkan agar bot dapat menjelaskan bahwa outlet sedang libur dan kapan outlet buka kembali.

### Form Tambah Jadwal Khusus

```text
Tambah Jadwal Khusus

Status jadwal
[ Tutup ▼ ]

Tanggal mulai
[ 17 Agustus 2026 ]

Tanggal selesai
[ 17 Agustus 2026 ]

Judul
[ Hari Kemerdekaan ]

Catatan untuk customer
[ Outlet tutup pada Hari Kemerdekaan. ]

[Batalkan] [Simpan]
```

Daftar jadwal khusus menyesuaikan format periode:

```text
17 Agustus 2026
Tutup · Hari Kemerdekaan
```

```text
20–26 Juli 2026
Tutup · Perbaikan outlet
```

```text
28 Desember 2026–3 Januari 2027
Tutup · Libur akhir tahun
```

Jadwal khusus yang rentangnya tumpang tindih tidak boleh disimpan. Owner harus mengubah atau menghapus jadwal yang sudah ada agar sistem memiliki satu sumber jadwal yang jelas untuk setiap tanggal.

---

## Flow

### Alur Konfigurasi Pertama

```text
1. Owner membuka Pengaturan Outlet → Jam Operasional.
2. Sistem menampilkan status "Belum dikonfigurasi".
3. Owner memilih zona waktu outlet.
4. Owner mengatur jadwal Senin–Minggu.
5. Owner menambahkan jadwal khusus jika diperlukan.
6. Sistem melakukan validasi.
7. Owner melihat preview jadwal.
8. Owner memilih:
   a. Simpan Saja → jadwal tersimpan, intent tetap OFF.
   b. Simpan & Aktifkan → jadwal tersimpan, intent menjadi ON.
9. Sistem menampilkan status terbaru.
```

### Alur Edit Jadwal Saat Intent Aktif

```text
1. Owner membuka jadwal outlet yang sudah aktif.
2. Owner melakukan perubahan.
3. Perubahan hanya berada pada form dan belum memengaruhi bot.
4. Owner memilih Simpan Perubahan.
5. Setelah berhasil disimpan, jadwal baru menjadi sumber jawaban bot.
6. Intent tetap ON selama jadwal hasil perubahan masih valid.
```

### Alur dari Control Panel

```text
1. Owner mencoba mengaktifkan get_operating_hours.
2. Sistem menemukan jadwal belum dikonfigurasi atau belum lengkap.
3. Toggle tidak berubah menjadi ON.
4. Sistem menampilkan dialog prasyarat.
5. Owner memilih "Atur Jam Operasional".
6. Owner diarahkan ke halaman pengaturan outlet terkait.
7. Owner menyimpan atau menyimpan sekaligus mengaktifkan.
```

---

## UI / UX

### Layout Utama

```text
┌────────────────────────────────────────────────────┐
│ Jam Operasional — Outlet A                         │
│ Status: Belum dikonfigurasi                        │
├────────────────────────────────────────────────────┤
│ Zona waktu *                                       │
│ [ WIB — Asia/Jakarta (UTC+07:00)              ▾ ] │
├────────────────────────────────────────────────────┤
│ Jadwal mingguan                                    │
│ Senin    [Buka ▾]  [08.00]–[21.00] [+ Sesi]       │
│ Selasa   [Buka ▾]  [08.00]–[21.00] [+ Sesi]       │
│ ...                                                │
│ Minggu   [Tutup ▾]                                 │
├────────────────────────────────────────────────────┤
│ Jadwal khusus                                      │
│ [+ Tambah Jadwal Khusus]                           │
├────────────────────────────────────────────────────┤
│ Preview                                            │
│ Senin–Sabtu: 08.00–21.00                          │
│ Minggu: Tutup                                      │
│ Zona waktu: WIB                                    │
├────────────────────────────────────────────────────┤
│ [Batal]               [Simpan Saja] [Simpan & Aktifkan]│
└────────────────────────────────────────────────────┘
```

### CTA Berdasarkan Kondisi

| Kondisi | CTA |
|---|---|
| Konfigurasi pertama, intent OFF | **Simpan Saja** dan **Simpan & Aktifkan** |
| Jadwal sudah tersimpan, intent OFF | **Simpan Perubahan** dan **Simpan & Aktifkan** |
| Jadwal aktif | **Simpan Perubahan** |
| Form belum valid | CTA simpan disabled dan error ditampilkan pada bagian terkait. |

### Konfirmasi Perubahan Zona Waktu

Jika zona waktu diubah ketika intent aktif:

```text
Ubah zona waktu outlet?

Jam operasional akan dihitung menggunakan zona waktu baru.
Nilai jam buka dan tutup tidak berubah. Pastikan jadwal masih sesuai
untuk waktu lokal outlet.

Zona waktu sebelumnya:
WIB — Asia/Jakarta (UTC+07:00)

Zona waktu baru:
WITA — Asia/Makassar (UTC+08:00)

[Batal] [Ubah Zona Waktu]
```

### Konfirmasi Menghapus Jadwal Aktif

Owner tidak dapat meninggalkan outlet tanpa jadwal valid ketika intent masih ON.

Jika tindakan owner membuat jadwal tidak valid atau menghapus seluruh konfigurasi:

```text
Nonaktifkan Jam Operasional?

Perubahan ini membuat jadwal outlet tidak dapat digunakan oleh bot.
Intent Jam Operasional akan dinonaktifkan, tetapi data lain tidak terpengaruh.

[Batal] [Hapus & Nonaktifkan]
```

---

## Validation Rules

| Rule | Hasil Jika Tidak Valid |
|---|---|
| Zona waktu wajib dipilih. | Jadwal tidak dapat disimpan sebagai konfigurasi valid. |
| Ketujuh hari wajib memiliki status. | Tampilkan hari yang belum lengkap. |
| Hari berstatus Buka wajib memiliki minimal satu sesi. | Tampilkan error pada hari terkait. |
| Hari berstatus Tutup atau 24 Jam tidak boleh memiliki sesi aktif. | Sesi dihapus setelah konfirmasi atau status tidak dapat disimpan. |
| Jam buka dan tutup dalam satu sesi tidak boleh sama. | Tampilkan error. |
| Dua sesi pada hari yang sama tidak boleh tumpang tindih. | Tampilkan error pada kedua sesi. |
| Maksimal dua sesi per hari. | Tombol tambah sesi tidak tersedia setelah sesi kedua. |
| Jadwal melewati tengah malam wajib ditandai `+1 hari`. | Sistem meminta konfirmasi interpretasi jadwal. |
| Rentang jadwal khusus tidak boleh tumpang tindih dengan jadwal khusus lain. | Owner harus mengubah atau menghapus salah satu jadwal. |
| Tanggal selesai tidak boleh sebelum tanggal mulai. | Tampilkan error. |
| Status Buka dengan jam khusus wajib memiliki minimal satu sesi. | Tampilkan error pada jadwal khusus terkait. |
| Status Tutup atau 24 Jam tidak boleh memiliki sesi aktif. | Sesi dihapus setelah konfirmasi atau jadwal tidak dapat disimpan. |

---

## Decision Matrix Aktivasi

| Zona Waktu | Jadwal Mingguan | Jadwal Valid | Intent Saat Ini | Aksi yang Diizinkan |
|---|---|---|---|---|
| Belum ada | Apa pun | Tidak | OFF | Simpan draft form; tidak dapat aktif. |
| Ada | Belum lengkap | Tidak | OFF | Simpan perbaikan; tidak dapat aktif. |
| Ada | Lengkap | Ya | OFF | Simpan Saja atau Simpan & Aktifkan. |
| Ada | Lengkap | Ya | ON | Simpan Perubahan; intent tetap ON. |
| Dihapus/tidak valid | Tidak valid | Tidak | ON | Wajib konfirmasi untuk menonaktifkan intent. |

Jika master switch WABA sedang OFF, **Simpan & Aktifkan** tetap dapat mengatur intent outlet menjadi ON. Namun sistem harus memberi informasi bahwa bot baru dapat merespons setelah master switch WABA kembali ON.

---

## Implikasi ke Control Panel

- `get_operating_hours` memiliki default OFF untuk outlet baru dan existing outlet yang belum memiliki konfigurasi.
- Row intent menampilkan status konfigurasi: **Perlu pengaturan**, **Siap diaktifkan**, atau **Aktif**.
- Toggle ON tidak dapat disimpan jika jadwal belum valid.
- CTA **Atur Jam Operasional** mengarahkan owner ke halaman pengaturan outlet terkait.
- Aksi **Simpan & Aktifkan** dari halaman ini memperbarui status intent pada Control Panel.
- Mematikan intent dari Control Panel tidak menghapus jadwal.
- Jadwal yang tidak valid tidak dihitung sebagai intent aktif dalam greeting atau menu bot.

---

## Implikasi ke Intent Bot

- Bot menggunakan zona waktu outlet untuk menafsirkan `sekarang`, `hari ini`, `besok`, dan tanggal pergantian hari.
- Jadwal khusus selalu diprioritaskan dibanding jadwal mingguan.
- Bot hanya dapat menjawab jika jadwal valid dan intent ON.
- Bot tidak boleh menggunakan alamat outlet untuk menebak zona waktu pada saat menjawab; zona waktu harus berasal dari konfigurasi yang sudah disimpan.
- Perubahan form yang belum disimpan tidak digunakan oleh bot.

---

## Edge Cases

| # | Skenario | Perilaku Sistem |
|---|---|---|
| 1 | Outlet baru belum memiliki jadwal. | Status Belum dikonfigurasi; intent OFF. |
| 2 | Existing outlet belum memiliki menu jam operasional sebelumnya. | Tidak membuat jadwal otomatis; intent tetap OFF sampai owner mengatur. |
| 3 | Owner memilih Buka tetapi belum mengisi jam. | Form belum valid; tidak dapat diaktifkan. |
| 4 | Owner mengubah hari menjadi Tutup saat masih ada sesi. | Sistem meminta konfirmasi untuk menghapus sesi hari tersebut. |
| 5 | Sesi kedua tumpang tindih dengan sesi pertama. | Simpan diblokir sampai konflik diperbaiki. |
| 6 | Jam buka 18.00 dan tutup 02.00. | Ditampilkan sebagai 18.00–02.00 (+1 hari). |
| 7 | Outlet buka 24 jam selama tujuh hari. | Preview menampilkan Senin–Minggu: 24 Jam. |
| 8 | Jadwal khusus Tutup bertabrakan dengan jadwal mingguan Buka. | Jadwal khusus digunakan untuk tanggal atau rentang tersebut. |
| 9 | Owner membuat jadwal satu hari. | Tanggal mulai dan selesai menggunakan tanggal yang sama. |
| 10 | Owner membuat penutupan selama satu minggu. | Satu jadwal khusus Tutup menggunakan tanggal mulai dan selesai yang berbeda. |
| 11 | Dua rentang jadwal khusus saling tumpang tindih. | Sistem menolak konflik dan meminta salah satu jadwal diubah. |
| 12 | Penutupan sementara sedang berlangsung ketika intent ON. | Intent tetap ON agar bot dapat menjelaskan status tutup dan waktu buka kembali. |
| 13 | Periode jadwal khusus berakhir. | Jadwal mingguan otomatis berlaku kembali. |
| 14 | Owner mematikan intent. | Jadwal tetap tersimpan dan dapat dikelola. |
| 15 | Owner menghapus konfigurasi ketika intent aktif. | Tampilkan konfirmasi; intent ikut OFF jika tindakan diteruskan. |
| 16 | Owner mengganti zona waktu saat intent aktif. | Tampilkan konfirmasi; jam tidak bergeser otomatis. |
| 17 | Master switch WABA OFF saat owner memilih Simpan & Aktifkan. | Jadwal dan intent tersimpan; tampilkan info bahwa bot tetap nonaktif karena master OFF. |
| 18 | Owner menutup halaman dengan perubahan belum disimpan. | Tampilkan konfirmasi meninggalkan halaman. |
| 19 | Jadwal khusus sudah lewat. | Tetap dapat dilihat sebagai riwayat ringan, tetapi tidak memengaruhi jadwal berjalan. |

---

## Acceptance Criteria

### Functional

- [ ] Owner dapat membuka pengaturan jam operasional untuk outlet miliknya.
- [ ] Sistem menampilkan status konfigurasi dan status intent secara terpisah.
- [ ] Owner dapat memilih zona waktu melalui dropdown berbasis wilayah.
- [ ] Pilihan Indonesia mencakup WIB, WITA, dan WIT dengan identitas wilayah yang sesuai.
- [ ] Owner dapat mengatur status Buka, Tutup, atau 24 Jam untuk Senin–Minggu.
- [ ] Owner dapat menambahkan maksimal dua sesi operasional per hari.
- [ ] Sistem mendukung jadwal melewati tengah malam dengan label `+1 hari`.
- [ ] Owner dapat membuat jadwal khusus menggunakan tanggal mulai dan tanggal selesai.
- [ ] Tanggal selesai otomatis mengikuti tanggal mulai pada input awal.
- [ ] Satu tanggal dapat disimpan dengan tanggal mulai dan selesai yang sama.
- [ ] Beberapa hari dapat disimpan sebagai satu rentang tanpa membuat jadwal per tanggal.
- [ ] Owner dapat memilih status Tutup, Buka dengan jam khusus, atau 24 Jam.
- [ ] Sistem menolak rentang jadwal khusus yang tumpang tindih.
- [ ] Jadwal khusus mengalahkan jadwal mingguan pada seluruh tanggal di dalam rentang.
- [ ] Setelah periode jadwal khusus berakhir, jadwal mingguan otomatis berlaku kembali.
- [ ] Penutupan sementara tidak menonaktifkan intent `get_operating_hours`.
- [ ] Owner dapat melihat preview jadwal sebelum menyimpan.
- [ ] Owner dapat memilih Simpan Saja atau Simpan & Aktifkan ketika intent masih OFF.
- [ ] Simpan & Aktifkan hanya berhasil jika semua prasyarat valid.
- [ ] Mematikan intent tidak menghapus jadwal.
- [ ] Menghapus atau membuat jadwal tidak valid saat intent aktif memerlukan konfirmasi dan menonaktifkan intent.

### UX

- [ ] Error validasi ditampilkan pada hari atau field yang bermasalah.
- [ ] CTA aktivasi tidak tersedia ketika konfigurasi belum valid.
- [ ] Perubahan yang belum disimpan tidak memengaruhi bot.
- [ ] Sistem memperingatkan owner sebelum meninggalkan halaman dengan perubahan belum disimpan.
- [ ] Perubahan zona waktu saat intent aktif membutuhkan konfirmasi.
- [ ] Preview menampilkan zona waktu, jadwal mingguan, dan jadwal khusus yang relevan.

### Bot Readiness

- [ ] Intent `get_operating_hours` default OFF untuk outlet tanpa konfigurasi.
- [ ] Existing outlet tidak menerima jadwal default berdasarkan asumsi.
- [ ] Jadwal dinyatakan siap hanya jika zona waktu dan tujuh hari operasional valid.
- [ ] Setelah Simpan & Aktifkan berhasil, Control Panel menampilkan intent ON.
- [ ] Jika master switch WABA OFF, status intent dapat ON tetapi bot tetap mengikuti master switch.

### Security & Governance

- [ ] Owner hanya dapat mengatur outlet miliknya.
- [ ] Perubahan menyimpan informasi siapa yang terakhir mengubah dan kapan diperbarui.
- [ ] Customer tidak dapat melihat halaman pengaturan atau catatan internal.

---

## Metrics

| Metrik | Tujuan |
|---|---|
| Outlet dengan konfigurasi lengkap | Mengukur kesiapan data jam operasional. |
| Outlet yang mengaktifkan intent setelah konfigurasi | Mengukur konversi setup ke aktivasi. |
| Validasi form gagal per rule | Menemukan bagian form yang membingungkan. |
| Perubahan jadwal khusus | Mengukur kebutuhan exception schedule. |
| Intent dinonaktifkan karena jadwal dihapus/tidak valid | Mengukur risiko konfigurasi. |
| Waktu median menyelesaikan konfigurasi pertama | Mengukur kemudahan setup. |

---

## Out of Scope (Future)

- Import jadwal dari platform eksternal.
- Approval perubahan jadwal oleh role lain.
- Template jadwal untuk diterapkan ke banyak outlet sekaligus.
- Bulk edit banyak outlet.
- Jadwal musiman yang berulang otomatis setiap tahun.
- Notifikasi perubahan jam operasional ke customer.
- Analitik kunjungan berdasarkan jam operasional.
- Pengaturan cut-off order yang terpisah dari jam tutup outlet.

---

## Open Questions

1. Apakah catatan customer-facing selalu ditampilkan ketika tersedia, atau hanya ketika customer menanyakan alasan outlet tutup?
2. Apakah jadwal khusus yang sudah lewat perlu dapat dihapus permanen atau cukup diarsipkan otomatis?
3. Apakah fase berikutnya memerlukan template jadwal untuk owner dengan banyak outlet?
