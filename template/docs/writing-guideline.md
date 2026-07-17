# Writing Guideline PRD Markdown AI Friendly

## 1. Gunakan ID yang stabil

Setiap PRD, Epic, Feature, User Story, Business Rule, dan Scenario wajib punya ID.

Contoh:

```text
PRD-OC-001
EPIC-PE-001
FEAT-PE-001
US-Pe-086
BR-PE-001
SC-298
```

## 2. Satu konsep, satu tempat

- Business Rule ditulis di Feature.
- Form Rule ditulis di User Story.
- Detail perilaku sistem ditulis di Scenario/GWT.
- Jangan menulis rule yang sama berulang-ulang di banyak User Story.

## 3. Gunakan bahasa yang eksplisit

Kurang baik:

```text
Sistem menampilkan data yang sesuai.
```

Lebih baik:

```text
Sistem menampilkan daftar template broadcast dengan status approved.
```

## 4. Hindari kata ambigu

Hindari kata:

- sesuai kebutuhan
- sewajarnya
- normal
- data terkait
- dll
- dan sebagainya

Ganti dengan definisi spesifik.

## 5. Tulis GWT sebagai behavior, bukan narasi panjang

Format:

```text
Given: user berada pada halaman Broadcast
When: user klik CTA Buat Kampanye
Then: sistem menampilkan form pembuatan kampanye
```

## 6. Letakkan detail panjang di bawah tabel GWT

Tabel GWT cukup ringkas. Detail field, validasi, error, dan UI behavior ditulis di bagian `Detail SC-xxx`.

## 7. Untuk developer

PRD harus bisa menjawab:

- screen apa yang dibutuhkan?
- field apa saja yang muncul?
- mana field required?
- validasinya apa?
- error message-nya apa?
- business rule yang berlaku apa?
- kondisi sukses dan gagal seperti apa?
- dependency ke service/modul lain apa?
