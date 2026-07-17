# Bot Control Panel — PRD (Revisi Jam Operasional)

## Overview

Bot Control Panel adalah dashboard bagi owner laundry untuk mengontrol bot pada level WABA dan per outlet. Owner dapat menggunakan master switch untuk menghentikan atau menjalankan bot pada seluruh outlet dalam satu WABA, serta mengaktifkan atau menonaktifkan capability tertentu pada masing-masing outlet.

Revisi ini menambahkan konsep **intent dengan prasyarat konfigurasi**. Intent `get_operating_hours` tidak dapat diperlakukan seperti toggle biasa karena bot membutuhkan zona waktu dan jadwal operasional yang valid sebelum dapat menjawab customer. Karena itu, toggle Jam Operasional berfungsi sekaligus sebagai entry point menuju proses setup ketika konfigurasi belum siap.

Tujuan: memberikan kontrol bot yang granular tanpa membiarkan capability aktif sebelum data pendukungnya tersedia dan valid.

---

## User Stories

- As an **owner**, saya ingin **mengaktifkan atau menonaktifkan master switch untuk satu WABA** agar **dapat menghentikan atau menjalankan bot pada seluruh outlet dengan cepat**.
- As an **owner**, saya ingin **mengaktifkan atau menonaktifkan intent tertentu per outlet** agar **hanya capability yang siap yang tersedia bagi customer**.
- As an **owner**, saya ingin **melihat status konfigurasi intent Jam Operasional** agar **memahami mengapa toggle belum dapat diaktifkan**.
- As an **owner**, saya ingin **menekan toggle Jam Operasional sebagai akses menuju setup** agar **tidak perlu mencari menu pengaturannya secara manual**.
- As an **owner**, saya ingin **menyimpan dan mengaktifkan Jam Operasional dari halaman pengaturan** agar **tidak perlu kembali ke Control Panel**.
- As an **owner**, saya ingin **mematikan intent Jam Operasional tanpa kehilangan jadwal** agar **dapat mengaktifkannya kembali tanpa setup ulang**.
- As an **owner**, saya ingin **melihat semua outlet Omnichannel beserta status capability-nya** agar **dapat memahami kesiapan bot dengan cepat**.

---

## Goals & Non-Goals

### Goals

- Master switch ON/OFF per WABA.
- Konfigurasi per-intent untuk setiap outlet yang terdaftar di Omnichannel.
- Master switch OFF tidak menghapus atau mengubah konfigurasi per-outlet.
- `talk_to_agent` selalu tersedia dan tidak dapat dimatikan.
- Menampilkan status kesiapan data untuk intent yang memiliki prasyarat.
- `get_operating_hours` default OFF sampai jadwal valid tersedia.
- Menjadikan toggle Jam Operasional sebagai entry point setup jika konfigurasi belum tersedia.
- Mencegah owner menyimpan state ON untuk Jam Operasional jika jadwal belum valid.
- Mendukung aktivasi Jam Operasional dari halaman pengaturan melalui **Simpan & Aktifkan**.
- Menjaga jadwal tetap tersimpan ketika intent dimatikan.
- Menampilkan hanya intent aktif dan siap pada greeting/menu customer.
- Menjaga intent Jam Operasional tetap ON ketika outlet sedang berada dalam jadwal khusus Tutup; status libur bukan alasan menonaktifkan capability.
- Memastikan existing outlet tidak memperoleh konfigurasi jam operasional berdasarkan asumsi.
- Akses Control Panel terbatas untuk owner.

### Non-Goals (MVP)

- Bulk action untuk banyak outlet.
- Schedule otomatis master switch atau intent.
- Maintenance mode dengan custom message.
- Role outlet manager atau kasir.
- Analytics conversation di dalam Control Panel.
- Notifikasi perubahan setting kepada owner lain.
- Approval sebelum setting berlaku.
- Konfirmasi ketika master switch diubah.
- Setup jam operasional langsung di dalam modal Control Panel.
- Menjadikan seluruh intent sebagai prerequisite-aware pada MVP; perubahan khusus difokuskan pada `get_operating_hours`.

