# Endpoint: detail membership

## Overview

- **URL**: Perlu dikonfirmasi backend. Contoh pola yang dipakai produk: endpoint detail membership memakai ID membership dari hasil lookup list.
- **Handler**: Perlu dikonfirmasi backend.
- **Auth**: JWT Bearer Token (Header `Authorization: Bearer <token>`)
- **Description**: Mengambil detail membership berdasarkan ID membership. Dipakai ketika customer bertanya detail membership tertentu dari daftar membership aktif.

> **Catatan**: Nama endpoint/handler perlu dikonfirmasi dengan backend. Struktur response di bawah mengikuti contoh JSON yang tersedia.

---

## Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | `string` (UUID) | Yes | ID membership |

---

## Response Structure

```json
{
  "code": 200,
  "status": true,
  "msg": "Success",
  "reason": null,
  "data": {
    "id": "4b965696-8e99-4b47-aa0f-edcf8bb90c76",
    "name": "MEMBER AREMANIA",
    "idkaryawan": "",
    "idowner": "THEONE",
    "deleted_at": null,
    "created_at": "2023-06-07T02:47:36Z",
    "updated_at": "2023-06-07T02:51:02Z",
    "active": true,
    "active_period_type": 1,
    "active_period_value": 1,
    "active_period_day": 30,
    "register_fee_enable": true,
    "register_fee": 1500,
    "member_location": {
      "type": "all",
      "idoutlet": []
    },
    "description": "isuk isuk tuku lengo nyangking botol",
    "meta": {
      "created_by": "Owner",
      "outlet_name": null,
      "count_customer_usage": 2,
      "count_promo": 0
    }
  }
}
```

---

## Example Data

```json
{
  "id": "4b965696-8e99-4b47-aa0f-edcf8bb90c76",
  "name": "MEMBER AREMANIA",
  "idkaryawan": "",
  "idowner": "THEONE",
  "deleted_at": null,
  "created_at": "2023-06-07T02:47:36Z",
  "updated_at": "2023-06-07T02:51:02Z",
  "active": true,
  "active_period_type": 1,
  "active_period_value": 1,
  "active_period_day": 30,
  "register_fee_enable": true,
  "register_fee": 1500,
  "member_location": {
    "type": "all",
    "idoutlet": []
  },
  "description": "isuk isuk tuku lengo nyangking botol",
  "meta": {
    "created_by": "Owner",
    "outlet_name": null,
    "count_customer_usage": 2,
    "count_promo": 0
  }
}
```

---

## Field Reference

### 1. Root — Membership Detail

| Field | Type | Description |
|-------|------|-------------|
| `id` | `string` (UUID) | ID unik membership |
| `name` | `string` | Nama membership (customer-facing) |
| `idkaryawan` | `string` | ID karyawan; internal, jangan tampilkan ke customer |
| `idowner` | `string` | ID owner; internal, jangan tampilkan ke customer |
| `deleted_at` | `RFC3339\|null` | Status soft delete; internal |
| `created_at` | `RFC3339` | Waktu dibuat; internal |
| `updated_at` | `RFC3339` | Waktu terakhir di-update; internal |
| `active` | `boolean` | Status aktif membership |
| `active_period_type` | `number` | Tipe masa aktif numerik; perlu mapping backend |
| `active_period_value` | `number` | Nilai masa aktif |
| `active_period_day` | `number` | Masa aktif dalam hari |
| `register_fee_enable` | `boolean` | Apakah biaya daftar aktif |
| `register_fee` | `number` | Biaya daftar membership |
| `description` | `string\|null` | Deskripsi membership |

### 2. `member_location` — Lokasi Membership

| Field | Type | Description |
|-------|------|-------------|
| `type` | `string` | Tipe lokasi: `all`, `only`, `except` |
| `idoutlet` | `array<string>` | Daftar outlet terkait membership |

### 3. `meta` — Internal Metadata

| Field | Type | Description |
|-------|------|-------------|
| `created_by` | `string` | Pembuat membership; internal |
| `outlet_name` | `string\|null` | Nama outlet jika tersedia |
| `count_customer_usage` | `number` | Jumlah customer pengguna; internal |
| `count_promo` | `number` | Jumlah promo terkait; internal |

---

## Display Mapping

### Biaya Daftar

| Source | Customer Label |
|--------|----------------|
| `register_fee_enable = false` | skip biaya daftar atau `Biaya daftar: Gratis` jika perlu menjawab biaya |
| `register_fee_enable = true`, `register_fee = 0` | `Biaya daftar: Gratis` |
| `register_fee_enable = true`, `register_fee > 0` | Tampilkan format rupiah Indonesia, misalnya `Biaya daftar: Rp1.500` |

### Masa Aktif

| Source | Customer Label |
|--------|----------------|
| `active_period_value = 0` | `Masa aktif: Tidak terbatas` |
| `active_period_value = 1`, `active_period_day = 30` | `Masa aktif: 1 bulan (±30 hari)` |
| `active_period_day > 0` | Tampilkan masa aktif sesuai nilai API, misalnya `Masa aktif: 1 bulan (±30 hari)` |

> Mapping `active_period_type` perlu dikonfirmasi backend. Untuk MVP, gunakan `active_period_value` + `active_period_day` bila tersedia.

### Lokasi

| Source | Customer Label |
|--------|----------------|
| `member_location.type = all` | `Berlaku di semua outlet` |
| `member_location.type = only` + `meta.outlet_name` | Tampilkan nama outlet dari API, misalnya `Outlet: Perkilo Premium Pejaten` |
| `member_location.type = except` | `Berlaku di semua outlet kecuali outlet tertentu` |

---

## Customer Output Example

```text
MEMBER AREMANIA
Biaya daftar: Rp1.500
Masa aktif: 1 bulan (±30 hari)
Berlaku di semua outlet

Deskripsi:
isuk isuk tuku lengo nyangking botol
```

---

## Error Responses

### Not Found (404)

```json
{
  "code": 404,
  "status": false,
  "msg": "Not Found",
  "reason": "membership not found",
  "data": null
}
```

Bot response:

```text
Maaf Kak, membership sudah tidak tersedia.
```

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

Bot response untuk error/timeout:

```text
Mohon maaf, sistem sedang gangguan. Saya hubungkan Kakak ke agent ya.
```

---

## Bot Lookup Logic

1. Customer tanya detail membership dengan nama tertentu.
2. Bot fetch list membership jika belum ada cache sesi.
3. Bot cari nama membership secara case-insensitive dan toleran typo ringan.
4. Jika match → ambil `meta.id`.
5. Fetch detail membership dengan ID tersebut.
6. Tampilkan detail customer-friendly.
7. Jangan tampilkan ID, owner, karyawan, audit time, usage count, atau promo count.
