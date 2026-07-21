---
id: US-AI-061
prd: PRD-AI-003
epic: EPIC-AI-003
feature: FEAT-AI-010
title: Info Member — Detail Membership Tertentu
status: Draft
priority: Medium
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-061 - Info Member — Detail Membership Tertentu

## 1. Parent Reference

- PRD: PRD-AI-003 - Customer Self-Service Capabilities
- Epic: EPIC-AI-003 - Intent Capabilities
- Feature: FEAT-AI-010 - Info Member

## 2. User Story

Sebagai customer,
Saya ingin melihat detail membership tertentu setelah melihat daftar,
Agar saya tahu deskripsi lengkap dan di mana membership berlaku.

## 3. Goal

Customer menyebut nama membership spesifik dan bot menampilkan detail: nama, status, biaya daftar, masa aktif, deskripsi, dan lokasi keberlakuan.

## 4. Acceptance Criteria

- Customer menyebut nama membership → bot cari dari daftar aktif (fetch jika belum ada di sesi).
- Jika tidak ditemukan → "Maaf Kak, membership dengan nama tersebut tidak ditemukan."
- Jika ditemukan → tampilkan detail sesuai BR-AI-354.
- Lokasi: `all` → "Berlaku di semua outlet", `only` → sebut outlet, `except` → "Berlaku di semua outlet kecuali outlet tertentu."
- Field kosong/null → skip.
- Estimasi hari dari `active_period_day` jika > 0: "(±X hari)".

## 5. Form Rules

> Tidak ada form UI untuk user story ini. Input diberikan customer melalui percakapan WhatsApp.

## 6. Form Notes

- Validasi input percakapan mengikuti business rule pada Feature terkait dan FEAT-AI-013.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-061-01 | Membership "MEMBER AREMANIA" aktif | Customer kirim "Detail MEMBER AREMANIA" | Bot tampilkan nama, biaya, masa aktif, deskripsi, lokasi. |
| SC-AI-061-02 | Membership "XXX" tidak ditemukan | Customer kirim "Info member XXX" | Bot: "Maaf Kak, membership dengan nama tersebut tidak ditemukan." |

### Detail SC-AI-061-01 - Detail Membership

#### Given

Membership "MEMBER AREMANIA": biaya Rp1.500, masa aktif 1 bulan, deskripsi "Membership basic untuk pelanggan setia", lokasi only Perkilo Premium Pejaten.

#### When

Customer kirim "Detail MEMBER AREMANIA"

#### Then

```
MEMBER AREMANIA
Biaya daftar: Rp1.500
Masa aktif: 1 bulan (±30 hari)

Membership basic untuk pelanggan setia.

Outlet: Perkilo Premium Pejaten
```

## 8. Supporting Information

- Perilaku lintas-capability untuk konteks, koreksi input, outlet, privasi referensi, dan hasil ambigu mengikuti FEAT-AI-013.
- Effective state, fallback, timeout, dan handoff mengikuti PRD-AI-001.
