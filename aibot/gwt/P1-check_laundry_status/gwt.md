# Cek Status Laundry — GWT Scenarios

> Generated dari PRD P1 — Cek Status Laundry.
> Pattern mengikuti table Scenario di page "Detail User Stories": setiap skenario = 1 row, field Given/When/Then plain text.
> Kode SC auto-generate dari formula di Coda (`"SC-"+RowId(thisRow)`) — tidak perlu ditulis manual.

---

## Master Switch & Intent Toggle

**Goals**: Customer dapat menggunakan fitur Cek status laundry sesuai hierarki master switch dan per-outlet intent toggle di outlet yang sedang dipilih.

> **Sebagai Customer,**
> saya ingin fitur Cek status laundry hanya aktif di outlet yang memungkinkan,
> sehingga pengalaman saya konsisten dengan konfigurasi yang owner atur.

**Detail**:
1. Master switch OFF di WABA → bot skip Cek status laundry di semua outlet di WABA (silent handoff ke agen).
2. Master switch ON + per-outlet "Cek status laundry" OFF → bot tampil fallback "Fitur ini sedang tidak tersedia di outlet ini" + ingatkan command "hubungi agen".
3. Master switch ON + per-outlet "Cek status laundry" ON → bot mulai flow slot-filling ID transaksi.
4. Override priority: master switch OFF > per-outlet OFF > per-outlet ON.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Customer memilih outlet di WABA. Per-outlet "Cek status laundry" OFF (lainnya ON). | Customer chat di outlet tersebut dengan pesan apa saja. | Bot tampil fallback: "Maaf Kak, fitur ini sedang tidak tersedia di outlet ini. Ketik 'hubungi agen' untuk berbicara dengan customer service kami." Bot tidak minta ID transaksi, tidak ada panggilan API. |
| SC Pendukung | Master switch WABA OFF, per-outlet "Cek status laundry" di outlet customer = ON. | Customer chat di outlet tersebut. | Bot skip Cek status laundry (dan semua intent). Conversation langsung di-eskalasi ke agen. Customer tidak terima greeting/menu/response bot apapun. |
| SC Pendukung | Master switch WABA OFF → ON, per-outlet "Cek status laundry" OFF. | Owner toggle master switch ke ON, customer chat dengan pesan biasa. | Bot load settings: master ON, per-outlet "Cek status laundry" OFF. Bot tampil fallback per-outlet OFF (bukan auto-classify Cek status laundry). |
| SC Utama | Master switch ON + per-outlet "Cek status laundry" ON. Customer chat "Halo, laundry saya sudah jadi?". | Customer memulai percakapan dengan pertanyaan tentang laundry. | Bot klasifikasi intent Cek status laundry. Bot minta ID transaksi: "Baik Kak, boleh kirim ID transaksi laundry-nya?" |

## ID Extraction & Normalization

**Goals**: Customer mengirim ID transaksi dengan format prefix 3 huruf + nomor (contoh `TMD260624160854112`), dan bot mengenali prefix + digit tanpa membuang satupun saat fetch ke backend.

> **Sebagai Customer,**
> saya ingin kirim ID transaksi saya apa adanya (TMDxxxxxxxxxxxxxxx) dan bot mengenali,
> sehingga saya tidak perlu format ulang atau strip prefix.

