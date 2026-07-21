---
id: US-AI-057
prd: PRD-AI-003
epic: EPIC-AI-003
feature: FEAT-AI-009
title: Promo Aktif — Lihat Daftar Promo
status: Draft
priority: Medium
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-057 - Promo Aktif — Lihat Daftar Promo

## 1. Parent Reference

- PRD: PRD-AI-003 - Customer Self-Service Capabilities
- Epic: EPIC-AI-003 - Intent Capabilities
- Feature: FEAT-AI-009 - Promo Aktif

## 2. User Story

Sebagai customer,
Saya ingin melihat promo yang sedang berlangsung di outlet dalam bahasa natural,
Agar saya tahu diskon atau penawaran tanpa harus datang atau telepon.

## 3. Goal

Customer bertanya promo dan bot menampilkan daftar promo aktif (maks 5) dengan nama, deskripsi benefit customer-friendly, dan periode berlaku (jika ada). Label "(Member)" jika promo khusus member.

## 4. Acceptance Criteria

- Tanpa slot wajib — outlet dari context, bot langsung fetch.
- Hanya promo dengan periode masih berlangsung yang ditampilkan (BR-AI-340).
- Maksimal 5 promo per respons. Lebih dari 5 → "...dan X promo lainnya." (BR-AI-341).
- Deskripsi benefit digenerate customer-friendly (BR-AI-342).
- Label "(Member)" jika promo khusus member.
- Periode berlaku opsional — tampilkan hanya jika tersedia.
- Field yang tidak ditampilkan di list: ID promo, kuota, status internal, nominal diskon mentah, minimal transaksi mentah (BR-AI-344).
- Data kosong → "Saat ini belum ada promo aktif di [Nama Outlet]."
- API error → eskalasi: "Mohon maaf, sistem sedang gangguan. Saya hubungkan Kakak ke agent ya."

## 5. Form Rules

> Tidak ada form UI untuk user story ini. Input diberikan customer melalui percakapan WhatsApp.

## 6. Form Notes

- Validasi input percakapan mengikuti business rule pada Feature terkait dan FEAT-AI-013.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-057-01 | Outlet punya 1 promo aktif | Customer kirim "Ada promo apa?" | Bot tampilkan: "Promo aktif di Perkilo Premium Pejaten:\n\nMEMBER PEJA 5% (Member)\nDiskon 5% untuk layanan.\nBerlaku 01 Januari 2026 — 01 Januari 2027" |
| SC-AI-057-02 | Outlet tidak punya promo aktif | Customer kirim "Promo?" | Bot: "Saat ini belum ada promo aktif di Perkilo Premium Pejaten." |
| SC-AI-057-03 | Outlet punya 7 promo aktif | Customer kirim "Ada promo apa?" | Bot tampilkan 5 promo pertama + "...dan 2 promo lainnya." |
| SC-AI-057-04 | API promo error | Customer kirim "Promo dong" | Bot: "Mohon maaf, sistem sedang gangguan. Saya hubungkan Kakak ke agent ya." + eskalasi. |

### Detail SC-AI-057-01 - Satu Promo Aktif

#### Given

Outlet Perkilo Premium Pejaten. 1 promo aktif: "MEMBER PEJA 5%", khusus member, diskon 5% layanan, berlaku 01 Jan 2026 – 01 Jan 2027.

#### When

Customer kirim "Ada promo apa?"

#### Then

```
Promo aktif di Perkilo Premium Pejaten:

MEMBER PEJA 5% (Member)
Diskon 5% untuk layanan.
Berlaku 01 Januari 2026 — 01 Januari 2027
```

## 8. Supporting Information

- Perilaku lintas-capability untuk konteks, koreksi input, outlet, privasi referensi, dan hasil ambigu mengikuti FEAT-AI-013.
- Effective state, fallback, timeout, dan handoff mengikuti PRD-AI-001.
