# Fitur: AI Bot Omnichannel WhatsApp — LLM-Assisted Slot Filling MVP

## 1. Ringkasan Fitur

AI Bot Omnichannel WhatsApp adalah layer pertama layanan customer setelah customer memilih outlet. Bot membantu customer melalui percakapan natural, bukan melalui tombol menu. Customer dapat mengetik kebutuhan secara bebas, lalu bot memahami maksud customer, meminta informasi yang belum lengkap, memberikan jawaban sesuai data yang tersedia, atau meneruskan percakapan ke agent jika diperlukan.

Bot ini tidak menggantikan agent. Bot berfungsi sebagai penyaring awal untuk pertanyaan repetitif dan pengecekan sederhana agar agent dapat fokus pada percakapan yang membutuhkan penanganan manusia.

Pada MVP, bot hanya mendukung capability yang sudah didefinisikan. Bot tidak menggunakan knowledge bebas, tidak membuat keputusan di luar scope, dan tidak melakukan action yang tidak tersedia dalam daftar capability.

---

## 2. Tujuan Fitur

Tujuan utama fitur ini adalah:

1. Mengurangi beban agent dari pertanyaan berulang.
2. Mempercepat respons awal kepada customer setelah memilih outlet.
3. Membuat pengalaman chat terasa lebih natural karena customer dapat mengetik bebas.
4. Membantu customer melakukan pengecekan status transaksi laundry.
5. Membantu customer melakukan pengecekan status ticket.
6. Menyediakan informasi outlet seperti layanan, jam operasional, promo, dan member.
7. Menyediakan eskalasi ke agent jika customer membutuhkan bantuan manusia.
8. Menjaga agar bot tetap aman, terbatas, dan tidak menjawab di luar capability yang tersedia.

---

## 3. Batasan Fitur

Fitur ini memiliki batasan sebagai berikut:

1. Bot aktif hanya setelah customer memilih outlet.
2. Bot hanya menjawab berdasarkan capability yang tersedia.
3. Bot tidak membuat ticket.
4. Bot tidak mengubah status ticket.
5. Bot hanya membaca status ticket berdasarkan ID ticket.
6. Bot tidak menangani informasi pembayaran sebagai capability MVP.
7. Bot tidak menampilkan detail internal operasional outlet.
8. Bot tidak menampilkan data sensitif yang tidak relevan untuk customer.
9. Bot akan mengarahkan customer ke agent jika tidak dapat memahami kebutuhan customer.
10. Bot akan mengarahkan customer ke agent jika respons sistem terlalu lama atau terjadi kendala.

---

## 4. Supported Intent MVP

Intent yang didukung pada MVP:

```text
check_laundry_status
check_ticket_status
get_outlet_services
get_operating_hours
get_active_promos
get_member_info
talk_to_agent
unknown
```

Intent yang tidak termasuk MVP:

```text
get_payment_info
create_ticket
update_ticket
close_ticket
payment_confirmation
refund_request_handling
```

---

## 5. Alur Utama Fitur

### 5.1 Alur Awal

```text
Customer chat ke nomor WABA
↓
Customer memilih outlet
↓
Conversation memiliki konteks outlet
↓
Bot aktif sebagai layer pertama
↓
Bot mengirim greeting dan daftar bantuan
↓
Customer mengetik kebutuhan secara bebas
↓
Bot memahami kebutuhan customer
↓
Bot meminta informasi tambahan jika diperlukan
↓
Bot memberikan jawaban atau meneruskan ke agent
```

---

## 6. Greeting Bot

Setelah customer memilih outlet, bot menyambut customer dan memberikan daftar bantuan dalam bentuk teks.

### Contoh Greeting

```text
Halo Kak, selamat datang di [Nama Outlet].

Saya bisa bantu:
1. Cek status laundry
2. Cek status tiket
3. Info layanan outlet
4. Jam operasional
5. Promo aktif
6. Member
7. Hubungi agent

Silakan ketik kebutuhan Kakak ya.
```

### Acceptance Criteria

| No | Kriteria                                                     |
| -- | ------------------------------------------------------------ |
| 1  | Bot mengirim greeting setelah outlet dipilih.                |
| 2  | Greeting mencantumkan nama outlet.                           |
| 3  | Greeting menampilkan daftar bantuan yang tersedia.           |
| 4  | Customer dapat melanjutkan percakapan dengan mengetik bebas. |

