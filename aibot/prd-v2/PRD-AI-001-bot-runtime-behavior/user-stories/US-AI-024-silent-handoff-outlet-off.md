---
id: US-AI-024
prd: PRD-AI-001
epic: EPIC-AI-001
feature: FEAT-AI-001
title: Silent Handoff - Customer Terdampak Outlet OFF
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-17
updated_at: 2026-07-17
---

# US-AI-024 - Silent Handoff: Customer Terdampak Outlet OFF

## 1. Parent Reference

- PRD: PRD-AI-001 - Bot Runtime Behavior
- Epic: EPIC-AI-001 - Conversation Flow & Routing
- Feature: FEAT-AI-001 - Greeting, Intent Classification, Slot Filling & Handoff

## 2. User Story

Sebagai customer yang chat ke outlet yang Outlet Switch-nya OFF,
Saya ingin percakapan saya langsung dialihkan ke agent tanpa bot merespons,
Agar saya tetap bisa berbicara dengan manusia meskipun bot outlet tersebut dinonaktifkan.

## 3. Goal

Saat Outlet Switch OFF, bot engine mengevaluasi Gate 2 → OFF → trigger silent handoff. Bot tidak kirim greeting, tidak klasifikasi intent, langsung ke agent. Outlet lain tidak terpengaruh.

## 4. Acceptance Criteria

- Customer baru di outlet OFF: bot engine cek Gate 1 (Master) → ON, Gate 2 (Outlet) → OFF → silent handoff ke agent outlet tersebut.
- Customer sedang flow bot di outlet: in-flight interruption — percakapan dihentikan pada respons berikutnya, ke agent.
- Customer sudah di sesi agent: tidak terdampak, tetap di sesi.
- Outlet lain dengan Outlet Switch ON tetap berfungsi normal.

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-024-01 | Outlet A Outlet Switch OFF, master switch ON. Customer baru kirim pesan pertama ke outlet A | Customer chat ke outlet A | Bot engine cek: Gate 1 ON → Gate 2 OFF. Bot skip greeting, skip classification. Langsung silent handoff ke agent outlet A. |
| SC-AI-024-02 | Outlet A Outlet Switch ON. Customer sedang flow "Cek status laundry" di outlet A | Owner toggle Outlet Switch A OFF, konfirmasi | Bot engine terima state change → in-flight interruption: percakapan dihentikan, customer diarahkan ke agent pada respons berikutnya. |
| SC-AI-024-03 | Outlet A Outlet Switch ON. Customer sudah di sesi agent outlet A | Owner toggle Outlet Switch A OFF | Bot engine cek: tidak ada sesi bot aktif → BR-AI-102 tidak berlaku. Customer tetap di sesi agent. |

### Detail SC-AI-024-01 - Customer Baru Outlet OFF

#### Given

Outlet A Outlet Switch OFF, master switch ON.

#### When

Customer baru mengirim pesan pertama ke outlet A.

#### Then

- Bot engine evaluasi effective state: Gate 1 = ON, Gate 2 = OFF.
- Gate 3 tidak dicek (cascading).
- Bot tidak mengirim greeting.
- Bot tidak melakukan klasifikasi intent.
- Percakapan langsung silent handoff ke agent outlet A.

#### Related Business Rules

- BR-AI-100: Hierarki Aktivasi Bot
- BR-AI-101: Pengalihan Langsung

### Detail SC-AI-024-02 - Customer Sedang Flow, Outlet OFF

#### Given

Outlet A Outlet Switch ON. Customer sedang dalam flow "Cek status laundry".

#### When

Owner toggle Outlet Switch A dari ON ke OFF, konfirmasi di modal (Control Panel).

#### Then

- Bot engine terima state change.
- In-flight interruption: percakapan bot customer dihentikan.
- Customer diarahkan ke agent pada respons berikutnya.

#### Related Business Rules

- BR-AI-102: Penghentian Percakapan Aktif
- BR-AI-101: Pengalihan Langsung

### Detail SC-AI-024-03 - Customer Sudah di Sesi Agent

#### Given

Outlet A Outlet Switch ON. Customer sudah di sesi agent (handoff sudah terjadi).

#### When

Owner toggle Outlet Switch A OFF.

#### Then

- Outlet Switch OFF di Control Panel.
- Bot engine cek: tidak ada sesi bot aktif → aturan penghentian tidak berlaku.
- Customer tetap di sesi agent.

#### Related Business Rules

- BR-AI-102: Penghentian Percakapan Aktif

## 8. Supporting Information

- **Cross-reference**: US-AI-022 (Control Panel — aksi owner mematikan outlet switch).
- **Cross-reference**: BR-AI-017 di FEAT-AI-005 mendeskripsikan prioritas outlet switch OFF; BR-AI-100 di sini adalah implementasi runtime-nya.
