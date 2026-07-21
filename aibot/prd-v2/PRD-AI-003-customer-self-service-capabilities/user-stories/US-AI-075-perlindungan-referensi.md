---
id: US-AI-075
prd: PRD-AI-003
epic: EPIC-AI-003
feature: FEAT-AI-013
title: Melindungi Referensi Transaksi dan Ticket
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-075 - Melindungi Referensi Transaksi dan Ticket

## 1. Parent Reference

- PRD: PRD-AI-003 - Customer Self-Service Capabilities
- Epic: EPIC-AI-003 - Intent Capabilities
- Feature: FEAT-AI-013 - Orkestrasi Self-Service

## 2. User Story

Sebagai customer,  
Saya ingin bot hanya menampilkan informasi untuk referensi lengkap yang saya berikan,  
Agar data transaksi atau ticket lain tidak terungkap melalui tebakan maupun saran otomatis.

## 3. Goal

Bot menerapkan respons aman dan konsisten untuk ID transaksi serta nomor ticket tanpa mengonfirmasi keberadaan data melalui referensi parsial.

## 4. Acceptance Criteria

- Bot tidak melakukan autocomplete untuk ID transaksi atau nomor ticket.
- Bot tidak menampilkan daftar referensi yang mirip ketika input tidak ditemukan.
- Referensi parsial diperlakukan sebagai format belum lengkap, bukan data tidak ditemukan.
- Respons not-found tidak mengungkap apakah bagian tertentu dari referensi cocok.
- Referensi dari sesi bot lain tidak dapat digunakan sebagai konteks.
- Bot hanya menampilkan field yang diizinkan oleh feature capability terkait.
- Permintaan untuk melihat semua transaksi atau ticket customer diarahkan ke agent karena tidak termasuk MVP.

## 5. Form Rules

> Mengikuti field `transaction_id` dan `ticket_id` pada US-AI-072.

## 6. Form Notes

- Bot tidak menyebut contoh berdasarkan data nyata milik customer.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-075-01 | Customer mengirim ID transaksi parsial | Bot memvalidasi format | Bot meminta ID lengkap tanpa menampilkan kandidat |
| SC-AI-075-02 | ID berformat valid tidak ditemukan | Pencarian selesai | Bot menampilkan not-found generik sesuai capability |
| SC-AI-075-03 | Customer meminta daftar seluruh ticket miliknya | Bot mengenali permintaan di luar scope | Bot menawarkan handoff ke agent |
| SC-AI-075-04 | Customer menanyakan apakah ID tertentu milik orang lain | Bot menerima permintaan | Bot tidak mengonfirmasi kepemilikan atau keberadaan data |

### Detail SC-AI-075-01 - Referensi Parsial

#### Given

Flow cek status laundry aktif dan format ID transaksi memerlukan referensi lengkap.

#### When

Customer hanya mengirim sebagian karakter ID.

#### Then

Bot tidak menjalankan pencarian dan tidak menawarkan daftar ID yang mirip.

#### Error Message

> ID transaksinya belum lengkap, Kak. Silakan kirim ID lengkap seperti yang tertera pada nota.

## 8. Supporting Information

- Field sensitif tetap mengikuti BR-AI-302 dan BR-AI-311.