---

## 7. Natural Chat Understanding

Bot memahami pesan bebas customer dan mengarahkannya ke intent yang sesuai.

### Contoh Input Customer

```text
Laundry saya sudah selesai belum?
Saya mau cek tiket.
Ada promo hari ini?
Outlet buka sampai jam berapa?
Saya mau bicara dengan admin.
```

### Expected Behavior

| Kondisi                 | Perilaku Bot                            |
| ----------------------- | --------------------------------------- |
| Pesan sesuai capability | Bot melanjutkan ke flow intent terkait. |
| Pesan belum lengkap     | Bot meminta informasi tambahan.         |
| Pesan tidak dikenali    | Bot menampilkan fallback.               |
| Customer minta agent    | Bot langsung mengarahkan ke agent.      |

---

## 8. Slot Filling / Pengumpulan Informasi

Slot filling adalah proses bot meminta informasi yang dibutuhkan sebelum menjawab kebutuhan customer.

### Contoh Slot Filling Status Laundry

```text
Customer:
Saya mau cek status laundry.

Bot:
Baik Kak, boleh kirim ID transaksi laundry-nya?

Customer:
TRX9812377123

Bot:
Baik Kak, saya cek status transaksinya dulu ya.
```

### Contoh Slot Filling Status Ticket

```text
Customer:
Saya mau cek status tiket.

Bot:
Baik Kak, boleh kirim nomor tiketnya?

Customer:
TCK-88321

Bot:
Baik Kak, saya cek status tiketnya dulu ya.
```

### Acceptance Criteria

| No | Kriteria                                                                           |
| -- | ---------------------------------------------------------------------------------- |
| 1  | Bot dapat mengetahui informasi yang belum lengkap.                                 |
| 2  | Bot dapat menanyakan parameter yang dibutuhkan.                                    |
| 3  | Bot tidak memberikan jawaban final jika parameter wajib belum tersedia.            |
| 4  | Bot dapat melanjutkan flow setelah customer memberikan parameter.                  |
| 5  | Bot dapat membatalkan flow dan menghubungkan ke agent jika customer meminta agent. |

---

# 9. Detail Fitur: Cek Status Laundry

## 9.1 Deskripsi

Fitur cek status laundry memungkinkan customer mengetahui status transaksi laundry berdasarkan ID transaksi. Fitur ini menjadi salah satu capability utama karena pertanyaan status transaksi adalah salah satu pertanyaan repetitif yang paling sering masuk ke agent.

## 9.2 Intent

```text
check_laundry_status
```

## 9.3 Contoh Input Customer

```text
Saya mau cek status laundry.
Laundry saya sudah selesai belum?
Cek status transaksi saya.
Cek TRX9812377123.
Nota saya INV-10239 sudah jadi belum?
```

## 9.4 Parameter

| Parameter    | Wajib/Opsional | Keterangan                                                                                                |
| ------------ | -------------- | --------------------------------------------------------------------------------------------------------- |
| ID transaksi | Wajib          | Digunakan untuk mencari detail transaksi laundry.                                                         |
| Nomor HP     | Opsional       | Dapat dipertimbangkan untuk fase lanjutan jika sistem mendukung pencarian transaksi berdasarkan nomor HP. |
| Outlet       | Otomatis       | Diambil dari outlet yang dipilih customer di awal percakapan.                                             |

## 9.5 Alur Cek Status Laundry

```text
Customer mengetik kebutuhan cek status laundry
↓
Bot memahami intent check_laundry_status
↓
Bot mengecek apakah ID transaksi sudah diberikan
↓
Jika belum ada, bot meminta ID transaksi
↓
Customer memberikan ID transaksi
↓
Bot mencari data transaksi berdasarkan outlet dan ID transaksi
↓
Jika ditemukan, bot menampilkan status transaksi
↓
Jika tidak ditemukan, bot menampilkan fallback dan menawarkan agent
```

---

## 9.6 Informasi yang Wajib Ditampilkan

Jika transaksi ditemukan, bot wajib menampilkan informasi berikut:

