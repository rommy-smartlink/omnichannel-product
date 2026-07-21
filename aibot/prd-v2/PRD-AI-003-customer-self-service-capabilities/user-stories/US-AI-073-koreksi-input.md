---
id: US-AI-073
prd: PRD-AI-003
epic: EPIC-AI-003
feature: FEAT-AI-013
title: Mengoreksi Input Sebelum Hasil
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-073 - Mengoreksi Input Sebelum Hasil

## 1. Parent Reference

- PRD: PRD-AI-003 - Customer Self-Service Capabilities
- Epic: EPIC-AI-003 - Intent Capabilities
- Feature: FEAT-AI-013 - Orkestrasi Self-Service

## 2. User Story

Sebagai customer,  
Saya ingin memperbaiki ID, tanggal, outlet, atau pilihan yang salah,  
Agar bot menggunakan data terbaru tanpa memaksa saya mengulang flow dari awal.

## 3. Goal

Koreksi eksplisit customer menggantikan input lama pada flow aktif dan bot menjelaskan nilai yang digunakan ketika koreksi berpotensi mengubah hasil.

## 4. Acceptance Criteria

- Frasa seperti "salah", "maksud saya", "ganti", dan "bukan itu" diperlakukan sebagai indikasi koreksi jika disertai nilai pengganti.
- Nilai terbaru menggantikan nilai lama sesuai BR-AI-371.
- Retry not-found dihitung berdasarkan upaya pencarian, bukan jumlah pesan koreksi sebelum pencarian.
- Jika koreksi dikirim setelah hasil final, bot memperlakukannya sebagai pencarian baru dalam capability yang sama.
- Koreksi yang tidak menyertakan nilai baru diikuti permintaan nilai pengganti.
- Bot tidak menggabungkan sebagian ID lama dengan sebagian ID baru.

## 5. Form Rules

> Mengikuti Form Rules pada US-AI-072.

## 6. Form Notes

- Seluruh nilai lama pada slot yang dikoreksi diganti, bukan ditambal sebagian.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-073-01 | Customer baru mengirim ID transaksi pertama dan hasil belum diberikan | Customer mengirim "salah, ID-nya TRX-002" | Bot menggunakan TRX-002 dan mengabaikan ID pertama |
| SC-AI-073-02 | Bot meminta tanggal | Customer mengirim "bukan besok, hari Jumat" | Bot memakai hari Jumat sebagai tanggal pertanyaan |
| SC-AI-073-03 | Hasil status sudah ditampilkan | Customer mengirim "salah ID, cek TRX-003" | Bot memulai pencarian baru untuk TRX-003 |
| SC-AI-073-04 | Customer mengirim "salah" tanpa nilai baru | Bot mendeteksi koreksi tidak lengkap | Bot meminta nilai pengganti |

### Detail SC-AI-073-01 - Mengganti ID Sebelum Hasil

#### Given

Flow cek status laundry aktif dan customer telah mengirim ID transaksi, tetapi jawaban final belum diberikan.

#### When

Customer mengirim "salah Kak, maksud saya TRX-002".

#### Then

Bot membatalkan penggunaan ID pertama, menggunakan `TRX-002`, dan hasil hanya merujuk ke ID terbaru.

#### Validation

- ID pertama tidak muncul dalam respons final.
- Tidak ada pencampuran data antara dua transaksi.

## 8. Supporting Information

- Berlaku untuk slot lintas-capability, termasuk tanggal dan pilihan bernomor.
