# Hubungi Agent — GWT Scenarios

> Generated dari PRD P2 — Hubungi Agent.
> Pattern mengikuti table Scenario di page "Detail User Stories": setiap skenario = 1 row, field Given/When/Then plain text.
> Kode SC auto-generate dari formula di Coda (`"SC-"+RowId(thisRow)`) — tidak perlu ditulis manual.

---

## Trigger Detection

**Goals**: Customer dapat meminta agen dengan berbagai variasi bahasa natural, dan bot mengenali semua variasi sebagai permintaan Hubungi Agent tanpa customer harus pakai command spesifik.

> **Sebagai Customer,**
> saya ingin minta bicara dengan agen manusia kapan saja,
> sehingga saya tidak terjebak di flow bot yang tidak bisa membantu kebutuhan saya.

**Detail**:
1. Trigger phrases: "admin", "CS", "agen", "manusia", "orang", "komplain", "bisa hubungi", "bicara dengan".
2. Tidak ada penalty over-escalation — jika ragu, eskalasi.
3. Fitur Hubungi Agent tidak memiliki per-outlet toggle (hak customer tidak bisa dimatikan owner).

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Master switch ON, customer di outlet dengan semua per-outlet ON. | Customer chat "saya mau bicara dengan admin". | Bot klasifikasi permintaan Hubungi Agent, balas konfirmasi eskalasi, trigger handoff ke antrean agen outlet. Bot stop membalas. |
| SC Utama | Master switch ON. | Customer chat "hubungi CS". | Bot balas konfirmasi eskalasi + handoff. |
| SC Utama | Master switch ON. | Customer chat "agen dong". | Bot balas konfirmasi eskalasi + handoff. |
| SC Utama | Master switch ON. | Customer chat "manusia aja". | Bot balas konfirmasi eskalasi + handoff. |
| SC Utama | Master switch ON. | Customer chat "saya mau komplain". | Bot balas konfirmasi eskalasi + handoff. |
| SC Utama | Master switch ON. | Customer chat "panggil supervisor". | Bot balas konfirmasi eskalasi + handoff. |
| SC Pendukung | Master switch ON. | Customer chat "bosan nih sama bot". | Bot klasifikasi sebagai permintaan Hubungi Agent (frustrasi phrase), balas konfirmasi eskalasi + handoff. |

## Eskalasi Saat Idle (Belum Ada Flow)

**Goals**: Customer yang baru chat di outlet dan langsung minta agen langsung di-handle tanpa harus greeting/flow lain.

> **Sebagai Customer,**
> saya mau langsung bicara dengan agen tanpa harus baca menu atau greeting dulu,
> sehingga saya tidak buang waktu untuk hal yang tidak perlu.

**Detail**:
1. Customer baru chat, belum pernah dapat greeting/menu.
2. Customer langsung minta agen di pesan pertama.
3. Bot skip greeting, langsung trigger eskalasi.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Master switch ON, customer baru pilih outlet, greeting sudah dikirim. | Customer chat pertama "saya mau bicara dengan orang". | Bot klasifikasi permintaan Hubungi Agent, balas konfirmasi eskalasi + handoff ke agen outlet. Menu bantuan tidak ditampilkan lagi. |
| SC Utama | Master switch ON, customer chat pertama langsung "CS". | Customer chat "CS" (singkat). | Bot klasifikasi permintaan Hubungi Agent (bukan intent lain), balas konfirmasi eskalasi + handoff. |
| SC Pendukung | Master switch ON, customer baru chat. | Customer chat "agen" tanpa konteks tambahan. | Bot klasifikasi permintaan Hubungi Agent langsung dari pesan pertama. Tidak ada greeting ulangan, tidak ada slot-filling. |

## Eskalasi Mid-Flow

**Goals**: Customer yang sedang di tengah flow bot (misal slot-filling Cek Status Laundry) tetap bisa minta agen kapan saja dan langsung di-handle.

