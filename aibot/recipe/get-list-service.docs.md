# Endpoint: `GET /masterData/meta/list_layanandefault_meta`

## Overview
- **URL**: `/masterData/meta/list_layanandefault_meta?idoutlet={idoutlet}&order_by=ASC&sort_by=nama_layanan&limit=20&offset=0`
- **Handler**: `masterData.GetDefaultLayanan`
- **Auth**: JWT Bearer Token (Header `Authorization: Bearer <token>`)
- **Description**: Mengambil daftar layanan default yang tersedia untuk outlet tertentu berdasarkan data harga layanan dan relasinya dengan layanan.

---

## Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `idoutlet` | `string` | Yes | ID outlet yang ingin diambil daftar layanannya |
| `key` | `string` | No | Filter pencarian berdasarkan nama layanan |
| `search` | `string` | No | Alias pencarian yang sama dengan `key` |
| `sort_by` | `string` | No | Kolom yang dipakai untuk sorting, misalnya `nama_layanan` |
| `order_by` | `string` | No | Urutan sorting, `ASC` atau `DESC` |
| `limit` | `int` | No | Batas jumlah data yang dikembalikan, default `20` |
| `offset` | `int` | No | Offset data, default `0` |

---

## Response Structure

```json
{
  "code": 200,
  "status": true,
  "msg": "Success",
  "reason": null,
  "data": [
    {
      "outlet_idoutlet": "OTL17781198865288",
      "harga": 15000,
      "layanan_idlayanan": "LAY001",
      "min_order_reg": 0,
      "min_order_depo": 0,
      "use_inventory": 0,
      "layanan": {
        "idlayanan": "LAY001",
        "nama_layanan": "Cuci Kering",
        "owner_idowner": "OWN001",
        "outlet_idoutlet": "OTL17781198865288",
        "satuan": {
          "iddata_satuan": 1,
          "nama": "pcs",
          "jenis_satuan": 1
        }
      },
      "snap": {
        "use_snapbrige": false,
        "mesin_use": ""
      }
    }
  ],
  "lenght_data": 20
}
```

---

## 1. `data` — Array of Object

### 1a. Object `HargaLayanan`

| Field | Type | Description |
|-------|------|-------------|
| `outlet_idoutlet` | `string` | ID outlet pemilik harga layanan |
| `harga` | `float32` | Harga layanan untuk outlet tertentu |
| `layanan_idlayanan` | `string` | ID layanan yang terhubung |
| `min_order_reg` | `float32` | Minimal order untuk regular |
| `min_order_depo` | `float32` | Minimal order untuk depo |
| `use_inventory` | `int` | Flag apakah layanan memakai inventory |
| `layanan` | `object` | Data detail layanan terkait |
| `snap` | `object` | Informasi snap/mesin yang dipakai layanan |

### 1b. `layanan` — Object

| Field | Type | Description |
|-------|------|-------------|
| `idlayanan` | `string` | ID layanan |
| `nama_layanan` | `string` | Nama layanan |
| `owner_idowner` | `string` | ID owner pemilik layanan |
| `outlet_idoutlet` | `string` | ID outlet terkait layanan |
| `satuan` | `object` | Data satuan layanan |

### 1c. `satuan` — Object

| Field | Type | Description |
|-------|------|-------------|
| `iddata_satuan` | `int` | ID satuan |
| `nama` | `string` | Nama satuan (mis. `pcs`, `kg`) |
| `jenis_satuan` | `int` | Tipe satuan |

### 1d. `snap` — Object

| Field | Type | Description |
|-------|------|-------------|
| `use_snapbrige` | `bool` | Apakah layanan memakai Snapbridge |
| `mesin_use` | `string` | Mesin yang dipakai, mis. `Cuci`, `Pengering`, atau `Cuci & Pengering` |

---

## 2. `lenght_data` — Integer

| Field | Type | Description |
|-------|------|-------------|
| `lenght_data` | `int` | Total jumlah data hasil pencarian sebelum pagination diterapkan |

---

## Error Responses

### Unauthorized (401)
```json
{
  "code": 401,
  "status": false,
  "msg": "Authorized failed",
  "reason": "Authorized failed",
  "data": null
}
```

### Bad Request / Internal Error (500)
```json
{
  "code": 500,
  "status": false,
  "msg": "Keslahan sistem, harap hubungi pusat bantuan. Error : ( idoutlet tidak dicantumkan )",
  "reason": "idoutlet wajib disertakan",
  "data": null
}
```

### Internal Server Error (500)
```json
{
  "code": 500,
  "status": false,
  "msg": "Keslahan sistem, harap hubungi pusat bantuan. Error : (error message)",
  "reason": "error message",
  "data": null
}
```
