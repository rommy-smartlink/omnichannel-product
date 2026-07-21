---
id: US-AI-054
prd: PRD-AI-003
epic: EPIC-AI-003
feature: FEAT-AI-007
title: Jam Operasional — Prioritas Jadwal Khusus
status: Draft
priority: Medium
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-054 - Jam Operasional — Prioritas Jadwal Khusus

## 1. Parent Reference

- PRD: PRD-AI-003 - Customer Self-Service Capabilities
- Epic: EPIC-AI-003 - Intent Capabilities
- Feature: FEAT-AI-007 - Jam Operasional

## 2. User Story

Sebagai bot engine,
Saya perlu selalu memprioritaskan jadwal khusus di atas jadwal mingguan untuk tanggal yang sama,
Agar customer mendapat informasi akurat saat ada libur nasional, tanggal spesial, atau perubahan jadwal temporer.

## 3. Goal

Bot selalu mengecek jadwal khusus terlebih dahulu untuk setiap tanggal referensi. Jika ditemukan jadwal khusus yang mencakup tanggal tersebut, jadwal mingguan diabaikan. Bot tidak menggabungkan keduanya.

## 4. Acceptance Criteria

- Setiap kali mengevaluasi jadwal untuk suatu tanggal, urutan prioritas: (1) jadwal khusus, (2) jadwal mingguan, (3) fallback data tidak tersedia.
- Jadwal khusus bisa berupa tanggal tunggal (tanggal mulai = tanggal selesai) atau rentang tanggal.
- Jika jadwal khusus berlaku → jadwal mingguan untuk hari tersebut diabaikan.
- Jika jadwal khusus status "Tutup" selama rentang multi-hari → bot tidak menampilkan jadwal mingguan untuk hari-hari dalam rentang.
- Jika customer bertanya "setiap [hari]" → bot menggunakan jadwal mingguan (bukan jadwal khusus).
- Bot tidak menggabungkan data dari jadwal khusus dan jadwal mingguan.

## 5. Form Rules

> Tidak ada form UI untuk user story ini. Input diberikan customer melalui percakapan WhatsApp.

## 6. Form Notes

- Validasi input percakapan mengikuti business rule pada Feature terkait dan FEAT-AI-013.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-054-01 | Jadwal khusus 20 Juli: Buka 10.00–15.00. Jadwal mingguan Senin: 08.00–21.00 | Customer tanya "Senin 20 Juli buka?" | Bot tampilkan jadwal khusus: "Senin, 20 Juli 2026: 10.00–15.00 WIB." |
| SC-AI-054-02 | Jadwal khusus 20–25 Juli: Tutup. Jadwal mingguan Senin–Jumat: 08.00–21.00 | Customer tanya "22 Juli buka?" | Bot tampilkan: "22 Juli 2026 Perkilo Premium Pejaten tutup. Buka kembali 26 Juli." |
| SC-AI-054-03 | Jadwal khusus 17 Agustus: Tutup | Customer tanya "Setiap Agustus buka?" | Bot gunakan jadwal mingguan untuk hari-hari Agustus (jadwal khusus hanya 17 Agustus). |

### Detail SC-AI-054-01 - Jadwal Khusus Prioritas

#### Given

Outlet Perkilo Premium Pejaten. Zona WIB. Jadwal mingguan: Senin 08.00–21.00. Jadwal khusus: 20 Juli 2026, Buka 10.00–15.00 (event spesial).

#### When

Customer kirim "Senin 20 Juli buka jam berapa?"

#### Then

```
Senin, 20 Juli 2026:
10.00–15.00 WIB
```

## 8. Supporting Information

- Perilaku lintas-capability untuk konteks, koreksi input, outlet, privasi referensi, dan hasil ambigu mengikuti FEAT-AI-013.
- Effective state, fallback, timeout, dan handoff mengikuti PRD-AI-001.
