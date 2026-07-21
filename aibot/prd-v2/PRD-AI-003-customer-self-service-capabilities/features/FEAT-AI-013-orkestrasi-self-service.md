---
id: FEAT-AI-013
prd: PRD-AI-003
epic: EPIC-AI-003
title: Orkestrasi Self-Service
status: Draft
owner: Product Owner
created_at: 2026-07-21
updated_at: 2026-07-21
---

# FEAT-AI-013 - Orkestrasi Self-Service

## 1. Purpose

Menentukan perilaku yang berlaku konsisten di seluruh capability self-service ketika bot mengumpulkan input, customer mengoreksi data, konteks outlet berubah, referensi sensitif digunakan, atau hasil pencarian memiliki lebih dari satu kemungkinan.

## 2. Scope

### In Scope

- Pengumpulan satu slot pada satu waktu.
- Validasi format dan koreksi input customer.
- Penetapan serta perubahan konteks outlet.
- Perlindungan referensi transaksi dan ticket.
- Klarifikasi ketika hasil pencarian ambigu.
- Penggunaan kembali input dalam flow aktif.

### Out of Scope

- Isi respons dan display rules tiap capability.
- Pengaturan capability melalui Control Panel.
- Login atau verifikasi identitas tambahan di luar konteks WhatsApp MVP.
- Perubahan data transaksi, ticket, promo, membership, layanan, atau jadwal.

## 3. Business Rules

### BR-AI-370 — Satu Slot per Giliran

Bot hanya meminta satu informasi yang belum tersedia dalam satu giliran. Informasi yang sudah valid tidak diminta ulang selama flow yang sama masih aktif.

### BR-AI-371 — Koreksi Terakhir Menggantikan Input Lama

Jika customer secara eksplisit mengoreksi input sebelum jawaban final diberikan, nilai terbaru menggantikan nilai sebelumnya. Bot mengonfirmasi nilai yang dipakai jika perubahan dapat memengaruhi hasil.

### BR-AI-372 — Outlet dari Konteks Percakapan

Capability menggunakan outlet yang terhubung dengan percakapan. Jika customer menyebut outlet lain, bot harus memastikan outlet tersebut dapat dilayani sebelum mengganti konteks.

### BR-AI-373 — Referensi Sensitif Tidak Boleh Ditebak

Bot tidak boleh melengkapi, menebak, atau menampilkan alternatif ID transaksi dan nomor ticket. Hasil hanya diberikan untuk referensi lengkap yang dikirim customer dalam sesi aktif.

### BR-AI-374 — Hasil Ambigu Memerlukan Pilihan

Jika nama promo, membership, atau layanan cocok dengan lebih dari satu data, bot menampilkan pilihan ringkas dan meminta customer memilih. Bot tidak menentukan hasil berdasarkan asumsi.

### BR-AI-375 — Data Lintas Outlet Tidak Digabung

Satu jawaban capability hanya menggunakan satu outlet. Data dari outlet berbeda tidak digabung kecuali user story secara eksplisit mendefinisikan perbandingan; perbandingan outlet tidak termasuk MVP.

## 4. Dependencies

- Konteks outlet pada percakapan Omnichannel.
- FEAT-AI-001 untuk klasifikasi intent, perpindahan flow, dan handoff.
- FEAT-AI-005 sampai FEAT-AI-011 untuk aturan capability.

## 5. User Story List

| US ID | Judul US | Goal | Status |
|---|---|---|---|
| US-AI-072 | Mengumpulkan dan Memvalidasi Input | Bot meminta satu slot, memvalidasi, dan mempertahankan input yang sudah benar | Draft |
| US-AI-073 | Mengoreksi Input Sebelum Hasil | Customer dapat mengganti input tanpa mengulang flow dari awal | Draft |
| US-AI-074 | Mengelola Perubahan Outlet | Bot memastikan seluruh jawaban memakai outlet yang jelas dan konsisten | Draft |
| US-AI-075 | Melindungi Referensi Transaksi dan Ticket | Bot tidak menebak atau membocorkan referensi sensitif | Draft |
| US-AI-076 | Memilih Hasil yang Ambigu | Customer memilih ketika nama cocok dengan lebih dari satu data | Draft |

## 6. Notes for Developer

- Feature ini tidak menambah capability customer-facing baru.
- Business rules di feature ini berlaku lintas-capability dan tidak perlu disalin ke setiap feature.
