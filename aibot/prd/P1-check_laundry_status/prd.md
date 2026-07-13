# Cek Status Laundry (`check_laundry_status`) — PRD

## Overview

Fitur cek status laundry memungkinkan customer mengetahui status transaksi laundry berdasarkan **ID transaksi**. Customer mengetik permintaan dalam bahasa natural; bot memahami ID, lalu menampilkan status dengan label customer-friendly. Jika butuh konteks tambahan, customer bisa request "detail". Pada kondisi tertentu (refund, transaksi rusak, dsb) bot auto-eskalasi ke agen manusia.

Tujuan: menjawab pertanyaan repetitif tertinggi CS dengan self-service yang aman, singkat, dan konsisten di semua outlet.

---

## User Stories

- As a **customer**, saya ingin **menanyakan status laundry dalam bahasa natural** agar **bot bisa cek tanpa harus ketik command ribet**.
- As a **customer**, saya ingin **hanya lihat info yang relevan buat saya** (ID, outlet, status, progress, tanggal, arahan) agar **tidak bingung sama data internal**.
- As a **customer**, saya ingin **lihat total tagihan tanpa rincian metode bayar** agar **punya konteks pembayaran tanpa harus kontak agen**.
- As a **customer**, saya ingin **bisa minta "detail"** agar **lihat layanan + status ambil/pengantaran ketika butuh konteks lebih**.
- As a **owner**, saya ingin **bot auto-eskalasi ke agen untuk kondisi sensitif** (refund, hapus, komplain, dsb) agar **CS fokus ke kasus yang memang butuh manusia**.

---

## Goals & Non-Goals

### Goals

- Intent `check_laundry_status` aktif per outlet (toggleable via Control Panel, default ON).
- Slot wajib: **ID transaksi**. Format: **prefix 3 huruf + digit**, contoh `TMD260624160854112`.
- Mode respons default = **Ringkas** (≤ 8 baris + baris Total + baris Arahan), berisi ID transaksi, outlet, nama customer, status, progress, tanggal diterima, estimasi selesai, total tagihan, dan arahan berikutnya.
- Mode respons on-demand = **Detail Ringan** (trigger "detail / info lengkap / lebih lengkap / rincian"), menambahkan layanan + status ambil + pengantaran.
- Mode **Eskalasi Agent** otomatis untuk 6 kondisi sensitif — tampilkan pesan bahwa transaksi perlu dicek agent, lalu hubungkan customer ke agent.
- Transaksi tidak ditemukan → tanya ulang **2×**, escalate pada percobaan ke-3.
- Field sensitif **wajib tidak muncul** di response customer: nama karyawan/kasir, ID customer, nomor telepon, detail pajak, detail diskon, detail biaya tambahan, detail metode pembayaran, rincian pembayaran penuh, data refund, foto transaksi, foto bukti ambil, catatan internal outlet, dan data pengiriman mentah.
- Bahasa Indonesia, tone "Kak", format tanggal "DD MMMM YYYY" + jam jika relevan.
- Lookup dibatasi outlet yang sedang dipilih customer.
- Patuh hierarki kontrol fitur: jika master switch OFF, bot tidak menjawab otomatis dan customer diarahkan ke agent; jika fitur per outlet OFF, bot menampilkan fallback bahwa fitur sedang tidak tersedia di outlet tersebut.
- Command manual "menu" / "batal" selalu override: "menu" mengembalikan customer ke menu utama, "batal" membatalkan proses cek status yang sedang berjalan.

### Non-Goals (MVP)

- Lookup via nomor HP.
- Tampilkan **rincian** metode bayar / nominal per metode / data refund / foto transaksi.
- Tampilkan alamat pengantaran lengkap (masking sesuai policy PII).
- Buat/edit transaksi, refund processing, notifikasi push.
- Multi-language (English / bilingual).
- Konfigurasi 6 kondisi eskalasi per outlet (hardcode di MVP, configurable fase 2).
- Konfigurasi retry count per outlet (hardcode 2× retry).
- Tampilkan poin member di MVP ini (intent terpisah `get_member_info`).

