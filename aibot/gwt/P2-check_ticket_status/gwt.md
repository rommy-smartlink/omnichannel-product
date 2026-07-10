# Cek Status Ticket (`check_ticket_status`) — GWT Scenarios

> Generated dari PRD P2 — Cek Status Ticket.
> Pattern mengikuti table Scenario di page "Detail User Stories": setiap skenario = 1 row, field Given/When/Then plain text.
> Kode SC auto-generate dari formula di Coda (`"SC-"+RowId(thisRow)`) — tidak perlu ditulis manual.

---

## Master Switch & Intent Toggle

**Goals**: Customer dapat menggunakan fitur Cek status ticket sesuai hierarki master switch dan per-outlet intent toggle di outlet yang sedang dipilih.

> **Sebagai Customer,**
> saya ingin fitur Cek status ticket hanya aktif di outlet yang mengaktifkannya,
> sehingga pengalaman saya konsisten dengan konfigurasi yang owner atur.

**Detail**:
1. Master switch OFF → bot skip semua intent (silent handoff ke agen).
2. Master switch ON + per-outlet `check_ticket_status` OFF → bot tampil fallback "Fitur cek status tiket sedang tidak tersedia di outlet ini."
3. Master switch ON + per-outlet `check_ticket_status` ON → bot mulai flow slot-filling nomor ticket.
4. Override priority: master switch OFF > per-outlet OFF > per-outlet ON.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Customer memilih outlet di WABA. Per-outlet `check_ticket_status` OFF (intent lain mungkin ON). | Customer chat di outlet tersebut dengan pesan terkait cek ticket. | Bot tampil fallback: "Maaf Kak, fitur cek status tiket sedang tidak tersedia di outlet ini. Ketik 'hubungi agen' untuk berbicara dengan customer service kami." Bot tidak minta nomor ticket, tidak ada panggilan API. |
| SC Pendukung | Master switch OFF, per-outlet `check_ticket_status` di outlet customer = ON. | Customer chat di outlet tersebut. | Bot skip semua intent. Conversation langsung di-eskalasi ke agen. Customer tidak terima respons bot apapun. |
| SC Pendukung | Master switch OFF → ON, per-outlet `check_ticket_status` OFF. | Owner toggle master switch ke ON, customer chat dengan pesan cek ticket. | Bot load settings: master ON, per-outlet OFF. Bot tampil fallback per-outlet OFF. |
| SC Utama | Master switch ON + per-outlet `check_ticket_status` ON. | Customer memulai percakapan dengan "cek tiket saya dong". | Bot klasifikasi intent `check_ticket_status`. Bot minta nomor ticket: "Baik Kak, boleh kirim nomor ticket-nya?" |

## Trigger Detection

**Goals**: Bot mengenali berbagai variasi bahasa natural customer untuk cek status ticket tanpa perlu command khusus.

> **Sebagai Customer,**
> saya ingin bilang "cek tiket" dengan gaya bahasa saya sendiri,
> sehingga saya tidak perlu hafal command tertentu.

