# Cek Status Laundry (`check_laundry_status`) — PRD

## Overview

Fitur cek status laundry memungkinkan customer mengetahui status transaksi laundry berdasarkan **ID transaksi**. Customer mengetik permintaan dalam bahasa natural; bot mengekstrak ID, fetch ke endpoint `GET /masterData/meta/detail_data_transaksi`, lalu menampilkan status dengan label customer-friendly. Jika butuh konteks tambahan, customer bisa request "detail". Pada kondisi tertentu (refund, transaksi rusak, dsb) bot auto-eskalasi ke agen manusia.

Tujuan: menjawab pertanyaan repetitif tertinggi CS dengan self-service yang aman, singkat, dan konsisten di semua outlet.

---

## User Stories

- As a **customer**, saya ingin **menanyakan status laundry dalam bahasa natural** agar **bot帮我 cek tanpa harus ketik command ribet**.
- As a **customer**, saya ingin **hanya lihat info yang relevan buat saya** (ID, outlet, status, progress, tanggal, arahan) agar **tidak bingung sama data internal**.
- As a **customer**, saya ingin **lihat total tagihan tanpa rincian metode bayar** agar **punya konteks pembayaran tanpa harus kontak agen**.
- As a **customer**, saya ingin **bot tidak menampilkan rincian metode bayar** (nominal per metode, data refund, dsb) agar **info sensitif tetap aman di tangan agen**.
- As a **customer**, saya ingin **bisa minta "detail"** agar **lihat layanan + status ambil/pengantaran ketika butuh konteks lebih**.
- As a **owner**, saya ingin **bot auto-eskalasi ke agen untuk kondisi sensitif** (refund, hapus, komplain, dsb) agar **CS fokus ke kasus yang memang butuh manusia**.
- As a **owner**, saya ingin **bot tidak menampilkan 13 field sensitif** (payment detail, alamat lengkap, HP, foto, dll) agar **compliance & privasi terjaga**.

---

## Goals & Non-Goals

### Goals

- Intent `check_laundry_status` aktif per outlet (toggleable via Control Panel, default ON).
- Slot wajib: **ID transaksi**. Format dikenali: **prefix 3 huruf + digit**, contoh `TMD260624160854112`. Prefix wajib dikirim utuh, digit harus lengkap.
- Mode respons default = **Ringkas** (≤ 8 baris, wajib tampilkan 8 field per [main.md](../docs/main.md) 9.6 + total tagihan).
- Mode respons on-demand = **Detail Ringan** (trigger "detail / info lengkap / lebih lengkap / rincian"), menambahkan layanan + status ambil + pengantaran.
- Mode **Eskalasi Agent** otomatis untuk 6 kondisi sensitif — tampilkan info ringkas dulu, lalu handoff ke `talk_to_agent`.
- Transaksi tidak ditemukan → tanya ulang **2×**, escalate pada percobaan ke-3.
- 13 field "Tidak Disarankan Ditampilkan" per [main.md](../docs/main.md) 9.8 **wajib tidak muncul** di response customer.
- Bahasa Indonesia, tone "Kak", format tanggal "DD MMMM YYYY" + jam jika relevan.
- Lookup dibatasi outlet dari conversation context (tidak bocor ke outlet lain).
- Patuh hierarki master switch + per-outlet intent toggle (dari Control Panel PRD).
- Command manual "menu" / "batal" selalu override (lihat [master-switch brainstorm](../brainstorm/2026-07-01-master-switch.md)).

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

| Parameter    | Wajib/Opsional | Sumber                                                                                          |
| ------------ | -------------- | ----------------------------------------------------------------------------------------------- |
| ID transaksi | **Wajib**      | Customer input — prefix 3 huruf + digit (contoh `TMD260624160854112`). Normalisasi di bot layer. |
| Outlet       | Otomatis       | Conversation context (pilihan outlet di awal percakapan).                                       |
| Mode         | Otomatis       | Default `ringkas`; berubah ke `detail` jika customer trigger on-demand.                        |
| Retry count  | State          | Lokal per conversation; reset setelah ID berhasil ditemukan atau escalate.                       |

