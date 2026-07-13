# Detail Promo General

```
GET /promo/9a0b31b8-5547-4d85-98a7-22cd6c6968aa
```

## Base URL

`{{host}}` (per environment)

## Path

`GET /promo/{id}`

## Path Parameter

| Nama | Tipe | Deskripsi |
|---|---|---|
| `id` | `string` (UUID) | ID promo yang ingin di-detailkan |

## Header (Wajib)

| Header | Tipe | Deskripsi |
|---|---|---|
| `Idowner` | `string` | ID owner. Dicek middleware global — jika kosong return **407**. |

## Field Description

### Promo Location (3 Tipe)

Field `promo_location.type` memiliki 3 nilai:

| Tipe | Nilai | Deskripsi |
|---|---|---|
| **All** | `"all"` | Promo berlaku di **semua outlet**. `idoutlet` boleh kosong. |
| **Specified (Only)** | `"only"` | Promo **hanya** berlaku di outlet yang tercantum di `idoutlet`. |
| **Except** | `"except"` | Promo berlaku di **semua outlet kecuali** yang tercantum di `idoutlet`. |

### Terms Current

`terms.terms_current` (dan `terms.terms_past` — struktur sama) mendefinisikan syarat berlaku promo.

| Field | Tipe | Deskripsi |
|---|---|---|
| `applied_for` | `string` | Target promo. Nilai: `"transaction"` (semua item), `"service"` (jasa saja), `"product"` (produk saja). |
| `service.type` | `string` | Filter layanan. Nilai: `"all"` (semua layanan), `"only"` (hanya layanan tertentu), `"except"` (kecuali layanan tertentu). |
| `service.value` | `[]string` | ID layanan (terisi jika type `"only"` / `"except"`). |
| `service.name` | `[]string` | Nama layanan (read-only, di-enrich). |
| `product.type` | `string` | Filter produk. Nilai: `"all"`, `"only"`, `"except"`. |
| `product.value` | `[]string` | ID produk (terisi jika type `"only"` / `"except"`). |
| `product.name` | `[]string` | Nama produk (read-only, di-enrich). |
| `minimal_value` | `float64` | Minimal nilai transaksi (Rp) agar promo berlaku. |
| `minimal_qty` | `float64` | Minimal qty item agar promo berlaku. |

### Promo User

`terms.promo_user` mendefinisikan target pelanggan promo.

| Field | Tipe | Deskripsi |
|---|---|---|
| `type` | `string` | Nilai: `"all"` (semua pelanggan), `"member"` (hanya member tertentu). |
| `member.type` | `string` | Filter member. Nilai: `"all"`, `"selected"`. |
| `member.value` | `[]string` | ID member (terisi jika type `"selected"`). |
| `member.name` | `[]string` | Nama member (read-only, di-enrich). |

### Promo Payment Method

`terms.promo_payment_method` — daftar metode pembayaran yang memenuhi syarat. Nilai umum: `"cash"`, `"qris"`, `"edc"`, `"transfer"`, `"epay"`.

### Boolean Flags pada Terms

| Field | Tipe | Deskripsi |
|---|---|---|
| `allow_multiply` | `bool` | Boleh digabung dengan promo lain. |
| `combine_promo` | `bool` | Bisa dikombinasikan. |
| `use_physical_receipt` | `bool \| null` | Wajib struk fisik. |
| `use_id_card` | `bool \| null` | Wajib KTP. |
| `must_paid_full` | `bool \| null` | Wajib bayar lunas. |
| `attachment_evidence` | `bool` | Wajib lampirkan bukti. |
| `post_evidence` | `[]string` | Jenis bukti yang wajib dilampirkan. |

## Response Sukses (200 OK)

