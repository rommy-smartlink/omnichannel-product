# Bot Control Panel — PRD

## Overview

Dashboard bagi owner laundry untuk mengontrol bot di level WABA dan per outlet. Owner bisa:

1. **Master switch per-WABA**: ON/OFF bot untuk semua outlet di WABA sekaligus (untuk maintenance atau emergency).
2. **Per-outlet intent toggle**: enable/disable masing-masing intent (misal `check_laundry_status`, `check_ticket_status`, dst) untuk outlet tertentu, tanpa bulk action.

Tujuan: owner punya kontrol granular kapan dan di outlet mana fitur bot hidup, sambil punya jalur cepat (master switch) untuk keadaan darurat atau maintenance window.

---

## User Stories

- As an **owner**, I want to **toggle master switch ON/OFF for my entire WABA** so that I can **quickly stop or resume bot during maintenance or emergency**.
- As an **owner**, I want to **toggle ON/OFF specific intent per outlet** so that I can **enable features only for outlets that are ready to support them**.
- As an **owner**, I want to **see all my outlets and their current bot settings in one view** so that I can **quickly understand the current configuration**.

---

## Goals & Non-Goals

### Goals

- Master switch ON/OFF per-WABA (semua outlet sekaligus)
  - Saat OFF: **konfigurasi intent per-outlet tetap tersimpan** (tidak di-reset atau dimodifikasi), hanya saja bot di-skip untuk semua outlet di WABA tersebut. Customer chat → langsung ke agent (silent handoff).
  - Saat ON: konfigurasi per-outlet berlaku normal.
- ON/OFF per intent, per outlet
- Akses terbatas ke owner saja (bukan outlet manager)
- Default state: master switch ON saat WABA baru dibuat
- Default state: semua intent ON saat outlet baru dibuat
- Jika master switch OFF di WABA, semua outlet di WABA tersebut dianggap bot-nya off (skip greeting + skip classification, silent handoff ke agent, sama seperti outlet yang hanya `talk_to_agent` aktif).
- Jika di satu outlet hanya `talk_to_agent` yang aktif (semua 6 intent configurable OFF, tapi master ON), outlet dianggap bot tidak aktif — bot tidak kirim greeting, tidak tampilkan menu, langsung silent handoff ke agent.

### Non-Goals (MVP)

- Bulk action (toggle multiple outlet sekaligus dalam satu aksi)
- Schedule / auto-toggle berdasarkan jam buka
- Maintenance mode dengan custom message (master switch OFF = silent handoff ke agent, tanpa pesan tambahan)
- Role outlet manager / kasir
- Statistik / analytics (chat count, intent distribution, escalation rate)
- Permission level selain owner
- Notifikasi ke owner lain kalau setting di-toggle
- Konfirmasi dialog sebelum toggle master switch (langsung toggle, reversible)

---

## Affected Intents

6 intent configurable, 2 locked:

| Intent | Toggleable |
|--------|------------|
| `check_laundry_status` | ✓ |
| `check_ticket_status` | ✓ |
| `get_outlet_services` | ✓ |
| `get_operating_hours` | ✓ |
| `get_active_promos` | ✓ |
| `get_member_info` | ✓ |
| `talk_to_agent` | ✗ — always ON (bot harus bisa eskalasi) |
| `unknown` | ✗ — internal, not configurable |

`talk_to_agent` selalu ON karena bot harus selalu bisa escalate. Tanpa kemampuan escalate, customer stuck.

---

## Data Model

```sql
-- waba_bot_settings (master switch per WABA)
waba_id        uuid NOT NULL REFERENCES wabas(id) ON DELETE CASCADE
is_enabled     boolean NOT NULL DEFAULT true
updated_by     uuid REFERENCES users(id)
updated_at     timestamptz NOT NULL DEFAULT now()

PRIMARY KEY (waba_id)

-- outlet_intent_settings (satu row per (outlet, intent))
outlet_id         uuid NOT NULL REFERENCES outlets(id) ON DELETE CASCADE
intent_name       text NOT NULL
is_enabled        boolean NOT NULL DEFAULT true
updated_by        uuid REFERENCES users(id)
updated_at        timestamptz NOT NULL DEFAULT now()

PRIMARY KEY (outlet_id, intent_name)

-- Index untuk lookup cepat saat conversation start
CREATE INDEX idx_outlet_intent_settings_outlet ON outlet_intent_settings(outlet_id);
```

