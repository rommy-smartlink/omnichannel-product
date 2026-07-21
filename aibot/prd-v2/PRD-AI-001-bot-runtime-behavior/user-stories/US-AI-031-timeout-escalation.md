---
id: US-AI-031
prd: PRD-AI-001
epic: EPIC-AI-001
feature: FEAT-AI-001
title: Timeout Escalation
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-17
updated_at: 2026-07-17
---

# US-AI-031 - Timeout Escalation

## 1. Parent Reference

- PRD: PRD-AI-001 - Bot Runtime Behavior
- Epic: EPIC-AI-001 - Conversation Flow & Routing
- Feature: FEAT-AI-001 - Greeting, Intent Classification, Slot Filling & Handoff

## 2. User Story

Sebagai customer yang sedang menunggu respons bot,
Saya ingin dihubungkan ke agent jika bot terlalu lama tidak merespons,
Agar saya tidak menunggu tanpa kepastian.

## 3. Goal

Jika bot tidak bisa merespons dalam 1 menit (dari pesan terakhir customer), percakapan otomatis dieskalasi ke agent.

## 4. Acceptance Criteria

- Timer 1 menit dimulai setelah customer mengirim pesan.
- Jika bot berhasil merespons dalam 1 menit → timer reset, percakapan lanjut normal.
- Jika 1 menit terlampaui tanpa respons bot → eskalasi ke agent dengan pesan: "Mohon maaf Kak, saya mengalami kendala. Saya hubungkan ke agent kami."
- Timeout berlaku di semua fase: klasifikasi intent, slot filling, fetch data.
- Setelah timeout escalation → bot berhenti membalas (BR-AI-105).

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-031-01 | Customer kirim "Cek laundry TRX123", bot mulai fetch data dari sistem laundry | Fetch data > 1 menit (timeout external API) | Setelah 1 menit, bot kirim: "Mohon maaf Kak, saya mengalami kendala. Saya hubungkan ke agent kami." Percakapan dialihkan ke agent. |
| SC-AI-031-02 | Customer kirim "Cek laundry TRX123", bot fetch data sukses dalam 30 detik | Bot kirim respons dalam 30 detik | Timer reset. Percakapan lanjut normal. |
| SC-AI-031-03 | Customer sedang slot filling, bot menunggu input nomor tiket | Customer tidak membalas selama 5 menit | Tidak ada timeout di sisi customer — timeout hanya berlaku untuk respons bot, bukan customer. |

### Detail SC-AI-031-01 - Timeout Fetch Data

#### Given

Customer mengirim "Cek laundry TRX123". Bot sudah klasifikasi intent dan mulai fetch data dari sistem laundry.

#### When

Fetch data memakan waktu > 1 menit (timeout external API).

#### Then

- Setelah 1 menit dari pesan terakhir customer tanpa respons bot.
- Bot kirim eskalasi: "Mohon maaf Kak, saya mengalami kendala. Saya hubungkan ke agent kami."
- Percakapan dialihkan ke agent.
- Bot berhenti membalas (BR-AI-105).

#### Related Business Rules

- BR-AI-106: Pengalihan saat Bot Tidak Dapat Melanjutkan
- BR-AI-105: Penguncian Percakapan ke Agent

### Detail SC-AI-031-02 - Respons Dalam Batas Waktu

#### Given

Customer mengirim "Cek laundry TRX123".

#### When

Bot fetch data sukses dan kirim respons dalam 30 detik.

#### Then

- Timer reset.
- Percakapan lanjut normal — tidak ada eskalasi.

### Detail SC-AI-031-03 - Timeout Hanya untuk Bot

#### Given

Customer sedang slot filling, bot menunggu input nomor tiket.

#### When

Customer tidak membalas selama 5 menit.

#### Then

- Tidak ada timeout — timer hanya berlaku saat bot yang harus merespons.
- Bot tetap menunggu input customer.

#### Related Business Rules

- BR-AI-106: Pengalihan saat Bot Tidak Dapat Melanjutkan

## 8. Supporting Information

- 1 menit adalah nilai initial. Bisa di-adjust setelah observasi performa di production.
- Timer di-reset setiap kali bot mengirim respons — bukan akumulasi total.
