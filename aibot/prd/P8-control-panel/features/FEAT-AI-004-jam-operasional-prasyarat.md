---
id: FEAT-AI-004
prd: PRD-AI-002
epic: EPIC-AI-002
title: Jam Operasional — Default 24 Jam
status: Draft
owner: Product Owner
created_at: 2026-07-16
updated_at: 2026-07-16
---

# FEAT-AI-004 - Jam Operasional — Default 24 Jam

## 1. Purpose

Intent `get_operating_hours` bisa langsung ON tanpa prasyarat. Jika konfigurasi jam operasional belum diatur oleh owner (karena fitur baru), sistem mengasumsikan outlet beroperasi 24 jam. Owner dapat mengatur jadwal spesifik kapan pun melalui halaman Pengaturan Jam Operasional.

Card outlet hanya menampilkan **24 Jam** jika outlet menggunakan skema default. Jika outlet memiliki jadwal kustom (zona waktu, jadwal per hari, sesi), card tidak menampilkan info jam operasional — detail tampil di modal capability.

## 2. Scope

### In Scope

- Toggle Jam Operasional bisa ON/OFF bebas tanpa validasi prasyarat.
- Card outlet menampilkan **24 Jam** hanya untuk skema default. Outlet dengan jadwal kustom tidak menampilkan info jam operasional di card.
- Detail status (Default 24/7, Jadwal disesuaikan, Tutup sementara, Nonaktif, Perlu perbaikan) tampil di modal capability, bukan di card.
- Toggle bisa ON → langsung aktif, default 24 jam jika belum ada jadwal.
- Toggle bisa OFF kapan saja; jadwal tetap tersimpan, tidak dihapus.
- Navigasi dari Control Panel ke halaman Pengaturan Jam Operasional untuk mengatur jadwal spesifik.
- Jadwal khusus Tutup (libur sementara) tidak menonaktifkan capability — toggle tetap ON agar bot bisa menjelaskan status libur dan kapan outlet kembali buka.
- Informasi saat intent tersimpan ON tetapi master switch OFF: "Bot akan mulai menggunakan capability ini setelah Status Bot WABA diaktifkan."
- `get_operating_hours` default ON untuk outlet baru.
- Existing outlet: `get_operating_hours` tetap di state terakhir yang disimpan.

### Out of Scope

- Setup jam operasional langsung di dalam modal Control Panel.
- Membuat jadwal spesifik otomatis untuk outlet baru (hanya asumsi 24 jam).
- Bulk setup Jam Operasional untuk banyak outlet.
- Validasi prasyarat untuk intent manapun.
- Integrasi dengan Google Business Profile atau sumber jadwal eksternal.

## 3. Business Rules

### BR-AI-014 - Default 24 Jam

Intent `get_operating_hours` bisa langsung ON. Jika jadwal operasional belum dikonfigurasi, sistem mengasumsikan 24 jam. Card outlet menampilkan **24 Jam**. Jika owner mengatur jadwal kustom, card tidak lagi menampilkan status jam operasional.

### BR-AI-015 - Default ON untuk Outlet Baru

Saat outlet baru terdaftar omnichannel, intent `get_operating_hours` default ON dengan asumsi 24 jam. Owner dapat mengatur jadwal spesifik atau menonaktifkan intent kapan pun.

### BR-AI-016 - Existing Outlet

Saat fitur dirilis, seluruh existing outlet memiliki `get_operating_hours` ON dengan status Default 24 Jam, kecuali outlet yang sudah memiliki konfigurasi jam operasional (status Dikonfigurasi).

### BR-AI-017 - Jadwal Tetap Tersimpan Saat Intent OFF

Menonaktifkan intent Jam Operasional tidak menghapus jadwal yang sudah dikonfigurasi. Saat intent diaktifkan kembali, jadwal tersimpan dapat langsung digunakan.

### BR-AI-018 - Jadwal Tutup Sementara Bukan Alasan Nonaktif

Jadwal khusus Tutup (libur sementara) tidak menonaktifkan capability Jam Operasional. Toggle tetap ON agar bot bisa menjelaskan status libur dan waktu outlet kembali buka.

## 4. Dependencies

- Halaman Pengaturan Jam Operasional outlet.
- Data zona waktu outlet.
- Data jadwal operasional 7 hari.
- Greeting bot dan menu capability.

## 5. User Story List

| US ID | Judul US | Goal | Status |
|---|---|---|---|
| US-AI-019 | Melihat Status Jam Operasional di Card | Owner dapat melihat status Jam Operasional outlet di card tanpa membuka modal | Draft |
| US-AI-023 | Mengaktifkan Jam Operasional Langsung | Owner dapat toggle ON tanpa prasyarat — langsung aktif dengan default 24 jam | Draft |
| US-AI-024 | Menonaktifkan Jam Operasional | Owner dapat toggle OFF tanpa kehilangan jadwal | Draft |
| US-AI-025 | Navigasi ke Pengaturan Jam Operasional | Owner dapat mengatur jadwal spesifik dari Control Panel | Draft |
| US-AI-026 | Jadwal Tutup Sementara | Toggle tetap ON saat jadwal khusus Tutup aktif | Draft |

## 6. Notes for Developer

- Card outlet menampilkan **24 Jam** hanya untuk skema default.
- Outlet dengan jadwal kustom → card tidak menampilkan info jam operasional. Detail di modal capability.
- Detail status (Default 24/7, Jadwal disesuaikan, Tutup sementara, Nonaktif, Perlu perbaikan) dihitung server-side dan tampil di modal capability.
- Jika intent ON dan jadwal belum dikonfigurasi, bot merespons dengan asumsi 24 jam.
- Jika intent ON dan jadwal sudah dikonfigurasi, bot merespons dengan jadwal aktual.
- Jika intent OFF, bot menggunakan fallback capability OFF.
