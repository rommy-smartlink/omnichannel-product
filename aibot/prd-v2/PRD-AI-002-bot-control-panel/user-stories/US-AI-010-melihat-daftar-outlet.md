---
id: US-AI-010
prd: PRD-AI-002
epic: EPIC-AI-002
feature: FEAT-AI-003
title: Melihat Daftar Outlet di Control Panel
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-17
updated_at: 2026-07-17
merge: US-AI-021
---

# US-AI-010 - Melihat Daftar Outlet di Control Panel

## 1. Parent Reference

- PRD: PRD-AI-002 - Bot Control Panel
- Epic: EPIC-AI-002 - Bot Control Panel
- Feature: FEAT-AI-003 - Konfigurasi Intent per Outlet

## 2. User Story

Sebagai owner,
Saya ingin melihat, mencari, dan mengurutkan daftar outlet omnichannel di halaman Control Panel dalam bentuk card yang menampilkan ringkasan capability dan jam operasional,
Agar saya dapat mengidentifikasi outlet mana yang perlu disesuaikan pengaturan bot-nya.

## 3. Goal

Owner dapat melihat daftar outlet omnichannel dalam card read-only dengan ringkasan capability, fitur pencarian substring, dan sort.

## 4. Acceptance Criteria

- Hanya outlet yang terdaftar omnichannel yang muncul di daftar.
- Setiap outlet ditampilkan sebagai card yang berisi: nama outlet, kota, jumlah intent aktif, status jam operasional (24 jam jika default, tidak tampil jika kustom), outlet switch, timestamp terakhir diubah.
- Card outlet bersifat read-only — interaksi hanya melalui tap/click untuk membuka modal konfigurasi intent.
- Search melakukan substring match terhadap nama, kota, atau kode outlet.
- Default state outlet baru: Outlet Switch ON.
- Identitas pengubah terakhir ditampilkan di card bersama timestamp.
- Sort default: Nama A-Z ascending. Opsi sort: Nama Z-A, Terakhir diubah.

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-010-01 | Owner memiliki 5 outlet terdaftar omnichannel | Owner mengakses halaman Control Panel | Sistem menampilkan daftar 5 outlet sebagai card dengan ringkasan capability dan jam operasional |
| SC-AI-010-02 | Owner di Control Panel, daftar outlet ter-load | Owner mengetik "tebet" di kolom search | Daftar outlet ter-filter, hanya menampilkan outlet yang nama/kota/kode mengandung "tebet" |
| SC-AI-010-03 | Owner di Control Panel, daftar outlet ter-load | Owner memilih opsi sort dari dropdown sort | Daftar outlet ter-sort sesuai pilihan (Nama A-Z / Nama Z-A / Terakhir diubah) |
| SC-AI-010-04 | Owner memiliki 10 outlet fisik, hanya 6 yang terdaftar omnichannel | Owner membuka Control Panel | Hanya 6 outlet terdaftar omnichannel yang muncul di daftar |
| SC-AI-010-05 | Outlet A baru terdaftar omnichannel (default Outlet Switch ON) | Owner membuka Control Panel, melihat card outlet A | Card menampilkan Outlet Switch di posisi ON, indicator normal, teks "Autobot aktif", timestamp dan identitas pengubah terakhir |

### Detail SC-AI-010-01 - Menampilkan Daftar Outlet

#### Given

Owner login dan membuka menu Bot Control Panel. Owner memiliki 5 outlet terdaftar omnichannel dengan konfigurasi intent masing-masing.

#### When

Owner mengakses halaman Control Panel.

#### Then

- 5 outlet tampil sebagai card, masing-masing menampilkan:
  - Nama outlet dan kota
  - Outlet Switch status
  - Jumlah intent aktif (misal "6 capability aktif")
  - Status Jam Operasional ("Jam Operasional 24 Jam" jika skema default; tidak tampil jika jadwal kustom)
  - Timestamp terakhir diubah
- Default sort: Nama A-Z.

#### UI Behavior

- Card outlet memuat data dari server saat halaman dibuka.
- Scroll jika outlet lebih dari batas tampilan viewport.

### Detail SC-AI-010-02 - Mencari Outlet

#### Given

Owner berada di halaman Control Panel. Daftar outlet sudah ter-load.

#### When

Owner mengetik "tebet" di kolom search.

#### Then

- Daftar outlet ter-filter real-time.
- Hanya outlet dengan nama, kota, atau kode yang mengandung substring "tebet" (case-insensitive) ditampilkan.
- Sort tetap berlaku pada hasil filter.

### Detail SC-AI-010-03 - Mengurutkan Outlet

#### Given

Owner di Control Panel. Daftar outlet sudah ter-load.

#### When

Owner memilih opsi sort "Nama Z-A" dari dropdown sort.

#### Then

- Daftar outlet ter-sort descending berdasarkan nama.
- Opsi sort lain ("Nama A-Z", "Terakhir diubah") berfungsi serupa.

#### UI Behavior

- Dropdown sort di samping atau di bawah kolom search.
- Default: "Nama A-Z".

### Detail SC-AI-010-04 - Hanya Outlet Omnichannel

#### Given

Owner memiliki 10 outlet fisik, 6 terdaftar omnichannel, 4 belum subscribe.

#### When

Owner membuka Control Panel.

#### Then

Hanya 6 outlet omnichannel yang tampil. 4 outlet non-omnichannel tidak muncul.

### Detail SC-AI-010-05 - Outlet Baru Default ON

#### Given

Outlet A baru terdaftar omnichannel. Outlet Switch default ON.

#### When

Owner membuka Control Panel, melihat card outlet A.

#### Then

- Outlet Switch toggle di posisi ON, indicator normal.
- Teks: "Autobot aktif".
- Timestamp terakhir diubah dan identitas pengubah ditampilkan.

#### Related Business Rules

- BR-AI-019: Default Outlet Switch ON

## 8. Supporting Information

Lo-Fi design card outlet:

```text
┌──────────────────────────────────────────────────────────────┐
│ Outlet A — Jakarta Pusat                  Autobot [ ●── ON ] │
│ Autobot aktif                                                │
│ 6 capability aktif · Jam Operasional 24 Jam                › │
│ Diperbarui 2 jam lalu                                        │
└──────────────────────────────────────────────────────────────┘

Untuk outlet dengan jadwal kustom, baris Jam Operasional tidak ditampilkan:

```text
┌──────────────────────────────────────────────────────────────┐
│ Outlet A — Jakarta Pusat                  Autobot [ ●── ON ] │
│ Autobot aktif                                                │
│ 6 capability aktif                                          › │
│ Diperbarui 2 jam lalu                                        │
└──────────────────────────────────────────────────────────────┘
```
