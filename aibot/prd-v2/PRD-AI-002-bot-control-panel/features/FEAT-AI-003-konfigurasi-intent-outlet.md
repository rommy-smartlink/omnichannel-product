---
id: FEAT-AI-003
prd: PRD-AI-002
epic: EPIC-AI-002
title: Konfigurasi Intent per Outlet
status: Draft
owner: Product Owner
created_at: 2026-07-16
updated_at: 2026-07-16
---

# FEAT-AI-003 - Konfigurasi Intent per Outlet

## 1. Purpose

Owner dapat mengaktifkan atau menonaktifkan 6 intent configurable untuk setiap outlet yang terdaftar omnichannel. Konfigurasi dilakukan melalui modal edit yang dibuka dari card outlet. Perubahan bersifat lokal sampai tombol Simpan ditekan. Intent `talk_to_agent` selalu ON dan tidak dapat dinonaktifkan.

## 2. Scope

### In Scope

- Daftar outlet omnichannel dalam bentuk card read-only, menampilkan ringkasan capability.
- Pencarian outlet berdasarkan nama, kota, atau kode outlet (substring match).
- Pengurutan: Nama A-Z (default), Nama Z-A, Terakhir diubah.
- Modal edit per outlet berisi:
  - 6 toggle intent configurable dengan state terkini dari database.
  - Toggle `talk_to_agent` dalam state readonly/disabled dengan info "Selalu aktif".
- Perubahan toggle bersifat lokal sampai Simpan ditekan.
- Tombol Simpan mengirim PATCH hanya untuk intent yang berubah.
- Tombol Batal menutup modal tanpa menyimpan dan membuang perubahan lokal.
- PATCH gagal → toast error, modal tetap terbuka, perubahan lokal dipertahankan.
- PATCH sukses → modal tertutup, toast "Tersimpan", daftar outlet refresh otomatis.
- Keenam intent configurable default ON untuk outlet baru.
- `get_operating_hours` default ON, semua outlet dianggap 24 jam.
- Intent `talk_to_agent` selalu ON, tidak memiliki row di tabel pengaturan.
- Card outlet menampilkan jumlah intent aktif dan status Jam Operasional.

> **Catatan runtime**: Dampak intent OFF ke customer (fallback message, silent handoff saat semua OFF, command override) ada di [FEAT-AI-001 (Runtime Behavior)](../../PRD-AI-001-bot-runtime-behavior/features/FEAT-AI-001-greeting-classification-handoff.md). Control Panel hanya menyediakan UI konfigurasi.

### Out of Scope

- Konfirmasi saat Autobot Outlet diubah.
- Setup jam operasional langsung di dalam modal Control Panel (navigasi ke halaman terpisah).
- Pembuatan, pengubahan, atau penghapusan outlet.
- Menampilkan outlet yang tidak terdaftar omnichannel.

## 3. Business Rules

### BR-AI-007 - talk_to_agent Selalu ON

Intent `talk_to_agent` tidak dapat dimatikan. Baris ini ditampilkan di modal edit dengan toggle readonly/disabled dan label "Selalu aktif". Saat owner menekan toggle readonly, muncul toast info: "Hubungi agen selalu aktif agar customer selalu bisa terhubung dengan tim kami." Toast auto-dismiss setelah 3–5 detik.

### BR-AI-008 - Fallback Intent OFF

Saat intent dalam keadaan OFF, bot tidak mengeksekusi capability tersebut. Customer yang mengirim pesan yang cocok dengan intent OFF mendapat fallback message:

```text
Maaf Kak, fitur ini sedang tidak tersedia di outlet ini.
Saya bisa hubungkan Kakak ke agent untuk dibantu lebih lanjut.
```

### BR-AI-009 - Silent Handoff Saat Semua Intent OFF

Jika di satu outlet keenam intent configurable dalam keadaan OFF dan hanya `talk_to_agent` yang ON, outlet dianggap bot tidak aktif. Bot tidak mengirim greeting, tidak menampilkan menu bantuan, dan langsung mengarahkan percakapan ke agent melalui silent handoff. Perilaku ini sama seperti master switch OFF.

