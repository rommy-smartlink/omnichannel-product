---
id: US-AI-047
prd: PRD-AI-003
epic: EPIC-AI-003
feature: FEAT-AI-006
title: Cek Status Ticket — Lihat Status
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-047 - Cek Status Ticket — Lihat Status

## 1. Parent Reference

- PRD: PRD-AI-003 - Customer Self-Service Capabilities
- Epic: EPIC-AI-003 - Intent Capabilities
- Feature: FEAT-AI-006 - Cek Status Ticket

## 2. User Story

Sebagai customer,
Saya ingin mengecek status ticket dengan mengirim nomor ticket dalam bahasa natural,
Agar saya tahu perkembangan ticket tanpa harus menunggu agent.

## 3. Goal

Customer mengirim nomor ticket dan bot menampilkan detail ticket dengan label customer-friendly: ID ticket, outlet, nama, topik, ringkasan, kategori, status, tanggal dibuat/diperbarui/selesai, dan transaksi terkait (jika ada). Field sensitif (nama staff, PII, ID internal) tidak ditampilkan.

## 4. Acceptance Criteria

- Bot mengenali intent `check_ticket_status` dari pesan natural customer.
- Jika nomor ticket belum ada → bot meminta: "Baik Kak, boleh kirim nomor ticket-nya?"
- Nomor ticket dinormalisasi: trim, uppercase.
- Jika ticket ditemukan dan aman → tampilkan detail ticket (field sesuai BR-AI-311 & BR-AI-312).
- Kategori dipetakan ke label customer-friendly: KENDALA→"Kendala", KOMPLAIN→"Komplain", PERTANYAAN→"Pertanyaan", PERMINTAAN→"Permintaan bantuan".
- Status dipetakan ke label customer-friendly.
- Priority tidak ditampilkan ke customer.
- `notas[].external_id` ditampilkan sebagai "Transaksi terkait" maks 3 item.
- Lookup dibatasi outlet conversation context.
- Read-only: tidak membuat/mengubah ticket.

## 5. Form Rules

> Tidak ada form UI untuk user story ini. Input diberikan customer melalui percakapan WhatsApp.

## 6. Form Notes

- Validasi input percakapan mengikuti business rule pada Feature terkait dan FEAT-AI-013.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-047-01 | Ticket TKOM-0004-0626 ada, kategori PERTANYAAN, status OPEN | Customer kirim "Cek tiket TKOM-0004-0626" | Bot tampilkan detail ticket dengan label customer-friendly. |
| SC-AI-047-02 | Customer belum kirim nomor ticket | Customer kirim "Mau cek tiket" | Bot minta nomor ticket. |
| SC-AI-047-03 | Ticket punya `summary` aman | Ticket ditemukan dengan summary normal | Bot tampilkan summary sebagaimana adanya. |

### Detail SC-AI-047-01 - Ticket Ditemukan

#### Given

Customer di outlet Perkilo Premium Pejaten. Ticket TKOM-0004-0626: topik "Laundry belum selesai", kategori PERTANYAAN, status OPEN, dibuat 13 Juli 2026, diperbarui 14 Juli 2026.

#### When

Customer kirim "Cek status ticket TKOM-0004-0626"

#### Then

Bot merespons:

```
Baik Kak, berikut status ticket Kakak:

ID Ticket: TKOM-0004-0626
Outlet: Perkilo Premium Pejaten
Nama: Budi
Topik: Laundry belum selesai
Kategori: Pertanyaan
Status: Sedang diproses
Dibuat: 13 Juli 2026, 10.30
Diperbarui: 14 Juli 2026, 15.00
```

## 8. Supporting Information

- Perilaku lintas-capability untuk konteks, koreksi input, outlet, privasi referensi, dan hasil ambigu mengikuti FEAT-AI-013.
- Effective state, fallback, timeout, dan handoff mengikuti PRD-AI-001.