---

## Affected Intents

| Intent | Dapat Diubah | Default Outlet Baru | Prasyarat |
|---|---:|---:|---|
| `check_laundry_status` | Ya | ON | Mengikuti kesiapan fitur existing. |
| `check_ticket_status` | Ya | ON | Mengikuti kesiapan fitur existing. |
| `get_outlet_services` | Ya | ON | Mengikuti kesiapan fitur existing. |
| `get_operating_hours` | Ya, dengan validasi | **OFF** | Zona waktu dan jadwal operasional valid. |
| `get_active_promos` | Ya | ON | Mengikuti kesiapan fitur existing. |
| `get_member_info` | Ya | ON | Mengikuti kesiapan fitur existing. |
| `talk_to_agent` | Tidak | Selalu ON | Tidak ada; wajib tersedia. |
| `unknown` | Tidak | Internal | Tidak ditampilkan sebagai toggle. |

`talk_to_agent` selalu ON agar customer memiliki jalur eskalasi ke manusia.

---

## Default State & Existing Outlet

### WABA Baru

- Master switch default **ON**.

### Outlet Baru yang Terdaftar Omnichannel

- Lima intent configurable selain Jam Operasional mengikuti default existing: **ON**.
- `get_operating_hours` default **OFF**.
- Card outlet menampilkan **24 Jam** hanya untuk skema default. Outlet dengan jadwal kustom tidak menampilkan info jam operasional di card.

### Existing Outlet

- `get_operating_hours` ditetapkan **OFF** jika belum memiliki konfigurasi jam operasional yang tervalidasi.
- Sistem tidak boleh membuat jadwal default berdasarkan alamat, jam umum bisnis, histori transaksi, atau asumsi lain.
- Owner harus mengatur dan mengonfirmasi jadwal sebelum intent dapat aktif.

---

## Status Jam Operasional di Card Control Panel

Card outlet hanya menampilkan status jam operasional jika outlet menggunakan skema default 24 jam:

| Status | Kondisi |
|---|---|
| 24 Jam | Outlet menggunakan skema default 24 jam. |
| (tidak tampil) | Outlet menggunakan jadwal kustom. Detail di modal capability. |

---

## Master Switch Behavior

### Saat ON

- Konfigurasi per-outlet berlaku normal.
- Intent ON dan siap dapat digunakan bot.
- Intent OFF atau belum siap tidak digunakan bot.

### Saat OFF

- Bot tidak mengirim greeting.
- Bot tidak melakukan intent classification.
- Conversation langsung diarahkan ke agent melalui silent handoff.
- Seluruh konfigurasi per-outlet tetap tersimpan.
- Owner tetap dapat membuka dan mengubah konfigurasi outlet.
- Owner tetap dapat menyiapkan dan mengaktifkan Jam Operasional, tetapi bot baru berjalan ketika master switch kembali ON.

### Override Priority

```text
Master switch OFF
>
Intent outlet OFF / belum siap
>
Intent outlet ON dan siap
```

Master switch selalu memiliki prioritas tertinggi.

---

## Bot Flow Integration

```text
1. Customer mengirim pesan ke outlet X dalam WABA W.
2. Sistem membaca master switch WABA W.
3. Jika master OFF:
   - skip greeting;
   - skip classification;
   - silent handoff ke agent.
4. Jika master ON:
   - sistem membaca status intent outlet X;
   - intent ON dan siap → capability berjalan normal;
   - intent OFF → fallback intent tidak tersedia;
   - intent ON tetapi prasyarat tidak siap → diperlakukan OFF + fallback data;
   - semua configurable intent OFF/tidak siap → silent handoff tanpa greeting/menu.
5. talk_to_agent selalu tersedia.
```

### Greeting & Menu