**Regex identifikasi ID (di bot layer, pre-fetch):**

```
Prefix 3 huruf (uppercase atau lowercase) + digit lengkap, opsional whitespace/dash di antara.
Contoh match: TMD260624160854112, tmd260624160854112, TMD 260624160854112, TMD-260624160854112.
```

**Normalisasi**: uppercase prefix 3 huruf, strip whitespace & dash. **Prefix 3 huruf TIDAK di-strip** — harus ikut terkirim utuh ke endpoint sebagai `id_transaksi_clean`.

---

## Flow

### Alur Utama

```text
1. Customer kirim pesan di outlet X (outlet_id=X, waba_id=W sudah di context)
2. Master switch check (WABA W):
   - OFF → skip, silent handoff ke talk_to_agent (sesuai master-switch PRD)
   - ON  → lanjut
3. outlet_intent_settings.check_laundry_status check (outlet X):
   - OFF → tampil fallback "Fitur ini sedang tidak tersedia di outlet ini"
   - ON  → lanjut
4. LLM intent classification:
   - check_laundry_status → masuk flow ini
   - other → handle sesuai intent masing-masing
5. Slot filling — ID transaksi:
   a. Cek regex pada pesan customer
   b. Match → langsung ke step 7
   c. Tidak match → tanya "Baik Kak, boleh kirim ID transaksi laundry-nya?"
      → State: awaiting_id
      → Timeout 1 menit (global) → escalate
6. (tidak applicable: ID sudah match di step 5b)
7. Pre-fetch validation:
   - id_transaksi_clean harus berupa prefix 3 huruf (uppercase) + digit, tanpa whitespace/dash.
   - Prefix kurang dari 3 huruf atau digit kosong → tanya ulang (tidak kurangi retry_count).
   - Customer chat dengan numeric saja tanpa prefix → tanya ulang: "Kode depan ID-nya kelihatannya kurang lengkap, bisa kirim ulang lengkap dengan kode di depannya?"
8. Fetch API:
   GET {BASE_API}/masterData/meta/detail_data_transaksi?idtransaksi={id}
   Authorization: Bearer {jwt}
   Timeout: 10 detik
9. Handling response (lihat Decision Matrix di bawah)
10.Conversation state cleanup:
   - Stale > 5 menit → state reset (untuk conversation baru)
   - Mode "detail" → 1× render saja; minta lagi → "Detail sudah ditampilkan di atas ya Kak."
```

### Decision Matrix (Mode Selection)

```
fetched_data = api_response.data.detail_transaksi + family

evaluated = false:
  if status_transaksi_format == "Problem"                    → escalated = true
  if status_hapus == 1                                       → escalated = true  (reason: deleted)
  if idpermintaan_hapus != null                              → escalated = true  (reason: deletion_request)
  if data_refund.status ∈ {"pending","diproses"}             → escalated = true  (reason: refund_pending)
  if pengantaran_extra.status ∈ {"gagal","failed"}           → escalated = true  (reason: delivery_failed)
  if catatan mengandung keyword komplain/hilang/rusak        → escalated = true  (reason: customer_complaint)
else → escalated = false

if escalated:
  tampil ringkas (ID + outlet + nama + status + 1 kalimat konteks)
  + escalate ke talk_to_agent
  + audit log reason
else if mode == "detail":
  tampil detail ringan
else:
  tampil ringkas default
```

### Error Handling

| Status | Aksi |
|--------|-----|
| 200 + data valid | Lihat Decision Matrix di atas. |
| 404 | retry_count += 1; kalau ≤ 2 → tanya ulang; kalau > 2 → escalate. |
| 400 | bot tidak sampai sini (pre-valid); kalau muncul → tanya ulang. |
| 401 | Log error + tampil "Sistem ada kendala, saya hubungkan ke agent ya." + escalate. |
| 500 | Sama seperti 401 tapi variasi pesan. |
| Timeout > 10s | Treat sama seperti 500. |

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

