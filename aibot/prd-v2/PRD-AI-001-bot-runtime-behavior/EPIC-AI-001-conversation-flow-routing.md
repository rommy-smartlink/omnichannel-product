---
id: EPIC-AI-001
prd: PRD-AI-001
title: Conversation Flow & Routing
status: Draft
owner: Product Owner
created_at: 2026-07-17
updated_at: 2026-07-17
---

# EPIC-AI-001 - Conversation Flow & Routing

## 1. Tujuan Epic

Menentukan bagaimana bot merespons customer berdasarkan configured state dari Control Panel. Mencakup: evaluasi status operasional bot berdasarkan hierarki kontrol, greeting & menu, routing percakapan antara bot dan agent, pengalihan langsung saat bot tidak aktif, pengalihan atas permintaan customer, fallback saat bot tidak bisa melayani, dan penghentian bot pada percakapan yang sedang berlangsung.

## 2. Related Problems

* PROB-AI-005: Semua chat masuk ke agent manusia tanpa filter → beban agent tinggi.
* PROB-AI-006: Tidak ada klasifikasi intent → agent harus memahami maksud customer dari nol.
* PROB-AI-007: Tidak ada slot-filling → agent harus mengumpulkan data pelanggan secara manual.
* PROB-AI-008: Tidak ada fallback atau eskalasi terstruktur → customer sederhana tetap menunggu antrean.

## 3. Feature List

| Feature ID  | Nama Feature                                            | Purpose                                                                                                        | Status |
| ----------- | ------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- | ------ |
| FEAT-AI-001 | Greeting, Intent Classification, Slot Filling & Handoff | Menentukan alur percakapan bot dari greeting, klasifikasi intent, pengumpulan data, hingga pengalihan ke agent | Draft  |

## 4. Notes

* Epic ini adalah jembatan antara Control Panel (apa yang diatur owner) dan intent (apa yang dieksekusi bot).
* Semua aturan di sini berlaku di level bot engine — tidak ada UI admin.
* Business rules hasil dari epic ini direferensikan oleh Control Panel (sebagai "apa yang terjadi saat switch diubah").
* Urutan prioritas 3 lapis: Switch Master OFF > Switch Outlet OFF > Switch Intent OFF.

## 5. Aturan Bisnis (Global)

### BR-AI-100 — Hierarki Aktivasi Bot

Bot hanya melayani customer jika tiga kondisi terpenuhi secara berurutan: Switch Master WABA aktif, Switch Autobot Outlet aktif, dan intent yang diminta aktif serta siap digunakan.

Pemeriksaan dilakukan berdasarkan urutan prioritas. Switch Master WABA diperiksa terlebih dahulu. Jika Switch Master WABA tidak aktif, bot berhenti dan tidak memeriksa status outlet maupun intent.

Jika Switch Master WABA aktif tetapi Switch Autobot Outlet tidak aktif, bot berhenti dan tidak memeriksa status intent.

### BR-AI-101 — Pengalihan Langsung saat Bot Tidak Aktif

Saat Switch Master WABA atau Switch Autobot Outlet tidak aktif, bot tidak menyapa customer, tidak mencoba mengenali intent, dan tidak memulai pengumpulan data.

Percakapan langsung diarahkan ke agent tanpa respons atau pemberitahuan apa pun dari bot.

Jika bot aktif tetapi intent tertentu tidak aktif atau tidak siap digunakan, kondisi tersebut mengikuti aturan ketersediaan layanan dan fallback intent.

### BR-AI-102 — Penghentian Bot pada Percakapan Aktif

Ketika Switch Master WABA atau Switch Autobot Outlet diubah dari aktif menjadi tidak aktif saat percakapan bot sedang berlangsung, bot menyelesaikan respons yang sedang diproses, kemudian menghentikan proses pada kesempatan pemrosesan berikutnya.

Bot tidak melanjutkan klasifikasi intent, slot-filling, atau proses layanan yang sedang berjalan. Percakapan kemudian langsung diarahkan ke agent.

Aturan ini tidak berlaku jika customer sudah berada dalam sesi agent.

### BR-AI-103 — Salam Berdasarkan Layanan Tersedia

Salam pembuka bot hanya menampilkan layanan yang memenuhi seluruh kondisi berikut:

* intent aktif;
* konfigurasi intent lengkap; dan
* data pendukung tersedia.

Intent yang tidak aktif, belum selesai dikonfigurasi, atau data pendukungnya belum tersedia tidak ditampilkan dalam daftar layanan.

### BR-AI-104 — Perintah Kendali yang Selalu Tersedia

Perintah "menu" dan "hubungi agen" tetap diproses selama customer masih berada dalam sesi bot, meskipun intent layanan tertentu tidak aktif.

Owner tidak dapat menonaktifkan perintah tersebut.

Perintah "menu" menampilkan kembali daftar layanan yang tersedia. Perintah "hubungi agen" menghentikan alur bot dan mengalihkan percakapan ke agent.

### BR-AI-105 — Penguncian Percakapan ke Agent

Setelah percakapan dialihkan ke agent, bot tidak akan memproses atau membalas pesan customer berikutnya dalam sesi percakapan tersebut.

Semua pesan setelah pengalihan langsung diarahkan ke agent.

Bot tidak kembali menangani percakapan secara otomatis, termasuk ketika customer mengirim perintah bot atau switch bot kembali diaktifkan.

### BR-AI-106 — Pengalihan saat Bot Tidak Dapat Melanjutkan

Jika bot tidak dapat melanjutkan atau menyelesaikan proses layanan, percakapan otomatis dialihkan ke agent.

Kondisi tersebut dapat terjadi ketika bot tidak menghasilkan respons dalam waktu 1 menit, proses slot-filling tidak dapat dilanjutkan, data pendukung tidak ditemukan, atau proses layanan mengalami kegagalan yang tidak dapat diselesaikan oleh bot.

Sebelum pengalihan, bot memberi tahu customer bahwa percakapan akan dilanjutkan oleh agent.

Pemberitahuan tidak diberikan jika pengalihan terjadi karena Switch Master WABA atau Switch Autobot Outlet tidak aktif.

### BR-AI-107 — Penanganan Intent Tidak Dikenali

Jika LLM tidak dapat mengenali intent customer, bot menampilkan daftar layanan yang tersedia dan meminta customer memilih atau menjelaskan kembali kebutuhannya.

Daftar layanan hanya berisi intent yang aktif dan siap digunakan.

Jika intent masih tidak dapat dikenali setelah 2 percobaan berturut-turut, bot memberi tahu customer bahwa percakapan akan dilanjutkan oleh agent, kemudian mengalihkan percakapan ke agent.

### BR-AI-110 — Hubungi Agen Selalu Aktif

Intent `talk_to_agent` tidak pernah dinonaktifkan dan tidak dicek di Gate 3 (Intent Switch). Customer selalu memiliki jalur eskalasi ke manusia terlepas dari pengaturan intent per outlet.

## 6. Referensi Dokumen Terkait

| Dokumen | Deskripsi |
|---------|-----------|
| `aibot/docs/intent-wording.md` | Label customer-facing untuk intent |
| `aibot/prd-v2/PRD-AI-003-customer-self-service-capabilities/prd.md` | Spesifikasi tiap intent self-service |
| `aibot/prd-v2/PRD-AI-004-pengaturan-outlet/prd.md` | Pengaturan jam operasional sebagai sumber data |
| `aibot/docs/conversation-flow.md` | Arsitektur alur percakapan |
| `aibot/docs/bot-behavior.md` | Aturan perilaku bot |
