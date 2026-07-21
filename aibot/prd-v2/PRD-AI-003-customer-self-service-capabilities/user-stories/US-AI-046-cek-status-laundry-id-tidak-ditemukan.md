---
id: US-AI-046
prd: PRD-AI-003
epic: EPIC-AI-003
feature: FEAT-AI-005
title: Cek Status Laundry — ID Tidak Ditemukan
status: Draft
priority: Medium
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-046 - Cek Status Laundry — ID Tidak Ditemukan

## 1. Parent Reference

- PRD: PRD-AI-003 - Customer Self-Service Capabilities
- Epic: EPIC-AI-003 - Intent Capabilities
- Feature: FEAT-AI-005 - Cek Status Laundry

## 2. User Story

Sebagai bot engine,
Saya perlu meminta customer mengulang ID transaksi jika tidak ditemukan, maksimal 2 kali,
Agar customer punya kesempatan memperbaiki kesalahan ketik sebelum eskalasi ke agen.

## 3. Goal

Bot memberi retry 2× untuk ID transaksi yang tidak ditemukan. Percobaan ke-3 → eskalasi ke agen.

## 4. Acceptance Criteria

- ID tidak ditemukan → bot minta cek ulang: "Mohon cek kembali ID transaksi Kakak. Pastikan ID yang dikirim sudah lengkap ya."
- Retry counter = 1 setelah permintaan pertama.
- Customer kirim ID lagi → tetap tidak ditemukan → minta ulang kedua: "ID transaksi masih belum ditemukan. Coba sekali lagi ya Kak."
- Retry counter = 2.
- Customer kirim ID lagi → masih tidak ditemukan → eskalasi ke agen: "Mohon maaf, ID transaksi masih belum ditemukan. Saya hubungkan Kakak ke agent agar bisa dibantu manual."
- Setelah eskalasi, bot silent.
- Retry counter reset saat customer cek ID baru.
- Jika customer kirim "batal" atau "menu" di tengah retry → command override, counter reset.

## 5. Form Rules

> Tidak ada form UI untuk user story ini. Input diberikan customer melalui percakapan WhatsApp.

## 6. Form Notes

- Validasi input percakapan mengikuti business rule pada Feature terkait dan FEAT-AI-013.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-046-01 | ID "TMD999" tidak ada di sistem | Customer kirim "Cek TMD999" (retry 1) | Bot minta cek ulang ID. |
| SC-AI-046-02 | ID masih tidak ditemukan, retry counter = 1 | Customer kirim "TMD888" (retry 2) | Bot minta coba sekali lagi. |
| SC-AI-046-03 | ID masih tidak ditemukan, retry counter = 2 | Customer kirim "TMD777" (retry 3) | Bot eskalasi ke agen, silent. |
| SC-AI-046-04 | Di tengah retry, customer kirim "menu" | Customer kirim "menu" | Bot tampilkan menu utama, retry counter reset. |

### Detail SC-AI-046-01 - Retry Pertama

#### Given

Customer di outlet A. ID "TMD999" tidak ditemukan di sistem.

#### When

Customer kirim "Cek laundry TMD999"

#### Then

Bot merespons:

```
Mohon cek kembali ID transaksi Kakak. Pastikan ID yang dikirim sudah lengkap ya.
```

## 8. Supporting Information

- Perilaku lintas-capability untuk konteks, koreksi input, outlet, privasi referensi, dan hasil ambigu mengikuti FEAT-AI-013.
- Effective state, fallback, timeout, dan handoff mengikuti PRD-AI-001.