Greeting hanya menampilkan capability yang memenuhi dua kondisi:

```text
Intent = ON
DAN
Prasyarat data = siap
```

Jam Operasional tidak ditampilkan jika:

- intent OFF;
- konfigurasi belum tersedia;
- konfigurasi belum lengkap;
- konfigurasi tidak valid.

### Fallback Intent OFF

```text
Maaf Kak, fitur ini sedang tidak tersedia di outlet ini.
Saya bisa hubungkan Kakak ke agent untuk dibantu lebih lanjut.
```

### Fallback Data Tidak Siap

Digunakan jika terjadi state tidak konsisten:

```text
Maaf Kak, informasi jam operasional outlet belum dapat dipastikan saat ini.
Saya bisa hubungkan Kakak ke agent outlet untuk konfirmasi.
```

---

## UI / UX

### Owner Dashboard Layout

```text
┌──────────────────────────────────────────────────────────┐
│ Bot Control Panel                                        │
├──────────────────────────────────────────────────────────┤
│ WABA: [ WABA-1 (Utama)                              ▾ ] │
├──────────────────────────────────────────────────────────┤
│ Status Bot: [ ●──── ON ]  Diperbarui 2 jam lalu         │
│ Saat OFF, semua outlet langsung diarahkan ke agent.      │
├──────────────────────────────────────────────────────────┤
│ Search: [________________] Sort: [Nama A-Z          ▾ ] │
├──────────────────────────────────────────────────────────┤
│ Outlet A — Jakarta Pusat                                │
│ 5 capability aktif                                     › │
├──────────────────────────────────────────────────────────┤
│ Outlet B — Bandung                                      │
│ 6 capability aktif · Jam Operasional 24 Jam           › │
└──────────────────────────────────────────────────────────┘
```

### Outlet Card

Card tetap read-only dan menampilkan ringkasan:

- Nama outlet.
- Kota atau wilayah.
- Jumlah intent aktif dan siap.
- Status Jam Operasional di card: **24 Jam** jika skema default. Tidak tampil jika jadwal kustom.
- Terakhir diperbarui.

Owner membuka detail dengan menekan card.

### Modal Pengaturan Outlet

```text
┌────────────────────────────────────────────────────┐
│ Outlet A — Jakarta Pusat                       ✕  │
├────────────────────────────────────────────────────┤
│ Cek status laundry                 [ ●──── ON ]    │
│ Cek status tiket                   [ ●──── ON ]    │
│ Layanan outlet                     [ ●──── ON ]    │
│                                                    │
│ Jam operasional                                    │
│ 24 Jam                                             │
│ [Kelola Jam Operasional]           [ ●──── ON ]    │
│                                                    │
│ Promo aktif                        [ ●──── ON ]    │
│ Info member                        [ ●──── ON ]    │
│ Hubungi agen · Selalu aktif        [ ●──── ON ] 🔒│
├────────────────────────────────────────────────────┤
│                    [Batal] [Simpan]                │
└────────────────────────────────────────────────────┘
```

Jika jadwal valid tetapi intent OFF:

```text
Jam operasional
Jadwal tersedia · terakhir diperbarui 13 Juli 2026
[Kelola Jam Operasional]              [ ○──── OFF ]
```

Jika aktif:

```text
Jam operasional
Aktif · menggunakan jadwal yang telah disimpan
[Kelola Jam Operasional]              [ ●──── ON ]
```

Jika sedang ada jadwal khusus Tutup yang aktif:

```text
Jam operasional
Aktif · Tutup sementara 20–26 Juli 2026
[Kelola Jam Operasional]              [ ●──── ON ]
```

Toggle tetap ON karena capability masih dibutuhkan untuk menjelaskan status libur dan waktu outlet kembali buka.

---

## Interaction: Toggle Intent Umum

