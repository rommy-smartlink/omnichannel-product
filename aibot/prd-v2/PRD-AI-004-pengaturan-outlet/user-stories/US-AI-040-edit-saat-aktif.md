---
id: US-AI-040
prd: PRD-AI-004
epic: EPIC-AI-004
feature: FEAT-AI-012
title: Mengubah Jadwal Saat Intent Aktif
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-20
updated_at: 2026-07-20
---

# US-AI-040 - Mengubah Jadwal Saat Intent Aktif

## 1. Parent Reference

- PRD: PRD-AI-004 - Pengaturan Outlet
- Epic: EPIC-AI-004 - Pengaturan Outlet
- Feature: FEAT-AI-012 - Pengaturan Jam Operasional Outlet

## 2. User Story

Sebagai owner,  
Saya ingin mengubah jadwal outlet yang sedang aktif (intent ON) dan menyimpan perubahan tanpa menonaktifkan intent,  
Agar jadwal tetap up-to-date tanpa mengganggu layanan bot ke customer.

## 3. Goal

Owner melakukan perubahan pada jadwal yang sedang aktif. Perubahan hanya berlaku setelah "Simpan Perubahan" ditekan. Selama proses edit, bot tetap menggunakan jadwal lama yang tersimpan. Intent tetap ON setelah simpan selama jadwal hasil perubahan masih valid.

## 4. Acceptance Criteria

- Hanya satu CTA: "Simpan".
- Selama form diedit dan belum disimpan, bot tetap menggunakan jadwal versi database (BR-AI-213).
- Setelah Simpan berhasil, jadwal baru langsung menjadi sumber jawaban bot.
- Intent tetap ON setelah simpan selama jadwal valid.
- Owner menutup halaman dengan perubahan belum disimpan → konfirmasi "Tinggalkan halaman? Perubahan yang belum disimpan akan hilang."
- Jika perubahan membuat jadwal tidak valid (misal menghapus zona waktu): konfirmasi muncul (BR-AI-211).

## 5. Form Rules

> Tidak ada form rules khusus. Menggunakan field yang sama dengan US-AI-035, US-AI-036, US-AI-038.

## 6. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-040-01 | Jadwal valid, intent ON | Owner ubah jam Sabtu dari 08.00–18.00 jadi 08.00–20.00, lalu Simpan | Jadwal tersimpan. Bot menggunakan jadwal baru. |
| SC-040-02 | Jadwal valid, intent ON | Owner tambah jadwal khusus 17 Agustus Tutup, lalu Simpan | Jadwal khusus tersimpan. Bot akan menggunakan jadwal khusus untuk 17 Agustus. |
| SC-040-03 | Jadwal valid, intent ON | Owner ubah beberapa field, lalu tekan Batal | Semua perubahan dibuang. Form kembali ke data tersimpan. |
| SC-040-04 | Jadwal valid, intent ON | Owner hapus zona waktu, lalu Simpan | Konfirmasi muncul (BR-AI-211). |
| SC-040-05 | Jadwal valid, intent ON | Owner ubah jadwal, belum simpan, lalu tutup tab/browser | Konfirmasi "Tinggalkan halaman?" muncul. |

### Detail SC-040-01 - Edit Normal Saat Aktif

#### Given

Outlet B: jadwal valid, intent `get_operating_hours` ON. Sabtu: 08.00–18.00.

#### When

1. Owner mengubah jam Sabtu menjadi 08.00–20.00.
2. Owner menekan "Simpan".

#### Then

- Validasi lolos.
- Jadwal tersimpan di database.
- Intent tetap ON.
- Toast: "Perubahan disimpan."
- Customer yang bertanya jam operasional Sabtu akan mendapat jawaban 08.00–20.00 (jadwal baru).
- Customer yang sedang dalam percakapan sebelum simpan tetap mendapat jawaban dari jadwal lama untuk respons yang sudah diproses.

#### Related Business Rules

- BR-AI-209: Satu Aksi Simpan
- BR-AI-213: Perubahan Belum Disimpan Tidak Memengaruhi Bot

### Detail SC-040-04 - Menghapus Data Membuat Tidak Valid

#### Given

Outlet B: jadwal valid, intent ON.

#### When

Owner menghapus atau mengosongkan zona waktu, lalu menekan "Simpan Perubahan".

#### Then

- Sistem mendeteksi perubahan membuat jadwal tidak valid.
- Konfirmasi muncul: "Nonaktifkan Jam Operasional? Perubahan ini membuat jadwal outlet tidak dapat digunakan oleh bot. Intent Jam Operasional akan dinonaktifkan."
- "Batal" → kembali ke form, perubahan dibatalkan.
- "Hapus & Nonaktifkan" → jadwal disimpan (draft), intent OFF.

#### Related Business Rules

- BR-AI-200: Zona Waktu Wajib
- BR-AI-211: Konfirmasi Jadwal Tidak Valid
