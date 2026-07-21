---
id: US-AI-044
prd: PRD-AI-003
epic: EPIC-AI-003
feature: FEAT-AI-005
title: Cek Status Laundry — Mode Detail Ringan
status: Draft
priority: Medium
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-044 - Cek Status Laundry — Mode Detail Ringan

## 1. Parent Reference

- PRD: PRD-AI-003 - Customer Self-Service Capabilities
- Epic: EPIC-AI-003 - Intent Capabilities
- Feature: FEAT-AI-005 - Cek Status Laundry

## 2. User Story

Sebagai customer,
Saya ingin meminta detail transaksi laundry setelah melihat status ringkas,
Agar saya bisa lihat layanan yang saya pesan dan status pengambilan/pengantaran.

## 3. Goal

Setelah Mode Ringkas ditampilkan, customer bisa trigger Mode Detail Ringan dengan kata kunci "detail", "info lengkap", "lebih lengkap", atau "rincian". Bot menambahkan daftar layanan, status ambil, dan pengantaran. Hanya 1× render per ID.

## 4. Acceptance Criteria

- Trigger Mode Detail Ringan: "detail", "info lengkap", "lebih lengkap", "rincian".
- Hanya bisa diakses setelah Mode Ringkas ditampilkan di sesi yang sama untuk ID yang sama.
- Menambahkan: daftar layanan (nama layanan, jumlah, satuan), status ambil, pengantaran.
- Tidak mengulang field dari Mode Ringkas (tetap tampil, ditambah field baru).
- Request detail kedua → "Detail sudah saya tampilkan di atas ya Kak."
- Reset ke ringkas jika customer mengecek ID baru.
- Field sensitif tetap tidak ditampilkan.

## 5. Form Rules

> Tidak ada form UI untuk user story ini. Input diberikan customer melalui percakapan WhatsApp.

## 6. Form Notes

- Validasi input percakapan mengikuti business rule pada Feature terkait dan FEAT-AI-013.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-044-01 | Mode Ringkas sudah tampil untuk ID TMD001 | Customer kirim "detail" | Bot tampilkan Mode Detail Ringan: tambah layanan, status ambil, pengantaran. |
| SC-AI-044-02 | Mode Detail Ringan sudah tampil untuk ID TMD001 | Customer kirim "detail" lagi | Bot balas "Detail sudah saya tampilkan di atas ya Kak." |
| SC-AI-044-03 | Customer cek ID baru TMD002 | Customer kirim "cek TMD002" lalu "detail" | Bot tampilkan Ringkas dulu, lalu Detail Ringan untuk TMD002. Reset counter. |

### Detail SC-AI-044-01 - Detail Ringan Pertama

#### Given

Mode Ringkas transaksi TMD001 sudah tampil: status "Siap diambil", total Rp85.000 Lunas. Transaksi punya 2 layanan: Cuci Kering 3 kg, Setrika 2 pcs. Status ambil: Belum diambil. Pengantaran: tidak ada.

#### When

Customer kirim "info lengkap"

#### Then

Bot merespons dengan Mode Detail Ringan:

```
Baik Kak, berikut detail transaksi Kakak:

ID Transaksi: TMD001
Outlet: Perkilo Premium Pejaten
Nama: Budi
Status: Siap diambil
Progress: 100%
Diterima: 24 Juni 2026
Estimasi selesai: 26 Juni 2026
Total: Rp85.000 · Lunas

Layanan:
- Cuci Kering — 3 kg
- Setrika — 2 pcs

Status ambil: Belum diambil
Pengantaran: Tidak ada
```

## 8. Supporting Information

- Perilaku lintas-capability untuk konteks, koreksi input, outlet, privasi referensi, dan hasil ambigu mengikuti FEAT-AI-013.
- Effective state, fallback, timeout, dan handoff mengikuti PRD-AI-001.
