# PRD — Bot Control Panel

## 1. Overview

Bot Control Panel adalah dashboard bagi owner laundry untuk mengontrol Autobot pada level WABA dan per outlet, serta mengatur capability yang dapat digunakan bot pada masing-masing outlet.

Control Panel memiliki tiga lapisan kontrol:

1. **Master Switch WABA** untuk menjalankan atau menghentikan bot pada seluruh outlet dalam satu WABA.
2. **Autobot Outlet** untuk menjalankan atau menghentikan bot hanya pada outlet tertentu.
3. **Capability Toggle** untuk menentukan intent yang dapat digunakan bot pada outlet tersebut.

Intent `get_operating_hours` langsung menggunakan jadwal default **Buka 24 jam setiap hari** ketika fitur dirilis. Owner dapat menyesuaikan jadwal tersebut melalui Pengaturan Jam Operasional tanpa harus melakukan setup awal agar capability dapat digunakan.

Tujuan utama Control Panel adalah memberikan kontrol bot yang granular, mudah dipahami, dan aman tanpa menghapus konfigurasi yang telah dimiliki outlet.

---

## 2. User Stories

- As an owner, saya ingin mengaktifkan atau menonaktifkan Master Switch untuk satu WABA agar dapat menghentikan atau menjalankan bot pada seluruh outlet dengan cepat.
- As an owner, saya ingin mengaktifkan atau menonaktifkan Autobot pada outlet tertentu agar dapat menghentikan bot hanya pada outlet tersebut tanpa memengaruhi outlet lain.
- As an owner, saya ingin mengaktifkan atau menonaktifkan capability tertentu per outlet agar hanya capability yang dibutuhkan yang tersedia bagi customer.
- As an owner, saya ingin konfigurasi capability tetap tersimpan ketika Master Switch WABA atau Autobot Outlet dinonaktifkan agar tidak perlu melakukan setup ulang.
- As an owner, saya ingin melihat apakah Autobot Outlet benar-benar aktif atau masih menunggu Master Switch WABA agar memahami status bot yang berlaku.
- As an owner, saya ingin melihat apakah Jam Operasional masih menggunakan jadwal default 24/7 atau sudah disesuaikan agar dapat memastikan informasi yang diberikan bot sesuai kondisi outlet.
- As an owner, saya ingin mengubah jadwal default 24/7 menjadi jadwal aktual outlet agar informasi jam operasional yang diberikan bot akurat.
- As an owner, saya ingin tetap dapat mengelola capability ketika Autobot Outlet atau Master Switch WABA nonaktif agar konfigurasi dapat dipersiapkan sebelum bot dijalankan kembali.
- As an owner, saya ingin melihat seluruh outlet Omnichannel beserta status Autobot dan capability-nya agar dapat memahami kesiapan bot dengan cepat.

---

## 3. Goals & Non-Goals

### 3.1 Goals

- Menyediakan Master Switch ON/OFF per WABA.
- Menyediakan toggle Autobot ON/OFF per outlet.
- Menyediakan konfigurasi capability per outlet yang terdaftar di Omnichannel.
- Menjaga agar Master Switch WABA OFF tidak mengubah konfigurasi outlet.
- Menjaga agar Autobot Outlet OFF tidak mengubah konfigurasi capability maupun Jam Operasional.
- Menjadikan `talk_to_agent` selalu tersedia dan tidak dapat dimatikan.
- Menyediakan Jam Operasional default 24/7 pada saat fitur dirilis.
- Mengaktifkan `get_operating_hours` secara default.
- Memungkinkan owner mengubah jadwal default 24/7 menjadi jadwal aktual outlet.
- Menampilkan configured state dan effective state Autobot Outlet.
- Menampilkan status Jam Operasional, termasuk default 24/7, jadwal disesuaikan, tutup sementara, nonaktif, dan perlu perbaikan.
- Memungkinkan owner tetap mengubah capability ketika bot sedang nonaktif.
- Membatasi akses Control Panel hanya untuk owner pada MVP.

### 3.2 Non-Goals — MVP

- Bulk action untuk banyak outlet.
- Bulk setup Jam Operasional.
- Schedule otomatis Master Switch WABA.
- Schedule otomatis Autobot Outlet.
- Maintenance mode dengan custom message.
- Custom handoff message per outlet.
- Role outlet manager atau kasir.
- Analytics conversation di dalam Control Panel.
- Notifikasi perubahan setting kepada owner lain.
- Approval sebelum setting berlaku.
- Konfirmasi ketika Autobot Outlet diubah.
- Setup Jam Operasional langsung di dalam modal capability.
- Menjadikan seluruh intent sebagai prerequisite-aware pada MVP.
- Auto-disable Autobot berdasarkan Jam Operasional.

---

## 4. Terminologi

### 4.1 Master Switch WABA

Kontrol utama untuk menjalankan atau menghentikan bot pada seluruh outlet dalam satu WABA.

### 4.2 Autobot Outlet

Kontrol untuk menjalankan atau menghentikan bot hanya pada satu outlet tertentu.

### 4.3 Capability

Kemampuan bot yang direpresentasikan oleh intent, misalnya cek status laundry, cek status tiket, layanan outlet, Jam Operasional, promo aktif, dan info member.

### 4.4 Configured State

Nilai pengaturan yang tersimpan pada toggle, misalnya Autobot Outlet tersimpan ON.