> **Sebagai Customer,**
> saya mau bisa minta agen kapan saja walaupun sedang di flow bot,
> sehingga saya tidak terjebak di flow yang tidak membantu.

**Detail**:
1. Mid-flow = customer sedang di slot-filling, sudah dapat respons, atau di flow lain.
2. Customer minta agen di mid-flow → eskalasi langsung, slot state dibuang.
3. Tidak ada konfirmasi "apakah yakin?" atau "saya akan membatalkan flow".

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Master switch ON, customer sedang di flow Cek Status Laundry (sudah diminta ID transaksi). | Customer chat "saya mau bicara dengan agen". | Bot klasifikasi permintaan Hubungi Agent, balas konfirmasi eskalasi, handoff ke agen. Slot state dibuang, tidak minta ID transaksi lagi. |
| SC Utama | Master switch ON, customer baru chat "TMD260624160854112" (alur Cek Status Laundry). Bot sudah fetch & tampil Mode Ringkas. | Customer chat follow-up "saya mau ngomong sama manusia". | Bot klasifikasi permintaan Hubungi Agent, balas konfirmasi eskalasi + handoff. Mode Ringkas tidak diulang. |
| SC Pendukung | Master switch ON, customer di flow Cek Status Ticket, sudah diminta nomor tiket. | Customer chat "stop, saya mau agen". | Bot klasifikasi permintaan Hubungi Agent, balas konfirmasi eskalasi + handoff. Slot tiket dibuang. |

## Cancel & Re-Request

**Goals**: Customer yang minta agen tidak dapat membatalkan eskalasi via "batal" — eskalasi adalah final.

> **Sebagai Customer,**
> saya mau keputusan saya untuk bicara dengan agen dihormati tanpa bisa ditarik balik,
> sehingga eskalasi tidak bisa di-cancel oleh sistem.

**Detail**:
1. Setelah eskalasi triggered, "batal" tidak menarik kembali ke bot.
2. Customer yang berubah pikiran cukup bicara dengan agen yang sudah terhubung.
3. Idempotent: customer minta eskalasi berkali-kali = 1 eskalasi.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Master switch ON, bot sudah balas konfirmasi eskalasi, handoff triggered. | Customer chat "batal". | Bot tidak membalas (sudah di-silent). Conversation ada di agen. |
| SC Pendukung | Master switch ON, bot sudah balas konfirmasi eskalasi. | Customer chat "tunggu, saya mau tanya bot dulu". | Bot tidak membalas (silent setelah eskalasi). Customer hanya mendapat respons dari agen. |
| SC Pendukung | Master switch ON, eskalasi sudah triggered untuk conversation ini. | Customer chat "agen" lagi. | Tidak ada eskalasi kedua (idempotent). Conversation tetap di agen. |

## Mixed Intent (Eskalasi + Info)

**Goals**: Customer yang minta agen di pesan yang sama dengan info lain langsung di-eskalasi — info lain diabaikan.

> **Sebagai Customer,**
> saya mau permintaan eskalasi saya di pesan campuran tetap dihormati,
> sehingga eskalasi tidak terlewat karena ada info lain di pesan.

**Detail**:
1. Pesan gabungan "saya mau cek laundry, tolong hubungkan ke agen" → eskalasi.
2. Info layanan (misal nama transaksi, nomor HP) di pesan eskalasi diabaikan.
3. Tidak ada attempt bot untuk menjawab info sebelum eskalasi.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Master switch ON. | Customer chat "saya mau cek status laundry, tolong hubungkan ke admin". | Bot klasifikasi permintaan Hubungi Agent (intent dominan), balas konfirmasi eskalasi + handoff. Tidak ada request ID transaksi. |
| SC Pendukung | Master switch ON. | Customer chat "info layanannya apa? Saya mau bicara dengan agen". | Bot klasifikasi permintaan Hubungi Agent, balas konfirmasi eskalasi + handoff. Tidak ada respons info layanan. |