**Default saat WABA baru dibuat**: `waba_bot_settings.is_enabled = true`.

**Default saat outlet baru dibuat**: trigger / app-level insert 6 intent configurable dengan `is_enabled = true`.

> Catatan: outlet yang ditampilkan di control panel hanya yang **terdaftar di omnichannel**. Owner bisa punya puluhan outlet fisik, tapi cuma yang subscribe omnichannel yang muncul dan bisa diatur di sini.

---

## Bot Flow Integration

Saat customer mulai percakapan di outlet X (X.waba_id = W):

```
1. Customer send message ke outlet X
2. Bot load settings:
   a. waba_bot_settings WHERE waba_id = W → master_switch_state
   b. outlet_intent_settings WHERE outlet_id = X → per_intent_state
3. Decision:
   a. master_switch_state = OFF → skip greeting + skip classification → langsung escalate ke agent
   b. master_switch_state = ON → lanjut ke logic per-outlet (step 4)
4. Per-outlet logic (master ON):
   - Ada enabled intent match → handle normal
   - Intent match tapi OFF → fallback message + skip
   - Semua configurable intent OFF → outlet dianggap bot tidak aktif, silent handoff ke agent (tanpa greeting, tanpa menu)
   - Intent `talk_to_agent` selalu match regardless of settings
5. Customer bisa kasih command override ("menu", "agent") regardless of intent settings
```

**Override priority**: `master_switch OFF > per-outlet intent OFF > per-outlet intent ON`. Master switch selalu menang.

### Fallback message ketika intent OFF

```
Bot: "Maaf, fitur ini sedang tidak tersedia di outlet ini.
      Ketik 'hubungi agen' untuk berbicara dengan customer service kami."
```

---

## UI / UX

### Owner Dashboard Layout

```
┌─────────────────────────────────────────────────────────┐
│  🤖 Bot Control Panel                       Owner       │
├─────────────────────────────────────────────────────────┤
│  WABA: [ WABA-1 (Utama) ▾ ]                              │
├─────────────────────────────────────────────────────────┤
│  Status Bot:  [ ●──── ON  ]   Last updated: 2 jam lalu  │
│  ℹ️  Saat OFF, semua outlet di WABA langsung ke agent   │
├─────────────────────────────────────────────────────────┤
│  Search: [____________________]  Sort: [Nama A-Z ▾]     │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ┌─ Outlet A (Jakarta Pusat) ──────────────────────┐  │
│  │  Cek status laundry         ON   · updated 2 hari│  │
│  │  Cek status tiket           ON   · updated 5 hari│  │
│  │  Layanan outlet              ON   · updated 1 hari│ │
│  │  ...                          ›                  │  │
│  └──────────────────────────────────────────────────┘  │
│                                                         │
│  ┌─ Outlet B (Bandung) ─────────────────────────────┐  │
│  │  Cek status laundry         OFF  · updated 1 hari │  │
│  │  Cek status tiket           OFF  · updated 1 hari │  │
│  │  ...                          ›                  │  │
│  └──────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────┘
```

### Master Switch

- Lokasi: **header atas** Control Panel, sebelum search/sort.
- Toggle langsung (tanpa modal konfirmasi, reversible).
- Saat ON: visual normal (warna hijau / icon check).
- Saat OFF: visual warning (warna merah / icon alert), banner info **"Bot nonaktif — semua outlet langsung ke agent"** muncul.
- Last updated timestamp + siapa yang toggle ditampilkan di sebelah switch.
- Default state: ON saat WABA baru dibuat.

### Sort