### 4.5 Effective State

Kondisi bot yang benar-benar berlaku setelah memperhitungkan Master Switch WABA, Autobot Outlet, capability, dan kesiapan data.

---

## 5. Control Hierarchy

Urutan prioritas kontrol:

```text
Master Switch WABA OFF
>
Autobot Outlet OFF
>
Capability OFF atau data tidak siap
>
Capability ON dan data siap
```

Autobot Outlet hanya efektif aktif jika:

```text
Master Switch WABA = ON
DAN
Autobot Outlet = ON
```

| Master WABA | Autobot Outlet | Effective State |
|---|---:|---|
| ON | ON | Autobot aktif |
| ON | OFF | Autobot outlet nonaktif |
| OFF | ON | Menunggu Master WABA aktif |
| OFF | OFF | Autobot outlet nonaktif |

---

## 6. Affected Intents

| Intent | Dapat Diubah | Default Outlet Baru | Prasyarat |
|---|---|---|---|
| `check_laundry_status` | Ya | ON | Mengikuti kesiapan fitur existing |
| `check_ticket_status` | Ya | ON | Mengikuti kesiapan fitur existing |
| `get_outlet_services` | Ya | ON | Mengikuti kesiapan fitur existing |
| `get_operating_hours` | Ya, dengan validasi | ON | Jadwal default 24/7 otomatis tersedia; jadwal hasil perubahan owner harus valid |
| `get_active_promos` | Ya | ON | Mengikuti kesiapan fitur existing |
| `get_member_info` | Ya | ON | Mengikuti kesiapan fitur existing |
| `talk_to_agent` | Tidak | Selalu ON | Tidak ada; wajib tersedia |

`talk_to_agent` selalu ON agar customer selalu memiliki jalur eskalasi ke manusia.

---

## 7. Default State & Rollout

### 7.1 WABA Baru

- Master Switch WABA default ON.

### 7.2 Outlet Baru yang Terdaftar Omnichannel

- Autobot Outlet default ON.
- Seluruh capability configurable default ON.
- `get_operating_hours` default ON.
- Sistem menyediakan jadwal awal **Buka 24 jam setiap hari**.
- Status Jam Operasional: **Aktif · Menggunakan jadwal default 24/7**.
- Effective state langsung aktif selama Master Switch WABA ON.

### 7.3 Existing Outlet Saat Fitur Dirilis

- Jika outlet sudah memiliki jadwal operasional valid, sistem mempertahankan jadwal tersebut.
- Jika outlet belum memiliki jadwal operasional, sistem menyediakan jadwal default 24/7.
- Jadwal existing yang valid tidak boleh ditimpa oleh jadwal default.
- `get_operating_hours` default ON.
- Outlet yang sebelumnya telah menggunakan bot dimigrasikan sebagai Autobot Outlet ON.
- Jika sebelumnya tidak ada status bot per outlet, Autobot Outlet default ON agar behavior existing tidak terputus.
- Konfigurasi capability existing tetap dipertahankan jika tersedia.

---

## 8. Status Jam Operasional di Card Outlet

Card outlet hanya menampilkan status jam operasional jika outlet menggunakan skema default 24 jam. Jika outlet memiliki jadwal kustom (zona waktu, jadwal per hari, sesi, jadwal khusus), card tidak menampilkan info jam operasional — detailnya hanya tampil di modal capability.

| Status | Kondisi |
|---|---|
| 24 Jam | Outlet menggunakan skema default 24 jam. |
| (tidak tampil) | Outlet menggunakan jadwal kustom. Detail di modal capability. |

---

## 9. Master Switch WABA Behavior

### 9.1 Saat ON

- Sistem melanjutkan pemeriksaan Autobot Outlet.
- Outlet dengan Autobot ON dapat menjalankan capability sesuai konfigurasinya.
- Outlet dengan Autobot OFF melakukan silent handoff.

### 9.2 Saat OFF

- Bot tidak mengirim greeting.
- Bot tidak melakukan intent classification.
- Conversation langsung diarahkan ke agent melalui silent handoff.
- Seluruh configured state Autobot Outlet tetap tersimpan.
- Seluruh konfigurasi capability tetap tersimpan.
- Owner tetap dapat membuka dan mengubah konfigurasi outlet.
- Owner tetap dapat mengelola Jam Operasional.
- Bot baru berjalan ketika Master Switch WABA kembali ON.

### 9.3 Interaction

- Toggle Master Switch WABA menampilkan modal konfirmasi sebelum perubahan disimpan.
- Modal konfirmasi menjelaskan bahwa mematikan bot akan menghentikan percakapan bot yang sedang berlangsung dan mengalihkan customer ke agent.
- Owner memilih **Konfirmasi** untuk menyimpan perubahan, atau **Batal** untuk membatalkan tanpa mengubah state.
- Jika penyimpanan gagal, toggle kembali ke state sebelumnya dan sistem menampilkan error.
- Saat Master Switch WABA diubah menjadi OFF, percakapan bot yang sedang berlangsung langsung dihentikan dan customer diarahkan ke agent melalui silent handoff pada respons berikutnya.
- Aturan penghentian tidak berlaku jika customer sedang menchat dengan agent, karena tidak ada sesi bot yang aktif.
- Saat Master Switch WABA diubah menjadi ON, bot kembali berjalan pada pesan customer berikutnya menggunakan konfigurasi tersimpan.

