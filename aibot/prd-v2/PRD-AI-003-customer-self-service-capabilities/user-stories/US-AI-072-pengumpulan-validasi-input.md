---
id: US-AI-072
prd: PRD-AI-003
epic: EPIC-AI-003
feature: FEAT-AI-013
title: Mengumpulkan dan Memvalidasi Input
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-072 - Mengumpulkan dan Memvalidasi Input

## 1. Parent Reference

- PRD: PRD-AI-003 - Customer Self-Service Capabilities
- Epic: EPIC-AI-003 - Intent Capabilities
- Feature: FEAT-AI-013 - Orkestrasi Self-Service

## 2. User Story

Sebagai customer,  
Saya ingin bot meminta informasi yang dibutuhkan secara bertahap dan menjelaskan ketika input belum valid,  
Agar saya dapat menyelesaikan self-service tanpa bingung mengenai data yang harus dikirim.

## 3. Goal

Bot mengidentifikasi slot yang sudah tersedia, meminta satu slot yang belum tersedia, serta membedakan format tidak valid dari data valid yang tidak ditemukan.

## 4. Acceptance Criteria

- Bot tidak meminta slot yang sudah tersedia dan valid pada pesan customer.
- Bot hanya meminta satu slot per giliran sesuai BR-AI-370.
- Permintaan slot menyebut nama data dan satu contoh format yang diterima.
- Input kosong, hanya spasi, atau tidak memenuhi format tidak dikirim untuk pencarian data.
- Format valid tetapi data tidak ditemukan mengikuti retry rule capability terkait.
- Setelah input valid, bot melanjutkan flow tanpa meminta konfirmasi tambahan kecuali hasilnya ambigu.
- Perintah **menu** dan **hubungi agent** tetap dapat digunakan saat slot-filling.

## 5. Form Rules

| Label | Field Key | Type | Required | Unique | Max Length | Description |
|---|---|---|---:|---:|---:|---|
| ID Transaksi | transaction_id | string | conditional | no | Mengikuti format existing | Wajib untuk cek status laundry jika belum tersedia dalam pesan |
| Nomor Ticket | ticket_id | string | conditional | no | Mengikuti format existing | Wajib untuk cek status ticket jika belum tersedia dalam pesan |
| Tanggal Pertanyaan | requested_date | date/natural language | conditional | no | - | Digunakan jika customer menanyakan jadwal tanggal tertentu |
| Nama Item | requested_item_name | string | conditional | no | 150 | Nama promo, membership, atau layanan yang ingin dilihat |

## 6. Form Notes

- `conditional` berarti wajib hanya untuk capability dan skenario yang membutuhkan field tersebut.
- Bot menerima input natural language, kemudian meminta ulang jika nilai belum dapat ditentukan secara tunggal.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-072-01 | Customer meminta status laundry tanpa ID | Bot mengenali intent | Bot meminta ID transaksi dengan contoh format |
| SC-AI-072-02 | Customer menyertakan ID transaksi yang valid pada pesan pertama | Bot mengenali intent dan slot | Bot langsung mencari status tanpa meminta ID lagi |
| SC-AI-072-03 | Bot meminta nomor ticket | Customer mengirim input kosong atau format tidak valid | Bot menjelaskan format belum sesuai dan meminta ulang |
| SC-AI-072-04 | Customer mengirim referensi berformat valid tetapi tidak ditemukan | Pencarian selesai | Bot mengikuti retry not-found pada capability terkait |

### Detail SC-AI-072-03 - Format Input Tidak Valid

#### Given

Flow cek status ticket aktif dan bot sedang menunggu nomor ticket.

#### When

Customer mengirim teks yang tidak dapat dikenali sebagai nomor ticket.

#### Then

Bot tidak melakukan pencarian dan meminta customer mengirim ulang nomor ticket lengkap.

#### Validation

- Pesan validasi tidak menyatakan ticket tidak ditemukan karena pencarian belum dilakukan.
- Contoh tidak boleh menggunakan nomor ticket customer lain.

#### Error Message

> Nomor ticket-nya belum terbaca, Kak. Silakan kirim nomor ticket lengkap seperti yang tertera pada informasi ticket.

## 8. Supporting Information

- Format pasti ID mengikuti sumber data existing dan perlu dikunci bersama Product/QA sebelum status Ready.
