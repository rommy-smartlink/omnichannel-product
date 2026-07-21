---
id: US-AI-072
prd: PRD-AI-001
epic: EPIC-AI-001
feature: FEAT-AI-001
title: Autoresolve Sesi Bot saat Customer Tidak Merespons
status: Draft
priority: Medium
uix_status: 0%
created_at: 2026-07-21
updated_at: 2026-07-21
---

# US-AI-072 - Autoresolve Sesi Bot saat Customer Tidak Merespons

## 1. Parent Reference

- PRD: PRD-AI-001 - Bot Runtime Behavior
- Epic: EPIC-AI-001 - Conversation Flow & Routing
- Feature: FEAT-AI-001 - Greeting, Intent Classification, Slot Filling & Handoff

## 2. User Story

Sebagai customer yang tidak lagi merespons percakapan bot,  
Saya ingin sesi ditutup secara otomatis tanpa harus menunggu agent,  
Agar percakapan tidak menggantung dan saya bisa memulai sesi baru kapan saja dengan keadaan yang bersih.

## 3. Goal

Bot mendeteksi customer tidak merespons, mengirim notifikasi penutupan, dan menutup sesi setelah grace period. Customer yang kembali setelah resolve mendapat sesi baru dengan evaluasi 3-gate.

## 4. Acceptance Criteria

- Bot mulai menghitung waktu idle setelah respons terakhir terkirim ke customer.
- Jika bot dalam state **flow selesai** (jawaban sudah diberikan) dan customer tidak merespons 10 menit, bot kirim notifikasi penutupan.
- Jika bot dalam state **mid-slot-filling** (menunggu input) dan customer tidak merespons 5 menit, bot kirim notifikasi penutupan.
- Notifikasi: *"Kak, karena tidak ada balasan, saya tutup percakapan ini ya. Kalau butuh bantuan lagi, silakan chat kembali."*
- Setelah notifikasi, bot memberi grace period 5 menit.
- Jika customer merespons dalam grace period, autoresolve dibatalkan. Bot melanjutkan percakapan dari state terakhir.
- Jika grace period habis tanpa respons, bot menutup sesi (autoresolve).
- Setelah sesi diresolve, pesan customer berikutnya diperlakukan sebagai sesi baru — bot evaluasi effective state (3-gate) dari awal.
- Autoresolve tidak berlaku untuk customer yang sudah berada di sesi agent (post-handoff).
- Autoresolve tidak berlaku saat handoff sedang diproses.

## 5. Form Rules

> Tidak ada form untuk user story ini.

## 6. Form Notes

> Tidak ada form notes.

## 7. Scenario / GWT

| Kode SC | Given | When | Then |
|---|---|---|---|
| SC-AI-072-01 | Bot sudah selesai menjawab, tidak ada pesan customer selama 10 menit | Waktu idle mencapai 10 menit | Bot mengirim notifikasi penutupan dan memulai grace period 5 menit |
| SC-AI-072-02 | Bot dalam grace period 5 menit setelah notifikasi | Customer tidak merespons hingga grace period habis | Bot menutup sesi (autoresolve) |
| SC-AI-072-03 | Bot sedang menunggu nomor tiket (mid-slot-filling), customer diam 5 menit | Waktu idle mencapai 5 menit | Bot mengirim notifikasi penutupan dan memulai grace period 5 menit |
| SC-AI-072-04 | Bot dalam grace period 5 menit | Customer mengirim pesan | Bot membatalkan autoresolve, melanjutkan dari state terakhir |
| SC-AI-072-05 | Sesi sudah diresolve | Customer mengirim pesan baru | Bot evaluasi 3-gate dari awal. Gate 1 & 2 ON → greeting. Gate 1/2 OFF → silent handoff. |

### Detail SC-AI-072-01 — Autoresolve Setelah Flow Selesai

#### Given

Bot di outlet A. Bot sudah selesai memberikan jawaban status laundry ke customer. Tidak ada pesan lanjutan dari customer.

