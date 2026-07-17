---
id: US-AI-023
prd: PRD-AI-002
epic: EPIC-AI-002
feature: FEAT-AI-005
title: Mengaktifkan Kembali Bot Per Outlet
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-17
updated_at: 2026-07-17
---

# US-AI-023 - Mengaktifkan Kembali Bot Per Outlet

## 1. Parent Reference

- PRD: PRD-AI-002 - Bot Control Panel
- Epic: EPIC-AI-002 - Bot Control Panel
- Feature: FEAT-AI-005 - Outlet Switch

## 2. User Story

Sebagai owner,
Saya ingin mengaktifkan kembali bot untuk satu outlet setelah maintenance selesai,
Agar bot kembali merespons customer di outlet tersebut dengan konfigurasi yang sudah ada.

## 3. Goal

Owner dapat mengubah Outlet Switch dari OFF ke ON tanpa modal konfirmasi. Konfigurasi intent outlet langsung berlaku kembali.

## 4. Acceptance Criteria

- Toggle dari OFF ke ON tidak memerlukan modal konfirmasi, langsung berlaku.
- Indicator visual kembali normal (hijau tidak warning).
- Teks "Autobot nonaktif" hilang, kembali ke "Autobot aktif".
- Konfigurasi intent outlet berlaku normal tanpa setup ulang.
- Timestamp dan identitas pengubah terupdate.
- Master Switch harus ON agar bot benar-benar berfungsi (Gate 1 > Gate 2).

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-023-01 | Outlet A Outlet Switch OFF. Master switch ON. Konfigurasi intent A tidak berubah saat OFF | Owner toggle Outlet Switch A dari OFF ke ON | Outlet Switch ON. Indicator normal. Bot kembali berfungsi. Konfigurasi intent sesuai sebelum OFF. |

### Detail SC-AI-023-01 - Mengaktifkan Kembali Outlet Switch

#### Given

Outlet A Outlet Switch OFF. Master switch ON. Konfigurasi intent outlet A tetap utuh dari sebelum OFF.

#### When

Owner toggle Outlet Switch A dari OFF ke ON.

#### Then

- Outlet Switch berubah ke ON, indicator normal.
- Teks "Autobot aktif".
- Konfigurasi intent outlet A berlaku normal — tidak perlu setup ulang.
- Bot kembali merespons customer di outlet A.

#### Related Business Rules

- BR-AI-018: Konfigurasi Tetap Tersimpan Saat Outlet OFF

#### UI Behavior

- Toggle OFF ke ON tidak memicu modal konfirmasi. Langsung PATCH dan update UI.

## 8. Supporting Information

Tidak ada.
