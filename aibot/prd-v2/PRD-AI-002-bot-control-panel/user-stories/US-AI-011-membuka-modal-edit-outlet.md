---
id: US-AI-011
prd: PRD-AI-002
epic: EPIC-AI-002
feature: FEAT-AI-003
title: Membuka Modal Konfigurasi Intent
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-17
updated_at: 2026-07-17
---

# US-AI-011 - Membuka Modal Konfigurasi Intent

## 1. Parent Reference

- PRD: PRD-AI-002 - Bot Control Panel
- Epic: EPIC-AI-002 - Bot Control Panel
- Feature: FEAT-AI-003 - Konfigurasi Intent per Outlet

## 2. User Story

Sebagai owner,
Saya ingin membuka modal konfigurasi intent dari card outlet yang menampilkan 6 toggle intent configurable dengan state terkini dari database,
Agar saya dapat melihat dan mengubah pengaturan bot untuk outlet tersebut.

## 3. Goal

Owner dapat membuka modal konfigurasi intent per outlet dengan tap/click pada card, menampilkan state terkini dari database.

## 4. Acceptance Criteria

- Modal terbuka saat owner tap/click pada card outlet.
- Modal menampilkan header: "Outlet [Nama] — [Kota]".
- 6 toggle intent configurable ditampilkan dengan state terkini dari database (bukan cache).
- Toggle `talk_to_agent` ditampilkan di baris terpisah dengan state readonly/disabled, label "Selalu aktif".
- Footer modal: tombol Simpan (primary) dan Batal (secondary).

## 5. Form Rules

> Tidak ada form untuk user story ini. Modal konfigurasi intent adalah UI display.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-011-01 | Owner di Control Panel. Card outlet A menampilkan 4 intent ON dan 2 intent OFF | Owner tap/click card outlet A | Modal konfigurasi intent terbuka: header "Outlet A — Jakarta Pusat", 6 toggle intent configurable sesuai state saat ini (4 ON, 2 OFF), toggle talk_to_agent readonly dengan label "Selalu aktif", tombol Simpan dan Batal di footer |

### Detail SC-AI-011-01 - Membuka Modal Konfigurasi Intent

#### Given

Owner di Control Panel. Card outlet A menampilkan 4 intent ON, 2 intent OFF.

#### When

Owner tap/click card outlet A.

#### Then

- Modal konfigurasi intent terbuka, background overlay.
- Header: "Outlet A — Jakarta Pusat".
- 6 intent configurable ditampilkan dengan toggle sesuai state database:
  - Cek status laundry (ON/OFF sesuai DB)
  - Cek status tiket (ON/OFF sesuai DB)
  - Layanan outlet (ON/OFF sesuai DB)
  - Jam operasional (ON/OFF sesuai DB)
  - Promo aktif (ON/OFF sesuai DB)
  - Info member (ON/OFF sesuai DB)
- Baris "Hubungi agen": toggle readonly ON, label "Selalu aktif".
- Footer: tombol Batal (kiri/secondary), tombol Simpan (kanan/primary).

#### UI Behavior

- Modal menarik data dari server saat dibuka, bukan dari cache.
- Overlay di luar modal tidak interaktif — tap di luar tidak menutup modal.

## 8. Lo-Fi Design — Modal Konfigurasi Intent

### Tampilan Default (beberapa intent ON, beberapa OFF)

```text
┌─────────────────────────────────────────────────────┐
│  Outlet A — Jakarta Pusat                         × │
├─────────────────────────────────────────────────────┤
│                                                     │
│  Cek status laundry                          [● ON] │
│                                                     │
│  Cek status tiket                           [○ OFF] │
│                                                     │
│  Layanan outlet                            [● ON]  │
│                                                     │
│  Jam operasional                           [● ON]  │
│                                                     │
│  Promo aktif                               [○ OFF] │
│                                                     │
│  Info member                               [● ON]  │
│                                                     │
│  ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─  │
│                                                     │
│  Hubungi agen                  Selalu aktif [● ON]  │
│                                                     │
├─────────────────────────────────────────────────────┤
│  [ Batal ]                          [ Simpan ]     │
└─────────────────────────────────────────────────────┘
```

### Notes Desain

- Garis pemisah (`─ ─ ─`) memisahkan intent configurable dari baris `talk_to_agent` yang readonly.
- Toggle `talk_to_agent`: disabled/readonly, tidak bisa diinteraksi.
- Label "Selalu aktif" di samping toggle `talk_to_agent`, sebagai informasi.
- Tombol Simpan: primary (kanan). Tombol Batal: secondary (kiri).
- Overlay gelap di luar modal, tap tidak menutup modal.
- Icon `×` di pojok kanan header berfungsi sama seperti tombol Batal.

## 9. Supporting Information

Tidak ada.
