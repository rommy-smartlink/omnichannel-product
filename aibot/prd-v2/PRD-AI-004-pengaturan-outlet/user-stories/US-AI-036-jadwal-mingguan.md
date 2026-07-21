---
id: US-AI-036
prd: PRD-AI-004
epic: EPIC-AI-004
feature: FEAT-AI-012
title: Mengatur Jadwal Mingguan
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-20
updated_at: 2026-07-20
---

# US-AI-036 - Mengatur Jadwal Mingguan

## 1. Parent Reference

- PRD: PRD-AI-004 - Pengaturan Outlet
- Epic: EPIC-AI-004 - Pengaturan Outlet
- Feature: FEAT-AI-012 - Pengaturan Jam Operasional Outlet

## 2. User Story

Sebagai owner,  
Saya ingin mengatur jadwal mingguan untuk setiap hari (Buka, Tutup, atau 24 Jam),
Agar jadwal operasional outlet terwakili dengan akurat.

## 3. Goal

Owner mengisi jadwal Senin–Minggu. Setiap hari bisa Buka (dengan jam buka–tutup), Tutup, atau 24 Jam. Ketujuh hari wajib memiliki status.

## 4. Acceptance Criteria

- Tabel jadwal menampilkan Senin–Minggu, masing-masing dengan tiga status pill: Buka, Tutup, 24 Jam.
- Hari berstatus Buka: wajib memiliki jam buka dan jam tutup.
- Hari berstatus Tutup: tidak menampilkan input jam.
- Hari berstatus 24 Jam: tidak menampilkan input jam.
- Input jam menggunakan format `HH.mm` (24 jam).
- Hari yang belum memiliki status tidak dapat disimpan.
- Saat status diubah dari Buka ke Tutup/24 Jam dan sudah ada jam, input jam dihapus setelah konfirmasi.
- Preview jadwal diperbarui secara real-time sesuai perubahan form.
- Jika jam tutup < jam buka, sistem menampilkan indikator `+1 hari`. Contoh: `18.00–02.00 (+1 hari)`.

## 5. Form Rules

| Label | Field Key | Type | Required | Unique | Max Length | Description |
|---|---|---|---:|---:|---:|---|
| Status hari | day_status | select (Buka/Tutup/24 Jam) | yes | no | — | Status operasional per hari |
| Jam buka | session_open | time (HH.mm) | yes (jika Buka) | no | — | Jam buka |
| Jam tutup | session_close | time (HH.mm) | yes (jika Buka) | no | — | Jam tutup |

## 6. Form Notes

- Default status: "24 Jam" untuk ketujuh hari.
- Indikator `+1 hari` adalah tampilan UI, bukan data tersimpan.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-036-01 | Semua hari default 24 Jam | Owner mengubah Senin–Jumat: Buka (08.00–21.00), Sabtu: Buka (08.00–18.00), Minggu: Tutup | Form menampilkan 7 hari dengan status baru. Preview menampilkan ringkasan. |
| SC-036-02 | Senin: Buka (08.00–21.00) | Owner ubah status jadi Tutup | Konfirmasi hapus jam. Jika dikonfirmasi, jam dihapus dan status jadi Tutup. |
| SC-036-03 | Sabtu: Buka, jam 08.00–08.00 | Owner tekan Simpan | Error: jam buka dan tutup tidak boleh sama. |
| SC-036-04 | Kamis: Buka, jam 08.00–02.00 | Owner lihat UI | Sistem menampilkan `08.00–02.00 (+1 hari)` |

### Detail SC-036-01 - Jadwal Normal

#### Given

Outlet baru. Semua hari default 24 Jam.

#### When

Owner mengatur: Senin–Jumat Buka 08.00–21.00, Sabtu Buka 08.00–18.00, Minggu Tutup.

#### Then

- Form menampilkan 7 baris dengan status dan jam sesuai input.
- Preview: "Senin–Jumat: 08.00–21.00, Sabtu: 08.00–18.00, Minggu: Tutup".
- Setelah zona waktu dipilih → jadwal valid.

#### Related Business Rules

- BR-AI-201: Jadwal Mingguan Wajib Lengkap
- BR-AI-202: Satu Sesi per Hari

### Detail SC-036-03 - Jam Sama

#### Given

Sabtu: Buka, jam 08.00–08.00.

#### When

Owner menekan Simpan.

#### Then

- Validasi gagal.
- Error: "Jam buka dan jam tutup tidak boleh sama. Jika outlet buka 24 jam, pilih status 24 Jam."
- Simpan diblokir.

#### Related Business Rules

- BR-AI-202: Satu Sesi per Hari

### Detail SC-036-04 - Lewat Tengah Malam

#### Given

Kamis: Buka, jam 08.00–02.00.

#### When

Owner melihat input jam.

#### Then

- Sistem otomatis menampilkan `08.00–02.00 (+1 hari)`.
- Indikator muncul real-time saat owner mengubah jam.
#### Error Message

"Jam operasional sesi 1 dan sesi 2 tidak boleh tumpang tindih."