- Nama A-Z (default, ascending)
- Nama Z-A (descending)
- Terakhir diubah (updated_at, paling baru di atas)

### Interaction

**List outlet adalah read-only.** Owner tidak bisa toggle langsung dari card.

Saat owner **tap/click pada card outlet**, muncul **modal** yang berisi:

- Nama outlet + kota (header modal)
- Daftar 6 intent configurable dengan status ON/OFF masing-masing + timestamp kapan terakhir diubah (`updated_at`)
- Toggle switch per intent (langsung aktif di modal, belum tersimpan)
- `talk_to_agent` ditampilkan sebagai toggle switch dalam state **disabled/readonly** (visible tapi tidak bisa di-interaksi aktif)
- Saat owner tap/click toggle `talk_to_agent` yang readonly: muncul **toast info** dengan teks **"Hubungi agen selalu aktif agar customer selalu bisa terhubung dengan tim kami."**, auto-dismiss dalam 3-5 detik, tidak ada efek di DB, modal tetap terbuka
- Tombol **Simpan** (primary, kanan bawah) dan **Batal** (secondary, kiri bawah)

```
┌─────────────────────────────────────────────┐
│  Outlet A — Jakarta Pusat              ✕   │
├─────────────────────────────────────────────┤
│                                             │
│  Cek status laundry      [ ●──── ON  ]     │
│  Cek status tiket        [ ●──── ON  ]     │
│  Layanan outlet         [ ●──── ON  ]     │
│  Jam operasional         [ ○──── OFF ]     │
│  Promo aktif             [ ●──── ON  ]     │
│  Info member             [ ●──── ON  ]     │
│  Hubungi agen            [ ●──── ON ] 🔒   │
│                                             │
├─────────────────────────────────────────────┤
│              [ Batal ]      [ Simpan ]      │
└─────────────────────────────────────────────┘
```

### PATCH Payload Format

Saat owner klik **Simpan** di modal edit outlet, client kirim PATCH request dengan format:

```json
{
  "outlet_id": "uuid",
  "intents": [
    { "intent_name": "check_laundry_status", "is_enabled": false },
    { "intent_name": "get_active_promos", "is_enabled": true }
  ]
}
```

- Payload hanya berisi **intent yang berubah state** (delta). Intent yang tidak diubah tidak perlu dikirim.
- `talk_to_agent` tidak boleh ada di payload — server ignore jika dikirim.
- Server update semua row yang ada di payload secara **atomic** dalam satu transaksi.
- Response: `200 OK` + updated rows dengan `updated_at` terbaru, atau `4xx/5xx` jika gagal.

### Interaction Flow

1. Owner tap card → modal buka dengan state settings outlet saat ini
2. Owner toggle beberapa intent (perubahan hanya di state lokal, belum PATCH)
3. Owner klik **Simpan** → PATCH endpoint dengan payload perubahan
4. Owner klik **Batal** → tutup modal, perubahan lokal di-discard
5. Sukses → toast "Tersimpan", modal tutup, list refresh
6. Error → toast error, modal tetap terbuka dengan perubahan lokal tetap ada (owner bisa retry Simpan)

### Empty States

Tidak ada empty state — menu Control Panel hanya muncul jika owner sudah punya outlet yang subscribe omnichannel. Sebaliknya, menu ini tidak ditampilkan sama sekali di sidebar/navigation.

---

## Permissions

| Role | Akses |
|------|-------|
| **Owner** | View + toggle master switch WABA + toggle per-outlet intent untuk outlet miliknya yang terdaftar di omnichannel |
| **Outlet manager** | Tidak ada akses (non-goal MVP) |
| **Customer** | Tidak ada akses |

- Verify ownership:
  - Master switch: `wabas.owner_id = current_user.id`
  - Per-outlet: `outlets.owner_id = current_user.id` AND `outlets.is_omnichannel_enabled = true`
- Server-side check: semua API endpoint validate owner + filter outlet terdaftar

---

## Edge Cases

