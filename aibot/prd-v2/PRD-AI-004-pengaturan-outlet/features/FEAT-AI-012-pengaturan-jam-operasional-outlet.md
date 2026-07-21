---
id: FEAT-AI-012
prd: PRD-AI-004
epic: EPIC-AI-004
title: Pengaturan Jam Operasional Outlet
status: Draft
owner: Product Owner
created_at: 2026-07-20
updated_at: 2026-07-20
---

# FEAT-AI-012 - Pengaturan Jam Operasional Outlet

## 1. Purpose

Owner dapat mengonfigurasi jam operasional untuk setiap outlet: memilih zona waktu, mengatur jadwal mingguan (Buka/Tutup/24 Jam), membuat jadwal khusus (rentang tanggal dengan status Tutup/Buka khusus/24 Jam), melihat preview, serta menyimpan jadwal. Intent `get_operating_hours` default ON saat rilis; semua outlet dianggap 24 jam sampai owner mengonfigurasi jadwal yang berbeda. Jadwal yang valid menjadi sumber data bagi intent `get_operating_hours`.

## 2. Scope

### In Scope

- Halaman pengaturan jam operasional per outlet, diakses dari menu Pengaturan Outlet atau dari Control Panel.
- Status konfigurasi: Default 24 Jam, Disesuaikan.
- Zona waktu outlet: dropdown wilayah (WIB — Asia/Jakarta, WITA — Asia/Makassar, WIT — Asia/Jayapura) dengan UTC offset sebagai informasi.
- Jadwal mingguan Senin–Minggu dengan tiga status: Buka, Tutup, 24 Jam.
- Jadwal khusus: tambah/edit/hapus dengan model rentang tanggal (detail lihat BR-AI-204).
- Status jadwal khusus: Tutup, Buka dengan jam khusus, 24 Jam.
- Judul jadwal khusus (wajib) dan catatan customer-facing (opsional).
- Prioritas jadwal: jadwal khusus > jadwal mingguan.
- Validasi sebelum simpan: zona waktu wajib, 7 hari lengkap, jam valid, rentang jadwal khusus tidak konflik.
- Preview jadwal sebelum disimpan.
- Satu CTA: Simpan.
- Konfirmasi saat perubahan menyebabkan jadwal tidak valid (misal menghapus zona waktu).
- Konfirmasi meninggalkan halaman dengan perubahan belum disimpan.

### Out of Scope

- Setup jam operasional langsung di dalam modal Control Panel.
- Bulk setup jam operasional untuk banyak outlet.
- Jadwal berbeda per layanan di dalam outlet.
- Sinkronisasi eksternal (Google Business Profile, dsb).
- Riwayat versi jadwal.
- Role outlet manager/kasir. Hak akses diatur melalui ACL.

## 3. Business Rules

### BR-AI-200 — Zona Waktu Wajib

Zona waktu wajib dipilih sebelum jadwal dapat disimpan sebagai konfigurasi valid. Default: WIB — Asia/Jakarta (UTC+07:00). Dropdown menggunakan format `[nama zona] — [wilayah] ([UTC offset])`. Pilihan: WIB — Asia/Jakarta (UTC+07:00), WITA — Asia/Makassar (UTC+08:00), WIT — Asia/Jayapura (UTC+09:00). Tidak menyediakan input bebas GMT.

### BR-AI-201 — Jadwal Mingguan Wajib Lengkap

Ketujuh hari (Senin–Minggu) wajib memiliki status (Buka/Tutup/24 Jam). Hari berstatus Buka wajib memiliki jam buka dan jam tutup. Hari berstatus Tutup atau 24 Jam tidak menampilkan input jam.

### BR-AI-204 — Rentang Jadwal Khusus

Setiap jadwal khusus memiliki Tanggal Mulai dan Tanggal Selesai. Jadwal berlaku inklusif untuk semua tanggal dalam rentang tersebut. Detail:

- **Satu hari**: Jika Tanggal Mulai = Tanggal Selesai → jadwal khusus berlaku hanya untuk 1 hari.
- **Beberapa hari**: Jika Tanggal Selesai > Tanggal Mulai → jadwal khusus berlaku untuk seluruh hari di antaranya (inklusif).
- **Tidak boleh**: Tanggal Selesai sebelum Tanggal Mulai.
- **Default UX**: Saat owner memilih Tanggal Mulai, Tanggal Selesai otomatis diisi sama dengan Tanggal Mulai. Owner bisa mengubah Tanggal Selesai jika ingin membuat rentang beberapa hari.

### BR-AI-205 — Jadwal Khusus Tidak Boleh Tumpang Tindih

Rentang jadwal khusus tidak boleh tumpang tindih dengan jadwal khusus lain. Jika terjadi konflik, sistem menolak simpan dan meminta owner mengubah atau menghapus salah satu jadwal.

### BR-AI-206 — Prioritas Jadwal Khusus

Jadwal khusus yang rentangnya mencakup tanggal referensi selalu diprioritaskan dibanding jadwal mingguan. Setelah rentang jadwal khusus berakhir, jadwal mingguan otomatis berlaku kembali.

### BR-AI-207 — Intent Tidak Otomatis OFF saat Penutupan Sementara

