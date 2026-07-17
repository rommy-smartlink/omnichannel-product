---
id: US-AI-009
prd: PRD-AI-002
epic: EPIC-AI-002
feature: FEAT-AI-002
title: Edit Konfigurasi Saat Master OFF
status: Draft
priority: Medium
uix_status: 0%
created_at: 2026-07-17
updated_at: 2026-07-17
---

# US-AI-009 - Edit Konfigurasi Saat Master OFF

## 1. Parent Reference

- PRD: PRD-AI-002 - Bot Control Panel
- Epic: EPIC-AI-002 - Bot Control Panel
- Feature: FEAT-AI-002 - Master Switch WABA

## 2. User Story

Sebagai owner,
Saya ingin tetap bisa membuka modal konfigurasi intent dan mengubah pengaturan intent meskipun master switch dalam keadaan OFF,
Agar saya bisa menyiapkan pengaturan outlet tanpa harus mengaktifkan bot dulu.

## 3. Goal

Owner tetap dapat mengakses dan mengubah konfigurasi intent per outlet saat master switch OFF. Perubahan disimpan ke database dan berlaku saat master switch kembali ON.

## 4. Acceptance Criteria

- Modal konfigurasi intent tetap dapat dibuka dengan tap/click pada card outlet saat master OFF.
- Toggle intent bisa diubah dan disimpan seperti biasa.
- Tombol Simpan mengirim PATCH dan menyimpan perubahan ke database.
- Perubahan berlaku saat master switch kembali ON.
- Tidak ada perbedaan UI di modal konfigurasi intent antara master ON dan OFF.

## 5. Form Rules

> Tidak ada form tambahan di luar yang sudah didefinisikan di FEAT-AI-003.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-009-01 | Master switch OFF. Outlet A memiliki intent "Cek status laundry" OFF | Owner buka modal konfigurasi intent outlet A, toggle "Cek status laundry" ke ON, klik Simpan | PATCH ke server sukses. Konfigurasi intent "Cek status laundry" outlet A tersimpan sebagai ON di database. Toast "Tersimpan". Saat master switch nanti di-ON-kan, intent "Cek status laundry" outlet A akan ON. |

### Detail SC-AI-009-01 - Edit Konfigurasi Saat Master OFF

#### Given

Master switch OFF. Outlet A memiliki konfigurasi intent "Cek status laundry" OFF.

#### When

Owner buka modal konfigurasi intent outlet A, ubah toggle "Cek status laundry" dari OFF ke ON, klik Simpan.

#### Then

- PATCH ke server sukses.
- Konfigurasi outlet A tersimpan dengan intent "Cek status laundry" ON di database.
- Toast "Tersimpan" tampil.
- Namun bot belum berfungsi karena master switch masih OFF.
- Saat master switch di-ON-kan nanti, intent "Cek status laundry" outlet A akan ON dan bot berfungsi sesuai konfigurasi terbaru.

#### Related Business Rules

- BR-AI-006: Edit Konfigurasi Tetap Diizinkan Saat Master OFF

#### UI Behavior

- Tidak ada perbedaan UI modal konfigurasi intent antara master ON dan OFF.

## 8. Supporting Information

Tidak ada.
