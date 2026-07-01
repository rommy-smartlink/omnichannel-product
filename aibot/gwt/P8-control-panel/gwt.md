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
3. Konfigurasi `outlet_intent_settings` **tetap tersimpan utuh** saat master OFF — tidak di-reset atau dimodifikasi.
4. Saat master switch di-toggle ON lagi, konfigurasi per-outlet berlaku normal tanpa perlu setting ulang.
5. Override priority: master switch OFF > per-outlet intent OFF > per-outlet intent ON.
6. Toggle master switch langsung (tanpa modal konfirmasi, reversible).
7. Default state WABA baru: master switch ON.
8. Saat owner ganti WABA via selector, master switch menampilkan state WABA yang baru dipilih (independent per WABA).

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Owner baru diaktivasi WABA-nya (master switch default ON), lalu membuka Control Panel dan memilih WABA tersebut via WABA selector. | Owner melihat header Control Panel. | Owner melihat master switch di posisi ON dengan indicator normal. Banner info "Saat OFF, semua outlet di WABA langsung ke agent" tampil di bawah switch. |
| SC Utama | Owner di Control Panel, master switch sedang ON, ada 5 outlet di WABA dengan konfigurasi intent per-outlet masing-masing. | Owner tap toggle master switch dari ON ke OFF. | Master switch berubah ke OFF. Indicator visual berubah ke warna merah / icon warning. Banner info tambahan "Bot nonaktif — semua outlet langsung ke agent" muncul. PATCH endpoint master switch sukses dengan `updated_by` dan `updated_at` baru. Konfigurasi `outlet_intent_settings` untuk semua outlet tetap utuh (tidak dimodifikasi). |
| SC Utama | Master switch WABA sedang OFF (semua outlet di WABA bot-nya off, konfigurasi per-outlet tetap tersimpan). Customer baru mengirim pesan pertama ke salah satu outlet di WABA. | Customer chat ke outlet manapun di WABA. | Bot skip greeting + skip classification. Conversation langsung di-eskalasi ke agent outlet terkait. Customer tidak terima response bot apapun (silent handoff). |
| SC Utama | Master switch WABA OFF, outlet A memiliki konfigurasi `check_laundry_status = OFF` dan `get_outlet_services = ON` di `outlet_intent_settings`. | Owner toggle master switch WABA dari OFF kembali ke ON. | Master switch berubah ke ON dengan indicator normal. Owner membuka modal edit outlet A — toggle `check_laundry_status` masih OFF dan `get_outlet_services` masih ON sesuai konfigurasi sebelum master OFF (konfigurasi per-outlet tetap tersimpan, tidak berubah). |
| SC Pendukung | Owner memiliki 2 WABA (WABA-1 dengan master switch ON, WABA-2 dengan master switch OFF). Owner sedang di Control Panel untuk WABA-1. | Owner ganti pilihan ke WABA-2 via WABA selector. | Master switch otomatis update menampilkan state OFF sesuai WABA-2. Outlet list juga update menampilkan outlet WABA-2 saja. Toggle master di WABA-1 tidak ter-ubah (independent per WABA). |
| SC Utama | Master switch WABA OFF, customer sedang dalam flow slot-filling `check_laundry_status` (sebelum master di-off-kan). | Owner toggle master switch OFF. | Percakapan customer yang sedang berlangsung lanjut normal sampai selesai. Request berikutnya dari customer (atau customer lain di WABA) akan langsung di-eskalasi ke agent. |
| SC Pendukung | Master switch WABA OFF. | Owner toggle master switch ke ON. | Toggle master switch ke ON, indicator kembali normal, banner info OFF hilang. Konfigurasi per-outlet tetap utuh dan berlaku normal untuk request customer berikutnya. |
| SC Pendukung | Master switch WABA ON. Outlet A dan outlet B masing-masing memiliki konfigurasi `outlet_intent_settings` sendiri. | Owner toggle master switch WABA ke OFF. | Toggle ke OFF. Saat customer chat ke outlet A maupun outlet B, conversation langsung di-eskalasi ke agent. Konfigurasi `outlet_intent_settings` untuk outlet A dan outlet B tetap utuh (tidak dimodifikasi). |

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
| SC Utama | Owner login dan membuka menu Bot Control Panel. Owner memiliki 5 outlet terdaftar omnichannel (is_omnichannel_enabled = true). | Owner mengakses halaman Control Panel. | Owner melihat daftar 5 outlet miliknya di control panel, masing-masing sebagai card outlet yang menampilkan ringkasan 6 intent configurable beserta last updated. |
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
4. Tombol Simpan memicu PATCH `/api/control-panel/outlets/{id}/settings` dengan payload berisi hanya intent yang berubah.
5. Tombol Batal menutup modal tanpa PATCH dan discard perubahan lokal.
6. Saat PATCH sukses, modal tertutup, toast "Tersimpan" muncul, dan list outlet refresh otomatis.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Owner di Control Panel. Card outlet A menampilkan 4 intent ON dan 2 intent OFF (current state). | Owner tap/click card outlet A. | Modal edit terbuka dengan header "Outlet A — Jakarta Pusat", menampilkan 6 intent configurable dengan masing-masing toggle sesuai state saat ini (4 ON, 2 OFF), plus talk_to_agent lock info "Selalu aktif". Tombol Simpan (primary) dan Batal (secondary) tampil di footer. |
| SC Utama | Modal edit outlet A terbuka. Toggle check_laundry_status saat ini OFF. | Owner tap toggle check_laundry_status dari OFF ke ON, tanpa klik Simpan. | Toggle berubah ke ON di state lokal UI, tidak ada request PATCH ke server. State DB masih OFF. Modal tetap terbuka. |
| SC Utama | Modal edit outlet A terbuka. Toggle get_active_promos saat ini ON. | Owner tap toggle get_active_promos dari ON ke OFF, tanpa klik Simpan. | Toggle berubah ke OFF di state lokal UI, tidak ada request PATCH ke server. State DB masih ON. Modal tetap terbuka. |
| SC Utama | Modal edit outlet A terbuka. Owner sudah toggle 2 intent dari state awal (check_laundry_status OFF→ON, get_active_promos ON→OFF). State lokal: 5 ON, 1 OFF. | Owner klik tombol Simpan. | Client kirim PATCH ke server dengan payload berisi hanya intent yang berubah. Server update outlet_intent_settings (upsert 2 row) + set updated_by dan updated_at. Response 200 → toast "Tersimpan" muncul, modal tertutup. List outlet refresh otomatis. |
| SC Utama | Modal edit outlet A terbuka. Owner sudah toggle 2 intent (state lokal divergen dari state DB). | Owner klik tombol Batal. | Modal tertutup. Perubahan lokal di-discard — tidak ada PATCH ke server. Card outlet A di list tetap menampilkan state DB. |