```text
1. Owner membuka modal outlet.
2. Owner mengubah satu atau lebih toggle intent umum.
3. Perubahan berada pada state lokal modal.
4. Owner memilih Simpan.
5. Jika berhasil, modal ditutup dan list diperbarui.
6. Jika gagal, modal tetap terbuka dan perubahan lokal dipertahankan.
7. Owner memilih Batal → perubahan lokal dibuang.
```

Tidak ada perubahan terhadap pola ini untuk intent selain Jam Operasional.

---

## Interaction: Toggle Jam Operasional

### Kondisi 1 — Belum Dikonfigurasi

Ketika owner menekan toggle OFF → ON:

- Toggle tidak berubah menjadi ON.
- Perubahan tidak masuk ke daftar perubahan lokal modal.
- Sistem menampilkan dialog prasyarat.

```text
Atur jam operasional terlebih dahulu

Bot memerlukan zona waktu dan jadwal operasional outlet agar dapat
memberikan informasi yang akurat kepada customer.

[Batal] [Atur Jam Operasional]
```

Jika memilih **Atur Jam Operasional**, owner diarahkan ke halaman pengaturan outlet terkait.

### Kondisi 2 — Konfigurasi Belum Lengkap

Dialog:

```text
Selesaikan pengaturan jam operasional

Sebagian pengaturan outlet belum lengkap. Lengkapi zona waktu dan
jadwal tujuh hari sebelum capability dapat diaktifkan.

[Batal] [Lanjutkan Pengaturan]
```

### Kondisi 3 — Konfigurasi Siap

- Toggle dapat diubah menjadi ON seperti intent lain.
- Perubahan baru berlaku setelah owner menekan **Simpan**.

### Kondisi 4 — Intent Aktif

- Toggle dapat diubah menjadi OFF.
- Menonaktifkan intent tidak menghapus jadwal.
- Ketika diaktifkan kembali, jadwal tersimpan dapat digunakan selama masih valid.

---

## Interaction: Navigasi dengan Perubahan Belum Disimpan

Jika owner sudah mengubah intent lain dalam modal lalu memilih **Atur Jam Operasional**, sistem harus mencegah perubahan lokal hilang tanpa disadari.

```text
Ada perubahan yang belum disimpan

Simpan perubahan intent sebelum membuka pengaturan jam operasional?

[Buang Perubahan] [Kembali] [Simpan & Lanjutkan]
```

- **Buang Perubahan**: perubahan lokal dibuang, lanjut ke pengaturan.
- **Kembali**: tetap berada pada modal.
- **Simpan & Lanjutkan**: simpan perubahan intent umum, lalu buka pengaturan.

---

## Interaction: Simpan & Aktifkan dari Pengaturan Jam Operasional

```text
1. Owner berada pada halaman Pengaturan Jam Operasional.
2. Owner melengkapi zona waktu dan jadwal.
3. Owner memilih Simpan & Aktifkan.
4. Sistem memvalidasi seluruh prasyarat.
5. Jika valid:
   - jadwal tersimpan;
   - get_operating_hours menjadi ON;
   - Control Panel menampilkan status Aktif.
6. Jika master WABA OFF:
   - intent tetap tersimpan ON;
   - tampilkan informasi bahwa bot menunggu master switch diaktifkan.
7. Jika tidak valid:
   - intent tetap OFF;
   - tampilkan bagian yang perlu diperbaiki.
```

Pesan ketika master OFF:

```text
Jadwal berhasil disimpan dan Jam Operasional telah diaktifkan.
Bot akan mulai menggunakan capability ini setelah Status Bot WABA diaktifkan.
```

---

## Save Rules

- Tombol **Simpan** pada modal hanya menyimpan perubahan intent yang valid.
- `get_operating_hours = ON` tidak dapat disimpan jika konfigurasi belum siap.
- Jika state Jam Operasional berubah menjadi tidak siap sebelum modal disimpan, sistem menolak aktivasi dan memperbarui status menjadi **Perlu pengaturan** atau **Belum lengkap**.
- Perubahan intent lain tetap dapat disimpan meskipun Jam Operasional belum siap, selama Jam Operasional tidak dipaksa ON.
- `talk_to_agent` tidak pernah menjadi perubahan yang dapat disimpan.

