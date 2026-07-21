---
id: US-AI-042
prd: PRD-AI-004
epic: EPIC-AI-004
feature: FEAT-AI-012
title: Navigasi Aktivasi dari Control Panel
status: Draft
priority: Medium
uix_status: 0%
created_at: 2026-07-20
updated_at: 2026-07-20
---

# US-AI-042 - Navigasi Pengaturan dari Control Panel

## 1. Parent Reference

- PRD: PRD-AI-004 - Pengaturan Outlet
- Epic: EPIC-AI-004 - Pengaturan Outlet
- Feature: FEAT-AI-012 - Pengaturan Jam Operasional Outlet

## 2. User Story

Sebagai owner,
Saya ingin diarahkan ke halaman pengaturan jam operasional dari Control Panel,
Agar saya bisa mengonfigurasi jadwal outlet tanpa mencari sendiri menu pengaturan.

## 3. Goal

Saat owner menekan CTA "Atur Jam Operasional" di Control Panel (card outlet atau modal edit), sistem membuka halaman pengaturan jam operasional outlet terkait.

## 4. Acceptance Criteria

- Di Control Panel, card outlet menampilkan info jam operasional ("24 Jam" atau jadwal kustom).
- Card/Modal memiliki CTA "Atur Jam Operasional" yang membuka halaman pengaturan outlet terkait.
- Navigasi menggunakan parameter outlet ID sehingga halaman pengaturan langsung terbuka di outlet yang tepat.
- Intent `get_operating_hours` default ON. Owner dapat menonaktifkannya dari toggle di Control Panel.
- Menonaktifkan intent tidak menghapus jadwal.

## 5. Form Rules

> Tidak ada form rules. Ini adalah flow navigasi.

## 6. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-042-01 | Outlet A: jadwal default 24 jam | Owner tekan "Atur Jam Operasional" di card Control Panel | Halaman pengaturan jam operasional outlet A terbuka. |
| SC-042-02 | Outlet B: jadwal kustom | Owner tekan "Atur Jam Operasional" di card Control Panel | Halaman pengaturan jam operasional outlet B terbuka. |
| SC-042-03 | Owner di Control Panel | Owner matikan toggle `get_operating_hours` | Intent OFF. Jadwal tetap tersimpan. |

### Detail SC-042-01 - Navigasi dari Control Panel

#### Given

Control Panel, daftar outlet. Outlet A: jadwal default 24 jam.

#### When

Owner menekan "Atur Jam Operasional" di card outlet A.

#### Then

- Halaman pengaturan jam operasional outlet A terbuka.
- Zona waktu, jadwal mingguan, jadwal khusus siap diisi.

#### Related Business Rules

- BR-AI-208: Intent Default ON, Outlet Default 24 Jam

#### UI Behavior

- Navigasi langsung ke halaman pengaturan dengan outlet ID.

- BR-AI-208: Validasi Sebelum Intent ON

#### Notes

Ini adalah skenario happy path — konfigurasi sudah siap, owner tinggal mengaktifkan dari Control Panel tanpa perlu masuk halaman pengaturan.