```json
{
  "code": 200,
  "status": true,
  "msg": "successfully get detail promo data",
  "data": {
    "id": "9a0b31b8-5547-4d85-98a7-22cd6c6968aa",
    "active": true,
    "name": "string",
    "period_date_start": "2026-01-01T00:00:00Z",
    "period_date_end": "2026-12-31T23:59:59Z",
    "period_time_start": "08:00",
    "period_time_end": "22:00",
    "quota": 1000,
    "idowner": "string",
    "idkaryawan": "string",
    "promo_application": "string",
    "promo_location": {
      "type": "all|selected",
      "idoutlet": ["string"],
      "outlet_name": ["string"]
    },
    "promo_type": "string",
    "promo_additional_setting": {
      "customer_profile": {
        "type": "string",
        "name_include": "string"
      },
      "accumulation": {
        "total_accumulation": 0,
        "start_date": "2026-01-01T00:00:00Z"
      }
    },
    "repeat_promotion": {
      "active": false,
      "type": null,
      "config": []
    },
    "benefit_type": "string",
    "benefit": {
      "general": {
        "discount": {
          "active": false,
          "discount_for": "string",
          "product": { "type": "string", "value": [], "name": [] },
          "service": { "type": "string", "value": [], "name": [] },
          "discount_type": "percent|nominal",
          "discount_value": 0,
          "max_discount": 0
        },
        "free_service": {
          "active": false,
          "idlayanan": "string",
          "qty": 0,
          "name": "string",
          "unit_name": "string"
        },
        "free_inventory": {
          "active": false,
          "idinventory_item": "string",
          "qty": 0,
          "name": "string",
          "unit_name": "string"
        },
        "free_gift": {
          "active": false,
          "gift_name": "string",
          "qty": 0,
          "unit_name": "string"
        },
        "voucher": {
          "active": false,
          "idvoucher": "string",
          "name": "string"
        }
      },
      "payment": {
        "qris": {
          "active": false,
          "benefit_type": "string",
          "benefit": { "discount": {}, "free_service": {}, "free_inventory": {}, "free_gift": {}, "voucher": {} }
        },
        "edc": { "active": false, "benefit_type": "string", "benefit": {} },
        "transfer": { "active": false, "benefit_type": "string", "benefit": {} },
        "epay": { "active": false, "benefit_type": "string", "benefit": {} },
        "cash": { "active": false, "benefit_type": "string", "benefit": {} }
      },
      "member": [
        {
          "idmember": "string",
          "name": "string",
          "active": true,
          "benefit_type": "string",
          "benefit": { "discount": {}, "free_service": {}, "free_inventory": {}, "free_gift": {}, "voucher": {} }
        }
      ]
    },
    "terms": {
      "terms_current": {
        "applied_for": "transaction|service|product",
        "service": { "type": "all|selected", "value": [], "name": [] },
        "product": { "type": "all|selected", "value": [], "name": [] },
        "minimal_value": 0,
        "minimal_qty": 0
      },
      "terms_past": {
        "applied_for": "transaction|service|product",
        "service": { "type": "all|selected", "value": [], "name": [] },
        "product": { "type": "all|selected", "value": [], "name": [] },
        "minimal_value": 0,
        "minimal_qty": 0
      },
      "allow_multiply": false,
      "promo_user": {
        "type": "all|member",
        "member": { "type": "all|selected", "value": [], "name": [] }
      },
      "promo_payment_method": ["string"],
      "combine_promo": false,
      "use_physical_receipt": null,
      "use_id_card": null,
      "must_paid_full": null,
      "post_evidence": ["string"],
      "attachment_evidence": false
    },
    "created_at": "2026-01-01T00:00:00Z",
    "updated_at": "2026-01-01T00:00:00Z",
    "deleted_at": null,
    "is_expired": false,
    "on_going": "string",
    "creator_name": "string",
    "count_transaction": 0
  }
}
```

## Error Responses

| HTTP Code | Kondisi | Kode Internal |
|---|---|---|
| **401** | `Idowner` header kosong | `ActionForbiden` |
| **404** | Promo tidak ditemukan (data terhapus / typo) | `DataNotFound` |
| **500** | Gagal query ES / parsing / enrich data | `DataBaseError` / `ParsingError` |
| **407** | `Idowner` header tidak dikirim (middleware global) | — |

### Format Error

```json
{
  "code": 404,
  "status": false,
  "reason": "sorry, promo data not found!. maybe you are typo or the data have been deleted",
  "data": null
}
```

## Call Chain

```
[Middlewares Global] → [Handler] → [UseCase] → [Data/ES] → Response
```

## Catatan

- **Middleware global** memvalidasi `Idowner` header, return 407 jika absent.
- **Idowner** juga dipakai sebagai filter di query ES.
- Field **is_expired**, **on_going**, **outlet_name**, **creator_name** adalah computed fields — di-enrich di layer usecase, bukan dari database.
- Sumber data: **Elasticsearch** index promo general.
