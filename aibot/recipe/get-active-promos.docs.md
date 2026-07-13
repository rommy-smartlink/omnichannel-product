# Endpoint: `GET /api/v1/promo/list`

## Overview
- **URL**: `/api/v1/promo/list?idoutlet={idoutlet}`
- **Handler**: `promo.GetPromoList`
- **Auth**: JWT Bearer Token (Header `Authorization: Bearer <token>`)
- **Description**: Mengambil daftar promo aktif yang terdaftar untuk outlet tertentu. Filter server-side: `is_active=true`, `is_expired=false`, `on_going` bukan `00_finished`.

---

## Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `idoutlet` | `string` | Yes | ID outlet yang ingin dicek promonya |

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
      "metadata": {
        "id": "9a0b31b8-5547-4d85-98a7-22cd6c6968aa",
        "name": "MEMBER PEJA 5%",
        "type": "member",
        "quota": 1000,
        "is_active": true,
        "on_going": "21_started",
        "is_expired": false,
        "location_type": "only",
        "location": [
          {
            "idoutlet": "OTL17653366966506",
            "name": "Perkilo Premium Pejaten"
          }
        ],
        "benefit_type": "member"
      },
      "datetime": {
        "created_at": "2026-05-15T08:23:19.618297926Z",
        "period": {
          "start": "2026-01-01T08:16:00Z",
          "end": "2027-01-01T08:17:00Z"
        }
      },
      "benefit": {
        "general": null,
        "member": [
          {
            "active": true,
            "benefit": {
              "discount": {
                "active": true,
                "discount_for": "service",
                "discount_type": "percent",
                "discount_value": 0.05,
                "max_discount": 0,
                "product": null,
                "service": {
                  "type": "except",
                  "value": [
                    "LYN23417653488026657",
                    "LYN28417653488022381",
                    "LYN29817653487932724",
                    "LYN41617653488028489",
                    "LYN50917653487914224",
                    "LYN57017653488028465",
                    "LYN59417653488111408",
                    "LYN71717653488027865",
                    "LYN86317653488028717",
                    "LYN89917653487929550",
                    "LYN16617653488091268",
                    "LYN76117653488027971",
                    "LYN83617653488023939",
                    "LYN70717653488026762"
                  ]
                }
              },
              "free_gift": null,
              "free_inventory": null,
              "free_service": null,
              "voucher": null
            },
            "benefit_type": "discount",
            "idmember": "f61284d1-3d0b-43b3-b5e9-c4c6df8888e6"
          }
        ],
        "payment": null
      },
      "terms": {
        "allow_multiply": false,
        "attachment_evidence": false,
        "combine_promo": false,
        "must_paid_full": false,
        "post_evidence": null,
        "promo_payment_method": ["cash", "transfer", "qris", "edc", "epayment"],
        "promo_user": {
          "member": { "type": "only", "value": ["f61284d1-3d0b-43b3-b5e9-c4c6df8888e6"] },
          "type": "member"
        },
        "terms_current": {
          "applied_for": "service",
          "minimal_qty": 0,
          "minimal_value": 0,
          "product": null,
          "service": {
            "type": "except",
            "value": [
              "LYN23417653488026657",
              "LYN28417653488022381",
              "LYN29817653487932724",
              "LYN41617653488028489",
              "LYN50917653487914224",
              "LYN57017653488028465",
              "LYN59417653488111408",
              "LYN71717653488027865",
              "LYN86317653488028717",
              "LYN89917653487929550",
              "LYN16617653488091268",
              "LYN76117653488027971",
              "LYN83617653488023939",
              "LYN70717653488026762"
            ]
          }
        },
        "terms_past": null,
        "use_id_card": false,
        "use_physical_receipt": false
      }
    }
  ]
}
```

> **Catatan**: Response `data` bisa berisi banyak objek promo. Lihat [Contoh Response Lengkap](#contoh-response-lengkap) di bawah.

---

## Field Reference

### 1. `metadata` — Promo Identity & Targeting

| Field | Type | Description |
|-------|------|-------------|
| `id` | `string` (UUID) | ID unik promo |
| `name` | `string` | Nama promo (customer-facing) |
| `type` | `string` | Jenis promo: `member`, `custom` |
| `quota` | `int` | Kuota total promo |
| `is_active` | `boolean` | Status aktif |
| `on_going` | `string` | Status siklus: `21_started` = berjalan, `00_finished` = selesai |
| `is_expired` | `boolean` | Status expired |
| `location_type` | `string` | Tipe lokasi: `only` = outlet tertentu, `all` = semua outlet |
| `location` | `array` | Daftar outlet tempat promo berlaku |
| `location[].idoutlet` | `string` | ID outlet |
| `location[].name` | `string` | Nama outlet |
| `benefit_type` | `string` | Tipe benefit: `member`, `general` |

### 2. `datetime` — Periode Waktu

| Field | Type | Description |
|-------|------|-------------|
| `created_at` | `RFC3339` | Waktu dibuat |
| `period.start` | `RFC3339` | Periode mulai promo |
| `period.end` | `RFC3339` | Periode selesai promo |

### 3. `benefit` — Benefit Promo

| Field | Type | Description |
|-------|------|-------------|
| `general` | `object\|null` | Benefit umum (untuk semua customer) |
| `general.discount.active` | `boolean` | Apakah diskon aktif |
| `general.discount.discount_for` | `string` | Sasaran diskon: `transaction_bill_total`, `service` |
| `general.discount.discount_type` | `string` | Tipe diskon: `percent`, `value` |
| `general.discount.discount_value` | `float64` | Nilai diskon (persen jika `percent`, nominal jika `value`) |
| `general.discount.max_discount` | `float64` | Maksimal diskon (0 = tidak terbatas) |
| `general.discount.product` | `object\|null` | Produk spesifik (null = semua produk) |
| `general.discount.service` | `object\|null` | Layanan spesifik (null = semua layanan) |
| `general.free_gift` | `object\|null` | Hadiah gratis |
| `general.free_inventory` | `object\|null` | Inventory gratis |
| `general.free_service` | `object\|null` | Layanan gratis |
| `general.voucher` | `object\|null` | Voucher |
| `member` | `array` | Benefit khusus member (lihat sub-struct di bawah) |
| `member[].benefit.discount` | `object\|null` | Sama struktur dengan `general.discount` |
| `member[].benefit_type` | `string` | Tipe benefit: `discount` |
| `member[].idmember` | `string` (UUID) | ID member |
| `payment` | `object\|null` | Benefit pembayaran (per metode) |

### 4. `terms` — Syarat & Ketentuan

| Field | Type | Description |
|-------|------|-------------|
| `allow_multiply` | `boolean` | Boleh digabung multiple promo |
| `combine_promo` | `boolean` | Boleh digabung promo lain |
| `must_paid_full` | `boolean` | Harus bayar penuh |
| `promo_payment_method` | `array` | Metode bayar yang berlaku |
| `promo_user.type` | `string` | Tipe user: `member`, `customer_member` |
| `promo_user.member` | `object\|null` | Filter member spesifik |
| `terms_current.applied_for` | `string` | Berlaku untuk: `service`, `transaction` |
| `terms_current.minimal_qty` | `float64` | Minimal quantity |
| `terms_current.minimal_value` | `float64` | Minimal transaksi (nominal) |
| `terms_current.product` | `object\|null` | Filter produk |
| `terms_current.service` | `object\|null` | Filter layanan (`type`: `include`/`except`, `value`: array ID) |

---

## Response — Tidak Ada Promo

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
1. `metadata.is_active == true`
2. `metadata.is_expired == false`
3. `metadata.on_going != "00_finished"`
4. `datetime.period.start <= now()`
5. `datetime.period.end >= now()`
6. `metadata.location` berisi outlet_id dari conversation context (atau `location_type == "all"`)
````
This is the description of what the code block changes:
<changeDescription>
Add full response example section with all 20 promo items
</changeDescription>

This is the code block that represents the suggested code change:
````mdc
---

## Contoh Response Lengkap

Berikut contoh response dengan 20 promo aktif dari berbagai outlet.

```json
{
  "code": 200,
  "status": true,
  "msg": "Success",
  "reason": null,
  "data": [
    {
      "metadata": {
        "id": "9a0b31b8-5547-4d85-98a7-22cd6c6968aa",
        "name": "MEMBER PEJA 5%",
        "type": "member",
        "quota": 1000,
        "is_active": true,
        "on_going": "21_started",
        "is_expired": false,
        "location_type": "only",
        "location": [
          {
            "idoutlet": "OTL17653366966506",
            "name": "Perkilo Premium Pejaten"
          }
        ],
        "benefit_type": "member"
      },
      "datetime": {
        "created_at": "2026-05-15T08:23:19.618297926Z",
        "period": {
          "start": "2026-01-01T08:16:00Z",
          "end": "2027-01-01T08:17:00Z"
        }
      },
      "benefit": {
        "general": null,
        "member": [
          {
            "active": true,
            "benefit": {
              "discount": {
                "active": true,
                "discount_for": "service",
                "discount_type": "percent",
                "discount_value": 0.05,
                "max_discount": 0,
                "product": null,
                "service": {
                  "type": "except",
                  "value": [
                    "LYN23417653488026657",
                    "LYN28417653488022381",
                    "LYN29817653487932724",
                    "LYN41617653488028489",
                    "LYN50917653487914224",
                    "LYN57017653488028465",
                    "LYN59417653488111408",
                    "LYN71717653488027865",
                    "LYN86317653488028717",
                    "LYN89917653487929550",
                    "LYN16617653488091268",
                    "LYN76117653488027971",
                    "LYN83617653488023939",
                    "LYN70717653488026762"
                  ]
                }
              },
              "free_gift": null,
              "free_inventory": null,
              "free_service": null,
              "voucher": null
            },
            "benefit_type": "discount",
            "idmember": "f61284d1-3d0b-43b3-b5e9-c4c6df8888e6"
          }
        ],
        "payment": null
      },
      "terms": {
        "allow_multiply": false,
        "attachment_evidence": false,
        "combine_promo": false,
        "must_paid_full": false,
        "post_evidence": null,
        "promo_payment_method": [
          "cash",
          "transfer",
          "qris",
          "edc",
          "epayment"
        ],
        "promo_user": {
          "member": {
            "type": "only",
            "value": [
              "f61284d1-3d0b-43b3-b5e9-c4c6df8888e6"
            ]
          },
          "type": "member"
        },
        "terms_current": {
          "applied_for": "service",
          "minimal_qty": 0,
          "minimal_value": 0,
          "product": null,
          "service": {
            "type": "except",
            "value": [
              "LYN23417653488026657",
              "LYN28417653488022381",
              "LYN29817653487932724",
              "LYN41617653488028489",
              "LYN50917653487914224",
              "LYN57017653488028465",
              "LYN59417653488111408",
              "LYN71717653488027865",
              "LYN86317653488028717",
              "LYN89917653487929550",
              "LYN16617653488091268",
              "LYN76117653488027971",
              "LYN83617653488023939",
              "LYN70717653488026762"
            ]
          }
        },
        "terms_past": null,
        "use_id_card": false,
        "use_physical_receipt": false
      }
    },
    {
      "metadata": {
        "id": "b4d6d74b-066d-4baa-906a-019f79c97ffa",
        "name": "VOUCHER GOOGLE KUNI",
        "type": "custom",
        "quota": 500,
        "is_active": true,
        "on_going": "21_started",
        "is_expired": false,
        "location_type": "only",
        "location": [
          {
            "idoutlet": "OTL17084974065021",
            "name": "Perkilo Premium Kuningan"
          }
        ],
        "benefit_type": "general"
      },
      "datetime": {
        "created_at": "2025-11-23T07:28:51.777582892Z",
        "period": {
          "start": "2025-11-24T00:00:00Z",
          "end": "2026-11-23T16:59:00Z"
        }
      },
      "benefit": {
        "general": {
          "discount": {
            "active": true,
            "discount_for": "transaction_bill_total",
            "discount_type": "value",
            "discount_value": 20000,
            "max_discount": 0,
            "product": null,
            "service": null
          },
          "free_gift": null,
          "free_inventory": null,
          "free_service": null,
          "voucher": null
        },
        "member": [],
        "payment": {
          "cash": null,
          "edc": null,
          "epay": null,
          "qris": null,
          "transfer": null
        }
      },
      "terms": {
        "allow_multiply": false,
        "attachment_evidence": false,
        "combine_promo": false,
        "must_paid_full": false,
        "post_evidence": null,
        "promo_payment_method": [
          "cash",
          "transfer",
          "qris",
          "edc",
          "epayment"
        ],
        "promo_user": {
          "member": {
            "type": "",
            "value": null
          },
          "type": "customer_member"
        },
        "terms_current": {
          "applied_for": "transaction",
          "minimal_qty": 0,
          "minimal_value": 20000,
          "product": null,
          "service": null
        },
        "terms_past": null,
        "use_id_card": false,
        "use_physical_receipt": false
      }
    },
    {
      "metadata": {
        "id": "053eca02-ae8f-40f9-8565-c54ef9c87605",
        "name": "VOUCHER GOOGLE SABA",
        "type": "custom",
        "quota": 500,
        "is_active": true,
        "on_going": "21_started",
        "is_expired": false,
        "location_type": "only",
        "location": [
          {
            "idoutlet": "OTL17464205689773",
            "name": "Perkilo Premium Sabang"
          }
        ],
        "benefit_type": "general"
      },
      "datetime": {
        "created_at": "2025-11-23T03:13:21.944648121Z",
        "period": {
          "start": "2025-11-24T00:00:00Z",
          "end": "2026-11-23T16:59:00Z"
        }
      },
      "benefit": {
        "general": {
          "discount": {
            "active": true,
            "discount_for": "transaction_bill_total",
            "discount_type": "value",
            "discount_value": 20000,
            "max_discount": 0,
            "product": null,
            "service": null
          },
          "free_gift": null,
          "free_inventory": null,
          "free_service": null,
          "voucher": null
        },
        "member": [],
        "payment": {
          "cash": null,
          "edc": null,
          "epay": null,
          "qris": null,
          "transfer": null
        }
      },
      "terms": {
        "allow_multiply": false,
        "attachment_evidence": false,
        "combine_promo": false,
        "must_paid_full": false,
        "post_evidence": null,
        "promo_payment_method": [
          "cash",
          "transfer",
          "qris",
          "edc",
          "epayment"
        ],
        "promo_user": {
          "member": {
            "type": "",
            "value": null
          },
          "type": "customer_member"
        },
        "terms_current": {
          "applied_for": "transaction",
          "minimal_qty": 0,
          "minimal_value": 20000,
          "product": null,
          "service": null
        },
        "terms_past": null,
        "use_id_card": false,
        "use_physical_receipt": false
      }
    },
    {
      "metadata": {
        "id": "c1736ce8-1059-488b-921c-27f85c1e2625",
        "name": "VOUCHER GOOGLE BUDI",
        "type": "custom",
        "quota": 500,
        "is_active": true,
        "on_going": "21_started",
        "is_expired": false,
        "location_type": "only",
        "location": [
          {
            "idoutlet": "OTL17202379114365",
            "name": "Perkilo Premium Setiabudi"
          }
        ],
        "benefit_type": "general"
      },
      "datetime": {
        "created_at": "2025-11-23T03:12:06.863790687Z",
        "period": {
          "start": "2025-11-24T00:00:00Z",
          "end": "2026-11-23T16:59:00Z"
        }
      },
      "benefit": {
        "general": {
          "discount": {
            "active": true,
            "discount_for": "transaction_bill_total",
            "discount_type": "value",
            "discount_value": 20000,
            "max_discount": 0,
            "product": null,
            "service": null
          },
          "free_gift": null,
          "free_inventory": null,
          "free_service": null,
          "voucher": null
        },
        "member": [],
        "payment": {
          "cash": null,
          "edc": null,
          "epay": null,
          "qris": null,
          "transfer": null
        }
      },
      "terms": {
        "allow_multiply": false,
        "attachment_evidence": false,
        "combine_promo": false,
        "must_paid_full": false,
        "post_evidence": null,
        "promo_payment_method": [
          "cash",
          "transfer",
          "qris",
          "edc",
          "epayment"
        ],
        "promo_user": {
          "member": {
            "type": "",
            "value": null
          },
          "type": "customer_member"
        },
        "terms_current": {
          "applied_for": "transaction",
          "minimal_qty": 0,
          "minimal_value": 20000,
          "product": null,
          "service": null
        },
        "terms_past": null,
        "use_id_card": false,
        "use_physical_receipt": false
      }
    },
    {
      "metadata": {
        "id": "a2d360d1-23bd-4a73-8480-920d31a1a44d",
        "name": "VOUCHER GOOGLE VETE",
        "type": "custom",
        "quota": 500,
        "is_active": true,
        "on_going": "21_started",
        "is_expired": false,
        "location_type": "only",
        "location": [
          {
            "idoutlet": "OTL17102212178175",
            "name": "Perkilo Premium Veteran"
          }
        ],
        "benefit_type": "general"
      },
      "datetime": {
        "created_at": "2025-11-23T03:10:39.993230584Z",
        "period": {
          "start": "2025-11-24T00:00:00Z",
          "end": "2026-11-23T16:59:00Z"
        }
      },
      "benefit": {
        "general": {
          "discount": {
            "active": true,
            "discount_for": "transaction_bill_total",
            "discount_type": "value",
            "discount_value": 20000,
            "max_discount": 0,
            "product": null,
            "service": null
          },
          "free_gift": null,
          "free_inventory": null,
          "free_service": null,
          "voucher": null
        },
        "member": [],
        "payment": {
          "cash": null,
          "edc": null,
          "epay": null,
          "qris": null,
          "transfer": null
        }
      },
      "terms": {
        "allow_multiply": false,
        "attachment_evidence": false,
        "combine_promo": false,
        "must_paid_full": false,
        "post_evidence": null,
        "promo_payment_method": [
          "cash",
          "transfer",
          "qris",
          "edc",
          "epayment"
        ],
        "promo_user": {
          "member": {
            "type": "",
            "value": null
          },
          "type": "customer_member"
        },
        "terms_current": {
          "applied_for": "transaction",
          "minimal_qty": 0,
          "minimal_value": 20000,
          "product": null,
          "service": null
        },
        "terms_past": null,
        "use_id_card": false,
        "use_physical_receipt": false
      }
    },
    {
      "metadata": {
        "id": "517d6054-2ded-4a0d-b22e-8e4a5fb843a4",
        "name": "VOUCHER GOOGLE CIRE",
        "type": "custom",
        "quota": 500,
        "is_active": true,
        "on_going": "21_started",
        "is_expired": false,
        "location_type": "only",
        "location": [
          {
            "idoutlet": "OTL16643713412240",
            "name": "Perkilo Premium Cirendeu"
          }
        ],
        "benefit_type": "general"
      },
      "datetime": {
        "created_at": "2025-11-23T03:09:27.957226779Z",
        "period": {
          "start": "2025-11-24T00:00:00Z",
          "end": "2026-11-23T16:59:00Z"
        }
      },
      "benefit": {
        "general": {
          "discount": {
            "active": true,
            "discount_for": "transaction_bill_total",
            "discount_type": "value",
            "discount_value": 20000,
            "max_discount": 0,
            "product": null,
            "service": null
          },
          "free_gift": null,
          "free_inventory": null,
          "free_service": null,
          "voucher": null
        },
        "member": [],
        "payment": {
          "cash": null,
          "edc": null,
          "epay": null,
          "qris": null,
          "transfer": null
        }
      },
      "terms": {
        "allow_multiply": false,
        "attachment_evidence": false,
        "combine_promo": false,
        "must_paid_full": false,
        "post_evidence": null,
        "promo_payment_method": [
          "cash",
          "transfer",
          "qris",
          "edc",
          "epayment"
        ],
        "promo_user": {
          "member": {
            "type": "",
            "value": null
          },
          "type": "customer_member"
        },
        "terms_current": {
          "applied_for": "transaction",
          "minimal_qty": 0,
          "minimal_value": 20000,
          "product": null,
          "service": null
        },
        "terms_past": null,
        "use_id_card": false,
        "use_physical_receipt": false
      }
    },
    {
      "metadata": {
        "id": "9fd16ac2-6e09-4b85-a38b-e1d28ba94b25",
        "name": "VOUCHER GOOGLE BSD",
        "type": "custom",
        "quota": 500,
        "is_active": true,
        "on_going": "21_started",
        "is_expired": false,
        "location_type": "only",
        "location": [
          {
            "idoutlet": "OTL16853749189854",
            "name": "Perkilo Premium BSD"
          }
        ],
        "benefit_type": "general"
      },
      "datetime": {
        "created_at": "2025-11-23T03:07:32.172991362Z",
        "period": {
          "start": "2025-11-24T00:00:00Z",
          "end": "2026-11-23T16:59:00Z"
        }
      },
      "benefit": {
        "general": {
          "discount": {
            "active": true,
            "discount_for": "transaction_bill_total",
            "discount_type": "value",
            "discount_value": 20000,
            "max_discount": 0,
            "product": null,
            "service": null
          },
          "free_gift": null,
          "free_inventory": null,
          "free_service": null,
          "voucher": null
        },
        "member": [],
        "payment": {
          "cash": null,
          "edc": null,
          "epay": null,
          "qris": null,
          "transfer": null
        }
      },
      "terms": {
        "allow_multiply": false,
        "attachment_evidence": false,
        "combine_promo": false,
        "must_paid_full": false,
        "post_evidence": null,
        "promo_payment_method": [
          "cash",
          "transfer",
          "qris",
          "edc",
          "epayment"
        ],
        "promo_user": {
          "member": {
            "type": "",
            "value": null
          },
          "type": "customer_member"
        },
        "terms_current": {
          "applied_for": "transaction",
          "minimal_qty": 0,
          "minimal_value": 20000,
          "product": null,
          "service": null
        },
        "terms_past": null,
        "use_id_card": false,
        "use_physical_receipt": false
      }
    },
    {
      "metadata": {
        "id": "46b36f5d-45e2-47cd-9940-1fd008204682",
        "name": "VOUCHER GOOGLE BULUS",
        "type": "custom",
        "quota": 500,
        "is_active": true,
        "on_going": "21_started",
        "is_expired": false,
        "location_type": "only",
        "location": [
          {
            "idoutlet": "OTL15957617649822",
            "name": "Perkilo Premium Lebak Bulus"
          }
        ],
        "benefit_type": "general"
      },
      "datetime": {
        "created_at": "2025-11-23T03:06:09.403054416Z",
        "period": {
          "start": "2025-11-24T00:00:00Z",
          "end": "2026-11-23T16:59:00Z"
        }
      },
      "benefit": {
        "general": {
          "discount": {
            "active": true,
            "discount_for": "transaction_bill_total",
            "discount_type": "value",
            "discount_value": 20000,
            "max_discount": 0,
            "product": null,
            "service": null
          },
          "free_gift": null,
          "free_inventory": null,
          "free_service": null,
          "voucher": null
        },
        "member": [],
        "payment": {
          "cash": null,
          "edc": null,
          "epay": null,
          "qris": null,
          "transfer": null
        }
      },
      "terms": {
        "allow_multiply": false,
        "attachment_evidence": false,
        "combine_promo": false,
        "must_paid_full": false,
        "post_evidence": null,
        "promo_payment_method": [
          "cash",
          "transfer",
          "qris",
          "edc",
          "epayment"
        ],
        "promo_user": {
          "member": {
            "type": "",
            "value": null
          },
          "type": "customer_member"
        },
        "terms_current": {
          "applied_for": "transaction",
          "minimal_qty": 0,
          "minimal_value": 20000,
          "product": null,
          "service": null
        },
        "terms_past": null,
        "use_id_card": false,
        "use_physical_receipt": false
      }
    },
    {
      "metadata": {
        "id": "45849ae4-5643-4698-9e27-83cd69524069",
        "name": "VOUCHER GOOGLE PINA",
        "type": "custom",
        "quota": 500,
        "is_active": true,
        "on_going": "21_started",
        "is_expired": false,
        "location_type": "only",
        "location": [
          {
            "idoutlet": "OTL16847280964460",
            "name": "Perkilo Premium Pinang"
          }
        ],
        "benefit_type": "general"
      },
      "datetime": {
        "created_at": "2025-11-23T03:04:40.663018577Z",
        "period": {
          "start": "2025-11-24T00:00:00Z",
          "end": "2026-11-23T16:59:00Z"
        }
      },
      "benefit": {
        "general": {
          "discount": {
            "active": true,
            "discount_for": "transaction_bill_total",
            "discount_type": "value",
            "discount_value": 20000,
            "max_discount": 0,
            "product": null,
            "service": null
          },
          "free_gift": null,
          "free_inventory": null,
          "free_service": null,
          "voucher": null
        },
        "member": [],
        "payment": {
          "cash": null,
          "edc": null,
          "epay": null,
          "qris": null,
          "transfer": null
        }
      },
      "terms": {
        "allow_multiply": false,
        "attachment_evidence": false,
        "combine_promo": false,
        "must_paid_full": false,
        "post_evidence": null,
        "promo_payment_method": [
          "cash",
          "transfer",
          "qris",
          "edc",
          "epayment"
        ],
        "promo_user": {
          "member": {
            "type": "",
            "value": null
          },
          "type": "customer_member"
        },
        "terms_current": {
          "applied_for": "transaction",
          "minimal_qty": 0,
          "minimal_value": 20000,
          "product": null,
          "service": null
        },
        "terms_past": null,
        "use_id_card": false,
        "use_physical_receipt": false
      }
    },
    {
      "metadata": {
        "id": "e8f0c346-1ce9-435a-b873-06622846a63c",
        "name": "VOUCHER GOOGLE RADA",
        "type": "custom",
        "quota": 500,
        "is_active": true,
        "on_going": "21_started",
        "is_expired": false,
        "location_type": "only",
        "location": [
          {
            "idoutlet": "OTL15306823801078",
            "name": "Perkilo Premium Radio Dalam"
          }
        ],
        "benefit_type": "general"
      },
      "datetime": {
        "created_at": "2025-11-23T03:03:23.052552113Z",
        "period": {
          "start": "2025-11-24T00:00:00Z",
          "end": "2026-11-23T16:59:00Z"
        }
      },
      "benefit": {
        "general": {
          "discount": {
            "active": true,
            "discount_for": "transaction_bill_total",
            "discount_type": "value",
            "discount_value": 20000,
            "max_discount": 0,
            "product": null,
            "service": null
          },
          "free_gift": null,
          "free_inventory": null,
          "free_service": null,
          "voucher": null
        },
        "member": [],
        "payment": {
          "cash": null,
          "edc": null,
          "epay": null,
          "qris": null,
          "transfer": null
        }
      },
      "terms": {
        "allow_multiply": false,
        "attachment_evidence": false,
        "combine_promo": false,
        "must_paid_full": false,
        "post_evidence": null,
        "promo_payment_method": [
          "cash",
          "transfer",
          "qris",
          "edc",
          "epayment"
        ],
        "promo_user": {
          "member": {
            "type": "",
            "value": null
          },
          "type": "customer_member"
        },
        "terms_current": {
          "applied_for": "transaction",
          "minimal_qty": 0,
          "minimal_value": 20000,
          "product": null,
          "service": null
        },
        "terms_past": null,
        "use_id_card": false,
        "use_physical_receipt": false
      }
    },
    {
      "metadata": {
        "id": "9c1c5e96-ec81-4e5b-ac53-4e12273bba9a",
        "name": "VOUCHER GOOGLE TRIO",
        "type": "custom",
        "quota": 500,
        "is_active": true,
        "on_going": "21_started",
        "is_expired": false,
        "location_type": "only",
        "location": [
          {
            "idoutlet": "OTL15266323387437",
            "name": "Perkilo Premium Satrio"
          }
        ],
        "benefit_type": "general"
      },
      "datetime": {
        "created_at": "2025-11-23T03:01:30.684152137Z",
        "period": {
          "start": "2025-11-24T00:00:00Z",
          "end": "2026-11-23T16:59:00Z"
        }
      },
      "benefit": {
        "general": {
          "discount": {
            "active": true,
            "discount_for": "transaction_bill_total",
            "discount_type": "value",
            "discount_value": 20000,
            "max_discount": 0,
            "product": null,
            "service": null
          },
          "free_gift": null,
          "free_inventory": null,
          "free_service": null,
          "voucher": null
        },
        "member": [],
        "payment": {
          "cash": null,
          "edc": null,
          "epay": null,
          "qris": null,
          "transfer": null
        }
      },
      "terms": {
        "allow_multiply": false,
        "attachment_evidence": false,
        "combine_promo": false,
        "must_paid_full": false,
        "post_evidence": null,
        "promo_payment_method": [
          "cash",
          "transfer",
          "qris",
          "edc",
          "epayment"
        ],
        "promo_user": {
          "member": {
            "type": "",
            "value": null
          },
          "type": "customer_member"
        },
        "terms_current": {
          "applied_for": "transaction",
          "minimal_qty": 0,
          "minimal_value": 20000,
          "product": null,
          "service": null
        },
        "terms_past": null,
        "use_id_card": false,
        "use_physical_receipt": false
      }
    },
    {
      "metadata": {
        "id": "b7f3207c-743f-45d0-868f-a71d12bbc872",
        "name": "VOUCHER GOOGLE DANO",
        "type": "custom",
        "quota": 500,
        "is_active": true,
        "on_going": "21_started",
        "is_expired": false,
        "location_type": "only",
        "location": [
          {
            "idoutlet": "OTL16607483302218",
            "name": "Perkilo Premium Tondano"
          }
        ],
        "benefit_type": "general"
      },
      "datetime": {
        "created_at": "2025-11-23T03:00:01.27431269Z",
        "period": {
          "start": "2025-11-24T00:00:00Z",
          "end": "2026-11-23T16:59:00Z"
        }
      },
      "benefit": {
        "general": {
          "discount": {
            "active": true,
            "discount_for": "transaction_bill_total",
            "discount_type": "value",
            "discount_value": 20000,
            "max_discount": 0,
            "product": null,
            "service": null
          },
          "free_gift": null,
          "free_inventory": null,
          "free_service": null,
          "voucher": null
        },
        "member": [],
        "payment": {
          "cash": null,
          "edc": null,
          "epay": null,
          "qris": null,
          "transfer": null
        }
      },
      "terms": {
        "allow_multiply": false,
        "attachment_evidence": false,
        "combine_promo": false,
        "must_paid_full": false,
        "post_evidence": null,
        "promo_payment_method": [
          "cash",
          "transfer",
          "qris",
          "edc",
          "epayment"
        ],
        "promo_user": {
          "member": {
            "type": "",
            "value": null
          },
          "type": "customer_member"
        },
        "terms_current": {
          "applied_for": "transaction",
          "minimal_qty": 0,
          "minimal_value": 20000,
          "product": null,
          "service": null
        },
        "terms_past": null,
        "use_id_card": false,
        "use_physical_receipt": false
      }
    },
    {
      "metadata": {
        "id": "cc149ca4-e27e-4fd0-ace2-5173fcfcf8e6",
        "name": "VOUCHER GOOGLE GONG",
        "type": "custom",
        "quota": 500,
        "is_active": true,
        "on_going": "21_started",
        "is_expired": false,
        "location_type": "only",
        "location": [
          {
            "idoutlet": "OTL15346864649131",
            "name": "Perkilo Premium Terogong"
          }
        ],
        "benefit_type": "general"
      },
      "datetime": {
        "created_at": "2025-11-23T02:58:39.189093712Z",
        "period": {
          "start": "2025-11-24T00:00:00Z",
          "end": "2026-11-23T16:59:00Z"
        }
      },
      "benefit": {
        "general": {
          "discount": {
            "active": true,
            "discount_for": "transaction_bill_total",
            "discount_type": "value",
            "discount_value": 20000,
            "max_discount": 0,
            "product": null,
            "service": null
          },
          "free_gift": null,
          "free_inventory": null,
          "free_service": null,
          "voucher": null
        },
        "member": [],
        "payment": {
          "cash": null,
          "edc": null,
          "epay": null,
          "qris": null,
          "transfer": null
        }
      },
      "terms": {
        "allow_multiply": false,
        "attachment_evidence": false,
        "combine_promo": false,
        "must_paid_full": false,
        "post_evidence": null,
        "promo_payment_method": [
          "cash",
          "transfer",
          "qris",
          "edc",
          "epayment"
        ],
        "promo_user": {
          "member": {
            "type": "",
            "value": null
          },
          "type": "customer_member"
        },
        "terms_current": {
          "applied_for": "transaction",
          "minimal_qty": 0,
          "minimal_value": 20000,
          "product": null,
          "service": null
        },
        "terms_past": null,
        "use_id_card": false,
        "use_physical_receipt": false
      }
    },
    {
      "metadata": {
        "id": "93be8586-6753-4634-85a1-5c09c0fc3c4a",
        "name": "VOUCHER GOOGLE PISS",
        "type": "custom",
        "quota": 500,
        "is_active": true,
        "on_going": "21_started",
        "is_expired": false,
        "location_type": "only",
        "location": [
          {
            "idoutlet": "OTL15348182614633",
            "name": "Perpiece Radio Dalam"
          }
        ],
        "benefit_type": "general"
      },
      "datetime": {
        "created_at": "2025-11-23T02:56:48.945674486Z",
        "period": {
          "start": "2025-11-24T00:00:00Z",
          "end": "2026-11-23T16:59:00Z"
        }
      },
      "benefit": {
        "general": {
          "discount": {
            "active": true,
            "discount_for": "transaction_bill_total",
            "discount_type": "value",
            "discount_value": 20000,
            "max_discount": 0,
            "product": null,
            "service": null
          },
          "free_gift": null,
          "free_inventory": null,
          "free_service": null,
          "voucher": null
        },
        "member": [],
        "payment": {
          "cash": null,
          "edc": null,
          "epay": null,
          "qris": null,
          "transfer": null
        }
      },
      "terms": {
        "allow_multiply": false,
        "attachment_evidence": false,
        "combine_promo": false,
        "must_paid_full": false,
        "post_evidence": null,
        "promo_payment_method": [
          "cash",
          "transfer",
          "qris",
          "edc",
          "epayment"
        ],
        "promo_user": {
          "member": {
            "type": "",
            "value": null
          },
          "type": "customer_member"
        },
        "terms_current": {
          "applied_for": "transaction",
          "minimal_qty": 0,
          "minimal_value": 20000,
          "product": null,
          "service": null
        },
        "terms_past": null,
        "use_id_card": false,
        "use_physical_receipt": false
      }
    },
    {
      "metadata": {
        "id": "545cd10c-95d9-4da3-8650-6c69e0fba283",
        "name": "VOUCHER GOOGLE NAWI",
        "type": "custom",
        "quota": 500,
        "is_active": true,
        "on_going": "21_started",
        "is_expired": false,
        "location_type": "only",
        "location": [
          {
            "idoutlet": "OTL16680674355521",
            "name": "Perkilo Premium Haji Nawi"
          }
        ],
        "benefit_type": "general"
      },
      "datetime": {
        "created_at": "2025-11-23T02:55:20.595640932Z",
        "period": {
          "start": "2025-11-24T00:00:00Z",
          "end": "2026-11-23T16:59:00Z"
        }
      },
      "benefit": {
        "general": {
          "discount": {
            "active": true,
            "discount_for": "transaction_bill_total",
            "discount_type": "value",
            "discount_value": 20000,
            "max_discount": 0,
            "product": null,
            "service": null
          },
          "free_gift": null,
          "free_inventory": null,
          "free_service": null,
          "voucher": null
        },
        "member": [],
        "payment": {
          "cash": null,
          "edc": null,
          "epay": null,
          "qris": null,
          "transfer": null
        }
      },
      "terms": {
        "allow_multiply": false,
        "attachment_evidence": false,
        "combine_promo": false,
        "must_paid_full": false,
        "post_evidence": null,
        "promo_payment_method": [
          "cash",
          "transfer",
          "qris",
          "edc",
          "epayment"
        ],
        "promo_user": {
          "member": {
            "type": "",
            "value": null
          },
          "type": "customer_member"
        },
        "terms_current": {
          "applied_for": "transaction",
          "minimal_qty": 0,
          "minimal_value": 20000,
          "product": null,
          "service": null
        },
        "terms_past": null,
        "use_id_card": false,
        "use_physical_receipt": false
      }
    },
    {
      "metadata": {
        "id": "e1677496-611d-4a51-8319-0e52b039ebd2",
        "name": "VOUCHER GOOGLE KATE",
        "type": "custom",
        "quota": 500,
        "is_active": true,
        "on_going": "21_started",
        "is_expired": false,
        "location_type": "only",
        "location": [
          {
            "idoutlet": "OTL16478377372373",
            "name": "Perkilo Premium Karang Tengah"
          }
        ],
        "benefit_type": "general"
      },
      "datetime": {
        "created_at": "2025-11-23T02:50:40.489884204Z",
        "period": {
          "start": "2025-11-24T00:00:00Z",
          "end": "2026-11-23T16:59:00Z"
        }
      },
      "benefit": {
        "general": {
          "discount": {
            "active": true,
            "discount_for": "transaction_bill_total",
            "discount_type": "value",
            "discount_value": 20000,
            "max_discount": 0,
            "product": null,
            "service": null
          },
          "free_gift": null,
          "free_inventory": null,
          "free_service": null,
          "voucher": null
        },
        "member": [],
        "payment": {
          "cash": null,
          "edc": null,
          "epay": null,
          "qris": null,
          "transfer": null
        }
      },
      "terms": {
        "allow_multiply": false,
        "attachment_evidence": false,
        "combine_promo": false,
        "must_paid_full": false,
        "post_evidence": null,
        "promo_payment_method": [
          "cash",
          "transfer",
          "qris",
          "edc",
          "epayment"
        ],
        "promo_user": {
          "member": {
            "type": "",
            "value": null
          },
          "type": "customer_member"
        },
        "terms_current": {
          "applied_for": "transaction",
          "minimal_qty": 0,
          "minimal_value": 20000,
          "product": null,
          "service": null
        },
        "terms_past": null,
        "use_id_card": false,
        "use_physical_receipt": false
      }
    },
    {
      "metadata": {
        "id": "26009817-124b-4830-b1dd-7815fe4fa42b",
        "name": "VOUCHER GOOGLE GAND",
        "type": "custom",
        "quota": 500,
        "is_active": true,
        "on_going": "21_started",
        "is_expired": false,
        "location_type": "only",
        "location": [
          {
            "idoutlet": "OTL16764343764890",
            "name": "Perkilo Premium Gandaria"
          }
        ],
        "benefit_type": "general"
      },
      "datetime": {
        "created_at": "2025-11-23T02:48:40.962550421Z",
        "period": {
          "start": "2025-11-24T00:00:00Z",
          "end": "2026-11-23T16:59:00Z"
        }
      },
      "benefit": {
        "general": {
          "discount": {
            "active": true,
            "discount_for": "transaction_bill_total",
            "discount_type": "value",
            "discount_value": 20000,
            "max_discount": 0,
            "product": null,
            "service": null
          },
          "free_gift": null,
          "free_inventory": null,
          "free_service": null,
          "voucher": null
        },
        "member": [],
        "payment": {
          "cash": null,
          "edc": null,
          "epay": null,
          "qris": null,
          "transfer": null
        }
      },
      "terms": {
        "allow_multiply": false,
        "attachment_evidence": false,
        "combine_promo": false,
        "must_paid_full": false,
        "post_evidence": null,
        "promo_payment_method": [
          "cash",
          "transfer",
          "qris",
          "edc",
          "epayment"
        ],
        "promo_user": {
          "member": {
            "type": "",
            "value": null
          },
          "type": "customer_member"
        },
        "terms_current": {
          "applied_for": "transaction",
          "minimal_qty": 0,
          "minimal_value": 20000,
          "product": null,
          "service": null
        },
        "terms_past": null,
        "use_id_card": false,
        "use_physical_receipt": false
      }
    },
    {
      "metadata": {
        "id": "e7f8f9a4-7b58-4044-8a1d-fed1da14b24a",
        "name": "VOUCHER GOOGLE PETE",
        "type": "custom",
        "quota": 500,
        "is_active": true,
        "on_going": "21_started",
        "is_expired": false,
        "location_type": "only",
        "location": [
          {
            "idoutlet": "OTL15347390068449",
            "name": "Perkilo Premium Cipete"
          }
        ],
        "benefit_type": "general"
      },
      "datetime": {
        "created_at": "2025-11-23T02:47:15.667623817Z",
        "period": {
          "start": "2025-11-24T00:00:00Z",
          "end": "2026-11-23T16:59:00Z"
        }
      },
      "benefit": {
        "general": {
          "discount": {
            "active": true,
            "discount_for": "transaction_bill_total",
            "discount_type": "value",
            "discount_value": 20000,
            "max_discount": 0,
            "product": null,
            "service": null
          },
          "free_gift": null,
          "free_inventory": null,
          "free_service": null,
          "voucher": null
        },
        "member": [],
        "payment": {
          "cash": null,
          "edc": null,
          "epay": null,
          "qris": null,
          "transfer": null
        }
      },
      "terms": {
        "allow_multiply": false,
        "attachment_evidence": false,
        "combine_promo": false,
        "must_paid_full": false,
        "post_evidence": null,
        "promo_payment_method": [
          "cash",
          "transfer",
          "qris",
          "edc",
          "epayment"
        ],
        "promo_user": {
          "member": {
            "type": "",
            "value": null
          },
          "type": "customer_member"
        },
        "terms_current": {
          "applied_for": "transaction",
          "minimal_qty": 0,
          "minimal_value": 20000,
          "product": null,
          "service": null
        },
        "terms_past": null,
        "use_id_card": false,
        "use_physical_receipt": false
      }
    },
    {
      "metadata": {
        "id": "d1e23e16-9d96-423a-8e0e-15ee49155d94",
        "name": "VOUCHER GOOGLE PAKU",
        "type": "custom",
        "quota": 500,
        "is_active": true,
        "on_going": "21_started",
        "is_expired": false,
        "location_type": "only",
        "location": [
          {
            "idoutlet": "OTL15347389343942",
            "name": "Perkilo Premium Cipaku"
          }
        ],
        "benefit_type": "general"
      },
      "datetime": {
        "created_at": "2025-11-23T02:45:42.17467207Z",
        "period": {
          "start": "2025-11-24T00:00:00Z",
          "end": "2026-11-23T16:59:00Z"
        }
      },
      "benefit": {
        "general": {
          "discount": {
            "active": true,
            "discount_for": "transaction_bill_total",
            "discount_type": "value",
            "discount_value": 20000,
            "max_discount": 0,
            "product": null,
            "service": null
          },
          "free_gift": null,
          "free_inventory": null,
          "free_service": null,
          "voucher": null
        },
        "member": [],
        "payment": {
          "cash": null,
          "edc": null,
          "epay": null,
          "qris": null,
          "transfer": null
        }
      },
      "terms": {
        "allow_multiply": false,
        "attachment_evidence": false,
        "combine_promo": false,
        "must_paid_full": false,
        "post_evidence": null,
        "promo_payment_method": [
          "cash",
          "transfer",
          "qris",
          "edc",
          "epayment"
        ],
        "promo_user": {
          "member": {
            "type": "",
            "value": null
          },
          "type": "customer_member"
        },
        "terms_current": {
          "applied_for": "transaction",
          "minimal_qty": 0,
          "minimal_value": 20000,
          "product": null,
          "service": null
        },
        "terms_past": null,
        "use_id_card": false,
        "use_physical_receipt": false
      }
    },
    {
      "metadata": {
        "id": "b3bccc5f-e44e-432e-936d-a22c82f63c8a",
        "name": "VOUCHER GOOGLE CIKA",
        "type": "custom",
        "quota": 500,
        "is_active": true,
        "on_going": "21_started",
        "is_expired": false,
        "location_type": "only",
        "location": [
          {
            "idoutlet": "OTL16225485072900",
            "name": "Perkilo Premium Cikajang"
          }
        ],
        "benefit_type": "general"
      },
      "datetime": {
        "created_at": "2025-11-23T02:44:15.617841943Z",
        "period": {
          "start": "2025-11-24T00:00:00Z",
          "end": "2026-11-23T16:59:00Z"
        }
      },
      "benefit": {
        "general": {
          "discount": {
            "active": true,
            "discount_for": "transaction_bill_total",
            "discount_type": "value",
            "discount_value": 20000,
            "max_discount": 0,
            "product": null,
            "service": null
          },
          "free_gift": null,
          "free_inventory": null,
          "free_service": null,
          "voucher": null
        },
        "member": [],
        "payment": {
          "cash": null,
          "edc": null,
          "epay": null,
          "qris": null,
          "transfer": null
        }
      },
      "terms": {
        "allow_multiply": false,
        "attachment_evidence": false,
        "combine_promo": false,
        "must_paid_full": false,
        "post_evidence": null,
        "promo_payment_method": [
          "cash",
          "transfer",
          "qris",
          "edc",
          "epayment"
        ],
        "promo_user": {
          "member": {
            "type": "",
            "value": null
          },
          "type": "customer_member"
        },
        "terms_current": {
          "applied_for": "transaction",
          "minimal_qty": 0,
          "minimal_value": 20000,
          "product": null,
          "service": null
        },
        "terms_past": null,
        "use_id_card": false,
        "use_physical_receipt": false
      }
    }
  ]
}
```
