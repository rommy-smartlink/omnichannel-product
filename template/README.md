# PRD Markdown Template - AI Friendly

Template ini digunakan oleh Product Owner untuk membuat PRD yang terstruktur, mudah dibaca manusia, dan mudah dikonsumsi AI maupun tim development.

## Struktur Utama

```text
PRD
-> Epic
-> Feature + Business Rules
-> User Story + Form Rules
-> Scenario / GWT
```

## Cara Pakai Singkat

1. Salin folder `templates/` untuk membuat PRD baru.
2. Isi `00-prd.md` sebagai dokumen utama PRD.
3. Buat satu file Epic untuk setiap objective besar.
4. Buat satu file Feature untuk setiap kemampuan produk.
5. Buat satu file User Story untuk setiap kebutuhan user.
6. Tulis Scenario/GWT di dalam file User Story.

## Prinsip Penulisan AI Friendly

- Gunakan ID konsisten untuk PRD, Epic, Feature, User Story, dan Scenario.
- Hindari paragraf terlalu panjang.
- Pisahkan konteks bisnis, business rules, form rules, acceptance criteria, dan scenario.
- Jangan menulis rule yang sama berulang-ulang di banyak tempat.
- Gunakan tabel untuk field form agar mudah dikonsumsi developer dan AI.
- Gunakan Given-When-Then untuk menjelaskan perilaku sistem.

## Rekomendasi ID

```text
PRD-OC-001      = PRD Omnichannel
EPIC-PE-001     = Epic Pengiriman Pesan
FEAT-PE-001     = Feature Pengiriman Pesan Massal
US-Pe-086       = User Story
SC-298          = Scenario
BR-PE-001       = Business Rule di Feature Pengiriman Pesan
```

