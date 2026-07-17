---
id: US-AI-026
prd: PRD-AI-002
epic: EPIC-AI-002
feature: FEAT-AI-005
title: Edit Konfigurasi Saat Outlet OFF
status: Draft
priority: Medium
uix_status: 0%
created_at: 2026-07-17
updated_at: 2026-07-17
---

# US-AI-026 - Edit Konfigurasi Saat Outlet OFF

## 1. Parent Reference

- PRD: PRD-AI-002 - Bot Control Panel
- Epic: EPIC-AI-002 - Bot Control Panel
- Feature: FEAT-AI-005 - Outlet Switch

## 2. User Story

Sebagai owner,
Saya ingin tetap bisa membuka modal konfigurasi intent dan mengubah pengaturan intent meskipun Outlet Switch dalam keadaan OFF,
Agar saya bisa menyiapkan pengaturan outlet tanpa harus mengaktifkan bot dulu.

## 3. Goal

Owner tetap dapat mengakses dan mengubah konfigurasi intent outlet saat Outlet Switch OFF. Perubahan disimpan ke database dan berlaku saat Outlet Switch kembali ON.

## 4. Acceptance Criteria

- Modal konfigurasi intent tetap dapat dibuka saat Outlet Switch OFF.
- Toggle intent bisa diubah dan disimpan seperti biasa.
- Tombol Simpan mengirim PATCH dan menyimpan perubahan ke database.
- Perubahan berlaku saat Outlet Switch kembali ON.
- Tidak ada perbedaan UI di modal konfigurasi intent antara Outlet ON dan OFF.

## 5. Form Rules

> Tidak ada form tambahan di luar yang sudah didefinisikan di FEAT-AI-003.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-026-01 | Outlet A Outlet Switch OFF. Intent "Cek status laundry" OFF | Owner buka modal konfigurasi intent, toggle "Cek status laundry" ON, klik Simpan | PATCH sukses. Konfigurasi "Cek status laundry" ON tersimpan. Saat Outlet Switch nanti ON, "Cek status laundry" akan ON. |

### Detail SC-AI-026-01 - Edit Konfigurasi Saat Outlet OFF

#### Given

Outlet A Outlet Switch OFF. Konfigurasi intent "Cek status laundry" OFF.

#### When

Owner buka modal konfigurasi intent outlet A, ubah toggle "Cek status laundry" dari OFF ke ON, klik Simpan.

#### Then

- PATCH ke server sukses.
- Konfigurasi tersimpan dengan intent "Cek status laundry" ON di database.
- Toast "Tersimpan" tampil.
- Bot belum berfungsi karena Outlet Switch masih OFF.
- Saat Outlet Switch di-ON-kan, intent "Cek status laundry" akan ON.

#### Related Business Rules

- BR-AI-022: Edit Konfigurasi Tetap Diizinkan Saat Outlet OFF

## 8. Supporting Information

Tidak ada.
