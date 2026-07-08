# Wording Intent — Customer Labels

Dokumen ini menetapkan **istilah resmi** untuk setiap intent yang ditampilkan ke customer (bot menu, control panel). Identifier internal (`check_laundry_status`, dll) ditentukan developer — tidak dibahas di sini.

---

## Intent Configurable (6)

| Label | Tampil di |
|-------|-----------|
| Cek status laundry | Bot menu, control panel toggle |
| Cek status tiket | Bot menu, control panel toggle |
| Layanan outlet | Bot menu, control panel toggle |
| Jam operasional | Bot menu, control panel toggle |
| Promo aktif | Bot menu, control panel toggle |
| Info member | Bot menu, control panel toggle |

## Intent Selalu Aktif (tidak dikonfigurasi di control panel)

| Label | Tampil di |
|-------|-----------|
| Hubungi agen | Bot menu (selalu aktif, tidak bisa di-toggle) |

Catatan: ada satu intent lagi di luar daftar ini (fallback untuk input yang tidak dikenali) — handled di level teknis, tidak butuh label karena tidak ditampilkan sebagai opsi ke customer.

---

## Aturan Konsistensi

1. Label pakai huruf kapital di awal — bukan semua kecil, bukan ALL CAPS.
2. Label di control panel dan bot menu **harus** sama persis.
3. Jangan ubah label tanpa update dokumen ini + kasih tahu tim.
4. Tambah intent baru? Ikutin pola: label customer-friendly, tentukan configurable atau selalu-aktif, lalu tambah row di tabel yang sesuai.