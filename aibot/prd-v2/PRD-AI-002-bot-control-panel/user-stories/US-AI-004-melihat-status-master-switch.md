---
id: US-AI-004
prd: PRD-AI-002
epic: EPIC-AI-002
feature: FEAT-AI-002
title: Melihat Status Master Switch
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-17
updated_at: 2026-07-17
---

# US-AI-004 - Melihat Status Master Switch

## 1. Parent Reference

- PRD: PRD-AI-002 - Bot Control Panel
- Epic: EPIC-AI-002 - Bot Control Panel
- Feature: FEAT-AI-002 - Master Switch WABA

## 2. User Story

Sebagai owner,
Saya ingin melihat status master switch ON/OFF di header Control Panel beserta timestamp dan identitas yang terakhir mengubah,
Agar saya tahu apakah bot sedang aktif atau nonaktif untuk seluruh outlet dalam WABA ini.

## 3. Goal

Owner dapat mengetahui status terkini master switch WABA saat pertama kali membuka halaman Control Panel.

## 4. Acceptance Criteria

- Master switch ditampilkan di header Control Panel, di bawah WABA selector.
- Indicator visual membedakan state ON (normal/hijau) dan OFF (warning/merah).
- Banner info ditampilkan di bawah switch: "Seluruh outlet pada WABA ini dapat menjalankan Autobot" saat ON.
- Banner info ditampilkan di bawah switch: "Bot nonaktif — semua outlet langsung ke agent" saat OFF.
- Timestamp terakhir diubah dan identitas pengubah ditampilkan di sebelah switch.
- Default state WABA baru: master switch ON.

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-004-01 | Owner baru diaktivasi WABA-nya (master switch default ON), lalu membuka Control Panel dan memilih WABA tersebut via WABA selector | Owner melihat header Control Panel | Sistem menampilkan master switch di posisi ON dengan indicator normal. Banner info "Seluruh outlet pada WABA ini dapat menjalankan Autobot" tampil di bawah switch. Timestamp dan identitas pengubah terakhir ditampilkan. |

### Detail SC-AI-004-01 - Melihat Master Switch Default ON

#### Given

Owner baru diaktivasi WABA-nya. Master switch default ON. Owner membuka Control Panel dan memilih WABA tersebut via WABA selector.

#### When

Owner melihat header Control Panel.

#### Then

- Master switch ditampilkan dengan state ON, indicator normal.
- Banner info: "Seluruh outlet pada WABA ini dapat menjalankan Autobot."
- Timestamp terakhir diubah dan identitas pengubah terlihat.

#### Related Business Rules

- BR-AI-003: Default Master Switch ON

#### UI Behavior

- Master switch berada di header Control Panel, di bawah WABA selector.

## 8. Supporting Information

Lo-Fi design:

```text
┌──────────────────────────────────────────────────────────────┐
│ Status Bot WABA: [ ●──── ON ]                               │
│ Seluruh outlet pada WABA ini dapat menjalankan Autobot.      │
│ Diperbarui 2 jam lalu oleh Agestya                           │
└──────────────────────────────────────────────────────────────┘
```
