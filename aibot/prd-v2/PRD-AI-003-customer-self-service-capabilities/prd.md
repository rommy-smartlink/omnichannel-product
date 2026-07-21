---
id: PRD-AI-003
title: Customer Self-Service Capabilities
product: Smartlink
module: AI Bot Omnichannel
version: 1.0.0
status: Draft
owner: Product Owner
reviewers:
  - UIX
  - Engineering
  - QA
created_at: 2026-07-21
updated_at: 2026-07-21
---

# PRD-AI-003 - Customer Self-Service Capabilities

## 1. Executive Summary

### Background

Setelah Bot Control Panel (PRD-AI-002) menyediakan infrastruktur konfigurasi dan Bot Runtime Behavior (PRD-AI-001) mendefinisikan aturan percakapan, bot membutuhkan kemampuan aktual yang bisa digunakan customer secara self-service. Tujuh intent MVP yang sudah didefinisikan di `aibot/docs/intents.md` perlu dispesifikasikan secara detail: apa data yang ditampilkan, bagaimana format respons, kapan eskalasi terjadi, dan aturan bisnis yang berlaku.

Customer Self-Service Capabilities adalah kumpulan 7 kemampuan intent yang bekerja di atas Bot Runtime Behavior dan dikonfigurasi melalui Bot Control Panel. Setiap intent memiliki spesifikasi respons, display rules, slot-filling flow, dan aturan eskalasi masing-masing.

### Current Condition

- Semua pertanyaan customer — dari cek status laundry sampai tanya jam buka — ditangani manual oleh agen manusia.
- Tidak ada respons otomatis untuk pertanyaan repetitif → beban agen tinggi.
- Tidak ada standarisasi format respons → kualitas jawaban agen bervariasi.
- Tidak ada klasifikasi intent otomatis → agen harus membaca dan memahami maksud customer dari nol.

### Problems

- PROB-AI-009: Pertanyaan repetitif (status laundry, status ticket, jam operasional, promo, layanan) membebani agen tanpa nilai tambah.
- PROB-AI-010: Customer harus menunggu antrean agen hanya untuk pertanyaan sederhana seperti "laundry saya sudah selesai?".
- PROB-AI-011: Tidak ada mekanisme self-service → customer tidak punya alternatif selain menunggu agen.
- PROB-AI-012: Eskalasi ke agen tidak terstruktur → customer dengan masalah sederhana dan masalah serius diperlakukan sama.

### Expected Condition

- Customer bisa cek status laundry, status ticket, jam operasional, promo, membership, layanan outlet, dan minta agen — semua lewat chat natural WhatsApp tanpa agen manusia.
- Bot merespons dengan format standar: ringkas, bahasa Indonesia, tone "Kak", data customer-friendly.
- Setiap intent memiliki aturan eskalasi yang jelas: kapan bot menyerahkan ke agen.
- Semua intent menghormati hierarki switch (Master → Outlet → Intent) dari Control Panel.
- `talk_to_agent` selalu tersedia sebagai safety net — customer tidak pernah terjebak di flow bot.

### Proposed Solution

Spesifikasi 7 intent self-service MVP yang berjalan di atas Bot Runtime Behavior:
1. **Cek Status Laundry** (`check_laundry_status`) — cari transaksi laundry berdasarkan ID.
2. **Cek Status Ticket** (`check_ticket_status`) — cari tiket berdasarkan ID (read-only).
3. **Jam Operasional** (`get_operating_hours`) — jadwal buka/tutup outlet.
4. **Hubungi Agent** (`talk_to_agent`) — eskalasi ke agen manusia tanpa syarat.
5. **Promo Aktif** (`get_active_promos`) — daftar promo yang sedang berlangsung.
6. **Info Member** (`get_memberships`) — paket membership yang tersedia.
7. **Info Layanan Outlet** (`get_outlet_services`) — layanan yang tersedia di outlet.

## 2. Global Scope

### In Scope

- Spesifikasi respons, display rules, dan slot-filling flow untuk 7 intent MVP.
- Aturan eskalasi per intent: kapan bot menyerahkan ke agen.
- Format respons standar: bahasa Indonesia, tone "Kak", WhatsApp-friendly.
- Lookup data per outlet (outlet dari conversation context).
- Patuh hierarki Master Switch → Outlet Switch → Intent Switch.
- `talk_to_agent` selalu aktif — tidak terpengaruh toggle intent.
- Command manual "menu", "batal", "hubungi agen" selalu berfungsi di semua intent.

