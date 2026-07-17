---
id: US-AI-015
prd: PRD-AI-002
epic: EPIC-AI-002
feature: FEAT-AI-003
title: Melihat Intent talk_to_agent Terkunci
status: Draft
priority: Medium
uix_status: 0%
created_at: 2026-07-17
updated_at: 2026-07-17
---

# US-AI-015 - Melihat Intent talk_to_agent Terkunci

## 1. Parent Reference

- PRD: PRD-AI-002 - Bot Control Panel
- Epic: EPIC-AI-002 - Bot Control Panel
- Feature: FEAT-AI-003 - Konfigurasi Intent per Outlet

## 2. User Story

Sebagai owner,
Saya ingin melihat intent "Hubungi agen" ditampilkan dengan toggle readonly dan mendapatkan toast info saat saya mencoba menekannya,
Agar saya paham bahwa jalur customer ke agent tidak pernah bisa dinonaktifkan.

## 3. Goal

Owner melihat toggle `talk_to_agent` dalam state readonly/disabled dengan label "Selalu aktif". Tap menampilkan toast info.

## 4. Acceptance Criteria

- Baris "Hubungi agen" ditampilkan dengan toggle switch dalam state disabled/readonly, state ON.
- Label "Selalu aktif" di samping toggle.
- Saat owner tap/click toggle readonly, muncul toast info: "Hubungi agen selalu aktif agar customer selalu bisa terhubung dengan tim kami."
- Toast auto-dismiss setelah 3-5 detik.
- Tidak ada perubahan state, tidak ada efek di database.

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-015-01 | Modal konfigurasi intent outlet A terbuka | Owner melihat baris "Hubungi agen" di list intent | Baris menampilkan toggle switch dalam state disabled/readonly ON, label "Selalu aktif". |
| SC-AI-015-02 | Modal konfigurasi intent outlet A terbuka, toggle "Hubungi agen" readonly | Owner tap/click toggle "Hubungi agen" | Toast info: "Hubungi agen selalu aktif agar customer selalu bisa terhubung dengan tim kami." Toast auto-dismiss 3-5 detik. Toggle tidak berubah. DB tidak ter-update. Modal tetap terbuka. |

### Detail SC-AI-015-01 - Melihat Toggle Terkunci

#### Given

Modal konfigurasi intent outlet A terbuka.

#### When

Owner melihat baris "Hubungi agen" di daftar intent dalam modal.

#### Then

- Toggle switch tampil dalam state ON, disabled/readonly (warna abu-abu atau visual berbeda dari toggle aktif).
- Label "Selalu aktif" di samping toggle.

#### Related Business Rules

- BR-AI-007: talk_to_agent Selalu ON

### Detail SC-AI-015-02 - Tap Toggle Terkunci

#### Given

Modal konfigurasi intent outlet A terbuka. Toggle "Hubungi agen" dalam state readonly.

#### When

Owner tap/click toggle.

#### Then

- Toast info: "Hubungi agen selalu aktif agar customer selalu bisa terhubung dengan tim kami."
- Toast auto-dismiss 3-5 detik.
- Toggle tidak berubah state.
- Tidak ada PATCH ke database.
- Modal tetap terbuka.

#### Related Business Rules

- BR-AI-007: talk_to_agent Selalu ON

## 8. Supporting Information

Tidak ada.
