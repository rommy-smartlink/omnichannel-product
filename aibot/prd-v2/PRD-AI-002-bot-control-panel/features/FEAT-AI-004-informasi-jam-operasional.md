---
id: FEAT-AI-004
prd: PRD-AI-002
epic: EPIC-AI-002
title: Informasi Jam Operasional
status: Draft
owner: Product Owner
created_at: 2026-07-16
updated_at: 2026-07-17
---

# FEAT-AI-004 - Informasi Jam Operasional

## 1. Purpose

Card outlet di Control Panel menampilkan **24 Jam** hanya jika outlet menggunakan skema default 24 jam. Jika outlet memiliki jadwal kustom (zona waktu, jadwal per hari, sesi), card tidak menampilkan info jam operasional. Aturan yang sama berlaku di modal capability. Saat fitur rilis, semua outlet default 24 Jam.

## 2. Scope

### In Scope

- Card outlet menampilkan "Jam Operasional 24 Jam" untuk outlet dengan skema default 24 jam.
- Outlet dengan jadwal kustom tidak menampilkan info jam operasional di card.
- Tombol "Atur Jam Operasional" di card outlet mengarahkan ke menu Pengaturan Outlet.
- Default 24 Jam untuk semua outlet saat fitur rilis.

### Out of Scope

- Setup jam operasional langsung di dalam modal Control Panel.
- Validasi prasyarat untuk intent `get_operating_hours`.
- Menampilkan detail status (Default 24/7, Jadwal disesuaikan, dll) di card atau modal.
- Bulk setup Jam Operasional.

## 3. Business Rules

### BR-AI-014 — Default 24 Jam

Saat fitur rilis, seluruh outlet default 24 jam. Card outlet menampilkan "Jam Operasional 24 Jam".

### BR-AI-015 — Navigasi ke Menu Outlet

Tombol "Atur Jam Operasional" mengarahkan owner ke menu Pengaturan Outlet.

### BR-AI-016 — Jam Operasional Bukan Prasyarat

Intent `get_operating_hours` dapat ON tanpa validasi jadwal.

### BR-AI-017 — Card & Modal Hanya untuk Default

Card outlet dan modal capability hanya menampilkan "Jam Operasional 24 Jam" untuk skema default. Outlet dengan jadwal kustom tidak menampilkan info jam operasional di card maupun modal.

## 4. Dependencies

- Menu Pengaturan Outlet.
- Data jam operasional outlet.

## 5. User Story List

| US ID | Judul US | Goal | Status |
|---|---|---|---|
| US-AI-019 | Melihat Status Jam Operasional di Card | Owner melihat "Jam Operasional 24 Jam" di card untuk outlet dengan skema default | Draft |
| US-AI-020 | Navigasi ke Pengaturan Jam Operasional | Owner menekan "Atur Jam Operasional" dan diarahkan ke menu outlet | Draft |

## 6. Notes for Developer

- Card outlet menampilkan "Jam Operasional 24 Jam" hanya untuk skema default.
- Outlet dengan jadwal kustom → card tidak menampilkan info jam operasional.
- Modal capability ikut aturan yang sama: tampil "Jam Operasional 24 Jam" hanya untuk skema default, tidak tampil untuk jadwal kustom. Tidak ada detail status konfigurasi outlet di modal.
