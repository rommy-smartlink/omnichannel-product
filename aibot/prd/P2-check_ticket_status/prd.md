# Cek Status Ticket (`check_ticket_status`) — PRD

## Overview

Fitur cek status ticket memungkinkan customer mengetahui perkembangan ticket berdasarkan **nomor ticket**. Customer mengetik permintaan dalam bahasa natural; bot mengekstrak nomor ticket, fetch ke existing system **detail ticket**, lalu menampilkan status dengan label customer-friendly.

Pada MVP, ticket bersifat **read-only**. Bot tidak membuat ticket, tidak mengubah status ticket, tidak menutup ticket, dan tidak menambahkan catatan ticket.

Tujuan: menjawab pertanyaan follow-up ticket yang repetitif secara self-service, singkat, aman, dan tetap memberi jalan eskalasi ke agent jika kasus butuh manusia.

---

## User Stories

- As a **customer**, saya ingin **cek status ticket lewat WhatsApp** agar **tidak perlu menunggu agent hanya untuk update sederhana**.
- As a **customer**, saya ingin **menanyakan status ticket dalam bahasa natural** agar **tidak perlu mengetik command khusus**.
- As a **customer**, saya ingin **bot meminta ID ticket jika belum saya kirim** agar **saya tahu data apa yang dibutuhkan**.
- As a **customer**, saya ingin **melihat status ticket dengan bahasa yang mudah dipahami** agar **saya tahu ticket saya sedang di tahap apa**.
- As a **customer**, saya ingin **melihat detail ticket yang aman ditampilkan** agar **mendapat konteks tanpa data internal outlet**.
- As an **owner**, saya ingin **bot hanya membaca status ticket** agar **tidak ada ticket yang berubah karena interaksi bot**.
- As an **owner**, saya ingin **bot menyembunyikan catatan internal dan data operasional sensitif** agar **privasi dan proses internal tetap aman**.
- As an **owner**, saya ingin **bot eskalasi untuk ticket sensitif atau ambigu** agar **agent tetap menangani kasus yang butuh judgment manusia**.

---

## Goals & Non-Goals

### Goals

- Intent `check_ticket_status` aktif per outlet, toggleable via Control Panel, default ON.
- Slot wajib: **ID ticket**.
- Bot mengenali contoh input seperti:
  - “Saya mau cek tiket.”
  - “Cek status ticket TKOM-0004-0626.”
  - “Tiket saya sudah sampai mana?”
  - “Nomor tiket saya TKOM-0004-0626.”
- Existing system yang dipakai:
  - **Detail ticket** untuk response utama berdasarkan nomor ticket.
- Lookup dibatasi outlet dari conversation context.
- Mode tampilan hanya 2:
  - **Detail Ticket** untuk ticket ditemukan dan aman ditampilkan.
  - **Eskalasi Agent** untuk ticket sensitif / butuh agent / error.
- Ticket tidak ditemukan → tanya ulang **2×**, escalate pada percobaan ke-3.
- Bot auto-eskalasi untuk kondisi sensitif / butuh agent.
- Bahasa Indonesia, tone “Kak”, WhatsApp-friendly.
- Patuh hierarki master switch + per-outlet intent toggle dari Control Panel PRD.
- Command manual “agent / admin / CS / manusia” selalu override dan langsung masuk `talk_to_agent`.

### Non-Goals (MVP)

- Membuat ticket baru.
- Mengubah status ticket.
- Menutup / reopen ticket.
- Menambahkan komentar, balasan, attachment, atau catatan ticket.
- Assign / reassign agent.
- Menampilkan catatan internal, assignment history, audit log, atau root cause internal.
- Menampilkan informasi refund / pembayaran detail.
- Lookup ticket lintas outlet.

- Lookup via nomor HP / nama customer.
- SLA guarantee otomatis.
- Multi-language.
- Konfigurasi status mapping per outlet.

---

## Parameter

| Parameter | Wajib/Opsional | Sumber | Keterangan |
| --- | --- | --- | --- |
| ID ticket | **Wajib** | Customer input | Nomor ticket dari existing system. Contoh: `TKOM-0004-0626`. |
| Outlet | **Wajib otomatis** | Conversation context | Outlet yang dipilih customer di awal percakapan. |

