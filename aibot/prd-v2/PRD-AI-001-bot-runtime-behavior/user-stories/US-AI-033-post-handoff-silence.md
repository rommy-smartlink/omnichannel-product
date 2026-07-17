---
id: US-AI-033
prd: PRD-AI-001
epic: EPIC-AI-001
feature: FEAT-AI-001
title: Post-Handoff Bot Silence
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-17
updated_at: 2026-07-17
---

# US-AI-033 - Post-Handoff Bot Silence

## 1. Parent Reference

- PRD: PRD-AI-001 - Bot Runtime Behavior
- Epic: EPIC-AI-001 - Conversation Flow & Routing
- Feature: FEAT-AI-001 - Greeting, Intent Classification, Slot Filling & Handoff

## 2. User Story

Sebagai customer yang sudah dialihkan ke agent,
Saya ingin percakapan saya sepenuhnya ditangani oleh manusia,
Agar saya tidak diganggu oleh respons bot yang tidak relevan.

## 3. Goal

Setelah handoff terjadi (explicit, silent, timeout, atau unknown fallback), bot berhenti membalas di percakapan tersebut. Semua pesan customer selanjutnya langsung ke agent.

## 4. Acceptance Criteria

- Setelah handoff, bot tidak mengirim pesan apapun ke customer.
- Bot tidak melakukan evaluasi effective state (3-gate) untuk customer yang sudah di sesi agent.
- Bot tidak klasifikasi intent untuk customer yang sudah di sesi agent.
- Pesan customer langsung masuk ke antrian agent outlet.
- Aturan ini berlaku untuk SEMUA jenis handoff: explicit (US-AI-029), silent (US-AI-007/017/024), timeout (US-AI-031), unknown fallback (US-AI-030).

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-033-01 | Handoff sudah terjadi (customer minta agent). Customer di sesi agent | Customer kirim "Halo, masih disana?" | Bot tidak merespons. Pesan langsung ke agent. Bot tidak evaluasi 3-gate, tidak klasifikasi intent. |
| SC-AI-033-02 | Handoff sudah terjadi (timeout). Customer di sesi agent | Owner toggle master switch ON kembali | Customer tidak terdampak — tetap di sesi agent. Bot tidak tiba-tiba merespons. |
| SC-AI-033-03 | Handoff sudah terjadi. Agent sudah selesai menangani dan menutup sesi | Customer kirim pesan baru setelah sesi agent ditutup | Sesi baru dimulai — bot evaluasi 3-gate dari awal, kirim greeting jika Gate 1 & 2 ON. |

### Detail SC-AI-033-01 - Bot Diam Setelah Handoff

#### Given

Customer di outlet A. Handoff sudah terjadi (customer minta "hubungi agen", bot sudah konfirmasi dan alihkan). Customer sekarang di sesi agent.

#### When

Customer mengirim pesan "Halo, masih disana?".

#### Then

- Bot sama sekali tidak merespons.
- Tidak ada evaluasi effective state.
- Tidak ada klasifikasi intent.
- Pesan langsung masuk ke antrian agent outlet A.

#### Related Business Rules

- BR-AI-105: Post-Handoff Bot Silence

### Detail SC-AI-033-02 - Switch ON Tidak Mengganggu Sesi Agent

#### Given

Master switch OFF, customer sudah di sesi agent (karena silent handoff sebelumnya). Owner toggle master switch ON kembali.

#### When

Customer di sesi agent mengirim pesan baru.

#### Then

- Bot tidak tiba-tiba merespons meskipun master switch sudah ON.
- Customer tetap di sesi agent.
- Aturan post-handoff silence tetap berlaku.

### Detail SC-AI-033-03 - Sesi Baru Setelah Agent Tutup

#### Given

Handoff sudah terjadi, agent sudah menangani dan menutup sesi chat.

#### When

Customer mengirim pesan baru ke outlet yang sama.

#### Then

- Ini adalah sesi baru.
- Bot evaluasi effective state (3-gate) dari awal.
- Jika Gate 1 & 2 ON → bot kirim greeting.
- Jika Gate 1 atau 2 OFF → silent handoff lagi.

#### Related Business Rules

- BR-AI-105: Post-Handoff Bot Silence

## 8. Supporting Information

- "Sesi agent ditutup" perlu trigger dari sistem agent. Koordinasi dengan tim engineering untuk mekanisme deteksi sesi berakhir.
- Post-handoff silence adalah safety net — mencegah bot "menyela" percakapan customer-agent.