### Out of Scope

- Intent di luar 7 MVP (pembayaran, refund, buat ticket, dll).
- Multi-language.
- Konfigurasi display rules per outlet (hardcode di MVP, configurable fase 2).
- Integrasi dengan channel selain WhatsApp.
- Notifikasi push atau proaktif dari bot.
- Context handover ke agen (fase 2).

## 3. Epic List

| Epic ID | Nama Epic | Tujuan Singkat | Status |
|---|---|---|---|
| EPIC-AI-003 | Intent Capabilities | Menyediakan 7 kemampuan self-service untuk customer melalui chat WhatsApp | Draft |

## 4. Feature List

| Feature ID | Nama Feature | Purpose | Status |
|---|---|---|---|
| FEAT-AI-005 | Cek Status Laundry | Customer mengecek status transaksi laundry berdasarkan ID transaksi | Draft |
| FEAT-AI-006 | Cek Status Ticket | Customer mengecek status ticket berdasarkan nomor ticket (read-only) | Draft |
| FEAT-AI-007 | Jam Operasional | Customer menanyakan jadwal buka/tutup outlet | Draft |
| FEAT-AI-008 | Hubungi Agent | Customer meminta koneksi langsung ke agen manusia tanpa syarat | Draft |
| FEAT-AI-009 | Promo Aktif | Customer melihat promo yang sedang berlangsung di outlet | Draft |
| FEAT-AI-010 | Info Member | Customer melihat paket membership yang tersedia di outlet | Draft |
| FEAT-AI-011 | Info Layanan Outlet | Customer menanyakan layanan yang tersedia di outlet | Draft |
| FEAT-AI-013 | Orkestrasi Self-Service | Menjaga pengumpulan input, konteks outlet, koreksi, privasi, dan hasil ambigu tetap konsisten di seluruh capability | Draft |

## 5. Global Business Rules

### BR-AI-200 — Intent Execution Gate

Setiap intent hanya dieksekusi setelah lolos 3-gate: Master Switch ON → Outlet Switch ON → Intent Switch ON. Jika salah satu gate OFF, intent tidak dieksekusi. `talk_to_agent` hanya memerlukan Gate 1 & Gate 2 — Gate 3 tidak berlaku karena `talk_to_agent` selalu ON.

### BR-AI-201 — Standard Response Tone

Semua respons bot menggunakan bahasa Indonesia, tone "Kak", format WhatsApp-friendly (tanpa markdown berat, tanpa tabel HTML). Tanggal dalam format "DD MMMM YYYY". Nominal rupiah dalam format "RpX" tanpa desimal.

### BR-AI-202 — Data Sensitivity

Bot tidak boleh menampilkan: nama karyawan/kasir, ID customer, nomor telepon, detail pajak, detail diskon, detail biaya tambahan, detail metode pembayaran, rincian pembayaran penuh, data refund, foto transaksi, catatan internal outlet.

### BR-AI-203 — Lookup Scope

Semua lookup data dibatasi outlet dari conversation context. Bot tidak mencari data lintas outlet.

### BR-AI-204 — Command Override

Command "menu", "batal", dan "hubungi agen" / "admin" / "CS" / "manusia" selalu berfungsi di tengah flow intent manapun. "Menu" mengembalikan ke daftar capability. "Batal" membatalkan proses yang sedang berjalan. "Hubungi agen" trigger intent `talk_to_agent`.

### BR-AI-205 — Retry Limit

Untuk intent dengan slot wajib (ID transaksi, ID ticket), bot meminta ulang maksimal 2× jika data tidak ditemukan. Percobaan ke-3 → eskalasi ke agen.

## 6. References

| Type | URL / Reference | Description |
|---|---|---|
| Bot Runtime Behavior | `aibot/prd-v2/PRD-AI-001-bot-runtime-behavior/prd.md` | Aturan percakapan & routing |
| Bot Control Panel | `aibot/prd-v2/PRD-AI-002-bot-control-panel/prd.md` | Konfigurasi switch & intent |
| Intents | `aibot/docs/intents.md` | Daftar intent MVP |
| Bot Behavior | `aibot/docs/bot-behavior.md` | Aturan perilaku bot & label status |
| Conversation Flow | `aibot/docs/conversation-flow.md` | Arsitektur alur percakapan |
| Existing PRDs | `aibot/prd/P1-*`, `aibot/prd/P2-*`, `aibot/prd/P3-*` | PRD intent existing |
