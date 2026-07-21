---
id: EPIC-AI-004
prd: PRD-AI-004
title: Pengaturan Outlet
status: Draft
owner: Product Owner
figma_url: TBD
created_at: 2026-07-20
updated_at: 2026-07-20
---

# EPIC-AI-004 - Pengaturan Outlet

## 1. Tujuan Epic

Menyediakan halaman pengaturan jam operasional per outlet bagi owner. Mencakup: pemilihan zona waktu outlet (default WIB), pengaturan jadwal mingguan (Senin–Minggu dengan status Buka/Tutup/24 Jam, default 24 Jam), pembuatan jadwal khusus (tanggal/rentang dengan status Tutup/Buka khusus/24 Jam), serta aksi Simpan. FE pre-fill default memastikan form selalu valid saat pertama dibuka — tidak ada state "Belum lengkap". Intent `get_operating_hours` sudah aktif sejak rilis; semua outlet dianggap 24 jam sampai owner mengonfigurasi jadwal yang berbeda.

## 2. Related Problems

- PROB-AI-009: Owner tidak bisa mengonfigurasi jam operasional outlet.
- PROB-AI-010: Tanpa zona waktu, bot tidak bisa menghitung "sekarang", "hari ini", "besok".
- PROB-AI-011: Tanpa jadwal khusus, bot salah informasi pada hari libur.

## 3. Figma URL

TBD

## 3a. Lo-Fi Design

```text
┌──────────────────────────────────────────────────────────────────┐
│ Jam Operasional — Laundry Kilat                                  │
│ Atur zona waktu dan jadwal. Bot langsung pakai data ini.         │
│                                                                  │
│ Zona Waktu *                                                     │
│ ┌─────────────────────────────────────────────────────────┐      │
│ │ WIB — Asia/Jakarta (UTC+07:00)                         ▼│      │
│ └─────────────────────────────────────────────────────────┘      │
│                                                                  │
│ Jadwal Mingguan *                                                │
│                                                                  │
│ Senin   [Buka] [Tutup] [24 Jam]   08:00 – 21:00       ×          │
│ ─────────────────────────────────────────────────────────────    │
│ Selasa  [Buka] [Tutup] [24 Jam]   Tutup — tidak ada sesi         │
│ ─────────────────────────────────────────────────────────────    │
│ Rabu    [Buka] [Tutup] [24 Jam]   Buka 24 jam                   │
│ ─────────────────────────────────────────────────────────────    │
│ Kamis   [Buka] [Tutup] [24 Jam]   —                             │
│ ─────────────────────────────────────────────────────────────    │
│ Jumat   [Buka] [Tutup] [24 Jam]   08:00 – 02:00 (+1)  ×        │
│ ─────────────────────────────────────────────────────────────    │
│ Sabtu   [Buka] [Tutup] [24 Jam]   09:00 – 15:00       ×         │
│ ─────────────────────────────────────────────────────────────    │
│ Minggu  [Buka] [Tutup] [24 Jam]   —                             │
│                                                                  │
│ ──── Jadwal Khusus ──────────────────────────────────────────    │
│ ┌──────────────────────────────────────────────────────┐        │
│ │ Hari Raya Idul Fitti                 │ Edit │ Hapus  │        │
│ │ 31 Mar – 1 Apr 2026 · Tutup          │      │        │        │
│ └──────────────────────────────────────────────────────┘        │
│ ┌──────────────────────────────────────────────────────┐        │
│ │ Perbaikan Mesin                      │ Edit  │ Hapus │        │
│ │ 15 Apr 2026 · Buka 10.00–16.00       │       │       │        │
│ └──────────────────────────────────────────────────────┘        │
│ ┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┐                          │
│ │ + Tambah Jadwal Khusus          │                          │
│ └ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┘                          │
│                                                                  │
│                                                                  │
│                         [Batal]       [Simpan]                  │
└──────────────────────────────────────────────────────────────────┘
```

## 4. Feature List

| Feature ID | Nama Feature | Purpose | Status |
|---|---|---|---|
| FEAT-AI-012 | Pengaturan Jam Operasional Outlet | Owner dapat mengonfigurasi zona waktu, jadwal mingguan, dan jadwal khusus per outlet sebagai sumber data intent `get_operating_hours` | Draft |

## 5. Notes

- Epic ini hanya mencakup pengaturan jam operasional outlet. 
- Halaman diakses dari menu Pengaturan Outlet, atau dari Control Panel melalui CTA "Atur Jam Operasional".
- Hak akses halaman diatur melalui ACL.
- Jadwal yang sudah tersimpan menjadi sumber data bagi intent `get_operating_hours` (PRD-AI-003, FEAT-AI-007).
- Intent `get_operating_hours` default ON saat rilis. Semua outlet dianggap 24 jam sampai owner mengonfigurasi jadwal.
- Owner dapat menonaktifkan intent kapan saja. Menonaktifkan intent tidak menghapus jadwal yang sudah disimpan.
- Hanya ada satu aksi: Simpan. Tidak ada Simpan & Aktifkan karena intent sudah aktif.
- FE pre-fill default: zona waktu = WIB, semua hari = 24 Jam. Form selalu valid saat pertama dibuka — tidak ada state "Belum lengkap". Owner tinggal menyesuaikan yang diperlukan lalu Simpan.
- Jadwal khusus selalu diprioritaskan dibanding jadwal mingguan oleh bot.
