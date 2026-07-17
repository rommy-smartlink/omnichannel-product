# Control Panel — GWT Scenarios

> Generated dari PRD P8 — Control Panel untuk Coda row US-Co-135 (Fitur: Control panel, Epic: AI Bot chat).
> Pattern mengikuti table Scenario di page "Detail User Stories": setiap skenario = 1 row, field Given/When/Then plain text.
> Kode SC auto-generate dari formula di Coda (`"SC-"+RowId(thisRow)`) — tidak perlu ditulis manual.

---

## Master Switch (per-WABA)

**Goals**: Owner dapat mengaktifkan atau menonaktifkan bot untuk WABA yang sedang dipilih melalui satu toggle, dengan konfigurasi per-outlet tetap tersimpan saat bot di-off-kan.

> **Sebagai Owner,**
> saya ingin toggle master switch ON/OFF untuk WABA yang sedang saya pilih,
> sehingga saya bisa menghentikan bot dengan cepat saat maintenance atau emergency, tanpa kehilangan konfigurasi per-outlet yang sudah saya atur.

**Detail**:
1. Master switch berada di header atas Control Panel, sebelum search/sort. Scope: WABA yang dipilih via WABA selector (pre-existing).
2. Saat master switch OFF, bot skip greeting + classification untuk semua outlet di WABA, customer langsung di-eskalasi ke agent (silent handoff).
3. Konfigurasi per-outlet **tetap tersimpan utuh** saat master OFF — tidak di-reset atau dimodifikasi.
4. Saat master switch di-toggle ON lagi, konfigurasi per-outlet berlaku normal tanpa perlu setting ulang.
5. Override priority: master switch OFF > per-outlet intent OFF > per-outlet intent ON.
6. Toggle master switch menampilkan modal konfirmasi sebelum state berubah. Konfirmasi menjelaskan bahwa mematikan bot akan menghentikan percakapan bot yang sedang berlangsung dan mengalihkan customer ke agent. Owner pilih "Konfirmasi" untuk menyimpan atau "Batal" untuk membatalkan.
7. Default state WABA baru: master switch ON.
8. Saat master switch diubah menjadi OFF, percakapan bot yang sedang berlangsung langsung dihentikan dan customer diarahkan ke agent pada respons berikutnya. Aturan ini tidak berlaku jika customer sudah berada di sesi chat dengan agent.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Owner baru diaktivasi WABA-nya (master switch default ON), lalu membuka Control Panel dan memilih WABA tersebut via WABA selector. | Owner melihat header Control Panel. | Owner melihat master switch di posisi ON dengan indicator normal. Banner info "Saat OFF, semua outlet di WABA langsung ke agent" tampil di bawah switch. |
| SC Utama | Owner di Control Panel, master switch sedang ON, ada 5 outlet di WABA dengan konfigurasi intent per-outlet masing-masing. | Owner tap toggle master switch dari ON ke OFF. | Master switch berubah ke OFF. Indicator visual berubah ke warna merah / icon warning. Banner info tambahan "Bot nonaktif — semua outlet langsung ke agent" muncul. PATCH ke server sukses dengan timestamp siapa & kapan berubah. Konfigurasi per-outlet untuk semua outlet tetap utuh (tidak dimodifikasi). |
| SC Utama | Master switch WABA sedang OFF (semua outlet di WABA bot-nya off, konfigurasi per-outlet tetap tersimpan). Customer baru mengirim pesan pertama ke salah satu outlet di WABA. | Customer chat ke outlet manapun di WABA. | Bot skip greeting + skip classification. Conversation langsung di-eskalasi ke agent outlet terkait. Customer tidak terima response bot apapun (silent handoff). |
| SC Utama | Master switch WABA OFF, outlet A memiliki konfigurasi "Cek status laundry" OFF dan "Layanan outlet" ON di pengaturan per-outlet. | Owner toggle master switch WABA dari OFF kembali ke ON. | Master switch berubah ke ON dengan indicator normal. Owner membuka modal edit outlet A — toggle "Cek status laundry" masih OFF dan "Layanan outlet" masih ON sesuai konfigurasi sebelum master OFF (konfigurasi per-outlet tetap tersimpan, tidak berubah). |
| SC Utama | Master switch WABA OFF, customer sedang dalam flow "Cek status laundry" (sebelum master di-off-kan). | Owner toggle master switch OFF, lalu konfirmasi di modal. | Modal konfirmasi tampil menjelaskan dampak penghentian chat. Setelah owner pilih "Konfirmasi", percakapan bot customer yang sedang berlangsung langsung dihentikan dan customer diarahkan ke agent pada respons berikutnya. Request berikutnya langsung di-eskalasi ke agent. |
| SC Pendukung | Master switch WABA OFF, customer sedang dalam flow "Cek status laundry". | Owner tap toggle OFF lalu pilih "Batal" di modal konfirmasi. | Toggle kembali ke ON (tidak ada perubahan state). Percakapan bot customer tetap berjalan normal. |
| SC Pendukung | Master switch WABA OFF saat customer sudah berada di sesi chat dengan agent (handoff sudah terjadi). | Owner toggle master switch OFF, lalu konfirmasi. | Toggle berubah ke OFF, namun aturan penghentian chat tidak berlaku karena tidak ada sesi bot aktif. Customer tetap di sesi agent. |
| SC Pendukung | Master switch WABA OFF. | Owner toggle master switch ke ON. | Toggle master switch ke ON, indicator kembali normal, banner info OFF hilang. Konfigurasi per-outlet tetap utuh dan berlaku normal untuk request customer berikutnya. |
| SC Pendukung | Master switch WABA ON. Outlet A dan outlet B masing-masing memiliki konfigurasi per-outlet sendiri. | Owner toggle master switch WABA ke OFF. | Toggle ke OFF. Saat customer chat ke outlet A maupun outlet B, conversation langsung di-eskalasi ke agent. Konfigurasi per-outlet untuk outlet A dan outlet B tetap utuh (tidak dimodifikasi). |

