# PRD — Bot Runtime Behavior

## 1. Overview

Bot Runtime Behavior mendefinisikan bagaimana `omnichannel-ai-bot` beroperasi di sisi customer saat percakapan berlangsung. Spesifikasi ini menjembatani **configured state** yang disimpan owner di Bot Control Panel dengan **perilaku nyata bot** di WhatsApp.

Runtime Behavior mencakup:

1. Cara bot membaca konfigurasi (Master Switch WABA, Autobot Outlet, Capability) untuk menentukan apakah bot boleh jalan.
2. Greeting dan menu berdasarkan capability yang aktif dan siap.
3. Intent classification dan slot filling.
4. Handoff ke agent (silent handoff dan explicit handoff).
5. **In-flight interruption** — apa yang terjadi pada percakapan yang sedang berjalan ketika owner mematikan switch.
6. Timeout fallback dan unknown fallback.

Spesifikasi ini dipisah dari Bot Control Panel agar aturan runtime dapat diuji melalui simulasi chat, terlepas dari pengujian UI dashboard.

---

## 2. User Stories

- As a customer, saya ingin bot membalas hanya jika outlet dan WABA sedang aktif agar saya tidak menunggu bot yang sebenarnya mati.
- As a customer, saya ingin diarahkan ke agent jika bot sedang dimatikan saat saya mengobrol, tanpa kebingungan.
- As a customer, saya ingin bot berhenti membalas segera setelah percakapan masuk ke agent.
- As an owner, saya ingin mematikan switch langsung menghentikan chat bot yang sedang berjalan dan mengalihkannya ke agent.
- As an owner, saya ingin aturan pengalihan tidak berlaku jika customer sudah berada di sesi agent.

---

## 3. Goals & Non-Goals

### 3.1 Goals

- Menentukan perilaku bot berdasarkan configured state dari Control Panel.
- Menjalankan greeting dan menu hanya untuk capability ON dan siap.
- Melakukan silent handoff ketika bot tidak boleh aktif.
- Menghentikan sesi bot yang sedang berjalan saat switch dimatikan (in-flight interruption).
- Mengecualikan penghentian saat customer sudah di sesi agent.
- Menjalankan explicit handoff saat customer meminta agent.
- Menjalankan timeout fallback dan unknown fallback.

### 3.2 Non-Goals — MVP

- Mengubah konfigurasi bot (milik Control Panel).
- Menentukan UI dashboard.
- Menentukan jadwal operasional (milik Pengaturan Jam Operasional).
- Menentukan isi respons tiap intent (milik PRD intent masing-masing).

---

## 4. Input dari Control Panel

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

---

## 5. Bot Flow Integration

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
   - capability ON dan data siap → capability berjalan normal;
   - capability OFF → fallback intent tidak tersedia;
   - capability ON tetapi data tidak siap → diperlakukan OFF dan menggunakan fallback data;
   - semua configurable capability OFF atau tidak siap → silent handoff tanpa greeting atau menu.
7. `talk_to_agent` selalu tersedia.

---

## 6. In-Flight Interruption

Aturan ini berlaku ketika owner mengubah configured state **saat customer sedang menchat dengan bot**.

### 6.1 Master Switch WABA dimatikan (OFF)

- Sistem menghentikan sesi bot yang sedang berlangsung di seluruh outlet WABA tersebut.
- Customer diarahkan ke agent melalui silent handoff pada respons berikutnya.
- Aturan tidak berlaku jika customer sudah berada dalam sesi chat dengan agent, karena tidak ada sesi bot yang aktif untuk dihentikan.

### 6.2 Autobot Outlet dimatikan (OFF)

- Sistem menghentikan sesi bot yang sedang berlangsung pada outlet tersebut.
- Customer diarahkan ke agent melalui silent handoff pada respons berikutnya.
- Aturan tidak berlaku jika customer sudah berada dalam sesi chat dengan agent, karena tidak ada sesi bot yang aktif untuk dihentikan.

### 6.3 Master Switch WABA diaktifkan kembali (ON)

- Bot kembali berjalan pada pesan customer berikutnya menggunakan konfigurasi tersimpan.

### 6.4 Autobot Outlet diaktifkan kembali (ON)

- Bot kembali berjalan pada pesan customer berikutnya menggunakan konfigurasi capability terakhir yang tersimpan.

### 6.5 Penjelasan untuk Owner

Konfirmasi di Control Panel (lihat PRD Bot Control Panel §9.3) harus menjelaskan bahwa mematikan bot akan menghentikan percakapan bot yang sedang berlangsung dan mengalihkan customer ke agent.

---

## 7. Greeting & Menu

Greeting hanya menampilkan capability yang memenuhi dua kondisi:

```text
Capability = ON
DAN
Data pendukung = siap
```

Jam Operasional tidak ditampilkan jika:

