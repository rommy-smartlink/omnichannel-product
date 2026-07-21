---
id: FEAT-AI-008
prd: PRD-AI-003
epic: EPIC-AI-003
title: Hubungi Agent
status: Draft
owner: Product Owner
created_at: 2026-07-21
updated_at: 2026-07-21
---

# FEAT-AI-008 - Hubungi Agent

## 1. Purpose

Customer meminta koneksi langsung ke agen manusia kapan saja selama percakapan. Eskalasi adalah hak customer — bot tidak boleh memaksa menyelesaikan flow, tidak bertanya alasan, tidak membatasi jumlah permintaan. Conversation diteruskan ke antrean agen outlet, dan bot berhenti membalas.

## 2. Scope

### In Scope

- Intent `talk_to_agent` selalu ON — tidak terpengaruh toggle intent per outlet (hanya perlu Master ON + Outlet ON).
- Trigger phrases: variasi bahasa natural minta agen/admin/CS/manusia/komplain.
- Konfirmasi eskalasi satu kali: "Saya hubungkan ke agent [Nama Outlet] ya."
- Customer tidak perlu memberikan alasan atau menyelesaikan slot-filling sebelum eskalasi.
- Conversation diteruskan ke antrean agen outlet (round-robin).
- Bot berhenti membalas (silent) setelah eskalasi.
- Customer di mid-flow intent lain tetap bisa eskalasi kapan saja.
- Jika agen offline → conversation masuk antrean unassigned (existing flow).
- Eskalasi final — tidak bisa dibatalkan dengan command "batal".

### Out of Scope

- Handoff dengan context summary (fase 2).
- Priority routing (VIP, bahasa tertentu).
- Re-assign ke agen berbeda mid-conversation.
- Auto-eskalasi berdasarkan sentiment analysis.
- Escalation ke channel lain (email, phone) dalam satu conversation.

## 3. Business Rules

### BR-AI-330 — Eskalasi Tanpa Syarat

Setiap permintaan eskalasi dihormati tanpa syarat: tidak ada konfirmasi "apakah kamu yakin?", tidak ada tanya alasan, tidak ada counter offer, tidak ada limit jumlah percakapan. Begitu customer mengindikasikan ingin agen → eskalasi.

### BR-AI-331 — talk_to_agent Selalu ON

Intent `talk_to_agent` tidak termasuk dalam 6 intent configurable. Intent ini selalu tersedia selama Master Switch ON dan Outlet Switch ON. Tidak bisa dimatikan oleh owner.

### BR-AI-332 — Bot Silence Setelah Eskalasi

Setelah handoff ke agen, bot tidak mengirim pesan apapun lagi di conversation tersebut. Tidak ada follow-up, tidak ada "apakah sudah terbantu?", tidak ada re-engagement.

### BR-AI-333 — Eskalasi Final

Eskalasi ke agen tidak bisa dibatalkan. Command "batal" setelah eskalasi tidak berlaku.

### BR-AI-334 — Trigger Phrases Inclusive

Jika ada keraguan apakah customer ingin agen → eskalasi. Trigger phrases: "admin", "CS", "agen", "manusia", "orang", "komplain", "bot doang", "capek", "bosan", "butuh bantuan", "panggil supervisor", dsb.

## 4. Dependencies

- Bot Runtime Behavior (PRD-AI-001): handoff protocol, post-handoff silence.
- Bot Control Panel (PRD-AI-002): Master Switch, Outlet Switch (talk_to_agent tidak perlu Intent Switch).
- Agent queue system: round-robin assignment, unassigned status.
- Conversation context: outlet ID, nama outlet.

## 5. User Story List

| US ID | Judul US | Goal | Status |
|---|---|---|---|
| US-AI-055 | Hubungi Agent — Eskalasi Langsung | Customer minta agen dengan bahasa natural dan langsung dihubungkan tanpa syarat | Draft |
| US-AI-056 | Hubungi Agent — Mid-Flow Eskalasi | Customer di tengah slot-filling intent lain tetap bisa eskalasi tanpa menyelesaikan flow | Draft |

## 6. Notes for Developer

- Trigger phrases detection ada di level LLM intent classification — bukan keyword matching eksak.
- Eskalasi di tengah slot-filling: state conversation dibuang, tidak perlu disimpan.
- Jika semua agen offline → conversation status unassigned, tidak ada pesan tambahan dari bot.
- Nama outlet diambil dari conversation context untuk template konfirmasi.
