# Alur Percakapan

```
Customer pilih outlet
    ↓
Bot context sudah ada outlet
    ↓
Bot kirim greeting + daftar capability
    ↓
Customer ketik bebas
    ↓
LLM klasifikasikan intent
    ↓
Slot filling jika perlu (tanya ID transaksi, tiket, dst)
    ↓
Bot fetch data dan respon
    ↓
Atau eskalasi ke agen jika dibutuhkan
```

## Constraints Utama

- Lookup transaksi dibatasi outlet yang dipilih
- Respons harus WhatsApp-friendly (singkat, mudah dibaca di mobile)
- Handoff ke agen mengakhiri keterlibatan bot di percakapan itu
