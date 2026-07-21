---
id: US-AI-043
prd: PRD-AI-003
epic: EPIC-AI-003
feature: FEAT-AI-005
title: Cek Status Laundry — Mode Ringkas
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-043 - Cek Status Laundry — Mode Ringkas

## 1. Parent Reference

- PRD: PRD-AI-003 - Customer Self-Service Capabilities
- Epic: EPIC-AI-003 - Intent Capabilities
- Feature: FEAT-AI-005 - Cek Status Laundry

## 2. User Story

Sebagai customer,
Saya ingin menanyakan status laundry dengan mengirim ID transaksi dalam bahasa natural,
Agar saya bisa tahu progress laundry tanpa harus menunggu agen.

## 3. Goal

Customer mengirim ID transaksi (prefix 3 huruf + digit) dan bot menampilkan status ringkas dengan label customer-friendly: ID, outlet, nama, status, progress (%), diterima, estimasi selesai, total + Lunas/Belum lunas, dan arahan berikutnya.

## 4. Acceptance Criteria

- Bot mengenali intent `check_laundry_status` dari pesan natural customer.
- Jika ID transaksi belum ada di pesan → bot meminta: "Baik Kak, boleh kirim ID transaksinya?"
- Jika ID tidak lengkap (format bukan prefix 3 huruf + digit) → bot meminta ulang ID lengkap.
- Jika transaksi ditemukan → tampilkan Mode Ringkas (≤ 8 baris + Total + Arahan).
- Status internal dipetakan ke label customer-friendly (sesuai BR-AI-305).
- Total tagihan ditampilkan dengan label "Lunas" / "Belum lunas".
- Field sensitif tidak ditampilkan (BR-AI-302).
- Lookup hanya di outlet conversation context.
- Tanggal dalam format "DD MMMM YYYY".

## 5. Form Rules

> Tidak ada form UI untuk user story ini. Input diberikan customer melalui percakapan WhatsApp.

## 6. Form Notes

- Validasi input percakapan mengikuti business rule pada Feature terkait dan FEAT-AI-013.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-043-01 | Customer di outlet A, transaksi TRX001 ada, status "Washing" | Customer kirim "Cek laundry TRX001" | Bot tampilkan Mode Ringkas: status "Sedang dicuci", progress, estimasi selesai, arahan. |
| SC-AI-043-02 | Customer di outlet A, belum kirim ID | Customer kirim "Mau cek laundry" | Bot minta "Baik Kak, boleh kirim ID transaksinya?" |
| SC-AI-043-03 | Customer kirim ID tidak lengkap (hanya angka) | Customer kirim "260624160854112" | Bot minta kirim ulang ID lengkap dengan kode depan. |
| SC-AI-043-04 | Transaksi tidak ditemukan, percobaan ke-1 | Customer kirim ID yang tidak ada | Bot minta cek ulang ID. Retry counter = 1. |

### Detail SC-AI-043-01 - Status Ditemukan

#### Given

Customer di outlet Perkilo Premium Pejaten. Transaksi `TMD260624160854112` ada dengan status "Washing", progress 45%, diterima 24 Juni 2026, estimasi selesai 26 Juni 2026, total Rp85.000 (Lunas).

#### When

Customer kirim "Cek laundry dong TMD260624160854112"

#### Then

Bot merespons:

```
Baik Kak, berikut status transaksi Kakak:

ID Transaksi: TMD260624160854112
Outlet: Perkilo Premium Pejaten
Nama: Budi
Status: Sedang dicuci
Progress: 45%
Diterima: 24 Juni 2026
Estimasi selesai: 26 Juni 2026
Total: Rp85.000 · Lunas

Laundry Kakak masih diproses. Silakan cek lagi mendekati waktu estimasi selesai ya.
```

## 8. Supporting Information

- Perilaku lintas-capability untuk konteks, koreksi input, outlet, privasi referensi, dan hasil ambigu mengikuti FEAT-AI-013.
- Effective state, fallback, timeout, dan handoff mengikuti PRD-AI-001.
