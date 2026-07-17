---
id: US-AI-007
prd: PRD-AI-001
epic: EPIC-AI-001
feature: FEAT-AI-001
title: Silent Handoff: Customer Terdampak Master OFF
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-17
updated_at: 2026-07-17
---

# US-AI-007 - Silent Handoff: Customer Terdampak Master OFF

## 1. Parent Reference

- PRD: PRD-AI-001 - Bot Runtime Behavior
- Epic: EPIC-AI-001 - Conversation Flow & Routing
- Feature: FEAT-AI-001 - Greeting, Intent Classification, Slot Filling & Handoff

## 2. User Story

Sebagai customer yang chat ke outlet dalam WABA yang master switch-nya OFF,
Saya ingin percakapan saya langsung dialihkan ke agent tanpa bot merespons,
Agar saya tetap bisa berbicara dengan manusia meskipun bot sedang dinonaktifkan.

## 3. Goal

Saat master switch OFF, bot engine mengevaluasi Gate 1 → OFF → trigger silent handoff. Bot tidak mengirim greeting, tidak melakukan klasifikasi intent, percakapan langsung ke agent.

## 4. Acceptance Criteria

- Customer baru: bot engine cek Gate 1 (Master) → OFF → silent handoff ke agent.
- Customer yang sedang dalam flow bot: in-flight interruption — percakapan bot dihentikan pada respons berikutnya, customer diarahkan ke agent.
- Customer yang sudah di sesi agent: tidak terdampak, aturan BR-AI-102 (in-flight interruption) tidak berlaku karena tidak ada sesi bot aktif.
- Customer tidak menerima respons bot apapun saat master OFF.

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-007-01 | Master switch WABA OFF. Customer baru mengirim pesan pertama ke outlet dalam WABA | Customer chat ke outlet manapun di WABA | Bot engine cek Gate 1 → OFF. Bot skip greeting, skip classification. Percakapan langsung di-eskalasi ke agent. Customer tidak menerima respons bot apapun (silent handoff). |
| SC-AI-007-02 | Master switch WABA ON. Customer sedang dalam flow "Cek status laundry". Owner toggle master switch OFF | Owner konfirmasi di modal (Control Panel) | Modal konfirmasi tampil di Control Panel. Setelah "Konfirmasi", bot engine menerima state change → in-flight interruption: percakapan bot customer dihentikan dan customer diarahkan ke agent pada respons berikutnya. |
| SC-AI-007-03 | Master switch WABA ON. Customer sudah berada di sesi chat dengan agent (handoff sudah terjadi) | Owner toggle master switch OFF, lalu konfirmasi | Bot engine cek: tidak ada sesi bot aktif → BR-AI-102 aturan penghentian tidak berlaku. Customer tetap di sesi agent. |

### Detail SC-AI-007-01 - Customer Baru Saat Master OFF

#### Given

Master switch WABA OFF (semua outlet di WABA bot-nya off). Customer baru mengirim pesan pertama ke salah satu outlet.

#### When

Customer chat ke outlet manapun di WABA.

#### Then

- Bot engine evaluasi effective state: Gate 1 (Master) = OFF.
- Gate 2 & 3 tidak dicek (cascading).
- Bot tidak mengirim greeting.
- Bot tidak melakukan intent classification.
- Percakapan langsung di-eskalasi ke agent outlet terkait (silent handoff).
- Customer tidak menerima respons bot apapun.

#### Related Business Rules

- BR-AI-100: Effective State 3-Gate
- BR-AI-101: Silent Handoff

### Detail SC-AI-007-02 - Customer Sedang Flow Bot, Master OFF

#### Given

Master switch WABA ON. Customer sedang dalam flow "Cek status laundry".

#### When

Owner toggle master switch OFF, lalu konfirmasi di modal (Control Panel).

#### Then

- Modal konfirmasi tampil di Control Panel.
- Setelah "Konfirmasi": bot engine menerima state change.
- In-flight interruption terjadi: percakapan bot customer dihentikan dan customer diarahkan ke agent pada respons berikutnya.
- Request berikutnya dari customer langsung silent handoff.

#### Related Business Rules

- BR-AI-102: In-Flight Interruption
- BR-AI-101: Silent Handoff

### Detail SC-AI-007-03 - Customer Sudah di Sesi Agent

#### Given

Master switch WABA ON. Customer sudah berada di sesi chat dengan agent (handoff sudah terjadi).

#### When

Owner toggle master switch OFF, lalu konfirmasi.

#### Then

- Toggle berubah ke OFF di Control Panel.
- Bot engine cek: tidak ada sesi bot aktif → BR-AI-102 aturan penghentian chat tidak berlaku.
- Customer tetap di sesi agent.

#### Related Business Rules

- BR-AI-102: In-Flight Interruption

## 8. Supporting Information

- **Cross-reference**: US-AI-005 (Control Panel — aksi owner mematikan master switch).
- **Cross-reference**: BR-AI-001 di FEAT-AI-002 mendeskripsikan prioritas master switch OFF; BR-AI-100 di sini adalah implementasi runtime-nya.
