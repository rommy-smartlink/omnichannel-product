---
id: US-AI-060
prd: PRD-AI-003
epic: EPIC-AI-003
feature: FEAT-AI-010
title: Info Member — Lihat Daftar Membership
status: Draft
priority: Medium
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-060 - Info Member — Lihat Daftar Membership

## 1. Parent Reference

- PRD: PRD-AI-003 - Customer Self-Service Capabilities
- Epic: EPIC-AI-003 - Intent Capabilities
- Feature: FEAT-AI-010 - Info Member

## 2. User Story

Sebagai customer,
Saya ingin melihat paket membership yang tersedia di outlet dalam bahasa natural,
Agar saya tahu pilihan member, biaya daftar, dan masa aktif sebelum mendaftar.

## 3. Goal

Customer bertanya membership dan bot menampilkan daftar membership aktif (maks 5) dengan nama, biaya daftar (customer-friendly), dan masa aktif (customer-friendly).

## 4. Acceptance Criteria

- Tanpa slot wajib — outlet dari context, bot langsung fetch.
- Hanya membership `active = true` dan relevan untuk outlet yang ditampilkan (BR-AI-350).
- Maksimal 5 membership per respons. Lebih → "...dan X membership lainnya." (BR-AI-351).
- Biaya daftar: 0 atau disabled → "Gratis", > 0 → "RpX" (BR-AI-352).
- Masa aktif customer-friendly: unlimited→"Tidak terbatas", monthly→"X bulan", yearly→"X tahun" (BR-AI-353).
- Field tidak ditampilkan: ID internal, created_by, created_at, updated_at, count_customer_usage, count_promo, status internal (BR-AI-355).
- Data kosong → "Saat ini belum ada membership aktif di [Nama Outlet]."
- API error → eskalasi.

## 5. Form Rules

> Tidak ada form UI untuk user story ini. Input diberikan customer melalui percakapan WhatsApp.

## 6. Form Notes

- Validasi input percakapan mengikuti business rule pada Feature terkait dan FEAT-AI-013.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-060-01 | Outlet punya 2 membership aktif | Customer kirim "Ada member apa?" | Bot tampilkan daftar dengan nama, biaya, masa aktif. |
| SC-AI-060-02 | Outlet tidak punya membership | Customer kirim "Membership?" | Bot: "Saat ini belum ada membership aktif di Perkilo Premium Pejaten." |
| SC-AI-060-03 | Membership dengan biaya gratis | Customer kirim "Member apa aja?" | Bot tampilkan "Biaya daftar: Gratis". |

### Detail SC-AI-060-01 - Dua Membership

#### Given

Outlet Perkilo Premium Pejaten. Membership: "MEMBER AREMANIA" (Rp1.500, 1 bulan), "ROMY GERUNK" (Rp5.000, 1 tahun).

#### When

Customer kirim "Ada member apa?"

#### Then

```
Membership aktif di Perkilo Premium Pejaten:

MEMBER AREMANIA
Biaya daftar: Rp1.500
Masa aktif: 1 bulan

ROMY GERUNK
Biaya daftar: Rp5.000
Masa aktif: 1 tahun
```

## 8. Supporting Information

- Perilaku lintas-capability untuk konteks, koreksi input, outlet, privasi referensi, dan hasil ambigu mengikuti FEAT-AI-013.
- Effective state, fallback, timeout, dan handoff mengikuti PRD-AI-001.