## List & Navigation

**Goals**: Owner dapat melihat, mencari, dan mengurutkan daftar outlet miliknya yang terdaftar omnichannel di Bot Control Panel.

> **Sebagai Owner,**
> saya ingin melihat, mencari, dan mengurutkan daftar outlet saya yang terdaftar omnichannel di halaman Bot Control Panel,
> sehingga saya dapat mengidentifikasi outlet mana yang perlu disesuaikan pengaturan bot-nya.

**Detail**:
1. Hanya outlet yang terdaftar omnichannel yang muncul di daftar.
2. Search melakukan substring match terhadap nama, kota, atau kode outlet.
3. Sort default adalah Nama A-Z ascending. Opsi sort lain: Nama Z-A, Terakhir diubah.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Owner login dan membuka menu Bot Control Panel. Owner memiliki 5 outlet terdaftar omnichannel. | Owner mengakses halaman Control Panel. | Owner melihat daftar 5 outlet miliknya di control panel, masing-masing sebagai card outlet yang menampilkan ringkasan 6 intent configurable beserta last updated. |
| SC Utama | Owner berada di halaman Control Panel dengan daftar outlet ter-load. | Owner mengetik "tebet" di kolom search. | List outlet ter-filter hanya menampilkan outlet yang nama/kota/kode mengandung "tebet". |
| SC Utama | Owner di Control Panel dengan daftar outlet sudah ter-load. | Owner memilih opsi sort "Nama A-Z" / "Nama Z-A" / "Terakhir diubah" dari dropdown sort. | Daftar outlet ter-sort sesuai pilihan. Default sort adalah Nama A-Z ascending. |
| SC Utama | Owner memiliki 10 outlet fisik, namun hanya 6 yang terdaftar omnichannel (4 sisanya belum subs). | Owner membuka Control Panel. | Hanya outlet terdaftar omnichannel yang muncul di daftar. |

## Modal Edit

**Goals**: Owner dapat membuka modal edit dari card outlet, mengubah toggle intent, lalu menyimpan atau membatalkan perubahan.

> **Sebagai Owner,**
> saya ingin membuka modal edit dari card outlet, mengubah toggle intent secara lokal, lalu menyimpan atau membatalkan perubahan,
> sehingga pengaturan bot per outlet dapat diperbarui tanpa risiko perubahan yang tidak disengaja.