---

## Master Switch UI

- Berada di header Control Panel setelah WABA selector.
- Toggle langsung tanpa modal konfirmasi.
- Saat ON: visual normal.
- Saat OFF: visual warning dan banner:

```text
Bot nonaktif — semua outlet pada WABA ini langsung diarahkan ke agent.
Konfigurasi per-outlet tetap tersimpan.
```

- Menampilkan siapa dan kapan terakhir diperbarui.
- Mengubah master switch tidak mengubah status toggle per-outlet.

---

## Search & Sort

### Search

Owner dapat mencari outlet berdasarkan:

- Nama outlet.
- Kode outlet.
- Kota atau wilayah.

### Sort

- Nama A–Z.
- Nama Z–A.
- Terakhir diubah.
- Kesiapan capability, opsional jika diperlukan pada desain final.

Default: Nama A–Z.

---

## Multi-WABA

- WABA selector berada di bagian paling atas.
- Seluruh state Control Panel mengikuti WABA yang sedang dipilih.
- Master switch independen untuk setiap WABA.
- Daftar outlet hanya menampilkan outlet milik owner, terdaftar Omnichannel, dan terkait WABA terpilih.
- Owner dengan satu WABA tetap melihat selector untuk konsistensi.
- Owner yang belum memiliki WABA tidak melihat menu Control Panel.

---

## Permissions

| Role | Akses |
|---|---|
| Owner | Melihat dan mengubah master switch, intent per outlet, serta membuka pengaturan Jam Operasional untuk outlet miliknya. |
| Outlet manager | Tidak ada akses pada MVP. |
| Customer | Tidak ada akses. |

Seluruh akses harus dibatasi pada WABA dan outlet milik owner yang sedang login.

---

## Edge Cases

| # | Skenario | Perilaku Sistem |
|---|---|---|
| 1 | Outlet baru belum memiliki jadwal. | Jam Operasional OFF dengan status Perlu pengaturan. |
| 2 | Existing outlet belum memiliki jadwal. | Jam Operasional OFF; tidak membuat jadwal otomatis. |
| 3 | Owner menekan toggle Jam Operasional saat belum dikonfigurasi. | Toggle tetap OFF; tampil dialog menuju setup. |
| 4 | Owner menekan toggle saat konfigurasi belum lengkap. | Toggle tetap OFF; tampil CTA Lanjutkan Pengaturan. |
| 5 | Jadwal sudah valid dan owner menekan toggle ON. | Perubahan menjadi state lokal; aktif setelah Simpan. |
| 6 | Owner mematikan Jam Operasional. | Intent OFF; jadwal tidak dihapus. |
| 7 | Owner mengaktifkan kembali dengan jadwal valid. | Tidak perlu setup ulang. |
| 8 | Jadwal dihapus saat intent aktif. | Pengaturan meminta konfirmasi dan intent ikut OFF. |
| 9 | Jadwal menjadi invalid karena perubahan yang belum selesai. | Intent aktif lama tetap menggunakan data tersimpan sampai perubahan valid disimpan. |
| 10 | Data tersimpan menjadi tidak konsisten tetapi intent ON. | Control Panel warning; runtime memperlakukan intent OFF. |
| 11 | Master switch OFF tetapi owner mengatur intent. | Pengaturan dapat disimpan; bot tetap tidak berjalan. |
| 12 | Master OFF lalu ON kembali. | Konfigurasi per-outlet berlaku tanpa setup ulang. |
| 13 | Semua intent configurable OFF atau tidak siap. | Outlet dianggap bot tidak aktif; silent handoff. |
| 14 | Hanya Jam Operasional belum siap, intent lain ON. | Bot tetap aktif dengan capability lain; Jam Operasional tidak tampil di menu. |
| 15 | Owner memiliki perubahan lokal lalu membuka setup Jam Operasional. | Tampilkan pilihan Buang, Kembali, atau Simpan & Lanjutkan. |
| 16 | Owner membuka Control Panel di dua perangkat. | Perubahan terakhir yang berhasil disimpan menjadi state terbaru; UI harus refresh setelah penyimpanan. |
| 17 | Toggle berubah ketika conversation sedang diproses. | Perubahan berlaku pada pesan customer berikutnya, bukan respons yang sedang berjalan. |
| 18 | Owner menekan Hubungi Agen yang locked. | Tampilkan toast informasi; tidak ada perubahan. |
| 19 | Outlet tidak lagi terdaftar Omnichannel. | Tidak muncul pada Control Panel. |
| 20 | Owner mengganti WABA dengan modal terbuka. | Minta menutup atau menyelesaikan perubahan modal terlebih dahulu. |
| 21 | Outlet sedang tutup berdasarkan jadwal khusus satu hari. | Intent tetap ON; modal dapat menampilkan tanggal tutup terdekat/aktif sebagai informasi. |
| 22 | Outlet tutup sementara selama beberapa hari. | Intent tetap ON; status dapat ditampilkan `Tutup sementara sampai [tanggal]`. |
| 23 | Periode penutupan selesai. | Status kembali mengikuti jadwal mingguan tanpa perubahan toggle. |

