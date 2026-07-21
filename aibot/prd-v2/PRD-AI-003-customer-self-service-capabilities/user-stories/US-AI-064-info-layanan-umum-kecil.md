---
id: US-AI-064
prd: PRD-AI-003
epic: EPIC-AI-003
feature: FEAT-AI-011
title: Info Layanan — Umum ≤20 Layanan
status: Draft
priority: Medium
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-064 - Info Layanan — Umum ≤20 Layanan

## 1. Parent Reference

- PRD: PRD-AI-003 - Customer Self-Service Capabilities
- Epic: EPIC-AI-003 - Intent Capabilities
- Feature: FEAT-AI-011 - Info Layanan Outlet

## 2. User Story

Sebagai customer,
Saya ingin melihat semua layanan yang tersedia di outlet kecil,
Agar saya bisa browsing pilihan tanpa perlu menyebut satu per satu.

## 3. Goal

Customer bertanya umum ("Ada layanan apa saja?") di outlet dengan ≤20 layanan, dan bot menampilkan semua layanan dalam format list sederhana.

## 4. Acceptance Criteria

- Bot mengenali pertanyaan umum: "Ada layanan apa saja?", "Layanan apa aja?", "Apa saja yang ada?", "Daftar layanan".
- Jika total layanan outlet ≤ 20 → tampilkan semua layanan yang tersedia.
- Format: "Di [Nama Outlet], layanan yang tersedia:\n\n1. [Layanan 1]\n2. [Layanan 2]\n..."
- Tidak menyebut satuan di list umum — hanya nama layanan.
- Tidak menampilkan harga.
- Jika `layanan.nama_layanan` kosong → item diabaikan.
- Outlet tanpa layanan sama sekali → "Maaf Kak, saat ini Outlet [Nama] belum memiliki layanan terdaftar. Saya bisa hubungkan ke agent untuk info lebih lanjut."

## 5. Form Rules

> Tidak ada form UI untuk user story ini. Input diberikan customer melalui percakapan WhatsApp.

## 6. Form Notes

- Validasi input percakapan mengikuti business rule pada Feature terkait dan FEAT-AI-013.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-064-01 | Outlet Menteng punya 4 layanan | Customer kirim "Ada layanan apa saja?" | Bot tampilkan list 1–4. |
| SC-AI-064-02 | Outlet punya 20 layanan | Customer kirim "Layanan apa aja?" | Bot tampilkan semua 20 layanan. |
| SC-AI-064-03 | Outlet tidak punya layanan terdaftar | Customer kirim "Layanan?" | Bot: "Maaf Kak, saat ini Outlet X belum memiliki layanan terdaftar..." |

### Detail SC-AI-064-01 - 4 Layanan

#### Given

Outlet Menteng. 4 layanan: Cuci Kering, Setrika Saja, Express Wash, Laundry Sepatu.

#### When

Customer kirim "Ada layanan apa saja?"

#### Then

```
Di Outlet Menteng, layanan yang tersedia:

1. Cuci Kering
2. Setrika Saja
3. Express Wash
4. Laundry Sepatu
```

## 8. Supporting Information

- Perilaku lintas-capability untuk konteks, koreksi input, outlet, privasi referensi, dan hasil ambigu mengikuti FEAT-AI-013.
- Effective state, fallback, timeout, dan handoff mengikuti PRD-AI-001.
