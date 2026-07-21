---
id: US-AI-058
prd: PRD-AI-003
epic: EPIC-AI-003
feature: FEAT-AI-009
title: Promo Aktif — Detail Promo Tertentu
status: Draft
priority: Medium
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-058 - Promo Aktif — Detail Promo Tertentu

## 1. Parent Reference

- PRD: PRD-AI-003 - Customer Self-Service Capabilities
- Epic: EPIC-AI-003 - Intent Capabilities
- Feature: FEAT-AI-009 - Promo Aktif

## 2. User Story

Sebagai customer,
Saya ingin tanya detail promo tertentu setelah melihat daftar promo,
Agar saya tahu syarat & ketentuan lengkap sebelum memutuskan menggunakan promo.

## 3. Goal

Customer menyebut nama promo spesifik dan bot menampilkan detail lengkap: nama, label member (jika relevan), periode, jam berlaku, benefit, syarat & ketentuan, lokasi, metode pembayaran, dan sisa kuota (jika ada).

## 4. Acceptance Criteria

- Customer menyebut nama promo spesifik → bot cari dari daftar promo aktif (fetch jika belum ada di sesi).
- Jika tidak ditemukan → "Maaf Kak, promo dengan nama tersebut tidak ditemukan."
- Jika ditemukan → tampilkan detail sesuai BR-AI-343.
- Syarat & ketentuan digenerate sebagai daftar poin customer-friendly.
- Field kosong/null → skip, jangan tampilkan.
- Tidak menampilkan: nominal diskon mentah, maksimal diskon, minimal transaksi mentah.
- Harga hanya dalam bentuk deskripsi benefit (diskon X% atau potongan RpX).

## 5. Form Rules

> Tidak ada form UI untuk user story ini. Input diberikan customer melalui percakapan WhatsApp.

## 6. Form Notes

- Validasi input percakapan mengikuti business rule pada Feature terkait dan FEAT-AI-013.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-058-01 | Promo "HEMAT20" aktif, diskon 20%, semua outlet, QRIS/Cash | Customer kirim "Detail HEMAT20" | Bot tampilkan detail: nama, benefit, periode, jam, lokasi, pembayaran, syarat. |
| SC-AI-058-02 | Promo "XXX" tidak ditemukan | Customer kirim "Cek promo XXX" | Bot: "Maaf Kak, promo dengan nama tersebut tidak ditemukan." |
| SC-AI-058-03 | Daftar promo belum di-fetch sesi ini | Customer kirim "Detail MEMBER PEJA" | Bot fetch daftar dulu, lalu cari "MEMBER PEJA", tampilkan detail. |

### Detail SC-AI-058-01 - Detail Promo Ditemukan

#### Given

Promo "HEMAT20" aktif: diskon 20% total belanja, berlaku 01 Jan – 31 Des 2026, jam 08:00–22:00, semua outlet, QRIS & Cash, minimal belanja Rp50.000, bisa digabung promo lain.

#### When

Customer kirim "Detail HEMAT20"

#### Then

```
HEMAT20
Diskon 20% untuk total belanja.

Berlaku: 01 Januari 2026 — 31 Desember 2026
Jam: 08:00 — 22:00
Berlaku di semua outlet
Pembayaran: QRIS, Cash

Syarat & Ketentuan:
• Minimal belanja: Rp50.000
• Bisa digabung dengan promo lain
```

## 8. Supporting Information

- Perilaku lintas-capability untuk konteks, koreksi input, outlet, privasi referensi, dan hasil ambigu mengikuti FEAT-AI-013.
- Effective state, fallback, timeout, dan handoff mengikuti PRD-AI-001.