**Detail**:
1. Trigger phrases dikenali secara natural oleh LLM, tidak terbatas pada keyword tetap.
2. Contoh input: "Saya mau cek tiket", "Cek status ticket TKOM-0004-0626", "Tiket saya sudah sampai mana?", "Nomor tiket saya TKOM-0004-0626", "Status ticket saya gimana?".
3. Jika input mengandung nomor ticket yang valid, bot langsung ekstrak dan fetch.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Master switch ON + per-outlet ON. | Customer chat "Saya mau cek tiket." | Bot klasifikasi intent `check_ticket_status`. Bot minta nomor ticket: "Baik Kak, boleh kirim nomor ticket-nya?" |
| SC Utama | Master switch ON + per-outlet ON, pesan mengandung nomor ticket. | Customer chat "Cek status ticket TKOM-0004-0626." | Bot klasifikasi intent + ekstrak nomor `TKOM-0004-0626`, langsung fetch. |
| SC Utama | Master switch ON + per-outlet ON. | Customer chat "Tiket saya sudah sampai mana?" | Bot klasifikasi intent `check_ticket_status`. Bot minta nomor ticket. |
| SC Utama | Master switch ON + per-outlet ON. | Customer chat "Nomor tiket saya TKOM-0004-0626." | Bot ekstrak nomor `TKOM-0004-0626`, langsung fetch. |
| SC Utama | Master switch ON + per-outlet ON. | Customer chat "Status ticket saya gimana?" | Bot klasifikasi intent `check_ticket_status`. Bot minta nomor ticket. |
| SC Pendukung | Master switch ON + per-outlet ON. | Customer chat "tiket" saja (ambigu). | Bot klasifikasi sebagai `check_ticket_status`. Bot minta nomor ticket. |
| SC Pendukung | Master switch ON + per-outlet ON. | Customer chat "follow up ticket saya". | Bot klasifikasi intent `check_ticket_status`. Bot minta nomor ticket. |
| SC Pendukung | Master switch ON + per-outlet ON. | Customer chat "cek tiket dong kak". | Bot klasifikasi intent `check_ticket_status`. Bot minta nomor ticket. |

## Slot Filling — ID Extraction & Normalization

**Goals**: Bot mengekstrak nomor ticket dari input customer dengan normalisasi ringan, atau memintanya jika belum tersedia.

> **Sebagai Customer,**
> saya ingin bot bisa baca nomor ticket dari chat saya apa adanya,
> sehingga saya tidak perlu format ulang.

**Detail**:
1. Format nomor ticket: `TKOM-0004-0626` (ticket number, bukan internal ID).
2. Normalisasi: trim whitespace, uppercase huruf, pertahankan dash (`-`).
3. Jika nomor ticket belum ada di pesan → bot tanya.
4. Jika customer tidak membalas → bot diam; tidak ada follow-up otomatis.
5. Jika customer kirim pesan lain → bot klasifikasi ulang (bisa ganti intent).

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Per-outlet ON, customer sudah di flow `check_ticket_status`. | Customer kirim "TKOM-0004-0626". | Bot normalisasi `TKOM-0004-0626` (uppercase, dash dipertahankan), langsung fetch. |
| SC Utama | Per-outlet ON, customer sudah di flow. | Customer kirim "tkom-0004-0626" (huruf kecil). | Bot uppercase-kan → `TKOM-0004-0626`, fetch. |
| SC Utama | Per-outlet ON, customer sudah di flow. | Customer kirim "  tkom-0004-0626  " (dengan spasi). | Bot trim + uppercase → `TKOM-0004-0626`, fetch. |
| SC Pendukung | Per-outlet ON, customer chat pertama langsung kirim nomor ticket. | Customer chat "TKOM-0004-0626". | Bot klasifikasi intent `check_ticket_status` + ekstrak nomor, langsung fetch. |
| SC Utama | Per-outlet ON, bot sudah minta nomor ticket (baru pertama). | Customer tidak membalas dalam waktu berapa pun. | Bot diam. Tidak ada follow-up otomatis. Tidak ada pengulangan pertanyaan. |
| SC Utama | Per-outlet ON, bot sudah minta nomor ticket. | Customer kirim pesan lain (bukan nomor ticket, bukan kelanjutan flow ini). | Bot klasifikasi ulang pesan tersebut. Bisa jadi intent lain. |
| SC Utama | Per-outlet ON, customer kirim input terlalu ambigu (misal "tiket 123"). | Customer kirim nomor tidak sesuai format. | Bot tidak fetch. Tanya ulang: "Mohon cek kembali nomor ticket Kakak, atau saya bisa hubungkan ke agent untuk dibantu manual." |

## Detail Ticket — Mode 1

**Goals**: Customer mendapat informasi detail ticket yang aman ditampilkan ketika nomor ticket valid dan tidak sensitif.