## Lock & Locked Intent

**Goals**: Owner melihat intent `talk_to_agent` tampil dengan toggle readonly yang menampilkan toast info saat di-klik, agar paham bahwa escape hatch customer ke agent tidak pernah bisa dinonaktifkan.

> **Sebagai Owner,**
> saya ingin melihat intent `talk_to_agent` dengan toggle readonly dan info toast saat di-klik,
> sehingga saya paham bahwa escape hatch customer ke agent tidak pernah bisa dinonaktifkan.

**Detail**:
1. Baris `talk_to_agent` ditampilkan dengan toggle switch (visible) tapi dalam state disabled/readonly.
2. Saat owner tap/click toggle readonly, muncul toast info dengan pesan supported.
3. Toast message: "Hubungi agen selalu aktif agar customer selalu bisa terhubung dengan tim kami."
4. Tidak ada perubahan state saat toggle readonly di-klik (tidak ada efek apa-apa di DB).
5. Toast auto-dismiss setelah beberapa detik (default: 3-5 detik, sama dengan toast lainnya di aplikasi).

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Modal edit outlet A terbuka, master switch ON. | Owner melihat baris Hubungi agen (talk_to_agent) di list intent. | Baris tersebut menampilkan toggle switch dalam state disabled/readonly (visible tapi tidak bisa di-interaksi aktif). State toggle mencerminkan status ON (sesuai default `talk_to_agent` yang selalu aktif). |
| SC Utama | Modal edit outlet A terbuka. Toggle `talk_to_agent` tampil readonly/disabled. | Owner tap/click toggle `talk_to_agent` yang readonly. | Muncul toast info: "Hubungi agen selalu aktif agar customer selalu bisa terhubung dengan tim kami." Toast auto-dismiss setelah beberapa detik. Toggle tidak berubah state, DB tidak ter-update. Modal tetap terbuka. |

## Default State

**Goals**: Outlet baru yang baru subscribe omnichannel otomatis memiliki semua 6 intent configurable dalam keadaan ON.

> **Sebagai Owner,**
> saya ingin outlet baru yang baru subscribe omnichannel otomatis memiliki semua 6 intent configurable dalam keadaan ON,
> sehingga outlet baru langsung dapat melayani customer tanpa perlu konfigurasi tambahan.

