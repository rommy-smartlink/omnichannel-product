---
id: FEAT-AI-005
prd: PRD-AI-003
epic: EPIC-AI-003
title: Cek Status Laundry
status: Draft
owner: Product Owner
created_at: 2026-07-21
updated_at: 2026-07-21
---

# FEAT-AI-005 - Cek Status Laundry

## 1. Purpose

Customer mengecek status transaksi laundry berdasarkan ID transaksi melalui percakapan natural WhatsApp. Bot mencari transaksi di outlet yang dipilih customer, lalu menampilkan status dengan label customer-friendly. Tersedia mode ringkas (default), mode detail ringan (on-demand), dan auto-eskalasi untuk kondisi sensitif.

## 2. Scope

### In Scope

- Intent `check_laundry_status` aktif per outlet (toggleable via Control Panel, default ON).
- Slot wajib: ID transaksi. Format: prefix 3 huruf + digit, contoh `TMD260624160854112`.
- Mode Ringkas (default): ID, outlet, nama, status, progress, diterima, estimasi selesai, total, arahan (≤ 8 baris + Total + Arahan).
- Mode Detail Ringan (on-demand via "detail"/"info lengkap"/"rincian"): tambah layanan + status ambil + pengantaran.
- Auto-eskalasi untuk 5 kondisi sensitif (dihapus, permintaan hapus, refund pending/diproses, pengantaran gagal, keyword komplain/hilang/rusak).
- Retry 2× jika ID tidak ditemukan → eskalasi percobaan ke-3.
- Label status customer-friendly (mapping dari `aibot/docs/bot-behavior.md`).
- Bahasa Indonesia, tone "Kak", format tanggal "DD MMMM YYYY".
- Lookup dibatasi outlet conversation context.
- Command "menu"/"batal"/"hubungi agen" selalu override.

### Out of Scope

- Lookup via nomor HP.
- Tampilkan rincian metode bayar / nominal per metode / data refund / foto transaksi.
- Tampilkan alamat pengantaran lengkap.
- Buat/edit transaksi, refund processing, notifikasi push.
- Multi-language.
- Konfigurasi 6 kondisi eskalasi per outlet (hardcode MVP, configurable fase 2).
- Konfigurasi retry count per outlet (hardcode 2×).
- Tampilkan poin member.

## 3. Business Rules

### BR-AI-300 — Default Mode Ringkas

Response default menampilkan Mode Ringkas: ID Transaksi, Outlet, Nama Customer, Status (label customer-friendly), Progress (%), Tanggal Diterima, Estimasi Selesai, Total + status bayar (Lunas/Belum lunas), dan Arahan berikutnya.

### BR-AI-301 — Detail Ringan On-Demand

Trigger: "detail", "info lengkap", "lebih lengkap", "rincian". Menambahkan daftar layanan (nama layanan, jumlah, satuan), status ambil, dan status pengantaran. Hanya 1× render per ID; reset ke ringkas di ID berikutnya.

### BR-AI-302 — Field Sensitif Dilarang

Tidak boleh tampil: nama karyawan/kasir, ID customer, nomor telepon, detail pajak, detail diskon, detail biaya tambahan, detail metode pembayaran, rincian pembayaran penuh, data refund, foto transaksi, foto bukti ambil, catatan internal outlet, data pengiriman mentah.

### BR-AI-303 — Auto-Eskalasi 5 Kondisi

Picu otomatis tanpa interaksi customer jika: (1) transaksi dihapus, (2) ada permintaan hapus transaksi, (3) refund pending/diproses, (4) pengantaran gagal, (5) catatan mengandung keyword "komplain"/"hilang"/"rusak".

### BR-AI-304 — Retry 2× Not Found

Jika ID transaksi tidak ditemukan: tanya ulang maksimal 2×. Percobaan ke-3 → eskalasi ke agen.

### BR-AI-305 — Status Label Mapping

Status internal wajib dipetakan ke label customer-friendly sesuai `aibot/docs/bot-behavior.md`. Tidak boleh tampilkan kode internal.

## 4. Dependencies

- Bot Runtime Behavior (PRD-AI-001): effective state 3-gate, greeting, fallback.
- Bot Control Panel (PRD-AI-002): toggle `check_laundry_status` per outlet.
- API: Detail transaksi laundry (by ID transaksi, scoped outlet).
- Conversation context: outlet ID dari sesi customer.

## 5. User Story List

| US ID | Judul US | Goal | Status |
|---|---|---|---|
| US-AI-043 | Cek Status Laundry — Mode Ringkas | Customer mengirim ID transaksi dan mendapat status ringkas dengan label customer-friendly | Draft |
| US-AI-044 | Cek Status Laundry — Mode Detail Ringan | Customer meminta detail transaksi dan mendapat informasi layanan, status ambil, dan pengantaran | Draft |
| US-AI-045 | Cek Status Laundry — Auto-Eskalasi Kondisi Sensitif | Bot otomatis mengeskalasi ke agen untuk 5 kondisi sensitif tanpa menampilkan data sensitif | Draft |
| US-AI-046 | Cek Status Laundry — ID Tidak Ditemukan | Bot meminta ulang ID maksimal 2×, eskalasi pada percobaan ke-3 | Draft |

## 6. Notes for Developer

- Format ID transaksi: prefix 3 huruf + digit. Contoh `TMD260624160854112`.
- Progress dalam persen, bulatkan.
- Total tampil dengan label "Lunas" / "Belum lunas" — tanpa nominal per metode bayar.
- Detail ringan hanya 1× per sesi ID. Request "detail" kedua → "Detail sudah saya tampilkan di atas ya Kak."
- Field opsional yang tidak tersedia di API → skip, jangan tampil "null" atau "-".
