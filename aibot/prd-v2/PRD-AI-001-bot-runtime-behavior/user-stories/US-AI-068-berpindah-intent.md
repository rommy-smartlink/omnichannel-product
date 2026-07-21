---
id: US-AI-068
prd: PRD-AI-001
epic: EPIC-AI-001
feature: FEAT-AI-001
title: Berpindah Intent di Tengah Percakapan
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-068 - Berpindah Intent di Tengah Percakapan

## 1. Parent Reference

- PRD: PRD-AI-001 - Bot Runtime Behavior
- Epic: EPIC-AI-001 - Conversation Flow & Routing
- Feature: FEAT-AI-001 - Greeting, Intent Classification, Slot Filling & Handoff

## 2. User Story

Sebagai customer,  
Saya ingin mengganti topik meskipun flow sebelumnya belum selesai,  
Agar saya dapat memperoleh bantuan sesuai kebutuhan terbaru tanpa harus menyelesaikan pertanyaan yang tidak lagi relevan.

## 3. Goal

Bot memprioritaskan intent terbaru yang dikenali dengan yakin, membatalkan slot-filling sebelumnya, dan tidak mencampur data antar-intent.

## 4. Acceptance Criteria

- Intent baru yang jelas menggantikan flow aktif sebelumnya.
- Slot yang hanya berlaku untuk flow sebelumnya tidak digunakan pada intent baru.
- Bot tidak meminta konfirmasi pembatalan untuk perpindahan ke capability self-service lain.
- Perpindahan ke **hubungi agent** langsung mengikuti flow handoff.
- Pesan yang masih mungkin merupakan jawaban slot diproses sebagai jawaban slot, bukan dianggap intent baru.
- Jika maksud perpindahan ambigu, bot meminta customer memilih melanjutkan flow atau mengganti kebutuhan.

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-068-01 | Bot sedang meminta ID transaksi laundry | Customer bertanya "outlet tutup jam berapa?" | Bot membatalkan pengumpulan ID dan menjawab jam operasional |
| SC-AI-068-02 | Bot sedang meminta nomor ticket | Customer mengirim nomor yang sesuai format ticket | Bot memperlakukan pesan sebagai jawaban slot ticket |
| SC-AI-068-03 | Bot sedang menampilkan promo | Customer meminta agent | Bot menghentikan flow promo dan menjalankan handoff |
| SC-AI-068-04 | Bot meminta ID transaksi | Customer mengirim "cek yang lain" | Bot meminta klarifikasi apakah mengganti transaksi atau mengganti layanan |

### Detail SC-AI-068-01 - Intent Baru yang Jelas

#### Given

Flow `check_laundry_status` aktif dan ID transaksi belum terisi.

#### When

Customer mengirim "outlet buka sampai jam berapa?".

#### Then

Bot menghapus kebutuhan slot ID transaksi, memproses intent `get_operating_hours`, lalu memberikan jam operasional outlet pada konteks percakapan.

#### Notes

Customer dapat kembali ke cek status laundry melalui menu atau pesan baru.

## 8. Supporting Information

- Isi respons intent mengikuti PRD-AI-003.
