---
id: US-AI-013
prd: PRD-AI-002
epic: EPIC-AI-002
feature: FEAT-AI-003
title: Menyimpan Perubahan Intent
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-17
updated_at: 2026-07-17
---

# US-AI-013 - Menyimpan Perubahan Intent

## 1. Parent Reference

- PRD: PRD-AI-002 - Bot Control Panel
- Epic: EPIC-AI-002 - Bot Control Panel
- Feature: FEAT-AI-003 - Konfigurasi Intent per Outlet

## 2. User Story

Sebagai owner,
Saya ingin menyimpan perubahan toggle intent melalui tombol Simpan yang hanya mengirim intent yang berubah ke server,
Agar pengaturan bot per outlet dapat diperbarui secara efisien.

## 3. Goal

Owner dapat menyimpan perubahan dengan PATCH minimal (hanya intent yang berubah). Sukses → toast + modal tertutup + daftar refresh. Gagal → toast error + modal tetap terbuka + perubahan lokal dipertahankan.

## 4. Acceptance Criteria

- Tombol Simpan mengirim PATCH ke server dengan payload hanya berisi intent yang berubah dari state database.
- PATCH server-side atomic per outlet — tidak ada partial update.
- Response 200: toast "Tersimpan", modal tertutup, daftar outlet refresh otomatis.
- Response error (network/5xx): toast "Gagal menyimpan. Coba lagi.", modal tetap terbuka, perubahan lokal dipertahankan untuk retry.

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-013-01 | Modal konfigurasi intent outlet A terbuka. Owner toggle "Cek status laundry" OFF→ON dan "Promo aktif" ON→OFF | Owner klik tombol Simpan | PATCH ke server dengan payload hanya berisi intent yang berubah. Response 200 → toast "Tersimpan", modal tertutup, daftar outlet refresh otomatis. Card outlet A menampilkan state terbaru. |
| SC-AI-013-02 | Modal konfigurasi intent outlet A terbuka. Owner sudah toggle 2 intent. Koneksi internet terputus atau server timeout | Owner klik Simpan | PATCH gagal. Toast error: "Gagal menyimpan. Coba lagi." Modal tidak tertutup. Perubahan lokal dipertahankan. Owner bisa retry. |

### Detail SC-AI-013-01 - Simpan Sukses

#### Given

Modal konfigurasi intent outlet A terbuka. Owner sudah mengubah: "Cek status laundry" OFF→ON, "Promo aktif" ON→OFF. State lokal: 5 ON, 1 OFF.

#### When

Owner klik tombol Simpan.

#### Then

1. Client kirim PATCH ke server dengan payload hanya intent yang berubah.
2. Server update data konfigurasi outlet + timestamp.
3. Response 200.
4. Toast "Tersimpan" muncul.
5. Modal tertutup.
6. Daftar outlet refresh otomatis. Card outlet A menampilkan state terbaru.

#### Related Business Rules

- BR-AI-013: Perubahan Hanya Intent yang Berubah

#### UI Behavior

- Tombol Simpan di footer modal, posisi kanan, style primary.
- Selama PATCH berlangsung, tombol dalam state loading/disabled untuk mencegah double submit.

### Detail SC-AI-013-02 - Simpan Gagal

#### Given

Modal konfigurasi intent outlet A terbuka. Owner sudah toggle 2 intent. Koneksi internet terputus atau server timeout.

#### When

Owner klik Simpan.

#### Then

1. Client kirim PATCH, request timeout/gagal.
2. Toast error: "Gagal menyimpan. Coba lagi."
3. Modal tidak tertutup.
4. Perubahan lokal dipertahankan — owner bisa klik Simpan lagi (retry).
5. State database tidak berubah sebagian (transaksi server-side rollback / atomic single PATCH).

## 8. Supporting Information

Tidak ada.