> **Sebagai Customer,**
> saya ingin lihat detail ticket setelah kirim nomor,
> sehingga saya tahu status ticket saya tanpa harus hubungi agen.

**Detail**:
1. Response mode default saat ticket ditemukan dan aman ditampilkan.
2. Field yang ditampilkan: ID Ticket, Outlet, Nama Customer, Kategori, Status, Dibuat, Update terakhir, Topik, Ringkasan (aman), Transaksi terkait (jika ada).
3. Status internal di-map ke label customer-friendly:

| Status Internal | Label untuk Customer |
|----------------|---------------------|
| `OPEN` | Tiket diterima |
| `IN_PROGRESS` | Sedang ditindaklanjuti |
| `RESOLVED` | Sudah diselesaikan |
| Unknown | Sedang dicek tim outlet |

4. `Transaksi terkait` hanya tampil jika ada data (ticket tidak selalu terkait transaksi laundry).

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Per-outlet ON, customer kirim nomor `TKOM-0004-0626`. Ticket ditemukan, status `OPEN`, aman ditampilkan. | Bot fetch sukses (200 + data valid). | Bot render Detail Ticket: ID Ticket, Outlet, Nama, Kategori "Kendala", Status "Tiket diterima", Dibuat, Update terakhir, Topik, Ringkasan. |
| SC Utama | Per-outlet ON, ticket ditemukan, status `IN_PROGRESS`. | Bot fetch sukses. | Bot tampil Status "Sedang ditindaklanjuti" dengan copy: "Tim outlet sedang menindaklanjuti tiket Kakak. Mohon ditunggu ya Kak." |
| SC Utama | Per-outlet ON, ticket ditemukan, status `RESOLVED`. | Bot fetch sukses. | Bot tampil Status "Sudah diselesaikan" dengan copy: "Tiket Kakak tercatat sudah diselesaikan. Jika masih ada kendala, saya bisa hubungkan ke agent." |
| SC Utama | Per-outlet ON, ticket ditemukan, ada `notas[].external_id`. | Bot fetch sukses. | Bot tampilkan `Transaksi terkait` dengan daftar ID transaksi (maks 3). |
| SC Utama | Per-outlet ON, ticket ditemukan, `notas` kosong atau null. | Bot fetch sukses. | Bot tampilkan Detail Ticket tanpa baris `Transaksi terkait`. Tidak inventarisasi data. |
| SC Utama | Per-outlet ON, ticket ditemukan, `summary` aman. | Bot fetch sukses. | Bot tampilkan Ringkasan sesuai isi `summary`. |
| SC Utama | Per-outlet ON, ticket ditemukan, `summary` mengandung kata sensitif/kasar. | Bot fetch sukses. | Bot tampilkan fallback: "Detail laporan sedang dicek oleh tim outlet." |
| SC Utama | Per-outlet ON, ticket ditemukan, `resolved_at` tersedia. | Bot fetch sukses, status `RESOLVED`. | Bot tampilkan baris `Selesai: [tanggal]` setelah Update terakhir. |
| SC Utama | Per-outlet ON, ticket ditemukan, category `KOMPLAIN`. | Bot fetch sukses. | Bot tampil Kategori "Komplain". |
| SC Utama | Per-outlet ON, ticket ditemukan, category tidak dikenal. | Bot fetch sukses. | Bot tampil Kategori "Laporan" (fallback). |

## Ticket Tidak Ditemukan — Retry & Escalate

**Goals**: Customer yang salah kirim nomor ticket dapat mencoba lagi 2×, lalu di-eskalasi ke agent jika masih gagal.

> **Sebagai Customer,**
> saya ingin bot kasih kesempatan coba lagi kalau salah nomor,
> sehingga saya tidak langsung dihubungkan ke agent hanya karena salah ketik.

