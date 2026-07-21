---
id: US-AI-050
prd: PRD-AI-003
epic: EPIC-AI-003
feature: FEAT-AI-007
title: Jam Operasional — Jadwal Hari Ini
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-050 - Jam Operasional — Jadwal Hari Ini

## 1. Parent Reference

- PRD: PRD-AI-003 - Customer Self-Service Capabilities
- Epic: EPIC-AI-003 - Intent Capabilities
- Feature: FEAT-AI-007 - Jam Operasional

## 2. User Story

Sebagai customer,
Saya ingin menanyakan jam operasional outlet hari ini dalam bahasa natural,
Agar saya tahu jam buka dan tutup outlet tanpa harus mencari informasi manual.

## 3. Goal

Customer bertanya jadwal hari ini dan bot menampilkan sesi buka/tutup berdasarkan jadwal yang berlaku (jadwal khusus diprioritaskan), dengan zona waktu outlet.

## 4. Acceptance Criteria

- Bot mengenali pertanyaan jadwal hari ini: "Buka jam berapa?", "Tutup jam berapa?", "Jam operasionalnya bagaimana?".
- Hari ini dihitung berdasarkan zona waktu outlet (BR-AI-320).
- Jika ada jadwal khusus untuk tanggal hari ini → gunakan jadwal khusus (BR-AI-321).
- Jika tidak ada jadwal khusus → gunakan jadwal mingguan.
- Jika hari ini "Tutup" → "Hari ini [Nama Outlet] tutup ya Kak. Outlet buka kembali [hari], [tanggal] pukul [jam] [zona waktu]."
- Jika ada dua sesi → tampilkan kedua sesi.
- Jika 24 Jam → "Hari ini buka 24 jam ya Kak."
- Format: "Jam operasional [Nama Outlet] hari ini, [hari] [tanggal]: [sesi-sesi]. Zona waktu: [zona]."
- Jika data tidak tersedia → fallback tawarkan agent (BR-AI-323).

## 5. Form Rules

> Tidak ada form UI untuk user story ini. Input diberikan customer melalui percakapan WhatsApp.

## 6. Form Notes

- Validasi input percakapan mengikuti business rule pada Feature terkait dan FEAT-AI-013.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-050-01 | Jadwal mingguan Senin: 08.00–21.00, hari ini Senin | Customer kirim "Buka jam berapa?" | Bot tampilkan "Jam operasional Perkilo Premium Pejaten hari ini, Senin 20 Juli 2026: 08.00–21.00. Zona waktu: WIB." |
| SC-AI-050-02 | Jadwal mingguan Minggu: Tutup, hari ini Minggu | Customer kirim "Jam operasional?" | Bot tampilkan "Hari ini Perkilo Premium Pejaten tutup ya Kak. Outlet buka kembali Senin, 21 Juli 2026 pukul 08.00 WIB." |
| SC-AI-050-03 | Jadwal khusus 20 Juli: Tutup (libur), jadwal mingguan Senin: 08.00–21.00 | Customer kirim "Buka?" di tgl 20 Juli | Bot gunakan jadwal khusus → "Hari ini Perkilo Premium Pejaten tutup ya Kak." |

### Detail SC-AI-050-01 - Jadwal Normal

#### Given

Customer di outlet Perkilo Premium Pejaten. Zona waktu: WIB. Hari ini Senin 20 Juli 2026. Jadwal mingguan Senin: 08.00–21.00.

#### When

Customer kirim "Buka jam berapa?"

#### Then

```
Jam operasional Perkilo Premium Pejaten hari ini, Senin 20 Juli 2026:
08.00–21.00

Zona waktu: WIB.
```

## 8. Supporting Information

- Perilaku lintas-capability untuk konteks, koreksi input, outlet, privasi referensi, dan hasil ambigu mengikuti FEAT-AI-013.
- Effective state, fallback, timeout, dan handoff mengikuti PRD-AI-001.
