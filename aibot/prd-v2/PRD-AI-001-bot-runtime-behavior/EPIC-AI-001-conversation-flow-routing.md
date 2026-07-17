---
id: EPIC-AI-001
prd: PRD-AI-001
title: Conversation Flow & Routing
status: Draft
owner: Product Owner
created_at: 2026-07-17
updated_at: 2026-07-17
---

# EPIC-AI-001 - Conversation Flow & Routing

## 1. Tujuan Epic

Menentukan bagaimana bot merespons customer berdasarkan configured state dari Control Panel. Mencakup: evaluasi effective state (3-gate), greeting & menu, routing percakapan (bot vs silent handoff vs explicit handoff), fallback saat bot tidak bisa melayani, dan apa yang terjadi saat percakapan bot dihentikan di tengah jalan (in-flight interruption).

## 2. Related Problems

- PROB-AI-005: Semua chat masuk ke agent manusia tanpa filter → beban agent tinggi.
- PROB-AI-006: Tidak ada klasifikasi intent → agent harus memahami maksud customer dari nol.
- PROB-AI-007: Tidak ada slot-filling → agent harus mengumpulkan data pelanggan secara manual.
- PROB-AI-008: Tidak ada fallback atau eskalasi terstruktur → customer sederhana tetap menunggu antrean.

## 3. Feature List

| Feature ID | Nama Feature | Purpose | Status |
|---|---|---|---|
| FEAT-AI-001 | Greeting, Intent Classification, Slot Filling & Handoff | Menentukan alur percakapan bot dari greeting, klasifikasi intent, pengumpulan data, hingga eskalasi ke agent | Draft |

## 4. Notes

- Epic ini adalah jembatan antara Control Panel (apa yang diatur owner) dan intent (apa yang dieksekusi bot).
- Semua aturan di sini berlaku di level bot engine — tidak ada UI admin.
- Business rules hasil dari epic ini direferensikan oleh Control Panel (sebagai "apa yang terjadi saat switch diubah").
- Override priority 3-gate: Master Switch OFF > Outlet Switch OFF > Intent Switch OFF.

## 5. Business Rules (Global)

### BR-AI-100 — Effective State 3-Gate

Bot hanya merespons customer jika Master Switch WABA ON, Outlet Switch ON, dan intent yang diminta ON. Pengecekan cascading: Gate 1 (Master) → Gate 2 (Outlet) → Gate 3 (Intent). Gate pertama OFF langsung memblokir, gate di bawahnya tidak dicek.

### BR-AI-101 — Silent Handoff

Saat bot tidak boleh aktif (Master OFF, Outlet OFF, atau semua intent configurable OFF), bot tidak mengirim greeting, tidak melakukan klasifikasi intent, dan langsung mengarahkan percakapan ke agent tanpa respons apapun ke customer.

### BR-AI-102 — In-Flight Interruption

Saat switch (Master/Outlet) diubah dari ON ke OFF, percakapan bot yang sedang berlangsung dihentikan pada respons berikutnya. Customer diarahkan ke agent. Aturan tidak berlaku jika customer sudah di sesi agent.

### BR-AI-103 — Greeting Hanya Intent Siap

Greeting bot hanya menampilkan capability yang memenuhi: intent ON DAN prasyarat data siap. Intent OFF atau data tidak siap tidak muncul di greeting.

### BR-AI-104 — Command Override

Perintah "menu", "hubungi agen", "batal" selalu diproses bot terlepas dari pengaturan intent. Owner tidak bisa memblokir akses customer ke agent atau menu.

### BR-AI-105 — Post-Handoff Bot Silence

Setelah handoff ke agent terjadi, bot berhenti membalas di percakapan tersebut. Semua pesan customer selanjutnya langsung ke agent.

### BR-AI-106 — Timeout Escalation

Jika bot tidak bisa merespons dalam 1 menit (slot filling macet, data tidak ditemukan, dsb), percakapan otomatis dieskalasi ke agent.

### BR-AI-107 — Unknown Intent Fallback

Jika LLM tidak bisa mengklasifikasikan intent setelah repeated attempt, bot mengirim daftar bantuan (capability yang ON). Jika masih unknown, eskalasi ke agent.
