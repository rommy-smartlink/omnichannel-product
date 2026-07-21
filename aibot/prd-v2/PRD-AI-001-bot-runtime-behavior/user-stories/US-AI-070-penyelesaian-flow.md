---
id: US-AI-070
prd: PRD-AI-001
epic: EPIC-AI-001
feature: FEAT-AI-001
title: Menyelesaikan Flow dan Menawarkan Langkah Berikutnya
status: Draft
priority: Medium
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-070 - Menyelesaikan Flow dan Menawarkan Langkah Berikutnya

## 1. Parent Reference

- PRD: PRD-AI-001 - Bot Runtime Behavior
- Epic: EPIC-AI-001 - Conversation Flow & Routing
- Feature: FEAT-AI-001 - Greeting, Intent Classification, Slot Filling & Handoff

## 2. User Story

Sebagai customer,  
Saya ingin mengetahui bahwa permintaan saya sudah selesai dan apa yang dapat dilakukan berikutnya,  
Agar percakapan tidak berhenti secara membingungkan.

## 3. Goal

Setelah jawaban self-service berhasil diberikan, bot menutup flow aktif dan memberi jalur untuk pertanyaan lanjutan, menu, atau agent tanpa mengirim greeting ulang.

## 4. Acceptance Criteria

- Flow dinyatakan selesai setelah output utama berhasil diberikan.
- Bot tidak meminta ulang slot yang sudah menghasilkan jawaban.
- Bot menambahkan pertanyaan penutup singkat hanya jika respons capability belum memiliki CTA atau pertanyaan lanjutan.
- Pesan customer berikutnya diklasifikasikan sebagai pertanyaan lanjutan atau intent baru.
- Bot tidak mengirim menu penuh secara otomatis setelah setiap jawaban.
- Flow yang berakhir dengan handoff tidak menampilkan pertanyaan penutup bot.

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-070-01 | Bot berhasil menampilkan status laundry ringkas | Tidak ada CTA khusus pada respons | Bot menambahkan "Ada yang ingin ditanyakan lagi?" dan menutup flow |
| SC-AI-070-02 | Bot menampilkan daftar promo dengan instruksi memilih promo | Respons sudah memiliki CTA percakapan | Bot tidak menambahkan pertanyaan penutup kedua |
| SC-AI-070-03 | Flow berakhir dengan handoff | Pengalihan dimulai | Bot tidak menawarkan menu atau pertanyaan lain |
| SC-AI-070-04 | Flow sudah selesai | Customer mengirim pertanyaan baru | Bot mengklasifikasikan pesan baru tanpa meminta slot flow sebelumnya |

### Detail SC-AI-070-02 - Respons Sudah Memiliki CTA

#### Given

Bot menampilkan daftar promo dan meminta customer menyebut nomor promo untuk melihat detail.

#### When

Respons daftar promo berhasil dikirim.

#### Then

Bot menunggu pilihan customer dan tidak menambahkan "Ada yang ingin ditanyakan lagi?".

## 8. Supporting Information

- Wording CTA spesifik tetap mengikuti user story capability terkait.
