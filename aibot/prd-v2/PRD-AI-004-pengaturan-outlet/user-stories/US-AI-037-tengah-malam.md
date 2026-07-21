---
id: US-AI-037
prd: PRD-AI-004
epic: EPIC-AI-004
feature: FEAT-AI-012
title: Jadwal Melewati Tengah Malam
status: Draft
priority: Medium
uix_status: 0%
created_at: 2026-07-20
updated_at: 2026-07-20
---

# US-AI-037 - Jadwal Melewati Tengah Malam

## 1. Parent Reference

- PRD: PRD-AI-004 - Pengaturan Outlet
- Epic: EPIC-AI-004 - Pengaturan Outlet
- Feature: FEAT-AI-012 - Pengaturan Jam Operasional Outlet

## 2. User Story

Sebagai owner,  
Saya ingin sistem mendeteksi dan menandai jadwal yang melewati tengah malam,  
Agar saya tidak salah memahami bahwa outlet tutup lebih awal dari jam buka.

## 3. Goal

Saat owner mengisi jam tutup yang lebih kecil dari jam buka (misal buka 18.00, tutup 02.00), sistem otomatis menampilkan indikator `+1 hari` di samping jam tutup. Ini menandakan outlet tetap buka hingga hari berikutnya.

## 4. Acceptance Criteria

- Jika jam tutup < jam buka, sistem menampilkan indikator `+1 hari` di sebelah jam tutup.
- Format tampilan: `18.00–02.00 (+1 hari)`.
- Jika jam tutup > jam buka, tidak ada indikator (sesi dalam hari yang sama).
- Indikator muncul secara real-time saat owner mengubah jam, tanpa perlu menekan Simpan.
- Di preview jadwal, hari dengan jam melewati tengah malam ditampilkan dengan indikator yang sama.
- Data tetap disimpan sebagai jam asli (18.00 dan 02.00), tidak dikonversi.

## 5. Form Rules

> Menggunakan field yang sama dengan US-AI-036 (session_open, session_close).

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-037-01 | Sabtu: Buka | Owner isi buka 18.00, tutup 02.00 | Sistem menampilkan `18.00–02.00 (+1 hari)` |
| SC-037-02 | Sabtu: jam `18.00–02.00 (+1 hari)` | Owner ubah tutup jadi 23.00 | Indikator hilang. Tampilan: `18.00–23.00` |
| SC-037-03 | Jumat: jam `22.00–03.00 (+1 hari)` | Owner lihat preview | Preview: "Jumat: 22.00–03.00 (+1 hari)" |

Owner mengisi jam tutup 02.00.

#### Then

- Sistem mendeteksi `02.00 < 18.00` → indikator `+1 hari` muncul.
- Tampilan sesi: `18.00–02.00 (+1 hari)`.
- Data disimpan sebagai `{ open: "18.00", close: "02.00" }`.

#### Related Business Rules

- BR-AI-203: Jadwal Melewati Tengah Malam

#### UI Behavior

- Indikator `+1 hari` muncul seketika setelah input jam tutup, tanpa perlu blur atau submit.

#### Notes

Indikator `+1 hari` penting agar owner tidak bingung melihat jam tutup (02.00) lebih kecil dari jam buka (18.00). Ini mencegah owner mengira ada kesalahan input.
