---
id: US-AI-069
prd: PRD-AI-001
epic: EPIC-AI-001
feature: FEAT-AI-001
title: Menangani Beberapa Intent dalam Satu Pesan
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-069 - Menangani Beberapa Intent dalam Satu Pesan

## 1. Parent Reference

- PRD: PRD-AI-001 - Bot Runtime Behavior
- Epic: EPIC-AI-001 - Conversation Flow & Routing
- Feature: FEAT-AI-001 - Greeting, Intent Classification, Slot Filling & Handoff

## 2. User Story

Sebagai customer,  
Saya ingin menyampaikan lebih dari satu kebutuhan dalam satu pesan,  
Agar saya tidak perlu mengirim pertanyaan satu per satu.

## 3. Goal

Bot merespons maksimal dua intent self-service yang sama-sama siap diproses, dengan urutan yang mengikuti pesan customer dan tanpa mencampur data.

## 4. Acceptance Criteria

- Bot memproses maksimal dua intent self-service dalam satu respons.
- Urutan respons mengikuti urutan kebutuhan dalam pesan customer.
- Setiap bagian respons memiliki label singkat agar hasil mudah dibaca.
- Jika salah satu intent membutuhkan slot, bot menjawab intent yang lengkap terlebih dahulu lalu meminta satu slot untuk intent yang belum lengkap.
- Permintaan agent selalu memiliki prioritas tertinggi; capability lain tidak diproses setelah handoff dimulai.
- Jika terdapat lebih dari dua intent, bot meminta customer memilih dua kebutuhan yang ingin didahulukan.
- Intent yang OFF atau tidak siap mengikuti fallback runtime masing-masing.

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-069-01 | Jam operasional dan promo aktif siap | Customer menanyakan jam tutup dan promo hari ini | Bot menjawab kedua intent sesuai urutan pertanyaan |
| SC-AI-069-02 | Jam operasional siap, ID laundry belum diberikan | Customer menanyakan jam tutup dan status laundry | Bot menjawab jam tutup lalu meminta ID transaksi |
| SC-AI-069-03 | Customer menyampaikan tiga kebutuhan | Bot mengenali tiga intent | Bot meminta customer memilih dua kebutuhan yang didahulukan |
| SC-AI-069-04 | Customer meminta promo dan agent | Bot mengenali `get_active_promos` dan `talk_to_agent` | Bot memprioritaskan handoff dan tidak mengambil data promo |

### Detail SC-AI-069-02 - Satu Intent Belum Lengkap

#### Given

Data jam operasional tersedia dan intent laundry memerlukan ID transaksi.

#### When

Customer mengirim "Hari ini tutup jam berapa dan laundry saya sudah selesai belum?".

#### Then

Bot menjawab jam tutup hari ini, kemudian meminta ID transaksi untuk melanjutkan pengecekan laundry.

#### Validation

- Bot hanya meminta satu slot pada satu waktu.
- Jawaban jam operasional tidak diulang setelah customer mengirim ID transaksi.

## 8. Supporting Information

- Batas dua intent menjaga respons WhatsApp tetap ringkas dan dapat ditindaklanjuti.
