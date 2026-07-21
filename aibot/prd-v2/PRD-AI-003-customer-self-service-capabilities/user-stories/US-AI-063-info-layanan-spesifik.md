---
id: US-AI-063
prd: PRD-AI-003
epic: EPIC-AI-003
feature: FEAT-AI-011
title: Info Layanan — Layanan Spesifik
status: Draft
priority: Medium
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-063 - Info Layanan — Layanan Spesifik

## 1. Parent Reference

- PRD: PRD-AI-003 - Customer Self-Service Capabilities
- Epic: EPIC-AI-003 - Intent Capabilities
- Feature: FEAT-AI-011 - Info Layanan Outlet

## 2. User Story

Sebagai customer,
Saya ingin menanyakan apakah layanan spesifik tersedia di outlet,
Agar saya cepat dapat jawaban ya/tidak tanpa harus browsing daftar panjang.

## 3. Goal

Customer menyebut layanan spesifik dan bot menjawab tersedia atau tidak. Jika tersedia, tambahkan konteks satuan (pcs, kg, dll). Jika tidak tersedia, tawarkan agent.

## 4. Acceptance Criteria

- Bot mengenali pertanyaan layanan spesifik: "Bisa laundry sepatu?", "Ada express?", "Cuci bed cover ada?".
- Keyword matching case-insensitive, partial match yang reasonable.
- Tersedia → "Ya Kak, [layanan] tersedia di [Nama Outlet] ([satuan]) ya."
- Konteks satuan dari `layanan.satuan.nama` — skip jika kosong.
- Tidak tersedia → "Maaf Kak, untuk [Nama Outlet] layanan [layanan] belum tersedia ya. Kakak bisa menanyakan layanan lain atau saya hubungkan ke agent jika perlu."
- Keyword tidak match layanan manapun → "Maaf Kak, layanan yang Kakak maksud belum tersedia di outlet ini. Saya bisa hubungkan ke agent jika perlu."
- Harga tidak ditampilkan (BR-AI-362).
- Lookup hanya di outlet conversation context.

## 5. Form Rules

> Tidak ada form UI untuk user story ini. Input diberikan customer melalui percakapan WhatsApp.

## 6. Form Notes

- Validasi input percakapan mengikuti business rule pada Feature terkait dan FEAT-AI-013.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-063-01 | Outlet punya layanan "Laundry Sepatu" satuan "pcs" | Customer kirim "Bisa laundry sepatu?" | Bot: "Ya Kak, laundry sepatu tersedia di Outlet Tebet (pcs) ya. Ada yang ingin ditanyakan lagi?" |
| SC-AI-063-02 | Outlet tidak punya layanan "Cuci Kasur" | Customer kirim "Ada cuci kasur?" | Bot: "Maaf Kak, untuk Outlet Tebet layanan cuci kasur belum tersedia ya. Kakak bisa menanyakan layanan lain atau saya hubungkan ke agent jika perlu." |
| SC-AI-063-03 | Customer tanya "laundry karpet" + minta agen | Customer kirim "Ada laundry karpet? Hubungkan agen" | Bot eskalasi, info layanan diabaikan. |

### Detail SC-AI-063-01 - Layanan Tersedia

#### Given

Outlet Tebet. Layanan "Laundry Sepatu" dengan satuan "pcs".

#### When

Customer kirim "Bisa laundry sepatu?"

#### Then

```
Ya Kak, laundry sepatu tersedia di Outlet Tebet (pcs) ya.
Ada yang ingin ditanyakan lagi?
```

## 8. Supporting Information

- Perilaku lintas-capability untuk konteks, koreksi input, outlet, privasi referensi, dan hasil ambigu mengikuti FEAT-AI-013.
- Effective state, fallback, timeout, dan handoff mengikuti PRD-AI-001.
