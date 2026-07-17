---
id: US-AI-006
prd: PRD-AI-002
epic: EPIC-AI-002
feature: FEAT-AI-002
title: Mengaktifkan Kembali Bot WABA
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-17
updated_at: 2026-07-17
---

# US-AI-006 - Mengaktifkan Kembali Bot WABA

## 1. Parent Reference

- PRD: PRD-AI-002 - Bot Control Panel
- Epic: EPIC-AI-002 - Bot Control Panel
- Feature: FEAT-AI-002 - Master Switch WABA

## 2. User Story

Sebagai owner,
Saya ingin mengaktifkan kembali bot untuk seluruh outlet setelah maintenance atau emergency selesai,
Agar bot kembali dapat merespons customer dengan konfigurasi per outlet yang sudah ada sebelumnya.

## 3. Goal

Owner dapat mengubah master switch dari OFF ke ON tanpa modal konfirmasi, dan konfigurasi per outlet langsung berlaku kembali.

## 4. Acceptance Criteria

- Toggle dari OFF ke ON tidak memerlukan modal konfirmasi, langsung berlaku.
- Indicator visual kembali normal (hijau/tidak warning).
- Banner info OFF hilang, kembali ke "Seluruh outlet pada WABA ini dapat menjalankan Autobot."
- Konfigurasi intent per outlet berlaku normal — tidak perlu setup ulang.
- Timestamp dan identitas pengubah terupdate.

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-006-01 | Master switch WABA OFF. Outlet A memiliki konfigurasi "Cek status laundry" OFF dan "Layanan outlet" ON | Owner toggle master switch WABA dari OFF ke ON | Master switch berubah ke ON dengan indicator normal. Banner info OFF hilang. Owner membuka modal konfigurasi intent outlet A — toggle "Cek status laundry" masih OFF dan "Layanan outlet" masih ON (konfigurasi tidak berubah). Bot kembali dapat merespons customer. |

### Detail SC-AI-006-01 - Mengaktifkan Kembali Master Switch

#### Given

Master switch WABA OFF. Outlet A memiliki konfigurasi "Cek status laundry" OFF dan "Layanan outlet" ON. Konfigurasi tidak berubah saat master OFF.

#### When

Owner toggle master switch dari OFF ke ON.

#### Then

- Master switch berubah ke ON, indicator normal.
- Banner info OFF hilang.
- Konfigurasi intent per outlet tetap sesuai sebelum master OFF — tidak ada reset.
- Bot kembali berfungsi normal untuk seluruh outlet.

#### Related Business Rules

- BR-AI-002: Konfigurasi Tetap Tersimpan Saat Master OFF

#### UI Behavior

- Toggle OFF ke ON tidak memicu modal konfirmasi. Langsung PATCH dan update UI.

## 8. Supporting Information

Tidak ada.
