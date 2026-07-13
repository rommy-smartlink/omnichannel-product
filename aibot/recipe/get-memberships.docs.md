# Endpoint: daftar membership

## Overview

- **URL**: Perlu dikonfirmasi backend. Contoh pola yang dipakai produk: endpoint list membership dengan query outlet dari conversation context.
- **Handler**: Perlu dikonfirmasi backend.
- **Auth**: JWT Bearer Token (Header `Authorization: Bearer <token>`)
- **Description**: Mengambil daftar membership untuk outlet tertentu. Bot hanya menampilkan membership aktif dan relevan untuk outlet dari conversation context.

> **Catatan**: Nama endpoint/handler perlu dikonfirmasi dengan backend. Struktur response di bawah mengikuti contoh JSON yang tersedia.

---

## Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `idoutlet` | `string` | Yes | ID outlet yang ingin dicek membership-nya |

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
      "meta": {
        "id": "4b965696-8e99-4b47-aa0f-edcf8bb90c76",
        "name": "MEMBER AREMANIA",
        "created_by": "Owner",
        "period_type": "monthly",
        "register_fee": 1500,
        "active": true,
        "active_period_value": 1
      },
      "datetime": {
        "created_at": "2023-06-07T02:47:36Z",
        "updated_at": "2023-06-07T02:51:02Z"
      }
    }
  ]
}
```

---

## Example Data

```json
[
  {
    "meta": {
      "id": "db15f088-71c0-4f06-8cd6-5fb411bf246f",
      "name": "MEMBER HITAM",
      "created_by": "Owner",
      "period_type": "unlimited",
      "register_fee": 0,
      "active": true,
      "active_period_value": 0
    },
    "datetime": {
      "created_at": "2025-09-29T06:41:40Z",
      "updated_at": "2025-09-29T06:42:13Z"
    }
  },
  {
    "meta": {
      "id": "5e21846c-4170-4903-a4f9-8703fccd2d37",
      "name": "REAL WASH ONLY",
      "created_by": "Owner",
      "period_type": "unlimited",
      "register_fee": 1,
      "active": true,
      "active_period_value": 0
    },
    "datetime": {
      "created_at": "2024-03-19T06:19:04Z",
      "updated_at": "2024-08-20T04:02:56Z"
    }
  },
  {
    "meta": {
      "id": "f75bd5f8-1e17-41d4-900d-eaed2ad5e3cf",
      "name": "MEMBER GOLD",
      "created_by": "Owner",
      "period_type": "monthly",
      "register_fee": 0,
      "active": true,
      "active_period_value": 4
    },
    "datetime": {
      "created_at": "2024-02-22T09:54:49Z",
      "updated_at": "2025-02-03T07:23:10Z"
    }
  },
  {
    "meta": {
      "id": "8a9d584e-7068-43df-bc99-c88c02618426",
      "name": "MEMBER TERBARU",
      "created_by": "Owner",
      "period_type": "unlimited",
      "register_fee": 0,
      "active": true,
      "active_period_value": 0
    },
    "datetime": {
      "created_at": "2023-08-01T10:06:12Z",
      "updated_at": "2023-08-01T10:06:12Z"
    }
  },
  {
    "meta": {
      "id": "22244de9-15f0-4136-9736-ed3d0d24f6b6",
      "name": "MEMBER YELLOW",
      "created_by": "Owner",
      "period_type": "monthly",
      "register_fee": 0,
      "active": false,
      "active_period_value": 6
    },
    "datetime": {
      "created_at": "2023-07-24T09:21:50Z",
      "updated_at": "2025-11-14T12:03:59Z"
    }
  },
  {
    "meta": {
      "id": "4b965696-8e99-4b47-aa0f-edcf8bb90c76",
      "name": "MEMBER AREMANIA",
      "created_by": "Owner",
      "period_type": "monthly",
      "register_fee": 1500,
      "active": true,
      "active_period_value": 1
    },
    "datetime": {
      "created_at": "2023-06-07T02:47:36Z",
      "updated_at": "2023-06-07T02:51:02Z"
    }
  },
  {
    "meta": {
      "id": "a886249f-9bfa-4b5f-b28c-290d8f478ec6",
      "name": "ROMY GERUNK",
      "created_by": "Owner",
      "period_type": "yearly",
      "register_fee": 5000,
      "active": true,
      "active_period_value": 12
    },
    "datetime": {
      "created_at": "2023-05-08T03:15:40Z",
      "updated_at": "2024-03-28T02:58:36Z"
    }
  }
]
```

---

## Field Reference

### 1. `meta` — Membership Identity & Status

| Field | Type | Description |
|-------|------|-------------|
| `id` | `string` (UUID) | ID unik membership |
| `name` | `string` | Nama membership (customer-facing) |
| `created_by` | `string` | Pembuat membership; internal, jangan tampilkan ke customer |
| `period_type` | `string` | Tipe masa aktif: `unlimited`, `monthly`, `yearly` |
| `register_fee` | `number` | Biaya daftar membership |
| `active` | `boolean` | Status aktif membership |
| `active_period_value` | `number` | Nilai masa aktif berdasarkan `period_type` |

### 2. `datetime` — Audit Time

| Field | Type | Description |
|-------|------|-------------|
| `created_at` | `RFC3339` | Waktu membership dibuat; internal |
| `updated_at` | `RFC3339` | Waktu membership terakhir di-update; internal |

---

## Response — Tidak Ada Membership

```json
{
  "code": 200,
  "status": true,
  "msg": "Success",
  "reason": null,
  "data": []
}
```

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

### Internal Server Error (500)

```json
{
  "code": 500,
  "status": false,
  "msg": "Kesalahan sistem, harap hubungi pusat bantuan.",
  "reason": "error message",
  "data": null
}
```

---

## Filter Logic (Bot Layer)

Bot harus memfilter client-side setelah fetch:

1. `meta.active == true`
2. Membership relevan untuk outlet dari conversation context
3. Jika response server sudah outlet-scoped, tetap jangan tampilkan membership dari outlet lain

---

## Display Mapping

### Biaya Daftar

| Source | Customer Label |
|--------|----------------|
| `register_fee = 0` | `Biaya daftar: Gratis` |
| `register_fee > 0` | Tampilkan format rupiah Indonesia, misalnya `Biaya daftar: Rp1.500` |

### Masa Aktif

| Source | Customer Label |
|--------|----------------|
| `period_type = unlimited` | `Masa aktif: Tidak terbatas` |
| `period_type = monthly`, `active_period_value = 1` | `Masa aktif: 1 bulan` |
| `period_type = monthly`, `active_period_value > 1` | Tampilkan jumlah bulan sesuai nilai API, misalnya `Masa aktif: 4 bulan` |
| `period_type = yearly`, `active_period_value = 12` | `Masa aktif: 1 tahun` |
| `period_type = yearly`, nilai lain | Tampilkan jumlah bulan sesuai nilai API jika value disimpan dalam bulan, misalnya `Masa aktif: 24 bulan` |

---

## Customer Output Example

```text
Membership aktif di Perkilo Premium Pejaten:

MEMBER HITAM
Biaya daftar: Gratis
Masa aktif: Tidak terbatas

REAL WASH ONLY
Biaya daftar: Rp1
Masa aktif: Tidak terbatas

MEMBER GOLD
Biaya daftar: Gratis
Masa aktif: 4 bulan

MEMBER TERBARU
Biaya daftar: Gratis
Masa aktif: Tidak terbatas

MEMBER AREMANIA
Biaya daftar: Rp1.500
Masa aktif: 1 bulan

...dan 1 membership lainnya.
```
