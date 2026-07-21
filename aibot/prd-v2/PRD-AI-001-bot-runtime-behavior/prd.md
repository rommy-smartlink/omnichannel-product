---
id: PRD-AI-001
title: Bot Runtime Behavior
product: Smartlink
module: AI Bot Omnichannel
version: 1.0.0
status: Draft
owner: Product Owner
reviewers:
  - UIX
  - Engineering
  - QA
created_at: 2026-07-16
updated_at: 2026-07-16
---

# PRD-AI-001 - Bot Runtime Behavior

## 1. Executive Summary

### Background

Bot Control Panel menyediakan konfigurasi: Master Switch WABA, Autobot Outlet, dan toggle capability per intent. Namun konfigurasi tersebut harus diterjemahkan menjadi perilaku nyata bot di WhatsApp. Tanpa spesifikasi runtime yang eksplisit, ada celah antara "apa yang diatur owner" dan "apa yang dialami customer".

Bot Runtime Behavior mendefinisikan bagaimana bot merespons customer berdasarkan configured state, kapan bot boleh jalan, kapan harus silent handoff, apa yang terjadi saat percakapan bot yang sedang berjalan dihentikan oleh owner, dan seperti apa fallback untuk setiap kondisi.

### Current Condition

- Belum ada bot AI Omnichannel. Semua chat WhatsApp dari customer masuk langsung ke agent manusia tanpa otomasi.
- Tidak ada klasifikasi intent otomatis — agent harus membaca dan memahami maksud customer secara manual.
- Tidak ada slot-filling — agent harus menanyakan satu per satu informasi yang dibutuhkan.
- Tidak ada autobot response — semua balasan diketik manual oleh agent.
- Tidak ada mekanisme handoff — karena seluruh percakapan sudah bersama agent manusia sejak awal.

### Problems

- PROB-AI-005: Semua chat masuk ke agent manusia tanpa filter → beban agent tinggi, tidak ada triase otomatis.
- PROB-AI-006: Tidak ada klasifikasi intent → agent harus memahami maksud customer dari nol di setiap percakapan.
- PROB-AI-007: Tidak ada slot-filling → agent harus mengumpulkan data pelanggan secara manual (nomor tiket, outlet, dsb).
- PROB-AI-008: Tidak ada fallback atau eskalasi terstruktur → customer dengan pertanyaan sederhana tetap menunggu antrean agent.

### Expected Condition

- Bot hanya berjalan jika Master Switch WABA ON dan Autobot Outlet ON.
- Owner bisa menghentikan chat bot yang sedang berlangsung dengan mematikan switch, dan customer langsung dialihkan ke agent.
- Greeting hanya menampilkan capability yang ON dan datanya siap.
- Silent handoff otomatis saat bot tidak boleh aktif.
- Explicit handoff saat customer meminta agent.
- Timeout fallback setelah 1 menit.
- Unknown intent fallback dengan daftar bantuan.

### Proposed Solution

Spesifikasi runtime behavior yang menjadi jembatan antara Bot Control Panel (konfigurasi) dan intent (eksekusi). Mencakup: effective state bot, greeting & menu, in-flight interruption, silent handoff, explicit handoff, timeout fallback, dan unknown fallback.

## 2. Global Scope

### In Scope

- Aturan effective state: bot hanya jalan jika Master WABA ON + Autobot Outlet ON.
- Greeting dan menu berdasarkan capability ON + data siap.
- In-flight interruption: menghentikan chat bot saat Master Switch atau Autobot Outlet dimatikan.
- Silent handoff otomatis saat bot tidak boleh aktif.
- Explicit handoff saat customer meminta agent.
- Bot berhenti membalas setelah handoff.
- Timeout fallback setelah 1 menit → eskalasi ke agent.
- Unknown intent fallback dengan daftar bantuan.
- Capability OFF fallback: fitur tidak tersedia.
- Data tidak siap fallback: informasi belum dapat dipastikan.
- Semua configurable capability OFF → silent handoff tanpa greeting.
- Command manual "menu", "hubungi agen" selalu berfungsi.

### Out of Scope

- Mengubah konfigurasi bot (milik Control Panel).
- Menentukan UI dashboard.
- Menentukan jadwal operasional (milik Pengaturan Jam Operasional).
- Menentukan isi respons tiap intent (milik PRD intent masing-masing).
- Menentukan slot filling flow per intent.

## 3. Epic List

| Epic ID | Nama Epic | Tujuan Singkat | Status |
|---|---|---|---|
| EPIC-AI-001 | Conversation Flow & Routing | Menentukan bagaimana bot merespons customer berdasarkan configured state | Draft |

## 4. Feature List

| Feature ID | Nama Feature | Purpose | Status |
|---|---|---|---|
| FEAT-AI-001 | Greeting, Intent Classification, Slot Filling & Handoff | Menentukan alur percakapan bot dari greeting, klasifikasi intent, pengumpulan data, hingga eskalasi ke agent | Draft |

## 5. References

| Type | URL / Reference | Description |
|---|---|---|
| Control Panel PRD | `aibot/prd-v2/PRD-AI-002-bot-control-panel/prd.md` | Sumber configured state |
| Conversation Flow | `aibot/docs/conversation-flow.md` | Arsitektur alur percakapan |
| Bot Behavior | `aibot/docs/bot-behavior.md` | Aturan perilaku bot |
| Intents | `aibot/docs/intents.md` | Daftar intent MVP |
| Main | `aibot/docs/main.md` | Feature overview utama |
| GWT Control Panel | `aibot/gwt/P8-control-panel/gwt.md` | Skenario GWT terkait bot behavior integration |
| Figma | TBD | Board desain terkait |
