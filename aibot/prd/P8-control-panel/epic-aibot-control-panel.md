---
id: EPIC-AI-002
prd: PRD-AI-002
title: Bot Control Panel
status: Draft
owner: Product Owner
figma_url: TBD
created_at: 2026-07-16
updated_at: 2026-07-16
---

# EPIC-AI-002 - Bot Control Panel

## 1. Tujuan Epic

Menyediakan dashboard bagi owner untuk mengontrol bot AI omnichannel pada tiga lapisan: Master Switch WABA (emergency stop seluruh outlet), Konfigurasi Intent per Outlet (ON/OFF capability per outlet), dan Jam Operasional (default 24 jam jika jadwal belum dikonfigurasi).

## 2. Related Problems

- PROB-AI-001
- PROB-AI-002
- PROB-AI-003 (Resolved — default 24 jam)
- PROB-AI-004

## 3. Figma URL

TBD

## 4. Feature List

| Feature ID | Nama Feature | Purpose | Status |
|---|---|---|---|
| FEAT-AI-002 | Master Switch WABA | Owner dapat menghentikan atau menjalankan bot pada seluruh outlet dalam satu WABA | Draft |
| FEAT-AI-003 | Konfigurasi Intent per Outlet | Owner dapat mengaktifkan atau menonaktifkan 6 intent configurable per outlet | Draft |
| FEAT-AI-004 | Jam Operasional — Default 24 Jam | Intent Jam Operasional bisa ON langsung; default 24 jam jika jadwal belum dikonfigurasi | Draft |

## 5. Notes

- Akses Control Panel terbatas untuk owner pada MVP. Role outlet manager dan kasir tidak termasuk.
- Control Panel hanya menampilkan outlet yang terdaftar omnichannel.
- Master Switch scope per WABA — jika owner memiliki lebih dari satu WABA, masing-masing memiliki Master Switch sendiri.
- Konfigurasi intent per outlet tetap tersimpan saat Master Switch dimatikan. Tidak ada data yang dihapus atau di-reset.
- Intent `talk_to_agent` selalu ON dan tidak dapat dimatikan oleh owner.
- Intent `get_operating_hours` bisa langsung ON. Jika jadwal belum dikonfigurasi, default 24 jam berlaku.
- Seluruh fitur dalam Epic ini harus siap sebelum bot dinyatakan production-ready untuk customer.
- Development prioritas: FEAT-AI-003 (fondasi halaman) → FEAT-AI-002 (header toggle) → FEAT-AI-004 (satu toggle tanpa prasyarat, default 24 jam).
