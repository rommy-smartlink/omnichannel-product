---
id: US-AI-016
prd: PRD-AI-001
epic: EPIC-AI-001
feature: FEAT-AI-001
title: Fallback Intent OFF
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-17
updated_at: 2026-07-17
---

# US-AI-016 - Fallback Intent OFF

## 1. Parent Reference

- PRD: PRD-AI-001 - Bot Runtime Behavior
- Epic: EPIC-AI-001 - Conversation Flow & Routing
- Feature: FEAT-AI-001 - Greeting, Intent Classification, Slot Filling & Handoff

## 2. User Story

Sebagai customer yang chat ke outlet dengan intent tertentu dinonaktifkan,
Saya ingin mendapat pesan fallback yang jelas saat saya meminta capability yang tidak tersedia,
Agar saya tahu fitur tersebut tidak tersedia dan saya tetap bisa menghubungi agent.

## 3. Goal

Saat intent OFF, bot engine melewatkan klasifikasi intent tersebut dan mengirim fallback message (BR-AI-108). Intent lain yang ON tetap berfungsi.

## 4. Acceptance Criteria

- Bot engine memuat pengaturan per outlet di awal percakapan.
- Intent yang OFF dilewati dari klasifikasi — permintaan customer yang cocok dengan intent OFF mendapat pesan fallback.
- Fallback message: "Maaf Kak, fitur ini sedang tidak tersedia di outlet ini. Saya bisa hubungkan Kakak ke agent untuk dibantu lebih lanjut."
- Intent lainnya yang ON tetap berjalan normal.
- Command "hubungi agen" tetap berfungsi (BR-AI-104).

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-016-01 | Outlet X memiliki "Cek status laundry" OFF (lainnya ON). Master ON, Outlet Switch ON. Customer pilih outlet X, greeting sudah dikirim | Customer mengetik "Cek status laundry TRX12345" | Bot engine cek Gate 3: "Cek status laundry" OFF → skip intent classification untuk intent ini. Bot kirim fallback: "Maaf Kak, fitur ini sedang tidak tersedia di outlet ini. Saya bisa hubungkan Kakak ke agent untuk dibantu lebih lanjut." Intent lain yang ON tetap berfungsi normal. Command "Hubungi agen" tetap bisa dieksekusi. |

### Detail SC-AI-016-01 - Fallback Intent OFF

#### Given

Outlet X memiliki pengaturan: "Cek status laundry" OFF, 5 intent lainnya ON. Master ON, Outlet Switch ON. Customer sudah memilih outlet X dan greeting sudah dikirim.

#### When

Customer mengetik "Cek status laundry TRX12345".

#### Then

- Bot engine load settings per outlet (dilakukan di awal percakapan).
- Bot engine deteksi "Cek status laundry" OFF → skip intent classification.
- Bot engine kirim fallback message sesuai BR-AI-108: "Maaf Kak, fitur ini sedang tidak tersedia di outlet ini. Saya bisa hubungkan Kakak ke agent untuk dibantu lebih lanjut."
- Intent lain yang ON (misal "Jam operasional") tetap berfungsi normal jika customer memintanya.
- Customer bisa mengetik "hubungi agen" dan bot akan memproses handoff (BR-AI-104).

#### Related Business Rules

- BR-AI-100: Hierarki Aktivasi Bot
- BR-AI-108: Fallback Intent OFF
- BR-AI-104: Perintah Kendali

#### Validation

- Bot engine harus memuat konfigurasi per outlet sebelum klasifikasi intent.
- Intent OFF tidak muncul di greeting/menu (BR-AI-103).

## 8. Supporting Information

- **Cross-reference**: US-AI-012/US-AI-013 (Control Panel — aksi owner mengubah toggle intent).
- **Cross-reference**: BR-AI-008 di FEAT-AI-003 mendeskripsikan fallback intent OFF; BR-AI-108 di sini adalah implementasi runtime-nya.
