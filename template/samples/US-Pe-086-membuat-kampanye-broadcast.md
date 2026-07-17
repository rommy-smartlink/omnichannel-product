---
id: US-Pe-086
prd: PRD-OC-001
epic: EPIC-PE-001
feature: FEAT-PE-001
title: Membuat Kampanye Broadcast
status: Draft
priority: High
uix_status: 100%
created_at: 2026-07-13
updated_at: 2026-07-13
---

# US-Pe-086 - Membuat Kampanye Broadcast

## 1. Parent Reference

- PRD: PRD-OC-001 - Omnichannel Smartlink
- Epic: EPIC-PE-001 - Pengiriman Pesan
- Feature: FEAT-PE-001 - Pengiriman Pesan Massal

## 2. User Story

Sebagai user yang memiliki akses broadcast,  
Saya ingin membuat kampanye broadcast dengan memilih template pesan,  
Agar saya dapat mengirim pesan massal ke banyak kontak sesuai kebijakan WhatsApp.

## 3. Goal

User dapat membuat kampanye massal dengan memilih template yang sudah disetujui Meta.

## 4. Acceptance Criteria

- Pada halaman Broadcast tersedia CTA **Buat Kampanye**.
- User dapat memilih template dari daftar template yang tersedia.
- Template yang dapat dipilih hanya template yang statusnya **approved**.
- Setelah template dipilih, sistem menampilkan preview pesan.

## 5. Form Rules

| Label | Field Key | Type | Required | Unique | Max Length | Description |
|---|---|---|---:|---:|---:|---|
| Nama Kampanye | campaign_name | string | yes | no | 100 | Nama kampanye broadcast |
| Channel | channel | enum | yes | no | - | Auto terisi WhatsApp |
| Template | template_id | dropdown | yes | no | - | Berisi template broadcast dengan status approved |
| Gambar / URL | media | file/url | conditional | no | - | Wajib jika template membutuhkan gambar |
| Kontak | recipients | list | yes | no | - | Dapat dipilih dari kontak atau import CSV/XLS/XLSX |
| Jadwal Kirim | schedule_at | datetime | conditional | no | - | Wajib jika toggle jadwalkan kirim pesan aktif |

## 6. Form Notes

- Limit harian untuk broadcast pada tier awal adalah 250.
- Media mendukung format JPG, JPEG, dan PNG.
- Ukuran maksimum media adalah 1 MB.
- Kontak dapat dipilih melalui upload CSV/XLS/XLSX atau dari daftar kontak.
- CTA **Lanjut** hanya aktif jika seluruh field required sudah terisi.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-298 | User berada pada halaman Broadcast | User klik CTA **Buat Kampanye** | Sistem menampilkan form pembuatan kampanye |
| SC-299 | User berada pada form pembuatan kampanye | User memilih kontak melebihi daily limit broadcast | Sistem menampilkan error jumlah kontak melebihi batas pengiriman |
| SC-300 | User memilih template yang menggunakan parameter | Sistem mendeteksi template memiliki parameter | Sistem menampilkan field input sesuai jumlah parameter template |
| SC-302 | User sudah mengisi form sesuai ketentuan | User klik CTA **Lanjut** | Sistem menampilkan halaman preview broadcast |
| SC-303 | User berada pada halaman preview broadcast | User klik CTA **Kirim Pesan** | Sistem memproses pengiriman sesuai setting kampanye |
| SC-304 | User melihat response proses pengiriman | User klik CTA **Ya, Mengerti** | Sistem menampilkan halaman daftar kampanye sesuai status kampanye |

### Detail SC-298 - Menampilkan Form Pembuatan Kampanye

#### Given

User berada pada halaman Broadcast.

#### When

User klik CTA **Buat Kampanye**.

#### Then

Sistem menampilkan form pembuatan kampanye dengan detail:

- Nama kampanye.
- Channel, auto terisi WhatsApp.
- Informasi limit harian broadcast pada tier awal.
- Pilih template dari template broadcast dengan status approved.
- Upload gambar atau URL jika dibutuhkan template.
- Pilih kontak melalui import CSV/XLS/XLSX atau dari daftar kontak.
- Preview message dengan UI room chat WhatsApp.
- CTA **Lanjut**.

