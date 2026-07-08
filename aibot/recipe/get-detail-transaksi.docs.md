# Endpoint: `GET /masterData/meta/detail_data_transaksi`

## Overview
- **URL**: `/masterData/meta/detail_data_transaksi?idtransaksi={idtransaksi}`
- **Handler**: `transaksi.DetailRincianTransaksi`
- **Auth**: JWT Bearer Token (Header `Authorization: Bearer <token>`)

---

## Response Structure

```json
{
  "code": 200,
  "status": true,
  "msg": "Berhasil mendapatkan data detail nota transaksi.",
  "reason": null,
  "data": {
    "detail_transaksi": { ... },
    "rincian_pembayaran": [ ... ],
    "rincian_layanan": [ ... ],
    "data_refund": [ ... ],
    "produk": [ ... ],
    "item": [ ... ],
    "promo": { ... }
  }
}
```

---

## 1. `detail_transaksi` — Object

| Field | Type | Description |
|-------|------|-------------|
| `idoutlet` | `string` | ID Outlet |
| `nama_outlet` | `string` | Nama Outlet |
| `idtransaksi` | `string` | ID Transaksi |
| `idtransaksi_lama` | `string` | ID Transaksi lama (jika ada) |
| `status_bayar` | `int` | Status bayar (0/1) |
| `lunas` | `int` | Status lunas |
| `persensatase_pengerjaan` | `float64` | Persentase pengerjaan (%) |
| `nama_karyawan` | `string` | Nama karyawan kasir |
| `nama_konsumen` | `string` | Nama konsumen/pelanggan |
| `idcustomer` | `string` | ID customer |
| `telp_konsumen` | `string` | Telepon konsumen |
| `tanggal_diterima` | `string` | Tanggal diterima (format Indonesia) |
| `tanggal_selesai` | `string` | Tanggal selesai (format Indonesia) |
| `tanggal_selesai_pengerjaan` | `string` | Tanggal selesai pengerjaan (format Indonesia) |
| `tanggal_diterima_raw` | `time.Time` | Tanggal diterima (Unix timestamp) |
| `tanggal_selesai_raw` | `time.Time` | Tanggal selesai (Unix timestamp) |
| `tanggal_selesai_pengerjaan_raw` | `time.Time` | Tanggal selesai pengerjaan (Unix timestamp) |
| `pajak_nominal` | `float64` | Nominal pajak |
| `pajak_persen` | `float64` | Persentase pajak |
| `parfum` | `string` | Nama parfum |
| `diskon_nominal` | `float64` | Diskon nominal |
| `diskon_persen` | `float64` | Diskon persen |
| `total` | `float64` | Total transaksi |
| `grandtotal` | `float64` | Grand total |
| `biaya_kirim` | `float64` | Biaya kirim/pengantaran |
| `biaya_service` | `float64` | Biaya service |
| `biaya_tambah` | `float64` | Biaya tambah |
| `tarif_tambahan` | `float64` | Tarif tambahan |
| `tarif_kali` | `float64` | Tarif kali |
| `tipe_bayar` | `int` | Tipe pembayaran |
| `isantar` | `int` | Apakah antar (0/1) |
| `status_ambil` | `int` | Status ambil |
| `status_selesai` | `int` | Status selesai |
| `status_hapus` | `int` | Status hapus |
| `is_pengantaran_extra` | `int` | Apakah pengantaran extra (0/1) |
| `is_jemput` | `int` | Apakah jemput (0/1) |
| `pengantaran` | `object` | Data pengantaran |
| `pengantaran_extra` | `object` | Data pengantaran extra |
| `pengantaran_extra_bayar` | `array` | Pembayaran pengantaran extra |
| `jadwal_jemput_bayar` | `object\|null` | Pembayaran jadwal jemput |
| `jadwal_jemput` | `object` | Data jadwal jemput |
| `status_transaksi_format` | `string` | Status transaksi dalam format string |
| `keterangan_permintaan_hapus` | `string` | Keterangan permintaan hapus |
| `idpermintaan_hapus` | `int\|null` | ID permintaan hapus |
| `keterangan` | `string` | Keterangan transaksi |
| `transaksi_foto` | `array` | Foto transaksi |
| `bukti_ambil_foto` | `array` | Foto bukti ambil |
| `transaksi_loyalty` | `object\|null` | Data loyalty |

### 1a. `pengantaran` — Object
```json
{
  "isantar": 1,
  "status_antar": 2,
  "tanggal_antar": "2024-06-26T16:08:54Z",
  "alamat": "Jl. Contoh No. 1",
  "nama_kurir": "Budi Kurir"
}
```

| Field | Type |
|-------|------|
| `isantar` | `int` |
| `status_antar` | `int` |
| `tanggal_antar` | `time.Time\|null` |
| `alamat` | `string` |
| `nama_kurir` | `string` |