---

## 10. Autobot Outlet Behavior

### 10.1 Saat ON

Jika Master Switch WABA ON:

- Bot dapat mengirim greeting.
- Bot dapat melakukan intent classification.
- Capability ON dan siap dapat digunakan.
- Capability OFF atau tidak siap tidak digunakan.
- `talk_to_agent` tetap tersedia.

Jika Master Switch WABA OFF:

- Toggle Autobot Outlet tetap tersimpan ON.
- Bot belum berjalan.
- Status outlet ditampilkan sebagai **Menunggu Status Bot WABA diaktifkan**.

### 10.2 Saat OFF

- Bot tidak mengirim greeting.
- Bot tidak melakukan intent classification.
- Pesan customer langsung diarahkan ke agent melalui silent handoff.
- Chat bot yang sedang berlangsung di outlet ini langsung dihentikan dan customer dialihkan ke agent pada respons berikutnya.
- Aturan penghentian tidak berlaku jika customer sudah berada dalam sesi chat dengan agent, karena tidak ada sesi bot yang aktif untuk dihentikan.
- Konfigurasi capability tetap tersimpan.
- Konfigurasi Jam Operasional tetap tersimpan.
- Owner tetap dapat membuka dan mengubah capability.
- Outlet lain dalam WABA yang sama tidak terpengaruh.

### 10.3 Saat Diaktifkan Kembali

- Bot menggunakan konfigurasi capability terakhir yang tersimpan.
- Capability yang OFF tetap OFF.
- Capability yang ON dan siap kembali digunakan.
- Jadwal Jam Operasional terakhir kembali digunakan.
- Owner tidak perlu melakukan setup ulang.

### 10.4 Interaction

- Toggle Autobot Outlet tersedia pada card outlet.
- Perubahan berlaku langsung tanpa tombol Simpan modal.
- Saat proses penyimpanan, toggle menampilkan loading dan tidak dapat ditekan ulang.
- Jika gagal, toggle kembali ke state sebelumnya.
- Menekan toggle tidak membuka modal capability.

---

## 11. Capability Behavior

### 11.1 Saat Capability ON

- Capability dapat digunakan jika data pendukung siap.
- Capability muncul pada greeting atau menu jika memenuhi kondisi runtime.

### 11.2 Saat Capability OFF

- Capability tidak muncul pada greeting atau menu.
- Jika customer tetap meminta capability tersebut, bot menggunakan fallback intent tidak tersedia.

### 11.3 Saat Data Tidak Siap

- Capability diperlakukan OFF pada runtime.
- Control Panel menampilkan warning.
- Bot menggunakan fallback data tidak dapat dipastikan.

### 11.4 `talk_to_agent`

- Selalu ON.
- Tidak dapat dimatikan.
- Tampil read-only dengan icon lock.
- Ketika ditekan, tampil toast informasi bahwa capability wajib tersedia.

---

## 12. Bot Flow Integration

1. Customer mengirim pesan ke outlet X dalam WABA W.
2. Sistem membaca Master Switch WABA W.
3. Jika Master Switch WABA OFF:
   - skip greeting;
   - skip intent classification;
   - silent handoff ke agent.
4. Jika Master Switch WABA ON, sistem membaca Autobot Outlet X.
5. Jika Autobot Outlet OFF:
   - skip greeting;
   - skip intent classification;
   - silent handoff ke agent.
6. Jika Autobot Outlet ON, sistem membaca capability outlet:
   - capability ON dan data siap → capability berjalan normal;
   - capability OFF → fallback intent tidak tersedia;
   - capability ON tetapi data tidak siap → diperlakukan OFF dan menggunakan fallback data;
   - semua configurable capability OFF atau tidak siap → silent handoff tanpa greeting atau menu.
7. `talk_to_agent` selalu tersedia.
8. Jika Master Switch WABA diubah menjadi OFF saat customer sedang menchat dengan bot, sistem menghentikan sesi bot tersebut dan mengalihkan customer ke agent pada respons berikutnya.
9. Aturan penghentian pada poin 8 tidak berlaku jika customer sudah berada dalam sesi chat dengan agent, karena tidak ada sesi bot yang sedang aktif untuk dihentikan.
10. Jika Autobot Outlet diubah menjadi OFF saat customer sedang menchat dengan bot pada outlet tersebut, sistem menghentikan sesi bot outlet tersebut dan mengalihkan customer ke agent pada respons berikutnya.
11. Aturan penghentian pada poin 10 tidak berlaku jika customer sudah berada dalam sesi chat dengan agent, karena tidak ada sesi bot yang aktif untuk dihentikan.

---

## 13. Greeting & Menu

Greeting hanya menampilkan capability yang memenuhi dua kondisi:

```text
Capability = ON
DAN
Data pendukung = siap
```

Jam Operasional tidak ditampilkan jika:

- intent OFF;
- data jadwal hilang;
- data jadwal rusak;
- data jadwal tidak valid.

Jadwal default 24/7 dianggap valid dan boleh ditampilkan.

---

## 14. Fallback Message

### 14.1 Capability OFF

```text
Maaf Kak, fitur ini sedang tidak tersedia di outlet ini.
Saya bisa hubungkan Kakak ke agent untuk dibantu lebih lanjut.
```

### 14.2 Data Tidak Siap

