---
id: US-AI-022
prd: PRD-AI-002
epic: EPIC-AI-002
feature: FEAT-AI-005
title: Mematikan Bot Per Outlet
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-17
updated_at: 2026-07-17
---

# US-AI-022 - Mematikan Bot Per Outlet

## 1. Parent Reference

- PRD: PRD-AI-002 - Bot Control Panel
- Epic: EPIC-AI-002 - Bot Control Panel
- Feature: FEAT-AI-005 - Outlet Switch

## 2. User Story

Sebagai owner,
Saya ingin mematikan bot untuk satu outlet tertentu melalui toggle Outlet Switch di card outlet,
Agar saya bisa menonaktifkan bot di outlet yang sedang maintenance atau tidak siap tanpa memengaruhi outlet lain.

## 3. Goal

Owner dapat mengubah Outlet Switch dari ON ke OFF dengan modal konfirmasi, menghentikan bot di outlet tersebut saja.

## 4. Acceptance Criteria

- Toggle Outlet Switch dari ON ke OFF menampilkan modal konfirmasi.
- Modal konfirmasi menjelaskan dampak: percakapan bot di outlet tersebut akan dihentikan, customer dialihkan ke agent.
- Owner memilih "Konfirmasi" → switch OFF, PATCH ke server, timestamp + identitas tercatat.
- Owner memilih "Batal" → toggle kembali ON, tidak ada perubahan.
- Indicator visual berubah merah/icon warning + teks "Autobot nonaktif".
- Konfigurasi intent outlet tidak dihapus/di-reset.
- Outlet lain tidak terpengaruh.
- Master Switch OFF override Outlet Switch — jika master OFF, outlet switch tidak relevan.

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-022-01 | Outlet A Outlet Switch ON. Ada 3 outlet lain juga ON | Owner tap toggle Outlet Switch A dari ON ke OFF, konfirmasi di modal | Outlet Switch A OFF. Indicator merah, teks "Autobot nonaktif". Outlet lain tetap ON. Konfigurasi intent A tidak berubah. |
| SC-AI-022-02 | Outlet A Outlet Switch ON. Owner tap toggle ke OFF, modal konfirmasi tampil | Owner pilih "Batal" | Toggle kembali ON. Tidak ada perubahan state. Outlet A tetap berfungsi normal. |

### Detail SC-AI-022-01 - Mematikan Outlet Switch

#### Given

Outlet A Outlet Switch ON. Ada 3 outlet lain juga ON dengan konfigurasi masing-masing.

#### When

Owner tap toggle Outlet Switch A dari ON ke OFF. Modal konfirmasi tampil. Owner pilih "Konfirmasi".

#### Then

- Outlet Switch A berubah ke OFF.
- Indicator visual merah/icon warning. Teks "Autobot nonaktif".
- Outlet lain tetap ON — tidak terpengaruh.
- Konfigurasi intent outlet A tidak berubah di database.
- Timestamp dan identitas pengubah terupdate.

#### Related Business Rules

- BR-AI-017: Outlet Switch Off Priority
- BR-AI-020: Modal Konfirmasi Outlet Switch OFF

#### UI Behavior

- Modal konfirmasi: "Mematikan bot akan menghentikan percakapan bot yang sedang berlangsung di outlet ini dan mengalihkan customer ke agent."
- Tombol: "Batal" (secondary), "Konfirmasi" (primary/destructive).

### Detail SC-AI-022-02 - Batal Mematikan

#### Given

Outlet A Outlet Switch ON. Owner tap toggle ke OFF. Modal konfirmasi tampil.

#### When

Owner pilih "Batal".

#### Then

- Toggle kembali ke posisi ON.
- Tidak ada PATCH ke server.
- Outlet A tetap berfungsi normal.

## 8. Supporting Information

Tidak ada.