### Normalisasi ID Ticket

MVP menerima ID ticket sebagai string customer input, lalu bot melakukan normalisasi ringan:

- Trim whitespace di awal/akhir.
- Uppercase huruf.
- Pertahankan dash (`-`) jika format existing system memakainya.
- Jangan menebak ID jika input terlalu ambigu.

> Format nomor ticket: `TKOM-0004-0626` (ticket number, bukan internal ID).

---

## Flow

### Alur Utama

```text
1. Customer kirim pesan di outlet X (outlet_id=X, waba_id=W sudah di context)
2. Master switch check (WABA W):
   - OFF → skip, silent handoff ke talk_to_agent
   - ON  → lanjut
3. outlet_intent_settings.check_ticket_status check (outlet X):
   - OFF → tampil fallback “Fitur cek status tiket sedang tidak tersedia di outlet ini.”
   - ON  → lanjut
4. LLM intent classification:
   - check_ticket_status → masuk flow ini
   - talk_to_agent → langsung eskalasi
   - other → handle sesuai intent masing-masing
5. Slot filling — ID ticket:
   a. ID ticket terdeteksi di pesan customer → normalisasi → lanjut fetch
   b. ID ticket belum ada → tanya “Baik Kak, boleh kirim nomor ticket-nya?”
    → Jika customer tidak membalas, bot diam; tidak ada follow-up otomatis
    → Jika customer mengirim pesan lain, bot klasifikasi ulang pesan tersebut
6. Fetch existing system — Detail ticket by nomor ticket (scoped to outlet context).
7. Handling response:
  - Ticket ditemukan + aman ditampilkan → render Detail Ticket
   - Ticket tidak ditemukan → retry / escalate
   - Ticket sensitif → render pesan aman + escalate
   - API error → escalate
```

### Decision Matrix

```text
if ticket not found:
  tanya ulang ID ticket maksimal 2×
  jika masih tidak ditemukan → escalate ke agent

else if API error / timeout / unauthorized:
  tampil pesan kendala sistem + escalate ke agent

else if customer meminta agent kapan pun:
  escalate ke talk_to_agent tanpa tanya alasan

else if ticket status / content sensitif:
  tampil pesan aman + escalate ke agent

else:
  tampil Detail Ticket
```

### Kondisi Auto-Eskalasi

Bot auto-eskalasi jika salah satu terjadi:

1. Ticket mengandung isu sensitif: `refund`, `pembayaran`, `hilang`, `rusak`, `komplain berat`, `dispute`.
2. Ticket status `RESOLVED`, tetapi customer menyatakan masalah belum selesai.
3. Ticket tidak ditemukan setelah 2× retry.
4. API detail ticket error, timeout, atau unauthorized.
5. Customer minta agent/admin/CS/manusia kapan pun.

---

## Error Handling

| Kondisi | Aksi |
| --- | --- |
| 200 + data valid | Render sesuai Decision Matrix. |
| Ticket tidak ditemukan | Retry maksimal 2×, lalu escalate. |
| ID ticket kosong / ambigu | Tanya ulang tanpa fetch. |
| 401 / unauthorized | Log error + tampil “Sistem ada kendala, saya hubungkan ke agent ya.” + escalate. |
| 500 | Sama seperti 401. |
| Timeout > 10 detik | Sama seperti 500. |
| Response data tidak lengkap | Tampilkan field aman yang tersedia + escalate jika status tidak bisa dipastikan. |

---

## Mode Tampilan

### Field Mapping dari Object Ticket

