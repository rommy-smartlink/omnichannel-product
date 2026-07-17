---
id: PRD-AI-002
title: Bot Control Panel
product: Smartlink
module: AI Bot Omnichannel
version: 3.0.0
status: Draft
owner: Product Owner
reviewers:
  - UIX
  - Engineering
  - QA
created_at: 2026-07-16
updated_at: 2026-07-16
---

# PRD-AI-002 - Bot Control Panel

## 1. Executive Summary

### Background

Owner laundry yang menggunakan bot AI omnichannel membutuhkan kontrol granular atas perilaku bot. Setiap outlet memiliki kesiapan operasional yang berbeda — ada outlet yang siap melayani semua capability bot, ada yang hanya ingin menggunakan sebagian fitur, dan ada yang ingin menghentikan bot sepenuhnya. Tanpa control panel, owner tidak bisa menyesuaikan perilaku bot sesuai kondisi aktual masing-masing outlet.

Selain itu, owner membutuhkan emergency switch untuk menghentikan bot di seluruh outlet dengan cepat saat maintenance, insiden, atau situasi darurat.

### Current Condition

- Seluruh intent bot aktif untuk semua outlet, tanpa kemampuan menonaktifkan per outlet.
- Owner tidak bisa menghentikan bot dengan cepat saat maintenance atau emergency.
- Owner tidak bisa membatasi capability per outlet — misalnya menonaktifkan cek status laundry untuk outlet yang belum siap.
- Intent `get_operating_hours` belum memiliki validasi prasyarat data (zona waktu dan jadwal operasional).
- Tidak ada visibilitas status kesiapan bot per outlet.

### Problems

- PROB-AI-001: Owner tidak bisa menyesuaikan capability bot per outlet.
- PROB-AI-002: Owner tidak bisa emergency stop bot untuk seluruh outlet.
- PROB-AI-003: ~~Bot bisa memberikan informasi jam operasional tanpa data valid.~~ (Resolved: default 24/7 dianggap valid — lihat §1 Proposed Solution)
- PROB-AI-004: Owner tidak memiliki visibilitas status kesiapan capability per outlet.

### Expected Condition

- Owner bisa mengaktifkan/menonaktifkan bot untuk seluruh outlet melalui Master Switch WABA.
- Owner bisa mengaktifkan/menonaktifkan masing-masing intent per outlet.
- Intent `get_operating_hours` bisa langsung ON. Jika konfigurasi jam operasional belum diatur (karena fitur baru), sistem mengasumsikan outlet beroperasi 24 jam.
- Owner bisa melihat status capability semua outlet dalam satu halaman.
- Konfigurasi per outlet tetap tersimpan saat Master Switch dimatikan.
- `talk_to_agent` selalu tersedia sebagai jalur eskalasi ke manusia.

### Proposed Solution

Control Panel Bot — dashboard bagi owner untuk mengontrol bot pada tiga lapisan:

1. **Master Switch WABA**: ON/OFF bot untuk seluruh outlet dalam satu WABA.
2. **Konfigurasi Intent per Outlet**: toggle ON/OFF untuk 6 intent configurable per outlet.
3. **Jam Operasional**: intent `get_operating_hours` bisa langsung ON. Jika jadwal belum dikonfigurasi, default 24 jam berlaku. Owner dapat mengatur jadwal kapan pun, tetapi tidak menjadi prasyarat aktivasi.

## 2. Global Scope

### In Scope

- Master Switch ON/OFF per WABA, dengan modal konfirmasi sebelum perubahan.
- Toggle intent ON/OFF per outlet untuk 6 intent configurable.
- `talk_to_agent` selalu ON dan tidak dapat dimatikan.
- Intent `get_operating_hours` bisa langsung ON tanpa prasyarat. Jika konfigurasi jam operasional belum diatur, default 24 jam berlaku.
- Card outlet menampilkan **24 Jam** hanya untuk skema default. Outlet dengan jadwal kustom tidak menampilkan info jam operasional di card — detail tampil di modal capability.
- Navigasi dari Control Panel ke halaman Pengaturan Jam Operasional untuk mengatur jadwal spesifik.
- Jadwal operasional tetap tersimpan saat intent Jam Operasional dimatikan.
- Jadwal khusus Tutup tidak menonaktifkan capability Jam Operasional.
- Daftar outlet omnichannel dengan pencarian dan pengurutan.
- Modal edit per outlet dengan toggle intent, simpan/batal.
- Konfigurasi per outlet tetap tersimpan saat Master Switch OFF.
- Konfigurasi per outlet tetap tersimpan saat intent dimatikan.
- Akses terbatas untuk owner.

### Out of Scope

- Bulk action untuk banyak outlet.
- Schedule otomatis Master Switch atau intent.
- Maintenance mode dengan custom message.
- Role outlet manager atau kasir.
- Analytics conversation di dalam Control Panel.
- Notifikasi perubahan setting kepada owner lain.
- Approval sebelum setting berlaku.
- Isu penimpaan data antar device yang melakukan edit bersamaan.
- Setup jam operasional langsung di dalam modal Control Panel.
- Menjadikan seluruh intent sebagai prerequisite-aware pada MVP; tidak ada validasi prasyarat untuk intent manapun.

## 3. Epic List

| Epic ID | Nama Epic | Tujuan Singkat | Status |
|---|---|---|---|
| EPIC-AI-002 | Bot Control Panel | Menyediakan kontrol granular bot untuk owner | Draft |

## 4. Feature List

| Feature ID | Nama Feature | Purpose | Status |
|---|---|---|---|
| FEAT-AI-002 | Master Switch WABA | Owner dapat menghentikan atau menjalankan bot pada seluruh outlet dalam satu WABA | Draft |
| FEAT-AI-003 | Konfigurasi Intent per Outlet | Owner dapat mengaktifkan atau menonaktifkan 6 intent configurable per outlet | Draft |
| FEAT-AI-004 | Jam Operasional — Default 24 Jam | Intent Jam Operasional bisa ON langsung; default 24 jam jika jadwal belum dikonfigurasi | Draft |

## 5. References

| Type | URL / Reference | Description |
|---|---|---|
| GWT | `aibot/gwt/P8-control-panel/gwt.md` | Skenario GWT Control Panel existing |
| PRD v2 | `aibot/prd/P8-control-panel/prd-v2.md` | PRD versi sebelumnya |
| Intents | `aibot/docs/intents.md` | Daftar intent MVP |
| Bot Behavior | `aibot/docs/bot-behavior.md` | Aturan perilaku bot |
| Conversation Flow | `aibot/docs/conversation-flow.md` | Arsitektur alur percakapan |
| Figma | TBD | Board desain Control Panel |
