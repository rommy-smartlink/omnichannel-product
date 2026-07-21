---
id: US-AI-017
prd: PRD-AI-001
epic: EPIC-AI-001
feature: FEAT-AI-001
title: Silent Handoff - Semua Intent OFF
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-17
updated_at: 2026-07-17
---

# US-AI-017 - Silent Handoff: Semua Intent OFF

## 1. Parent Reference

- PRD: PRD-AI-001 - Bot Runtime Behavior
- Epic: EPIC-AI-001 - Conversation Flow & Routing
- Feature: FEAT-AI-001 - Greeting, Intent Classification, Slot Filling & Handoff

## 2. User Story

Sebagai customer yang chat ke outlet yang semua intent configurable-nya OFF,
Saya ingin langsung dihubungkan ke agent tanpa bot merespons,
Agar saya tidak kebingungan saat bot tidak bisa melayani apapun.

## 3. Goal

Saat keenam intent configurable OFF (hanya `talk_to_agent` ON), bot engine mendeteksi outlet tidak bisa melayani apapun → trigger silent handoff. Bot tidak kirim greeting, tidak tampilkan menu, langsung ke agent. Scope per outlet.

## 4. Acceptance Criteria

- Bot engine load settings di awal percakapan.
- Jika semua 6 configurable intent OFF: Gate 3 evaluasi → tidak ada intent yang bisa match → outlet dianggap bot tidak aktif.
- Bot tidak mengirim greeting, tidak menampilkan menu bantuan.
- Pesan customer langsung di-eskalasi ke agent (silent handoff).
- `talk_to_agent` tidak dihitung dalam evaluasi 6 intent configurable (karena selalu ON — BR-AI-110).
- Perilaku outcome sama seperti silent handoff karena master OFF / outlet OFF — trigger berbeda.

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-017-01 | Outlet X memiliki semua 6 intent configurable OFF (hanya "Hubungi agen" ON secara default). Master ON, Outlet Switch ON | Customer pilih outlet X, kirim pesan pertama "Halo" | Bot engine load settings — Gate 1 ON, Gate 2 ON. Evaluasi Gate 3: tidak ada configurable intent yang bisa match. Outlet dianggap bot tidak aktif. Bot tidak kirim greeting. Bot tidak tampilkan menu. Pesan customer langsung dialihkan ke antrian agent outlet X (silent handoff). |

### Detail SC-AI-017-01 - Silent Handoff Semua OFF

#### Given

Outlet X memiliki pengaturan: keenam intent configurable (check_laundry_status, check_ticket_status, get_outlet_services, get_operating_hours, get_active_promos, get_member_info) dalam keadaan OFF. Hanya `talk_to_agent` ON (default, tidak bisa dimatikan — BR-AI-110). Master Switch ON, Outlet Switch ON.

#### When

Customer memilih outlet X dan mengirim pesan pertama "Halo".

#### Then

- Bot engine load settings per outlet.
- Gate 1 (Master) = ON, Gate 2 (Outlet) = ON.
- Gate 3: semua configurable intent OFF → tidak ada yang bisa match.
- Outlet dianggap bot tidak aktif.
- Bot tidak mengirim greeting.
- Bot tidak menampilkan menu bantuan.
- Pesan customer langsung dialihkan ke antrian agent outlet X (silent handoff).

#### Related Business Rules

- BR-AI-100: Hierarki Aktivasi Bot
- BR-AI-101: Pengalihan Langsung saat Bot Tidak Aktif
- BR-AI-110: Hubungi Agen Selalu Aktif

## 8. Supporting Information

- **Cross-reference**: US-AI-012/US-AI-013 (Control Panel — aksi owner mengubah toggle intent).
- **Cross-reference**: BR-AI-009 di FEAT-AI-003 mendeskripsikan aturan silent handoff saat semua intent OFF; BR-AI-101 di sini adalah implementasi runtime-nya.
