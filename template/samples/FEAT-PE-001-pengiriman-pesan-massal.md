---
id: FEAT-PE-001
prd: PRD-OC-001
epic: EPIC-PE-001
title: Pengiriman Pesan Massal
status: Draft
owner: Product Owner
created_at: 2026-07-13
updated_at: 2026-07-13
---

# FEAT-PE-001 - Pengiriman Pesan Massal

## 1. Purpose

Memungkinkan user membuat kampanye broadcast dan mengirim pesan massal ke banyak kontak menggunakan template pesan yang sudah disetujui Meta.

## 2. Scope

### In Scope

- Buat pesan broadcast.
- Pilih target penerima lebih dari satu pelanggan.
- Konfirmasi sebelum kirim.
- Status hasil pengiriman ringkas.
- Daftar nomor hasil import CSV.

### Out of Scope

- Pembuatan template pesan.
- Approval template oleh Meta.
- Pengaturan channel WhatsApp Official.
- Channel selain WhatsApp.

## 3. Business Rules

### BR-PE-001 - Template Approved

User hanya dapat memilih template broadcast dengan status `approved` dari Meta.

### BR-PE-002 - Daily Broadcast Limit

Jumlah penerima broadcast tidak boleh melebihi daily broadcast limit yang dimiliki user.

### BR-PE-003 - Broadcast Cost

Biaya kampanye per pesan adalah 700 koin.

### BR-PE-004 - Insufficient Coin Balance

Jika saldo koin lebih kecil dari estimasi biaya pengiriman pesan massal, CTA **Kirim Pesan** disabled dan sistem menampilkan CTA **Top Up Saldo Koin**.

### BR-PE-005 - Draft When Top Up

Jika user klik CTA **Top Up Saldo Koin** dari flow broadcast, kampanye yang sedang dibuat otomatis tersimpan sebagai draft.

## 4. Dependencies

- Template Pesan.
- Kontak Pelanggan.
- Saldo Koin.
- WhatsApp Official / Meta.
- Maxchat.

## 5. User Story List

| US ID | Judul US | Goal | Status |
|---|---|---|---|
| US-Pe-083 | Akses menu Broadcast | User dapat akses menu Broadcast / Pengiriman Pesan Massal | Done |
| US-Pe-086 | Membuat Kampanye Broadcast | User dapat membuat kampanye massal dengan memilih template approved | Draft |
| US-Pe-107 | Menghapus Broadcast | User dapat menghapus broadcast yang masih memenuhi ketentuan hapus | Draft |

## 6. Notes for Developer

- Jangan menambahkan flow pembuatan template di feature ini.
- Jangan menambahkan channel selain WhatsApp pada fase awal.
- Flow Top Up Saldo Koin mengikuti existing flow Smartlink.