**Detail**:
1. Format ID customer: **prefix 3 huruf + digit**, contoh `TMD260624160854112`. Prefix wajib ada.
2. Bot normalisasi: uppercase, strip spasi/dash/strip whitespace di antara huruf & digit. **Prefix 3 huruf TIDAK di-strip** — harus ikut ke fetch.
3. ID tanpa prefix 3 huruf → bot tanya ulang.
4. ID terlalu pendek atau digit tidak lengkap → bot tanya ulang.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Master switch ON + per-outlet ON, customer chat "TMD260624160854112" | Customer kirim ID lengkap dengan prefix 3 huruf + digit. | Bot ekstrak ID utuh `TMD260624160854112` (case jadi uppercase), langsung fetch. Mode default = Ringkas. |
| SC Utama | Per-outlet ON | Customer kirim "tmd260624160854112" (huruf kecil). | Bot uppercase-kan prefix jadi `TMD`, fetch dengan ID `TMD260624160854112`. |
| SC Utama | Per-outlet ON | Customer kirim "TMD 260624160854112" (dengan spasi antara prefix & digit). | Bot strip whitespace → `TMD260624160854112`, fetch. |
| SC Utama | Per-outlet ON | Customer kirim "TMD-260624160854112" (dengan dash). | Bot strip dash → `TMD260624160854112`, fetch. |
| SC Utama | Per-outlet ON | Customer kirim "260624160854112" (numeric saja, tanpa prefix). | Bot tidak fetch. Balas: "ID-nya kelihatannya kurang lengkap Kak, bisa kirim ulang lengkap dengan kode di depannya?" Prefix wajib dikirim utuh. |
| SC Utama | Per-outlet ON | Customer kirim "TM260624160854112" (prefix cuma 2 huruf). | Bot tidak fetch (prefix harus 3 huruf). Balas: "Kode depan ID-nya kelihatannya kurang lengkap, bisa dicek lagi Kak?" |
| SC Utama | Per-outlet ON | Customer kirim "TMD26062" (prefix lengkap, digit terlalu pendek). | Bot tidak fetch. Balas: "ID-nya kelihatannya kurang lengkap, bisa dicek lagi Kak?" |
| SC Utama | Per-outlet ON, customer sudah pernah kasih ID lengkap `TMD260624160854112`. | Customer kirim pesan tanpa ID, misal "kapan ya jadi?". | Bot tetap di flow Cek status laundry (last_id_transaksi masih ada). Tampilkan ulang Mode Ringkas untuk ID tersebut, tanpa minta ID lagi. |

## Mode Ringkas (default)

**Goals**: Customer mendapat informasi status transaksi dengan ringkas (8 field wajib + Total) sebagai respons default tanpa harus minta.

> **Sebagai Customer,**
> saya ingin langsung lihat status laundry saya dengan ringkas setelah kirim ID,
> sehingga saya tidak harus tanya lagi untuk info dasar.

**Detail**:
1. Respons default Mode Ringkas = 8 field wajib + Total tagihan dengan label "Lunas" / "Belum lunas". 8 field wajib:

| No | Informasi                  |
| -- | -------------------------- |
| 1  | ID transaksi               |
| 2  | Nama outlet                |
| 3  | Nama customer              |
| 4  | Status transaksi           |
| 5  | Progress pengerjaan        |
| 6  | Tanggal diterima           |
| 7  | Estimasi / tanggal selesai |
| 8  | Arahan berikutnya          |

Total: `Rp X` + label "Lunas" / "Belum lunas".
2. Status internal di-map ke label customer-friendly:

| Kondisi Transaksi | Label untuk Customer      |
| ----------------- | -------------------------- |
| New received      | Laundry diterima outlet    |
| In progress       | Laundry sedang diproses    |
| Washing           | Sedang dicuci              |
| Drying            | Sedang dikeringkan         |
| Ironing           | Dalam proses setrika       |
| Packing           | Sedang dikemas             |
| Completed         | Siap diambil               |
| Picked up         | Sudah diambil              |
| Cancelled         | Transaksi dibatalkan       |
| Problem           | Perlu dicek oleh agent     |
4. Bahasa Indonesia, tone "Kak", format tanggal customer-friendly.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Per-outlet ON, customer kirim ID valid `TMD260624160854112`. Transaksi ditemukan, status internal "Washing", lunas=true. | Customer selesai kirim ID, bot fetch sukses. | Bot tampil Mode Ringkas: ID Transaksi, Outlet, Nama, Status "Sedang dicuci", Progress "60%", Diterima, Estimasi selesai, Total "Rp 25.000 · Lunas", Arahan. Tidak ada field sensitif. |
| SC Utama | Per-outlet ON, transaksi ditemukan, lunas=false tapi status "Sudah diambil". | Customer kirim ID, bot fetch sukses. | Bot tampil Status "Sudah diambil" + Total "Rp 25.000 · Belum lunas". Bot tidak follow-up soal pembayaran. |
| SC Utama | Per-outlet ON, transaksi ditemukan, `tanggal_selesai` null (masih proses). | Customer kirim ID, bot fetch sukses. | Baris "Estimasi selesai" tampil "belum tersedia, silakan cek lagi". Field lain tampil normal. |