**Detail**:
1. Ticket tidak ditemukan → retry_count + 1.
2. retry ≤ 2 → bot tanya ulang.
3. retry = 3 (percobaan ke-3 masih gagal) → bot eskalasi otomatis.
4. retry_count di-reset setelah ticket ditemukan atau sudah eskalasi.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Per-outlet ON, customer kirim nomor ticket yang tidak ada di sistem. | Bot fetch, ticket tidak ditemukan (percobaan 1). | Bot balas: "Mohon cek kembali nomor ticket Kakak, atau saya bisa hubungkan ke agent untuk dibantu manual." Slot nomor ticket tetap terbuka. |
| SC Utama | Per-outlet ON, customer sudah 1× gagal, kirim nomor berbeda masih tidak ditemukan. | Bot fetch, ticket tidak ditemukan (percobaan 2). | Bot balas dengan pesan ramah yang sama (tidak menghakimi). Slot masih terbuka. |
| SC Utama | Per-outlet ON, customer sudah 2× gagal, kirim nomor ketiga kali masih tidak ditemukan. | Bot fetch, ticket tidak ditemukan (percobaan ke-3). | Bot balas: "Mohon cek kembali nomor ticket Kakak, atau saya bisa hubungkan ke agent untuk dibantu manual." lalu otomatis eskalasi ke agent outlet. Bot stop membalas. |
| SC Pendukung | Per-outlet ON, customer 2× gagal lalu kirim nomor valid. | Bot fetch, ticket ditemukan (percobaan ke-3 sebelum sempat escalate). | Bot render Detail Ticket. retry_count di-reset. |

## Auto-Eskalasi — Sensitive Ticket

**Goals**: Customer dengan ticket yang sensitif atau butuh judgment manusia langsung di-handle agent tanpa bot intervensi lebih lanjut.

> **Sebagai Customer,**
> saya mau ticket yang sensitif langsung ditangani agen,
> sehingga masalah saya tidak dijawab seadanya oleh bot.

**Detail**:
1. Kondisi auto-eskalasi: isu refund/pembayaran/hilang/rusak/komplain berat/dispute, status RESOLVED tapi customer menyatakan belum selesai, customer minta agent kapan pun.
2. Bot tampilkan pesan bahwa ticket ditemukan tapi perlu dicek agent.
3. Bot trigger `talk_to_agent` dan stop membalas.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Per-outlet ON, ticket ditemukan, summary/title mengandung kata "refund". | Bot fetch sukses. | Bot tampil: "Saya menemukan tiket Kakak, tetapi perlu dicek lebih lanjut oleh agent. Saya hubungkan Kakak ke agent outlet [Nama Outlet] ya. Mohon tunggu sebentar." Lalu trigger eskalasi. |
| SC Utama | Per-outlet ON, ticket ditemukan, summary mengandung "pembayaran". | Bot fetch sukses. | Bot auto-eskalasi dengan pesan yang sama. |
| SC Utama | Per-outlet ON, ticket ditemukan, summary mengandung "hilang". | Bot fetch sukses. | Bot auto-eskalasi. |
| SC Utama | Per-outlet ON, ticket ditemukan, summary mengandung "rusak". | Bot fetch sukses. | Bot auto-eskalasi. |
| SC Utama | Per-outlet ON, ticket ditemukan, status `RESOLVED`. | Customer chat "tapi masalah saya belum selesai." | Bot auto-eskalasi dengan pesan yang sama. |
| SC Pendukung | Per-outlet ON, ticket ditemukan, priority `HIGH` atau `URGENT` + summary sensitif. | Bot fetch sukses. | Bot auto-eskalasi (priority tinggi jadi pertimbangan tambahan). |

## Auto-Eskalasi — API Error

**Goals**: Customer mendapat informasi yang jelas ketika sistem bermasalah dan tetap diarahkan ke agent.

> **Sebagai Customer,**
> saya mau tetap dibantu kalau sistem error,
> sehingga saya tidak di-ignore.