---

## Parameter

| Parameter    | Wajib/Opsional | Sumber                                                    |
| ------------ | -------------- | --------------------------------------------------------- |
| ID transaksi | **Wajib**      | Customer input, contoh `TMD260624160854112`.              |
| Outlet       | Otomatis       | Outlet yang sedang dipilih customer.                      |
| Mode         | Otomatis       | Ringkas secara default; detail jika customer minta detail. |

---

## Flow

### Alur Utama

```text
1. Customer menanyakan status laundry di outlet yang sudah dipilih.
2. Jika fitur aktif, bot memahami intent `check_laundry_status`.
3. Jika ID transaksi belum ada, bot meminta customer mengirim ID transaksi.
4. Jika ID transaksi tidak lengkap, bot meminta customer mengirim ulang ID lengkap dengan kode depan.
5. Jika transaksi ditemukan, bot menampilkan status ringkas.
6. Jika customer meminta detail, bot menampilkan detail ringan satu kali.
7. Jika transaksi masuk kondisi sensitif, bot menghubungkan customer ke agent.
8. Jika transaksi tidak ditemukan, bot meminta customer cek ulang maksimal 2 kali, lalu eskalasi ke agent.
```

### Aturan Mode

- Default: tampilkan Mode Ringkas.
- Jika customer minta detail: tampilkan Mode Detail Ringan.
- Jika ada kondisi sensitif: tampilkan pesan eskalasi dan hubungkan ke agent.

### Error Handling

| Kondisi | Aksi |
|--------|-----|
| Transaksi ditemukan | Tampilkan sesuai mode. |
| Transaksi tidak ditemukan | Tanya ulang maksimal 2 kali, lalu eskalasi ke agent. |
| ID tidak lengkap | Minta customer kirim ulang ID lengkap. |
| Sistem bermasalah | Tampilkan pesan kendala dan hubungkan ke agent. |

---

## Mode Tampilan

### Mode 1 — Ringkas Default (default)

```
Baik Kak, berikut status transaksi Kakak:

ID Transaksi: [ID Transaksi]
Outlet: [Nama Outlet]
Nama: [Nama Customer]
Status: [Status Customer-Friendly]
Progress: [Persentase Pengerjaan]%
Diterima: [Tanggal Diterima]
Estimasi selesai: [Tanggal Selesai] [pukul HH.MM jika relevan]
Total: Rp [total] [· Lunas | · Belum lunas]

Arahan: [Arahan berikutnya sesuai status transaksi]
```

**Field wajib**: ID, Outlet, Nama, Status, Progress, Diterima, Estimasi selesai, Arahan.

**Field tambahan**: `Total: Rp X` (tanpa breakdown metode bayar). Status bayar pakai label "Lunas" / "Belum lunas" saja.

**Field WAJIB TIDAK ditampilkan**:
- Nama karyawan/kasir · ID customer · Nomor telepon customer · Detail pajak · Detail diskon · Detail biaya tambahan · Detail metode pembayaran · Rincian pembayaran penuh · Data refund · Foto transaksi · Foto bukti ambil · Catatan internal outlet · Data pengiriman mentah.

### Mode 2 — Detail Ringan (on-demand)

Trigger natural: "detail" / "info lengkap" / "lebih lengkap" / "rincian" dalam percakapan yang sama setelah status transaksi berhasil ditemukan.

```
Baik Kak, berikut detail transaksi Kakak:

ID Transaksi: [ID Transaksi]
Outlet: [Nama Outlet]
Nama: [Nama Customer]
Status: [Status Customer-Friendly]
Progress: [Persentase Pengerjaan]%
Diterima: [Tanggal Diterima]
Estimasi selesai: [Tanggal Selesai] [pukul HH.MM jika relevan]
Total: Rp [total] [· Lunas | · Belum lunas]

Layanan:
- [Nama Layanan] — [Jumlah] [Satuan]
- [Nama Layanan] — [Jumlah] [Satuan]

Status ambil: [Belum diambil | Sudah diambil | Siap diambil]
Pengantaran: [Tidak ada | Diantar, status dalam perjalanan | Sudah diantar pada [tanggal]]
```

