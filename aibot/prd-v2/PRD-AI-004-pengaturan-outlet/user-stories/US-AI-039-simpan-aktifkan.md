---
id: US-AI-039
prd: PRD-AI-004
epic: EPIC-AI-004
feature: FEAT-AI-012
title: Menyimpan Jadwal
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-20
updated_at: 2026-07-20
---

# US-AI-039 - Menyimpan Jadwal

## 1. Parent Reference

- PRD: PRD-AI-004 - Pengaturan Outlet
- Epic: EPIC-AI-004 - Pengaturan Outlet
- Feature: FEAT-AI-012 - Pengaturan Jam Operasional Outlet

## 2. User Story

Sebagai owner,
Saya ingin menyimpan jadwal operasional outlet melalui satu tombol Simpan,
Agar jadwal tersimpan dan bot langsung menggunakan data terbaru.

## 3. Goal

Owner menyimpan jadwal melalui satu CTA: Simpan. Sistem memvalidasi sebelum menyimpan. Setelah simpan, bot menggunakan jadwal baru. Intent tetap ON.

## 4. Acceptance Criteria

- Hanya satu CTA: "Simpan".
- Simpan menyimpan seluruh pengaturan: zona waktu, jadwal mingguan, jadwal khusus.
- Form selalu pre-filled dengan default valid (semua hari 24 Jam, zona waktu WIB). Simpan selalu enabled saat halaman pertama dibuka.
- Jika owner menghapus data wajib (misal menghapus zona waktu), Simpan disabled dengan pesan error.
- Setelah Simpan sukses: Toast "Jadwal tersimpan." Bot langsung menggunakan jadwal baru.
- Tombol Batal selalu tersedia, kembali tanpa menyimpan.
- Preview jadwal ditampilkan di atas CTA.

## 5. Form Rules

> Tidak ada form rules khusus untuk US ini. CTA behavior mengikuti kondisi validasi.

## 6. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-039-01 | Jadwal valid | Owner tekan "Simpan" | Jadwal tersimpan. Toast: "Jadwal tersimpan." Bot menggunakan jadwal baru. |
| SC-039-02 | Owner menghapus zona waktu dari dropdown | Owner lihat CTA | "Simpan" disabled. Error: "Zona waktu wajib dipilih." |
| SC-039-03 | Jadwal valid, owner ubah jadwal | Owner tekan "Simpan" | Perubahan tersimpan. Bot langsung menggunakan jadwal baru. |

### Detail SC-039-01 - Simpan Jadwal Valid

#### Given

Outlet A: zona waktu WIB, jadwal Senin–Minggu valid.

#### When

Owner menekan "Simpan".

#### Then

- Validasi lolos.
- Jadwal disimpan ke database.
- Status konfigurasi: "Disesuaikan".
- Toast sukses: "Jadwal tersimpan."
- Bot menggunakan jadwal baru.

#### Related Business Rules

- BR-AI-208: Intent Default ON, Outlet Default 24 Jam
- BR-AI-209: Satu Aksi Simpan

### Detail SC-039-02 - Hapus Zona Waktu

#### Given

Outlet A: zona waktu WIB, jadwal mingguan valid. Owner menghapus pilihan zona waktu dari dropdown.

#### When

Owner melihat bagian bawah halaman.

#### Then

- CTA "Simpan" dalam keadaan disabled.
- Error validasi: "Zona waktu wajib dipilih."