**Detail**:
1. Modal hanya terbuka dari tap/click pada card outlet di list.
2. Modal menampilkan 6 intent configurable dengan state sesuai DB saat modal dibuka.
3. Perubahan toggle bersifat lokal sampai tombol Simpan ditekan.
4. Tombol Simpan memicu PATCH ke server dengan payload berisi hanya intent yang berubah.
5. Tombol Batal menutup modal tanpa PATCH dan discard perubahan lokal.
6. Saat PATCH sukses, modal tertutup, toast "Tersimpan" muncul, dan list outlet refresh otomatis.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Owner di Control Panel. Card outlet A menampilkan 4 intent ON dan 2 intent OFF (current state). | Owner tap/click card outlet A. | Modal edit terbuka dengan header "Outlet A — Jakarta Pusat", menampilkan 6 intent configurable dengan masing-masing toggle sesuai state saat ini (4 ON, 2 OFF), plus talk_to_agent lock info "Selalu aktif". Tombol Simpan (primary) dan Batal (secondary) tampil di footer. |
| SC Utama | Modal edit outlet A terbuka. Toggle "Cek status laundry" saat ini OFF. | Owner tap toggle "Cek status laundry" dari OFF ke ON, tanpa klik Simpan. | Toggle berubah ke ON di state lokal UI, tidak ada request PATCH ke server. State DB masih OFF. Modal tetap terbuka. |
| SC Utama | Modal edit outlet A terbuka. Toggle "Promo aktif" saat ini ON. | Owner tap toggle "Promo aktif" dari ON ke OFF, tanpa klik Simpan. | Toggle berubah ke OFF di state lokal UI, tidak ada request PATCH ke server. State DB masih ON. Modal tetap terbuka. |
| SC Utama | Modal edit outlet A terbuka. Owner sudah toggle 2 intent dari state awal ("Cek status laundry" OFF→ON, "Promo aktif" ON→OFF). State lokal: 5 ON, 1 OFF. | Owner klik tombol Simpan. | Client kirim PATCH ke server dengan payload berisi hanya intent yang berubah. Server update data konfigurasi outlet + timestamp kapan berubah. Response 200 → toast "Tersimpan" muncul, modal tertutup. List outlet refresh otomatis. |
| SC Utama | Modal edit outlet A terbuka. Owner sudah toggle 2 intent (state lokal divergen dari state DB). | Owner klik tombol Batal. | Modal tertutup. Perubahan lokal di-discard — tidak ada PATCH ke server. Card outlet A di list tetap menampilkan state DB. |

## Lock & Locked Intent

**Goals**: Owner melihat intent "Hubungi agen" tampil dengan toggle readonly yang menampilkan toast info saat di-klik, agar paham bahwa jalur customer ke agent tidak pernah bisa dinonaktifkan.

> **Sebagai Owner,**
> saya ingin melihat intent "Hubungi agen" dengan toggle readonly dan info toast saat di-klik,
> sehingga saya paham bahwa jalur customer ke agent tidak pernah bisa dinonaktifkan.

**Detail**:
1. Baris "Hubungi agen" ditampilkan dengan toggle switch (visible) tapi dalam state disabled/readonly.
2. Saat owner tap/click toggle readonly, muncul toast info dengan pesan supported.
3. Toast message: "Hubungi agen selalu aktif agar customer selalu bisa terhubung dengan tim kami."
4. Tidak ada perubahan state saat toggle readonly di-klik (tidak ada efek apa-apa di DB).
5. Toast auto-dismiss setelah beberapa detik (default: 3-5 detik, sama dengan toast lainnya di aplikasi).

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Modal edit outlet A terbuka, master switch ON. | Owner melihat baris "Hubungi agen" di list intent. | Baris tersebut menampilkan toggle switch dalam state disabled/readonly (visible tapi tidak bisa di-interaksi aktif). State toggle mencerminkan status ON (sesuai default "Hubungi agen" yang selalu aktif). |
| SC Utama | Modal edit outlet A terbuka. Toggle "Hubungi agen" tampil readonly/disabled. | Owner tap/click toggle "Hubungi agen" yang readonly. | Muncul toast info: "Hubungi agen selalu aktif agar customer selalu bisa terhubung dengan tim kami." Toast auto-dismiss setelah beberapa detik. Toggle tidak berubah state, DB tidak ter-update. Modal tetap terbuka. |

## Default State

**Goals**: Outlet baru yang baru subscribe omnichannel otomatis memiliki semua 6 intent configurable dalam keadaan ON.

> **Sebagai Owner,**
> saya ingin outlet baru yang baru subscribe omnichannel otomatis memiliki semua 6 intent configurable dalam keadaan ON,
> sehingga outlet baru langsung dapat melayani customer tanpa perlu konfigurasi tambahan.