| No | Informasi                  | Tujuan                                                                    |
| -- | -------------------------- | ------------------------------------------------------------------------- |
| 1  | ID transaksi               | Memastikan customer tahu transaksi yang dicek.                            |
| 2  | Nama outlet                | Menghindari kebingungan jika customer pernah transaksi di outlet berbeda. |
| 3  | Nama customer              | Membantu customer memastikan transaksi benar.                             |
| 4  | Status transaksi           | Menjawab kebutuhan utama customer.                                        |
| 5  | Progress pengerjaan        | Memberikan gambaran sejauh mana transaksi diproses.                       |
| 6  | Tanggal diterima           | Memberi konteks kapan laundry masuk.                                      |
| 7  | Estimasi / tanggal selesai | Menjawab pertanyaan umum “kapan selesai?”.                                |
| 8  | Arahan berikutnya          | Memberi tahu customer apa yang perlu dilakukan.                           |

---

## 9.7 Informasi yang Opsional Ditampilkan

Informasi berikut hanya ditampilkan jika relevan, tersedia, dan tidak membuat respons terlalu panjang.

| Informasi                   | Kapan Ditampilkan                                                              |
| --------------------------- | ------------------------------------------------------------------------------ |
| Ringkasan layanan           | Jika customer perlu validasi layanan atau bot ingin memberi konteks transaksi. |
| Status ambil                | Jika laundry sudah selesai atau siap diambil.                                  |
| Status pengantaran          | Jika transaksi memiliki pengantaran.                                           |
| Status pembayaran sederhana | Jika perlu, cukup tampilkan “Lunas” atau “Belum lunas”.                        |
| Tanggal selesai pengerjaan  | Jika transaksi sudah selesai diproses.                                         |
| Catatan customer-facing     | Jika ada catatan yang memang aman untuk customer.                              |

---

## 9.8 Informasi yang Tidak Disarankan Ditampilkan

Bot tidak disarankan menampilkan informasi berikut secara otomatis:

| Informasi                | Alasan                                                    |
| ------------------------ | --------------------------------------------------------- |
| Nama karyawan/kasir      | Informasi internal, tidak perlu untuk customer.           |
| ID customer              | Informasi internal.                                       |
| Nomor telepon customer   | Data pribadi, tidak perlu ditampilkan ulang.              |
| Detail pajak             | Terlalu detail untuk status transaksi.                    |
| Detail diskon            | Tidak perlu untuk status utama.                           |
| Detail biaya tambahan    | Bisa memicu percakapan pembayaran yang di luar scope MVP. |
| Detail metode pembayaran | Capability pembayaran tidak termasuk MVP.                 |
| Rincian pembayaran penuh | Terlalu sensitif dan panjang untuk WhatsApp.              |
| Data refund              | Sensitif, sebaiknya diarahkan ke agent.                   |
| Foto transaksi           | Tidak ditampilkan otomatis.                               |
| Foto bukti ambil         | Tidak ditampilkan otomatis.                               |
| Catatan internal outlet  | Berpotensi tidak layak dibaca customer.                   |
| Data pengiriman mentah   | Informasi internal.                                       |

---

## 9.9 Mode Tampilan Status Transaksi

### Mode 1 — Ringkas Default

Mode ini digunakan sebagai respons utama bot.

```text
Baik Kak, berikut status transaksi Kakak:

ID Transaksi: [ID Transaksi]
Outlet: [Nama Outlet]
Nama: [Nama Customer]
Status: [Status Customer-Friendly]
Progress: [Persentase Pengerjaan]
Diterima: [Tanggal Diterima]
Estimasi selesai: [Tanggal Selesai]

Arahan: [Arahan Berikutnya]
```

### Mode 2 — Detail Ringan

Mode ini digunakan jika customer meminta detail tambahan.

```text
Baik Kak, berikut detail transaksi Kakak:

ID Transaksi: [ID Transaksi]
Outlet: [Nama Outlet]
Nama: [Nama Customer]
Status: [Status Customer-Friendly]
Progress: [Persentase Pengerjaan]
Diterima: [Tanggal Diterima]
Estimasi selesai: [Tanggal Selesai]

Layanan:
- [Nama Layanan] — [Jumlah] [Satuan]
- [Nama Layanan] — [Jumlah] [Satuan]

Status ambil: [Status Ambil]
Pengantaran: [Status Pengantaran jika ada]
```

### Mode 3 — Eskalasi Agent

