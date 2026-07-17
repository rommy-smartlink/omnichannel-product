---
id: US-AI-030
prd: PRD-AI-001
epic: EPIC-AI-001
feature: FEAT-AI-001
title: Unknown Intent Fallback
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-17
updated_at: 2026-07-17
---

# US-AI-030 - Unknown Intent Fallback

## 1. Parent Reference

- PRD: PRD-AI-001 - Bot Runtime Behavior
- Epic: EPIC-AI-001 - Conversation Flow & Routing
- Feature: FEAT-AI-001 - Greeting, Intent Classification, Slot Filling & Handoff

## 2. User Story

Sebagai customer yang pesannya tidak dipahami bot,
Saya ingin bot mengarahkan saya ke daftar bantuan atau ke agent,
Agar saya tidak stuck tanpa jawaban.

## 3. Goal

Saat LLM tidak bisa mengklasifikasikan intent, bot mengirim daftar bantuan (capability yang ON). Jika setelah repeated attempt masih unknown, eskalasi ke agent.

## 4. Acceptance Criteria

- Percobaan pertama unknown: bot kirim pesan berisi daftar capability yang ON + saran "hubungi agen".
- Percobaan kedua unknown (berturut-turut): bot langsung eskalasi ke agent.
- Daftar bantuan ditampilkan dalam format ringkas, WhatsApp-friendly.
- Counter reset jika customer mengirim pesan yang berhasil diklasifikasikan di antara unknown.
- Unknown intent berbeda dengan intent OFF — intent OFF sudah dikenali tapi dinonaktifkan (US-AI-016).

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-030-01 | Customer kirim pesan "Harga berapa?" — tidak cocok dengan intent manapun | LLM tidak bisa klasifikasikan (percobaan pertama) | Bot kirim daftar capability yang ON: "Kak, saya bisa bantu: 1. Cek status laundry, 2. Cek tiket, 3. Jam operasional, 4. Info member. Atau ketik 'hubungi agen' untuk bantuan langsung." |
| SC-AI-030-02 | Customer sudah 1x unknown, lalu kirim pesan kedua juga unknown "Mau komplain nih" | LLM tidak bisa klasifikasikan (kedua kali berturut-turut) | Bot eskalasi ke agent: "Sebentar Kak, saya hubungkan ke agent kami untuk membantu." |
| SC-AI-030-03 | Customer 1x unknown, lalu kirim "cek laundry TRX123" | LLM berhasil klasifikasikan "Cek status laundry" | Bot proses intent normal. Counter unknown reset ke 0. |

### Detail SC-AI-030-01 - Unknown Pertama

#### Given

Customer mengirim pesan "Harga berapa?" — tidak cocok dengan intent manapun yang tersedia.

#### When

LLM tidak bisa mengklasifikasikan intent (confidence di bawah threshold, atau tidak match).

#### Then

- Bot kirim daftar capability yang ON: "Kak, saya bisa bantu: 1. Cek status laundry, 2. Cek tiket, 3. Jam operasional, 4. Info member. Atau ketik 'hubungi agen' untuk bantuan langsung."
- Counter unknown = 1.

#### Related Business Rules

- BR-AI-107: Unknown Intent Fallback

### Detail SC-AI-030-02 - Unknown Kedua → Eskalasi

#### Given

Customer sudah 1x unknown, counter = 1.

#### When

Customer mengirim pesan kedua "Mau komplain nih" — juga unknown.

#### Then

- Counter unknown = 2 (berturut-turut).
- Bot langsung eskalasi ke agent dengan konfirmasi: "Sebentar Kak, saya hubungkan ke agent kami untuk membantu."
- Handoff dilakukan.

### Detail SC-AI-030-03 - Reset Counter

#### Given

Customer 1x unknown, counter = 1.

#### When

Customer mengirim "cek laundry TRX123" — berhasil diklasifikasikan.

#### Then

- Bot proses intent "Cek status laundry" normal.
- Counter unknown reset ke 0.

#### Related Business Rules

- BR-AI-107: Unknown Intent Fallback

## 8. Supporting Information

- Threshold confidence untuk "unknown" ditentukan oleh engineering berdasarkan testing.
- Daftar capability di fallback message harus sesuai dengan yang ON saat itu (dinamis).