| Field Object | Dipakai di Response | Aturan |
| --- | --- | --- |
| `ticket_number` | Ya | Tampilkan sebagai `ID Ticket`. |
| `outlet_name` | Ya | Tampilkan sebagai `Outlet`. |
| `contact_name` | Ya | Tampilkan nama depan / nama customer secukupnya. |
| `title` | Ya | Tampilkan sebagai `Topik`, jika aman. |
| `summary` | Opsional | Tampilkan sebagai `Ringkasan` hanya jika aman dan tidak kasar/sensitif; jika tidak aman, ganti “Detail laporan sedang dicek agent.” |
| `category` | Ya | Map ke label customer-friendly. |
| `priority` | Tidak default | Jangan tampilkan ke customer; boleh dipakai internal untuk eskalasi. |
| `status` | Ya | Map ke label customer-friendly. |
| `created_at` | Ya | Format `DD MMMM YYYY, HH.mm`. |
| `updated_at` | Ya | Format `DD MMMM YYYY, HH.mm`. |
| `resolved_at` | Opsional | Tampilkan hanya jika ticket sudah selesai dan value tersedia. |
| `notas[].external_id` | Opsional | Tampilkan sebagai `Transaksi terkait` maksimal 3 item, hanya ID transaksi. Hanya tampilkan jika ada (ticket tidak selalu terkait transaksi laundry). |
| `linked_messages` | Tidak | Jangan tampilkan isi message raw. |
| `contact_phone` | Tidak | PII, jangan tampilkan. |
| `assignee_name`, `created_by_name`, `resolved_by_name`, `employee_name` | Tidak | Data staff internal, jangan tampilkan. |
| `id`, `waba_id`, `organization_id`, `contact_id`, `room_id`, `external_channel_id` | Tidak | ID internal, jangan tampilkan. |

### Category Mapping

| `category` | Label untuk Customer |
| --- | --- |
| `KENDALA` | Kendala |
| `KOMPLAIN` | Komplain |
| `PERTANYAAN` | Pertanyaan |
| `PERMINTAAN` | Permintaan bantuan |
| Unknown | Laporan |

### Priority Mapping Internal

`priority` tidak ditampilkan ke customer di MVP. Dipakai untuk logic internal:

| `priority` | Perilaku |
| --- | --- |
| `LOW` / `MEDIUM` | Response normal. |
| `HIGH` / `URGENT` | Response normal + boleh auto-escalate jika summary/title mengandung isu sensitif. |

### Summary Safety Filter

Sebelum menampilkan `summary`, bot wajib cek keamanan teks.

Jangan tampilkan `summary` jika berisi:

- kata kasar / menghina;
- data pribadi;
- detail pembayaran / refund;
- tuduhan berat yang belum diverifikasi;
- isi chat raw yang membingungkan customer.

Fallback ringkasan:

```text
Detail laporan sedang dicek oleh tim outlet.
```

### Mode 1 — Detail Ticket

Mode default saat ticket ditemukan dan aman ditampilkan.

```text
Baik Kak, berikut detail tiket Kakak:

ID Ticket: [ID Ticket]
Outlet: [Nama Outlet]
Nama: [Nama Customer]
Kategori: [Kategori customer-friendly]
Status: [Status customer-friendly]
Dibuat: [Tanggal dibuat]
Update terakhir: [Tanggal / keterangan aman]
Topik: [Title aman]
Ringkasan: [Deskripsi aman / fallback]
Transaksi terkait:
- [ID transaksi 1]
- [ID transaksi 2]
```

**Aturan tambahan:**

- Jika detail berisi catatan internal / data sensitif, bot harus masking atau tidak menampilkan bagian itu.
- `Transaksi terkait` hanya menampilkan `notas[].external_id`; jangan tampilkan nama employee, employee ID, atau source internal.

**Contoh berdasarkan object:**

```text
Baik Kak, berikut detail tiket Kakak:

ID Ticket: TKOM-0004-0626
Outlet: Wangi Laundry - utama
Nama: Rommy Zohara
Kategori: Kendala
Status: Tiket diterima
Dibuat: 18 Juni 2026, 13.33
Update terakhir: 18 Juni 2026, 13.33
Topik: qris
Ringkasan: Detail laporan sedang dicek oleh tim outlet.
Transaksi terkait:
- ZTK260618132540441
- MTN260618132015097
```

### Mode 2 — Eskalasi Agent

Template:

```text
Saya menemukan tiket Kakak, tetapi perlu dicek lebih lanjut oleh agent.
Saya hubungkan Kakak ke agent outlet [Nama Outlet] ya.
Mohon tunggu sebentar.
```

Setelah pesan ini → trigger `talk_to_agent`; bot berhenti membalas.

---

## Label Customer-Friendly

| Status Internal | Label untuk Customer |
| --- | --- |
| `OPEN` | Tiket diterima |
| `IN_PROGRESS` | Sedang ditindaklanjuti |
| `RESOLVED` | Sudah diselesaikan |

