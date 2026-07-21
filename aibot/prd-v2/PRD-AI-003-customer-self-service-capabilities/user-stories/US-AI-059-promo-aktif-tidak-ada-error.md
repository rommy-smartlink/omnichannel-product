---
id: US-AI-059
prd: PRD-AI-003
epic: EPIC-AI-003
feature: FEAT-AI-009
title: Promo Aktif — Tidak Ada Promo / Error
status: Draft
priority: Medium
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-059 - Promo Aktif — Tidak Ada Promo / Error

## 1. Parent Reference

- PRD: PRD-AI-003 - Customer Self-Service Capabilities
- Epic: EPIC-AI-003 - Intent Capabilities
- Feature: FEAT-AI-009 - Promo Aktif

## 2. User Story

Sebagai bot engine,
Saya perlu memberi fallback yang jelas saat tidak ada promo aktif atau sistem error,
Agar customer tidak bingung atau mendapat informasi kosong.

## 3. Goal

Bot menangani dua kondisi fallback: (1) data promo kosong → pesan informatif tanpa eskalasi, (2) API error/timeout → pesan gangguan + eskalasi ke agen.

## 4. Acceptance Criteria

- Data promo kosong (tidak ada promo aktif) → "Saat ini belum ada promo aktif di [Nama Outlet]." Tidak eskalasi.
- API error (500, timeout, 401) → "Mohon maaf, sistem sedang gangguan. Saya hubungkan Kakak ke agent ya." + eskalasi.
- Fallback tidak ada promo juga berlaku jika semua promo sudah expired.
- Bot tidak menampilkan pesan teknis (status code, error message) ke customer.
- Setelah fallback tidak ada promo → bot tanya "Ada yang ingin ditanyakan lagi?"

## 5. Form Rules

> Tidak ada form UI untuk user story ini. Input diberikan customer melalui percakapan WhatsApp.

## 6. Form Notes

- Validasi input percakapan mengikuti business rule pada Feature terkait dan FEAT-AI-013.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-059-01 | Outlet tidak punya promo aktif | Customer kirim "Promo apa?" | Bot: "Saat ini belum ada promo aktif di Perkilo Premium Pejaten." + "Ada yang ingin ditanyakan lagi?" |
| SC-AI-059-02 | API promo return 500 | Customer kirim "Ada promo?" | Bot: "Mohon maaf, sistem sedang gangguan. Saya hubungkan Kakak ke agent ya." + eskalasi. |

### Detail SC-AI-059-01 - Tidak Ada Promo

#### Given

Outlet Perkilo Premium Pejaten. Tidak ada promo dengan periode masih berlangsung.

#### When

Customer kirim "Promo apa?"

#### Then

```
Saat ini belum ada promo aktif di Perkilo Premium Pejaten.
Ada yang ingin ditanyakan lagi?
```

## 8. Supporting Information

- Perilaku lintas-capability untuk konteks, koreksi input, outlet, privasi referensi, dan hasil ambigu mengikuti FEAT-AI-013.
- Effective state, fallback, timeout, dan handoff mengikuti PRD-AI-001.
