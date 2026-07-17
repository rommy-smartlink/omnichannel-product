---
id: US-AI-018
prd: PRD-AI-001
epic: EPIC-AI-001
feature: FEAT-AI-001
title: Command Override
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-17
updated_at: 2026-07-17
---

# US-AI-018 - Command Override

## 1. Parent Reference

- PRD: PRD-AI-001 - Bot Runtime Behavior
- Epic: EPIC-AI-001 - Conversation Flow & Routing
- Feature: FEAT-AI-001 - Greeting, Intent Classification, Slot Filling & Handoff

## 2. User Story

Sebagai customer di outlet dengan pengaturan intent apapun,
Saya ingin perintah manual "menu", "hubungi agen", dan "batal" selalu diproses oleh bot,
Agar saya selalu bisa mengakses menu bantuan, menghubungi agent, atau membatalkan percakapan tanpa peduli pengaturan intent.

## 3. Goal

Command "menu", "hubungi agen", "batal" selalu berfungsi di bot engine, terlepas dari pengaturan intent per outlet (Gate 3). Owner tidak bisa memblokir akses customer ke agent atau menu melalui Control Panel.

## 4. Acceptance Criteria

- "hubungi agen" → selalu diproses bot engine, memicu handoff ke agent.
- "menu" → selalu diproses, menampilkan daftar capability yang ON.
- "batal" → selalu diproses, membatalkan flow yang sedang berlangsung dan kembali ke state awal (siap menerima intent baru).
- Ketiga command ini bypass Gate 3 (Intent Switch) — tidak dicek apakah intent terkait ON atau OFF.
- Owner tidak bisa menonaktifkan ketiga command ini melalui pengaturan intent di Control Panel.

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-018-01 | Outlet X dengan `talk_to_agent` selalu ON (BR-AI-110). Semua configurable intent bisa ON atau OFF | Customer kirim "Saya mau bicara dengan admin" | Bot engine bypass Gate 3 — selalu match intent `talk_to_agent`. Bot konfirmasi dan teruskan ke agent outlet X. Customer selalu punya jalur ke manusia. |
| SC-AI-018-02 | Outlet X dengan 5 dari 6 intent configurable OFF. Customer sedang dalam flow intent yang ON | Customer mengetik "menu", "hubungi agen", atau "batal" | Bot engine bypass Gate 3 — selalu interpretasikan command tersebut terlepas dari pengaturan per outlet. Command override toggle pengaturan intent. |

### Detail SC-AI-018-01 - Hubungi Agen Selalu Tersedia

#### Given

Outlet X dengan `talk_to_agent` selalu ON. Semua configurable intent bisa ON atau OFF.

#### When

Customer di outlet X mengirim "Saya mau bicara dengan admin" atau "hubungi agen".

#### Then

- Bot engine selalu mengenali intent `talk_to_agent` — bypass Gate 3.
- Bot konfirmasi dan teruskan ke agent outlet X.
- Customer selalu punya escape ke manusia.

#### Related Business Rules

- BR-AI-104: Command Override
- BR-AI-110: talk_to_agent Always ON

### Detail SC-AI-018-02 - Menu dan Batal Selalu Berfungsi

#### Given

Outlet X dengan 5 dari 6 intent configurable OFF. Customer sudah berada di flow intent yang ON.

#### When

Customer mengetik "menu", "hubungi agen", atau "batal".

#### Then

- "menu" → bot engine bypass Gate 3, menampilkan daftar capability yang ON (BR-AI-103).
- "hubungi agen" → bot engine proses handoff.
- "batal" → bot engine batalkan flow saat ini, kembali ke state siap menerima intent baru.
- Semua command ini berfungsi terlepas dari toggle pengaturan intent di Control Panel.

#### Related Business Rules

- BR-AI-104: Command Override

## 8. Supporting Information

- **Cross-reference**: US-AI-015 (Control Panel — owner melihat toggle talk_to_agent readonly).
- **Cross-reference**: BR-AI-012 di FEAT-AI-003 mendeskripsikan command override; BR-AI-104 di sini adalah implementasi runtime-nya.