| Scenario | Behavior |
|----------|----------|
| Outlet baru, belum ada settings | Default semua intent = ON (auto-create row) |
| Owner toggle saat bot sedang proses chat | Settings baru apply di request berikutnya (no mid-flight change) |
| Settings conflict (intent OFF tapi customer kasih command manual "menu") | Command manual selalu dihormati (override toggle) |
| Race condition: 2 device toggle simultaneously | Last-write-wins + display latest updated_at |
| Owner hapus outlet | Cascade delete settings row |
| Intent OFF, tapi customer sudah masuk flow slot-filling | Flow lanjut normal sampai selesai (settings apply di request baru) |
| Bot restart / deploy saat user toggle | State persisted di DB, reload on startup — tidak ada in-memory cache yang perlu di-invalidate |
| `talk_to_agent` toggle request dari UI | Block — tampil sebagai info lock "Selalu aktif", tidak bisa di-interaksi |
| Bulk request (race condition dari 2 outlet) | Per-outlet independent, no cross-coupling |
| Owner punya outlet tidak terdaftar omnichannel | Tidak muncul di control panel |
| Master switch OFF, customer chat | Conversation langsung di-eskalasi ke agent, skip greeting + classification |
| Master switch OFF, customer sedang dalam flow bot | Flow selesai normal, request berikutnya akan skip bot |
| Master switch OFF, konfigurasi per-outlet | **Tetap tersimpan utuh** — `outlet_intent_settings` tidak dimodifikasi saat master OFF. Saat master ON lagi, konfigurasi per-outlet berlaku normal tanpa perlu setting ulang. |
| Master switch OFF, agent juga tidak tersedia | Customer terima fallback platform default (di luar scope MVP ini) |
| Master switch toggle saat bot proses chat | Apply di request customer berikutnya |
| 2 device toggle master switch bersamaan | Last-write-wins + display latest updated_at |
| Owner lupa switch ON setelah maintenance | Tidak ada auto-revert — owner bertanggung jawab |
| WABA baru aktivasi | Default master switch ON |
| WABA delete (deaktivasi) | Cascade delete `waba_bot_settings` row |

---

## Acceptance Criteria

### Functional

- [ ] Owner bisa lihat WABA selector di paling atas Control Panel
- [ ] Owner bisa ganti WABA via WABA selector, semua state Control Panel re-load sesuai WABA yang dipilih
- [ ] Master switch state independen per WABA (toggle di WABA-A tidak ubah WABA-B)
- [ ] Owner bisa lihat master switch WABA di header Control Panel
- [ ] Owner bisa toggle master switch ON/OFF langsung dari header (tanpa modal konfirmasi)
- [ ] Saat master switch ON, indicator normal; saat OFF, indicator warning + banner info "Bot nonaktif — semua outlet langsung ke agent"
- [ ] Default state WABA baru: master switch ON
- [ ] Owner bisa lihat semua outlet miliknya yang terdaftar omnichannel di control panel (filtered by WABA yang dipilih)
- [ ] Owner bisa search outlet berdasarkan nama / kode / kota
- [ ] Owner bisa sort outlet (alfabet / updated / status)
- [ ] Owner bisa toggle ON/OFF untuk 6 intent configurable
- [ ] PATCH payload berisi hanya intent yang berubah (delta), server atomic per outlet
- [ ] Saat tap toggle readonly "Hubungi agen", muncul toast info dan auto-dismiss 3-5 detik
- [ ] `talk_to_agent` tampil sebagai info lock "Selalu aktif", tidak bisa di-interaksi
- [ ] Card outlet read-only, tap untuk buka modal edit
- [ ] Modal edit punya tombol Simpan (PATCH) dan Batal (discard perubahan lokal)
- [ ] Sort mendukung Nama A-Z, Nama Z-A, Terakhir diubah
- [ ] Default state outlet baru terdaftar: semua intent ON
- [ ] Owner tidak bisa akses outlet bukan miliknya (server-side validation)
- [ ] Outlet tidak terdaftar omnichannel tidak muncul di control panel

