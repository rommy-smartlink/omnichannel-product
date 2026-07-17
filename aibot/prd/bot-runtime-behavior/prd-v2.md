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

- Bot langsung merespons customer tanpa memperhitungkan configured state.
- Tidak ada mekanisme in-flight interruption — owner tidak bisa menghentikan chat bot yang sedang berlangsung.
- Greeting menampilkan semua capability tanpa memeriksa kesiapan data.
- Tidak ada perbedaan antara silent handoff (otomatis) dan explicit handoff (customer minta agent).
- Tidak ada timeout fallback.

### Problems

- PROB-AI-005: Bot tetap berjalan meskipun owner sudah mematikan Master Switch.
- PROB-AI-006: Tidak ada mekanisme penghentian percakapan bot yang sedang berlangsung (in-flight interruption).
- PROB-AI-007: Greeting menampilkan capability yang datanya belum siap.
- PROB-AI-008: Tidak ada fallback saat bot timeout atau unknown intent.

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
- Command manual "menu", "hubungi agen", "batal" selalu berfungsi.

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

## 5. User Stories

- As a customer, saya ingin bot membalas hanya jika outlet dan WABA sedang aktif agar saya tidak menunggu bot yang sebenarnya mati.
- As a customer, saya ingin diarahkan ke agent jika bot sedang dimatikan saat saya mengobrol, tanpa kebingungan.
- As a customer, saya ingin bot berhenti membalas segera setelah percakapan masuk ke agent.
- As an owner, saya ingin mematikan switch langsung menghentikan chat bot yang sedang berjalan dan mengalihkannya ke agent.
- As an owner, saya ingin aturan pengalihan tidak berlaku jika customer sudah berada di sesi agent.

## 6. Goals & Non-Goals

### 6.1 Goals

- Menentukan perilaku bot berdasarkan configured state dari Control Panel.
- Menjalankan greeting dan menu hanya untuk capability ON dan siap.
- Melakukan silent handoff ketika bot tidak boleh aktif.
- Menghentikan sesi bot yang sedang berjalan saat switch dimatikan (in-flight interruption).
- Mengecualikan penghentian saat customer sudah di sesi agent.
- Menjalankan explicit handoff saat customer meminta agent.
- Menjalankan timeout fallback dan unknown fallback.

### 6.2 Non-Goals — MVP

- Mengubah konfigurasi bot (milik Control Panel).
- Menentukan UI dashboard.
- Menentukan jadwal operasional (milik Pengaturan Jam Operasional).
- Menentukan isi respons tiap intent (milik PRD intent masing-masing).
- Menentukan slot filling flow per intent.

## 7. Input dari Control Panel

Runtime membaca tiga nilai configured state per outlet:

| Sumber | Nilai | Dampak Runtime |
|---|---|---|
| Master Switch WABA | ON / OFF | Jika OFF, seluruh outlet di WABA tidak boleh menjalankan bot. |
| Autobot Outlet | ON / OFF | Jika OFF, outlet tersebut tidak boleh menjalankan bot. |
| Capability per intent | ON / OFF | Jika OFF, intent tidak muncul dan tidak dijalankan. |

Effective state bot untuk sebuah outlet:

```text
Bot boleh jalan HANYA JIKA:
Master Switch WABA = ON
DAN
Autobot Outlet = ON
```

## 8. Bot Flow Integration

1. Customer mengirim pesan ke outlet X dalam WABA W.
2. Sistem membaca Master Switch WABA W.
3. Jika Master Switch WABA OFF:
   - skip greeting;
   - skip intent classification;
   - silent handoff ke agent.
4. Jika Master Switch WABA ON, sistem membaca Autobot Outlet X.
5. Jika Autobot Outlet OFF:
   - skip greeting;
   - skip intent classification;
   - silent handoff ke agent.
6. Jika Autobot Outlet ON, sistem membaca capability outlet:
   - capability ON → capability berjalan normal;
   - capability OFF → fallback intent tidak tersedia;
   - `get_operating_hours` selalu dianggap data siap (default 24 jam jika jadwal belum dikonfigurasi);
   - semua configurable capability OFF → silent handoff tanpa greeting atau menu.
7. `talk_to_agent` selalu tersedia — tidak dapat dimatikan oleh owner (lihat EPIC-AI-002 §5 Notes).

## 9. In-Flight Interruption

Aturan ini berlaku ketika owner mengubah configured state **saat customer sedang menchat dengan bot**.

### 9.1 Master Switch WABA dimatikan (OFF)

- Sistem menghentikan sesi bot yang sedang berlangsung di seluruh outlet WABA tersebut.
- Customer diarahkan ke agent melalui silent handoff pada respons berikutnya.
- Aturan tidak berlaku jika customer sudah berada dalam sesi chat dengan agent — tidak ada sesi bot yang aktif untuk dihentikan.

### 9.2 Autobot Outlet dimatikan (OFF)

- Sistem menghentikan sesi bot yang sedang berlangsung pada outlet tersebut.
- Customer diarahkan ke agent melalui silent handoff pada respons berikutnya.
- Aturan tidak berlaku jika customer sudah berada dalam sesi chat dengan agent — tidak ada sesi bot yang aktif untuk dihentikan.

### 9.3 Master Switch WABA diaktifkan kembali (ON)

- Bot kembali berjalan pada pesan customer berikutnya menggunakan konfigurasi tersimpan.

### 9.4 Autobot Outlet diaktifkan kembali (ON)

