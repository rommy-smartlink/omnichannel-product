---
id: FEAT-AI-010
prd: PRD-AI-003
epic: EPIC-AI-003
title: Info Member
status: Draft
owner: Product Owner
created_at: 2026-07-21
updated_at: 2026-07-21
---

# FEAT-AI-010 - Info Member

## 1. Purpose

Customer melihat paket membership yang tersedia di outlet melalui percakapan natural WhatsApp. Bot menampilkan daftar membership dalam format ringkas: nama, biaya daftar, masa aktif. Customer bisa meminta detail membership tertentu untuk informasi lebih lengkap.

## 2. Scope

### In Scope

- Intent `get_memberships` aktif per outlet (toggleable via Control Panel, default ON).
- Tanpa slot wajib — outlet dari context, bot langsung fetch.
- Daftar membership: maksimal 5 per respons. Lebih dari 5 → "...dan X membership lainnya."
- Setiap membership: nama + biaya daftar + masa aktif.
- Biaya daftar: "Gratis" jika 0 atau `register_fee_enable = false`, format "RpX" jika > 0.
- Masa aktif: generate label customer-friendly dari `period_type`/`active_period_type`.
- Detail membership on-demand: nama, status, biaya daftar, masa aktif, deskripsi, lokasi.
- Filter: hanya membership `active = true` dan relevan untuk outlet.
- Fallback: tidak ada membership → "Saat ini belum ada membership aktif."; API error → eskalasi.
- Bahasa Indonesia, tone "Kak", format nominal rupiah.
- Command "menu"/"batal"/"hubungi agen" selalu override.

### Out of Scope

- Registrasi membership langsung dari bot.
- Pembayaran biaya daftar dari bot.
- Cek status membership customer yang sudah terdaftar.
- Menampilkan daftar customer pengguna membership.
- Menampilkan benefit promo turunan dari membership.
- Verifikasi eligibility customer.
- Multi-language.

## 3. Business Rules

### BR-AI-350 — Filter Membership Aktif

Hanya membership dengan `active = true` dan relevan untuk outlet conversation context yang ditampilkan.

### BR-AI-351 — Maksimal 5 Membership

Maksimal 5 membership dalam satu respons daftar. Jika lebih, tampilkan 5 pertama lalu "...dan X membership lainnya."

### BR-AI-352 — Biaya Daftar Customer-Friendly

`register_fee = 0` atau `register_fee_enable = false` → "Gratis". `register_fee > 0` → format rupiah "RpX".

### BR-AI-353 — Masa Aktif Customer-Friendly

Label dihasilkan dari `period_type`: `unlimited` → "Tidak terbatas", `monthly` → "X bulan", `yearly` → "X tahun". Jika `active_period_day` > 0, tambahkan estimasi hari "(±X hari)".

### BR-AI-354 — Detail Membership On-Demand

Customer menyebut nama membership → cari dari daftar aktif. Tidak ditemukan → "Maaf Kak, membership dengan nama tersebut tidak ditemukan." Ditemukan → tampilkan detail: nama, status, biaya, masa aktif, deskripsi, lokasi.

### BR-AI-355 — Field Tidak Ditampilkan

Identifier internal, `created_by`, `created_at`, `updated_at`, `count_customer_usage`, `count_promo`, status internal — tidak muncul di respons customer.

### BR-AI-356 — Tidak Ada Data Fallback

Data kosong → "Saat ini belum ada membership aktif di [Nama Outlet]." API error → eskalasi.

## 4. Dependencies

- Bot Runtime Behavior (PRD-AI-001): effective state 3-gate, fallback.
- Bot Control Panel (PRD-AI-002): toggle `get_memberships` per outlet.
- API: Daftar membership (by outlet), Detail membership (by ID).
- Conversation context: outlet ID, nama outlet.

## 5. User Story List

| US ID | Judul US | Goal | Status |
|---|---|---|---|
| US-AI-060 | Info Member — Lihat Daftar Membership | Customer tanya membership dan mendapat daftar dengan nama, biaya, dan masa aktif | Draft |
| US-AI-061 | Info Member — Detail Membership Tertentu | Customer menyebut nama membership dan mendapat deskripsi, lokasi, dan informasi lengkap | Draft |
| US-AI-062 | Info Member — Tidak Ada / Error | Bot memberi fallback saat tidak ada membership aktif atau sistem error | Draft |

## 6. Notes for Developer

- `period_type = yearly`, `active_period_value = 12` → tampilkan "1 tahun", bukan "12 bulan".
- `period_type = yearly`, `active_period_value = 24` → "24 bulan" (bukan "2 tahun").
- Lokasi membership: `all` → "Berlaku di semua outlet", `only` → sebut outlet, `except` → "semua kecuali outlet tertentu".
- Skip field yang kosong/null — jangan render "null" atau "-".