Penutupan sementara (jadwal khusus Tutup) tidak otomatis menonaktifkan intent `get_operating_hours`. Intent tetap ON agar bot dapat menjelaskan bahwa outlet sedang tutup dan kapan buka kembali.

### BR-AI-208 — Intent Default ON, Outlet Default 24 Jam

Intent `get_operating_hours` default ON saat rilis. Semua outlet dianggap 24 jam sampai owner mengonfigurasi jadwal yang berbeda. FE pre-fill semua hari = 24 Jam, zona waktu = WIB. Owner dapat menonaktifkan intent kapan saja dari Control Panel.

### BR-AI-209 — Satu Aksi Simpan

Hanya satu CTA: Simpan. Tidak ada Simpan Saja atau Simpan & Aktifkan karena intent sudah ON. Simpan hanya berhasil jika semua validasi terpenuhi (zona waktu dipilih, jadwal mingguan lengkap, tidak ada konflik). Karena FE pre-fill default (semua hari 24 Jam, zona waktu WIB), form selalu valid saat pertama dibuka — tidak ada state "Belum lengkap".

### BR-AI-210 — Jadwal Tetap Tersimpan Saat Intent OFF

Mematikan intent `get_operating_hours` dari Control Panel tidak menghapus jadwal. Jadwal tetap dapat dikelola dan digunakan kembali saat intent diaktifkan lagi.

### BR-AI-211 — Konfirmasi Jadwal Tidak Valid

Jika owner menghapus zona waktu (set ke kosong) saat intent ON, jadwal menjadi tidak valid. Sistem menampilkan konfirmasi: "Nonaktifkan Jam Operasional? Perubahan ini membuat jadwal outlet tidak dapat digunakan oleh bot. Intent Jam Operasional akan dinonaktifkan." Owner memilih Batal atau Hapus & Nonaktifkan.

### BR-AI-212 — Konfirmasi Ubah Zona Waktu Saat Intent Aktif

Jika zona waktu diubah saat intent ON, sistem menampilkan konfirmasi yang menjelaskan: jam buka/tutup tidak bergeser otomatis, pastikan jadwal masih sesuai untuk waktu lokal baru.

### BR-AI-213 — Perubahan Belum Disimpan Tidak Memengaruhi Bot

Perubahan pada form hanya berlaku setelah Simpan ditekan. Bot hanya menggunakan jadwal yang sudah tersimpan di database. Owner menutup halaman dengan perubahan belum disimpan → konfirmasi meninggalkan halaman.

### BR-AI-214 — Default 24 Jam untuk Outlet Existing

Saat fitur rilis, seluruh outlet yang belum memiliki konfigurasi jam operasional dianggap default 24 jam. Status konfigurasi: "Default 24 Jam". FE menampilkan pre-fill: zona waktu WIB, semua hari 24 Jam. Intent tetap ON.

## 4. Dependencies

- Control Panel (PRD-AI-002): toggle intent `get_operating_hours`, CTA navigasi "Atur Jam Operasional", dialog prasyarat.
- Intent `get_operating_hours` (PRD-AI-003, FEAT-AI-007): konsumen data jam operasional.
- Bot Runtime Behavior (PRD-AI-001): greeting hanya menampilkan intent yang siap.
- Data outlet omnichannel.

## 5. User Story List

| US ID | Judul US | Goal | Status |
|---|---|---|---|
| US-AI-034 | Melihat Status Konfigurasi Jam Operasional | Owner melihat status konfigurasi (Default 24 Jam/Disesuaikan) | Draft |
| US-AI-035 | Memilih Zona Waktu Outlet | Owner memilih zona waktu outlet melalui dropdown wilayah | Draft |
| US-AI-036 | Mengatur Jadwal Mingguan | Owner mengatur status Buka/Tutup/24 Jam dan sesi operasional untuk Senin–Minggu | Draft |
| US-AI-037 | Jadwal Melewati Tengah Malam | Sistem menampilkan indikator `+1 hari` saat jam tutup lebih kecil dari jam buka | Draft |
| US-AI-038 | Membuat Jadwal Khusus | Owner membuat jadwal khusus dengan rentang tanggal, status, judul, dan catatan | Draft |
| US-AI-039 | Menyimpan Jadwal | Owner menyimpan jadwal dan melihat hasilnya | Draft |
| US-AI-040 | Mengubah Jadwal Saat Intent Aktif | Owner mengubah jadwal yang sudah aktif dan menyimpan perubahan tanpa menonaktifkan intent | Draft |
| US-AI-041 | Validasi Konflik | Sistem memvalidasi konflik jadwal khusus | Draft |
| US-AI-042 | Navigasi Pengaturan dari Control Panel | Owner membuka halaman pengaturan dari Control Panel | Draft |

## 6. Notes for Developer

- Halaman pengaturan per outlet — tidak ada bulk edit.
- Dropdown zona waktu dapat dicari dengan kata kunci seperti "Jakarta", "WIB", "UTC+7", "Indonesia".
- Format jam menggunakan `HH.mm` (24 jam).
- Preview jadwal menampilkan zona waktu, ringkasan jadwal mingguan, dan jadwal khusus yang akan datang.
- PATCH simpan hanya mengirim data yang berubah.