Mode ini digunakan jika transaksi memiliki kondisi sensitif atau butuh pengecekan manual.

Contoh kondisi:

```text
refund
permintaan hapus transaksi
komplain
barang hilang/rusak
data transaksi tidak cocok
status bermasalah
pengantaran gagal
```

Contoh respons:

```text
Saya menemukan transaksi Kakak, tetapi ada kondisi yang perlu dicek lebih lanjut oleh agent.
Saya hubungkan Kakak ke agent outlet ya.
```

---

## 9.10 Rekomendasi Status Customer-Friendly

Status yang ditampilkan ke customer harus mudah dipahami dan tidak terlalu internal.

| Kondisi Transaksi       | Label untuk Customer    |
| ----------------------- | ----------------------- |
| Transaksi baru diterima | Laundry diterima outlet |
| Sedang diproses         | Laundry sedang diproses |
| Sedang dicuci           | Sedang dicuci           |
| Sedang dikeringkan      | Sedang dikeringkan      |
| Sedang disetrika        | Dalam proses setrika    |
| Sedang dikemas          | Sedang dikemas          |
| Selesai diproses        | Siap diambil            |
| Sudah diambil           | Sudah diambil           |
| Dibatalkan              | Transaksi dibatalkan    |
| Bermasalah              | Perlu dicek oleh agent  |

---

## 9.11 Arahan Berikutnya Berdasarkan Status

| Kondisi              | Arahan Customer                                                      |
| -------------------- | -------------------------------------------------------------------- |
| Masih diproses       | Customer diminta menunggu hingga estimasi selesai.                   |
| Siap diambil         | Customer dapat datang ke outlet untuk mengambil laundry.             |
| Sudah diambil        | Customer diberi informasi bahwa transaksi sudah selesai dan diambil. |
| Ada pengantaran      | Customer diberi informasi status pengantaran.                        |
| Data tidak ditemukan | Customer diminta mengecek ID transaksi atau hubungi agent.           |
| Ada kendala          | Customer diarahkan ke agent.                                         |

---

## 9.12 Contoh Respons

### Contoh 1 — Transaksi Masih Diproses

```text
Baik Kak, berikut status transaksi Kakak:

ID Transaksi: TRX9812377123
Outlet: Laundry Tebet
Nama: Budi
Status: Dalam proses setrika
Progress: 70%
Diterima: 24 Juni 2026
Estimasi selesai: 25 Juni 2026 pukul 17.00

Arahan: Laundry Kakak masih dalam proses. Silakan cek kembali mendekati waktu estimasi selesai ya.
```

### Contoh 2 — Transaksi Sudah Siap Diambil

```text
Baik Kak, transaksi TRX9812377123 sudah siap diambil.

Outlet: Laundry Tebet
Nama: Budi
Status: Siap diambil
Diterima: 24 Juni 2026
Selesai: 25 Juni 2026 pukul 16.30

Arahan: Kakak bisa datang ke outlet untuk mengambil laundry.
```

### Contoh 3 — Transaksi Sudah Diambil

```text
Baik Kak, transaksi TRX9812377123 sudah selesai dan sudah diambil.

Outlet: Laundry Tebet
Nama: Budi
Tanggal diterima: 24 Juni 2026
Tanggal selesai: 25 Juni 2026
Status: Sudah diambil

Jika ada kendala terkait transaksi ini, saya bisa bantu hubungkan ke agent.
```

### Contoh 4 — Transaksi Tidak Ditemukan

```text
Maaf Kak, saya belum menemukan transaksi dengan ID tersebut di Outlet Tebet.

Mohon cek kembali ID transaksi Kakak, atau saya bisa hubungkan ke agent untuk dibantu manual.
```

---

## 9.13 Acceptance Criteria

