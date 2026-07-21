---
id: US-AI-067
prd: PRD-AI-001
epic: EPIC-AI-001
feature: FEAT-AI-001
title: Melanjutkan Konteks Percakapan
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-067 - Melanjutkan Konteks Percakapan

## 1. Parent Reference

- PRD: PRD-AI-001 - Bot Runtime Behavior
- Epic: EPIC-AI-001 - Conversation Flow & Routing
- Feature: FEAT-AI-001 - Greeting, Intent Classification, Slot Filling & Handoff

## 2. User Story

Sebagai customer yang sedang berbicara dengan bot,  
Saya ingin bot memahami pesan lanjutan berdasarkan konteks percakapan sebelumnya,  
Agar saya tidak perlu mengulang nama outlet, intent, atau referensi yang baru saja diberikan.

## 3. Goal

Bot mempertahankan konteks aktif yang masih relevan, tetapi meminta klarifikasi apabila referensi customer dapat mengarah ke lebih dari satu data atau kebutuhan.

## 4. Acceptance Criteria

- Bot menggunakan outlet dari percakapan aktif selama customer tidak menyebut outlet lain.
- Bot memahami referensi lanjutan seperti "yang kedua", "detailnya", "kalau besok", dan "statusnya bagaimana" berdasarkan respons terakhir.
- Konteks dari flow yang sudah selesai boleh dipakai untuk pertanyaan lanjutan yang masih berkaitan.
- Bot tidak memakai ID transaksi, nomor ticket, promo, membership, atau layanan dari percakapan lain.
- Jika referensi memiliki lebih dari satu kemungkinan, bot meminta customer memilih sebelum memberikan jawaban.
- Perintah **menu** mengakhiri flow aktif dan menghapus slot yang belum selesai.
- Handoff mengakhiri seluruh konteks bot untuk sesi tersebut.

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-067-01 | Bot baru menampilkan daftar promo bernomor | Customer mengirim "detail yang kedua" | Bot menampilkan detail promo nomor 2 dari daftar terakhir |
| SC-AI-067-02 | Bot baru menjawab jam operasional hari ini | Customer mengirim "kalau besok?" | Bot menjawab jadwal besok untuk outlet yang sama |
| SC-AI-067-03 | Dua data dalam respons terakhir dapat dirujuk sebagai "yang tadi" | Customer mengirim "jelaskan yang tadi" | Bot meminta customer memilih data yang dimaksud |
| SC-AI-067-04 | Bot sedang mengumpulkan ID transaksi | Customer mengirim "menu" | Bot membatalkan flow aktif dan menampilkan menu layanan yang tersedia |

### Detail SC-AI-067-03 - Referensi Tidak Tunggal

#### Given

Respons terakhir memuat dua membership dan customer belum memilih salah satunya.

#### When

Customer mengirim "yang tadi berapa biayanya?".

#### Then

Bot tidak menebak membership yang dimaksud dan meminta customer memilih nama atau nomor membership.

#### Validation

- Pilihan hanya berasal dari data pada respons terakhir.
- Bot tidak menampilkan data tambahan sebelum pilihan customer jelas.

#### Error Message

> Kak, yang dimaksud membership pertama atau kedua? Bisa sebutkan nama atau nomornya, ya.

## 8. Supporting Information

- Berlaku untuk seluruh capability pada PRD-AI-003.
- Penguncian setelah handoff mengikuti BR-AI-105.