**Aturan tambahan**:
- Minta "detail" lagi → balas "Detail sudah saya tampilkan di atas ya Kak."
- Mode detail hanya 1× render per ID; reset ke ringkas di ID berikutnya.

### Mode 3 — Eskalasi Agent (otomatis, 6 kondisi)

Picu otomatis tanpa interaksi customer:

1. Status transaksi bermasalah.
2. Transaksi dihapus.
3. Ada permintaan hapus transaksi.
4. Refund sedang pending atau diproses.
5. Pengantaran gagal.
6. Catatan mengandung keyword "komplain" / "hilang" / "rusak"

**Templat respons**:

```
Saya menemukan transaksi Kakak, tetapi ada kondisi yang perlu dicek lebih lanjut oleh agent.
Saya hubungkan Kakak ke agent outlet [Nama Outlet] ya.
Mohon tunggu sebentar.
```

Setelah pesan ini tampil, customer dihubungkan ke agent; bot tidak intervensi lagi di conversation tersebut.

---

## Label Customer-Friendly

| Kondisi Transaksi       | Label untuk Customer    |
| ----------------------- | ----------------------- |
| New received            | Laundry diterima outlet |
| In progress             | Laundry sedang diproses |
| Washing                 | Sedang dicuci           |
| Drying                  | Sedang dikeringkan      |
| Ironing                 | Dalam proses setrika    |
| Packing                 | Sedang dikemas          |
| Completed               | Siap diambil            |
| Picked up               | Sudah diambil           |
| Cancelled               | Transaksi dibatalkan    |
| Problem                 | Perlu dicek oleh agent  |

Bot WAJIB map status internal ke label ini; tidak boleh tampilkan kode internal.

### Arahan Berikutnya per Status

| Kondisi              | Arahan Customer                                                                                  |
| -------------------- | ------------------------------------------------------------------------------------------------ |
| Masih diproses       | "Laundry Kakak masih diproses. Silakan cek lagi mendekati waktu estimasi selesai ya."            |
| Siap diambil         | "Kakak bisa datang ke outlet untuk mengambil laundry."                                           |
| Sudah diambil        | "Transaksi Kakak sudah selesai dan sudah diambil." + "Ada lagi yang bisa dibantu?"               |
| Ada pengantaran      | "Status pengantaran: [Diantar / Sudah diantar pada X]".                                          |
| Data tidak ditemukan | "Mohon cek kembali ID transaksi Kakak, atau saya bisa hubungkan ke agent untuk dibantu manual." |
| Ada kendala          | Auto-eskalasi (lihat Mode 3).                                                                    |

---

## Conversation State

- Bot mengingat ID transaksi terakhir dalam percakapan yang sama.
- Jika customer minta detail untuk ID yang sama, detail hanya ditampilkan satu kali.
- Jika customer pindah outlet, state status laundry reset.
- Jika percakapan tidak aktif lebih dari 5 menit, state status laundry reset.

---

## Implikasi ke Sistem

### Control Panel

- Tidak ada perubahan UI.
- Fitur cek status laundry aktif secara default per outlet.
- Jika toggle fitur di outlet OFF, bot tidak menjalankan cek status dan menampilkan fallback: "Fitur cek status laundry sedang tidak tersedia di outlet ini ya Kak."

### Master Switch

- Patuh hierarki: master OFF → skip. Master ON + per-outlet OFF → skip + fallback. Tidak ada perubahan baru.

### Conversation Flow

- Bot menjaga konteks status laundry dalam percakapan yang sama.
- Bot reset konteks jika customer pindah outlet atau percakapan sudah stale.
- Bot punya proteksi anti-spam per nomor WA.

### Logging & Observability

