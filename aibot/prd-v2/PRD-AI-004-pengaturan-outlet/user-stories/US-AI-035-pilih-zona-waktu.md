---
id: US-AI-035
prd: PRD-AI-004
epic: EPIC-AI-004
feature: FEAT-AI-012
title: Memilih Zona Waktu Outlet
status: Draft
priority: High
uix_status: 0%
created_at: 2026-07-20
updated_at: 2026-07-20
---

# US-AI-035 - Memilih Zona Waktu Outlet

## 1. Parent Reference

- PRD: PRD-AI-004 - Pengaturan Outlet
- Epic: EPIC-AI-004 - Pengaturan Outlet
- Feature: FEAT-AI-012 - Pengaturan Jam Operasional Outlet

## 2. User Story

Sebagai owner,  
Saya ingin memilih zona waktu outlet melalui dropdown berbasis wilayah,  
Agar bot dapat menghitung "sekarang", "hari ini", dan "besok" berdasarkan waktu lokal outlet.

## 3. Goal

Owner memilih zona waktu outlet dari dropdown yang menampilkan nama zona, wilayah, dan UTC offset. Zona waktu menjadi prasyarat sebelum jadwal dapat dinyatakan valid.

## 4. Acceptance Criteria

- Dropdown zona waktu ditampilkan dengan format: `[nama zona] — [wilayah] ([UTC offset])`.
- Pilihan untuk Indonesia: WIB — Asia/Jakarta (UTC+07:00), WITA — Asia/Makassar (UTC+08:00), WIT — Asia/Jayapura (UTC+09:00).
- Dropdown dapat dicari dengan kata kunci: "Jakarta", "WIB", "UTC+7", atau "Indonesia".
- Tidak menyediakan input bebas seperti "GMT +7".
- Zona waktu wajib dipilih sebelum jadwal dapat disimpan sebagai konfigurasi valid.
- Sistem dapat menyarankan zona waktu berdasarkan alamat outlet (opsional, nice-to-have).
- Jika zona waktu diubah saat intent ON, tampilkan konfirmasi (BR-AI-212).

## 5. Form Rules

| Label | Field Key | Type | Required | Unique | Max Length | Description |
|---|---|---|---:|---:|---:|---|
| Zona waktu | timezone | select | yes | no | — | Dropdown wilayah dengan UTC offset |

## 6. Form Notes

- Default: WIB — Asia/Jakarta (UTC+07:00).
- Pencarian mendukung substring match.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-035-01 | Outlet baru, zona waktu default WIB | Owner membuka halaman Jam Operasional | Zona waktu sudah terisi WIB. Owner dapat mengubah jika perlu. |
| SC-035-02 | Intent ON, zona waktu WIB | Owner mengubah ke WITA | Konfirmasi ditampilkan. Jika dikonfirmasi, zona waktu berubah. Jam tidak bergeser otomatis. |
| SC-035-03 | Owner mengetik "Jakarta" di dropdown | Search term "Jakarta" | Dropdown memfilter dan hanya menampilkan WIB — Asia/Jakarta. |

### Detail SC-035-02 - Ubah Zona Waktu Saat Intent Aktif

#### Given

Outlet A: zona waktu WIB, jadwal valid, intent `get_operating_hours` ON.

#### When

Owner mengubah zona waktu dari WIB ke WITA.

#### Then

- Sistem menampilkan konfirmasi: zona waktu sebelumnya (WIB), zona waktu baru (WITA), penjelasan bahwa jam buka/tutup tidak berubah.
- Owner memilih "Ubah Zona Waktu" → zona waktu berubah, intent tetap ON.
- Owner memilih "Batal" → zona waktu kembali ke WIB.

#### Related Business Rules

- BR-AI-200: Zona Waktu Wajib
- BR-AI-212: Konfirmasi Ubah Zona Waktu Saat Intent Aktif

#### UI Behavior

- Konfirmasi menampilkan kedua zona waktu (sebelum dan baru).
- Tidak ada konversi otomatis jam.

#### Notes

Jam buka/tutup tidak bergeser otomatis karena owner mungkin sengaja mengatur jadwal yang sama untuk waktu lokal baru.