### 1b. `pengantaran_extra` — Object
```json
{
  "karyawan": "Nama Karyawan",
  "idkaryawan": "KAR001",
  "idpengantaranextra": "AE001",
  "idtransaksi": "TMD260624160854112",
  "waktu": "26 Juni 2024 16:08",
  "waktu_raw": "2024-06-26T16:08:54Z",
  "biaya": 15000,
  "alamat": "Jl. Contoh No. 1",
  "courier_type": "instant",
  "expedition_name": "Gojek",
  "transportation_type": "motorcycle",
  "courier_code": "go-send",
  "vendor_courier_type": "",
  "vendor_courier_company": "",
  "vendor_courier_id": "",
  "shipment_raw_data": {}
}
```

### 1c. `jadwal_jemput` — Object
```json
{
  "karyawan": "Nama Karyawan",
  "idkaryawan": "KAR001",
  "idjemput": "JJ001",
  "waktu": "26 Juni 2024 16:08",
  "waktu_raw": "2024-06-26T16:08:54Z",
  "biaya": 10000,
  "alamat": "Jl. Contoh No. 1",
  "jenis_kurir": "motor",
  "nama_kurir": "Budi Kurir",
  "jenis_kendaraan": "motorcycle",
  "courier_type": "instant",
  "shipment_order_id": "SO123",
  "shipment_raw_data": {}
}
```

---

## 2. `rincian_pembayaran` — Array of Object

```json
[
  {
    "idbayar": 1,
    "idkaryawan": "KAR001",
    "nama_karyawan": "Nama Karyawan",
    "idoutlet": "OUT001",
    "jenis_bayar": 1,
    "tipe_bayar": 1,
    "kuota_awal": 0,
    "kuota_akhir": 0,
    "jumlah_beli": 0,
    "nominal": 50000,
    "dibayar": 50000,
    "kembalian": 0,
    "waktu_bayar": "26 Juni 2024 16:08",
    "waktu_bayar_raw": "2024-06-26T16:08:54Z",
    "nama_deposit": "",
    "satuan": "",
    "akun_keuangan": "",
    "idakun_keuangan": "",
    "edc_idprovider_customer": 0,
    "edc_nama_provider_customer": "",
    "edc_nama_mesin": "",
    "edc_idprovider_mesin": 0,
    "edc_nama_provider_mesin": "",
    "edc_biaya_transaksi": 0,
    "tf_bank_tujuan": "",
    "tf_no_rekening": "",
    "tf_pemilik_rekening": "",
    "rekening_owner": null,
    "masa_aktif_raw": null,
    "masa_aktif": null,
    "transaksi_foto": []
  }
]
```

### Notes by `jenis_bayar`:
| Jenis Bayar | Payment Type |
|------------|--------------|
| `1` | Cash |
| `2` | Qris |
| `3` | Transfer Bank |
| `4` | E-Money (GoPay/OVO/dll) |
| `5` | Deposit |
| `11` | EDC |
| Others | Voucher/dll |

---

## 3. `rincian_layanan` — Array of Object

```json
[
  {
    "nama_layanan": "Cuci Kering",
    "jumlah_beli": 2,
    "satuan": "pcs",
    "harga_satuan": 15000,
    "harga_total": 30000,
    "total_load": 0,
    "iddetlayanan": "DL001"
  }
]
```

| Field | Type | Description |
|-------|------|-------------|
| `nama_layanan` | `string` | Nama layanan |
| `jumlah_beli` | `float32` | Jumlah beli |
| `satuan` | `string` | Satuan (pcs, kg, dll) |
| `harga_satuan` | `float64` | Harga per satuan |
| `harga_total` | `float64` | Total harga |
| `total_load` | `int` | Total load (berat, untuk laundry kg) |
| `iddetlayanan` | `string` | ID detail layanan |

---

## 4. `data_refund` — Array of Object

```json
[
  {
    "title": "Tunai",
    "subtitle": "Di-refund dari Kas Laci",
    "jenis_bayar": 1,
    "total": 50000,
    "satuan": ""
  }
]
```

---

## 5. `produk` — Array (from Inventory Service)

Produk terkait transaksi dari service inventory.

---

## 6. `item` — Array (from Inventory Service)

Item terkait transaksi dari service inventory.

---

## 7. `promo` — Object (from Promo Service)

Detail promo yang berlaku pada transaksi.

---

## Error Responses

### Unauthorized (401)
```json
{
  "code": 401,
  "status": false,
  "msg": "Authorized failed",
  "reason": "...",
  "data": null
}
```

### Bad Request (400)
```json
{
  "code": 400,
  "status": false,
  "msg": "Error Invalid Data Parameter (idtransaksi), hubungi pusat bantuan.",
  "reason": "idtransaksi wajib disertakan",
  "data": null
}
```

### Internal Server Error (500)
```json
{
  "code": 500,
  "status": false,
  "msg": "...",
  "reason": "error message",
  "data": null
}
```