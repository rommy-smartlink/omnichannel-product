---
id: US-AI-019
prd: PRD-AI-002
epic: EPIC-AI-002
feature: FEAT-AI-004
title: Melihat Info Jam Operasional di Card
status: Draft
priority: Medium
uix_status: 0%
created_at: 2026-07-17
updated_at: 2026-07-17
---

# US-AI-019 - Melihat Info Jam Operasional di Card

## 1. Parent Reference

- PRD: PRD-AI-002 - Bot Control Panel
- Epic: EPIC-AI-002 - Bot Control Panel
- Feature: FEAT-AI-004 - Informasi Jam Operasional

## 2. User Story

Sebagai owner,
Saya ingin melihat informasi "Jam Operasional 24 Jam" di card outlet Control Panel untuk outlet dengan skema default,
Agar saya tahu status jam operasional outlet tanpa harus membuka menu terpisah.

## 3. Goal

Card outlet menampilkan "Jam Operasional 24 Jam" untuk outlet dengan skema default. Outlet dengan jadwal kustom tidak menampilkan info jam operasional di card — detail tampil di modal capability.

## 4. Acceptance Criteria

- Card outlet menampilkan "Jam Operasional 24 Jam" untuk outlet dengan skema default.
- Outlet dengan jadwal kustom (zona waktu, jadwal per hari, sesi) tidak menampilkan info jam operasional di card.
- Informasi ini adalah teks informatif, bukan status validasi.
- Jam operasional tidak memengaruhi kemampuan ON/OFF intent `get_operating_hours`.

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-019-01 | Outlet A memiliki jam operasional default 24 jam (belum disesuaikan owner) | Owner melihat card outlet A di Control Panel | Card menampilkan "Jam Operasional 24 Jam" |
| SC-AI-019-02 | Outlet B sudah memiliki jadwal kustom (zona waktu, per hari) melalui menu outlet | Owner melihat card outlet B di Control Panel | Card tidak menampilkan info jam operasional. Detail jadwal hanya tampil di modal capability |

### Detail SC-AI-019-01 - Default 24 Jam

#### Given

Outlet A baru terdaftar omnichannel, jam operasional default 24 jam. Owner belum menyesuaikan.

#### When

Owner melihat card outlet A di Control Panel.

#### Then

Card outlet menampilkan: "Jam Operasional 24 Jam".

#### Related Business Rules

- BR-AI-014: Default 24 Jam
- BR-AI-016: Jam Operasional Bukan Prasyarat

### Detail SC-AI-019-02 - Jadwal Kustom

#### Given

Outlet B sudah memiliki jadwal kustom (zona waktu, jadwal per hari) melalui menu Pengaturan Outlet.

#### When

Owner melihat card outlet B di Control Panel.

#### Then

Card tidak menampilkan info jam operasional. Owner harus membuka modal capability untuk melihat detail jadwal.

## 8. Supporting Information

Tidak ada.