| No | Kriteria                                                                                                                          |
| -- | --------------------------------------------------------------------------------------------------------------------------------- |
| 1  | Customer dapat meminta status laundry dengan bahasa bebas.                                                                        |
| 2  | Bot dapat meminta ID transaksi jika customer belum memberikannya.                                                                 |
| 3  | Bot dapat menampilkan status transaksi jika data ditemukan.                                                                       |
| 4  | Bot menampilkan ID transaksi, outlet, nama customer, status, progress, tanggal diterima, estimasi selesai, dan arahan berikutnya. |
| 5  | Bot tidak menampilkan informasi internal yang tidak relevan.                                                                      |
| 6  | Bot tidak menampilkan detail pembayaran penuh.                                                                                    |
| 7  | Bot tidak menampilkan data refund secara otomatis.                                                                                |
| 8  | Bot memberikan fallback jika transaksi tidak ditemukan.                                                                           |
| 9  | Bot menawarkan agent jika data tidak cocok atau transaksi perlu pengecekan manual.                                                |
| 10 | Respons bot tetap ringkas dan mudah dibaca di WhatsApp.                                                                           |

---

# 10. Detail Fitur: Cek Status Ticket

## 10.1 Deskripsi

Fitur cek status ticket memungkinkan customer mengetahui perkembangan ticket berdasarkan ID ticket. Pada bot, ticket bersifat read-only.

Bot tidak membuat ticket, tidak mengubah ticket, tidak menutup ticket, dan tidak menambahkan catatan ticket.

## 10.2 Intent

```text
check_ticket_status
```

## 10.3 Contoh Input Customer

```text
Saya mau cek tiket.
Cek status ticket TCK-88321.
Tiket saya sudah sampai mana?
Nomor tiket saya TCK-99123.
```

## 10.4 Parameter

| Parameter | Wajib/Opsional | Keterangan                             |
| --------- | -------------- | -------------------------------------- |
| ID ticket | Wajib          | Digunakan untuk mencari status ticket. |

## 10.5 Alur

```text
Customer meminta cek status ticket
↓
Bot memahami intent check_ticket_status
↓
Bot mengecek apakah ID ticket tersedia
↓
Jika belum tersedia, bot meminta ID ticket
↓
Customer mengirim ID ticket
↓
Bot mencari status ticket
↓
Jika ditemukan, bot menampilkan status ticket
↓
Jika tidak ditemukan, bot memberikan fallback
```

## 10.6 Informasi yang Ditampilkan

| Informasi          | Status   |
| ------------------ | -------- |
| ID ticket          | Wajib    |
| Status ticket      | Wajib    |
| Update terakhir    | Opsional |
| Keterangan singkat | Opsional |
| Arahan berikutnya  | Wajib    |

## 10.7 Contoh Respons

```text
Baik Kak, berikut status tiket Kakak:

ID Ticket: TCK-88321
Status: Sedang diproses
Update terakhir: Tim outlet sedang melakukan pengecekan.

Arahan: Mohon ditunggu ya Kak. Tim kami akan menindaklanjuti sesuai antrean.
```

Jika ticket tidak ditemukan:

```text
Maaf Kak, saya belum menemukan ticket dengan ID tersebut.

Mohon cek kembali nomor ticket Kakak, atau saya bisa hubungkan ke agent untuk dibantu.
```

## 10.8 Acceptance Criteria

| No | Kriteria                                                  |
| -- | --------------------------------------------------------- |
| 1  | Customer dapat meminta status ticket dengan bahasa bebas. |
| 2  | Bot dapat meminta ID ticket jika belum tersedia.          |
| 3  | Bot hanya membaca status ticket.                          |
| 4  | Bot tidak membuat, mengubah, atau menutup ticket.         |
| 5  | Bot memberikan fallback jika ticket tidak ditemukan.      |
| 6  | Bot dapat mengarahkan customer ke agent jika dibutuhkan.  |

---

# 11. Detail Fitur: Info Layanan Outlet

## 11.1 Deskripsi

Fitur info layanan outlet membantu customer mengetahui layanan yang tersedia pada outlet yang dipilih. Karena satu outlet dapat memiliki banyak layanan, bot tidak disarankan menampilkan semua layanan sekaligus.

Bot harus membantu customer menemukan layanan yang relevan berdasarkan kebutuhan yang ditanyakan.

## 11.2 Intent

```text
get_outlet_services
```

## 11.3 Contoh Input Customer

```text
Ada layanan apa saja?
Bisa laundry sepatu?
Ada express?
Bisa cuci bed cover?
```

## 11.4 Rekomendasi Produk untuk Outlet dengan Banyak Layanan

Jika outlet memiliki banyak layanan, bot sebaiknya tidak menampilkan seluruh daftar layanan dalam satu respons.

Pendekatan yang direkomendasikan:

