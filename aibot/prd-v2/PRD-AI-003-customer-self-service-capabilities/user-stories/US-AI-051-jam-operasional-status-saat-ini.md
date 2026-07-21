---
id: US-AI-051
prd: PRD-AI-003
epic: EPIC-AI-003
feature: FEAT-AI-007
title: Jam Operasional — Status Buka/Tutup Saat Ini
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-051 - Jam Operasional — Status Buka/Tutup Saat Ini

## 1. Parent Reference

- PRD: PRD-AI-003 - Customer Self-Service Capabilities
- Epic: EPIC-AI-003 - Intent Capabilities
- Feature: FEAT-AI-007 - Jam Operasional

## 2. User Story

Sebagai customer,
Saya ingin tahu apakah outlet sedang buka saat ini,
Agar saya tidak datang saat outlet tutup atau sedang istirahat.

## 3. Goal

Customer bertanya status buka/tutup saat ini dan bot mengevaluasi berdasarkan waktu lokal outlet terhadap jadwal yang berlaku, lalu memberi tahu status + jam tutup (jika buka) atau jam buka berikutnya (jika tutup/istirahat).

## 4. Acceptance Criteria

- Bot mengenali pertanyaan status: "Sekarang masih buka?", "Buka sekarang?", "Apa outlet sedang buka?".
- Waktu "sekarang" = waktu lokal outlet saat pesan diterima (BR-AI-320).
- Jika dalam sesi buka → "Saat ini [Nama Outlet] masih buka ya Kak. Outlet tutup pukul [jam] [zona]."
- Jika di antara dua sesi → "Saat ini [Nama Outlet] sedang istirahat ya Kak. Buka kembali pukul [jam] [zona]." (BR-AI-325).
- Jika sebelum sesi pertama → "Saat ini [Nama Outlet] masih tutup ya Kak. Outlet buka pukul [jam] [zona]."
- Jika setelah sesi terakhir → "Saat ini [Nama Outlet] sudah tutup ya Kak. Outlet buka kembali [hari], [tanggal] pukul [jam] [zona]."
- Jika jadwal melewati tengah malam (22.00–01.00) dan sekarang 23.00 → "Saat ini [Nama Outlet] masih buka ya Kak. Outlet tutup pukul 01.00 WIB."
- Jika 24 jam → "Saat ini [Nama Outlet] buka 24 jam ya Kak."

## 5. Form Rules

> Tidak ada form UI untuk user story ini. Input diberikan customer melalui percakapan WhatsApp.

## 6. Form Notes

- Validasi input percakapan mengikuti business rule pada Feature terkait dan FEAT-AI-013.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-051-01 | Outlet buka 08.00–21.00, sekarang 14.00 WIB | Customer kirim "Masih buka?" | "Saat ini Perkilo Premium Pejaten masih buka ya Kak. Outlet tutup pukul 21.00 WIB." |
| SC-AI-051-02 | Outlet sesi 08.00–12.00 dan 13.00–21.00, sekarang 12.30 WIB | Customer kirim "Buka?" | "Saat ini Perkilo Premium Pejaten sedang istirahat ya Kak. Buka kembali pukul 13.00 WIB." |
| SC-AI-051-03 | Outlet tutup di hari ini, sekarang 10.00 WIB | Customer kirim "Sekarang buka?" | "Hari ini Perkilo Premium Pejaten tutup ya Kak. Outlet buka kembali Selasa, 21 Juli 2026 pukul 08.00 WIB." |
| SC-AI-051-04 | Outlet sesi 22.00–02.00, sekarang 23.30 WIB | Customer kirim "Masih buka?" | "Saat ini Perkilo Premium Pejaten masih buka ya Kak. Outlet tutup pukul 02.00 WIB." |

### Detail SC-AI-051-01 - Sedang Buka

#### Given

Outlet Perkilo Premium Pejaten, zona WIB, Senin. Jadwal: 08.00–21.00. Sekarang pukul 14.00 WIB.

#### When

Customer kirim "Masih buka?"

#### Then

```
Saat ini Perkilo Premium Pejaten masih buka ya Kak.
Outlet tutup pukul 21.00 WIB.
```

## 8. Supporting Information

- Perilaku lintas-capability untuk konteks, koreksi input, outlet, privasi referensi, dan hasil ambigu mengikuti FEAT-AI-013.
- Effective state, fallback, timeout, dan handoff mengikuti PRD-AI-001.
