---
id: US-AI-052
prd: PRD-AI-003
epic: EPIC-AI-003
feature: FEAT-AI-007
title: Jam Operasional — Jadwal Hari/Tanggal Tertentu
status: Draft
priority: Medium
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-052 - Jam Operasional — Jadwal Hari/Tanggal Tertentu

## 1. Parent Reference

- PRD: PRD-AI-003 - Customer Self-Service Capabilities
- Epic: EPIC-AI-003 - Intent Capabilities
- Feature: FEAT-AI-007 - Jam Operasional

## 2. User Story

Sebagai customer,
Saya ingin menanyakan jadwal outlet pada hari atau tanggal tertentu,
Agar saya bisa merencanakan kunjungan di hari yang saya inginkan.

## 3. Goal

Customer menyebut hari atau tanggal spesifik dan bot menampilkan jadwal yang berlaku untuk hari/tanggal tersebut, dengan jadwal khusus diprioritaskan.

## 4. Acceptance Criteria

- Bot mengenali pertanyaan dengan hari: "Hari Minggu buka?", "Setiap Sabtu buka jam berapa?", "Besok buka?".
- Bot mengenali pertanyaan dengan tanggal: "Tanggal 17 Agustus buka?", "20 Juli 2026 buka jam berapa?".
- "Besok" dihitung berdasarkan zona waktu outlet.
- Jika customer menyebut "setiap [hari]" → gunakan jadwal mingguan reguler.
- Jika customer menyebut tanggal spesifik → cek jadwal khusus dulu, fallback ke jadwal mingguan.
- Ambiguasi tanggal tanpa tahun (BR-AI-324): gunakan kejadian terdekat yang akan datang + sebut tanggal lengkap.
- Respons format: "[hari], [tanggal]: [sesi]." atau "Tutup" jika tidak buka.

## 5. Form Rules

> Tidak ada form UI untuk user story ini. Input diberikan customer melalui percakapan WhatsApp.

## 6. Form Notes

- Validasi input percakapan mengikuti business rule pada Feature terkait dan FEAT-AI-013.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-052-01 | Jadwal mingguan Minggu: 09.00–18.00, sekarang Juli | Customer kirim "Hari Minggu buka?" | Bot tampilkan jadwal mingguan: "Minggu buka pukul 09.00–18.00 WIB ya Kak." |
| SC-AI-052-02 | Jadwal khusus 17 Agustus: Tutup (libur nasional) | Customer kirim "Tanggal 17 Agustus buka?" | Bot: "Tanggal 17 Agustus 2026 Perkilo Premium Pejaten tutup ya Kak." |
| SC-AI-052-03 | Sekarang 15 Juli, customer tanya "Tanggal 10 Juli buka?" | Customer tanya tanggal sudah lewat | Bot gunakan 10 Juli tahun depan: "Tanggal 10 Juli 2027..." dan sebut tanggal lengkap. |
| SC-AI-052-04 | Jadwal mingguan Sabtu: 08.00–21.00 | Customer kirim "Setiap Sabtu buka jam berapa?" | Bot tampilkan: "Sabtu buka pukul 08.00–21.00 WIB." |

### Detail SC-AI-052-02 - Jadwal Khusus Libur

#### Given

Outlet Perkilo Premium Pejaten. Zona WIB. Jadwal khusus: 17 Agustus 2026 status Tutup.

#### When

Customer kirim "Tanggal 17 Agustus buka?"

#### Then

```
Tanggal 17 Agustus 2026 Perkilo Premium Pejaten tutup ya Kak.
Outlet buka kembali Senin, 18 Agustus 2026 pukul 08.00 WIB.
```

## 8. Supporting Information

- Perilaku lintas-capability untuk konteks, koreksi input, outlet, privasi referensi, dan hasil ambigu mengikuti FEAT-AI-013.
- Effective state, fallback, timeout, dan handoff mengikuti PRD-AI-001.
