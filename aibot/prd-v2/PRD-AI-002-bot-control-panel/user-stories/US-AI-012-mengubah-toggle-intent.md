---
id: US-AI-012
prd: PRD-AI-002
epic: EPIC-AI-002
feature: FEAT-AI-003
title: Mengubah Toggle Intent
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-17
updated_at: 2026-07-17
---

# US-AI-012 - Mengubah Toggle Intent

## 1. Parent Reference

- PRD: PRD-AI-002 - Bot Control Panel
- Epic: EPIC-AI-002 - Bot Control Panel
- Feature: FEAT-AI-003 - Konfigurasi Intent per Outlet

## 2. User Story

Sebagai owner,
Saya ingin mengubah toggle intent ON/OFF di modal konfigurasi intent yang perubahannya bersifat lokal sampai saya menekan Simpan,
Agar saya bisa bereksperimen dengan konfigurasi sebelum menyimpannya.

## 3. Goal

Owner dapat mengubah satu atau lebih toggle intent di modal konfigurasi intent. Perubahan bersifat lokal, tidak ada PATCH ke server sampai Simpan ditekan.

## 4. Acceptance Criteria

- Toggle intent merespons tap/click dan berubah state secara lokal di UI.
- Tidak ada request PATCH ke server saat toggle diubah.
- State database tidak berubah sampai Simpan ditekan.
- Toggle `talk_to_agent` tidak berubah state — readonly.
- Owner bisa mengubah multiple toggle sebelum menyimpan.

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-012-01 | Modal konfigurasi intent outlet A terbuka. Toggle "Cek status laundry" saat ini OFF | Owner tap toggle "Cek status laundry" dari OFF ke ON | Toggle berubah ke ON di state lokal UI. Tidak ada request PATCH ke server. State DB masih OFF. Modal tetap terbuka. |
| SC-AI-012-02 | Modal konfigurasi intent outlet A terbuka. Toggle "Promo aktif" saat ini ON | Owner tap toggle "Promo aktif" dari ON ke OFF | Toggle berubah ke OFF di state lokal UI. Tidak ada request PATCH ke server. State DB masih ON. Modal tetap terbuka. |
| SC-AI-012-03 | Modal konfigurasi intent outlet A terbuka | Owner tap/click toggle "Hubungi agen" yang readonly | Muncul toast info: "Hubungi agen selalu aktif agar customer selalu bisa terhubung dengan tim kami." Toast auto-dismiss 3-5 detik. Toggle tidak berubah. |

### Detail SC-AI-012-01 - Toggle OFF ke ON

#### Given

Modal konfigurasi intent outlet A terbuka. Toggle "Cek status laundry" dalam state OFF (sesuai database).

#### When

Owner tap toggle "Cek status laundry".

#### Then

- Toggle berubah dari OFF ke ON di UI lokal.
- Tidak ada network request.
- State database tetap OFF.
- Modal tetap terbuka.

### Detail SC-AI-012-02 - Toggle ON ke OFF

#### Given

Modal konfigurasi intent outlet A terbuka. Toggle "Promo aktif" dalam state ON (sesuai database).

#### When

Owner tap toggle "Promo aktif".

#### Then

- Toggle berubah dari ON ke OFF di UI lokal.
- Tidak ada network request.
- State database tetap ON.
- Modal tetap terbuka.

### Detail SC-AI-012-03 - Tap Toggle Readonly

#### Given

Modal konfigurasi intent outlet A terbuka. Toggle "Hubungi agen" dalam state readonly ON.

#### When

Owner tap/click toggle "Hubungi agen".

#### Then

- Toast info tampil: "Hubungi agen selalu aktif agar customer selalu bisa terhubung dengan tim kami."
- Toast auto-dismiss setelah 3-5 detik.
- Toggle tidak berubah state.
- Database tidak ter-update.
- Modal tetap terbuka.

#### Related Business Rules

- BR-AI-007: talk_to_agent Selalu ON

## 8. Supporting Information

Tidak ada.