### BR-AI-010 - Default ON untuk 5 Intent Configurable

Saat outlet baru terdaftar omnichannel, sistem otomatis membuat konfigurasi dengan keenam intent configurable (check_laundry_status, check_ticket_status, get_outlet_services, get_operating_hours, get_active_promos, get_member_info) dalam keadaan ON. `talk_to_agent` tidak memiliki row karena selalu ON.

### BR-AI-011 - Greeting Hanya Menampilkan Intent Siap

Greeting bot hanya menampilkan capability yang memenuhi dua kondisi: intent ON DAN prasyarat data siap. Intent OFF atau intent ON tetapi prasyarat tidak siap tidak muncul di greeting.

### BR-AI-012 - Command Override

Perintah manual "menu", "hubungi agen", dan "batal" selalu diproses bot terlepas dari pengaturan intent. Owner tidak bisa menonaktifkan jalur customer ke agent atau akses ke menu.

### BR-AI-013 - Perubahan Hanya Intent yang Berubah

Saat Simpan, client hanya mengirim PATCH dengan payload berisi intent yang berubah dari state database. Intent yang tidak diubah tidak dikirim.

## 4. Dependencies

- Data outlet omnichannel.
- Data konfigurasi intent per outlet.
- Greeting bot dan menu capability.
- Intent classification bot.
- Sistem handoff ke agent.

## 5. User Story List

| US ID | Judul US | Goal | Status |
|---|---|---|---|
| US-AI-010 | Melihat Daftar Outlet di Control Panel | Owner dapat melihat, mencari, dan mengurutkan daftar outlet omnichannel | Draft |
| US-AI-011 | Membuka Modal Edit Outlet | Owner dapat membuka modal edit dengan menekan card outlet | Draft |
| US-AI-012 | Mengubah Toggle Intent | Owner dapat mengubah satu atau lebih toggle intent di modal edit | Draft |
| US-AI-013 | Menyimpan Perubahan Intent | Owner dapat menyimpan perubahan toggle intent melalui tombol Simpan | Draft |
| US-AI-014 | Membatalkan Perubahan Intent | Owner dapat membatalkan perubahan dengan tombol Batal | Draft |
| US-AI-015 | Melihat Intent talk_to_agent Terkunci | Owner melihat toggle talk_to_agent readonly dengan toast info | Draft |

> **Catatan**: US-AI-016 (Fallback Intent OFF), US-AI-017 (Silent Handoff Semua OFF), dan US-AI-018 (Command Override) dipindahkan ke [PRD-AI-001 Runtime Behavior](../../PRD-AI-001-bot-runtime-behavior/features/FEAT-AI-001-greeting-classification-handoff.md) karena merupakan perilaku bot, bukan aksi admin di UI.
| US-AI-016 | Fallback Intent OFF untuk Customer | Customer mendapat fallback message saat intent OFF | Draft |
| US-AI-017 | Silent Handoff saat Semua Intent OFF | Customer langsung ke agent saat semua intent configurable OFF | Draft |
| US-AI-018 | Command Override | Customer bisa akses menu, agent, atau batal kapan saja | Draft |

## 6. Notes for Developer

- Hanya outlet yang terdaftar omnichannel yang muncul di daftar.
- Search melakukan substring match terhadap nama, kota, atau kode outlet.
- Sort default: Nama A-Z ascending.
- Card outlet bersifat read-only — interaksi hanya melalui tap/click untuk membuka modal.
- Modal edit menampilkan state terkini dari database saat dibuka (bukan cache).
- PATCH server-side atomic per outlet — tidak ada partial update.
- Saat PATCH gagal (network error / 5xx), modal tetap terbuka dengan perubahan lokal utuh untuk retry.
- Toast error menggunakan pattern yang sama dengan toast lain di aplikasi.
- `talk_to_agent` tidak memiliki row di tabel pengaturan.
- `unknown` intent bersifat internal, tidak muncul di Control Panel.