---

## Acceptance Criteria

### Functional

- [ ] Owner dapat memilih WABA dan melihat state Control Panel yang sesuai.
- [ ] Master switch independen per WABA.
- [ ] Owner dapat melihat outlet miliknya yang terdaftar Omnichannel.
- [ ] Owner dapat mencari dan mengurutkan outlet.
- [ ] Owner dapat membuka modal pengaturan intent dari card outlet.
- [ ] Lima intent configurable selain Jam Operasional mengikuti default existing ON untuk outlet baru.
- [ ] `get_operating_hours` default OFF untuk outlet baru.
- [ ] Existing outlet tanpa konfigurasi memiliki Jam Operasional OFF.
- [ ] `talk_to_agent` selalu ON dan readonly.
- [ ] Perubahan intent umum disimpan melalui tombol Simpan.
- [ ] Batal membuang perubahan lokal.

### Jam Operasional Prerequisite

- [ ] Control Panel menampilkan status Perlu pengaturan, Belum lengkap, Siap diaktifkan, atau Aktif.
- [ ] Toggle Jam Operasional tidak dapat ON ketika konfigurasi belum tersedia.
- [ ] Klik toggle pada status Perlu pengaturan membuka dialog prasyarat.
- [ ] Dialog memiliki CTA menuju Pengaturan Jam Operasional outlet terkait.
- [ ] Klik toggle pada status Belum lengkap membuka CTA Lanjutkan Pengaturan.
- [ ] Toggle dapat ON normal ketika konfigurasi siap.
- [ ] Simpan menolak Jam Operasional ON jika konfigurasi tidak lagi valid.
- [ ] Owner dapat mengaktifkan intent melalui Simpan & Aktifkan pada halaman pengaturan.
- [ ] Mematikan intent tidak menghapus jadwal.
- [ ] Menghapus konfigurasi aktif menonaktifkan intent setelah konfirmasi.
- [ ] Jadwal khusus Tutup satu hari atau beberapa hari tidak menonaktifkan intent.
- [ ] Control Panel dapat menampilkan informasi penutupan sementara yang sedang aktif tanpa mengubah toggle.
- [ ] Setelah periode penutupan selesai, toggle tetap ON dan jadwal reguler kembali berlaku.
- [ ] Existing outlet tidak menerima jadwal default berdasarkan asumsi.

### Master Switch