**Detail**:
1. API error, timeout (>10 detik), atau unauthorized → bot tampilkan pesan kendala sistem lalu eskalasi.
2. Bot tidak menampilkan detail teknis error ke customer.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Per-outlet ON, customer kirim nomor valid. API return 500. | Bot fetch dapat error 500. | Bot balas: "Sistem ada kendala, saya hubungkan ke agent ya." + auto-eskalasi. Bot stop membalas. |
| SC Utama | Per-outlet ON, customer kirim nomor valid. API timeout > 10 detik. | Bot fetch timeout. | Bot balas pesan kendala sistem + auto-eskalasi. |
| SC Utama | Per-outlet ON, customer kirim nomor valid. API return 401. | Bot fetch unauthorized. | Bot balas pesan kendala sistem + auto-eskalasi (log internal). |
| SC Pendukung | Per-outlet ON, API error 500. Bot sudah trigger eskalasi. | Customer chat lagi. | Bot silent. Conversation sudah di agen. |

## Customer Requests Agent Mid-Flow

**Goals**: Customer yang sedang di flow Cek Status Ticket bisa minta agen kapan saja dan langsung di-handle.

> **Sebagai Customer,**
> saya mau bisa berhenti dan minta agen di tengah flow,
> sehingga saya tidak terpaksa lanjutkan proses yang tidak saya mau.

**Detail**:
1. Command "agent/admin/CS/manusia" di tengah flow → override, langsung eskalasi.
2. Slot state dibuang.
3. Tidak ada konfirmasi "apakah yakin?".

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Per-outlet ON, customer sedang di flow `check_ticket_status` (baru diminta nomor ticket). | Customer chat "saya mau bicara dengan admin". | Bot klasifikasi `talk_to_agent`, balas konfirmasi eskalasi + handoff. Slot nomor ticket dibuang. |
| SC Utama | Per-outlet ON, customer sudah dapat Detail Ticket. | Customer chat follow-up "agen dong". | Bot klasifikasi `talk_to_agent`, balas konfirmasi eskalasi + handoff. |
| SC Pendukung | Per-outlet ON, customer di flow, sudah 1× gagal (ticket not found). | Customer chat "CS". | Bot override ke `talk_to_agent`. Tidak lanjut retry. |

## Copy per Status

**Goals**: Customer mendapat respons yang sesuai dengan kondisi ticket-nya dengan bahasa yang ramah.

> **Sebagai Customer,**
> saya ingin bot bilang status ticket dengan jelas,
> sehingga saya tahu apa yang terjadi dengan ticket saya.

**Detail**:
1. Copy untuk tiap status sudah ditentukan di PRD.
2. Copy "Data tidak ditemukan" untuk retry.
3. Copy "Ada kendala sistem" untuk API error.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Per-outlet ON, status `OPEN`. | Bot render Detail Ticket. | Copy: "Tiket Kakak sudah diterima. Mohon ditunggu, tim outlet akan mengecek sesuai antrean." |
| SC Utama | Per-outlet ON, status `IN_PROGRESS`. | Bot render Detail Ticket. | Copy: "Tim outlet sedang menindaklanjuti tiket Kakak. Mohon ditunggu ya Kak." |
| SC Utama | Per-outlet ON, status `RESOLVED`. | Bot render Detail Ticket. | Copy: "Tiket Kakak tercatat sudah diselesaikan. Jika masih ada kendala, saya bisa hubungkan ke agent." |
| SC Utama | Per-outlet ON, ticket tidak ditemukan (retry). | Bot minta ulang nomor. | Copy: "Mohon cek kembali nomor ticket Kakak, atau saya bisa hubungkan ke agent untuk dibantu manual." |
| SC Utama | Per-outlet ON, API error. | Bot trigger eskalasi. | Copy: "Sistem ada kendala, saya hubungkan ke agent ya." |

## Field Safety

**Goals**: Bot tidak menampilkan field sensitif atau internal kepada customer.

