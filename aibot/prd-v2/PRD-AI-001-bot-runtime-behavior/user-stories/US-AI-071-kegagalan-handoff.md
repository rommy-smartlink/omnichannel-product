---
id: US-AI-071
prd: PRD-AI-001
epic: EPIC-AI-001
feature: FEAT-AI-001
title: Menangani Kegagalan Handoff
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-071 - Menangani Kegagalan Handoff

## 1. Parent Reference

- PRD: PRD-AI-001 - Bot Runtime Behavior
- Epic: EPIC-AI-001 - Conversation Flow & Routing
- Feature: FEAT-AI-001 - Greeting, Intent Classification, Slot Filling & Handoff

## 2. User Story

Sebagai customer yang perlu berbicara dengan agent,  
Saya ingin mendapat kepastian ketika pengalihan tidak dapat diselesaikan,  
Agar saya tidak mengira sudah masuk antrean padahal permintaan belum tercatat.

## 3. Goal

Bot hanya menyatakan handoff berhasil setelah percakapan diterima sistem agent dan memberikan fallback yang jujur jika proses tersebut gagal.

## 4. Acceptance Criteria

- Status **dialihkan ke agent** hanya digunakan setelah handoff berhasil dibuat.
- Saat handoff masih diproses, bot tidak menjanjikan bahwa agent sudah menerima percakapan.
- Jika handoff gagal, bot memberi tahu customer dan mencoba ulang satu kali.
- Jika percobaan ulang gagal, bot menyampaikan bahwa pengalihan belum berhasil serta meminta customer mencoba kembali beberapa saat lagi.
- Bot tidak menampilkan detail teknis kegagalan.
- Setelah handoff berhasil, BR-AI-105 berlaku dan bot berhenti membalas.
- Silent handoff akibat Master atau Outlet OFF tidak berubah menjadi respons bot; kegagalannya dicatat untuk tindak lanjut operasional.

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-071-01 | Customer meminta agent | Handoff berhasil dibuat | Bot memberi konfirmasi pengalihan lalu berhenti membalas |
| SC-AI-071-02 | Customer meminta agent | Percobaan handoff pertama gagal dan percobaan ulang berhasil | Bot memberi konfirmasi setelah percobaan ulang berhasil |
| SC-AI-071-03 | Customer meminta agent | Dua percobaan handoff gagal | Bot menyatakan pengalihan belum berhasil tanpa menyebut detail teknis |
| SC-AI-071-04 | Master OFF memicu silent handoff | Sistem agent gagal menerima handoff | Bot tetap diam dan kegagalan masuk pemantauan operasional |

### Detail SC-AI-071-03 - Handoff Gagal Setelah Retry

#### Given

Customer berada dalam sesi bot dan meminta berbicara dengan agent.

#### When

Pembuatan handoff gagal pada percobaan awal dan satu percobaan ulang.

#### Then

Bot tidak mengunci percakapan sebagai sesi agent dan memberi tahu bahwa pengalihan belum berhasil.

#### Error Message

> Maaf Kak, saat ini saya belum berhasil menghubungkan percakapan ke agent. Silakan coba lagi beberapa saat lagi.

#### Notes

Customer tetap dapat mengirim perintah **hubungi agent** kembali.

## 8. Supporting Information

- User story ini membedakan permintaan handoff, handoff diproses, dan handoff berhasil.
