---
id: US-AI-027
prd: PRD-AI-001
epic: EPIC-AI-001
feature: FEAT-AI-001
title: Evaluasi Effective State Bot (3-Gate)
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-17
updated_at: 2026-07-17
---

# US-AI-027 - Evaluasi Effective State Bot (3-Gate)

## 1. Parent Reference

- PRD: PRD-AI-001 - Bot Runtime Behavior
- Epic: EPIC-AI-001 - Conversation Flow & Routing
- Feature: FEAT-AI-001 - Greeting, Intent Classification, Slot Filling & Handoff

## 2. User Story

Sebagai bot engine,
Saya perlu mengevaluasi apakah saya boleh merespons customer berdasarkan 3 lapisan switch,
Agar saya tidak melanggar konfigurasi yang sudah diatur owner di Control Panel.

## 3. Goal

Bot engine mengecek 3-gate secara cascading (Master → Outlet → Intent) sebelum merespons customer. Gate pertama yang OFF langsung memblokir — gate berikutnya tidak dicek. Evaluasi dilakukan setiap customer mengirim pesan, bukan hanya di awal percakapan.

## 4. Acceptance Criteria

- Setiap kali customer mengirim pesan, bot engine mengevaluasi effective state:
  - Gate 1 — Master Switch WABA: OFF → silent handoff, stop evaluasi.
  - Gate 2 — Outlet Switch: OFF → silent handoff, stop evaluasi.
  - Gate 3 — Intent Switch: OFF → fallback message (BR-AI-108).
- Ketiga gate ON → bot jalan normal (greeting, klasifikasi, slot filling).
- Evaluasi dilakukan sekali per pesan (tidak di-cache lintas pesan).
- `talk_to_agent` tidak dicek di Gate 3 (BR-AI-110).
- Semua configurable intent OFF → outlet dianggap bot tidak aktif → silent handoff.

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-027-01 | Master ON, Outlet A ON, "Cek status laundry" ON | Customer di outlet A kirim "Cek laundry TRX123" | Gate 1 ON → Gate 2 ON → Gate 3 ON → bot klasifikasi intent dan proses. |
| SC-AI-027-02 | Master OFF, Outlet A ON, "Cek status laundry" ON | Customer di outlet A kirim pesan | Gate 1 OFF → silent handoff. Gate 2 & 3 tidak dicek. |
| SC-AI-027-03 | Master ON, Outlet A OFF, "Cek status laundry" ON | Customer di outlet A kirim pesan | Gate 1 ON → Gate 2 OFF → silent handoff. Gate 3 tidak dicek. |
| SC-AI-027-04 | Master ON, Outlet A ON, "Cek status laundry" OFF | Customer di outlet A kirim "Cek laundry TRX123" | Gate 1 ON → Gate 2 ON → Gate 3: "Cek status laundry" OFF → fallback message. |

### Detail SC-AI-027-01 - Semua Gate ON

#### Given

Master Switch ON, Outlet A Switch ON, "Cek status laundry" ON.

#### When

Customer di outlet A mengirim "Cek laundry TRX123".

#### Then

- Gate 1: Master ON ✓ → lanjut.
- Gate 2: Outlet A ON ✓ → lanjut.
- Gate 3: "Cek status laundry" ON ✓ → bot klasifikasi intent dan proses normal.

### Detail SC-AI-027-02 - Gate 1 OFF

#### Given

Master Switch OFF (semua outlet di WABA bot-nya off).

#### When

Customer di outlet manapun mengirim pesan.

#### Then

- Gate 1: Master OFF ✗ → silent handoff. Stop evaluasi.
- Gate 2 & 3 tidak dicek.

### Detail SC-AI-027-03 - Gate 2 OFF

#### Given

Master Switch ON, Outlet A Switch OFF.

#### When

Customer di outlet A mengirim pesan.

#### Then

- Gate 1: Master ON ✓ → lanjut.
- Gate 2: Outlet A OFF ✗ → silent handoff. Stop evaluasi.
- Gate 3 tidak dicek.

### Detail SC-AI-027-04 - Gate 3 OFF (Satu Intent)

#### Given

Master ON, Outlet A ON, "Cek status laundry" OFF, intent lainnya ON.

#### When

Customer di outlet A mengirim "Cek laundry TRX123".

#### Then

- Gate 1 ON ✓ → Gate 2 ON ✓ → Gate 3: "Cek status laundry" OFF ✗.
- Bot kirim fallback message (BR-AI-108).

#### Related Business Rules

- BR-AI-100: Hierarki Aktivasi Bot

## 8. Supporting Information

- Evaluasi per pesan penting karena switch bisa berubah kapan saja oleh owner — tidak bisa di-cache di awal sesi.
- Flow evaluasi ini digunakan oleh semua US lain di FEAT-AI-001 (silent handoff, fallback, handoff, dsb).