**Detail**:
1. Trigger/app-level otomatis insert 6 row di `outlet_intent_settings` saat outlet baru di-mark `is_omnichannel_enabled = true`.
2. Keenam intent (`check_laundry_status`, `check_ticket_status`, `get_outlet_services`, `get_operating_hours`, `get_active_promos`, `get_member_info`) memiliki `is_enabled = true` by default.
3. `talk_to_agent` dan `unknown` tidak dibuat row di `outlet_intent_settings` karena selalu ON secara default.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Pendukung | Owner meminta outlet baru untuk di-subscribe ke omnichannel. Outlet baru belum punya row di outlet_intent_settings. | Proses subscription outlet selesai (insert ke outlets, is_omnichannel_enabled = true). | Trigger/app-level secara otomatis insert 6 row di outlet_intent_settings (satu per intent configurable) dengan is_enabled = true. Saat owner membuka control panel, card outlet baru langsung menampilkan 6 intent ON. |

## Bot Behavior Integration

**Goals**: Intent yang OFF tidak dieksekusi bot, fallback atau eskalasi sesuai, escape hatch customer ke agent selalu tersedia.

> **Sebagai Owner,**
> saya ingin intent yang saya OFF-kan tidak lagi dieksekusi oleh bot, dengan fallback atau eskalasi yang sesuai,
> sehingga customer tetap mendapat pengalaman yang aman dan tidak ada celah untuk menonaktifkan escape hatch ke manusia.

**Detail**:
1. Bot memuat `outlet_intent_settings` di awal percakapan (target < 100ms p99).
2. Intent yang OFF dilewati dari klasifikasi intent — permintaan customer yang cocok dengan intent OFF mendapat pesan fallback.
3. Jika di outlet hanya `talk_to_agent` yang aktif (semua 6 intent yang bisa dikonfigurasi dalam keadaan OFF), outlet dianggap bot tidak aktif. Bot tidak mengirim salam pembuka dan tidak menampilkan menu bantuan — pesan customer langsung dialihkan ke agen (silent handoff, perilaku sama seperti master switch OFF).
4. Customer selalu bisa minta dihubungkan ke agen, tidak bisa dinonaktifkan.
5. Perintah manual "menu" / "hubungi agen" / "batal" selalu berfungsi apa pun pengaturannya.
6. Pesan fallback: "Maaf Kak, fitur ini sedang tidak tersedia di outlet ini. Ketik 'hubungi agen' untuk berbicara dengan customer service kami."

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Outlet X memiliki setting check_laundry_status = OFF (lainnya ON). Customer memilih outlet X via WhatsApp dan greeting sudah dikirim. | Customer mengetik "Cek status laundry TRX12345". | Bot skip intent classification untuk check_laundry_status (karena OFF). Bot kirim fallback: "Maaf Kak, fitur ini sedang tidak tersedia di outlet ini. Ketik 'hubungi agen' untuk berbicara dengan customer service kami." Command talk_to_agent masih bisa dieksekusi. Intent lainnya yang ON tetap berjalan normal. |
| SC Utama | Outlet X memiliki setting semua 6 configurable intent = OFF (hanya talk_to_agent yang ON secara default). | Customer memilih outlet X, mengirim pesan pertama "Halo". | Bot load settings — tidak ada configurable intent yang bisa match. Outlet dianggap bot tidak aktif. Bot tidak mengirim salam pembuka dan tidak menampilkan menu. Pesan customer langsung dialihkan ke antrian agent outlet X (silent handoff). |
| SC Pendukung | Outlet X dengan talk_to_agent = ON (default, tidak bisa di-toggle oleh UI). Semua configurable intent dalam keadaan apapun (ON atau OFF). | Customer di outlet X mengirim "Saya mau bicara dengan admin". | Bot selalu match intent talk_to_agent regardless setting apapun di outlet_intent_settings. Bot konfirmasi dan teruskan ke agent outlet X. Customer selalu punya escape ke manusia. |
| SC Pendukung | Outlet X dengan 5 dari 6 configurable intent OFF, customer sudah di flow intent ON. | Customer mengetik "menu", "hubungi agen", atau "batal". | Bot selalu interpretasikan manual command tersebut regardless of outlet_intent_settings. Command override toggle — owner tidak bisa disable escape hatch customer. |

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
| SC Pendukung | Outlet A dengan check_laundry_status = ON, updated_at = T0. Device device-1 dan device-2 keduanya membuka modal edit outlet A. | T1: device-1 toggle check_laundry_status ON→OFF, klik Simpan → server update is_enabled = false, updated_at = T1, updated_by = owner.id. T2 (T2 > T1): device-2 toggle get_outlet_services ON→OFF, klik Simpan → server update row tersebut dengan timestamp T2. | Tidak ada race condition — PATCH per intent (atau atomic per outlet) sukses. Card outlet A menampilkan timestamp T2 (last_write_wins dari updated_at). Kedua device menampilkan state server yang terakhir di-save setelah refresh. |