- intent OFF;
- data jadwal hilang;
- data jadwal rusak;
- data jadwal tidak valid.

Jadwal default 24/7 dianggap valid dan boleh ditampilkan.

---

## 8. Handoff ke Agent

### 8.1 Silent Handoff

Terjadi otomatis tanpa customer meminta, pada kondisi:

- Master Switch WABA OFF;
- Autobot Outlet OFF;
- semua configurable capability OFF atau tidak siap;
- in-flight interruption (§6) sedang berlaku.

### 8.2 Explicit Handoff

Terjadi saat customer meminta agent (`talk_to_agent`). Bot berhenti membalas setelah conversation masuk ke agent.

### 8.3 Aturan Setelah Handoff

- Bot berhenti membalas percakapan tersebut.
- Jika agent belum tersedia, customer mendapat pesan fallback yang jelas.
- Bot tidak mencoba melanjutkan jawaban otomatis setelah conversation masuk ke agent.

---

## 9. Fallback

### 9.1 Capability OFF

```text
Maaf Kak, fitur ini sedang tidak tersedia di outlet ini.
Saya bisa hubungkan Kakak ke agent untuk dibantu lebih lanjut.
```

### 9.2 Data Tidak Siap

```text
Maaf Kak, informasi jam operasional outlet belum dapat dipastikan saat ini.
Saya bisa hubungkan Kakak ke agent outlet untuk konfirmasi.
```

### 9.3 Unknown Intent

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

### 9.4 Timeout

Jika bot tidak dapat memberikan respons dalam waktu lebih dari 1 menit, customer diarahkan ke agent.

```text
Mohon maaf Kak, sistem kami membutuhkan waktu lebih lama dari biasanya.
Saya hubungkan Kakak ke agent agar bisa dibantu lebih cepat ya.
```

---

## 10. Edge Cases

| # | Skenario | Perilaku Sistem |
|---|---|---|
| 1 | Master WABA OFF saat chat berlangsung | Sesi bot dihentikan; customer dialihkan ke agent |
| 2 | Master WABA OFF saat customer sudah di agent | Tidak ada perubahan; customer tetap di agent |
| 3 | Autobot Outlet OFF saat chat berlangsung | Sesi bot outlet dihentikan; customer dialihkan ke agent |
| 4 | Autobot Outlet OFF saat customer sudah di agent | Tidak ada perubahan; customer tetap di agent |
| 5 | Master WABA kembali ON saat chat berlangsung | Bot berjalan pada pesan berikutnya |
| 6 | Autobot Outlet kembali ON saat chat berlangsung | Bot berjalan pada pesan berikutnya |
| 7 | Toggle berubah saat conversation sedang diproses | Perubahan berlaku pada pesan customer berikutnya |
| 8 | Semua configurable capability OFF | Silent handoff tanpa greeting |
| 9 | Hanya Jam Operasional OFF | Bot tetap berjalan dengan capability lain |
| 10 | Customer meminta agent di tengah flow | Bot berhenti membalas; explicit handoff |
| 11 | Bot timeout > 1 menit | Timeout fallback ke agent |
| 12 | Outlet tutup sementara | Intent tetap ON; bot menjelaskan status penutupan |

---

## 11. Acceptance Criteria

### 11.1 Effective State

- [ ] Bot hanya jalan jika Master WABA ON dan Autobot Outlet ON.
- [ ] Bot skip greeting dan classification saat Master WABA OFF.
- [ ] Bot skip greeting dan classification saat Autobot Outlet OFF.

### 11.2 In-Flight Interruption

- [ ] Mematikan Master WABA menghentikan chat bot yang sedang berlangsung dan mengalihkan ke agent.
- [ ] Mematikan Autobot Outlet menghentikan chat bot outlet dan mengalihkan ke agent.
- [ ] Penghentian tidak berlaku saat customer sudah di sesi agent.
- [ ] Mengaktifkan kembali bot berlaku pada pesan berikutnya.

### 11.3 Greeting & Menu

- [ ] Greeting hanya menampilkan capability ON dan siap.
- [ ] Capability OFF tidak muncul pada greeting.

### 11.4 Handoff & Fallback

- [ ] Silent handoff terjadi pada kondisi non-aktif.
- [ ] Explicit handoff saat customer minta agent.
- [ ] Bot berhenti membalas setelah handoff.
- [ ] Unknown fallback menampilkan daftar bantuan.
- [ ] Timeout > 1 menit mengalihkan ke agent.

---

## 12. Dependencies

- PRD Bot Control Panel — sumber configured state (Master Switch, Autobot Outlet, Capability).
- PRD Intent masing-masing — isi respons tiap intent.
- PRD Pengaturan Jam Operasional — validitas jadwal untuk `get_operating_hours`.
- `main.md` — alur utama, greeting, slot filling, handoff, timeout, unknown fallback.