- Bot kembali berjalan pada pesan customer berikutnya menggunakan konfigurasi capability terakhir yang tersimpan.

### 9.5 Penjelasan untuk Owner

Konfirmasi di Control Panel (lihat PRD Bot Control Panel master switch toggle) harus menjelaskan bahwa mematikan bot akan menghentikan percakapan bot yang sedang berlangsung dan mengalihkan customer ke agent.

## 10. Greeting & Menu

Greeting hanya menampilkan capability yang memenuhi dua kondisi:

```text
Capability = ON
DAN
Data pendukung = siap
```

Jam Operasional dianggap selalu siap karena default 24 jam berlaku jika jadwal belum dikonfigurasi (lihat PRD Control Panel v3 — FEAT-AI-004).

Jadwal khusus Tutup (libur sementara): intent tetap ON, bot menjelaskan status libur dan kapan outlet kembali buka.

## 11. Handoff ke Agent

### 11.1 Silent Handoff

Terjadi otomatis tanpa customer meminta, pada kondisi:

- Master Switch WABA OFF;
- Autobot Outlet OFF;
- semua configurable capability OFF atau tidak siap;
- in-flight interruption (§9) sedang berlaku.

### 11.2 Explicit Handoff

Terjadi saat customer meminta agent (`talk_to_agent`). Bot berhenti membalas setelah conversation masuk ke agent. Lihat PRD `talk_to_agent` untuk detail trigger phrases dan flow.

### 11.3 Aturan Setelah Handoff

- Bot berhenti membalas percakapan tersebut.
- Jika agent belum tersedia, customer mendapat pesan fallback yang jelas.
- Bot tidak mencoba melanjutkan jawaban otomatis setelah conversation masuk ke agent.

## 12. Fallback

### 12.1 Capability OFF

```text
Maaf Kak, fitur ini sedang tidak tersedia di outlet ini.
Saya bisa hubungkan Kakak ke agent untuk dibantu lebih lanjut.
```

### 12.2 Data Tidak Siap

Khusus untuk `get_operating_hours`, fallback ini tidak berlaku karena default 24 jam selalu tersedia. Untuk intent lain yang datanya corrupt/tidak valid saat runtime:

```text
Maaf Kak, informasi yang Kakak minta belum dapat dipastikan saat ini.
Saya bisa hubungkan Kakak ke agent outlet untuk konfirmasi.
```

### 12.3 Unknown Intent

```text
Maaf Kak, saya belum memahami kebutuhan Kakak.

Saya bisa bantu:
1. Cek status laundry
2. Cek status tiket
3. Info layanan outlet
4. Jam operasional
5. Promo aktif
6. Member
7. Hubungi agent

Boleh ketik kebutuhan Kakak?
```

### 12.4 Timeout Fallback

Setelah 1 menit tanpa respons customer di tengah flow, bot mengirim pesan timeout lalu eskalasi ke agent:

```text
Kak, sepertinya Kakak sedang tidak di depan layar.
Saya hubungkan ke agent ya supaya bisa dibantu saat Kakak kembali.
```

> Catatan: Command manual "menu", "hubungi agen", "batal" selalu berfungsi dalam kondisi apapun, selama bot masih aktif di percakapan.

## 13. Acceptance Criteria

- [ ] Bot hanya jalan jika Master Switch WABA ON dan Autobot Outlet ON.
- [ ] Master Switch OFF → silent handoff seluruh outlet di WABA.
- [ ] Autobot Outlet OFF → silent handoff untuk outlet tersebut.
- [ ] In-flight interruption: customer yang sedang chat bot langsung dialihkan ke agent saat switch dimatikan.
- [ ] Customer di sesi agent tidak terpengaruh oleh in-flight interruption.
- [ ] Greeting hanya menampilkan capability ON + data siap.
- [ ] Semua configurable capability OFF → silent handoff tanpa greeting.
- [ ] `talk_to_agent` selalu tersedia, tidak bisa dimatikan.
- [ ] Explicit handoff saat customer minta agent kapan pun, termasuk mid-flow.
- [ ] Bot berhenti membalas setelah handoff ke agent.
- [ ] Timeout fallback setelah 1 menit → eskalasi ke agent.
- [ ] Unknown intent fallback menampilkan daftar bantuan.
- [ ] Capability OFF fallback: pesan fitur tidak tersedia.
- [ ] `get_operating_hours` selalu dianggap data siap (default 24 jam jika jadwal belum dikonfigurasi).
- [ ] Command manual "menu", "hubungi agen", "batal" selalu berfungsi.

## 14. References

| Type | URL / Reference | Description |
|---|---|---|
| Control Panel PRD | `aibot/prd/P8-control-panel/prd-v3.md` | Sumber configured state |
| Control Panel Epic | `aibot/prd/P8-control-panel/epic-aibot-control-panel.md` | EPIC-AI-002 — Bot Control Panel |
| Conversation Flow | `aibot/docs/conversation-flow.md` | Arsitektur alur percakapan |
| Bot Behavior | `aibot/docs/bot-behavior.md` | Aturan perilaku bot |
| Intents | `aibot/docs/intents.md` | Daftar intent MVP |
| Main | `aibot/docs/main.md` | Feature overview utama |
| Talk to Agent PRD | `aibot/prd/P2-talk_to_agent/prd.md` | Detail explicit handoff |
| GWT Control Panel | `aibot/gwt/P8-control-panel/gwt.md` | Skenario GWT terkait bot behavior integration |
| Figma | TBD | Board desain terkait |