1. Jika customer menanyakan layanan spesifik, bot menjawab apakah layanan tersebut tersedia.
2. Jika customer bertanya umum, bot menampilkan kategori layanan utama.
3. Jika daftar layanan terlalu banyak, bot meminta customer memperjelas layanan yang dicari.
4. Bot menampilkan maksimal beberapa layanan paling relevan agar respons tetap nyaman dibaca.

## 11.5 Contoh Respons

Jika customer bertanya layanan spesifik:

```text
Untuk [Nama Outlet], layanan laundry sepatu tersedia ya Kak.
```

Jika layanan tidak tersedia:

```text
Untuk [Nama Outlet], layanan laundry sepatu belum tersedia ya Kak.
Kakak bisa menanyakan layanan lain atau saya hubungkan ke agent jika perlu.
```

Jika customer bertanya umum:

```text
Di [Nama Outlet], layanan tersedia dalam beberapa kategori seperti:

1. Cuci pakaian
2. Setrika
3. Express
4. Bed cover
5. Layanan khusus

Kakak ingin mencari layanan apa?
```

## 11.6 Acceptance Criteria

| No | Kriteria                                                                       |
| -- | ------------------------------------------------------------------------------ |
| 1  | Bot dapat menjawab pertanyaan layanan outlet.                                  |
| 2  | Bot tidak menampilkan ratusan layanan sekaligus.                               |
| 3  | Bot dapat menjawab ketersediaan layanan spesifik jika customer menyebutkannya. |
| 4  | Bot dapat meminta customer memperjelas layanan jika pertanyaan terlalu umum.   |
| 5  | Bot dapat mengarahkan customer ke agent jika informasi layanan tidak tersedia. |

---

# 12. Detail Fitur: Jam Operasional

## 12.1 Deskripsi

Fitur jam operasional membantu customer mengetahui waktu buka dan tutup outlet.

## 12.2 Intent

```text
get_operating_hours
```

## 12.3 Contoh Input Customer

```text
Buka jam berapa?
Tutup jam berapa?
Hari Minggu buka?
Sekarang masih buka?
```

## 12.4 Informasi yang Ditampilkan

| Informasi                | Status   |
| ------------------------ | -------- |
| Nama outlet              | Wajib    |
| Hari operasional         | Wajib    |
| Jam buka                 | Wajib    |
| Jam tutup                | Wajib    |
| Status sedang buka/tutup | Opsional |
| Catatan khusus           | Opsional |

## 12.5 Contoh Respons

```text
Jam operasional [Nama Outlet]:

Senin–Minggu: 08.00–21.00

Silakan datang sesuai jam operasional outlet ya Kak.
```

Jika bertanya hari tertentu:

```text
Untuk hari Minggu, [Nama Outlet] buka pukul 08.00–21.00 ya Kak.
```

## 12.6 Acceptance Criteria

| No | Kriteria                                                                |
| -- | ----------------------------------------------------------------------- |
| 1  | Bot dapat menjawab jam operasional outlet.                              |
| 2  | Bot mencantumkan nama outlet.                                           |
| 3  | Bot dapat menjawab pertanyaan hari tertentu jika data tersedia.         |
| 4  | Bot dapat memberikan fallback jika data jam operasional belum tersedia. |

---

# 13. Detail Fitur: Promo Aktif

## 13.1 Deskripsi

Fitur promo aktif membantu customer mengetahui promo yang sedang berlaku pada outlet yang dipilih.

## 13.2 Intent

```text
get_active_promos
```

## 13.3 Contoh Input Customer

```text
Ada promo?
Diskon apa hari ini?
Promo member ada?
Voucher apa yang aktif?
```

## 13.4 Informasi yang Ditampilkan

| Informasi         | Status               |
| ----------------- | -------------------- |
| Nama promo        | Wajib jika ada promo |
| Deskripsi promo   | Wajib jika ada promo |
| Periode berlaku   | Opsional             |
| Syarat singkat    | Opsional             |
| Arahan berikutnya | Wajib                |

## 13.5 Contoh Respons

Jika ada promo:

```text
Promo aktif di [Nama Outlet]:

HEMAT20
Diskon 20% untuk layanan cuci setrika.
Berlaku sampai 30 Juni 2026.

Syarat dan ketentuan dapat berlaku ya Kak.
```