## Mode Detail (on-demand)

**Goals**: Customer bisa minta info lebih lengkap ("detail", "info lengkap", "lebih lengkap", "rincian") dan bot menambahkan layanan + status ambil + pengantaran.

> **Sebagai Customer,**
> saya bisa minta detail tambahan tentang transaksi saya kapan saja,
> sehingga saya dapat info layanan, status ambil, dan status pengantaran tanpa harus kontak agen.

**Detail**:
1. Trigger phrase: "detail" / "info lengkap" / "lebih lengkap" / "rincian" (natural language).
2. Mode Detail hanya render 1× per ID. Minta lagi → balas "Detail sudah saya tampilkan di atas ya Kak."
3. ID berikutnya → reset ke Mode Ringkas default.
4. Pesan gabung ID + "detail" dalam 1 kali kirim → skip Ringkas, langsung render Detail.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Per-outlet ON, transaksi ditemukan, customer sudah dapat Mode Ringkas. | Customer chat "detail". | Bot render Mode Detail: 8 field + Layanan (list) + Status ambil + Pengantaran. Tepat 1× render. |
| SC Utama | Per-outlet ON, transaksi ditemukan, Mode Detail sudah pernah render untuk ID ini. | Customer chat "detail" lagi. | Bot balas "Detail sudah saya tampilkan di atas ya Kak." Tidak re-fetch, tidak re-render. |
| SC Utama | Per-outlet ON | Customer chat "TMD260624160854112 detail" (ID + trigger gabung). | Bot render Mode Detail (skip Mode Ringkas). Tidak ada render kedua. |
| SC Pendukung | Per-outlet ON, transaksi ditemukan, customer dapat Mode Detail. Customer lalu chat ID lain. | Customer kirim ID berbeda `TMD260625120954213`. | Bot render Mode Ringkas untuk ID baru (bukan Detail, karena Mode Detail cuma untuk ID yg sebelumnya). |
| SC Utama | Per-outlet ON, transaksi ditemukan, lunas=false. | Customer chat "detail". | Bot tampil Layanan + Status ambil + Pengantaran. Status bayar tetap label "Belum lunas" saja (tanpa nominal breakdown). |

## Mode Eskalasi (6 kondisi)

**Goals**: Customer yang transaksinya masuk 6 kondisi sensitif langsung di-handle agen manusia setelah ringkasan, tanpa bot intervensi lagi.

> **Sebagai Customer,**
> saya mau transaksi saya yg sensitif (masalah/hapus/refund/dll) ditangani agen,
> sehingga kasus saya tidak hilang dan dibantu manusia.

