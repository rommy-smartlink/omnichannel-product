# Hubungi Agent — PRD

## Overview

Fitur Hubungi Agent memungkinkan customer meminta koneksi langsung ke agen manusia kapan saja selama percakapan. Jika customer meminta agen, bot tidak boleh memaksa customer menyelesaikan flow bot — eskalasi adalah hak customer. Conversation diteruskan ke antrean agen outlet, dan bot berhenti membalas.

Tujuan: memberikan jalan keluar yang jelas bagi customer yang tidak ingin berinteraksi dengan bot, atau butuh bantuan yang di luar scope capability bot.

---

## User Stories

- As a **customer**, saya ingin **minta berbicara dengan agen kapan saja** agar **tidak terjebak di flow bot yang tidak membantu kebutuhan saya**.
- As a **customer**, saya ingin **bot langsung menghubungkan saya ke agen tanpa tanya alasan** agar **tidak perlu explain why**.
- As a **customer**, saya ingin **mendapat konfirmasi bahwa saya sedang diarahkan ke agen** agar **tidak bingung apakah permintaannya berhasil**.
- As a **owner**, saya ingin **bot berhenti membalas setelah conversation di-eskalasi** agar **tidak ada interference dengan percakapan customer-agen**.
- As a **owner**, saya ingin **customer yang butuh bantuan manusia langsung ke agen** agar **CS fokus ke kasus yang butuh manusia, bukan pertanyaan repetitif**.

---

## Goals & Non-Goals

### Goals

- Fitur Hubungi Agent aktif untuk semua outlet yang bot aktif (tidak ada per-outlet toggle — eskalasi adalah hak customer).
- Trigger phrases: bahasa natural customer yang mengindikasikan ingin berbicara dengan manusia (misal: "admin", "CS", "agen", "manusia", "komplain", "bicara orang", "hubungi", "saya mau manusia").
- Customer tidak perlu memberikan alasan atau menyelesaikan slot-filling sebelum minta agen.
- Konfirmasi bot sebelum eskalasi: "Saya hubungkan ke agent [Nama Outlet] ya."
- Conversation diteruskan ke antrean agen outlet via handoff protocol.
- Bot berhenti membalas (silent) setelah eskalasi triggered.
- Jika agen belum tersedia, customer mendapat pesan fallback yang jelas dengan estimasi wait time jika tersedia.

### Non-Goals (MVP)

- Handoff dengan context handover (bot summary belum di-MVP — fase 2).
- Priority routing (VIP customer, bahasa tertentu).
- Re-assign ke agen berbeda mid-conversation.
- Auto-eskalasi berdasarkan sentiment analysis.
- Escalation ke channel lain (email, phone) dalam satu conversation.

---

## Flow Utama

```
Customer kirim pesan yang mengindikasikan ingin berbicara dengan agen
↓
Bot klasifikasi intent Hubungi Agent
↓
Bot kirim konfirmasi eskalasi
↓
Bot trigger handoff ke antrean agen outlet
↓
Bot stop membalas (silent)
↓
Conversation handover ke agen
```

---

## Alur Percakapan

### Happy Path

```
Customer:
Saya mau bicara dengan admin.

Bot:
Baik Kak, saya hubungkan ke agent [Nama Outlet] agar bisa dibantu lebih lanjut.
Mohon tunggu sebentar ya.
```

### Dengan Context dari Flow Lain

```
Customer (sedang di flow Cek Status Laundry):
Ini laundry saya belum juga selesai. Saya mau ngobrol sama agen.

Bot:
Baik Kak, saya hubungkan ke agent [Nama Outlet] ya.
Mohon tunggu sebentar.
```

### Agen Tidak Tersedia

Conversation masuk antrean round-robin seperti biasa. Jika semua agent offline, conversation masuk status **unassigned** — mengikuti flow yang sudah ada di sistem.

---

## Trigger Phrases

Bot mengenali keinginan customer untuk bicara dengan agen dari variasi bahasa natural:

| Kategori | Contoh Input |
| -------- | ------------ |
| Minta orang | "saya mau bicara dengan admin", "hubungi CS", "agen dong", "manusia aja", "orang", "orang hidup" |
| Komplain | "saya mau komplain", "ada masalah", "ini nggak bisa", "gimana nih" |
| Butuh bantuan manusia | "bisa hubungi orang?", "ada yang bisa bantu?", "saya butuh bantuan", "panggil supervisor" |
| Frustrasi | "bot doang", "capek ngomong sama bot", "gak getoh", "bosan" |

> Catatan: Trigger phrases bersifat **inclusive** — jika ada keraguan, arahkan ke agen. Semua eskalasi di-grant tanpa syarat: tidak ada konfirmasi saat minta agen, tidak ada tanya alasan, tidak ada batas jumlah permintaan, tidak ada usaha menahan customer di bot.

---

## Edge Cases

| Scenario | Bot Response |
| -------- | ------------ |
| Customer minta agen di tengah slot-filling | Eskalasi langsung, tidak minta data tambahan |
| Customer minta agen + info lain dalam 1 pesan | Eskalasi, info tambahan diabaikan |
| Customer cancel eskalasi ("batal") | Tidak ada cancel eskalasi — eskalasi final |
| Agen offline / tidak tersedia | Conversation masuk antrean round-robin, status unassigned jika semua offline |

---

## Master Switch Behavior

| Kondisi | Perilaku |
| ------- | -------- |
| Master switch OFF | Bot tidak aktif — conversation langsung ke agen |
| Master switch ON + customer minta agen | Eskalasi via flow Hubungi Agent |
| Mid-flow request agen | Eskalasi langsung, state dibuang |

---

## Response Templates

### Konfirmasi Eskalasi (default)

```
Baik Kak, saya hubungkan ke agent [Nama Outlet] agar bisa dibantu lebih lanjut.
Mohon tunggu sebentar ya.
```

> **Catatan:** Jika agent outlet offline, conversation masuk antrean round-robin (status unassigned) — tidak ada fallback message tambahan dari bot. Agent assignment mengikuti flow existing.

---

## Acceptance Criteria

- [ ] Customer dapat meminta agen menggunakan bahasa bebas.
- [ ] Bot langsung mengarahkan ke agen tanpa tanya alasan.
- [ ] Bot berhenti membalas setelah conversation masuk ke agen.
- [ ] Jika semua agen offline, conversation masuk status unassigned (round-robin existing flow).
- [ ] Customer di mid-flow (slot-filling) tetap bisa minta agen kapan saja.
- [ ] Trigger phrases "admin", "CS", "agen", "manusia", "komplain" dikenali sebagai permintaan Hubungi Agent.
- [ ] Tidak ada per-outlet toggle untuk eskalasi — hak eskalasi tidak bisa dimatikan.
- [ ] Bahasa Indonesia, tone sopan, tidak menghakimi customer.
