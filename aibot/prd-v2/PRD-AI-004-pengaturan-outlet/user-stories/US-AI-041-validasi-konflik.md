---
id: US-AI-041
prd: PRD-AI-004
epic: EPIC-AI-004
feature: FEAT-AI-012
title: Validasi Konflik & Hapus Jadwal Aktif
status: Draft
priority: Medium
uix_status: 0%
created_at: 2026-07-20
updated_at: 2026-07-20
---

# US-AI-041 - Validasi Konflik & Hapus Jadwal Aktif

## 1. Parent Reference

- PRD: PRD-AI-004 - Pengaturan Outlet
- Epic: EPIC-AI-004 - Pengaturan Outlet
- Feature: FEAT-AI-012 - Pengaturan Jam Operasional Outlet

## 2. User Story

Sebagai owner,  
Saya ingin sistem mencegah jadwal khusus yang tumpang tindih dan mengonfirmasi sebelum menghapus jadwal yang sedang aktif,  
Agar data jam operasional selalu konsisten dan tidak ada kejutan kehilangan konfigurasi.

## 3. Goal

Sistem memvalidasi konflik jadwal khusus (rentang tidak boleh tumpang tindih), menampilkan error jika terjadi, dan mengonfirmasi pemilik sebelum menghapus jadwal yang membuat konfigurasi tidak valid saat intent ON.

## 4. Acceptance Criteria

- Saat menyimpan jadwal khusus baru atau mengedit yang sudah ada, sistem memeriksa tumpang tindih dengan jadwal khusus lain.
- Jika rentang tumpang tindih: simpan diblokir, error ditampilkan. Owner harus mengubah atau menghapus jadwal yang konflik terlebih dahulu.
- Validasi tumpang tindih: dua rentang dikatakan tumpang tindih jika ada tanggal yang sama di kedua rentang.
- Saat owner melakukan tindakan yang membuat jadwal tidak valid (menghapus zona waktu): konfirmasi muncul karena simpan tidak bisa dilakukan.

## 5. Form Rules

> Tidak ada form rules khusus. Validasi dilakukan server-side dan client-side.

## 6. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-041-01 | Jadwal khusus: 20–26 Juli Tutup | Owner tambah jadwal khusus: 24–28 Juli Buka | Error: rentang tumpang tindih dengan jadwal 20–26 Juli. |
| SC-041-02 | Jadwal khusus: 17 Agustus Tutup | Owner tambah jadwal khusus: 17 Agustus 24 Jam | Error: rentang tumpang tindih (tanggal sama). |
| SC-041-03 | Jadwal valid | Owner hapus semua input jam pada hari berstatus Buka lalu Simpan | Simpan diblokir. Error validasi: jadwal mingguan wajib lengkap. |
| SC-041-04 | Jadwal valid | Owner hapus zona waktu lalu Simpan | Simpan diblokir. Error validasi: zona waktu wajib dipilih. |

### Detail SC-041-01 - Rentang Tumpang Tindih

#### Given

Outlet A memiliki jadwal khusus "Perbaikan outlet": Tutup, 20–26 Juli 2026.

#### When

Owner membuka form Tambah Jadwal Khusus, mengisi: Buka dengan jam khusus, 24–28 Juli 2026. Menekan Simpan.

#### Then

- Validasi mendeteksi tumpang tindih: 24, 25, 26 Juli ada di kedua rentang.
- Simpan diblokir.
- Error: "Jadwal khusus ini tumpang tindih dengan 'Perbaikan outlet' (20–26 Juli 2026). Ubah tanggal atau hapus jadwal yang bertabrakan."
- Form tetap terbuka agar owner bisa mengubah tanggal.

#### Related Business Rules

- BR-AI-205: Jadwal Khusus Tidak Boleh Tumpang Tindih

#### Error Message

"Jadwal khusus ini tumpang tindih dengan '[judul jadwal existing]' ([rentang]). Silakan ubah tanggal atau hapus jadwal yang bertabrakan terlebih dahulu."

### Detail SC-041-03 - Hapus Jadwal Tidak Valid

#### Given

Outlet B: jadwal valid.

#### When

Owner menghapus semua input jam (jam buka dan jam tutup) dari hari berstatus Buka, lalu menekan "Simpan".

#### Then

- Simpan diblokir.
- Error: "Jadwal mingguan wajib diisi untuk 7 hari."
- Form tetap terbuka agar owner bisa memperbaiki.

#### Related Business Rules

- BR-AI-201: Jadwal Mingguan Wajib Lengkap
