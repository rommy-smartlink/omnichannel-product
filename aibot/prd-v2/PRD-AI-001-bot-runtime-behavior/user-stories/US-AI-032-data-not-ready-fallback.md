---
id: US-AI-032
prd: PRD-AI-001
epic: EPIC-AI-001
feature: FEAT-AI-001
title: Data Not Ready Fallback
status: Draft
priority: Medium
uix_status: 0%
created_at: 2026-07-17
updated_at: 2026-07-17
---

# US-AI-032 - Data Not Ready Fallback

## 1. Parent Reference

- PRD: PRD-AI-001 - Bot Runtime Behavior
- Epic: EPIC-AI-001 - Conversation Flow & Routing
- Feature: FEAT-AI-001 - Greeting, Intent Classification, Slot Filling & Handoff

## 2. User Story

Sebagai customer yang meminta informasi yang sumber datanya belum siap,
Saya ingin diberi tahu dengan jelas bahwa informasi tersebut belum bisa dipastikan,
Agar saya tidak mengira bot memberikan jawaban yang tidak akurat.

## 3. Goal

Saat intent ON tetapi sumber data tidak tersedia (integrasi down, data belum di-sync, dsb), bot mengirim fallback bahwa informasi belum dapat dipastikan, alih-alih memberikan data kosong atau error.

## 4. Acceptance Criteria

- Bot mendeteksi sumber data tidak siap setelah intent sudah diklasifikasikan.
- Bot kirim pesan: "Mohon maaf Kak, informasi yang Kakak minta belum dapat dipastikan saat ini. Silakan coba beberapa saat lagi atau ketik 'hubungi agen'."
- Tidak menampilkan data kosong / error mentah ke customer.
- Intent dengan data tidak siap TIDAK muncul di greeting (BR-AI-103).
- Jika customer tetap meminta intent yang datanya tidak siap → fallback ini terpicu.

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-032-01 | Outlet A: "Promo aktif" ON, tapi integrasi promo engine down. Customer tidak lihat "Promo aktif" di greeting | Customer ketik "promo apa aja nih?" | Bot klasifikasi intent "get_active_promos". Fetch data gagal (integrasi down). Bot kirim: "Mohon maaf Kak, informasi yang Kakak minta belum dapat dipastikan saat ini. Silakan coba beberapa saat lagi atau ketik 'hubungi agen'." |
| SC-AI-032-02 | Outlet A: "Info member" ON + data siap. Customer sudah lihat di greeting | Customer ketik "cek membership saya" | Bot fetch data sukses → tampilkan info member normal. |

### Detail SC-AI-032-01 - Data Tidak Siap

#### Given

Outlet A: "Promo aktif" ON di Control Panel, tapi integrasi promo engine sedang down. Customer tidak melihat "Promo aktif" di greeting (BR-AI-103 — data tidak siap tidak muncul di greeting).

#### When

Customer tetap mengetik "promo apa aja nih?".

#### Then

- Bot klasifikasi intent "get_active_promos".
- Bot fetch data → gagal (integrasi down, timeout, atau data null).
- Bot tidak menampilkan error mentah.
- Bot kirim fallback: "Mohon maaf Kak, informasi yang Kakak minta belum dapat dipastikan saat ini. Silakan coba beberapa saat lagi atau ketik 'hubungi agen'."

#### Related Business Rules

- BR-AI-109: Fallback Data Tidak Siap

### Detail SC-AI-032-02 - Data Siap, Respons Normal

#### Given

Outlet A: "Info member" ON + data siap. Customer lihat "Info member" di greeting.

#### When

Customer ketik "cek membership saya".

#### Then

- Bot klasifikasi intent "get_member_info".
- Fetch data sukses → tampilkan info member.
- Tidak ada fallback.

## 8. Supporting Information

- "Data siap" bisa berarti: integrasi API tersedia, data outlet sudah di-sync, credential valid.
- Evaluasi data siap bisa berbeda antar outlet (tergantung integrasi masing-masing).
