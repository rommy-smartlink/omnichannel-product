---
id: US-AI-065
prd: PRD-AI-003
epic: EPIC-AI-003
feature: FEAT-AI-011
title: Info Layanan — Umum ≥21 Layanan
status: Draft
priority: Medium
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-065 - Info Layanan — Umum ≥21 Layanan

## 1. Parent Reference

- PRD: PRD-AI-003 - Customer Self-Service Capabilities
- Epic: EPIC-AI-003 - Intent Capabilities
- Feature: FEAT-AI-011 - Info Layanan Outlet

## 2. User Story

Sebagai customer,
Saya ingin bot membantu saya menemukan layanan yang relevan saat outlet punya banyak layanan,
Agar saya tidak disuguhi puluhan layanan yang sulit dibaca di WhatsApp.

## 3. Goal

Customer bertanya umum di outlet dengan ≥21 layanan, dan bot meminta customer menyebut layanan/kebutuhan yang dicari. Bot memberi contoh keyword (maks 5) untuk membantu, tapi tidak mengklaim semua contoh pasti tersedia.

## 4. Acceptance Criteria

- Jika total layanan outlet ≥ 21 DAN customer bertanya umum → bot minta spesifik (BR-AI-360).
- Pesan: "Layanan di [Nama Outlet] cukup banyak, Kak. Kakak sedang cari layanan apa? Bisa sebutkan jenisnya, misalnya [contoh 1], [contoh 2], [contoh 3], [contoh 4], atau [contoh 5]."
- Contoh keyword maks 5, bukan daftar lengkap, bukan klaim tersedia (BR-AI-363).
- Setelah customer menyebut layanan spesifik → bot jawab ya/tidak (kembali ke flow US-AI-063).
- Jika customer tetap kirim pertanyaan umum lagi → ulangi pesan yang sama.
- Jika customer minta "daftar lengkap" → "Maaf Kak, daftar lengkapnya cukup banyak. Kakak bisa ceritakan jenis layanan yang Kakak cari, ya?"

## 5. Form Rules

> Tidak ada form UI untuk user story ini. Input diberikan customer melalui percakapan WhatsApp.

## 6. Form Notes

- Validasi input percakapan mengikuti business rule pada Feature terkait dan FEAT-AI-013.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-065-01 | Outlet Tebet punya 25 layanan | Customer kirim "Ada layanan apa saja?" | Bot minta spesifik + contoh keyword. |
| SC-AI-065-02 | Setelah bot minta spesifik, customer sebut "express" | Customer kirim "Ada express?" | Bot jawab ya/tidak sesuai ketersediaan. |
| SC-AI-065-03 | Customer minta "daftar lengkap" | Customer kirim "Tampilkan semua" | Bot: "Daftar lengkapnya cukup banyak. Kakak bisa ceritakan jenis layanan yang Kakak cari, ya?" |

### Detail SC-AI-065-01 - Outlet Besar

#### Given

Outlet Tebet memiliki 25 layanan terdaftar.

#### When

Customer kirim "Ada layanan apa saja?"

#### Then

```
Layanan di Outlet Tebet cukup banyak, Kak.
Kakak sedang cari layanan apa? Bisa sebutkan jenisnya, misalnya cuci pakaian, setrika, express, sepatu, atau bed cover.
```

## 8. Supporting Information

- Perilaku lintas-capability untuk konteks, koreksi input, outlet, privasi referensi, dan hasil ambigu mengikuti FEAT-AI-013.
- Effective state, fallback, timeout, dan handoff mengikuti PRD-AI-001.