#### Related Business Rules

- BR-PE-001
- BR-PE-002

#### UI Behavior

- CTA **Lanjut** hanya enabled jika semua field required sudah terisi.
- Jika template membutuhkan gambar, sistem menampilkan informasi: `Template yang Anda pilih memerlukan file gambar. Silakan unggah gambar terlebih dahulu.`
- Di bawah field media, sistem menampilkan informasi format file JPG, JPEG, PNG dengan maksimum ukuran 1 MB.

#### Validation

- Nama kampanye maksimal 100 karakter.
- Template yang muncul hanya template approved.
- Jumlah kontak dibatasi sesuai daily broadcast limit user.
- File gambar hanya menerima JPG, JPEG, dan PNG.
- Maksimum ukuran file gambar adalah 1 MB.

### Detail SC-299 - Kontak Melebihi Daily Limit Broadcast

#### Given

User berada pada form pembuatan kampanye broadcast.

#### When

User memilih kontak melebihi daily broadcast limit.

#### Then

Sistem menampilkan pesan error.

#### Error Message

> Jumlah kontak melebihi batas pengiriman WhatsApp harian. Silakan kurangi jumlah penerima atau kirim kembali setelah kuota tersedia.

#### Related Business Rules

- BR-PE-002

### Detail SC-300 - Template Menggunakan Parameter

#### Given

User berada pada form pembuatan kampanye broadcast.

#### When

User memilih template yang menggunakan parameter.

#### Then

Sistem menampilkan field input untuk mengisi parameter sesuai jumlah parameter yang diset pada template tersebut.

#### Notes

Behavior ini mengikuti existing flow parameter template pada US-In-075 SC-270.

### Detail SC-302 - Preview Broadcast Sebelum Kirim

#### Given

User sudah mengisi form sesuai ketentuan.

#### When

User klik CTA **Lanjut**.

#### Then

Sistem menampilkan halaman preview broadcast dengan detail:

- Judul kampanye, read only.
- Channel, read only.
- Template yang digunakan, read only.
- Toggle self assign, default nonaktif.
- Toggle jadwalkan kirim pesan, default nonaktif.
- Ringkasan kampanye.
- Total penerima kampanye.
- Biaya kampanye per pesan.
- Estimasi total biaya.
- CTA **Kirim Pesan**.
- CTA **Simpan menjadi Draf**.

#### Calculation

```text
estimasi_total_biaya = biaya_kampanye_per_pesan * total_penerima_kampanye
```

#### Related Business Rules

- BR-PE-003
- BR-PE-004
- BR-PE-005

#### UI Behavior

- Jika toggle self assign aktif, percakapan dari broadcast otomatis ditugaskan kepada user setelah broadcast selesai.
- Jika toggle jadwalkan kirim pesan aktif, user wajib memilih tanggal dan jam pengiriman.
- Jika saldo koin kurang dari estimasi biaya, CTA **Kirim Pesan** disabled dan muncul CTA **Top Up Saldo Koin**.
- Jika user klik CTA **Top Up Saldo Koin**, kampanye otomatis tersimpan sebagai draft.

### Detail SC-303 - Kirim Pesan Broadcast

#### Given

User berada pada halaman preview broadcast.

#### When

User klik CTA **Kirim Pesan**.

#### Then

Sistem merespons berdasarkan setting kampanye: kirim sekarang atau menggunakan jadwal. Sistem menampilkan informasi bahwa pesan sedang dalam proses pengiriman.

#### UI Message

> Pesan anda sedang dalam proses pengiriman.

#### Available CTA

- Ya, Mengerti.

### Detail SC-304 - Kembali ke Daftar Kampanye

#### Given

User melihat response bahwa pesan sedang dalam proses pengiriman.

#### When

User klik CTA **Ya, Mengerti**.

#### Then

Sistem menampilkan halaman daftar kampanye sesuai status kampanye tersebut.

## 8. Supporting Information

- Daily broadcast limit mengikuti ketentuan akun WABA / Maxchat.
- Pada tier awal, limit yang digunakan dalam PRD ini adalah 250.
- Informasi dari Maxchat menyebutkan limit dapat meningkat sesuai kredibilitas akun WABA.