```text
Maaf Kak, informasi jam operasional outlet belum dapat dipastikan saat ini.
Saya bisa hubungkan Kakak ke agent outlet untuk konfirmasi.
```

---

## 15. UI / UX

### 15.1 Owner Dashboard Layout

```text
┌──────────────────────────────────────────────────────────────┐
│ Bot Control Panel                                            │
├──────────────────────────────────────────────────────────────┤
│ WABA: [ WABA-1 (Utama)                                  ▾ ] │
├──────────────────────────────────────────────────────────────┤
│ Status Bot WABA: [ ●──── ON ]                               │
│ Seluruh outlet pada WABA ini dapat menjalankan Autobot.      │
│ Diperbarui 2 jam lalu oleh Agestya                           │
├──────────────────────────────────────────────────────────────┤
│ Search: [________________] Sort: [Nama A–Z              ▾ ] │
├──────────────────────────────────────────────────────────────┤
│ Outlet A — Jakarta Pusat                  Autobot [ ●── ON ] │
│ Autobot aktif                                                │
│ 6 capability aktif · Jam Operasional 24 Jam                › │
│ Diperbarui 2 jam lalu                                        │
├──────────────────────────────────────────────────────────────┤
│ Outlet B — Bandung                       Autobot [ ○── OFF ] │
│ Autobot outlet nonaktif                                      │
│ 6 capability tersimpan                                     › │
│ Diperbarui kemarin                                           │
├──────────────────────────────────────────────────────────────┤
│ Outlet C — Surabaya                      Autobot [ ●── ON ] │
│ Menunggu Status Bot WABA diaktifkan                           │
│ 6 capability aktif · Jam Operasional 24 Jam                › │
└──────────────────────────────────────────────────────────────┘
```

### 15.2 Master Switch UI

- Berada setelah WABA selector.
- Menampilkan configured state WABA.
- Menampilkan siapa dan kapan terakhir diperbarui.
- Saat ON, visual normal.
- Saat OFF, tampil visual warning dan banner.
- Menekan toggle memunculkan modal konfirmasi sebelum state berubah.
- Modal konfirmasi berisi penjelasan dampak mematikan bot, termasuk penghentian chat bot yang sedang berjalan dan pengalihan ke agent.

```text
Bot nonaktif — semua outlet pada WABA ini langsung diarahkan ke agent.
Konfigurasi per-outlet tetap tersimpan.
```

### 15.3 Outlet Card

Card outlet bersifat **partially interactive**.

Elemen pada card:

- Nama outlet.
- Kota atau wilayah.
- Toggle Autobot Outlet.
- Effective status Autobot.
- Jumlah capability ON dan siap.
- Status Jam Operasional: **24 Jam** (hanya untuk skema default).
- Terakhir diperbarui.
- Indikator untuk membuka detail capability.

Area interaksi:

- Menekan toggle hanya mengubah Autobot Outlet.
- Menekan bagian card selain toggle membuka modal capability.
- Menekan toggle tidak boleh ikut membuka modal.

### 15.4 Status Outlet Card

#### 24 Jam

```text
Outlet A — Jakarta Pusat                  Autobot [ ●── ON ]
Autobot aktif
6 capability aktif · Jam Operasional 24 Jam
Diperbarui 2 jam lalu
```

#### Tidak tampil (jadwal kustom)

```text
Outlet A — Jakarta Pusat                  Autobot [ ○── OFF ]
Autobot outlet nonaktif
6 capability tersimpan
Diperbarui 2 jam lalu
```

#### Menunggu Master WABA

```text
Outlet A — Jakarta Pusat                  Autobot [ ●── ON ]
Menunggu Status Bot WABA diaktifkan
6 capability aktif · Jam Operasional 24/7
```

### 15.5 Modal Pengaturan Capability — Autobot Aktif

```text
┌────────────────────────────────────────────────────┐
│ Outlet A — Jakarta Pusat                       ✕  │
├────────────────────────────────────────────────────┤
│ Autobot Outlet aktif                               │
│ Capability berikut dapat digunakan oleh bot.       │
├────────────────────────────────────────────────────┤
│ Cek status laundry                 [ ●──── ON ]    │
│ Cek status tiket                   [ ●──── ON ]    │
│ Layanan outlet                     [ ●──── ON ]    │
│                                                    │
│ Jam operasional                                    │
│ Aktif · Buka 24 jam setiap hari                    │
│ Menggunakan jadwal default                         │
│ [Kelola Jam Operasional]           [ ●──── ON ]    │
│                                                    │
│ Promo aktif                        [ ●──── ON ]    │
│ Info member                        [ ●──── ON ]    │
│ Hubungi agen · Selalu aktif        [ ●──── ON ] 🔒│
├────────────────────────────────────────────────────┤
│                    [Batal] [Simpan]                │
└────────────────────────────────────────────────────┘
```

### 15.6 Modal Pengaturan Capability — Autobot Nonaktif

