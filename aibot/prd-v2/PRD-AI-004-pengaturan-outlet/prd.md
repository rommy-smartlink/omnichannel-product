---
id: PRD-AI-004
title: Pengaturan Outlet
product: Smartlink
module: AI Bot Omnichannel
version: 1.0.0
status: Draft
owner: Product Owner
reviewers:
  - UIX
  - Engineering
  - QA
created_at: 2026-07-20
updated_at: 2026-07-20
---

# PRD-AI-004 - Pengaturan Outlet

## 1. Executive Summary

### Background

Customer sering menanyakan jam operasional outlet melalui WhatsApp — "buka jam berapa?", "Minggu buka?", "sekarang masih buka?". Tanpa sumber data jam operasional yang akurat, agent harus menjawab manual setiap pertanyaan, atau bot akan mengarang jawaban dari asumsi.

Owner membutuhkan tempat untuk mengonfigurasi jam operasional per outlet: zona waktu, jadwal mingguan, jadwal khusus (hari libur/perbaikan), serta kontrol kapan jadwal tersebut siap digunakan bot. Pengaturan ini menjadi sumber data resmi yang dikonsumsi oleh intent `get_operating_hours`.

PRD ini hanya mencakup pengaturan jam operasional outlet. Tidak mencakup pengaturan outlet lain (alamat, layanan, dsb) pada MVP.

### Current Condition

- Tidak ada menu pengaturan jam operasional outlet di sistem.
- Owner tidak bisa mengonfigurasi zona waktu, jadwal mingguan, atau jadwal khusus per outlet.
- Bot belum ada — semua pertanyaan jam operasional dijawab manual oleh agent.
- Tidak ada validasi atau prasyarat data sebelum informasi jam operasional diberikan ke customer.

### Problems

- PROB-AI-009: Owner tidak bisa mengonfigurasi jam operasional outlet → bot tidak punya sumber data akurat.
- PROB-AI-010: Tanpa zona waktu per outlet, bot tidak bisa menghitung "sekarang", "hari ini", "besok" dengan benar.
- PROB-AI-011: Tanpa jadwal khusus, bot akan memberikan informasi salah pada hari libur atau perbaikan outlet.

### Expected Condition

- Owner bisa mengatur zona waktu, jadwal mingguan, dan jadwal khusus untuk setiap outlet.
- Intent `get_operating_hours` default ON saat rilis. Semua outlet dianggap 24 jam sampai owner mengonfigurasi jadwal.
- Jadwal yang sudah valid menjadi sumber data bagi bot untuk menjawab pertanyaan jam operasional customer.
- Owner menyimpan jadwal melalui satu aksi: Simpan. Simpan hanya berhasil jika validasi terpenuhi.
- Perubahan jadwal yang belum disimpan tidak memengaruhi jawaban bot.

### Proposed Solution

Halaman Pengaturan Jam Operasional per outlet yang terdiri dari: zona waktu (dropdown wilayah), jadwal mingguan (Senin–Minggu: Buka/Tutup/24 Jam), jadwal khusus (tanggal/rentang dengan status Tutup/Buka khusus/24 Jam), preview jadwal, dan aksi Simpan.

## 2. Global Scope

### In Scope

- Halaman pengaturan jam operasional per outlet.
- Zona waktu outlet melalui dropdown berbasis wilayah (WIB/WITA/WIT).
- Jadwal mingguan Senin–Minggu: status Buka, Tutup, atau 24 Jam.
- Jadwal khusus berdasarkan rentang tanggal (satu tanggal = tanggal mulai = tanggal selesai).
- Jadwal khusus: status Tutup, Buka dengan jam khusus, atau 24 Jam.
- Validasi kelengkapan jadwal sebelum intent `get_operating_hours` dapat ON.
- Aksi Simpan.
- Preview jadwal sebelum disimpan.
- Jadwal tetap tersimpan saat intent dimatikan.
- Konfirmasi saat menghapus jadwal yang sedang aktif.
- Konfirmasi saat mengubah zona waktu dengan intent aktif.

### Out of Scope

- Pengaturan outlet selain jam operasional (alamat, layanan, dsb).
- Penjadwalan otomatis aktivasi/nonaktivasi intent bot.
- Sinkronisasi dengan Google Business Profile atau platform eksternal.
- Jadwal berbeda berdasarkan layanan di dalam outlet.
- Pengaturan kapasitas, antrean, atau cut-off penerimaan laundry.
- Pengaturan jam kerja karyawan atau shift kasir.
- Persetujuan berlapis sebelum perubahan jadwal berlaku.
- Riwayat versi lengkap dan pemulihan versi jadwal sebelumnya.
- Role outlet manager atau kasir untuk mengubah jadwal. Hak akses diatur melalui ACL.
- Notifikasi otomatis ke customer saat jadwal berubah.

## 3. Epic List

| Epic ID | Nama Epic | Tujuan Singkat | Status |
|---|---|---|---|
| EPIC-AI-004 | Pengaturan Outlet | Menyediakan halaman pengaturan jam operasional per outlet sebagai sumber data intent `get_operating_hours` | Draft |

## 4. References

| Type | URL / Reference | Description |
|---|---|---|
| Control Panel PRD | `aibot/prd-v2/PRD-AI-002-bot-control-panel/prd.md` | Relasi intent `get_operating_hours` dan prasyarat aktivasi |
| Intent get_operating_hours | `aibot/prd/P2-get_operating_hours/prd.md` | Konsumen data jam operasional |
| PRD existing | `aibot/prd/P8-pengaturan-jam-operasional/prd-pengaturan-jam-operasional-outlet.md` | Sumber konten utama |
| Bot Runtime Behavior | `aibot/prd-v2/PRD-AI-001-bot-runtime-behavior/prd.md` | Aturan greeting intent siap |
| Conversation Flow | `aibot/docs/conversation-flow.md` | Arsitektur alur percakapan |
| Intents | `aibot/docs/intents.md` | Daftar intent MVP |
| Figma | TBD | Board desain Pengaturan Outlet |
