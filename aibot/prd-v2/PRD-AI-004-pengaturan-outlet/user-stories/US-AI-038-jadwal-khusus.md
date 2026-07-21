---
id: US-AI-038
prd: PRD-AI-004
epic: EPIC-AI-004
feature: FEAT-AI-012
title: Membuat Jadwal Khusus
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-20
updated_at: 2026-07-20
---

# US-AI-038 - Membuat Jadwal Khusus

## 1. Parent Reference

- PRD: PRD-AI-004 - Pengaturan Outlet
- Epic: EPIC-AI-004 - Pengaturan Outlet
- Feature: FEAT-AI-012 - Pengaturan Jam Operasional Outlet

## 2. User Story

Sebagai owner,  
Saya ingin membuat jadwal khusus untuk tanggal atau rentang tanggal tertentu (hari libur, perbaikan, jam khusus),  
Agar jadwal mingguan tidak memberikan informasi yang salah pada tanggal-tanggal pengecualian.

## 3. Goal

Owner menambahkan jadwal khusus melalui form dengan: status, rentang tanggal (tanggal mulai = tanggal selesai untuk satu hari, tanggal selesai > tanggal mulai untuk periode), judul, dan catatan customer-facing opsional. Jadwal khusus diprioritaskan dibanding jadwal mingguan.

## 4. Acceptance Criteria

- Tombol "+ Tambah Jadwal Khusus" di bawah jadwal mingguan.
- Form tambah jadwal khusus: status (Tutup/Buka dengan jam khusus/24 Jam), tanggal mulai, tanggal selesai, judul, catatan customer-facing (opsional).
- Tanggal selesai otomatis mengisi sama dengan tanggal mulai saat tanggal mulai dipilih.
- Tanggal selesai dapat diubah menjadi lebih besar dari tanggal mulai untuk rentang beberapa hari.
- Status "Buka dengan jam khusus": form menampilkan input jam buka dan tutup.
- Daftar jadwal khusus menampilkan: rentang tanggal, status, judul.
- Format daftar: "17 Agustus 2026 — Tutup · Hari Kemerdekaan" atau "20–26 Juli 2026 — Tutup · Perbaikan outlet".
- Jadwal khusus yang sudah lewat tetap dapat dilihat sebagai riwayat ringan.
- Tombol hapus tersedia di setiap jadwal khusus.
- Tombol edit (opsional untuk MVP, bisa pakai hapus + tambah baru).

## 5. Form Rules

| Label | Field Key | Type | Required | Unique | Max Length | Description |
|---|---|---|---:|---:|---:|---|
| Status jadwal | special_status | select (Tutup/Buka khusus/24 Jam) | yes | no | — | Status operasional selama periode |
| Tanggal mulai | date_start | date | yes | no | — | Tanggal pertama jadwal berlaku |
| Tanggal selesai | date_end | date | yes | no | — | Default = date_start, bisa > date_start |
| Jam buka | spec_session_open | time | kondisional (Buka khusus) | no | — | — |
| Jam tutup | spec_session_close | time | kondisional (Buka khusus) | no | — | — |
| Judul | title | string | yes | no | 100 | Nama singkat seperti "Hari Kemerdekaan" |
| Catatan customer | customer_note | string | no | no | 200 | Penjelasan untuk customer |

## 6. Form Notes

- Status "Tutup" dan "24 Jam": input jam tidak ditampilkan.
- Status "Buka khusus": input jam wajib diisi.
- Tanggal selesai tidak boleh < tanggal mulai.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-038-01 | Belum ada jadwal khusus | Owner tambah: Tutup, 17 Agustus 2026, "Hari Kemerdekaan" | Disimpan. Daftar menampilkan "17 Agustus 2026 — Tutup · Hari Kemerdekaan" |
| SC-038-02 | Belum ada jadwal khusus | Owner tambah: Tutup, 20–26 Juli 2026, "Perbaikan outlet", catatan "Outlet tutup sementara karena perbaikan." | Disimpan. Daftar menampilkan "20–26 Juli 2026 — Tutup · Perbaikan outlet" |
| SC-038-03 | Owner ingin satu hari | Owner pilih tanggal mulai 17 Agustus 2026 | Tanggal selesai otomatis terisi 17 Agustus 2026 |
| SC-038-04 | Owner ingin periode | Owner pilih tanggal mulai 20 Juli, lalu ubah tanggal selesai ke 26 Juli | Rentang berlaku 20–26 Juli 2026 |
| SC-038-05 | Owner pilih "Buka dengan jam khusus" | Owner isi jam 09.00–15.00 untuk 17 Agustus 2026 | Jadwal khusus buka dengan jam terbatas pada tanggal tersebut |
| SC-038-06 | Tanggal selesai < tanggal mulai | Owner pilih mulai 20 Juli, selesai 15 Juli | Error validasi. Simpan diblokir. |

### Detail SC-038-01 - Jadwal Khusus Satu Hari

#### Given

Outlet A. Jadwal mingguan: Senin–Sabtu Buka, Minggu Tutup.

#### When

Owner menambah jadwal khusus: Tutup, 17 Agustus 2026, judul "Hari Kemerdekaan".

#### Then

- Form tertutup, daftar jadwal khusus bertambah.
- Daftar menampilkan: "17 Agustus 2026 — Tutup · Hari Kemerdekaan".
- Preview memperhitungkan jadwal khusus: pada 17 Agustus, outlet Tutup (override jadwal mingguan).

#### Related Business Rules

- BR-AI-204: Rentang Jadwal Khusus
- BR-AI-206: Prioritas Jadwal Khusus

### Detail SC-038-02 - Jadwal Khusus Rentang

#### Given

Outlet A.

#### When

Owner menambah jadwal khusus: Tutup, 20–26 Juli 2026, judul "Perbaikan outlet", catatan "Outlet tutup sementara karena perbaikan."

#### Then

- Daftar menampilkan: "20–26 Juli 2026 — Tutup · Perbaikan outlet".
- Preview: pada 20–26 Juli, outlet Tutup.

#### Related Business Rules

- BR-AI-204: Rentang Jadwal Khusus
- BR-AI-206: Prioritas Jadwal Khusus
- BR-AI-207: Intent Tidak Otomatis OFF saat Penutupan Sementara

#### Notes

Catatan customer-facing akan digunakan bot saat customer bertanya tentang penutupan. Contoh respons bot: "Outlet tutup sementara pada 20–26 Juli 2026 karena perbaikan outlet."
