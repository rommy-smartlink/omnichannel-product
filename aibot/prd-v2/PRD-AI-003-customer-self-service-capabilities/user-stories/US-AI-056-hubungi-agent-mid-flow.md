---
id: US-AI-056
prd: PRD-AI-003
epic: EPIC-AI-003
feature: FEAT-AI-008
title: Hubungi Agent — Mid-Flow Eskalasi
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-056 - Hubungi Agent — Mid-Flow Eskalasi

## 1. Parent Reference

- PRD: PRD-AI-003 - Customer Self-Service Capabilities
- Epic: EPIC-AI-003 - Intent Capabilities
- Feature: FEAT-AI-008 - Hubungi Agent

## 2. User Story

Sebagai customer,
Saya ingin bisa minta agen di tengah-tengah proses cek laundry atau intent lain,
Agar saya tidak dipaksa menyelesaikan flow bot ketika saya sudah ingin bantuan manusia.

## 3. Goal

Customer di tengah slot-filling intent lain (cek laundry, cek ticket, dll) tetap bisa eskalasi ke agen kapan saja. Slot-filling state dibuang, bot langsung eskalasi tanpa meminta data tambahan.

## 4. Acceptance Criteria

- Customer di mid-flow intent manapun → kirim pesan minta agen → eskalasi langsung (BR-AI-330).
- Bot tidak meminta menyelesaikan slot-filling terlebih dahulu.
- Slot-filling state dibuang (tidak disimpan).
- Jika customer kirim pesan yang mengandung info intent lain + minta agen → eskalasi, info tambahan diabaikan.
- Konfirmasi eskalasi tetap ditampilkan.
- Bot silent setelah handoff.
- Mid-flow termasuk: sedang menunggu input ID transaksi, sedang menunggu input nomor ticket, setelah melihat hasil pencarian, dll.

## 5. Form Rules

> Tidak ada form UI untuk user story ini. Input diberikan customer melalui percakapan WhatsApp.

## 6. Form Notes

- Validasi input percakapan mengikuti business rule pada Feature terkait dan FEAT-AI-013.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-056-01 | Customer di flow cek laundry, bot minta ID transaksi | Customer kirim "hubungi agen aja" | Bot eskalasi, slot-filling state dibuang. |
| SC-AI-056-02 | Customer di flow cek laundry, bot sudah tampilkan status | Customer kirim "saya mau komplain, panggil CS" | Bot eskalasi langsung, tidak tanya "ada yang lain?". |
| SC-AI-056-03 | Customer di mid-flow, kirim "TMD123 dan tolong panggil agen" | Customer gabung info + eskalasi | Bot eskalasi, info TMD123 diabaikan. |

### Detail SC-AI-056-01 - Mid-Flow Slot Filling

#### Given

Customer di outlet A. Bot sedang menunggu input ID transaksi (flow check_laundry_status). Sebelumnya bot kirim: "Baik Kak, boleh kirim ID transaksinya?"

#### When

Customer kirim "hubungi agen aja"

#### Then

```
Baik Kak, saya hubungkan ke agent Perkilo Premium Pejaten agar bisa dibantu lebih lanjut.
Mohon tunggu sebentar ya.
```

Slot-filling state dibuang. Handoff. Bot silent.

## 8. Supporting Information

- Perilaku lintas-capability untuk konteks, koreksi input, outlet, privasi referensi, dan hasil ambigu mengikuti FEAT-AI-013.
- Effective state, fallback, timeout, dan handoff mengikuti PRD-AI-001.