**Detail**:
1. Bot tampilkan ringkasan singkat dulu (ID + outlet + nama + status + 1 kalimat konteks), lalu eskalasi.
2. 6 kondisi picu eskalasi otomatis: Problem, status_hapus=1, ada permintaan hapus, refund pending/diproses, pengantaran gagal, catatan mengandung komplain/hilang/rusak.
3. Bot tidak intervensi lagi setelah eskalasi di-trigger.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Per-outlet ON, transaksi ditemukan, status internal "Problem". | Customer kirim ID, bot fetch sukses. | Bot tampil ringkas (ID + outlet + nama + status "Perlu dicek oleh agent") + kalimat "Saya menemukan transaksi Kakak, tetapi ada kondisi yang perlu dicek lebih lanjut oleh agent." + otomatis hubungkan ke agen outlet. |
| SC Utama | Per-outlet ON, transaksi ditemukan, status_hapus=1. | Customer kirim ID, bot fetch sukses. | Bot tampil ringkas + eskalasi otomatis ke agen. |
| SC Utama | Per-outlet ON, transaksi ditemukan, ada idpermintaan_hapus (ada pengajuan hapus). | Customer kirim ID, bot fetch sukses. | Bot tampil ringkas + eskalasi otomatis ke agen. |
| SC Utama | Per-outlet ON, transaksi ditemukan, data_refund.status = "pending" atau "diproses". | Customer kirim ID, bot fetch sukses. | Bot tampil ringkas + eskalasi otomatis ke agen. |
| SC Utama | Per-outlet ON, transaksi ditemukan, pengantaran_extra.status = "gagal" atau "failed". | Customer kirim ID, bot fetch sukses. | Bot tampil ringkas + eskalasi otomatis ke agen. |
| SC Utama | Per-outlet ON, transaksi ditemukan, catatan outlet mengandung keyword "komplain" / "hilang" / "rusak". | Customer kirim ID, bot fetch sukses. | Bot tampil ringkas + eskalasi otomatis ke agen. |
| SC Utama | Per-outlet ON, transaksi ditemukan, masuk 1 dari 6 kondisi eskalasi. | Customer kirim ID, bot sudah trigger eskalasi. | Customer chat lagi di conversation yg sama → bot tidak intervensi (mode eskalasi permanen). Agent yg handle. |
| SC Pendukung | Per-outlet ON, transaksi ditemukan, transaksi di outlet lain (bukan outlet customer). | Customer kirim ID, bot fetch sukses. | Bot tampil catatan "Transaksi ini tercatat di outlet [X], beda dengan outlet yang sedang dipilih Kakak." + otomatis eskalasi ke agen outlet customer (sesuai conversation context). |

## Not Found & Retry

**Goals**: Customer yang salah ketik ID dapat kesempatan mencoba lagi tanpa ribet, dengan eskalasi ke agen setelah 2× percobaan gagal.

> **Sebagai Customer,**
> saya mau bot tanya lagi dengan sopan kalau ID saya salah,
> sehingga saya bisa coba ulangi sebelum dihubungkan ke agen.

**Detail**:
1. 404 dari API → retry_count + 1.
2. retry ≤ 2 → bot tanya ulang dengan pesan sopan.
3. retry > 2 (percobaan ke-3) → bot eskalasi otomatis.
4. State retry di-reset begitu ID berhasil ditemukan atau sudah eskalasi.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Per-outlet ON, customer kirim ID yg tidak ada di sistem. | Bot fetch API dapat 404 (percobaan 1). | Bot minta coba lagi: "Maaf Kak, ID-nya tidak saya temukan. Bisa dicek lagi atau kirim ulang?" Modal slot-filling tetap terbuka. |
| SC Utama | Per-outlet ON, customer sudah 1× gagal, kirim ID berbeda masih 404. | Bot fetch API dapat 404 (percobaan 2). | Bot minta coba lagi dengan pesan ramah (sama gaya). |
| SC Utama | Per-outlet ON, customer sudah 2× gagal, kirim ID ketiga kali masih 404. | Bot fetch API dapat 404 (percobaan ke-3). | Bot tampil pesan "Mohon cek kembali ID transaksi Kakak, atau saya bisa hubungkan ke agent untuk dibantu manual." lalu otomatis eskalasi ke agen outlet. |
| SC Pendukung | Per-outlet ON, ID valid ditemukan setelah retry. | Customer kirim ID benar, bot fetch sukses. | Bot render Mode Ringkas untuk ID tersebut. retry_count di-reset. |

## Edge Cases

**Goals**: Customer mendapat perilaku bot yang konsisten di berbagai kondisi Edge (batal, gabungan pesan, stale, multi-transaksi).

> **Sebagai Customer,**
> saya bisa batalkan kapan saja dan lanjutkan lagi tanpa repot,
> sehingga saya tidak terjebak di satu flow.