- Catat outcome cek status laundry untuk monitoring.
- ID transaksi di log wajib dimasking.
- Eskalasi dicatat beserta alasannya.

---

## Edge Cases

| # | Skenario | Perilaku Bot |
|---|----------|--------------|
| 1 | ID dengan spasi / typo (`TMD 260624 160854112`) | Bot tetap mengenali sebagai `TMD260624160854112`. |
| 2 | ID pakai huruf kecil di prefix (`tmd260624160854112`) | Bot tetap mengenali sebagai `TMD260624160854112`. |
| 3 | ID pakai dash (`TMD-260624160854112`) | Bot tetap mengenali sebagai `TMD260624160854112`. |
| 4 | ID terlalu pendek atau prefix tidak lengkap (`TMD123` atau `TM123456789012`) | Tanya ulang: "ID-nya kelihatannya kurang lengkap, bisa dicek lagi Kak?" (tidak kurangi retry_count). |
| 5 | ID milik outlet lain | Tampilkan catatan bahwa transaksi tercatat di outlet lain, lalu hubungkan ke agent. |
| 6 | Customer punya 2+ transaksi di outlet ini | Bot hanya cek ID yang dikirim. Tidak menampilkan daftar transaksi. |
| 7 | Customer minta "detail" >1× dalam 1 conversation + 1 ID | Tampilkan detail 1×. Berikutnya: "Detail sudah saya tampilkan di atas ya Kak." |
| 8 | Transaksi dihapus | Bot tampilkan pesan eskalasi dan hubungkan ke agent. |
| 9 | Status transaksi bermasalah | Bot tampilkan pesan eskalasi dan hubungkan ke agent. |
| 10 | Refund pending atau diproses | Bot tampilkan pesan eskalasi dan hubungkan ke agent. |
| 11 | Pengantaran gagal | Bot tampilkan pesan eskalasi dan hubungkan ke agent. |
| 12 | Customer bilang "batal" setelah dapat ringkas | Reset slot-filling state, kembali ke menu: "Baik Kak, dibatalkan. Ada lagi yang bisa dibantu?" |
| 13 | Customer bilang "batal" sebelum kirim ID | Sama, tapi tidak ada state yang perlu di-reset. |
| 14 | Customer kirim ID + langsung "detail" dalam 1 pesan | Mode = detail saat render pertama (skip Mode Ringkas). |
| 15 | `lunas=false` tapi transaksi sudah selesai & diambil | Mode Ringkas, tampil "Selesai, sudah diambil" + "Total: Rp X · Belum lunas". Tidak follow-up payment. |
| 16 | `total = 0` (transaksi sample / promo) | Tampilkan normal, "Total: Rp 0". |
| 17 | Customer pakai bahasa campur / kode | Bot tetap berusaha memahami maksud cek status laundry. |
| 18 | Customer follow-up "kapan?" setelah dapat ringkas | Bot memakai konteks ID transaksi terakhir dan menjawab estimasi selesai jika tersedia. |
| 19 | `tanggal_selesai` null / masih proses | Tampilkan "Estimasi: belum tersedia, silakan cek lagi" jika null. |
| 20 | Foto transaksi exists | TIDAK ditampilkan otomatis. Jika customer minta → escalate (reason: photo_request). |
| 21 | Conversation stale > 5 menit | Reset state; ID berikutnya = flow baru. |
| 22 | Customer sudah terhubung ke agent, lalu chat lagi di conversation yang sama | Bot tidak intervensi. |

---

## Acceptance Criteria

### Functional

