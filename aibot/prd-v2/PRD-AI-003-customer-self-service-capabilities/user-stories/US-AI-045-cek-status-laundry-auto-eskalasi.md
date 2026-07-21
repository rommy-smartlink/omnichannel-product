---
id: US-AI-045
prd: PRD-AI-003
epic: EPIC-AI-003
feature: FEAT-AI-005
title: Cek Status Laundry — Auto-Eskalasi Kondisi Sensitif
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-045 - Cek Status Laundry — Auto-Eskalasi Kondisi Sensitif

## 1. Parent Reference

- PRD: PRD-AI-003 - Customer Self-Service Capabilities
- Epic: EPIC-AI-003 - Intent Capabilities
- Feature: FEAT-AI-005 - Cek Status Laundry

## 2. User Story

Sebagai bot engine,
Saya perlu otomatis mengeskalasi transaksi laundry ke agen jika terdeteksi kondisi sensitif,
Agar customer dengan masalah serius langsung ditangani manusia tanpa melihat data yang mungkin membingungkan.

## 3. Goal

Bot mendeteksi 5 kondisi sensitif dan otomatis mengeskalasi ke agen dengan pesan yang sopan, tanpa menampilkan data transaksi sensitif ke customer.

## 4. Acceptance Criteria

- 5 kondisi auto-eskalasi dikenali:
  1. Transaksi dihapus (`status_hapus = 1`).
  2. Ada permintaan hapus transaksi (`idpermintaan_hapus` tidak null).
  3. Ada data refund (`data_refund` tidak kosong).
  4. Pengantaran gagal.
  5. Catatan mengandung keyword "komplain", "hilang", atau "rusak".
- Untuk kondisi 1–4: transaksi ditemukan, tapi langsung eskalasi tanpa tampilkan Mode Ringkas.
- Untuk kondisi 5: catatan internal mengandung keyword → eskalasi.
- Pesan eskalasi: "Saya menemukan transaksi Kakak, tetapi ada kondisi yang perlu dicek lebih lanjut oleh agent. Saya hubungkan Kakak ke agent outlet [Nama Outlet] ya. Mohon tunggu sebentar."
- Setelah pesan eskalasi, customer dihubungkan ke agen.
- Bot tidak intervensi lagi di conversation tersebut.
- Data sensitif tidak ditampilkan dalam pesan eskalasi.

## 5. Form Rules

> Tidak ada form UI untuk user story ini. Input diberikan customer melalui percakapan WhatsApp.

## 6. Form Notes

- Validasi input percakapan mengikuti business rule pada Feature terkait dan FEAT-AI-013.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-045-01 | Transaksi TMD001 normal, status "Washing" | Customer kirim "Cek TMD001" | Bot tampilkan Mode Ringkas normal (tidak eskalasi). |
| SC-AI-045-02 | Transaksi TMD002 ada refund pending | Customer kirim "Cek TMD002" | Bot tampilkan pesan eskalasi, hubungkan ke agen. |

## 8. Supporting Information

- Perilaku lintas-capability untuk konteks, koreksi input, outlet, privasi referensi, dan hasil ambigu mengikuti FEAT-AI-013.
- Effective state, fallback, timeout, dan handoff mengikuti PRD-AI-001.
