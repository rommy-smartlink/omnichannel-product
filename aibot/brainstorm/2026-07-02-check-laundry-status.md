# Brainstorm: Cek Status Laundry (`check_laundry_status`)

> Tanggal: 2026-07-02 · Sesi: 3 (lanjutan master switch & control panel) · Trigger: implementasi MVP untuk intent `check_laundry_status` yang merupakan capability inti dengan traffic tertinggi; perlu konfirmasi terhadap endpoint, mode tampilan, slot-filling, dan kondisi eskalasi.

> Catatan: ini BRAINSTORM (eksploratif). Tanda **[DRAFT]** berarti keputusan masih bisa berubah. Keputusan final akan pindah ke [prd/P1-check_laundry_status.md](../prd/P1-check_laundry_status.md) dan [gwt/P1-check_laundry_status.md](../gwt/P1-check_laundry_status.md).

---

## Decision (awal)

1. **Slot wajib**: hanya **ID transaksi**. Pencarian via nomor HP **tidak** masuk MVP (lihat Out of Scope). **[DRAFT — bisa berubah jika product memprioritaskan HP]**.
2. **Mode tampilan**: ada **3 mode** internal —
   - **Ringkas** = default respons utama.
   - **Detail Ringan** = triggered on-demand saat customer ketik "detail" / "info lengkap" / "lebih lengkap" dalam conversation ini.
   - **Eskalasi Agent** = otomatis untuk 7 kondisi sensitif (lihat Section 9 [main.md](../docs/main.md)) tanpa interaksi customer.
3. **Transaksi tidak ditemukan**:
   - Tanya ulang ID sampai **2×** dengan sopan.
   - Jika pada percobaan ke-3 masih tidak ketemu → auto-escalate ke `talk_to_agent`.
   - **Final** (diubah dari draft awal 1× retry menjadi 2× retry).
4. **Eskalasi kondisi sensitif**: bot **tampilkan info ringkas dulu** (ID + outlet + status + 1 kalimat konteks), **lalu** auto-escalate ke `talk_to_agent` — bukan langsung silent handoff. Customer tidak bingung tiba-tiba di-redirect.

---

## Use Case

| Skenario | Customer Input | Bot Response (ringkas) | Status Code Inti |
|----------|---------------|-------------------------|------------------|
| Status proses normal | "Laundry saya sudah jadi belum?" | "Belum Kak, masih dalam proses setrika (70%). Estimasi selesai 25 Juni 2026 17.00." | 200 |
| Siap diambil | "Cek TRX9812377123" | "Siap diambil Kak. Bisa datang ke outlet ya." + blok info singkat | 200 |
| Sudah diambil | "Nota INV-10239 gimana?" | "Sudah diambil Kak pada 25 Juni 2026." | 200 |
| Dibatalkan | "Status TRX9099" | "Transaksi dibatalkan Kak. Jika ada pertanyaan, saya hubungkan ke agent." | 200 |
| Minta detail | (setelah dapat ringkas) "detail" | Tambahkan layanan + status bayar + pengantaran | 200 |
| Tidak ditemukan (1× dari 2) | "TRX99999999" | "Maaf Kak, belum ketemu. Bisa cek lagi ID-nya?" | 404 |
| Tidak ditemukan (2× dari 2) | (kirim ID lain, juga 404) | "Saya hubungkan ke agent ya Kak untuk dicek manual." | escalate |
| Refund pending | "Cek TRX5566" | Ringkas 2 baris + "Ada proses refund, saya hubungkan ke agent ya." | 200 + escalate |
| API timeout | (fetch > 10 detik) | "Sistem butuh waktu lama, saya hubungkan ke agent." | timeout + escalate |
| Cancel flow | (customer ketik "batal" mid-slot) | "Baik Kak, saya batalkan. Ada yang bisa dibantu lain?" | cancel |
| Minta HP-based lookup | "Cari lewat HP dong" | "Untuk saat ini cek laundry pakai ID transaksi dulu ya Kak." | out-of-scope |

---

## Affected Intents & Components

### Intents yang terdampak langsung

