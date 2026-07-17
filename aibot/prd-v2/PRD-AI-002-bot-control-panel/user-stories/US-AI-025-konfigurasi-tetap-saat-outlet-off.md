---
id: US-AI-025
prd: PRD-AI-002
epic: EPIC-AI-002
feature: FEAT-AI-005
title: Konfigurasi Outlet Tetap Saat Outlet OFF
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-17
updated_at: 2026-07-17
---

# US-AI-025 - Konfigurasi Outlet Tetap Saat Outlet OFF

## 1. Parent Reference

- PRD: PRD-AI-002 - Bot Control Panel
- Epic: EPIC-AI-002 - Bot Control Panel
- Feature: FEAT-AI-005 - Outlet Switch

## 2. User Story

Sebagai owner,
Saya ingin konfigurasi intent outlet tetap tersimpan saat Outlet Switch dimatikan,
Agar saya tidak perlu mengatur ulang outlet saat bot diaktifkan kembali.

## 3. Goal

Konfigurasi intent outlet tidak dihapus, di-reset, atau dimodifikasi saat Outlet Switch OFF, dan berlaku normal saat Outlet Switch kembali ON.

## 4. Acceptance Criteria

- Database konfigurasi intent outlet tidak berubah saat Outlet Switch di-toggle OFF.
- Database konfigurasi intent outlet tidak berubah saat Outlet Switch di-toggle ON.
- Saat Outlet Switch kembali ON, bot engine memuat konfigurasi yang ada dan berfungsi normal.
- Owner bisa verifikasi: buka modal konfigurasi intent saat Outlet OFF, semua toggle sesuai pengaturan sebelumnya.

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-025-01 | Outlet A Outlet Switch OFF. Konfigurasi intent A: "Cek status laundry" OFF, "Layanan outlet" ON | Owner toggle Outlet Switch A dari OFF ke ON | Outlet Switch ON. Owner buka modal konfigurasi intent — "Cek status laundry" masih OFF, "Layanan outlet" masih ON. Bot berfungsi sesuai konfigurasi. |

### Detail SC-AI-025-01 - Konfigurasi Tetap Saat Outlet ON Kembali

#### Given

Outlet A Outlet Switch OFF. Sebelum OFF, outlet A punya konfigurasi: "Cek status laundry" OFF, "Layanan outlet" ON.

#### When

Owner toggle Outlet Switch A dari OFF ke ON.

#### Then

- Outlet Switch ON, indicator normal.
- Owner buka modal konfigurasi intent: "Cek status laundry" tetap OFF, "Layanan outlet" tetap ON.
- Bot kembali berfungsi sesuai konfigurasi.

#### Related Business Rules

- BR-AI-018: Konfigurasi Tetap Tersimpan Saat Outlet OFF

## 8. Supporting Information

Tidak ada.