#### When

Waktu idle mencapai 10 menit sejak respons terakhir bot.

#### Then

- Bot mengirim notifikasi: *"Kak, karena tidak ada balasan, saya tutup percakapan ini ya. Kalau butuh bantuan lagi, silakan chat kembali."*
- Bot memulai grace period 5 menit.
- Bot tidak mengirim pesan lain selama grace period.
- Bot menunggu respons customer.

#### Related Business Rules

- BR-AI-111: Autoresolve Sesi Bot saat Customer Tidak Merespons

### Detail SC-AI-072-02 — Grace Period Habis, Sesi Ditutup

#### Given

Bot sudah mengirim notifikasi penutupan. Grace period 5 menit berjalan.

#### When

Customer tidak merespons hingga grace period habis.

#### Then

- Bot menutup sesi percakapan (autoresolve).
- Sesi dianggap selesai — sama seperti resolve oleh agent.
- Tidak ada pesan tambahan dari bot.

### Detail SC-AI-072-03 — Autoresolve saat Mid-Slot-Filling

#### Given

Bot sedang dalam flow "Cek status laundry", menunggu customer mengirim nomor tiket. Customer belum mengirim nomor tiket.

#### When

Waktu idle mencapai 5 menit sejak pertanyaan terakhir bot (permintaan nomor tiket).

#### Then

- Bot mengirim notifikasi penutupan: *"Kak, karena tidak ada balasan, saya tutup percakapan ini ya. Kalau butuh bantuan lagi, silakan chat kembali."*
- Bot memulai grace period 5 menit.
- Slot-filling yang belum selesai dibatalkan saat autoresolve terjadi.

#### Related Business Rules

- BR-AI-111: Autoresolve Sesi Bot saat Customer Tidak Merespons

### Detail SC-AI-072-04 — Customer Merespons di Grace Period

#### Given

Bot sudah mengirim notifikasi penutupan dan grace period 5 menit berjalan.

#### When

Customer mengirim pesan dalam grace period.

#### Then

- Bot membatalkan autoresolve.
- Jika sebelumnya di state flow selesai, bot mengklasifikasikan pesan customer sebagai pertanyaan lanjutan atau intent baru (US-AI-067).
- Jika sebelumnya di state mid-slot-filling, bot melanjutkan dari state terakhir (misal menunggu nomor tiket).

### Detail SC-AI-072-05 — Customer Kembali Setelah Autoresolve

#### Given

Sesi sudah diresolve oleh bot (autoresolve). Tidak ada sesi agent aktif.

#### When

Customer mengirim pesan baru ke outlet yang sama.

#### Then

- Ini adalah sesi baru.
- Bot evaluasi effective state (3-gate) dari awal.
- Gate 1 (Master Switch) & Gate 2 (Outlet Switch) ON → bot kirim greeting dengan daftar layanan tersedia.
- Gate 1 atau 2 OFF → silent handoff: pesan langsung masuk antrian agent tanpa respons bot.
- Gate 1 & 2 ON tetapi Gate 3 (Intent Switch) untuk intent tertentu OFF → layanan tersebut tidak muncul di daftar greeting.

#### Related Business Rules

- BR-AI-100: Hierarki Aktivasi Bot
- BR-AI-101: Pengalihan Langsung saat Bot Tidak Aktif

## 8. Supporting Information

- Autoresolve berbeda dengan timeout escalation (BR-AI-106). BR-AI-106 = bot gagal merespons → handoff ke agent. BR-AI-111 = customer diam → tutup sesi.
- Autoresolve tidak mengirim customer ke antrian agent. Sesi benar-benar ditutup.
- Mekanisme deteksi "customer tidak merespons" perlu koordinasi dengan tim engineering — apakah berdasarkan timestamp pesan terakhir atau webhook callback.
- Setelah autoresolve, customer bisa start sesi baru kapan saja dengan chat ke outlet yang sama.
