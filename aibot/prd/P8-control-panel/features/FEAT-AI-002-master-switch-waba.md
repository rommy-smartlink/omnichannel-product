---
id: FEAT-AI-002
prd: PRD-AI-002
epic: EPIC-AI-002
title: Master Switch WABA
status: Draft
owner: Product Owner
created_at: 2026-07-16
updated_at: 2026-07-16
---

# FEAT-AI-002 - Master Switch WABA

## 1. Purpose

Owner dapat mengaktifkan atau menonaktifkan bot untuk seluruh outlet dalam satu WABA melalui satu toggle. Saat OFF, bot berhenti merespons customer di semua outlet — tidak mengirim greeting, tidak melakukan klasifikasi intent, dan langsung mengarahkan percakapan ke agent (silent handoff). Konfigurasi intent per outlet tetap tersimpan utuh dan berlaku kembali saat master switch diaktifkan.

## 2. Scope

### In Scope

- Toggle Master Switch ON/OFF di header Control Panel, scope per WABA.
- Modal konfirmasi sebelum state berubah dari ON ke OFF.
- Modal konfirmasi menjelaskan dampak: percakapan bot yang sedang berlangsung akan dihentikan dan customer dialihkan ke agent.
- Saat master OFF: bot skip greeting, skip intent classification, silent handoff ke agent.
- Konfigurasi per outlet tetap tersimpan saat master OFF, tidak di-reset atau dimodifikasi.
- Saat master diaktifkan kembali ke ON, konfigurasi per outlet berlaku normal.
- Default state WABA baru: master switch ON.
- Owner tetap bisa membuka dan mengubah konfigurasi outlet saat master OFF.
- Percakapan bot yang sedang berlangsung dihentikan saat master diubah ke OFF; customer diarahkan ke agent pada respons berikutnya.
- Aturan penghentian chat tidak berlaku jika customer sudah berada di sesi agent.
- Timestamp dan identitas yang terakhir mengubah ditampilkan di sebelah switch.

### Out of Scope

- Schedule otomatis untuk master switch.
- Custom message saat master OFF (langsung silent handoff).
- Konfirmasi untuk mengubah dari OFF ke ON.
- Bulk action untuk banyak WABA.

## 3. Business Rules

### BR-AI-001 - Master Switch Off Priority

Master switch OFF memiliki prioritas tertinggi. Saat OFF, bot tidak mengirim greeting, tidak melakukan klasifikasi intent, dan langsung mengarahkan percakapan ke agent melalui silent handoff untuk semua outlet dalam WABA tersebut.

Override priority:

```text
Master switch OFF
>
Intent outlet OFF / belum siap
>
Intent outlet ON dan siap
```

### BR-AI-002 - Konfigurasi Tetap Tersimpan Saat Master OFF

Konfigurasi intent per outlet tidak dihapus, di-reset, atau dimodifikasi saat master switch dimatikan. Saat master switch diaktifkan kembali ke ON, seluruh konfigurasi per outlet berlaku normal tanpa perlu setup ulang.

### BR-AI-003 - Default Master Switch ON

Saat WABA baru dibuat, master switch otomatis dalam keadaan ON.

### BR-AI-004 - Modal Konfirmasi Master Switch OFF

Saat owner mengubah master switch dari ON ke OFF, sistem menampilkan modal konfirmasi yang menjelaskan bahwa mematikan bot akan menghentikan percakapan bot yang sedang berlangsung dan mengalihkan customer ke agent. Owner memilih "Konfirmasi" untuk menyimpan atau "Batal" untuk membatalkan.

### BR-AI-005 - Penghentian Chat Saat Master OFF

Saat master switch diubah menjadi OFF, percakapan bot yang sedang berlangsung langsung dihentikan dan customer diarahkan ke agent pada respons berikutnya. Aturan ini tidak berlaku jika customer sudah berada di sesi chat dengan agent (handoff sudah terjadi).

### BR-AI-006 - Edit Konfigurasi Tetap Diizinkan Saat Master OFF

Owner tetap dapat membuka modal edit outlet dan mengubah konfigurasi intent saat master switch OFF. Perubahan disimpan ke database dan berlaku ketika master switch kembali ON. Owner juga tetap dapat menyiapkan dan mengaktifkan Jam Operasional, tetapi bot baru berjalan ketika master switch kembali ON.

## 4. Dependencies

- WABA Selector (komponen existing untuk memilih WABA).
- Data outlet omnichannel.
- Data konfigurasi intent per outlet.
- Sistem handoff ke agent.

## 5. User Story List

| US ID | Judul US | Goal | Status |
|---|---|---|---|
| US-AI-004 | Melihat Status Master Switch | Owner dapat melihat status master switch ON/OFF di header Control Panel saat pertama membuka halaman | Draft |
| US-AI-005 | Mematikan Bot WABA | Owner dapat mengubah master switch dari ON ke OFF untuk menghentikan bot di seluruh outlet, dengan modal konfirmasi | Draft |
| US-AI-006 | Mengaktifkan Kembali Bot WABA | Owner dapat mengubah master switch dari OFF ke ON untuk menjalankan kembali bot di seluruh outlet | Draft |
| US-AI-007 | Customer Terdampak Master OFF | Customer mendapat silent handoff saat master switch OFF | Draft |
| US-AI-008 | Konfigurasi Outlet Tetap Saat Master OFF | Konfigurasi per outlet tetap tersimpan dan berlaku saat master kembali ON | Draft |
| US-AI-009 | Edit Konfigurasi Saat Master OFF | Owner tetap dapat mengubah konfigurasi outlet saat master switch OFF | Draft |

## 6. Notes for Developer

- Komponen Master Switch berada di header atas Control Panel, sebelum search/sort.
- Scope master switch adalah WABA yang sedang dipilih melalui WABA selector.
- Toggle dari OFF ke ON tidak memerlukan modal konfirmasi, langsung berlaku.
- Saat master switch OFF, percakapan bot customer berhenti pada respons berikutnya, bukan secara instan (untuk menghindari kehilangan pesan yang sedang diproses).
- Tidak ada perubahan pada database konfigurasi outlet saat master switch di-toggle.
