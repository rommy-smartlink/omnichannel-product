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

- Bot AI omnichannel belum tersedia — semua percakapan customer langsung ditangani agen manusia.
- Owner tidak memiliki kendali untuk mengaktifkan/menonaktifkan bot karena fitur bot belum ada.
- Tidak ada mekanisme emergency stop — tidak ada bot yang perlu dihentikan.
- Jam operasional layanan outlet belum terintegrasi dengan sistem percakapan.

### Problems

- PROB-AI-001: Owner tidak bisa menyesuaikan capability bot per outlet.
- PROB-AI-002: Owner tidak bisa emergency stop bot untuk seluruh outlet.

### Expected Condition

- Owner bisa mengaktifkan/menonaktifkan bot untuk seluruh outlet melalui Master Switch WABA.
- Owner bisa mengaktifkan/menonaktifkan bot per outlet melalui Outlet Switch.
- Owner bisa mengaktifkan/menonaktifkan masing-masing intent per outlet.
- Jam operasional default 24 jam untuk semua outlet saat fitur rilis, dapat disesuaikan dari menu outlet.
- Owner bisa melihat status capability semua outlet dalam satu halaman.
- Konfigurasi per outlet tetap tersimpan saat Master Switch atau Outlet Switch dimatikan.
- `talk_to_agent` selalu tersedia sebagai jalur eskalasi ke manusia.

### Proposed Solution

Control Panel Bot — dashboard bagi owner untuk mengontrol bot pada tiga lapisan switch:

1. **Master Switch WABA**: ON/OFF bot untuk seluruh outlet dalam satu WABA.
2. **Outlet Switch**: ON/OFF bot per outlet.
3. **Konfigurasi Intent per Outlet**: toggle ON/OFF untuk 6 intent configurable per outlet.
4. **Informasi Jam Operasional**: menampilkan info "Jam operasional buka 24 jam" dengan tombol "Atur Jam Operasional" yang mengarahkan ke menu outlet.

## 2. Global Scope

### In Scope

- Master Switch ON/OFF per WABA, dengan modal konfirmasi sebelum perubahan.
- Outlet Switch ON/OFF per outlet, dengan modal konfirmasi sebelum perubahan.
- Toggle intent ON/OFF per outlet untuk 6 intent configurable.
- `talk_to_agent` selalu ON dan tidak dapat dimatikan.
- Informasi Jam Operasional: wording "Jam operasional buka 24 jam" dengan tombol "Atur Jam Operasional" yang navigasi ke menu outlet.
- Daftar outlet omnichannel dengan pencarian dan pengurutan.
- Modal edit per outlet dengan toggle intent, simpan/batal.
- Konfigurasi per outlet tetap tersimpan saat Master Switch OFF maupun Outlet Switch OFF.
- Konfigurasi per outlet tetap tersimpan saat intent dimatikan.
- Akses terbatas untuk owner.
- Override priority 3-gate: Master Switch > Outlet Switch > Intent Switch.

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

## 3. Epic List

| Epic ID | Nama Epic | Tujuan Singkat | Status |
|---|---|---|---|
| EPIC-AI-002 | Bot Control Panel | Menyediakan kontrol granular bot untuk owner | Draft |

## 4. Feature List

| Feature ID | Nama Feature | Purpose | Status |
|---|---|---|---|
| FEAT-AI-002 | Master Switch WABA | Owner dapat menghentikan atau menjalankan bot pada seluruh outlet dalam satu WABA (gate 1) | Draft |
| FEAT-AI-005 | Outlet Switch | Owner dapat menghentikan atau menjalankan bot per outlet (gate 2) | Draft |
| FEAT-AI-003 | Konfigurasi Intent per Outlet | Owner dapat mengaktifkan atau menonaktifkan 6 intent configurable per outlet (gate 3) | Draft |
| FEAT-AI-004 | Informasi Jam Operasional | Menampilkan info jam operasional 24 jam default dengan navigasi ke menu outlet untuk penyesuaian | Draft |

## 5. References

| Type | URL / Reference | Description |
|---|---|---|
| GWT | `aibot/gwt/P8-control-panel/gwt.md` | Skenario GWT Control Panel existing |
| PRD v2 | `aibot/prd/P8-control-panel/prd-v2.md` | PRD versi sebelumnya |
| Intents | `aibot/docs/intents.md` | Daftar intent MVP |
| Bot Behavior | `aibot/docs/bot-behavior.md` | Aturan perilaku bot |
| Conversation Flow | `aibot/docs/conversation-flow.md` | Arsitektur alur percakapan |
| Figma | TBD | Board desain Control Panel |
