---
id: US-AI-053
prd: PRD-AI-003
epic: EPIC-AI-003
feature: FEAT-AI-007
title: Jam Operasional — Buka Kembali Setelah Tutup
status: Draft
priority: Medium
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-053 - Jam Operasional — Buka Kembali Setelah Tutup

## 1. Parent Reference

- PRD: PRD-AI-003 - Customer Self-Service Capabilities
- Epic: EPIC-AI-003 - Intent Capabilities
- Feature: FEAT-AI-007 - Jam Operasional

## 2. User Story

Sebagai customer,
Saya ingin tahu kapan outlet buka kembali jika saat ini tutup,
Agar saya punya arahan jelas tanpa harus menebak atau mencari informasi lain.

## 3. Goal

Saat outlet tutup (atau setelah jam tutup), customer bertanya "Buka lagi kapan?" dan bot mencari sesi berikutnya pada hari yang sama, atau hari operasional berikutnya jika semua sesi sudah lewat. Jadwal khusus diperhitungkan jika ada dalam rentang.

## 4. Acceptance Criteria

- Bot mengenali pertanyaan: "Buka lagi kapan?", "Kapan buka?", "Buka kembali jam berapa?".
- Jika masih ada sesi berikutnya di hari yang sama → tampilkan sesi tersebut.
- Jika semua sesi hari ini sudah lewat → cari hari operasional berikutnya (dengan mempertimbangkan jadwal khusus untuk hari-hari ke depan).
- Jika outlet dalam periode tutup sementara (jadwal khusus multi-hari Tutup) → cari buka pertama setelah tanggal selesai jadwal khusus.
- Format: "Outlet buka kembali [hari], [tanggal] pukul [jam] [zona]."
- Zona waktu outlet sebagai acuan.

## 5. Form Rules

> Tidak ada form UI untuk user story ini. Input diberikan customer melalui percakapan WhatsApp.

## 6. Form Notes

- Validasi input percakapan mengikuti business rule pada Feature terkait dan FEAT-AI-013.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-053-01 | Sesi 08.00–12.00, 13.00–21.00, sekarang 12.30 (istirahat) | Customer kirim "Buka lagi kapan?" | "Buka kembali pukul 13.00 WIB." |
| SC-AI-053-02 | Sekarang 21.30, sesi hari ini sudah selesai, besok Selasa buka 08.00 | Customer kirim "Buka lagi kapan?" | "Outlet buka kembali Selasa, 21 Juli 2026 pukul 08.00 WIB." |
| SC-AI-053-03 | Jadwal khusus: 20–22 Juli Tutup (libur 3 hari), sekarang 20 Juli | Customer kirim "Kapan buka lagi?" | "Outlet buka kembali Kamis, 23 Juli 2026 pukul 08.00 WIB." |

### Detail SC-AI-053-02 - Sesi Sudah Lewat

#### Given

Outlet Perkilo Premium Pejaten, zona WIB. Senin 20 Juli, jadwal 08.00–21.00. Sekarang 21.30 WIB. Selasa jadwal mingguan: 08.00–21.00.

#### When

Customer kirim "Buka lagi kapan?"

#### Then

```
Saat ini Perkilo Premium Pejaten sudah tutup ya Kak.
Outlet buka kembali Selasa, 21 Juli 2026 pukul 08.00 WIB.
```

## 8. Supporting Information

- Perilaku lintas-capability untuk konteks, koreksi input, outlet, privasi referensi, dan hasil ambigu mengikuti FEAT-AI-013.
- Effective state, fallback, timeout, dan handoff mengikuti PRD-AI-001.