Bot wajib map status internal ke label ini. Jika status internal belum dikenal, tampilkan “Sedang dicek tim outlet” dan escalate jika risiko salah informasi tinggi.

---

## Copy Status per Status

| Kondisi | Copy Customer |
| --- | --- |
| Tiket diterima | “Tiket Kakak sudah diterima. Mohon ditunggu, tim outlet akan mengecek sesuai antrean.” |
| Sedang dicek / ditindaklanjuti | “Tim outlet sedang menindaklanjuti tiket Kakak. Mohon ditunggu ya Kak.” |
| Sudah diselesaikan | “Tiket Kakak tercatat sudah diselesaikan. Jika masih ada kendala, saya bisa hubungkan ke agent.” |
| Data tidak ditemukan | “Mohon cek kembali nomor ticket Kakak, atau saya bisa hubungkan ke agent untuk dibantu manual.” |
| Ada kendala sistem | Auto-eskalasi ke agent. |

---

## Field Yang Tidak Boleh Ditampilkan

Bot wajib tidak menampilkan:

- Catatan internal outlet.
- Nama agent / staff internal jika bukan bagian dari pesan customer-facing.
- Assignment history.
- Audit log.
- Internal priority jika tidak customer-safe.
- Root cause internal yang belum final.
- Data customer lain.
- Nomor telepon / alamat lengkap / PII yang tidak perlu.
- Attachment / foto internal.
- Informasi refund detail.
- Detail pembayaran.
- Raw payload API.
- ID internal database selain ID ticket customer-facing.

## Privacy & Safety Rules

- Bot hanya boleh menggunakan outlet dari conversation context.
- Bot tidak boleh lookup ticket milik outlet lain.
- Bot tidak boleh menampilkan data internal walaupun tersedia di detail ticket.
- Bot tidak boleh mengubah data ticket.
- Bot tidak boleh memberi janji SLA spesifik kecuali ada field resmi dari existing system.
- Jika data ambigu, lebih aman eskalasi daripada menebak.

---

## Acceptance Criteria

- [ ] Customer dapat meminta status ticket dengan bahasa bebas.
- [ ] Bot dapat mengenali intent `check_ticket_status`.
- [ ] Bot dapat meminta ID ticket jika belum tersedia.
- [ ] Bot dapat membaca ticket dari existing system berdasarkan ID ticket.
- [ ] Bot membatasi lookup berdasarkan outlet context.
- [ ] Bot menampilkan ID ticket dan status ticket.
- [ ] Bot mendukung mode Detail Ticket.
- [ ] Bot mendukung mode Eskalasi Agent.
- [ ] Bot tidak membuat, mengubah, menutup, assign, atau menambahkan catatan ticket.
- [ ] Bot tidak menampilkan catatan internal, audit log, raw payload, PII, refund detail, atau payment detail.
- [ ] Bot retry maksimal 2× jika ticket tidak ditemukan.
- [ ] Bot eskalasi pada percobaan ke-3 jika ticket tetap tidak ditemukan.
- [ ] Bot eskalasi jika customer meminta agent kapan pun.
- [ ] Bot eskalasi untuk ticket sensitif / butuh judgment manusia.
- [ ] Bot berhenti membalas setelah handoff ke agent.
- [ ] Bot patuh master switch dan per-outlet intent toggle.

---

## Open Questions (Resolved)

| # | Pertanyaan | Jawaban |
|---|-----------|---------|
| 1 | Format final ID ticket? | `TKOM-0004-0626` — menggunakan **ticket number**, bukan internal ID. |
| 2 | Field list ticket? | **Tidak dipakai.** List ticket di-drop dari scope. |
| 3 | Field detail ticket? | Sudah di-provide via JSON endpoint (lihat field mapping di atas). |
| 4 | Status internal? | `OPEN`, `IN_PROGRESS`, `RESOLVED` — sudah sesuai di dokumen. |
| 5 | Ticket lintas outlet / WABA? | Ticket **hanya terkait outlet**. Lookup dibatasi per outlet dari conversation context. |
| 6 | Ticket selalu terkait transaksi laundry? | **Tidak selalu.** `Transaksi terkait` hanya tampil jika ada data. |
| 7 | SLA / estimasi penyelesaian? | **Tidak perlu ditampilkan** ke customer. |
