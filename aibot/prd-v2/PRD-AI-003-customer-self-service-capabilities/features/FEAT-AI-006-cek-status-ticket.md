---
id: FEAT-AI-006
prd: PRD-AI-003
epic: EPIC-AI-003
title: Cek Status Ticket
status: Draft
owner: Product Owner
created_at: 2026-07-21
updated_at: 2026-07-21
---

# FEAT-AI-006 - Cek Status Ticket

## 1. Purpose

Customer mengecek status ticket berdasarkan nomor ticket melalui percakapan natural WhatsApp. Bot mencari ticket di existing system, menampilkan status dengan label customer-friendly. Read-only: bot tidak membuat, mengubah, atau menutup ticket. Auto-eskalasi untuk ticket sensitif atau ambigu.

## 2. Scope

### In Scope

- Intent `check_ticket_status` aktif per outlet (toggleable via Control Panel, default ON).
- Slot wajib: nomor ticket. Format: `TKOM-0004-0626` (normalisasi: trim, uppercase, pertahankan dash).
- Field yang ditampilkan: ID Ticket, Outlet, Nama Customer, Topik, Ringkasan (jika aman), Kategori (label customer-friendly), Status (label customer-friendly), Dibuat (DD MMMM YYYY, HH.mm), Diperbarui (DD MMMM YYYY, HH.mm), Selesai (jika resolved), Transaksi terkait (maks 3 ID).
- Field yang dilarang: ID internal, nama staff, nomor telepon, isi pesan mentah.
- Auto-eskalasi untuk: ticket mengandung isu sensitif (refund/pembayaran/hilang/rusak/komplain/dispute), status RESOLVED tapi customer belum puas, retry 2× not found, API error/timeout/401.
- Retry 2× jika nomor ticket tidak ditemukan → eskalasi percobaan ke-3.
- Bahasa Indonesia, tone "Kak".
- Lookup dibatasi outlet conversation context.
- Command "menu"/"batal"/"hubungi agen" selalu override.

### Out of Scope

- Membuat ticket baru.
- Mengubah status ticket.
- Menutup/reopen ticket.
- Menambahkan komentar, balasan, attachment, atau catatan ticket.
- Assign/reassign agent.
- Menampilkan catatan internal, assignment history, audit log.
- Menampilkan informasi refund/pembayaran detail.
- Lookup ticket lintas outlet.
- Lookup via nomor HP/nama customer.
- SLA guarantee otomatis.
- Multi-language.

## 3. Business Rules

### BR-AI-310 — Read-Only Ticket

Bot hanya membaca dan menampilkan status ticket. Tidak boleh membuat, mengubah, menutup, atau menambah catatan pada ticket.

### BR-AI-311 — Field Sensitif Dilarang

Tidak boleh tampil: `id`, `waba_id`, `organization_id`, `contact_id`, `room_id`, `external_channel_id`, `contact_phone`, `assignee_name`, `created_by_name`, `resolved_by_name`, `employee_name`, `linked_messages`.

### BR-AI-312 — Category & Status Mapping

Kategori internal (`KENDALA`, `KOMPLAIN`, `PERTANYAAN`, `PERMINTAAN`) dipetakan ke label customer-friendly. Status ticket dipetakan ke label customer-friendly. Priority tidak ditampilkan ke customer.

### BR-AI-313 — Auto-Eskalasi Ticket Sensitif

Auto-eskalasi jika: (1) ticket mengandung isu refund/pembayaran/hilang/rusak/komplain/dispute, (2) status RESOLVED tapi customer menyatakan belum selesai, (3) retry 2× not found, (4) API error/timeout/401.

### BR-AI-314 — Retry 2× Not Found

Jika nomor ticket tidak ditemukan: tanya ulang maksimal 2×. Percobaan ke-3 → eskalasi ke agen.

### BR-AI-315 — Ringkasan Tidak Aman

Jika `summary` mengandung kata kasar, data PII, atau konten sensitif → ganti dengan "Detail laporan sedang dicek agent."

## 4. Dependencies

- Bot Runtime Behavior (PRD-AI-001): effective state 3-gate, fallback.
- Bot Control Panel (PRD-AI-002): toggle `check_ticket_status` per outlet.
- API: Detail ticket (by nomor ticket, scoped outlet).
- Conversation context: outlet ID.

## 5. User Story List

| US ID | Judul US | Goal | Status |
|---|---|---|---|
| US-AI-047 | Cek Status Ticket — Lihat Status | Customer mengirim nomor ticket dan mendapat status ticket dengan label customer-friendly | Draft |
| US-AI-048 | Cek Status Ticket — Auto-Eskalasi Sensitif | Bot otomatis mengeskalasi ticket sensitif ke agen tanpa menampilkan data internal | Draft |
| US-AI-049 | Cek Status Ticket — Nomor Tidak Ditemukan | Bot meminta ulang nomor ticket maksimal 2×, eskalasi pada percobaan ke-3 | Draft |

## 6. Notes for Developer

- Format nomor ticket: bebas, normalisasi trim + uppercase.
- Linked messages tidak ditampilkan sama sekali.
- `notas[].external_id` ditampilkan sebagai "Transaksi terkait" maks 3 item, hanya ID transaksi.
- Jika ringkasan tidak aman → ganti dengan teks statis "Detail laporan sedang dicek agent."
