---
id: FEAT-AI-007
prd: PRD-AI-003
epic: EPIC-AI-003
title: Jam Operasional
status: Draft
owner: Product Owner
created_at: 2026-07-21
updated_at: 2026-07-21
---

# FEAT-AI-007 - Jam Operasional

## 1. Purpose

Customer menanyakan jadwal buka/tutup outlet melalui percakapan natural WhatsApp. Bot menjawab berdasarkan konfigurasi jam operasional outlet yang valid — jadwal mingguan, jadwal khusus, status buka/tutup saat ini, jam tutup hari ini, atau waktu buka berikutnya. Semua perhitungan waktu menggunakan zona waktu outlet.

## 2. Scope

### In Scope

- Intent `get_operating_hours` aktif per outlet (toggleable via Control Panel). Hanya bisa ON jika konfigurasi jam operasional valid.
- Jenis pertanyaan yang dikenali: jadwal mingguan, jadwal hari ini, status saat ini, jam tutup, jam buka, buka berikutnya, jadwal hari/tanggal tertentu.
- Jadwal khusus selalu diprioritaskan di atas jadwal mingguan untuk tanggal yang sama.
- Dukungan status: Buka, Tutup, 24 Jam, dua sesi per hari, jadwal melewati tengah malam.
- Pengelompokan hari dengan jadwal sama agar respons mingguan ringkas.
- Zona waktu outlet sebagai acuan semua perhitungan waktu.
- Fallback jelas jika data tidak tersedia atau tidak konsisten.
- Bahasa Indonesia, tone "Kak".
- Command "menu"/"batal"/"hubungi agen" selalu override.

### Out of Scope

- Membuat reservasi atau janji kunjungan.
- Mengubah jam operasional melalui percakapan.
- Estimasi antrean atau kepadatan outlet.
- Batas terakhir penerimaan laundry (jika berbeda dari jam tutup).
- Jadwal layanan spesifik (jika berbeda dari jadwal outlet).
- Jam kerja karyawan/agent.
- Notifikasi otomatis sebelum buka/tutup.
- Menyarankan outlet alternatif yang sedang buka.
- Multi-language.

## 3. Business Rules

### BR-AI-320 — Zona Waktu Outlet

Semua perhitungan "hari ini", "besok", "sekarang", "Minggu depan", dan pergantian tanggal menggunakan zona waktu outlet (dari Pengaturan Jam Operasional). Bukan zona waktu customer atau server.

### BR-AI-321 — Prioritas Jadwal Khusus

Jadwal khusus (tanggal tunggal maupun rentang) selalu mengalahkan jadwal mingguan. Bot tidak menggabungkan jadwal khusus dan jadwal mingguan pada tanggal yang sama.

### BR-AI-322 — Intent Hanya Aktif Jika Jadwal Valid

Intent `get_operating_hours` hanya bisa ON jika outlet memiliki konfigurasi jam operasional yang valid (zona waktu tersedia + jadwal tersimpan). Tanpa konfigurasi valid → tampilkan fallback data tidak tersedia + tawarkan agent.

### BR-AI-323 — Fallback Data Tidak Tersedia

Jika konfigurasi tidak tersedia, tidak valid, atau tidak konsisten → bot tidak mengarang. Tampilkan pesan bahwa data jam operasional belum tersedia dan tawarkan eskalasi ke agen.

### BR-AI-324 — Ambiguasi Tanggal

Jika customer menyebut tanggal tanpa tahun dan tanggal tersebut sudah lewat di tahun berjalan → bot menggunakan kejadian terdekat yang akan datang dan menyebut tanggal lengkap pada respons.

### BR-AI-325 — Status Antara Dua Sesi

Jika outlet memiliki dua sesi (misal 08.00–12.00 dan 13.00–21.00) dan waktu sekarang berada di antara sesi → informasikan sedang istirahat/tutup sementara dan jam buka sesi berikutnya.

## 4. Dependencies

- Bot Runtime Behavior (PRD-AI-001): effective state 3-gate, fallback.
- Bot Control Panel (PRD-AI-002): toggle `get_operating_hours` per outlet.
- Pengaturan Jam Operasional (PRD-AI-004): sumber konfigurasi jadwal + zona waktu.
- Conversation context: outlet ID.

## 5. User Story List

| US ID | Judul US | Goal | Status |
|---|---|---|---|
| US-AI-050 | Jam Operasional — Jadwal Hari Ini | Customer tanya jadwal hari ini dan mendapat sesi buka/tutup dengan zona waktu outlet | Draft |
| US-AI-051 | Jam Operasional — Status Buka/Tutup Saat Ini | Customer tanya apakah outlet sedang buka dan mendapat status real-time + info tutup/buka berikutnya | Draft |
| US-AI-052 | Jam Operasional — Jadwal Hari/Tanggal Tertentu | Customer tanya jadwal hari atau tanggal spesifik dan bot menjawab sesuai jadwal yang berlaku | Draft |
| US-AI-053 | Jam Operasional — Buka Kembali Setelah Tutup | Customer tanya kapan buka kembali dan bot mencari sesi berikutnya atau hari operasional berikutnya | Draft |
| US-AI-054 | Jam Operasional — Prioritas Jadwal Khusus | Bot memprioritaskan jadwal khusus (libur nasional, tanggal spesial) di atas jadwal mingguan | Draft |

## 6. Notes for Developer

- Tidak ada slot wajib dari customer — outlet dan zona waktu dari context.
- Jenis pertanyaan diklasifikasi dari pesan customer: `weekly_schedule`, `day_schedule`, `current_status`, `opening_time`, `closing_time`, `next_opening`.
- Jadwal melewati tengah malam: jika sesi 22.00–02.00 dan sekarang 23.00 → "Sedang buka, tutup pukul 02.00 WIB."
- Hari dikelompokkan jika jadwal sama. Contoh: "Senin–Jumat: 08.00–21.00, Sabtu–Minggu: 09.00–18.00".
- Jadwal khusus dengan status "Tutup" dan rentang multi-hari → cari jadwal buka pertama setelah tanggal selesai.