```text
┌────────────────────────────────────────────────────┐
│ Outlet A — Jakarta Pusat                       ✕  │
├────────────────────────────────────────────────────┤
│ Autobot Outlet nonaktif                            │
│ Semua chat saat ini langsung diarahkan ke agent.   │
│ Pengaturan capability tetap dapat diubah dan akan  │
│ berlaku setelah Autobot Outlet diaktifkan kembali. │
├────────────────────────────────────────────────────┤
│ Cek status laundry                 [ ●──── ON ]    │
│ Cek status tiket                   [ ●──── ON ]    │
│ Layanan outlet                     [ ●──── ON ]    │
│                                                    │
│ Jam operasional                                    │
│ Aktif · Buka 24 jam setiap hari                    │
│ Menggunakan jadwal default                         │
│ [Kelola Jam Operasional]           [ ●──── ON ]    │
│                                                    │
│ Promo aktif                        [ ●──── ON ]    │
│ Info member                        [ ●──── ON ]    │
│ Hubungi agen · Selalu aktif        [ ●──── ON ] 🔒│
├────────────────────────────────────────────────────┤
│                    [Batal] [Simpan]                │
└────────────────────────────────────────────────────┘
```

Capability tetap editable saat Autobot Outlet OFF.

### 15.7 Tampilan Jam Operasional

#### Default 24/7

```text
Jam operasional
Aktif · Buka 24 jam setiap hari
Menggunakan jadwal default

[Kelola Jam Operasional]              [ ●──── ON ]
```

#### Jadwal Disesuaikan

```text
Jam operasional
Aktif · Menggunakan jadwal outlet
Terakhir diperbarui 13 Juli 2026

[Kelola Jam Operasional]              [ ●──── ON ]
```

#### Intent Nonaktif

```text
Jam operasional
Nonaktif · Jadwal tetap tersimpan

[Kelola Jam Operasional]              [ ○──── OFF ]
```

#### Tutup Sementara

```text
Jam operasional
Aktif · Tutup sementara 20–26 Juli 2026

[Kelola Jam Operasional]              [ ●──── ON ]
```

Toggle tetap ON ketika outlet tutup sementara karena capability masih diperlukan untuk menjelaskan status penutupan dan tanggal outlet kembali buka.

#### Perlu Perbaikan

```text
Jam operasional
Perlu perbaikan · Jadwal tidak valid

[Perbaiki Jam Operasional]            [ ○──── OFF ]
```

---

## 16. Interaction: Toggle Capability Umum

1. Owner membuka modal outlet.
2. Owner mengubah satu atau lebih toggle capability.
3. Perubahan berada pada state lokal modal.
4. Owner memilih **Simpan**.
5. Jika berhasil, modal ditutup dan list diperbarui.
6. Jika gagal, modal tetap terbuka dan perubahan lokal dipertahankan.
7. Owner memilih **Batal**, maka perubahan lokal dibuang.

---

## 17. Interaction: Toggle Jam Operasional

### 17.1 Default 24/7 atau Jadwal Valid dan Intent Aktif

- Owner dapat mengubah toggle dari ON menjadi OFF.
- Perubahan menjadi state lokal modal.
- Perubahan berlaku setelah owner menekan Simpan.
- Menonaktifkan intent tidak menghapus jadwal.

### 17.2 Intent Nonaktif dengan Jadwal Valid

- Owner dapat mengubah toggle dari OFF menjadi ON.
- Jadwal tersimpan digunakan kembali.
- Owner tidak perlu melakukan setup ulang.
- Perubahan berlaku setelah owner menekan Simpan.

### 17.3 Data Jadwal Hilang atau Tidak Valid

Ketika owner menekan toggle OFF menjadi ON:

- Toggle tidak berubah menjadi ON.
- Perubahan tidak masuk ke daftar perubahan lokal modal.
- Sistem menampilkan dialog:

```text
Perbaiki pengaturan jam operasional

Data jam operasional outlet tidak tersedia atau tidak valid.
Periksa dan simpan kembali jadwal sebelum capability dapat diaktifkan.

[Batal] [Perbaiki Pengaturan]
```

### 17.4 Owner Sedang Mengubah Jadwal

- Perubahan yang belum valid tidak menggantikan jadwal aktif.
- Jadwal valid terakhir tetap digunakan sampai perubahan baru berhasil disimpan.
- Jika owner membatalkan perubahan, jadwal sebelumnya tetap berlaku.

---

## 18. Interaction: Navigasi dengan Perubahan Belum Disimpan

Jika owner sudah mengubah capability dalam modal lalu memilih **Kelola Jam Operasional**, sistem harus melindungi perubahan lokal.

```text
Ada perubahan yang belum disimpan

Simpan perubahan capability sebelum membuka pengaturan jam operasional?

[Buang Perubahan] [Kembali] [Simpan & Lanjutkan]
```

- **Buang Perubahan**: perubahan lokal dibuang, lalu membuka pengaturan.
- **Kembali**: tetap berada pada modal.
- **Simpan & Lanjutkan**: menyimpan perubahan capability, lalu membuka Pengaturan Jam Operasional.

---

## 19. Interaction: Simpan Jam Operasional

1. Owner berada pada halaman Pengaturan Jam Operasional.
2. Owner mengubah zona waktu atau jadwal.
3. Owner memilih Simpan.
4. Sistem memvalidasi seluruh data.
5. Jika valid:
   - jadwal tersimpan;
   - status berubah menjadi jadwal disesuaikan;
   - `get_operating_hours` tetap mengikuti state toggle yang tersimpan.
6. Jika tidak valid:
   - jadwal aktif sebelumnya tetap digunakan;
   - perubahan baru tidak menggantikan jadwal aktif;
   - sistem menunjukkan bagian yang harus diperbaiki.
7. Jika Master WABA OFF atau Autobot Outlet OFF:
   - jadwal tetap dapat disimpan;
   - tampil informasi bahwa bot belum efektif berjalan.