| Intent | Arah dampak |
|--------|-------------|
| `check_laundry_status` | **Primary** — semua flow di file ini. |
| `talk_to_agent` | **Downstream** — menerima handoff dari 7 kondisi sensitif + 404 kedua + timeout API. |
| `unknown` | **Edge** — jika customer ketik ID dengan noise ("cek yang TRX-9812 itu ya") trigger LLM unsure; bukan `unknown` tapi tetap `check_laundry_status` jika regex menangkap pola. |

### Komponen platform yang terdampak

| Komponen | Perubahan |
|----------|-----------|
| `outlet_intent_settings.check_laundry_status` | Sudah ada (default `true`) dari Control Panel PRD. Tidak ada perubahan schema, tapi ditambahkan konfigurasi: `retry_id_not_found = 1` **[DRAFT]** (hardcode dulu). |
| `waba_bot_settings` (master switch) | dari master-switch.md: jika OFF → `check_laundry_status` skip, customer langsung ke agent. Tidak ada perubahan tambahan di file ini. |
| Conversation state | Tambah field `laundry_context.mode = "ringkas" \| "detail" \| "escalated"` dan `laundry_context.last_id_transaksi` **[DRAFT]** untuk mendukung "ketik 'detail' lagi = munculkan detail". |
| Response template registry | Tambah 4 template: `laundry.ringkas`, `laundry.detail`, `laundry.not_found_retry`, `laundry.escalate_sensitive`. Masing-masing parameterized. |
| Audit log | Log: intent detection, ID value (masked? lihat Open Q #6), fetch status, mode pilihan, escalate trigger. |

---

## Flow Integration

```text
1. Customer message masuk di outlet X
   → Bot context sudah punya outlet_id=X, waba_id=W
2. Master switch check (WABA level):
   - OFF → skip semua, langsung talk_to_agent
   - ON  → lanjut
3. outlet_intent_settings check (intent level untuk outlet X):
   - OFF → fallback "Fitur ini sedang tidak tersedia di outlet ini"
   - ON  → lanjut
4. LLM intent classification:
   - unknown / other → keluar flow, handle sesuai intent masing-masing
   - check_laundry_status → masuk flow ini
5. Slot filling — ID transaksi:
   a. Cek apakah customer sudah menyertakan pola ID.
      Pola regex (DRAFT):
        - [Tt][Rr][Xx]\d{8,}   → "TRX9812377123"
        - [Ii][Nn][Vv][-_]?\d+ → "INV-10239", "INV_10239", "INV10239"
        - [Nn][Oo][Tt][Aa]\s?\d+ → "Nota 10239"
        - Long numeric (≥8 digit) → "9812377123"
      Normalisasi: uppercase, strip prefix/spasi/dash, simpan sebagai `id_transaksi_clean`.
   b. Jika belum ada / regex tidak match:
      → Bot: "Baik Kak, boleh kirim ID transaksi laundry-nya?"
      → Tunggu reply berikutnya (state: `awaiting_id`).
      → Timeout 1 menit (global) → escalate.
   c. Jika ada → langsung ke step 6.
6. Pre-fetch validation (DRAFT — ringan, di bot layer):
   - id_transaksi_clean.length >= 6 (float untuk menangkap "TRX12" pun tetap di-pass ke endpoint; validasi penuh di endpoint).
   - Jika terlalu pendek → tanya ulang.
7. Fetch ke API:
   GET {BASE_API}/masterData/meta/detail_data_transaksi?idtransaksi={id}
   Header: Authorization: Bearer {jwt}
   Timeout: 10 detik (DRAFT).
8. Handling response:
   - 200 + data valid:
     a. Evaluasi kondisi eskalasi (lihat Mode Selection).
     b. Pilih mode:
        - escalated → tampil ringkas (ID+outlet+nama+status+1 kalimat) + auto handoff
        - mode=detail (dari request customer "detail") → tampil detail ringan
        - mode=ringkas (default) → tampil ringkas
   - 404:
     - retry_count += 1
     - jika retry_count <= 2 → "Maaf Kak, belum ketemu. Bisa dicek lagi ID-nya?"
     - jika retry_count > 2  → escalate
   - 400 (id kosong dari sisi client) → bot tidak sampai sini karena pre-valid, tapi jika muncul → tanya ulang.
   - 401 → log error, tampil generic "Sistem ada kendala, saya hubungkan ke agent ya." + escalate.
   - 500 → sama seperti 401 tapi message variasi "Sistem butuh waktu lebih lama".
   - Timeout (no response within 10s) → treat sama seperti 500: tampil fallback + escalate.
9. Mode detail dipicu:
   - Customer reply "detail" / "info lengkap" / "lebih lengkap" / "rincian" dalam conversation ini (dan ada last_id_transaksi valid) → regenerate dengan mode=detail.
   - 1× saja per ID; jika minta "detail" lagi → balas "Detail sudah ditampilkan di atas ya Kak."
10. Conversation state cleanup:
    - Stale state (yang tidak di-interact > 5 menit) → reset; ID berikut diperlakukan sebagai flow baru. **[DRAFT]**
```

### Mode Selection (Eskalasi Decision Tree)

```
mode = "ringkas" (default)
  ↓
evaluate(fetched_data):
  if status_transaksi_format == "Problem"                  → escalated
  if status_hapus == 1                                     → escalated
  if idpermintaan_hapus exists                             → escalated
  if data_refund.status == "pending" or "diproses"         → escalated
  if pengantaran_extra.status == "gagal"                   → escalated
  if rincian catatan mengandung keyword komplain/hilang/rusak → escalated
  if lunas=false AND total>0 AND pengantaran.status != "selesai" AND tanggal_diterima > 14 hari lalu → escalated (asumsi: transaksi "lama belum lunas")
     ↑ DRAFT: threshold "lama" 14 hari perlu konfirmasi
  else                                                      → ringkas (lanjut)
```

---

## UX / Contoh Response

### Skenario A — Ringkas default, status proses

```
Customer: laundry saya sudah jadi belum?
Bot:      Baik Kak, saya cek dulu ya.

Bot:      Berikut status transaksi Kakak:

          ID Transaksi: TRX9812377123
          Outlet: Laundry Tebet
          Nama: Kak Budi
          Status: Dalam proses setrika
          Progress: 70%
          Diterima: 24 Juni 2026
          Estimasi selesai: 25 Juni 2026 pukul 17.00

          Arahan: Laundry Kakak masih diproses. Silakan cek lagi mendekati waktu estimasi selesai ya.
```

### Skenario B — Ringkas default, siap diambil

```
Customer: cek TRX9812377123
Bot:      Baik Kak, saya cek dulu ya.

Bot:      Laundry Kakak sudah siap diambil.

          ID: TRX9812377123
          Outlet: Laundry Tebet
          Status: Siap diambil
          Diterima: 24 Juni 2026
          Selesai: 25 Juni 2026 pukul 16.30

          Arahan: Kakak bisa datang ke outlet untuk mengambil laundry.
```

### Skenario C — Ringkas default, sudah diambil

```
Customer: nota INV-10239 gimana?
Bot:      Transaksi Kakak sudah selesai dan sudah diambil ya.

          ID: TRX9812377123
          Outlet: Laundry Tebet
          Diterima: 24 Juni 2026
          Diambil: 25 Juni 2026

          Ada lagi yang bisa dibantu?
```

### Skenario D — Ringkas default, dibatalkan

```
Customer: status TRX9099 gimana?
Bot:      Transaksi Kakak berstatus dibatalkan ya.

          ID: TRX9099
          Outlet: Laundry Tebet

          Jika ada pertanyaan lain, saya hubungkan ke agent untuk dibantu manual ya Kak.
```

### Skenario E — Detail Ringan (on-demand)

```
Customer: detail
Bot:      Berikut detail transaksi Kakak:

          ID: TRX9812377123
          Outlet: Laundry Tebet
          Nama: Kak Budi
          Status: Dalam proses setrika
          Progress: 70%
          Diterima: 24 Juni 2026
          Estimasi selesai: 25 Juni 2026 pukul 17.00

          Layanan:
          - Cuci Setrika — 3 Kg
          - Bed Cover — 1 pcs

          Status ambil: Belum diambil
          Pengantaran: Diantar, status dalam perjalanan
```

### Skenario F — Tidak ditemukan + retry (1× dari 2)

```
Customer: TRX99999999
Bot:      Maaf Kak, saya belum menemukan transaksi dengan ID itu di Outlet Tebet.
          Bisa dicek lagi ID-nya? Kalau ada di nota biasanya diawali TRX, INV, atau "Nota".
```

### Skenario G — Tidak ditemukan + escalate (setelah 2 retry)

```
Customer: (kirim ulang) TRX88888888
Bot:      Mohon maaf Kak, saya tetap belum menemukan transaksinya.
          Saya hubungkan ke agent Outlet Tebet agar bisa dicek langsung ya.
          Mohon tunggu sebentar.
```

### Skenario H — Eskalasi otomatis, refund pending

```
Customer: cek transaksi yang kemarin ada refund
Bot:      Saya menemukan transaksi Kakak ya. Tapi ada proses refund yang perlu dicek lebih lanjut.
          Saya hubungkan ke agent Outlet Tebet agar jelas ya. Mohon tunggu sebentar.
```

### Skenario I — API down / timeout

```
(fetch timeout / 500)
Bot:      Mohon maaf Kak, sistem kami butuh waktu lebih lama dari biasanya.
          Saya hubungkan ke agent agar bisa dibantu lebih cepat.
          Mohon tunggu sebentar ya.
```

---

## Edge Cases

| # | Skenario | Perilaku Bot |
|---|----------|--------------|
| 1 | Customer kirim ID dengan spasi / typo (`TRX 9812 377`) | Normalisasi (strip spasi & dash), fetch. |
| 2 | ID pakai prefix `Nota` (`Nota 10239`) | Regex tangkap → fetch. |
| 3 | ID pakai prefix `INV` (`INV-10239`) | Regex tangkap → fetch. **[DRAFT]** mapping `INV-*` → `idtransaksi` perlu konfirmasi ke tim backend. |
| 4 | ID terlalu pendek (`TRX1`) | Tanya ulang: "ID-nya kelihatannya kurang lengkap, bisa dicek lagi?" |
| 5 | ID milik outlet lain (fetched.nama_outlet != outlet customer) | Tampilkan data tetap (karena outlet scope bot = outlet customer), tapi tambahkan catatan: "Transaksi ini tercatat di outlet [X], beda dengan outlet yang sedang dipilih Kakak." **[DRAFT]** atau auto-escalate. Lebih aman: tampil info + escalate. |
| 6 | Customer punya 2+ transaksi di outlet ini | Bot hanya fetch ID yang dikirim. Tidak auto-listing semua transaksi. |
| 7 | Customer minta "detail" berkali-kali | Tampilkan detail 1×. Berikutnya: "Detail sudah saya tampilkan di atas ya Kak." |
| 8 | Transaksi `status_hapus = 1` | Bot: "Transaksi sudah dihapus dari sistem." + escalate. |
| 9 | Status `Problem` | Auto-escalate (lihat Mode Selection). |
| 10 | Refund `status = pending` | Auto-escalate setelah tampil ringkas. |
| 11 | Pengantaran gagal / `pengantaran_extra` error | Auto-escalate. |
| 12 | Customer bilang "batal" setelah dapat ringkas | Reset slot-filling state, kembali ke menu. "Baik Kak, dibatalkan. Ada lagi yang bisa dibantu?" |
| 13 | Customer bilang "batal" sebelum kirim ID | Sama, tapi tidak ada state yang perlu di-reset. |
| 14 | Customer kirim ID + langsung "detail" dalam 1 pesan | Mode = detail (intent=check_laundry_status dengan mode=detail). **[DRAFT]** implementasi: split message sebelum classify, atau LLM terima dua intent — perlu design. |
| 15 | `lunas=false` tapi transaksi sudah selesai & diambil | Tampil "Selesai, sudah diambil" tanpa info bayar (lihat [main.md](../docs/main.md) 9.8: payment detail JANGAN). |
| 16 | `total = 0` (mungkin transaksi sample) | Tampil normal, abaikan total. |
| 17 | Customer pakai bahasa campur / kode | LLM handles; intent classification robust. |
| 18 | Customer follow-up "kapan?" setelah dapat ringkas | Tetap dianggap `check_laundry_status` (sudah ada state.last_id_transaksi), tampilkan ulang ringkas atau info tambahan jika relevan. **[DRAFT]** — apakah follow-up generic di-respond atau auto-redirect. |
| 19 | `tanggal_selesai` null / masih proses | Tampilkan "Estimasi: [tanggal_selesai_format]" (jika ada) atau "Belum ada estimasi, silakan cek lagi." **[DRAFT]** fallback phrasing. |
| 20 | Foto transaksi exists (`transaksi_foto` / `bukti_ambil_foto` ada) | TIDAK ditampilkan otomatis (per [main.md](../docs/main.md) 9.8). Hanya escalate jika customer minta foto. |
| 21 | Conversation stale > 5 menit | Reset state; ID berikut dianggap flow baru. **[DRAFT]** — atau pertahankan state selamanya dalam 1 sesi WABA? Default: reset per "session" conversation (24 jam?). |
| 22 | Customer sudah escalate, lalu chat lagi di conversation yang sama | Bot tidak intervensi (talk_to_agent sudah handle). |

---

## Acceptance Criteria (draft)

### Functional

- [ ] LLM bisa klasifikasi `check_laundry_status` dari minimal 8 variasi input natural (lihat [main.md](../docs/main.md) 9.3).
- [ ] Bot bisa ekstrak ID transaksi dari pola `TRX*`, `INV*`, `Nota *`, atau numeric ≥8 digit.
- [ ] Bot normalisasi ID (uppercase, strip spasi/dash) sebelum fetch.
- [ ] Bot fetch `GET /masterData/meta/detail_data_transaksi?idtransaksi={id}` dengan JWT outlet.
- [ ] Bot tampilkan **wajib** per [main.md](../docs/main.md) 9.6: ID, outlet, nama, status (label ramah), progress, tgl diterima, estimasi selesai, arahan.
- [ ] Bot **JANGAN** tampilkan 13 field internal per [main.md](../docs/main.md) 9.8.
- [ ] Customer bisa request "detail" → bot tampilkan layanan + status ambil + pengantaran.
- [ ] Bot retry 2× untuk ID not found, escalate pada retry ke-3.

### Bot Behavior

- [ ] Mode default = Ringkas (≤ 8 baris).
- [ ] Mode Detail hanya via trigger natural "detail / info lengkap / lebih lengkap".
- [ ] Mode Eskalasi otomatis untuk 7 kondisi (refund, hapus, komplain, barang hilang/rusak, data tidak cocok, status bermasalah, pengantaran gagal).
- [ ] Bahasa: Indonesia, tone "Kak", WhatsApp-friendly (line break bukan bullet untuk hal kritis).
- [ ] Format tanggal customer-friendly: "DD MMMM YYYY" (contoh "24 Juni 2026"), sertakan jam jika relevan ("25 Juni 2026 pukul 17.00").
- [ ] Label status customer-friendly (mirror [main.md](../docs/main.md) 9.10 / [bot-behavior.md](../docs/bot-behavior.md)).
- [ ] Arahan berikutnya muncul sesuai tabel [main.md](../docs/main.md) 9.11.

### Performance

- [ ] Fetch API timeout: **10 detik** (DRAFT — perlu tuning, eksperimen rata-rata fetch ~ 2-3 detik).
- [ ] Total response time (intent classify + fetch + render) target **p95 ≤ 8 detik**.
- [ ] Tidak ada double-fetch untuk 1 ID dalam 1 conversation window (cache).
- [ ] Bot state stale cleanup: state > 5 menit di-reset.

### Security

- [ ] JWT bot tidak boleh bocor ke customer (log internal hanya).
- [ ] ID transaksi di-log dengan masking parsial (3 char terakhir, lihat Open Q).
- [ ] Tidak ada PII tambahan di response (no HP customer, no alamat lengkap, no email).
- [ ] Rate limit per nomor WA: maks 5 fetch / 5 menit (anti-abuse).
- [ ] Audit log: timestamp, outlet_id, intent, id_transaksi (masked), outcome (200/404/timeout/escalated).

---

## Implikasi ke Existing

### Control Panel

- Tidak ada perubahan UI Control Panel dari file ini. Pengaturan `outlet_intent_settings.check_laundry_status` sudah ada dan default `true`. **[DRAFT]** verifikasi di PRD Control Panel bahwa toggle OFF → bot skip intent + fallback (sudah dibahas di [brainstorm control-panel.md](2026-07-01-control-panel.md)).
- Mungkin tambah display "last used" timestamp untuk audit demak. **[DRAFT]** non-goal MVP.

### Master Switch

- Tetap patuh hierarki: master OFF → skip `check_laundry_status`. Master ON + per-outlet OFF untuk intent ini → skip + fallback. Tidak ada perubahan baru.

### Conversation Flow

- Tambah state lokal: `laundry_context = { last_id_transaksi, mode, retry_count }`. **[DRAFT]** apakah disimpan di session storage, DB, atau hanya di memory.
- Tambah timer: stale > 5 menit → state cleared. **[DRAFT]**

### Response Template Registry

- Tambah 4 template: `laundry.ringkas`, `laundry.detail`, `laundry.not_found_retry`, `laundry.escalate_sensitive_singular`.
- Lokalisasi: nama hari/bulan Indonesia (lun untuk naming function).

### Logging & Observability

- Tambah event `laundry_status.fetched` dengan field: outlet_id, id_transaksi (masked), http_status, mode, escalated_reason (nullable).
- Tambah counter `laundry_status.escalated_total{reason}` untuk metrik.

### Docs

- [intents.md](../docs/intents.md) — sudah punya entry. Tidak ada perubahan.
- [main.md](../docs/main.md) Section 9 — sudah ada. Mungkin tambahkan referensi ke brainstorm ini.
- [bot-behavior.md](../docs/bot-behavior.md) — mirror, tidak ada perubahan.

---

## Open Questions

> Setiap pertanyaan punya **default/asumsi** untuk MVP. **[ ]** = perlu konfirmasi product sebelum PRD final.

1. **[ ]** Mapping `INV-*` ke `idtransaksi` — apakah prefix `INV-10239` selalu map ke internal ID `TRX9812377123`?
   *Default*: regex ambil trailing numeric, fetch → kalau 404, tanya ulang.
2. **[ ]** Fetch mana yang benar jika `idtransaksi` punya prefix `INV`? Apakah endpoint hanya menerima `TRX*` atau semua?
   *Default*: endpoint menerima numeric saja — prefix di-strip sebelum fetch.
3. **Apakah `total` boleh ditampilkan?**
   *Decision*: **tampilkan "Total: Rp X" tanpa breakdown metode bayar** (final). Override default [main.md](../docs/main.md) 9.8. Risiko follow-up payment tetap di luar scope MVP (bot hanya acknowledge, escalate jika customer tanya detail bayar).
4. **[ ]** Bagaimana handle ID milik outlet lain?
   *Default*: tampilkan catatan "Terdaftar di outlet [X]" + auto-escalate. **[DRAFT]** alternatif: silent redirect ke agent.
5. **Berapa retry ID not found?**
   *Decision*: **2× retry** (final).
6. **[ ]** Masking ID untuk log: berapa char?
   *Default*: tampil 4 char terakhir, sensor sisanya (`*******9812`). Tapi untuk audit perlu full ID dengan permission.
7. **[ ]** Batas waktu fetch API?
   *Default*: 10 detik.
8. **[ ]** Cache fetched data berapa lama?
   *Default*: cache 60 detik untuk 1 ID dalam 1 conversation window (anti duplicate-fetch).
9. **[ ]** Auto-escalate jika `lunas=false` + umur > X hari, perlu?
   *Default*: tidak. Logika `lunas` tidak masuk MVP karena payment detail eksplisit di luar scope. **[DRAFT]** usul di Mode Selection tree di atas.
10. **[ ]** Format tanggal fallback untuk `tanggal_selesai` null?
    *Default*: "Belum ada estimasi, Kak. Tim outlet akan update setelah diproses."
11. **[ ]** Foto transaksi & bukti ambil — apakah customer boleh request ("kirim fotonya")?
    *Default*: tidak (per [main.md](../docs/main.md) 9.8). Jika customer minta → escalate.
12. **[ ]** Apakah customer bisa request `check_laundry_status` tanpa pilih outlet di awal (mis. chat langsung ke nomor WABA)?
    *Default*: harus sudah pilih outlet. Jika tidak → bot tampilkan prompt pilih outlet.
13. **Apakah 7 kondisi eskalasi hardcode atau configurable per outlet?**
    *Decision*: **hardcode di MVP**, configurable di fase 2.
14. **[ ]** Apakah bot perlu follow-up otomatis ("ada lagi yang bisa dibantu?") setelah tampil ringkas?
    *Default*: tidak. Customer bisa ketik apa pun.

---

## Out of Scope (Future)

Eksplisit **TIDAK** masuk MVP `check_laundry_status`:

- **Lookup via nomor HP** (per [open-questions.md](../docs/open-questions.md) #1).
- **Tampilkan status pembayaran lengkap** (lunas + breakdown) (per [main.md](../docs/main.md) 9.8 + [open-questions.md](../docs/open-questions.md) #2/#3).
- **Tampilkan alamat pengantaran lengkap** (masking sesuai policy PII — per [open-questions.md](../docs/open-questions.md) #4).
- **Tampilkan foto transaksi / bukti ambil** otomatis.
- **Notifikasi push** (mis. "laundry sudah siap, bot akan chat otomatis").
- **Multi-language** (English / bilingual) — MVP Indonesia only.
- **Edit transaksi** (bot read-only).
- **Buat transaksi baru** via bot.
- **Refund processing**.
- **Konfigurasi per outlet** untuk 7 kondisi eskalasi (hardcode MVP).
- **Rate limiting per outlet** (cukup per WA number).
- **Voice note handling** — untuk MVP diasumsikan text only.

---

## Next Step

Setelah brainstorm ini disetujui product, berikut rekomendasi deliverables (siap ditulis tanpa eksplorasi ulang):

### Segera bisa ditulis (1-2 hari)

1. **PRD [prd/P1-check_laundry_status.md](../prd/P1-check_laundry_status.md)** — ambil dari [main.md](../docs/main.md) Section 9 + hasil keputusan di file ini. Jadikan baseline resmi. Tandai hal yang masih **[DRAFT]** sebagai TODO.
2. **GWT [gwt/P1-check_laundry_status.md](../gwt/P1-check_laundry_status.md)** — konversi setiap Use Case + Edge Case ke Given/When/Then. Minimal 25 skenario.
3. **API contract review** — konfirmasi ke tim backend:
   - Apakah `idtransaksi` menerima prefix apa pun atau numeric only?
   - Status code 404 untuk "tidak ditemukan" atau selalu 200 dengan `null`? (saat ini asumsi 404, perlu konfirmasi.)
   - Apakah endpoint mengembalikan foto sebagai URL atau base64?
   - Rate limit dari sisi server?

### Setelah PRD approved

4. **Response template spec** — formalisasi 4 template ke file `docs/templates-laundry.md` dengan placeholder syntax.
5. **Logging spec** — list event + field wajib + masking rule.
6. **Conversation state spec** — TTL, cleanup, retry policy.
7. **Localization file** — daftar label customer-friendly per status (mirror [bot-behavior.md](../docs/bot-behavior.md), validasi ulang oleh copywriting).

### Setelah dev start

8. **Test data fixture** — minimal 10 sample `idtransaksi` dari staging yang mencakup: semua status, status_hapus=1, refund pending, multi-outlet, normal proses, siap diambil, sudah diambil, dibatalkan.
9. **QA checklist** — inherit dari acceptance criteria + 22 edge cases di atas.
10. **Live monitoring dashboard** — counter per endpoint (200/404/500/timeout) + counter escalate per reason.

### Cross-reference

- [Brainstorm 2026-07-01-master-switch.md](2026-07-01-master-switch.md) → control hierarki.
- [Brainstorm 2026-07-01-control-panel.md](2026-07-01-control-panel.md) → toggle per-outlet per-intent.
- [Docs main.md](../docs/main.md) Section 9 → product spec source.
- [Docs open-questions.md](../docs/open-questions.md) → 4 pertanyaan relevan (HP lookup, payment, masking, caching).