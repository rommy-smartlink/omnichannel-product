# Info Layanan Outlet — GWT Scenarios

> Generated dari PRD P3 — Info Layanan Outlet.
> Pattern mengikuti table Scenario di page "Detail User Stories": setiap skenario = 1 row, field Given/When/Then plain text.
> Kode SC auto-generate dari formula di Coda (`"SC-"+RowId(thisRow)`) — tidak perlu ditulis manual.

---

## Trigger & Basic Flow

**Goals**: Customer dapat menanyakan layanan outlet dengan bahasa natural, dan bot mengenali sebagai permintaan Info Layanan Outlet dengan respons adaptif.

> **Sebagai Customer,**
> saya ingin tanya layanan outlet pakai bahasa sehari-hari,
> sehingga saya cepat dapat jawaban ya/tidak atau daftar layanan yang masih nyaman dibaca.

**Detail**:
1. Bot klasifikasi permintaan Info Layanan Outlet untuk variasi: "ada layanan apa saja", "bisa laundry sepatu", "ada express", "bisa cuci bed cover".
2. Per-outlet toggle Info Layanan Outlet ON → bot mulai flow.
3. Per-outlet toggle OFF → bot tampil fallback "Fitur ini sedang tidak tersedia di outlet ini."

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Master switch ON + per-outlet Info Layanan Outlet ON, outlet punya maksimal 20 layanan. Customer chat "ada layanan apa saja?". | Customer tanya daftar layanan umum. | Bot klasifikasi permintaan Info Layanan Outlet, tampil semua layanan outlet. |
| SC Utama | Master switch ON + per-outlet Info Layanan Outlet ON, outlet punya 21 layanan atau lebih. Customer chat "ada layanan apa saja?". | Customer tanya daftar layanan umum. | Bot tidak tampilkan semua layanan, minta customer menyebut layanan/kebutuhan yang dicari. |
| SC Utama | Master switch ON + per-outlet ON. Customer chat "bisa laundry sepatu?". | Customer tanya layanan spesifik yang tersedia. | Bot balas: "Ya Kak, laundry sepatu tersedia di [Nama Outlet] ya." |
| SC Utama | Master switch ON + per-outlet ON. Customer chat "ada cuci kasur?". | Customer tanya layanan spesifik yang tidak tersedia. | Bot balas: "Maaf Kak, untuk [Nama Outlet] layanan cuci kasur belum tersedia ya. Kakak bisa menanyakan layanan lain atau saya hubungkan ke agent jika perlu." |
| SC Pendukung | Master switch ON + per-outlet Info Layanan Outlet OFF. | Customer chat "ada layanan apa saja?". | Bot tampil: "Maaf Kak, fitur ini sedang tidak tersedia di outlet ini. Saya bisa hubungkan Kakak ke agent jika dibantu." |

## Layanan Spesifik — Match & No-Match

**Goals**: Customer yang sebutkan layanan spesifik langsung dapat jawaban ya/tidak, tanpa listing semua layanan.

> **Sebagai Customer,**
> saya mau tanya apakah satu layanan tertentu tersedia,
> sehingga saya cepat dapat jawaban singkat.

**Detail**:
1. Bot fetch layanan aktif di outlet customer.
2. Bot match keyword customer ke data layanan.
3. Match → respons "Ya Kak, [layanan] tersedia."
4. No match → respons "Maaf Kak, [layanan] belum tersedia."
5. Customer bisa follow-up "ada yang lain?" → bot lanjut atau minta klarifikasi.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Per-outlet ON, outlet memiliki layanan "Laundry Sepatu" + "Laundry Tas" + "Setrika". | Customer chat "bisa laundry sepatu?". | Bot match keyword "sepatu" → "Ya Kak, laundry sepatu tersedia di [Nama Outlet] ya." |
| SC Utama | Per-outlet ON, outlet memiliki "Laundry Sepatu" + "Setrika" + "Cuci Kering". | Customer chat "ada laundry helm?". | Bot no match → "Maaf Kak, untuk [Nama Outlet] layanan laundry helm belum tersedia ya. Kakak bisa menanyakan layanan lain atau saya hubungkan ke agent jika perlu." |
| SC Utama | Per-outlet ON. | Customer chat "ada express?". | Bot match → "Ya Kak, layanan express tersedia di [Nama Outlet] ya. Layanan ini biasanya siap dalam waktu 3–4 jam." |
| SC Pendukung | Per-outlet ON, outlet punya layanan "Steam". | Customer chat "ada steam?". | Bot match → "Ya Kak, steam tersedia ya." |
| SC Pendukung | Per-outlet ON, outlet tidak punya "Cuci Kasur". | Customer chat "bisa cuci kasur?". | Bot no match → fallback + arahkan agen. |
| SC Pendukung | Per-outlet ON, outlet punya "Express" dan "Setrika". | Customer chat "ada express? Setrika?" (dua layanan dalam 1 pesan). | Bot jawab kedua: "Ya Kak, express dan setrika keduanya tersedia di [Nama Outlet] ya." |
| SC Utama | Per-outlet ON, outlet punya "Express". | Customer chat "apakah express ada?" (sinonim). | Bot match → "Ya Kak, express tersedia ya." |
| SC Pendukung | Per-outlet ON, outlet punya 21 layanan atau lebih. | Customer chat "layanannya apa aja ya?". | Bot klasifikasi permintaan Info Layanan Outlet, minta customer menyebut layanan/kebutuhan yang dicari. |

