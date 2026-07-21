---
id: FEAT-AI-009
prd: PRD-AI-003
epic: EPIC-AI-003
title: Promo Aktif
status: Draft
owner: Product Owner
created_at: 2026-07-21
updated_at: 2026-07-21
---

# FEAT-AI-009 - Promo Aktif

## 1. Purpose

Customer melihat promo yang sedang berlangsung di outlet melalui percakapan natural WhatsApp. Bot menampilkan daftar promo dalam format ringkas: nama, benefit, periode. Customer bisa meminta detail promo tertentu untuk melihat syarat & ketentuan lengkap.

## 2. Scope

### In Scope

- Intent `get_active_promos` aktif per outlet (toggleable via Control Panel, default ON).
- Tanpa slot wajib — outlet dari context, bot langsung fetch.
- Daftar promo: maksimal 5 promo per respons. Lebih dari 5 → "...dan X promo lainnya."
- Setiap promo: nama + deskripsi benefit + periode berlaku (jika ada).
- Label "(Member)" jika promo khusus member.
- Deskripsi benefit digenerate dari data benefit (bukan data mentah).
- Detail promo on-demand: nama, periode, jam berlaku, benefit, syarat & ketentuan, lokasi, metode pembayaran, sisa kuota.
- Filter: hanya promo dengan periode masih berlangsung.
- Fallback: tidak ada promo → "Saat ini belum ada promo aktif."; API error → eskalasi.
- Bahasa Indonesia, tone "Kak", format tanggal "DD MMMM YYYY".
- Command "menu"/"batal"/"hubungi agen" selalu override.

### Out of Scope

- Detail promo per transaksi (promo yang sudah dipakai).
- Kode promo redeem / klaim dari bot.
- History promo yang sudah lewat.
- Filter/sort promo (misal "promo diskon tertinggi").
- Verifikasi syarat promo (misal "apakah saya memenuhi syarat?").
- Multi-language.

## 3. Business Rules

### BR-AI-340 — Filter Periode Aktif

Hanya promo yang periodenya masih berlangsung (tanggal sekarang dalam rentang berlaku) yang ditampilkan. Promo expired atau belum mulai tidak muncul.

### BR-AI-341 — Maksimal 5 Promo

Maksimal 5 promo dalam satu respons daftar. Jika lebih, tampilkan 5 pertama lalu "...dan X promo lainnya." (X = jumlah sisa).

### BR-AI-342 — Benefit Customer-Friendly

Deskripsi benefit digenerate, bukan data mentah. Diskon persen → "Diskon 20% untuk total belanja." Potongan nominal → "Potongan Rp20.000 untuk total belanja." Diskon member → "Diskon 5% khusus member." Hadiah → "Bonus hadiah gratis."

### BR-AI-343 — Detail Promo On-Demand

Customer menyebut nama promo spesifik → bot cari dari daftar promo aktif. Jika tidak ditemukan → "Maaf Kak, promo dengan nama tersebut tidak ditemukan." Jika ditemukan → tampilkan detail lengkap (nama, periode, jam, benefit, syarat, lokasi, metode bayar, kuota).

### BR-AI-344 — Field Tidak Ditampilkan di List

ID promo, kuota, status internal, lokasi mentah, terms mentah, nominal diskon mentah, minimal transaksi mentah — tidak muncul di list. Sebagian hanya muncul di detail.

### BR-AI-345 — Tidak Ada Data Fallback

Jika data promo kosong → "Saat ini belum ada promo aktif di [Nama Outlet]." Jika API error → eskalasi ke agen dengan pesan sistem gangguan.

## 4. Dependencies

- Bot Runtime Behavior (PRD-AI-001): effective state 3-gate, fallback.
- Bot Control Panel (PRD-AI-002): toggle `get_active_promos` per outlet.
- API: Daftar promo aktif (by outlet), Detail promo (by ID/nama).
- Conversation context: outlet ID, nama outlet.

## 5. User Story List

| US ID | Judul US | Goal | Status |
|---|---|---|---|
| US-AI-057 | Promo Aktif — Lihat Daftar Promo | Customer tanya promo dan mendapat daftar promo aktif dengan benefit customer-friendly | Draft |
| US-AI-058 | Promo Aktif — Detail Promo Tertentu | Customer menyebut nama promo dan mendapat syarat & ketentuan lengkap | Draft |
| US-AI-059 | Promo Aktif — Tidak Ada Promo / Error | Bot memberi fallback jelas saat tidak ada promo atau sistem error | Draft |

## 6. Notes for Developer

- Benefit description digenerate LLM dari data benefit — bukan string literal API.
- Label "(Member)" ditambahkan jika field member-specific true.
- Periode berlaku opsional — tampilkan hanya jika tersedia di data.
- Jika daftar promo belum di-fetch di sesi, fetch dulu sebelum mencari detail.