Arahan: [Arahan Berikutnya per main.md 9.11]
```

**Field wajib** (per [main.md](../docs/main.md) 9.6): ID, Outlet, Nama, Status, Progress, Diterima, Estimasi selesai, Arahan.

**Field tambahan** (PRD ini override [main.md](../docs/main.md) 9.8): `Total: Rp X` (tanpa breakdown metode bayar). Status bayar pakai label "Lunas" / "Belum lunas" saja (lihat Open Q #3 jawaban final).

**Field WAJIB TIDAK ditampilkan** (per [main.md](../docs/main.md) 9.8, 13 item):
- Nama karyawan/kasir · ID customer · Nomor telepon customer · Detail pajak · Detail diskon · Detail biaya tambahan · Detail metode pembayaran · Rincian pembayaran penuh · Data refund · Foto transaksi · Foto bukti ambil · Catatan internal outlet · Data pengiriman mentah.

### Mode 2 — Detail Ringan (on-demand)

Trigger natural: "detail" / "info lengkap" / "lebih lengkap" / "rincian" (dalam conversation ini + ada `last_id_transaksi` valid).

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
- Minta "detail" lagi → balas "Detail sudah saya tampilkan di atas ya Kak." (tidak re-fetch, tidak re-render).
- Mode detail hanya 1× render per ID; reset ke ringkas di ID berikutnya.

### Mode 3 — Eskalasi Agent (otomatis, 6 kondisi)

Picu otomatis tanpa interaksi customer:

1. `status_transaksi_format == "Problem"`
2. `status_hapus == 1` (transaksi dihapus)
3. `idpermintaan_hapus != null` (ada permintaan hapus)
4. `data_refund.status` ∈ `{pending, diproses}`
5. `pengantaran_extra.status` ∈ `{gagal, failed}`
6. Catatan mengandung keyword "komplain" / "hilang" / "rusak"

**Templat respons**:

```
Saya menemukan transaksi Kakak, tetapi ada kondisi yang perlu dicek lebih lanjut oleh agent.
Saya hubungkan Kakak ke agent outlet [Nama Outlet] ya.
Mohon tunggu sebentar.
```

Setelah tampilkan pesan ini → trigger `talk_to_agent` secara otomatis; bot tidak intervensi lagi di conversation tersebut.

---

## Label Customer-Friendly (mirror [bot-behavior.md](../docs/bot-behavior.md))

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

### Arahan Berikutnya per Status (mirror [main.md](../docs/main.md) 9.11)

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

Field lokal per conversation (memory saja, bukan DB):

```
laundry_context = {
  last_id_transaksi_clean: string  // numeric only
  mode: "ringkas" | "detail"      // default ringkas; berubah saat customer trigger
  retry_count: int                // 0-2; reset saat ID berhasil atau escalate
  mode_detail_already_rendered: bool
  last_fetch_at: timestamp        // untuk cache TTL 60 detik
  last_outlet_id: uuid
  last_status_http: int
}
```

**Cleanup**:
- TTL state = 5 menit tanpa interaksi → reset.
- Cache fetched data = 60 detik untuk 1 ID dalam 1 conversation window (anti duplicate-fetch).

---

## Implikasi ke Sistem

### Control Panel

- Tidak ada perubahan UI. Pengaturan `outlet_intent_settings.check_laundry_status` sudah ada (default `true`).
- Verifikasi: toggle OFF → bot skip intent + tampil fallback (sudah sesuai Control Panel PRD).

### Master Switch

- Patuh hierarki: master OFF → skip. Master ON + per-outlet OFF → skip + fallback. Tidak ada perubahan baru.

### Conversation Flow

- Tambah state `laundry_context` (lihat di atas).
- Tambah timer stale cleanup 5 menit.
- Rate limit: maks 5 fetch / 5 menit per nomor WA (anti-abuse).

### Response Template Registry

Tambah 4 template:
- `laundry.ringkas` — Mode 1.
- `laundry.detail` — Mode 2.
- `laundry.not_found_retry` — pesan retry.
- `laundry.escalate_sensitive` — Mode 3.

### Logging & Observability

Event `laundry_status.fetched`:
```
{
  outlet_id: uuid,
  id_transaksi_masked: string,    // "******9812"
  http_status: int,
  mode: "ringkas" | "detail",
  escalated: bool,
  escalated_reason: string | null  // dari 6 kondisi
}
```

Counter `laundry_status.escalated_total{reason}` untuk monitoring.

### Dependencies

- Endpoint `GET /masterData/meta/detail_data_transaksi` (sudah ada, Open Q #1/#2 untuk konfirmasi tim backend).
- JWT auth per outlet (sudah ada di WABA selection).

---

## Edge Cases

| # | Skenario | Perilaku Bot |
|---|----------|--------------|
| 1 | ID dengan spasi / typo (`TMD 260624 160854112`) | Normalisasi (strip spasi & dash), fetch dengan `TMD260624160854112`. |
| 2 | ID pakai huruf kecil di prefix (`tmd260624160854112`) | Uppercase prefix → `TMD260624160854112`, fetch. |
| 3 | ID pakai dash (`TMD-260624160854112`) | Strip dash → `TMD260624160854112`, fetch. |
| 4 | ID terlalu pendek atau prefix tidak lengkap (`TMD123` atau `TM123456789012`) | Tanya ulang: "ID-nya kelihatannya kurang lengkap, bisa dicek lagi Kak?" (tidak kurangi retry_count). |
| 5 | ID milik outlet lain (`fetched.nama_outlet != outlet customer`) | Tampilkan catatan "Transaksi ini tercatat di outlet [X], beda dengan outlet yang sedang dipilih Kakak." + auto-escalate. |
| 6 | Customer punya 2+ transaksi di outlet ini | Bot hanya fetch ID yang dikirim. Tidak auto-listing. |
| 7 | Customer minta "detail" >1× dalam 1 conversation + 1 ID | Tampilkan detail 1×. Berikutnya: "Detail sudah saya tampilkan di atas ya Kak." |
| 8 | Transaksi `status_hapus = 1` | Bot tampil ringkas + escalate (reason: deleted). |
| 9 | Status `Problem` | Auto-escalate (reason: problem). |
| 10 | Refund `status ∈ {pending, diproses}` | Auto-escalate (reason: refund_pending). |
| 11 | Pengantaran `gagal` | Auto-escalate (reason: delivery_failed). |
| 12 | Customer bilang "batal" setelah dapat ringkas | Reset slot-filling state, kembali ke menu: "Baik Kak, dibatalkan. Ada lagi yang bisa dibantu?" |
| 13 | Customer bilang "batal" sebelum kirim ID | Sama, tapi tidak ada state yang perlu di-reset. |
| 14 | Customer kirim ID + langsung "detail" dalam 1 pesan | Mode = detail saat render pertama (skip Mode Ringkas). |
| 15 | `lunas=false` tapi transaksi sudah selesai & diambil | Mode Ringkas, tampil "Selesai, sudah diambil" + "Total: Rp X · Belum lunas". Tidak follow-up payment. |
| 16 | `total = 0` (transaksi sample / promo) | Tampilkan normal, "Total: Rp 0". |
| 17 | Customer pakai bahasa campur / kode | LLM handles; intent classification robust. |
| 18 | Customer follow-up "kapan?" setelah dapat ringkas | Tetap flow `check_laundry_status` (`last_id_transaksi` masih ada), tampilkan ulang ringkas atau info tambahan jika relevan. |
| 19 | `tanggal_selesai` null / masih proses | Tampilkan "Estimasi: belum tersedia, silakan cek lagi" jika null. |
| 20 | Foto transaksi exists | TIDAK ditampilkan otomatis. Jika customer minta → escalate (reason: photo_request). |
| 21 | Conversation stale > 5 menit | Reset state; ID berikutnya = flow baru. |
| 22 | Customer sudah escalate, lalu chat lagi di conversation yang sama | Bot tidak intervensi (talk_to_agent handle). |

---

## Acceptance Criteria

### Functional

- [ ] LLM klasifikasi `check_laundry_status` dari minimal 8 variasi input natural (lihat [main.md](../docs/main.md) 9.3).
- [ ] Bot ekstrak ID transaksi dari prefix 3 huruf (case insensitive) + digit, opsional whitespace/dash.
- [ ] Bot normalisasi ID (uppercase prefix, strip spasi/dash) sebelum fetch.
- [ ] Bot fetch `GET /masterData/meta/detail_data_transaksi?idtransaksi={id}` dengan JWT outlet.
- [ ] Bot tampilkan 8 field wajib per [main.md](../docs/main.md) 9.6 + Total (override 9.8) di Mode Ringkas.
- [ ] Bot WAJIB TIDAK tampilkan 13 field internal per [main.md](../docs/main.md) 9.8.
- [ ] Customer bisa trigger "detail" → bot tampilkan layanan + status ambil + pengantaran.
- [ ] Bot retry 2× untuk 404, escalate pada percobaan ke-3.
- [ ] Bot auto-eskalasi untuk 6 kondisi dengan reason masing-masing.

### Bot Behavior

- [ ] Mode default = Ringkas (≤ 8 baris + baris Total + baris Arahan).
- [ ] Mode Detail hanya via trigger natural (4 trigger phrase).
- [ ] Mode Eskalasi tampilkan info ringkas dulu, lalu handoff.
- [ ] Bahasa Indonesia, tone "Kak", WhatsApp-friendly.
- [ ] Format tanggal customer-friendly: "DD MMMM YYYY" (contoh "24 Juni 2026"), sertakan jam jika relevan ("25 Juni 2026 pukul 17.00").
- [ ] Label status customer-friendly (mirror [bot-behavior.md](../docs/bot-behavior.md)).
- [ ] Total format: "Rp 50.000" (ribuan pakai titik desimal sesuai konvensi ID).
- [ ] Status bayar label: "Lunas" / "Belum lunas" saja, tanpa nominal breakdown.
- [ ] Customer follow-up dalam conversation yang sama tetap flow `check_laundry_status`.

### Performance

- [ ] Fetch API timeout: 10 detik.
- [ ] Total response time (classify + fetch + render) target p95 ≤ 8 detik.
- [ ] Cache fetched data 60 detik per ID (anti duplicate-fetch).
- [ ] State cleanup TTL 5 menit.

### Security

- [ ] JWT bot tidak bocor ke customer (log internal only).
- [ ] ID transaksi di-log masking 4 char terakhir (`*******9812`).
- [ ] Tidak ada PII tambahan (HP customer, alamat lengkap, email).
- [ ] Rate limit per nomor WA: maks 5 fetch / 5 menit.
- [ ] Audit log wajib: timestamp, outlet_id, intent, id_transaksi (masked), outcome (200/404/timeout/escalated + reason).

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

## Open Questions (perlu konfirmasi sebelum dev start)

1. **[ANSWERED]** Format ID transaksi = prefix 3 huruf + digit (contoh `TMD260624160854112`). Prefix wajib dikirim utuh ke endpoint. Bot cukup normalisasi case + whitespace/dash. **Tidak** strip prefix.
2. **[DONE — untuk developer]** Gimana response kalo ID gak ketemu? Endpoint balik apa? Developer yang atur. Yang penting: kalo gak ketemu → bot tanya ulang customer max 2×, ke-3 eskalasi ke agent.
3. **[CLOSED]** Foto transaksi tidak usah dikirim otomatis ke customer. Done.
4. **[DONE — untuk developer]** Rate limit ambilalir dari dev, yang penting ada proteksi anti-spam per nomor WA.
5. **[CLOSED]** Logic auto-eskalasi transaksi overdue + belum bayar dibuang dari MVP. Sekarang 6 kondisi eskalasi (sebelumnya 7). Ditangani manual sama agen.
6. **[ANSWERED]** Ya, kalau customer pindah outlet di tengah conversation → state reset. Aman, nggak bocor data antar outlet.
