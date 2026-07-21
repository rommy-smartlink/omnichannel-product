---
id: FEAT-AI-001
prd: PRD-AI-001
epic: EPIC-AI-001
title: Greeting, Intent Classification, Slot Filling & Handoff
status: Draft
owner: Product Owner
created_at: 2026-07-17
updated_at: 2026-07-17
---

# FEAT-AI-001 - Greeting, Intent Classification, Slot Filling & Handoff

## 1. Purpose

Menentukan bagaimana bot merespons customer dari pesan pertama hingga percakapan selesai (baik terlayani bot, eskalasi ke agent, atau silent handoff). Fitur ini adalah runtime engine yang membaca configured state dari Control Panel dan menerjemahkannya menjadi perilaku nyata bot di WhatsApp.

## 2. Scope

### In Scope

- **Evaluasi status efektif**: 3-gate check (Master → Outlet → Intent) sebelum bot merespons.
- **Salam & menu**: menampilkan daftar capability yang ON + data siap.
- **Pengalihan langsung (semua penyebab)**: Master OFF, Outlet OFF, semua intent configurable OFF.
- **Penghentian percakapan aktif**: menghentikan percakapan bot saat switch dimatikan owner.
- **Fallback intent nonaktif**: pesan fallback saat customer meminta intent yang OFF.
- **Pengalihan atas permintaan**: customer minta agent — konfirmasi lalu handoff.
- **Fallback intent tidak dikenali**: bot tidak paham → tawar daftar bantuan → repeated unknown → eskalasi.
- **Eskalasi waktu habis**: 1 menit bot tidak bisa merespons → eskalasi ke agent.
- **Fallback data tidak siap**: informasi belum dapat dipastikan.
- **Perintah kendali**: "menu", "hubungi agen" selalu berfungsi.
- **Penguncian percakapan ke agent**: setelah handoff, bot berhenti membalas.

### Out of Scope

- Menentukan isi respons tiap intent (milik PRD-AI-003 intent masing-masing).
- Menentukan slot filling flow per intent (milik PRD-AI-003).
- Mengubah konfigurasi bot (milik PRD-AI-002 Control Panel).
- Menentukan jadwal operasional (milik PRD-AI-004).
- UI dashboard.

## 3. Business Rules

| BR ID | Nama | Deskripsi |
|---|---|---|
| BR-AI-100 | Hierarki Aktivasi Bot | Master ON + Outlet ON + Intent ON → bot jalan. Urutan: Master → Outlet → Intent |
| BR-AI-101 | Pengalihan Langsung saat Bot Tidak Aktif | Bot tidak kirim greeting, tidak klasifikasi, langsung ke agent |
| BR-AI-102 | Penghentian Bot pada Percakapan Aktif | Switch OFF → bot hentikan percakapan pada respons berikutnya, arahkan ke agent |
| BR-AI-103 | Salam Berdasarkan Layanan Tersedia | Hanya capability ON + data siap yang muncul di greeting |
| BR-AI-104 | Perintah Kendali yang Selalu Tersedia | "menu", "hubungi agen" selalu diproses |
| BR-AI-105 | Penguncian Percakapan ke Agent | Setelah handoff, bot stop membalas |
| BR-AI-106 | Pengalihan saat Bot Tidak Dapat Melanjutkan | 1 menit no response → eskalasi ke agent |
| BR-AI-107 | Penanganan Intent Tidak Dikenali | Repeated unknown → daftar bantuan → eskalasi |
| BR-AI-108 | Fallback Intent OFF | Intent OFF → "Maaf Kak, fitur ini sedang tidak tersedia…" |
| BR-AI-109 | Fallback Data Tidak Siap | Data tidak siap → "Informasi belum dapat dipastikan…" |
| BR-AI-110 | Hubungi Agen Selalu Aktif | talk_to_agent tidak pernah dinonaktifkan, tidak dicek di gate 3 |

## 4. Dependencies

- PRD-AI-002 Bot Control Panel (sumber configured state: Master Switch, Outlet Switch, Intent Switch).
- PRD-AI-003 intent masing-masing (isi respons per intent).
- Data outlet omnichannel.
- Sistem handoff ke agent.
- LLM intent classification engine.

## 5. User Story List

| US ID | Judul US | Goal | Area | Status |
|---|---|---|---|---|
| US-AI-027 | Evaluasi Effective State Bot | Bot menentukan boleh jalan/tidak berdasarkan 3-gate check | Effective State | Draft |
| US-AI-028 | Greeting Berdasarkan Intent Siap | Greeting hanya menampilkan capability ON + data siap | Greeting | Draft |
| US-AI-007 | Silent Handoff: Customer Terdampak Master OFF | Customer silent handoff saat master switch OFF | Silent Handoff | Draft |
| US-AI-024 | Silent Handoff: Customer Terdampak Outlet OFF | Customer silent handoff di outlet yang outlet switch-nya OFF | Silent Handoff | Draft |
| US-AI-017 | Silent Handoff: Semua Intent OFF | Silent handoff saat semua intent configurable OFF | Silent Handoff | Draft |
| US-AI-016 | Fallback Intent OFF | Pesan fallback saat customer minta capability yang OFF | Fallback | Draft |
| US-AI-029 | Explicit Handoff: Customer Minta Agent | Customer minta "hubungi agen" → konfirmasi → handoff | Handoff | Draft |
| US-AI-030 | Unknown Intent Fallback | Bot tidak paham → tawar bantuan → eskalasi | Fallback | Draft |
| US-AI-031 | Timeout Escalation | 1 menit tidak bisa respon → eskalasi ke agent | Fallback | Draft |
| US-AI-032 | Data Not Ready Fallback | Data tidak siap → pesan tidak dapat dipastikan | Fallback | Draft |
| US-AI-018 | Command Override | "menu", "hubungi agen" selalu berfungsi | Command | Draft |
| US-AI-033 | Post-Handoff Bot Silence | Setelah handoff, bot berhenti membalas | Handoff | Draft |
| US-AI-067 | Melanjutkan Konteks Percakapan | Bot mempertahankan konteks untuk pesan lanjutan yang masih terkait | Context | Draft |
| US-AI-068 | Berpindah Intent di Tengah Percakapan | Customer dapat mengganti kebutuhan tanpa terjebak flow sebelumnya | Routing | Draft |
| US-AI-069 | Menangani Beberapa Intent dalam Satu Pesan | Bot memproses beberapa kebutuhan dengan urutan yang aman dan jelas | Routing | Draft |
| US-AI-070 | Menyelesaikan Flow dan Menawarkan Langkah Berikutnya | Bot menutup layanan secara jelas tanpa mengakhiri sesi secara prematur | Completion | Draft |
| US-AI-071 | Menangani Kegagalan Handoff | Customer mendapat kepastian saat pengalihan ke agent tidak dapat diselesaikan | Handoff | Draft |

## 6. Notes for Developer

- Effective state dievaluasi setiap kali customer mengirim pesan (bukan hanya di awal percakapan).
- Silent handoff berarti zero response dari bot — customer hanya melihat "menunggu agent" di sisi WhatsApp.
- In-flight interruption tidak instan — bot menyelesaikan response yang sedang diproses, baru setelah itu handoff.
- Semua business rules di feature ini direferensikan oleh Control Panel (sebagai "dampak saat switch diubah").
- Override priority 3-gate dicek oleh bot engine. Lihat EPIC-AI-001 untuk urutan lengkap.
