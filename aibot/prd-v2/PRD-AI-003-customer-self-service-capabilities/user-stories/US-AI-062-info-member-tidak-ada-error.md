---
id: US-AI-062
prd: PRD-AI-003
epic: EPIC-AI-003
feature: FEAT-AI-010
title: Info Member — Tidak Ada / Error
status: Draft
priority: Medium
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-062 - Info Member — Tidak Ada / Error

## 1. Parent Reference

- PRD: PRD-AI-003 - Customer Self-Service Capabilities
- Epic: EPIC-AI-003 - Intent Capabilities
- Feature: FEAT-AI-010 - Info Member

## 2. User Story

Sebagai bot engine,
Saya perlu memberi fallback yang jelas saat tidak ada membership aktif atau sistem error,
Agar customer tidak bingung atau mendapat informasi kosong.

## 3. Goal

Bot menangani dua kondisi fallback: (1) data membership kosong → pesan informatif tanpa eskalasi, (2) API error/timeout → pesan gangguan + eskalasi ke agen.

## 4. Acceptance Criteria

- Data membership kosong → "Saat ini belum ada membership aktif di [Nama Outlet]." Tidak eskalasi.
- API error (500, timeout, 401) → "Mohon maaf, sistem sedang gangguan. Saya hubungkan Kakak ke agent ya." + eskalasi.
- Bot tidak menampilkan pesan teknis ke customer.
- Setelah fallback tidak ada membership → bot tanya "Ada yang ingin ditanyakan lagi?"

## 5. Form Rules

> Tidak ada form UI untuk user story ini. Input diberikan customer melalui percakapan WhatsApp.

## 6. Form Notes

- Validasi input percakapan mengikuti business rule pada Feature terkait dan FEAT-AI-013.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-062-01 | Outlet tidak punya membership aktif | Customer kirim "Member apa?" | Bot: "Saat ini belum ada membership aktif di Perkilo Premium Pejaten." + "Ada yang ingin ditanyakan lagi?" |
| SC-AI-062-02 | API membership error | Customer kirim "Membership dong" | Bot: "Mohon maaf, sistem sedang gangguan. Saya hubungkan Kakak ke agent ya." + eskalasi. |

### Detail SC-AI-062-01 - Tidak Ada Membership

#### Given

Outlet Perkilo Premium Pejaten. Tidak ada membership dengan active = true.

#### When

Customer kirim "Member apa?"

#### Then

```
Saat ini belum ada membership aktif di Perkilo Premium Pejaten.
Ada yang ingin ditanyakan lagi?
```

## 8. Supporting Information

- Perilaku lintas-capability untuk konteks, koreksi input, outlet, privasi referensi, dan hasil ambigu mengikuti FEAT-AI-013.
- Effective state, fallback, timeout, dan handoff mengikuti PRD-AI-001.
