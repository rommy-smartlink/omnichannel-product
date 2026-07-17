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

Menyediakan dashboard bagi owner untuk mengontrol bot AI omnichannel melalui tiga lapisan switch: Master Switch WABA (emergency stop seluruh outlet), Outlet Switch (ON/OFF per outlet), dan Konfigurasi Intent per Outlet (ON/OFF capability per outlet). Serta menampilkan informasi Jam Operasional yang dapat disesuaikan dari menu outlet.

## 2. Related Problems

- PROB-AI-001
- PROB-AI-002

## 3. Figma URL

TBD

## 4. Feature List

| Feature ID | Nama Feature | Purpose | Status |
|---|---|---|---|
| FEAT-AI-002 | Master Switch WABA | Owner dapat menghentikan atau menjalankan bot pada seluruh outlet dalam satu WABA (gate 1) | Draft |
| FEAT-AI-005 | Outlet Switch | Owner dapat menghentikan atau menjalankan bot per outlet (gate 2) | Draft |
| FEAT-AI-003 | Konfigurasi Intent per Outlet | Owner dapat mengaktifkan atau menonaktifkan 6 intent configurable per outlet (gate 3) | Draft |
| FEAT-AI-004 | Informasi Jam Operasional | Menampilkan info jam operasional 24 jam default dengan navigasi ke menu outlet untuk penyesuaian | Draft |

## 5. Notes

- Akses Control Panel terbatas untuk owner pada MVP. Role outlet manager dan kasir tidak termasuk.
- Control Panel hanya menampilkan outlet yang terdaftar omnichannel.
- Master Switch scope per WABA — jika owner memiliki lebih dari satu WABA, masing-masing memiliki Master Switch sendiri.
- Outlet Switch scope per outlet — setiap outlet memiliki toggle independen.
- Konfigurasi intent per outlet tetap tersimpan saat Master Switch atau Outlet Switch dimatikan. Tidak ada data yang dihapus atau di-reset.
- Intent `talk_to_agent` selalu ON dan tidak dapat dimatikan oleh owner.
- Jam Operasional default 24 jam untuk semua outlet. Control Panel menampilkan informasi "Jam operasional buka 24 jam" dengan tombol navigasi ke menu outlet untuk menyesuaikan. Jam Operasional bukan prasyarat intent.
- Seluruh fitur dalam Epic ini harus siap sebelum bot dinyatakan production-ready untuk customer.
- Development prioritas: FEAT-AI-003 (fondasi halaman) → FEAT-AI-002 (header toggle) → FEAT-AI-005 (outlet toggle) → FEAT-AI-004 (info jam operasional dengan navigasi).

## 6. Override Priority (3-Gate)

Bot hanya merespons customer jika **ketiga gate dalam keadaan ON**. Pengecekan dilakukan cascading dari atas ke bawah oleh bot engine:

```text
Gate 1 — Master Switch OFF
        >
Gate 2 — Outlet Switch OFF
        >
Gate 3 — Intent Switch OFF (konfigurasi intent per outlet)
```

**Cara kerja:**

1. **Cek Master Switch (Gate 1).** Kalau OFF → bot diam total di semua outlet. Selesai.
2. **Cek Outlet Switch (Gate 2).** Kalau OFF → bot diam di outlet tersebut. Selesai.
3. **Cek Intent Switch (Gate 3).** Intent OFF → bot tidak eksekusi capability tersebut.

Gate pertama yang OFF langsung memblokir — gate di bawahnya tidak dicek. Bot jalan normal hanya jika Master ON, Outlet ON, dan Intent ON.

Override priority ini mengikat FEAT-AI-002 (Master Switch), FEAT-AI-005 (Outlet Switch), dan FEAT-AI-003 (Konfigurasi Intent).