Jika tidak ada promo:

```text
Saat ini belum ada promo aktif di [Nama Outlet].
```

## 13.6 Acceptance Criteria

| No | Kriteria                                                                                     |
| -- | -------------------------------------------------------------------------------------------- |
| 1  | Bot dapat menjawab promo aktif outlet.                                                       |
| 2  | Bot hanya menampilkan promo yang masih berlaku.                                              |
| 3  | Bot memberikan fallback jika tidak ada promo aktif.                                          |
| 4  | Bot dapat mengarahkan customer ke agent jika customer membutuhkan detail promo lebih lanjut. |

---

# 14. Detail Fitur: Informasi Member

## 14.1 Deskripsi

Fitur informasi member membantu customer mendapatkan informasi terkait program member. Untuk MVP, detail parameter member masih dapat disesuaikan berdasarkan kesiapan data.

## 14.2 Intent

```text
get_member_info
```

## 14.3 Contoh Input Customer

```text
Member itu apa?
Benefit member apa saja?
Saya mau cek poin member.
Nomor saya sudah terdaftar member belum?
```

## 14.4 Scope MVP

Untuk MVP, fitur member dapat dimulai dari informasi umum seperti:

1. Benefit member.
2. Cara menggunakan benefit.
3. Penjelasan umum program member.

Jika data sudah tersedia, fitur dapat diperluas ke:

1. Cek status member.
2. Cek poin member.
3. Cek benefit yang tersedia untuk customer.

## 14.5 Contoh Respons Basic

```text
Member mendapatkan poin dari setiap transaksi dan dapat menikmati promo khusus dari outlet.

Untuk informasi detail member, saya bisa hubungkan Kakak ke agent.
```

## 14.6 Acceptance Criteria

| No | Kriteria                                                                   |
| -- | -------------------------------------------------------------------------- |
| 1  | Bot dapat memahami pertanyaan terkait member.                              |
| 2  | Bot dapat memberikan informasi umum member.                                |
| 3  | Bot tidak mengarang data poin atau status member jika data belum tersedia. |
| 4  | Bot dapat meminta informasi tambahan jika fitur cek member diaktifkan.     |
| 5  | Bot dapat mengarahkan customer ke agent jika data member tidak tersedia.   |

---

# 15. Detail Fitur: Hubungi Agent

## 15.1 Deskripsi

Customer dapat meminta bantuan agent kapan saja. Jika customer meminta agent, bot tidak boleh memaksa customer menyelesaikan flow bot.

## 15.2 Intent

```text
talk_to_agent
```

## 15.3 Contoh Input Customer

```text
Saya mau bicara dengan admin.
Hubungkan ke CS.
Agent dong.
Saya mau komplain.
Manusia aja.
```

## 15.4 Alur

```text
Customer meminta agent
↓
Bot mengonfirmasi akan menghubungkan ke agent
↓
Conversation diteruskan ke agent outlet
↓
Bot berhenti membalas percakapan tersebut
```

## 15.5 Contoh Respons

```text
Baik Kak, saya hubungkan ke agent [Nama Outlet] agar bisa dibantu lebih lanjut.
Mohon tunggu sebentar ya.
```

## 15.6 Acceptance Criteria

| No | Kriteria                                                                |
| -- | ----------------------------------------------------------------------- |
| 1  | Customer dapat meminta agent menggunakan bahasa bebas.                  |
| 2  | Bot langsung mengarahkan ke agent jika customer meminta agent.          |
| 3  | Bot berhenti membalas setelah conversation masuk ke agent.              |
| 4  | Jika agent belum tersedia, customer mendapat pesan fallback yang jelas. |

---

# 16. Detail Fitur: Fallback Unknown Intent

## 16.1 Deskripsi

Fallback digunakan ketika bot tidak memahami kebutuhan customer atau kebutuhan customer tidak termasuk scope MVP.

## 16.2 Intent

```text
unknown
```

## 16.3 Contoh Respons

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

## 16.4 Acceptance Criteria

| No | Kriteria                                                    |
| -- | ----------------------------------------------------------- |
| 1  | Bot memberikan fallback jika tidak memahami pesan customer. |
| 2  | Fallback menampilkan daftar bantuan yang tersedia.          |
| 3  | Jika gagal berulang, bot menawarkan bantuan agent.          |
| 4  | Bot tidak menjawab di luar capability yang tersedia.        |