## Master Switch OFF

**Goals**: Customer tidak terjebak tanpa respons saat bot dimatikan — conversation otomatis masuk antrean agen via round-robin.

> **Sebagai Customer,**
> saya mau tetap mendapat respons walaupun bot dimatikan,
> sehingga saya tidak di-ignore.

**Detail**:
1. Master switch OFF → bot tidak aktif sama sekali.
2. Semua pesan customer (termasuk greeting) langsung masuk antrean round-robin outlet.
3. Tidak ada respons dari bot.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Master switch OFF. | Customer chat apa saja di outlet manapun. | Bot silent — tidak ada greeting, menu, atau respons. Conversation masuk antrean round-robin outlet (status unassigned jika semua agen offline). |
| SC Pendukung | Master switch OFF → ON, customer sudah chat saat OFF. | Customer chat setelah master switch ON. | Bot load settings, mulai normal flow (greeting/menu). Customer yang chat saat OFF sudah di antrean agen. |

## Multi-Customer & Session State

**Goals**: Eskalasi adalah per-conversation, tidak bocor ke customer lain yang chat di outlet yang sama.

> **Sebagai Customer,**
> saya mau eskalasi saya tidak mengganggu customer lain,
> sehingga privasi dan continuity conversation terjaga.

**Detail**:
1. Eskalasi terikat conversation customer yang request.
2. Customer lain yang chat setelah eskalasi customer pertama → bot tetap aktif untuk mereka.
3. Session bot customer pertama ter-reset setelah eskalasi (jika kembali ke bot).

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Master switch ON, customer pertama sudah di-eskalasi. Customer lain chat di outlet yang sama. | Customer lain chat "halo, ada layanan apa?". | Bot klasifikasi permintaan Layanan Outlet, balas normal untuk customer lain. Customer pertama tetap di agen (tidak ter-reset). |
| SC Pendukung | Master switch ON, customer pertama baru chat "saya mau bicara dengan admin" lalu eskalasi. | Customer pertama kembali chat "info layanan ya". | Bot silent untuk customer pertama (sudah di-silent setelah eskalasi). Pesan masuk ke antrean agen, bukan bot. |

## Respons & Bahasa

**Goals**: Konfirmasi eskalasi singkat, sopan, menyebut nama outlet, dan tidak menghakimi customer yang minta manusia.

> **Sebagai Customer,**
> saya mau dapat konfirmasi yang jelas bahwa saya akan dihubungkan ke agen,
> sehingga saya tahu permintaan saya diproses.

**Detail**:
1. Konfirmasi menyebut nama outlet customer.
2. Tone sopan, "Kak", singkat.
3. Tidak ada "apakah yakin?" atau "mengapa ingin bicara dengan agen?".
4. Bahasa Indonesia konsisten.
5. Tidak ada fallback message tambahan saat agent outlet offline — conversation masuk antrean round-robin (status unassigned), bot tidak kirim pesan tambahan.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Master switch ON, customer chat "saya mau bicara dengan admin". | Bot trigger eskalasi. | Bot balas: "Baik Kak, saya hubungkan ke agent [Nama Outlet] agar bisa dibantu lebih lanjut. Mohon tunggu sebentar ya." |
| SC Pendukung | Master switch ON, customer chat "agen". | Bot trigger eskalasi. | Bot balas konfirmasi dengan nama outlet + "Mohon tunggu sebentar ya." (tone sopan, tidak ada tanya alasan). |
| SC Pendukung | Master switch ON, customer chat "bisa ke manusia?". | Bot trigger eskalasi. | Bot balas konfirmasi dalam Bahasa Indonesia, tidak ada bilingual/English. |
| SC Pendukung | Master switch ON, customer chat "agen" tapi semua agent outlet offline. | Bot trigger eskalasi. | Bot balas konfirmasi eskalasi standar. Tidak ada fallback message tambahan. Conversation masuk antrean round-robin dengan status unassigned. |