Pesan ketika Master WABA OFF:

```text
Jadwal berhasil disimpan.
Bot akan mulai menggunakan jadwal ini setelah Status Bot WABA diaktifkan.
```

Pesan ketika Autobot Outlet OFF:

```text
Jadwal berhasil disimpan.
Bot akan mulai menggunakan jadwal ini setelah Autobot Outlet diaktifkan.
```

---

## 20. Save Rules

- Perubahan Master Switch WABA disimpan langsung.
- Perubahan Autobot Outlet disimpan langsung dari card.
- Perubahan capability disimpan melalui tombol Simpan pada modal.
- Master WABA OFF tidak mengubah state Autobot Outlet.
- Autobot Outlet OFF tidak mengubah state capability.
- Autobot Outlet OFF tidak menghapus Jam Operasional.
- Jadwal default 24/7 dianggap valid.
- `get_operating_hours` hanya dapat ON jika terdapat jadwal valid.
- Menonaktifkan `get_operating_hours` tidak menghapus jadwal.
- Jika jadwal hilang atau tidak valid, aktivasi intent ditolak.
- Perubahan capability lain tetap dapat disimpan meskipun Jam Operasional perlu perbaikan.
- `talk_to_agent` tidak pernah menjadi perubahan yang dapat disimpan.
- Perubahan yang gagal harus mengembalikan toggle direct-save ke state sebelumnya.
- Error pada penyimpanan modal tidak boleh menghapus perubahan lokal.

---

## 21. Search & Sort

### 21.1 Search

Owner dapat mencari outlet berdasarkan:

- Nama outlet.
- Kode outlet.
- Kota atau wilayah.

### 21.2 Sort

- Nama A–Z.
- Nama Z–A.
- Terakhir diubah.
- Autobot aktif lebih dahulu.
- Autobot nonaktif lebih dahulu.
- Kesiapan capability, opsional.

Default: Nama A–Z.

---

## 22. Multi-WABA

- WABA selector berada di bagian paling atas.
- Seluruh state Control Panel mengikuti WABA yang dipilih.
- Master Switch independen untuk setiap WABA.
- Autobot Outlet berada dalam konteks WABA terpilih.
- Daftar outlet hanya menampilkan outlet milik owner, terdaftar Omnichannel, dan terkait WABA terpilih.
- Owner dengan satu WABA tetap melihat selector untuk konsistensi.
- Owner yang belum memiliki WABA tidak melihat menu Control Panel.
- Jika owner mengganti WABA saat modal memiliki perubahan lokal, sistem meminta owner menyelesaikan atau membuang perubahan terlebih dahulu.

---

## 23. Permissions

| Role | Akses |
|---|---|
| Owner | Melihat dan mengubah Master Switch WABA, Autobot Outlet, capability per outlet, serta membuka Pengaturan Jam Operasional untuk outlet miliknya |
| Outlet manager | Tidak ada akses pada MVP |
| Customer | Tidak ada akses |

Seluruh akses dibatasi pada WABA dan outlet milik owner yang sedang login.

---

## 24. Edge Cases

| # | Skenario | Perilaku Sistem |
|---|---|---|
| 1 | Outlet baru terdaftar Omnichannel | Autobot ON, capability default ON, dan Jam Operasional default 24/7 |
| 2 | Existing outlet belum memiliki jadwal saat rollout | Sistem membuat jadwal default 24/7 |
| 3 | Existing outlet sudah memiliki jadwal valid | Sistem mempertahankan jadwal existing |
| 4 | Owner mematikan Master WABA | Semua outlet silent handoff; konfigurasi tetap tersimpan; chat bot yang sedang berlangsung dihentikan dan customer dialihkan ke agent |
| 4a | Owner mematikan Master WABA saat customer sedang chat dengan agent | Tidak ada sesi bot aktif; pengalihan tidak berlaku karena customer sudah di agent |
| 5 | Master WABA OFF tetapi Autobot Outlet ON | Toggle outlet tetap ON; status menunggu Master WABA |
| 6 | Master WABA kembali ON | Outlet dengan Autobot ON langsung aktif kembali |
| 7 | Owner mematikan Autobot Outlet | Hanya outlet tersebut yang silent handoff; chat bot yang sedang berlangsung di outlet tersebut dihentikan dan customer dialihkan ke agent |
| 7a | Owner mematikan Autobot Outlet saat customer sedang chat dengan agent | Tidak ada sesi bot aktif; pengalihan tidak berlaku karena customer sudah di agent |
| 8 | Outlet lain dalam WABA yang sama aktif | Outlet lain tetap berjalan |
| 9 | Autobot OFF tetapi capability masih ON | Capability tetap tersimpan tetapi tidak digunakan |
| 10 | Owner mengubah capability saat Autobot OFF | Perubahan dapat disimpan |
| 11 | Owner mengaktifkan kembali Autobot | Konfigurasi capability terakhir digunakan kembali |
| 12 | Semua configurable capability OFF | Outlet melakukan silent handoff tanpa greeting |
| 13 | Hanya Jam Operasional OFF | Bot tetap berjalan dengan capability lain |
| 14 | Owner mematikan Jam Operasional | Intent OFF; jadwal tidak dihapus |
| 15 | Owner mengaktifkan kembali Jam Operasional | Jadwal tersimpan langsung digunakan |
| 16 | Owner mengubah jadwal tetapi perubahan belum valid | Jadwal valid sebelumnya tetap digunakan |
| 17 | Data jadwal hilang atau rusak | Intent diperlakukan OFF dan Control Panel menampilkan warning |
| 18 | Outlet tutup satu hari | Intent tetap ON dan bot menjelaskan outlet sedang tutup |
| 19 | Outlet tutup dalam rentang tanggal | Intent tetap ON dan bot menjelaskan tanggal buka kembali |
| 20 | Periode tutup selesai | Sistem kembali menggunakan jadwal mingguan |
| 21 | Owner memiliki perubahan lokal lalu membuka pengaturan Jam Operasional | Tampil Buang, Kembali, atau Simpan & Lanjutkan |
| 22 | Owner membuka Control Panel di dua perangkat | Perubahan terakhir yang berhasil disimpan menjadi state terbaru; UI refresh setelah penyimpanan |
| 23 | Toggle berubah saat conversation sedang diproses | Perubahan berlaku pada pesan customer berikutnya |
| 24 | Owner menekan Hubungi Agen yang locked | Tampil toast; tidak ada perubahan |
| 25 | Outlet tidak lagi terdaftar Omnichannel | Outlet tidak muncul pada Control Panel |
| 26 | Owner mengganti WABA dengan modal terbuka | Owner diminta menyelesaikan atau membuang perubahan |
| 27 | Toggle direct-save gagal | Toggle kembali ke state sebelumnya dan tampil error |
| 28 | Owner menekan toggle berkali-kali saat loading | Input berikutnya diabaikan sampai proses selesai |
| 29 | Owner tidak memiliki akses outlet | Perubahan ditolak dan state tidak berubah |
| 30 | Autobot OFF saat outlet sedang tutup sementara | Bot silent handoff; jadwal tutup tetap tersimpan |