**Detail**:
1. Command "batal" kapan saja → reset slot-filling ke menu.
2. ID milik outlet lain (di luar context customer) → tampil catatan + eskalasi.
3. Transaksi milik customer >1 di outlet yg sama → bot fetch hanya ID yg dikirim, tidak auto-listing.
4. Conversation stale > 5 menit → state reset.
5. Customer bilang "kapan?" setelah dapat Ringkas → tetap di flow Cek status laundry, render ulang Ringkas.
6. Customer minta foto → bot tidak kirim, eskalasi.
7. Numeric count dari total = 0 (sample/promo) → tampil "Total: Rp 0" normal.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Per-outlet ON, customer sudah dapat Mode Ringkas untuk ID `TMD260624160854112`. | Customer chat "batal". | Bot reset state, tampil: "Baik Kak, dibatalkan. Ada lagi yang bisa dibantu?" Kembali ke menu utama. |
| SC Pendukung | Per-outlet ON, customer baru chat, belum pernah kasih ID. | Customer chat "batal". | Bot tampil: "Baik Kak, dibatalkan. Ada lagi yang bisa dibantu?" (tidak ada perubahan state yg perlu di-reset). |
| SC Utama | Per-outlet ON, customer punya 3 transaksi di outlet ini. | Customer kirim ID `TMD260624160854112` saja. | Bot hanya fetch ID itu (1 transaksi). Bot tidak auto-list 3 transaksi. |
| SC Utama | Per-outlet ON, ID milik outlet berbeda (misal outlet customer = A, ID terdaftar di outlet B). | Customer kirim ID, bot fetch sukses. | Bot tampil catatan "Transaksi ini tercatat di outlet [B], beda dengan outlet yang sedang dipilih Kakak." lalu otomatis eskalasi ke agen outlet A (sesuai conversation context customer). |
| SC Pendukung | Per-outlet ON, Mode Ringkas sudah render, conversation diam 5+ menit. | Customer chat `TMD260625120954213` setelah jeda. | State di-reset (stale). Bot klasifikasi intent → minta ID lagi (treat as flow baru). |
| SC Utama | Per-outlet ON, Mode Ringkas sudah render. | Customer chat "kapan jadi?" (follow-up tanpa ID baru). | Bot tetap di flow Cek status laundry (last_id_transaksi masih ada), tampilkan ulang Mode Ringkas atau info tambahan relevan. |
| SC Utama | Per-outlet ON, transaksi ditemukan, `foto_transaksi` ada di response API. | Customer chat tanpa minta foto. | Bot TIDAK kirim foto otomatis. Tampilkan Mode Ringkas normal. |
| SC Utama | Per-outlet ON, transaksi ditemukan, foto transaksi ada. | Customer chat "kirim foto" / "ada foto?". | Bot tidak kirim foto. Eskalasi otomatis ke agen dengan pesan "Saya hubungkan ke agen ya, agar bisa kami bantu Kirim fotonya." |
| SC Pendukung | Per-outlet ON, transaksi ditemukan, total = 0. | Customer kirim ID, fetch sukses. | Bot tampil "Total: Rp 0". Status bayar netral (transaksi sample/promo). |
| SC Pendukung | Per-outlet ON, customer pakai bahasa campur (misal "cek status laundry TMD260624160854112 dong"). | Customer kirim pesan mixed. | Bot klasifikasi intent Cek status laundry + ekstrak ID, render Mode Ringkas. Robust pada variasi input. |

## Master Switch Mid-Flow

**Goals**: Conversation customer yang sedang berlangsung lanjut normal saat owner toggle master switch di tengah percakapan.

> **Sebagai Customer,**
> saya mau conversation saya yg sedang berlangsung tidak terganggu oleh perubahan setting owner,
> sehingga saya tetap mendapat jawaban sampai selesai.

**Detail**:
1. Setting baru (master switch atau per-outlet toggle) hanya apply di request customer berikutnya, bukan mid-flight.
2. Conversation yg sedang dalam flow Cek status laundry saat owner toggle → lanjut sampai selesai.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Per-outlet ON, customer sedang di flow Cek status laundry (sudah kirim ID, Mode Ringkas hampir render). | Owner toggle master switch WABA ke OFF. | Conversation customer lanjut normal — Mode Ringkas tetap di-render, ID sukses di-fetch. Request berikutnya dari customer (atau customer lain) baru akan skip bot. |
| SC Pendukung | Per-outlet ON, customer sudah dapat Mode Detail untuk ID `TMD260624160854112`. | Owner toggle per-outlet "Cek status laundry" ke OFF. | Conversation customer yg sedang berlangsung lanjut normal sampai selesai. Customer lain yg chat berikutnya dapat fallback "tidak tersedia". |
