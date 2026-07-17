---
id: US-AI-008
prd: PRD-AI-002
epic: EPIC-AI-002
feature: FEAT-AI-002
title: Konfigurasi Outlet Tetap Saat Master OFF
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-17
updated_at: 2026-07-17
---

# US-AI-008 - Konfigurasi Outlet Tetap Saat Master OFF

## 1. Parent Reference

- PRD: PRD-AI-002 - Bot Control Panel
- Epic: EPIC-AI-002 - Bot Control Panel
- Feature: FEAT-AI-002 - Master Switch WABA

## 2. User Story

Sebagai owner,
Saya ingin konfigurasi intent per outlet tetap tersimpan saat master switch dimatikan,
Agar saya tidak perlu mengatur ulang semua outlet saat bot diaktifkan kembali.

## 3. Goal

Konfigurasi intent per outlet tidak dihapus, di-reset, atau dimodifikasi saat master switch OFF, dan berlaku normal saat master switch kembali ON.

## 4. Acceptance Criteria

- Database konfigurasi intent per outlet tidak berubah saat master switch di-toggle OFF.
- Database konfigurasi intent per outlet tidak berubah saat master switch di-toggle ON.
- Saat master switch kembali ON, bot engine memuat konfigurasi per outlet yang ada dan berfungsi normal.
- Owner bisa memverifikasi: buka modal konfigurasi intent saat master OFF, semua toggle sesuai pengaturan sebelumnya.

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-008-01 | Master switch WABA OFF. Outlet A memiliki konfigurasi "Cek status laundry" OFF dan "Layanan outlet" ON | Owner toggle master switch dari OFF ke ON | Master switch ON. Owner membuka modal konfigurasi intent outlet A — toggle "Cek status laundry" masih OFF dan "Layanan outlet" masih ON (tidak berubah). Bot kembali berfungsi. |
| SC-AI-008-02 | Master switch WABA ON. Outlet A dan outlet B memiliki konfigurasi berbeda | Owner toggle master switch ke OFF | Toggle ke OFF. Konfigurasi untuk outlet A dan outlet B tetap utuh — tidak ada row yang dimodifikasi di database pengaturan. |

### Detail SC-AI-008-01 - Konfigurasi Berlaku Saat Master ON Kembali

#### Given

Master switch OFF. Outlet A memiliki pengaturan "Cek status laundry" OFF, "Layanan outlet" ON dari sebelum master OFF.

#### When

Owner toggle master switch dari OFF ke ON.

#### Then

- Master switch ON.
- Owner membuka modal konfigurasi intent outlet A: "Cek status laundry" tetap OFF, "Layanan outlet" tetap ON.
- Bot kembali berfungsi normal sesuai konfigurasi.

#### Related Business Rules

- BR-AI-002: Konfigurasi Tetap Tersimpan Saat Master OFF

### Detail SC-AI-008-02 - Konfigurasi Multi Outlet Tidak Berubah

#### Given

Master switch ON. Outlet A dan outlet B memiliki konfigurasi intent berbeda.

#### When

Owner toggle master switch ke OFF.

#### Then

- Master switch OFF.
- Konfigurasi outlet A dan outlet B di database tidak berubah.

#### Related Business Rules

- BR-AI-002: Konfigurasi Tetap Tersimpan Saat Master OFF

## 8. Supporting Information

Tidak ada.