---

## 25. Acceptance Criteria

### 25.1 Master Switch WABA

- [ ] Owner dapat memilih WABA dan melihat state Control Panel yang sesuai.
- [ ] Master Switch independen per WABA.
- [ ] WABA baru memiliki Master Switch default ON.
- [ ] Master OFF melewati greeting dan classification lalu silent handoff.
- [ ] Master OFF tidak mengubah Autobot Outlet.
- [ ] Master OFF tidak mengubah capability per outlet.
- [ ] Master ON kembali menggunakan konfigurasi tersimpan.
- [ ] Owner tetap dapat mengelola outlet saat Master OFF.
- [ ] Master OFF menampilkan banner warning.
- [ ] Sistem menampilkan siapa dan kapan Master Switch terakhir diperbarui.
- [ ] Mengubah Master Switch menampilkan modal konfirmasi sebelum state tersimpan.
- [ ] Mematikan Master Switch menghentikan chat bot yang sedang berlangsung dan mengalihkan customer ke agent.
- [ ] Aturan penghentian chat tidak berlaku saat customer sudah menchat dengan agent.

### 25.2 Autobot Outlet

- [ ] Setiap outlet Omnichannel memiliki toggle Autobot Outlet.
- [ ] Outlet baru memiliki Autobot Outlet default ON.
- [ ] Owner dapat mengaktifkan atau menonaktifkan Autobot per outlet.
- [ ] Perubahan Autobot tidak memengaruhi outlet lain.
- [ ] Perubahan Autobot disimpan langsung tanpa tombol Simpan modal.
- [ ] Autobot OFF tidak menghapus capability.
- [ ] Autobot OFF tidak menghapus Jam Operasional.
- [ ] Owner tetap dapat mengubah capability saat Autobot OFF.
- [ ] Mengaktifkan kembali Autobot menggunakan konfigurasi terakhir.
- [ ] Master OFF dan Autobot ON menampilkan status menunggu Master WABA.
- [ ] Autobot OFF melewati greeting dan intent classification.
- [ ] Mematikan Autobot Outlet menghentikan chat bot yang sedang berlangsung dan mengalihkan customer ke agent.
- [ ] Aturan penghentian tidak berlaku saat customer sudah menchat dengan agent.
- [ ] Toggle menampilkan loading saat penyimpanan.
- [ ] Jika penyimpanan gagal, toggle kembali ke state sebelumnya.

### 25.3 Capability

- [ ] Owner dapat membuka modal capability dari card outlet.
- [ ] Capability configurable default ON untuk outlet baru.
- [ ] `talk_to_agent` selalu ON dan read-only.
- [ ] Perubahan capability disimpan melalui tombol Simpan.
- [ ] Batal membuang perubahan lokal.
- [ ] Error penyimpanan mempertahankan modal dan perubahan lokal.
- [ ] Capability hanya muncul pada greeting jika ON dan siap.
- [ ] Semua configurable capability OFF atau tidak siap menghasilkan silent handoff.

### 25.4 Jam Operasional — Default dan Rollout

- [ ] Outlet baru otomatis memiliki jadwal Buka 24 jam setiap hari.
- [ ] `get_operating_hours` default ON untuk outlet baru.
- [ ] Existing outlet tanpa jadwal memperoleh jadwal default 24/7 saat rollout.
- [ ] Existing outlet dengan jadwal valid tetap menggunakan jadwal existing.
- [ ] Jadwal existing valid tidak ditimpa jadwal default.
- [ ] Control Panel dapat membedakan jadwal default dan jadwal disesuaikan.