- [ ] Bot mengenali minimal 8 variasi input natural untuk cek status laundry, misalnya "laundry saya sudah selesai?", "cek status TMD260624160854112", "kapan cucian saya bisa diambil?", "status order saya gimana?", "tolong cek transaksi ini", "sudah sampai mana laundry saya?", "cek nota TMD260624160854112", dan "baju saya sudah ready?".
- [ ] Bot memahami ID transaksi dari prefix 3 huruf + digit, termasuk variasi huruf kecil, spasi, atau dash.
- [ ] Bot tampilkan ID transaksi, outlet, nama customer, status, progress, tanggal diterima, estimasi selesai, total tagihan, dan arahan di Mode Ringkas.
- [ ] Bot WAJIB TIDAK tampilkan 13 field internal: nama karyawan/kasir, ID customer, nomor telepon, detail pajak, detail diskon, detail biaya tambahan, detail metode pembayaran, rincian pembayaran penuh, data refund, foto transaksi, foto bukti ambil, catatan internal outlet, dan data pengiriman mentah.
- [ ] Customer bisa trigger "detail" → bot tampilkan layanan + status ambil + pengantaran.
- [ ] Jika transaksi tidak ditemukan, bot tanya ulang maksimal 2 kali dan eskalasi pada percobaan ke-3.
- [ ] Bot auto-eskalasi untuk 6 kondisi dengan reason masing-masing.

### Bot Behavior

- [ ] Mode default = Ringkas (≤ 8 baris + baris Total + baris Arahan).
- [ ] Mode Detail hanya via trigger natural (4 trigger phrase).
- [ ] Mode Eskalasi tampilkan info ringkas dulu, lalu handoff.
- [ ] Bahasa Indonesia, tone "Kak", WhatsApp-friendly.
- [ ] Format tanggal customer-friendly: "DD MMMM YYYY" (contoh "24 Juni 2026"), sertakan jam jika relevan ("25 Juni 2026 pukul 17.00").
- [ ] Label status customer-friendly: Laundry diterima outlet, Laundry sedang diproses, Sedang dicuci, Sedang dikeringkan, Dalam proses setrika, Sedang dikemas, Siap diambil, Sudah diambil, Transaksi dibatalkan, Perlu dicek oleh agent.
- [ ] Total format: "Rp 50.000" (ribuan pakai titik desimal sesuai konvensi ID).
- [ ] Status bayar label: "Lunas" / "Belum lunas" saja, tanpa nominal breakdown.
- [ ] Customer follow-up dalam conversation yang sama tetap flow `check_laundry_status`.

### Performance

- [ ] Target waktu jawab status laundry ≤ 8 detik untuk mayoritas percakapan.
- [ ] State cleanup TTL 5 menit.

### Security

- [ ] ID transaksi di-log masking 4 char terakhir (`*******9812`).
- [ ] Tidak ada PII tambahan (HP customer, alamat lengkap, email).
- [ ] Proteksi anti-spam per nomor WA: maksimal 5 kali cek status dalam 5 menit.
- [ ] Audit log wajib mencatat outlet, intent, ID transaksi masked, outcome, dan alasan eskalasi jika ada.

---

## Out of Scope (Future)

- Lookup via nomor HP.
- Rincian metode bayar / data refund / foto transaksi otomatis.
- Alamat pengantaran lengkap (masking sesuai policy PII).
- Notifikasi push.
- Multi-language.
- Edit transaksi / buat baru / refund processing.
- Konfigurasi per outlet untuk 6 kondisi eskalasi (hardcode MVP).
- Konfigurasi retry count per outlet.
- Voice note handling.
- Tampilkan poin member.

---

## Keputusan Produk

1. Format ID transaksi = prefix 3 huruf + digit, contoh `TMD260624160854112`.
2. Jika transaksi tidak ditemukan, bot tanya ulang customer maksimal 2 kali. Pada percobaan ke-3, bot hubungkan customer ke agent.
3. Foto transaksi tidak dikirim otomatis ke customer. Jika customer meminta foto, bot hubungkan ke agent.
4. Bot wajib punya proteksi anti-spam per nomor WA.
5. Transaksi overdue + belum bayar tidak masuk auto-eskalasi MVP. Kondisi ini ditangani manual oleh agent jika dibutuhkan.
6. Jika customer pindah outlet di tengah percakapan, state cek status laundry reset supaya data tidak bocor antar outlet.