---

# 17. Detail Fitur: Timeout Fallback

## 17.1 Deskripsi

Jika bot membutuhkan waktu terlalu lama untuk merespons, customer tidak boleh dibiarkan menunggu tanpa kepastian. Jika respons bot melewati batas waktu yang ditentukan, conversation diarahkan ke agent.

## 17.2 Rule Produk

```text
Jika bot tidak dapat memberikan respons dalam waktu lebih dari 1 menit, customer diarahkan ke agent.
```

## 17.3 Contoh Respons

```text
Mohon maaf Kak, sistem kami membutuhkan waktu lebih lama dari biasanya.
Saya hubungkan Kakak ke agent agar bisa dibantu lebih cepat ya.
```

## 17.4 Acceptance Criteria

| No | Kriteria                                                                            |
| -- | ----------------------------------------------------------------------------------- |
| 1  | Customer tidak menunggu lebih dari batas waktu yang ditentukan tanpa respons.       |
| 2  | Jika timeout terjadi, customer mendapat pesan yang jelas.                           |
| 3  | Customer diarahkan ke agent saat timeout.                                           |
| 4  | Bot tidak mencoba melanjutkan jawaban otomatis setelah conversation masuk ke agent. |

---

# 18. Prinsip Perilaku Bot

Bot harus mengikuti prinsip berikut:

1. Bot membantu, bukan menggantikan agent.
2. Bot hanya menjawab dalam scope capability yang tersedia.
3. Bot menggunakan bahasa yang sopan, singkat, dan mudah dipahami customer.
4. Bot tidak menampilkan informasi internal.
5. Bot tidak mengarang data.
6. Bot tidak memproses pembayaran.
7. Bot tidak membuat atau mengubah ticket.
8. Bot hanya membaca status ticket.
9. Bot harus meminta data tambahan jika informasi customer belum lengkap.
10. Bot harus mengarahkan ke agent jika percakapan membutuhkan bantuan manusia.

---

# 19. Metrik Keberhasilan

| Metrik                                 | Tujuan                                        |
| -------------------------------------- | --------------------------------------------- |
| Jumlah conversation yang ditangani bot | Mengukur adopsi bot.                          |
| Jumlah cek status laundry berhasil     | Mengukur efektivitas fitur utama.             |
| Jumlah cek status ticket berhasil      | Mengukur efektivitas ticket lookup.           |
| Jumlah pertanyaan layanan outlet       | Mengukur kebutuhan informasi layanan.         |
| Jumlah fallback unknown                | Mengukur area yang belum dipahami bot.        |
| Jumlah handoff ke agent                | Mengukur kebutuhan eskalasi.                  |
| Bot deflection rate                    | Mengukur pengurangan beban agent.             |
| Timeout rate                           | Mengukur kestabilan pengalaman customer.      |
| Intent paling sering digunakan         | Menentukan prioritas pengembangan berikutnya. |

---

# 20. Open Questions

1. Apakah ID transaksi menjadi satu-satunya input wajib untuk cek status laundry?
2. Apakah customer boleh mencari transaksi berdasarkan nomor HP pada fase berikutnya?
3. Apakah status pembayaran sederhana boleh ditampilkan dalam status transaksi?
4. Apakah total transaksi boleh ditampilkan kepada customer?
5. Apakah alamat pengantaran boleh ditampilkan penuh atau perlu disamarkan?
6. Bagaimana format final status customer-friendly untuk setiap status transaksi?
7. Apakah layanan outlet perlu dikelompokkan berdasarkan kategori?
8. Apakah fitur member pada MVP hanya informasi umum atau termasuk cek poin?
9. Berapa maksimal fallback sebelum customer diarahkan ke agent?
10. Apakah conversation yang berhasil dijawab bot perlu ditutup otomatis?
11. Bagaimana pesan jika agent tidak tersedia?
12. Apakah customer bisa meminta ulang menu bantuan dengan mengetik “menu”?
13. Apakah customer bisa membatalkan flow dengan mengetik “batal”?
14. Apakah ticket status perlu dibatasi berdasarkan outlet atau cukup berdasarkan ID ticket?
15. Apakah customer boleh melihat update ticket yang bersifat internal?

---
