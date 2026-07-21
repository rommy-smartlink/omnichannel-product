---
id: US-AI-034
prd: PRD-AI-004
epic: EPIC-AI-004
feature: FEAT-AI-012
title: Melihat Status Konfigurasi Jam Operasional
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-20
updated_at: 2026-07-20
---

# US-AI-034 - Melihat Status Konfigurasi Jam Operasional

## 1. Parent Reference

- PRD: PRD-AI-004 - Pengaturan Outlet
- Epic: EPIC-AI-004 - Pengaturan Outlet
- Feature: FEAT-AI-012 - Pengaturan Jam Operasional Outlet

## 2. User Story

Sebagai owner,  
Saya ingin melihat status konfigurasi jam operasional outlet,  
Agar saya tahu apakah jadwal masih default 24 jam atau sudah disesuaikan.

## 3. Goal

Owner membuka halaman Jam Operasional dan langsung mengetahui status konfigurasi saat ini beserta status intent `get_operating_hours`.

## 4. Acceptance Criteria

- Halaman menampilkan header: "Jam Operasional — [Nama Outlet]".
- Status konfigurasi ditampilkan di bawah header: Default 24 Jam atau Disesuaikan.
- Status intent `get_operating_hours` (ON/OFF) ditampilkan terpisah dari status konfigurasi. Default ON.
- Outlet default 24 jam menampilkan status "Default 24 Jam" dan intent ON.
- Outlet dengan jadwal kustom menampilkan status "Disesuaikan".
- Status diperbarui setiap kali halaman dibuka (bukan cache).

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-034-01 | Outlet baru, belum pernah diatur | Owner buka halaman Jam Operasional | Status: "Default 24 Jam". Intent: ON. Form pre-fill: semua hari 24 Jam, zona waktu WIB. |
| SC-034-02 | Jadwal kustom valid tersimpan | Owner buka halaman Jam Operasional | Status: "Disesuaikan". Intent: ON. |

### Detail SC-034-01 - Outlet Baru

#### Given

Outlet A terdaftar omnichannel. Belum ada konfigurasi jam operasional.

#### When

Owner membuka halaman Jam Operasional outlet A.

#### Then

- Header: "Jam Operasional — Outlet A".
- Status konfigurasi: "Default 24 Jam".
- Status intent `get_operating_hours`: ON.
- Zona waktu: WIB (default).
- Jadwal mingguan: 24 Jam (default), ketujuh hari terisi.

#### Related Business Rules

- BR-AI-208: Intent Default ON, Outlet Default 24 Jam

### Detail SC-034-02 - Jadwal Disesuaikan

#### Given

Outlet B memiliki zona waktu WIB dan jadwal Senin–Minggu valid.

#### When

Owner membuka halaman Jam Operasional outlet B.

#### Then

- Status konfigurasi: "Disesuaikan".
- Status intent: ON.
- Zona waktu, jadwal mingguan, dan jadwal khusus ditampilkan sesuai data tersimpan.

#### Related Business Rules

- BR-AI-200: Zona Waktu Wajib
- BR-AI-201: Jadwal Mingguan Wajib Lengkap
