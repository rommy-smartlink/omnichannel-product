# Aturan Perilaku Bot

- Jangan pernah tampilkan data internal (nama karyawan, customer ID, detail pembayaran mentah)
- Jangan proses pembayaran atau buat/ubah tiket
- Hanya baca status tiket
- Gunakan label status yang ramah customer (misal "Sedang dicuci" bukan kode internal)
- Fallback ke agen setelah repeated unknown intent
- Timeout eskalasi setelah 1 menit jika bot tidak bisa merespons

## Label Status Ramah Customer

| Internal State | Label untuk Customer |
|----------------|----------------------|
| New received | Laundry diterima outlet |
| In progress | Laundry sedang diproses |
| Washing | Sedang dicuci |
| Drying | Sedang dikeringkan |
| Ironing | Dalam proses setrika |
| Packing | Sedang dikemas |
| Completed | Siap diambil |
| Picked up | Sudah diambil |
| Cancelled | Transaksi dibatalkan |
| Problem | Perlu dicek oleh agen |
