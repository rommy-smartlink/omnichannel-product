---
id: US-AI-028
prd: PRD-AI-001
epic: EPIC-AI-001
feature: FEAT-AI-001
title: Greeting Berdasarkan Intent Siap
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-17
updated_at: 2026-07-17
---

# US-AI-028 - Greeting Berdasarkan Intent Siap

## 1. Parent Reference

- PRD: PRD-AI-001 - Bot Runtime Behavior
- Epic: EPIC-AI-001 - Conversation Flow & Routing
- Feature: FEAT-AI-001 - Greeting, Intent Classification, Slot Filling & Handoff

## 2. User Story

Sebagai customer yang baru pertama chat ke outlet,
Saya ingin melihat daftar capability yang benar-benar tersedia dan siap digunakan,
Agar saya tahu apa saja yang bisa saya tanyakan ke bot.

## 3. Goal

Bot engine mengirim greeting dengan daftar capability yang memenuhi dua syarat: intent ON DAN prasyarat data siap. Intent OFF atau data belum siap tidak muncul di greeting.

## 4. Acceptance Criteria

- Bot kirim greeting hanya saat Gate 1 & Gate 2 ON.
- Greeting menampilkan daftar capability yang intent-nya ON DAN datanya siap di-fetch.
- Intent ON tetapi data belum siap → tidak muncul di greeting.
- Intent OFF → tidak muncul di greeting.
- `talk_to_agent` selalu muncul di greeting (dengan wording "Hubungi agen").
- Format greeting WhatsApp-friendly — singkat, bullet/numbering, mudah dibaca di mobile.
- Jika tidak ada satupun capability siap (semua intent OFF atau data tidak siap) → silent handoff.

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-028-01 | Outlet A: 4 intent ON + data siap, 1 intent ON tapi data belum siap, 1 intent OFF. Master ON, Outlet ON | Customer baru pilih outlet A | Bot kirim greeting dengan 4 capability siap + "Hubungi agen". Intent data-belum-siap dan intent OFF tidak muncul. |
| SC-AI-028-02 | Outlet A: semua intent OFF, hanya talk_to_agent ON | Customer baru pilih outlet A | Bot tidak kirim greeting → silent handoff (BR-AI-101, US-AI-017). |
| SC-AI-028-03 | Outlet A: semua intent ON tapi data belum siap (misal integrasi down) | Customer baru pilih outlet A | Bot tidak kirim greeting → silent handoff — tidak ada yang bisa ditampilkan. |

### Detail SC-AI-028-01 - Greeting Normal

#### Given

Outlet A: 4 intent ON + data siap (cek laundry, cek tiket, jam operasional, info member), 1 intent ON tapi data belum siap (promo aktif — integrasi promo belum ready), 1 intent OFF (layanan outlet). Master ON, Outlet ON.

#### When

Customer baru memilih outlet A dan mengirim pesan pertama.

#### Then

- Gate 1 & 2 ON → bot boleh kirim greeting.
- Bot evaluasi intent: 4 siap, 1 data belum siap, 1 OFF.
- Greeting hanya menampilkan 4 capability siap + "Hubungi agen".
- "Promo aktif" tidak muncul (data belum siap).
- "Layanan outlet" tidak muncul (intent OFF).

#### Related Business Rules

- BR-AI-103: Greeting Hanya Intent Siap

### Detail SC-AI-028-02 - Semua Intent OFF

#### Given

Outlet A: semua 6 intent configurable OFF. Hanya talk_to_agent ON.

#### When

Customer baru memilih outlet A dan mengirim pesan pertama.

#### Then

- Gate 1 & 2 ON.
- Bot evaluasi: tidak ada capability siap.
- Bot tidak kirim greeting → silent handoff (US-AI-017).

### Detail SC-AI-028-03 - Semua Data Belum Siap

#### Given

Outlet A: semua intent ON tapi semua sumber data belum siap (integrasi down).

#### When

Customer baru memilih outlet A.

#### Then

- Bot evaluasi: tidak ada capability yang datanya siap.
- Bot tidak kirim greeting → silent handoff.
- Customer tidak menerima greeting kosong.

#### Related Business Rules

- BR-AI-103: Greeting Hanya Intent Siap
- BR-AI-101: Silent Handoff

## 8. Supporting Information

- Prasyarat data siap: integrasi dengan source data (laundry system, promo engine, membership DB, dsb) harus tersedia untuk outlet tersebut.
- Evaluasi data siap bisa berbeda per outlet — outlet A mungkin punya promo engine, outlet B belum.