## Pertanyaan Umum — Batas 20 Layanan

**Goals**: Outlet dengan maksimal 20 layanan boleh menampilkan semua layanan; outlet dengan 21 layanan atau lebih tidak menampilkan daftar lengkap.

> **Sebagai Customer,**
> saya mau cek layanan outlet,
> sehingga saya mendapat daftar jika masih pendek, atau diarahkan menyebut kebutuhan jika terlalu panjang.

**Detail**:
1. Outlet dengan maksimal 20 layanan → output semua layanan.
2. Outlet dengan 21 layanan atau lebih → tidak output semua layanan.
3. Bot minta customer memperjelas jika daftar terlalu panjang.
4. Bot boleh memberi contoh keyword umum maksimal 5 item untuk membantu customer.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Per-outlet ON, outlet punya 20 layanan. | Customer chat "ada layanan apa saja?". | Bot tampil semua 20 layanan. |
| SC Utama | Per-outlet ON, outlet punya 21 layanan. | Customer chat "ada layanan apa saja?". | Bot tidak tampilkan semua layanan; bot minta customer menyebut layanan/kebutuhan yang dicari dan boleh memberi contoh keyword maksimal 5 item. |
| SC Utama | Per-outlet ON, outlet punya banyak layanan, customer chat "bisa laundry sepatu?". | Customer tanya spesifik dari banyak layanan. | Bot match "sepatu" → jawab "Ya Kak, laundry sepatu tersedia." |
| SC Pendukung | Per-outlet ON, outlet punya 21 layanan atau lebih, customer chat "ada layanan apa?". | Customer tanya umum. | Bot minta customer menyebut layanan/kebutuhan yang dicari. |
| SC Pendukung | Per-outlet ON, outlet punya 21 layanan atau lebih. | Customer chat "ada laundry apa saja?". | Bot minta customer memperjelas jenis layanan laundry yang dicari. |

## Follow-up & Elaboration

**Goals**: Customer yang diminta memperjelas bisa lanjut tanya spesifik, dan bot jawab sesuai context.

> **Sebagai Customer,**
> saya mau lanjut tanya setelah diminta memperjelas,
> sehingga saya bisa narrowing down layanan yang saya butuh.

**Detail**:
1. Setelah pertanyaan umum dengan 21 layanan atau lebih, bot minta customer menyebut layanan/kebutuhan yang dicari.
2. Customer sebut layanan spesifik → bot matching.
3. Customer minta daftar lagi pada outlet dengan 21 layanan atau lebih → bot tetap tidak menampilkan semua layanan dan minta customer memperjelas.
4. Customer minta detail harga → eskalasi ke agen.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Per-outlet ON, bot baru meminta customer memperjelas layanan yang dicari. | Customer follow-up "ada express?". | Bot match → "Ya Kak, layanan express tersedia di [Nama Outlet] ya." |
| SC Utama | Per-outlet ON, outlet punya 21 layanan atau lebih, bot baru meminta customer memperjelas. | Customer follow-up "tampilkan semua". | Bot tidak menampilkan semua layanan; bot minta customer menyebut jenis layanan yang dicari atau menawarkan hubungkan ke agent. |
| SC Pendukung | Per-outlet ON, layanan spesifik ditemukan. | Customer follow-up "harganya berapa?". | Bot eskalasi: "Maaf Kak, untuk informasi harga detail, saya bisa hubungkan Kakak ke agent outlet ya." |
| SC Pendukung | Per-outlet ON. | Customer follow-up "mau booking". | Bot eskalasi: "Maaf Kak, booking belum bisa dilakukan lewat bot. Saya bisa hubungkan Kakak ke agent ya." |
| SC Pendukung | Per-outlet ON, outlet punya 21 layanan atau lebih, customer chat "yang lain apa?". | Customer minta daftar layanan lain. | Bot minta customer menyebut layanan/kebutuhan yang dicari. |

## Per-Outlet Toggle

**Goals**: Per-outlet toggle menentukan apakah fitur ini aktif di outlet tertentu.

> **Sebagai Owner,**
> saya ingin toggle Info Layanan Outlet per outlet,
> sehingga saya bisa matikan di outlet yang belum update datanya.