- [ ] Master switch default ON untuk WABA baru.
- [ ] Master OFF melewati greeting dan classification lalu silent handoff.
- [ ] Master OFF tidak mengubah konfigurasi per-outlet.
- [ ] Master ON kembali menggunakan konfigurasi per-outlet yang tersimpan.
- [ ] Owner tetap dapat mengelola jadwal dan intent saat master OFF.
- [ ] Simpan & Aktifkan saat master OFF menampilkan informasi bahwa bot menunggu master ON.

### Bot Behavior

- [ ] Intent hanya muncul di greeting/menu jika ON dan siap.
- [ ] Jam Operasional belum siap tidak muncul di greeting/menu.
- [ ] Master ON + intent ON dan siap → capability berjalan normal.
- [ ] Master ON + intent OFF → fallback intent tidak tersedia.
- [ ] Master ON + intent tercatat ON tetapi data tidak siap → fallback data tidak dapat dipastikan.
- [ ] Semua intent configurable OFF/tidak siap → silent handoff tanpa greeting.
- [ ] `talk_to_agent` selalu tersedia.
- [ ] Perubahan toggle berlaku pada pesan customer berikutnya.

### UX

- [ ] Card outlet menampilkan ringkasan jumlah capability aktif dan status Jam Operasional.
- [ ] Modal menampilkan CTA Atur/Kelola Jam Operasional sesuai status.
- [ ] Owner mendapat perlindungan terhadap kehilangan perubahan lokal ketika berpindah ke setup.
- [ ] Error penyimpanan mempertahankan modal dan perubahan lokal.
- [ ] Master OFF menampilkan banner warning yang jelas.
- [ ] Tap pada Hubungi Agen menampilkan toast alasan capability terkunci.

### Permissions & Governance

- [ ] Owner hanya dapat melihat dan mengubah WABA/outlet miliknya.
- [ ] Outlet non-Omnichannel tidak muncul.
- [ ] Sistem menampilkan siapa dan kapan setting terakhir diperbarui.

---

## Metrics

| Metrik | Tujuan |
|---|---|
| Jumlah outlet dengan Jam Operasional Perlu pengaturan | Mengukur backlog setup. |
| Klik toggle Jam Operasional saat belum siap | Mengukur discovery melalui Control Panel. |
| Completion rate dari dialog ke konfigurasi valid | Mengukur efektivitas flow setup. |
| Aktivasi melalui Simpan & Aktifkan | Mengukur keberhasilan aktivasi tanpa kembali ke panel. |
| Intent activation failure karena data tidak valid | Mengidentifikasi masalah konfigurasi. |
| Jumlah capability aktif per outlet | Mengukur kesiapan bot per outlet. |
| Frekuensi master switch OFF | Mengukur penggunaan emergency control. |
| Outlet dengan state tidak konsisten | Mengukur kualitas integrasi konfigurasi. |

---

## Out of Scope (Future)

- Bulk setup atau bulk toggle banyak outlet.
- Template jadwal untuk banyak outlet.
- Schedule otomatis master switch.
- Maintenance message per intent.
- Role outlet manager.
- Analytics conversation dan deflection.
- Notification saat owner lain mengubah setting.
- Prerequisite framework generik untuk seluruh intent.
- A/B testing capability.
- Auto-enable intent setelah konfigurasi tanpa tindakan eksplisit owner.

---

## Dependencies

- PRD **Pengaturan Jam Operasional Outlet** sebagai sumber status kesiapan konfigurasi.
- PRD **Intent Jam Operasional (`get_operating_hours`)** sebagai definisi behavior customer-facing.
- Daftar outlet yang telah terdaftar pada Omnichannel.
- Relasi outlet dengan WABA yang dipilih.

---

## Open Questions

1. Apakah card outlet perlu menyediakan filter khusus `Perlu pengaturan` untuk mempercepat setup banyak outlet?
2. Apakah Control Panel cukup menampilkan penutupan khusus yang sedang aktif, atau juga jadwal khusus terdekat yang akan datang?
3. Apakah prerequisite-aware toggle akan diperluas ke intent lain setelah pola Jam Operasional tervalidasi?
