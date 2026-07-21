---
id: US-AI-029
prd: PRD-AI-001
epic: EPIC-AI-001
feature: FEAT-AI-001
title: Explicit Handoff: Customer Minta Agent
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-17
updated_at: 2026-07-17
---

# US-AI-029 - Explicit Handoff: Customer Minta Agent

## 1. Parent Reference

- PRD: PRD-AI-001 - Bot Runtime Behavior
- Epic: EPIC-AI-001 - Conversation Flow & Routing
- Feature: FEAT-AI-001 - Greeting, Intent Classification, Slot Filling & Handoff

## 2. User Story

Sebagai customer yang sedang dalam percakapan dengan bot,
Saya ingin bisa meminta dihubungkan ke agent manusia kapan saja,
Agar saya mendapat bantuan personal saat bot tidak bisa membantu.

## 3. Goal

Customer bisa meminta agent kapan saja — bot mengkonfirmasi lalu melakukan handoff. Setelah handoff, bot berhenti membalas (BR-AI-105).

## 4. Acceptance Criteria

- Customer mengetik "hubungi agen" (atau variasi natural seperti "mau bicara admin", "agent dong") → bot mengenali intent `talk_to_agent`.
- Bot mengirim pesan konfirmasi singkat sebelum handoff: "Sebentar Kak, saya hubungkan ke agent kami."
- Setelah konfirmasi, percakapan dialihkan ke antrian agent outlet tersebut.
- Setelah handoff, bot berhenti membalas di percakapan itu (BR-AI-105).
- `talk_to_agent` selalu ON dan tidak bisa dimatikan — customer selalu punya jalur escape.
- Explicit handoff berlaku di semua state: saat dalam flow bot, saat greeting, bahkan saat slot filling.

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-029-01 | Customer sedang dalam flow "Cek status laundry", bot sedang menunggu input nomor tiket | Customer mengetik "Saya mau bicara dengan admin" | Bot klasifikasi sebagai `talk_to_agent`. Bot kirim konfirmasi: "Sebentar Kak, saya hubungkan ke agent kami." Percakapan dialihkan ke agent. Bot berhenti membalas. |
| SC-AI-029-02 | Customer baru selesai menerima greeting, belum memulai flow apapun | Customer mengetik "hubungi agen" | Bot langsung proses handoff tanpa menunggu input lain. |
| SC-AI-029-03 | Customer sudah di sesi agent (handoff sudah terjadi) | Customer kirim pesan apapun | Bot tidak merespons. Semua pesan langsung ke agent. |

### Detail SC-AI-029-01 - Handoff dari Dalam Flow

#### Given

Customer sedang dalam flow "Cek status laundry", bot sedang menunggu input nomor tiket (slot filling).

#### When

Customer mengetik "Saya mau bicara dengan admin".

#### Then

- Bot klasifikasi sebagai `talk_to_agent` — bypass flow yang sedang berjalan.
- Bot kirim konfirmasi: "Sebentar Kak, saya hubungkan ke agent kami."
- Slot filling "Cek status laundry" dibatalkan.
- Percakapan dialihkan ke antrian agent outlet.
- Bot berhenti membalas (BR-AI-105).

#### Related Business Rules

- BR-AI-104: Perintah Kendali
- BR-AI-105: Penguncian Percakapan ke Agent
- BR-AI-110: Hubungi Agen Selalu Aktif

### Detail SC-AI-029-02 - Handoff dari Greeting

#### Given

Customer baru menerima greeting, belum memulai flow apapun.

#### When

Customer mengetik "hubungi agen".

#### Then

- Bot klasifikasi `talk_to_agent`.
- Bot kirim konfirmasi dan handoff.
- Bot berhenti membalas.

### Detail SC-AI-029-03 - Sudah di Sesi Agent

#### Given

Customer sudah di sesi agent (handoff sudah terjadi sebelumnya).

#### When

Customer mengirim pesan baru.

#### Then

- Bot tidak merespons — post-handoff silence (BR-AI-105).
- Semua pesan langsung ke agent.

## 8. Supporting Information

- Wording konfirmasi bisa di-adjust. Yang penting: singkat, WhatsApp-friendly, customer tahu sedang dialihkan.
- Tidak ada batasan berapa kali customer bisa minta handoff.