**Detail**:
1. Per-outlet OFF → bot tampil fallback, tidak fetch layanan.
2. Per-outlet ON → bot fetch & tampil layanan outlet.
3. Master switch OFF → bot silent, agen handle.
4. Per-outlet toggle override tidak melebihi master switch (hierarchy: master OFF > per-outlet OFF > per-outlet ON).

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Master switch ON, per-outlet Info Layanan Outlet OFF. | Customer chat "ada layanan apa saja?". | Bot tampil fallback, tidak fetch data layanan. |
| SC Utama | Master switch ON, per-outlet Info Layanan Outlet ON, outlet punya maksimal 20 layanan. | Customer chat "ada layanan apa saja?". | Bot fetch data layanan, tampil semua layanan. |
| SC Utama | Master switch ON, per-outlet Info Layanan Outlet ON, outlet punya 21 layanan atau lebih. | Customer chat "ada layanan apa saja?". | Bot fetch data layanan, tidak tampilkan semua layanan, minta customer memperjelas. |
| SC Utama | Master switch OFF. | Customer chat "ada layanan apa saja?". | Bot silent. Conversation di-handle agen. |
| SC Pendukung | Master switch ON + per-outlet ON → OFF (saat customer sedang chat). | Owner toggle per-outlet ke OFF di tengah conversation customer. | Conversation yang sedang berlangsung tetap di-handle bot (setting baru apply di request berikutnya). Customer berikutnya dapat fallback. |

## Outlet Edge Cases

**Goals**: Bot handle outlet yang belum daftar layanan atau data yang tidak konsisten.

> **Sebagai Customer,**
> saya mau tetap dapat info meskipun outlet belum lengkap data,
> sehingga saya tidak kecewa tanpa jawaban sama sekali.

**Detail**:
1. Outlet tanpa layanan sama sekali → fallback + arahkan agen.
2. Data layanan kosong/null → fallback.
3. Outlet baru yang belum daftar layanan → fallback (tidak error).

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | Per-outlet ON, outlet baru tanpa data layanan sama sekali. | Customer chat "ada layanan apa saja?". | Bot tampil: "Maaf Kak, saat ini [Nama Outlet] belum memiliki layanan terdaftar. Saya bisa hubungkan Kakak ke agent untuk info lebih lanjut." |
| SC Pendukung | Per-outlet ON, data layanan kosong/null. | Customer chat "bisa laundry sepatu?". | Bot: "Maaf Kak, data layanan belum tersedia untuk [Nama Outlet]. Saya bisa hubungkan Kakak ke agent ya." |
| SC Pendukung | Per-outlet ON, outlet punya 1 layanan. | Customer chat "ada layanan apa saja?". | Bot tampil 1 layanan: "Di [Nama Outlet], tersedia: Cuci pakaian." |

## Multi-Customer & Context Isolation

**Goals**: Layanan yang ditampilkan adalah milik outlet customer, bukan outlet lain atau customer lain.

> **Sebagai Customer,**
> saya mau info layanan milik outlet saya sendiri,
> sehingga saya tidak dapat data outlet lain.

**Detail**:
1. Bot filter layanan berdasarkan outlet dari conversation context.
2. Customer berbeda chat outlet yang sama → sama dapat data outlet yang sama (tidak ada data PII per customer).
3. Outlet context tidak bisa diubah mid-flow.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Pendukung | Per-outlet ON, dua customer chat outlet yang sama. | Customer pertama chat "ada express?". Customer kedua chat "ada laundry sepatu?". | Kedua customer dapat respons yang relevan dari data outlet yang sama. Tidak ada data PII yang bocor. |
| SC Pendukung | Per-outlet ON, customer chat di outlet A. | Customer chat "outlet B ada apa?". | Bot tampil: "Maaf Kak, saya bisa bantu informasi layanan [Nama Outlet] tempat Kakak chat ya. Ada yang ingin ditanyakan?" |

## Language & Tone

**Goals**: Respons Info Layanan Outlet singkat, sopan, dan nyaman dibaca di WhatsApp.

> **Sebagai Customer,**
> saya mau respons yang ringkas dan friendly,
> sehingga saya bisa cepat baca di WhatsApp.

**Detail**:
1. Tone "Kak", Bahasa Indonesia.
2. Respons spesifik ≤ 2 baris.
3. Respons daftar layanan maksimal 20 item untuk pertanyaan umum.
4. Untuk outlet dengan 21 layanan atau lebih, respons tidak menampilkan semua layanan dan meminta customer memperjelas.
5. Tidak ada technical term atau internal code layanan.

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Pendukung | Per-outlet ON. | Customer chat "ada layanan apa saja?". | Bot balas Bahasa Indonesia, tone "Kak", format list numbered. |
| SC Pendukung | Per-outlet ON. | Customer chat "is there express?". (English). | Bot balas Bahasa Indonesia: "Ya Kak, express tersedia di [Nama Outlet] ya." |
| SC Pendukung | Per-outlet ON, outlet punya 20 layanan. | Customer chat "ada layanan apa saja?". | Bot tampil semua 20 layanan dalam format numbered list. |
| SC Pendukung | Per-outlet ON, outlet punya 21 layanan atau lebih. | Customer chat "ada layanan apa saja?". | Bot tidak tampilkan semua layanan; bot minta customer menyebut layanan/kebutuhan yang dicari. |
