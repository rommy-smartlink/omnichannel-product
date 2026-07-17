---
id: US-AI-020
prd: PRD-AI-002
epic: EPIC-AI-002
feature: FEAT-AI-004
title: Navigasi ke Pengaturan Jam Operasional
status: Draft
priority: Medium
uix_status: 0%
created_at: 2026-07-17
updated_at: 2026-07-17
---

# US-AI-020 - Navigasi ke Pengaturan Jam Operasional

## 1. Parent Reference

- PRD: PRD-AI-002 - Bot Control Panel
- Epic: EPIC-AI-002 - Bot Control Panel
- Feature: FEAT-AI-004 - Informasi Jam Operasional

## 2. User Story

Sebagai owner,
Saya ingin menekan tombol "Atur Jam Operasional" di card outlet dan diarahkan ke menu Pengaturan Outlet,
Agar saya bisa menyesuaikan jam operasional outlet tanpa harus mencari menu outlet secara manual.

## 3. Goal

Owner dapat navigasi dari Control Panel ke menu Pengaturan Outlet melalui tombol di card outlet.

## 4. Acceptance Criteria

- Tombol "Atur Jam Operasional" tersedia di card outlet.
- Tap tombol mengarahkan owner ke menu Pengaturan Outlet.
- Navigasi tidak membawa konteks perubahan intent yang belum disimpan di modal.

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-020-01 | Owner di Control Panel. Card outlet A menampilkan "Jam Operasional 24 Jam" (skema default) | Owner tekan tombol "Atur Jam Operasional" di card outlet A | Owner diarahkan ke menu Pengaturan Outlet untuk outlet A. Konteks perubahan intent yang belum disimpan di modal tidak ikut terbawa. |

### Detail SC-AI-020-01 - Navigasi ke Menu Outlet

#### Given

Owner di Control Panel. Card outlet A menampilkan "Jam Operasional 24 Jam". Tidak ada modal konfigurasi intent yang terbuka.

#### When

Owner tekan tombol "Atur Jam Operasional" di card outlet A.

#### Then

- Owner diarahkan ke halaman/menu Pengaturan Outlet.
- Navigasi tidak membawa perubahan intent lokal yang belum disimpan (ini hanya relevan jika modal konfigurasi intent terbuka, tapi tombol ini berada di luar modal).

#### Related Business Rules

- BR-AI-015: Navigasi ke Menu Outlet

#### UI Behavior

- Tombol "Atur Jam Operasional" di card outlet, di bawah atau di samping info jam operasional.
- Navigasi tidak memengaruhi flow simpan di modal konfigurasi intent — karena tombol ini di card, bukan di modal.

## 8. Supporting Information

Tidak ada.