### 25.5 Jam Operasional — Control Panel

- [ ] Control Panel menampilkan status Aktif · Default 24/7.
- [ ] Control Panel menampilkan status jadwal disesuaikan setelah owner menyimpan perubahan.
- [ ] Owner dapat membuka Pengaturan Jam Operasional melalui CTA Kelola Jam Operasional.
- [ ] Owner dapat menonaktifkan intent tanpa menghapus jadwal.
- [ ] Owner dapat mengaktifkan kembali intent tanpa setup ulang selama jadwal valid.
- [ ] Jadwal default 24/7 dianggap valid.
- [ ] Jadwal khusus tutup satu hari atau beberapa hari tidak menonaktifkan intent.
- [ ] Setelah periode penutupan selesai, jadwal reguler kembali berlaku.
- [ ] Data jadwal tidak valid ditampilkan sebagai warning, bukan state awal outlet.
- [ ] Perubahan jadwal belum valid tidak menggantikan jadwal aktif sebelumnya.

### 25.6 UX

- [ ] Card outlet menampilkan nama outlet, wilayah, Autobot toggle, effective status, capability summary, Jam Operasional, dan last updated.
- [ ] Card bersifat partially interactive.
- [ ] Menekan toggle Autobot tidak membuka modal.
- [ ] Menekan body card membuka modal capability.
- [ ] Modal menampilkan banner ketika Autobot OFF.
- [ ] Capability tetap editable ketika Autobot OFF.
- [ ] Owner terlindungi dari kehilangan perubahan lokal ketika berpindah ke Pengaturan Jam Operasional.
- [ ] Hubungi Agen menampilkan toast alasan capability terkunci.

### 25.7 Permissions & Governance

- [ ] Hanya owner yang dapat mengakses Control Panel.
- [ ] Owner hanya dapat melihat dan mengubah WABA serta outlet miliknya.
- [ ] Outlet non-Omnichannel tidak muncul.
- [ ] Sistem menampilkan siapa dan kapan setting terakhir diperbarui.

---

## 26. Metrics

| Metrik | Tujuan |
|---|---|
| Jumlah outlet dengan Autobot OFF | Mengukur outlet yang dihentikan secara individual |
| Frekuensi perubahan Autobot Outlet | Mengukur penggunaan operational control |
| Durasi outlet berada dalam kondisi Autobot OFF | Mengidentifikasi outlet yang lama tidak menggunakan bot |
| Jumlah Autobot ON yang menunggu Master WABA | Mengukur dampak Master Switch terhadap outlet |
| Failure rate perubahan toggle direct-save | Mengidentifikasi masalah pada proses pengaturan |
| Jumlah outlet yang masih menggunakan jadwal default 24/7 | Mengukur backlog owner yang belum menyesuaikan jadwal |
| Conversion rate dari default 24/7 ke jadwal disesuaikan | Mengukur adopsi Pengaturan Jam Operasional |
| Jumlah outlet dengan data jadwal tidak valid | Mengukur kualitas dan konsistensi konfigurasi |
| Jumlah owner yang membuka Kelola Jam Operasional dari Control Panel | Mengukur discovery flow |
| Jumlah intent Jam Operasional yang dinonaktifkan | Mengukur kebutuhan owner menonaktifkan capability |
| Jumlah outlet dengan penutupan sementara aktif | Mengukur penggunaan jadwal khusus |
| Jumlah capability aktif per outlet | Mengukur kesiapan bot per outlet |
| Frekuensi Master Switch WABA OFF | Mengukur penggunaan emergency control |

---

## 27. Out of Scope — Future

- Bulk activation atau deactivation Autobot untuk banyak outlet.
- Bulk toggle capability.
- Template jadwal untuk banyak outlet.
- Schedule otomatis Master Switch WABA.
- Schedule otomatis Autobot Outlet.
- Maintenance message per intent.
- Custom handoff message per outlet.
- Role outlet manager.
- Analytics conversation dan deflection.
- Notification saat owner lain mengubah setting.
- Prerequisite framework generik untuk seluruh intent.
- A/B testing capability.
- Auto-enable capability setelah konfigurasi tanpa tindakan eksplisit owner.
- Auto-disable Autobot berdasarkan Jam Operasional.

---

## 28. Dependencies

- PRD Pengaturan Jam Operasional Outlet sebagai sumber behavior jadwal reguler dan jadwal khusus.
- PRD Intent Jam Operasional `get_operating_hours` sebagai definisi behavior customer-facing.
- Daftar outlet yang telah terdaftar pada Omnichannel.
- Relasi outlet dengan WABA yang dipilih.
- Status ownership WABA dan outlet.

---

## 29. Open Questions

1. Apakah dashboard perlu menyediakan filter khusus Autobot aktif, Autobot nonaktif, dan Menunggu Master WABA?
2. Apakah card outlet cukup menampilkan penutupan khusus yang sedang aktif, atau juga jadwal khusus terdekat yang akan datang?
3. Apakah owner perlu melihat label khusus bahwa outlet masih menggunakan jadwal default 24/7 agar terdorong melakukan penyesuaian?
4. Apakah capability prerequisite-aware akan diperluas ke intent lain setelah pola Jam Operasional tervalidasi?
5. Apakah status Autobot Outlet perlu ditampilkan pada halaman lain di luar Control Panel?