**Detail**:
1. Trigger/app-level otomatis insert 6 row di tabel pengaturan saat outlet baru di-mark terdaftar omnichannel.
2. Keenam intent ("Cek status laundry", "Cek status tiket", "Layanan outlet", "Jam operasional", "Promo aktif", "Info member") memiliki state ON by default.
3. "Hubungi agen" dan fallback tidak dibuat row di tabel pengaturan karena selalu ON secara default.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Pendukung | Owner meminta outlet baru untuk di-subscribe ke omnichannel. Outlet baru belum punya data konfigurasi intent. | Proses subscription outlet selesai. | Trigger/app-level secara otomatis insert 6 row di tabel pengaturan (satu per intent configurable) dengan state ON. Saat owner membuka control panel, card outlet baru langsung menampilkan 6 intent ON. |

## Bot Behavior Integration

**Goals**: Intent yang OFF tidak dieksekusi bot, fallback atau eskalasi sesuai, jalur customer ke agent selalu tersedia.

> **Sebagai Owner,**
> saya ingin intent yang saya OFF-kan tidak lagi dieksekusi oleh bot, dengan fallback atau eskalasi yang sesuai,
> sehingga customer tetap mendapat pengalaman yang aman dan tidak ada celah untuk menonaktifkan jalur ke manusia.

**Detail**:
1. Bot memuat pengaturan per-outlet di awal percakapan (target < 100ms p99).
2. Intent yang OFF dilewati dari klasifikasi intent — permintaan customer yang cocok dengan intent OFF mendapat pesan fallback.
3. Jika di outlet hanya "Hubungi agen" yang aktif (semua 6 intent yang bisa dikonfigurasi dalam keadaan OFF), outlet dianggap bot tidak aktif. Bot tidak mengirim salam pembuka dan tidak menampilkan menu bantuan — pesan customer langsung dialihkan ke agen (silent handoff, perilaku sama seperti master switch OFF).
4. Customer selalu bisa minta dihubungkan ke agen, tidak bisa dinonaktifkan.
5. Perintah manual "menu" / "hubungi agen" / "batal" selalu berfungsi apa pun pengaturannya.
6. Pesan fallback: "Maaf Kak, fitur ini sedang tidak tersedia di outlet ini. Ketik 'hubungi agen' untuk berbicara dengan customer service kami."

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Outlet X memiliki setting "Cek status laundry" OFF (lainnya ON). Customer memilih outlet X via WhatsApp dan greeting sudah dikirim. | Customer mengetik "Cek status laundry TRX12345". | Bot skip intent classification untuk "Cek status laundry" (karena OFF). Bot kirim fallback: "Maaf Kak, fitur ini sedang tidak tersedia di outlet ini. Ketik 'hubungi agen' untuk berbicara dengan customer service kami." Command "Hubungi agen" masih bisa dieksekusi. Intent lainnya yang ON tetap berjalan normal. |
| SC Utama | Outlet X memiliki setting semua 6 configurable intent = OFF (hanya "Hubungi agen" yang ON secara default). | Customer memilih outlet X, mengirim pesan pertama "Halo". | Bot load settings — tidak ada configurable intent yang bisa match. Outlet dianggap bot tidak aktif. Bot tidak mengirim salam pembuka dan tidak menampilkan menu. Pesan customer langsung dialihkan ke antrian agent outlet X (silent handoff). |
| SC Pendukung | Outlet X dengan "Hubungi agen" ON (default, tidak bisa di-toggle oleh UI). Semua configurable intent dalam keadaan apapun (ON atau OFF). | Customer di outlet X mengirim "Saya mau bicara dengan admin". | Bot selalu match intent "Hubungi agen" regardless setting apapun di pengaturan per-outlet. Bot konfirmasi dan teruskan ke agent outlet X. Customer selalu punya escape ke manusia. |
| SC Pendukung | Outlet X dengan 5 dari 6 configurable intent OFF, customer sudah di flow intent ON. | Customer mengetik "menu", "hubungi agen", atau "batal". | Bot selalu interpretasikan manual command tersebut regardless of pengaturan per-outlet. Command override toggle — owner tidak bisa menonaktifkan jalur customer ke agen. |
| SC Utama | Outlet X dengan Autobot Outlet ON, customer sedang dalam flow "Cek status laundry". | Owner matikan Autobot Outlet X (toggle OFF di card). | Chat bot customer yang sedang berlangsung langsung dihentikan dan customer dialihkan ke agent pada respons berikutnya. Konfigurasi capability outlet tetap tersimpan. |
| SC Pendukung | Outlet X dengan Autobot Outlet ON, customer sudah berada di sesi chat dengan agent. | Owner matikan Autobot Outlet X (toggle OFF). | Toggle berubah ke OFF, namun aturan penghentian chat tidak berlaku karena customer sudah di sesi agent. Customer tetap di sesi agent. |

## Error & Edge

