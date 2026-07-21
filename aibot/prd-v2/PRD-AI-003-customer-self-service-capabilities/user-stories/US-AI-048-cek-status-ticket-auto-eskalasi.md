---
id: US-AI-048
prd: PRD-AI-003
epic: EPIC-AI-003
feature: FEAT-AI-006
title: Cek Status Ticket — Auto-Eskalasi Sensitif
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-048 - Cek Status Ticket — Auto-Eskalasi Sensitif

## 1. Parent Reference

- PRD: PRD-AI-003 - Customer Self-Service Capabilities
- Epic: EPIC-AI-003 - Intent Capabilities
- Feature: FEAT-AI-006 - Cek Status Ticket

## 2. User Story

Sebagai bot engine,
Saya perlu otomatis mengeskalasi ticket ke agen jika terdeteksi isu sensitif atau kondisi yang butuh judgment manusia,
Agar customer dengan masalah serius tidak mendapat jawaban bot yang tidak memadai.

## 3. Goal

Bot mendeteksi kondisi auto-eskalasi pada ticket dan langsung mengarahkan ke agen dengan pesan aman, tanpa menampilkan data yang berpotensi memicu kebingungan atau eskalasi berantai.

## 4. Acceptance Criteria

- 5 kondisi auto-eskalasi:
  1. Ticket mengandung isu sensitif: refund, pembayaran, hilang, rusak, komplain berat, dispute.
  2. Ticket status RESOLVED tapi customer menyatakan masalah belum selesai.
  3. `summary` mengandung kata kasar atau data PII.
  4. API error, timeout > 10 detik, atau 401 unauthorized.
  5. Ticket mengandung data yang tidak lengkap sehingga status tidak bisa dipastikan.
- Pesan eskalasi: "Saya menemukan ticket Kakak, tetapi ada kondisi yang perlu dicek lebih lanjut oleh agent. Saya hubungkan Kakak ke agent outlet [Nama Outlet] ya. Mohon tunggu sebentar."
- Untuk kondisi 3 (summary sensitif): ganti summary dengan "Detail laporan sedang dicek agent."
- Bot tidak menampilkan field sensitif dalam pesan eskalasi.
- Setelah eskalasi, bot silent.

## 5. Form Rules

> Tidak ada form UI untuk user story ini. Input diberikan customer melalui percakapan WhatsApp.

## 6. Form Notes

- Validasi input percakapan mengikuti business rule pada Feature terkait dan FEAT-AI-013.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-048-01 | Ticket kategori KOMPLAIN, topik "refund" | Customer kirim "Cek TKOM-0010" | Bot deteksi isu refund → eskalasi. |
| SC-AI-048-02 | Ticket status RESOLVED, customer kirim "Masalah belum selesai nih" | Customer kirim pesan lanjutan | Bot deteksi ketidakpuasan → eskalasi. |
| SC-AI-048-03 | Ticket summary mengandung kata kasar | Customer kirim "Cek TKOM-0020" | Bot ganti summary dengan teks aman + eskalasi. |
| SC-AI-048-04 | API detail ticket return 500 | Customer kirim "Cek TKOM-0030" | Bot tampil pesan sistem gangguan + eskalasi. |

### Detail SC-AI-048-01 - Isu Refund

#### Given

Ticket TKOM-0010: topik "Refund transaksi", kategori KOMPLAIN.

#### When

Customer kirim "Cek tiket TKOM-0010"

#### Then

Bot merespons:

```
Saya menemukan ticket Kakak, tetapi ada kondisi yang perlu dicek lebih lanjut oleh agent.
Saya hubungkan Kakak ke agent outlet Perkilo Premium Pejaten ya.
Mohon tunggu sebentar.
```

## 8. Supporting Information

- Perilaku lintas-capability untuk konteks, koreksi input, outlet, privasi referensi, dan hasil ambigu mengikuti FEAT-AI-013.
- Effective state, fallback, timeout, dan handoff mengikuti PRD-AI-001.
