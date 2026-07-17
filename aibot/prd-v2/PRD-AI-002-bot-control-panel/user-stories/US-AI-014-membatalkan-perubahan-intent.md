---
id: US-AI-014
prd: PRD-AI-002
epic: EPIC-AI-002
feature: FEAT-AI-003
title: Membatalkan Perubahan Intent
status: Draft
priority: Medium
uix_status: 0%
created_at: 2026-07-17
updated_at: 2026-07-17
---

# US-AI-014 - Membatalkan Perubahan Intent

## 1. Parent Reference

- PRD: PRD-AI-002 - Bot Control Panel
- Epic: EPIC-AI-002 - Bot Control Panel
- Feature: FEAT-AI-003 - Konfigurasi Intent per Outlet

## 2. User Story

Sebagai owner,
Saya ingin membatalkan perubahan toggle intent dengan tombol Batal,
Agar perubahan yang belum saya simpan di-discard dan modal tertutup tanpa efek ke database.

## 3. Goal

Owner dapat membatalkan perubahan dengan tombol Batal. Modal tertutup, perubahan lokal di-discard, tidak ada PATCH.

## 4. Acceptance Criteria

- Tombol Batal menutup modal.
- Semua perubahan lokal di-discard — toggle kembali ke state database.
- Tidak ada PATCH ke server.
- Card outlet di daftar tetap menampilkan state database (tidak berubah).

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-014-01 | Modal konfigurasi intent outlet A terbuka. Owner sudah toggle 2 intent (state lokal divergen dari state database) | Owner klik tombol Batal | Modal tertutup. Perubahan lokal di-discard. Tidak ada PATCH ke server. Card outlet A di daftar tetap menampilkan state database tanpa perubahan. |

### Detail SC-AI-014-01 - Batal dan Discard

#### Given

Modal konfigurasi intent outlet A terbuka. Owner toggle 2 intent: "Cek status laundry" OFF→ON, "Promo aktif" ON→OFF. State lokal divergen dari database.

#### When

Owner klik tombol Batal.

#### Then

- Modal tertutup.
- Semua perubahan lokal di-discard.
- Tidak ada PATCH ke server.
- Card outlet A di daftar tetap menampilkan state database (sebelum perubahan).

#### UI Behavior

- Tombol Batal di footer modal, posisi kiri, style secondary.
- Tidak ada dialog konfirmasi "Yakin ingin membatalkan?" — langsung tutup dan discard.

## 8. Supporting Information

Tidak ada.
