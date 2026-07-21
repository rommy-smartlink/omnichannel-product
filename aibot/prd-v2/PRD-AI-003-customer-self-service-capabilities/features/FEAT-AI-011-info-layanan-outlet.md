---
id: FEAT-AI-011
prd: PRD-AI-003
epic: EPIC-AI-003
title: Info Layanan Outlet
status: Draft
owner: Product Owner
created_at: 2026-07-21
updated_at: 2026-07-21
---

# FEAT-AI-011 - Info Layanan Outlet

## 1. Purpose

Customer menanyakan layanan yang tersedia di outlet melalui percakapan natural WhatsApp. Bot merespons secara adaptif: layanan spesifik → ya/tidak, pertanyaan umum ≤20 layanan → tampilkan semua, pertanyaan umum ≥21 layanan → minta customer menyebut layanan yang dicari. Read-only: bot tidak membuat atau mengubah data layanan.

## 2. Scope

### In Scope

- Intent `get_outlet_services` aktif per outlet (toggleable via Control Panel, default ON).
- Respons adaptif berdasarkan jumlah layanan outlet:
  - **Spesifik**: customer sebut layanan → ya/tidak + konteks satuan.
  - **Umum, ≤20 layanan**: tampilkan semua layanan.
  - **Umum, ≥21 layanan**: minta customer menyebut layanan/kebutuhan + contoh keyword (maks 5).
- Konteks satuan dari `layanan.satuan.nama` (pcs, kg, dll).
- Read-only: hanya membaca data layanan outlet.
- Layanan tidak tersedia → "Maaf Kak, [layanan] belum tersedia." + tawarkan agent.
- Customer tanya harga → arahkan ke agent untuk detail harga.
- Bahasa Indonesia, tone "Kak", format list WhatsApp-friendly.
- Command "menu"/"batal"/"hubungi agen" selalu override.

### Out of Scope

- Browse/katalog lengkap semua layanan dalam satu respons.
- Order atau booking layanan.
- Harga per layanan (hanya menyebut tersedia/tidak, tanpa nominal).
- Rekomendasi layanan berdasarkan riwayat transaksi.
- Multi-language.

## 3. Business Rules

### BR-AI-360 — Respons Adaptif Berdasarkan Jumlah Layanan

- Customer menyebut layanan spesifik: cek ketersediaan, jawab ya/tidak.
- Customer tanya umum ("ada layanan apa?") + total ≤20: tampilkan semua.
- Customer tanya umum + total ≥21: minta customer menyebut layanan yang dicari + beri maks 5 contoh keyword.

### BR-AI-361 — Keyword Tidak Match

Jika customer menyebut layanan dan tidak ada yang match → "Maaf Kak, layanan yang Kakak maksud belum tersedia di outlet ini. Saya bisa hubungkan ke agent jika perlu."

### BR-AI-362 — Harga Tidak Ditampilkan

Bot tidak menampilkan `harga` dari API. Jika customer tanya harga → arahkan ke agent: "Layanan tersebut tersedia di outlet ini, Kak. Untuk harga detail, saya hubungkan Kakak ke agent outlet ya."

### BR-AI-363 — Contoh Keyword Bukan Daftar Lengkap

Contoh keyword yang diberikan bot (maks 5 item) bukan daftar lengkap layanan dan bukan klaim semua contoh pasti tersedia. Tujuan: membantu customer memperjelas pencarian.

### BR-AI-364 — Paginasi API

Jika response API terpaginated, bot harus membaca semua halaman sebelum menyimpulkan layanan tidak tersedia.

### BR-AI-365 — Layanan Tanpa Nama

Jika `layanan.nama_layanan` kosong → item diabaikan. Jika `layanan.satuan.nama` kosong → tidak menyebut satuan.

## 4. Dependencies

- Bot Runtime Behavior (PRD-AI-001): effective state 3-gate, fallback.
- Bot Control Panel (PRD-AI-002): toggle `get_outlet_services` per outlet.
- API: Daftar layanan outlet (by outlet ID).
- Conversation context: outlet ID, nama outlet.

## 5. User Story List

| US ID | Judul US | Goal | Status |
|---|---|---|---|
| US-AI-063 | Info Layanan — Layanan Spesifik | Customer tanya layanan spesifik dan bot menjawab tersedia/tidak dengan konteks satuan | Draft |
| US-AI-064 | Info Layanan — Umum ≤20 Layanan | Customer tanya umum di outlet kecil dan bot menampilkan semua layanan | Draft |
| US-AI-065 | Info Layanan — Umum ≥21 Layanan | Customer tanya umum di outlet besar dan bot meminta customer memperjelas | Draft |
| US-AI-066 | Info Layanan — Tanya Harga | Customer tanya harga layanan dan bot mengarahkan ke agent tanpa menyebut nominal | Draft |

## 6. Notes for Developer

- Threshold 20 layanan: ≤20 tampilkan semua, ≥21 minta spesifik.
- Keyword matching layanan: case-insensitive, partial match yang reasonable.
- Outlet tanpa layanan: pesan khusus "belum memiliki layanan terdaftar" + tawarkan agent.
- Customer menyebut layanan outlet lain → tetap gunakan outlet dari conversation context.
