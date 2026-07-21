---
id: US-AI-066
prd: PRD-AI-003
epic: EPIC-AI-003
feature: FEAT-AI-011
title: Info Layanan — Tanya Harga
status: Draft
priority: Medium
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-066 - Info Layanan — Tanya Harga

## 1. Parent Reference

- PRD: PRD-AI-003 - Customer Self-Service Capabilities
- Epic: EPIC-AI-003 - Intent Capabilities
- Feature: FEAT-AI-011 - Info Layanan Outlet

## 2. User Story

Sebagai customer,
Saya ingin tahu harga layanan,
Tetapi bot mengarahkan saya ke agent karena harga tidak bisa ditampilkan otomatis.

## 3. Goal

Customer bertanya harga layanan dan bot mengkonfirmasi layanan tersedia, lalu mengarahkan ke agent untuk detail harga. Bot tidak menyebut nominal harga.

## 4. Acceptance Criteria

- Bot mengenali pertanyaan harga: "Harga laundry per kg?", "Express berapa?", "Biaya cuci kering?", "Berapa harganya?".
- Bot menjawab: "Layanan tersebut tersedia di outlet ini, Kak. Untuk harga detail, saya hubungkan Kakak ke agent outlet ya."
- Jika layanan tidak tersedia → bot sampaikan belum tersedia dulu (tidak perlu arahkan ke agent untuk harga).
- Bot tidak menyebut nominal harga dalam kondisi apapun (BR-AI-362).
- Jika customer memaksa tanya harga lagi → eskalasi.

## 5. Form Rules

> Tidak ada form UI untuk user story ini. Input diberikan customer melalui percakapan WhatsApp.

## 6. Form Notes

- Validasi input percakapan mengikuti business rule pada Feature terkait dan FEAT-AI-013.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-066-01 | Outlet punya layanan "Express Wash" | Customer kirim "Express berapa harganya?" | Bot: "Layanan tersebut tersedia di outlet ini, Kak. Untuk harga detail, saya hubungkan Kakak ke agent outlet ya." + eskalasi. |
| SC-AI-066-02 | Outlet tidak punya "Cuci Karpet" | Customer kirim "Harga cuci karpet?" | Bot: "Maaf Kak, layanan cuci karpet belum tersedia." Tidak sebut harga. |
| SC-AI-066-03 | Customer tanya "laundry per kg berapa?" setelah lihat daftar | Customer tanya harga | Bot arahkan ke agent tanpa menyebut nominal. |

### Detail SC-AI-066-01 - Tanya Harga Layanan Tersedia

#### Given

Outlet Tebet. Layanan "Express Wash" tersedia.

#### When

Customer kirim "Express berapa harganya?"

#### Then

```
Layanan tersebut tersedia di outlet ini, Kak.
Untuk harga detail, saya hubungkan Kakak ke agent outlet ya.
```

## 8. Supporting Information

- Perilaku lintas-capability untuk konteks, koreksi input, outlet, privasi referensi, dan hasil ambigu mengikuti FEAT-AI-013.
- Effective state, fallback, timeout, dan handoff mengikuti PRD-AI-001.
