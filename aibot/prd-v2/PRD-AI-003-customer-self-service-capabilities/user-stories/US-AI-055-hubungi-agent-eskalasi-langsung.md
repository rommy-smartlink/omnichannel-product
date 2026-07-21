---
id: US-AI-055
prd: PRD-AI-003
epic: EPIC-AI-003
feature: FEAT-AI-008
title: Hubungi Agent — Eskalasi Langsung
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-055 - Hubungi Agent — Eskalasi Langsung

## 1. Parent Reference

- PRD: PRD-AI-003 - Customer Self-Service Capabilities
- Epic: EPIC-AI-003 - Intent Capabilities
- Feature: FEAT-AI-008 - Hubungi Agent

## 2. User Story

Sebagai customer,
Saya ingin meminta berbicara dengan agen manusia kapan saja tanpa syarat,
Agar saya tidak terjebak di flow bot yang tidak membantu kebutuhan saya.

## 3. Goal

Customer mengirim pesan yang mengindikasikan ingin agen (admin, CS, manusia, komplain, dll) dan bot langsung menghubungkan ke agen dengan satu konfirmasi. Bot berhenti membalas setelah eskalasi. Tidak ada tanya alasan, tidak ada konfirmasi kedua, tidak ada batas jumlah permintaan.

## 4. Acceptance Criteria

- Intent `talk_to_agent` selalu ON — tidak terpengaruh toggle intent per outlet (BR-AI-331).
- Trigger phrases dikenali: "admin", "CS", "agen", "manusia", "orang", "komplain", "bot doang", "capek", "bosan", "butuh bantuan", "panggil supervisor", dsb (BR-AI-334).
- Bot kirim konfirmasi SEKALI: "Baik Kak, saya hubungkan ke agent [Nama Outlet] agar bisa dibantu lebih lanjut. Mohon tunggu sebentar ya."
- Conversation di-handoff ke antrean agen outlet.
- Bot silent setelah handoff (BR-AI-332).
- Eskalasi final — "batal" setelah eskalasi tidak berlaku (BR-AI-333).
- Jika agen offline → conversation unassigned, tidak ada pesan tambahan dari bot.
- Customer tidak perlu memberikan alasan.

## 5. Form Rules

> Tidak ada form UI untuk user story ini. Input diberikan customer melalui percakapan WhatsApp.

## 6. Form Notes

- Validasi input percakapan mengikuti business rule pada Feature terkait dan FEAT-AI-013.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-055-01 | Customer di outlet A, Master ON, Outlet ON | Customer kirim "Saya mau bicara dengan admin" | Bot konfirmasi eskalasi, handoff, silent. |
| SC-AI-055-02 | Customer kirim "agen dong" | Customer kirim "agen dong" | Bot langsung eskalasi tanpa tanya alasan. |
| SC-AI-055-03 | Customer kirim "bot doang, capek ngomong sama bot" | Customer ekspresi frustrasi | Bot eskalasi — trigger inclusive. |
| SC-AI-055-04 | Customer sudah di-eskalasi | Customer kirim "batal" setelah eskalasi | Bot tetap silent — eskalasi final. |

### Detail SC-AI-055-01 - Eskalasi Normal

#### Given

Customer di outlet Perkilo Premium Pejaten. Master ON, Outlet ON.

#### When

Customer kirim "Saya mau bicara dengan admin"

#### Then

```
Baik Kak, saya hubungkan ke agent Perkilo Premium Pejaten agar bisa dibantu lebih lanjut.
Mohon tunggu sebentar ya.
```

Kemudian handoff ke agent. Bot silent.

## 8. Supporting Information

- Perilaku lintas-capability untuk konteks, koreksi input, outlet, privasi referensi, dan hasil ambigu mengikuti FEAT-AI-013.
- Effective state, fallback, timeout, dan handoff mengikuti PRD-AI-001.