**Goals**: Perubahan yang gagal disimpan tidak menghilangkan perubahan lokal, dan perubahan bersamaan dari beberapa device tidak saling menimpa data.

> **Sebagai Owner,**
> saya ingin perubahan yang gagal disimpan tidak menghilangkan perubahan lokal saya, dan perubahan bersamaan dari beberapa device tidak saling menimpa data,
> sehingga konfigurasi bot tetap konsisten dan dapat di-recovery jika terjadi gangguan.

**Detail**:
1. PATCH server-side atomic per outlet — tidak ada partial update.
2. Saat PATCH gagal (network error / 5xx), toast error muncul, modal tetap terbuka dengan perubahan lokal utuh untuk di-retry.
3. Saat dua device toggle bersamaan, last-write-wins — `updated_at` terbaru yang ditampilkan di card outlet.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Pendukung | Modal edit outlet A terbuka. Owner sudah toggle 2 intent. Koneksi internet tiba-tiba terputus atau server timeout. | Owner klik Simpan. | Client kirim PATCH, request timeout/gagal. Toast error tampil: "Gagal menyimpan. Coba lagi." Modal tidak tertutup — owner bisa klik Simpan lagi (retry) tanpa kehilangan perubahan lokal. State DB tidak berubah sebagian (transaksi server side rollback / atomic single PATCH). |
| SC Pendukung | Outlet A dengan "Cek status laundry" ON, timestamp terakhir = T0. Device device-1 dan device-2 keduanya membuka modal edit outlet A. | T1: device-1 toggle "Cek status laundry" ON→OFF, klik Simpan → server update row tersebut dengan timestamp T1. T2 (T2 > T1): device-2 toggle "Layanan outlet" ON→OFF, klik Simpan → server update row tersebut dengan timestamp T2. | Tidak ada race condition — PATCH per intent (atau atomic per outlet) sukses. Card outlet A menampilkan timestamp T2 (last_write_wins). Kedua device menampilkan state server yang terakhir di-save setelah refresh. |

---

## Supporting Information

### Lo-Fi Design — Master Switch & Outlet Card

```text
┌──────────────────────────────────────────────────────────────┐
│ Bot Control Panel                                            │
├──────────────────────────────────────────────────────────────┤
│ WABA: [ WABA-1 (Utama)                                  ▾ ] │
├──────────────────────────────────────────────────────────────┤
│ Status Bot WABA: [ ●──── ON ]                               │
│ Seluruh outlet pada WABA ini dapat menjalankan Autobot.      │
│ Diperbarui 2 jam lalu oleh Agestya                           │
├──────────────────────────────────────────────────────────────┤
│ Outlet A — Jakarta Pusat                  Autobot [ ●── ON ] │
│ Autobot aktif                                                │
│ 6 capability aktif · Jam Operasional 24/7                  › │
│ Diperbarui 2 jam lalu                                        │
└──────────────────────────────────────────────────────────────┘
```

### Lo-Fi Design — Modal Konfirmasi Master Switch OFF

```text
┌──────────────────────────────────────────────────────────────┐
│ Nonaktifkan Bot WABA-1?                                  ✕  │
├──────────────────────────────────────────────────────────────┤
│ Mematikan bot akan menghentikan percakapan bot yang sedang   │
│ berlangsung dan mengalihkan customer ke agent.               │
│ Konfigurasi per-outlet tetap tersimpan.                      │
├──────────────────────────────────────────────────────────────┤
│                    [Batal] [Konfirmasi]                      │
└──────────────────────────────────────────────────────────────┘
```

### Referensi PRD
- PRD P8 Control Panel §9.3 (Interaction Master Switch — konfirmasi), §10.2 (Autobot Outlet OFF — in-flight interruption), §12 (Bot Flow Integration).
- PRD Bot Runtime Behavior §6 (In-Flight Interruption) untuk perilaku chat saat switch berubah.

### Data Fixture (contoh konkret)
- WABA: `WABA-1 (Utama)`.
- Outlet: `Outlet A — Jakarta Pusat`, `Outlet B — Bandung`, `Outlet C — Surabaya`.
- Intent configurable: `Cek status laundry`, `Cek status tiket`, `Layanan outlet`, `Jam operasional`, `Promo aktif`, `Info member`.
- Intent locked: `Hubungi agen` (selalu ON).

### Catatan Asumsi
- WABA selector sudah ada sebelum Control Panel (pre-existing), tidak dibuat di fitur ini.
- Timestamp "diperbarui" diambil dari `updated_at` server-side per WABA dan per outlet.
