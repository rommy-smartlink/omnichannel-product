---
id: FEAT-AI-005
prd: PRD-AI-002
epic: EPIC-AI-002
title: Outlet Switch
status: Draft
owner: Product Owner
created_at: 2026-07-16
updated_at: 2026-07-16
---

# FEAT-AI-005 - Outlet Switch

## 1. Purpose

Owner dapat mengaktifkan atau menonaktifkan bot untuk satu outlet tertentu melalui toggle Outlet Switch di card outlet Control Panel. Saat OFF, bot berhenti merespons customer di outlet tersebut — tidak mengirim greeting, tidak melakukan klasifikasi intent, dan langsung mengarahkan percakapan ke agent (silent handoff). Konfigurasi intent outlet tetap tersimpan utuh dan berlaku kembali saat outlet switch diaktifkan.

Outlet Switch adalah gate level 2 dari 3-gate override priority. Untuk relasi antar-switch, lihat [Override Priority di EPIC-AI-002](../EPIC-AI-002-bot-control-panel.md#6-override-priority).

## 2. Scope

### In Scope

- Toggle Outlet Switch ON/OFF di card outlet Control Panel, scope per outlet.
- Modal konfirmasi sebelum state berubah dari ON ke OFF.
- Modal konfirmasi menjelaskan dampak: percakapan bot yang sedang berlangsung di outlet tersebut akan dihentikan dan customer dialihkan ke agent.
- Konfigurasi intent outlet tetap tersimpan saat outlet switch OFF, tidak di-reset atau dimodifikasi.
- Saat outlet switch diaktifkan kembali ke ON, konfigurasi intent outlet berlaku normal.
- Default state outlet baru: Outlet Switch ON.
- Owner tetap bisa membuka dan mengubah konfigurasi intent outlet saat outlet switch OFF.
- Timestamp dan identitas yang terakhir mengubah ditampilkan di sebelah switch.

> **Catatan**: Dampak outlet OFF ke customer (silent handoff, in-flight interruption) adalah ranah bot engine. Lihat BR-AI-101 & BR-AI-102 di [FEAT-AI-001 (Runtime Behavior)](../../PRD-AI-001-bot-runtime-behavior/features/FEAT-AI-001-greeting-classification-handoff.md) dan user story terkait (US-AI-024).

### Out of Scope

- Schedule otomatis untuk outlet switch.
- Custom message saat outlet OFF (langsung silent handoff).
- Konfirmasi untuk mengubah dari OFF ke ON.
- Bulk action untuk banyak outlet.
- Master Switch (lihat FEAT-AI-002) dan Intent Switch (lihat FEAT-AI-003).

## 3. Business Rules

### BR-AI-017 — Outlet Switch Off Priority

Outlet switch OFF memblokir bot untuk outlet tersebut terlepas dari state Intent Switch. Saat outlet switch OFF, bot tidak mengirim greeting, tidak melakukan klasifikasi intent, dan langsung mengarahkan percakapan ke agent melalui silent handoff. Outlet switch kalah prioritas dari Master Switch. Lihat [Override Priority di EPIC-AI-002](../EPIC-AI-002-bot-control-panel.md#6-override-priority).

### BR-AI-018 — Konfigurasi Tetap Tersimpan Saat Outlet OFF

Konfigurasi intent outlet tidak dihapus, di-reset, atau dimodifikasi saat outlet switch dimatikan. Saat outlet switch diaktifkan kembali ke ON, seluruh konfigurasi intent outlet berlaku normal tanpa perlu setup ulang.

### BR-AI-019 — Default Outlet Switch ON

Saat outlet baru terdaftar omnichannel, outlet switch otomatis dalam keadaan ON.

### BR-AI-020 — Modal Konfirmasi Outlet Switch OFF

Saat owner mengubah outlet switch dari ON ke OFF, sistem menampilkan modal konfirmasi yang menjelaskan bahwa mematikan bot akan menghentikan percakapan bot yang sedang berlangsung di outlet tersebut dan mengalihkan customer ke agent. Owner memilih "Konfirmasi" untuk menyimpan atau "Batal" untuk membatalkan. Perubahan dari OFF ke ON tidak memerlukan modal konfirmasi.

### BR-AI-021 — Penghentian Chat Saat Outlet OFF

Saat outlet switch diubah menjadi OFF, percakapan bot yang sedang berlangsung di outlet tersebut dihentikan dan customer diarahkan ke agent pada respons berikutnya. Aturan ini tidak berlaku jika customer sudah berada di sesi chat dengan agent (handoff sudah terjadi).

### BR-AI-022 — Edit Konfigurasi Tetap Diizinkan Saat Outlet OFF

Owner tetap dapat membuka modal edit outlet dan mengubah konfigurasi intent saat outlet switch OFF. Perubahan disimpan ke database dan berlaku ketika outlet switch kembali ON. Owner juga tetap dapat menyiapkan dan mengaktifkan Jam Operasional, tetapi bot baru berjalan ketika outlet switch kembali ON.

## 4. Dependencies

- Data outlet omnichannel.
- Data konfigurasi intent per outlet.
- Sistem handoff ke agent.

## 5. User Story List

| US ID | Judul US | Goal | Status |
|---|---|---|---|
| US-AI-021 | Melihat Status Outlet Switch | Owner dapat melihat status outlet switch ON/OFF di setiap card outlet Control Panel | Draft |
| US-AI-022 | Mematikan Bot Per Outlet | Owner dapat mengubah outlet switch dari ON ke OFF untuk menghentikan bot di satu outlet, dengan modal konfirmasi | Draft |
| US-AI-023 | Mengaktifkan Kembali Bot Per Outlet | Owner dapat mengubah outlet switch dari OFF ke ON untuk menjalankan kembali bot di satu outlet | Draft |
| US-AI-025 | Konfigurasi Outlet Tetap Saat Outlet OFF | Konfigurasi intent outlet tetap tersimpan dan berlaku saat outlet switch kembali ON | Draft |
| US-AI-026 | Edit Konfigurasi Saat Outlet OFF | Owner tetap dapat mengubah konfigurasi intent outlet saat outlet switch OFF | Draft |

> **Catatan**: US-AI-024 (Customer Terdampak Outlet OFF) dipindahkan ke [PRD-AI-001 Runtime Behavior](../../PRD-AI-001-bot-runtime-behavior/features/FEAT-AI-001-greeting-classification-handoff.md) karena merupakan perilaku bot, bukan aksi admin di UI.

## 6. Notes for Developer

- Komponen Outlet Switch berada di dalam card outlet Control Panel.
- Scope Outlet Switch adalah outlet individu.
- Toggle dari OFF ke ON tidak memerlukan modal konfirmasi, langsung berlaku.
- Toggle dari ON ke OFF selalu memerlukan modal konfirmasi.
- Saat outlet switch OFF, percakapan bot customer di outlet tersebut berhenti pada respons berikutnya, bukan secara instan.
- Tidak ada perubahan pada database konfigurasi intent outlet saat outlet switch di-toggle.
- Override priority 3-gate dicek oleh bot engine. Lihat EPIC-AI-002 untuk urutan lengkap.