> **Sebagai Customer,**
> saya hanya ingin lihat info yang relevan,
> sehingga tidak ada data internal outlet yang bocor ke saya.

**Detail**:
1. Field yang wajib disembunyikan: `contact_phone`, `assignee_name`, `created_by_name`, `resolved_by_name`, `employee_name`, `id`, `waba_id`, `organization_id`, `contact_id`, `room_id`, `external_channel_id`, `linked_messages`, `priority` (dari customer), catatan internal, audit log, raw payload, refund detail, payment detail, foto internal.
2. PII yang tidak perlu tidak ditampilkan.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Per-outlet ON, response API mengandung `contact_phone`, `assignee_name`, `employee_name`. | Bot render Detail Ticket. | Field tersebut tidak muncul di respons. Hanya field aman yang ditampilkan. |
| SC Utama | Per-outlet ON, response API mengandung `id`, `waba_id`, `organization_id`. | Bot render Detail Ticket. | ID internal tidak muncul. Hanya `ticket_number` yang muncul sebagai ID Ticket. |
| SC Utama | Per-outlet ON, response API mengandung `linked_messages` dengan isi chat raw. | Bot render Detail Ticket. | `linked_messages` tidak ditampilkan. |
| SC Utama | Per-outlet ON, response API mengandung `priority: HIGH`. | Bot render Detail Ticket. | `priority` tidak ditampilkan ke customer. Hanya dipakai internal untuk eskalasi jika diperlukan. |
| SC Utama | Per-outlet ON, response API mengandung `notas[].external_id` beserta employee ID. | Bot render Detail Ticket. | Hanya `external_id` yang tampil. Employee ID, nama employee, source internal tidak tampil. |

## Edge Cases

**Goals**: Customer mendapat perilaku bot yang konsisten di berbagai kondisi edge.

> **Sebagai Customer,**
> saya ingin bot tetap responsif di berbagai situasi,
> sehingga pengalaman saya tidak terputus.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Per-outlet ON, ticket ditemukan, response API data tidak lengkap (field penting null). | Bot fetch sukses tapi data parsial. | Bot tampilkan field aman yang tersedia. Jika status tidak bisa dipastikan, eskalasi ke agent. |
| SC Pendukung | Per-outlet ON, customer pakai bahasa campur (misal "cek tiket TKOM-0004-0626 dong"). | Customer kirim pesan mixed. | Bot ekstrak nomor ticket + klasifikasi intent, render Detail Ticket. |
| SC Pendukung | Per-outlet ON, customer chat nomor ticket yang valid tapi milik outlet berbeda (bukan outlet di context). | Bot fetch sukses. | Bot deteksi outlet mismatch. Eskalasi ke agent outlet sesuai conversation context. |
| SC Pendukung | Per-outlet ON, status internal ticket tidak dikenal (bukan OPEN/IN_PROGRESS/RESOLVED). | Bot fetch sukses. | Bot tampilkan "Sedang dicek tim outlet" dan eskalasi jika risiko salah informasi tinggi. |
| SC Pendukung | Per-outlet ON, customer kirim "batal" di tengah flow slot-filling. | Customer batalkan. | Bot reset state, balas: "Baik Kak, dibatalkan. Ada lagi yang bisa dibantu?" Kembali ke menu utama. |

---

## Ringkasan Skenario

| Area | Jumlah SC |
|------|-----------|
| Master Switch & Intent Toggle | 4 |
| Trigger Detection | 7 |
| Slot Filling — ID Extraction & Normalization | 7 |
| Detail Ticket — Mode 1 | 10 |
| Ticket Tidak Ditemukan — Retry & Escalate | 4 |
| Auto-Eskalasi — Sensitive Ticket | 6 |
| Auto-Eskalasi — API Error | 4 |
| Customer Requests Agent Mid-Flow | 3 |
| Copy per Status | 5 |
| Field Safety | 5 |
| Edge Cases | 5 |
| **Total** | **60** |
