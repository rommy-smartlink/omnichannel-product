---
id: US-AI-049
prd: PRD-AI-003
epic: EPIC-AI-003
feature: FEAT-AI-006
title: Cek Status Ticket — Nomor Tidak Ditemukan
status: Draft
priority: Medium
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-049 - Cek Status Ticket — Nomor Tidak Ditemukan

## 1. Parent Reference

- PRD: PRD-AI-003 - Customer Self-Service Capabilities
- Epic: EPIC-AI-003 - Intent Capabilities
- Feature: FEAT-AI-006 - Cek Status Ticket

## 2. User Story

Sebagai bot engine,
Saya perlu meminta customer mengulang nomor ticket jika tidak ditemukan, maksimal 2 kali,
Agar customer punya kesempatan memperbaiki kesalahan penulisan sebelum eskalasi ke agen.

## 3. Goal

Bot memberi retry 2× untuk nomor ticket yang tidak ditemukan. Percobaan ke-3 → eskalasi ke agen.

## 4. Acceptance Criteria

- Nomor ticket tidak ditemukan → bot minta cek ulang: "Nomor ticket belum ditemukan. Boleh dicek lagi ya Kak?"
- Retry counter = 1.
- Customer kirim nomor lagi → tetap tidak ditemukan → minta ulang kedua: "Masih belum ditemukan. Coba sekali lagi ya Kak."
- Retry counter = 2.
- Customer kirim nomor lagi → masih tidak ditemukan → eskalasi: "Mohon maaf, nomor ticket masih belum ditemukan. Saya hubungkan Kakak ke agent ya."
- Bot silent setelah eskalasi.
- Retry counter reset saat customer cek nomor baru.
- Command "batal"/"menu" di tengah retry → override, counter reset.

## 5. Form Rules

> Tidak ada form UI untuk user story ini. Input diberikan customer melalui percakapan WhatsApp.

## 6. Form Notes

- Validasi input percakapan mengikuti business rule pada Feature terkait dan FEAT-AI-013.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-049-01 | Nomor "TKOM-9999" tidak ada | Customer kirim "Cek TKOM-9999" (retry 1) | Bot minta cek ulang. |
| SC-AI-049-02 | Nomor masih tidak ditemukan, retry=1 | Customer kirim "TKOM-8888" (retry 2) | Bot minta coba sekali lagi. |
| SC-AI-049-03 | Nomor masih tidak ditemukan, retry=2 | Customer kirim "TKOM-7777" (retry 3) | Bot eskalasi, silent. |

### Detail SC-AI-049-01 - Retry Pertama

#### Given

Nomor ticket "TKOM-9999" tidak ditemukan di sistem outlet A.

#### When

Customer kirim "Cek tiket TKOM-9999"

#### Then

```
Nomor ticket belum ditemukan. Boleh dicek lagi ya Kak?
```

## 8. Supporting Information

- Perilaku lintas-capability untuk konteks, koreksi input, outlet, privasi referensi, dan hasil ambigu mengikuti FEAT-AI-013.
- Effective state, fallback, timeout, dan handoff mengikuti PRD-AI-001.
