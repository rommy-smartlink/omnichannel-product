---
id: US-AI-005
prd: PRD-AI-002
epic: EPIC-AI-002
feature: FEAT-AI-002
title: Mematikan Bot WABA
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-17
updated_at: 2026-07-17
---

# US-AI-005 - Mematikan Bot WABA

## 1. Parent Reference

- PRD: PRD-AI-002 - Bot Control Panel
- Epic: EPIC-AI-002 - Bot Control Panel
- Feature: FEAT-AI-002 - Master Switch WABA

## 2. User Story

Sebagai owner,
Saya ingin mematikan bot untuk seluruh outlet dalam satu WABA melalui toggle master switch,
Agar saya bisa emergency stop saat maintenance, insiden, atau situasi darurat.

## 3. Goal

Owner dapat mengubah master switch dari ON ke OFF dengan modal konfirmasi, menghentikan bot di seluruh outlet.

## 4. Acceptance Criteria

- Toggle master switch dari ON ke OFF menampilkan modal konfirmasi.
- Modal konfirmasi menjelaskan dampak: percakapan bot yang sedang berlangsung akan dihentikan dan customer dialihkan ke agent.
- Owner memilih "Konfirmasi" → switch berubah ke OFF, PATCH ke server, timestamp + identitas pengubah dicatat.
- Owner memilih "Batal" → toggle kembali ke ON, tidak ada perubahan.
- Indicator visual berubah ke warna merah / icon warning saat OFF.
- Konfigurasi per outlet tidak dihapus atau di-reset.
- Saat OFF: percakapan kembali ke agent, tidak ada bot yang aktif.

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-005-01 | Owner di Control Panel, master switch sedang ON, ada 5 outlet di WABA dengan konfigurasi intent per outlet masing-masing | Owner tap toggle master switch dari ON ke OFF | Sistem menampilkan modal konfirmasi. Owner pilih "Konfirmasi". Master switch berubah ke OFF. Indicator visual berubah merah/icon warning. Banner "Bot nonaktif — semua outlet langsung ke agent" tampil. Timestamp dan identitas pengubah terupdate. Konfigurasi per outlet tidak dimodifikasi. |
| SC-AI-005-02 | Master switch ON. Owner tap toggle ke OFF, modal konfirmasi tampil | Owner pilih "Batal" di modal konfirmasi | Toggle kembali ke ON. Tidak ada perubahan state. |

### Detail SC-AI-005-01 - Mematikan Master Switch dengan Konfirmasi

#### Given

Owner berada di Control Panel. Master switch sedang ON. Ada 5 outlet dalam WABA dengan konfigurasi intent masing-masing.

#### When

Owner tap toggle master switch dari ON ke OFF.

#### Then

1. Modal konfirmasi tampil dengan teks: "Mematikan bot akan menghentikan percakapan bot yang sedang berlangsung dan mengalihkan customer ke agent. Konfigurasi per outlet tetap tersimpan."
2. Owner pilih "Konfirmasi".
3. Master switch berubah ke OFF. Indicator visual berubah merah/icon warning.
4. Banner info: "Bot nonaktif — semua outlet langsung ke agent."
5. PATCH ke server sukses. Timestamp dan identitas pengubah tercatat.
6. Konfigurasi intent per outlet untuk semua outlet tidak berubah.

#### Related Business Rules

- BR-AI-001: Master Switch Off Priority
- BR-AI-004: Modal Konfirmasi Master Switch OFF

#### UI Behavior

- Modal konfirmasi memiliki dua tombol: "Batal" (secondary) dan "Konfirmasi" (primary/destructive).
- Off → ON tidak memerlukan modal konfirmasi, langsung berlaku.

### Detail SC-AI-005-02 - Batal Mematikan Master Switch

#### Given

Master switch ON. Owner tap toggle ke OFF. Modal konfirmasi tampil.

#### When

Owner pilih "Batal".

#### Then

Toggle kembali ke posisi ON. Tidak ada PATCH ke server. Tidak ada perubahan state.

## 8. Supporting Information

Lo-Fi design:

```text
┌──────────────────────────────────────────────────────────────┐
│ Nonaktifkan Bot WABA-1?                                  ✕  │
├──────────────────────────────────────────────────────────────┤
│ Mematikan bot akan menghentikan percakapan bot yang sedang   │
│ berlangsung dan mengalihkan customer ke agent.               │
│ Konfigurasi per-outlet tetap tersimpan.                      │
├──────────────────────────────────────────────────────────────┤
│                    [Batal] [Konfirmasi]                      │
└──────────────────────────────────────────────────────────────┘
```