### Bot Behavior

- [ ] Master switch OFF → semua conversation di WABA langsung di-eskalasi ke agent (skip greeting + classification)
- [ ] Master switch OFF → konfigurasi `outlet_intent_settings` **tetap tersimpan utuh**, tidak dimodifikasi atau di-reset
- [ ] Master switch OFF lalu di-toggle ON lagi → konfigurasi per-outlet berlaku normal tanpa perlu setting ulang
- [ ] Master switch ON + per-outlet intent ON → bot berjalan normal
- [ ] Master switch ON + per-outlet intent OFF → fallback message (bukan escalate otomatis, kecuali semua OFF)
- [ ] Master switch ON + semua per-outlet configurable intent OFF → outlet dianggap bot tidak aktif, bot tidak kirim greeting/menu, conversation langsung di-eskalasi ke agent (silent handoff)
- [ ] Override priority: master switch OFF selalu menang dari per-outlet setting apapun
- [ ] `talk_to_agent` selalu tersedia regardless of settings
- [ ] Manual command ("menu", "agent", "batal") selalu work regardless of settings
- [ ] Toggle baru (master dan per-outlet) apply di request customer berikutnya, bukan mid-flight
- [ ] Invalid slot handling: bahas per intent (di luar scope PRD ini)

### Performance

- [ ] Settings load di conversation start: < 100ms (p99)
- [ ] DB query menggunakan index `idx_outlet_intent_settings_outlet`
- [ ] Toggle UI update: < 500ms (p95) setelah API response

### Security

- [ ] Endpoint toggle: require auth + ownership check
- [ ] Tidak ada endpoint yang bypass ownership validation

---

## Multi-WABA & WABA Selector

Owner dapat memiliki lebih dari satu WABA. Control Panel di-scope per-WABA melalui WABA selector.

**Behavior**:

- WABA selector berada di paling atas Control Panel (sebelum master switch, search, dan sort).
- Saat owner ganti pilihan WABA, semua state Control Panel (master switch, banner info, daftar outlet, sort) **re-load** sesuai WABA yang dipilih — independent per WABA.
- Master switch state tersimpan **independen per WABA**: toggle di WABA-A tidak mengubah state WABA-B.
- Outlet yang ditampilkan di list hanya outlet yang **(a) milik owner saat ini** AND **(b) `is_omnichannel_enabled = true`** AND **(c) terkait WABA yang sedang dipilih via selector**.
- Default WABA yang dipilih saat membuka Control Panel: WABA yang pertama kali diaktivasi (atau sesuai konfigurasi "default WABA" owner jika ada — di luar scope MVP ini, pilih WABA pertama di list).

**Edge case**:

- Owner dengan 1 WABA: WABA selector tetap tampil (bukan hidden) untuk konsistensi UI — namun hanya menampilkan 1 pilihan.
- Owner yang belum punya WABA: menu Control Panel tidak muncul (sesuai empty state PRD).

---

## Out of Scope (Future)

- Per-intent `maintenance_mode` dengan custom message
- Schedule (auto-toggle jam tertentu)
- Bulk action (multiple outlet sekaligus)
- Outlet manager role
- Analytics dashboard
- A/B testing per intent
- Per-intent allowed message templates

---

## Open Questions

1. Apakah master switch OFF juga mematikan greeting + quick reply automation, atau cuma intent classification? — **Belum** (saat OFF: skip greeting + skip classification → langsung escalate)
2. Apakah customer perlu terima pesan "Bot sedang maintenance" sebelum di-eskalasi, atau silent handoff? — **Belum** (default MVP: silent handoff ke agent)
3. Apakah perlu konfirmasi dialog saat toggle master switch OFF? — **Belum** (default MVP: tanpa konfirmasi, reversible)
4. Apakah customer masih bisa kirim pesan saat master switch OFF? — **Belum** (default: ya, langsung ke agent)
5. Bagaimana cara owner mendaftarkan outlet ke omnichannel? — **Diluar scope PRD ini**