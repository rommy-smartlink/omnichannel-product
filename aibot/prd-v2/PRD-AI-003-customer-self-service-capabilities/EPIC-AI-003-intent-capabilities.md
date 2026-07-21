---
id: EPIC-AI-003
prd: PRD-AI-003
title: Intent Capabilities
status: Draft
owner: Product Owner
created_at: 2026-07-21
updated_at: 2026-07-21
---

# EPIC-AI-003 - Intent Capabilities

## 1. Tujuan Epic

Menyediakan 7 kemampuan self-service bagi customer melalui percakapan WhatsApp natural: cek status laundry, cek status ticket, jam operasional, hubungi agent, promo aktif, info member, dan info layanan outlet. Setiap intent memiliki slot-filling flow, format respons standar, display rules, dan aturan eskalasi yang jelas.

## 2. Related Problems

- PROB-AI-009: Pertanyaan repetitif membebani agen tanpa nilai tambah.
- PROB-AI-010: Customer menunggu antrean agen untuk pertanyaan sederhana.
- PROB-AI-011: Tidak ada mekanisme self-service.
- PROB-AI-012: Eskalasi ke agen tidak terstruktur.

## 3. Feature List

| Feature ID | Nama Feature | Purpose | Status |
|---|---|---|---|
| FEAT-AI-005 | Cek Status Laundry | Customer mengecek status transaksi laundry berdasarkan ID transaksi | Draft |
| FEAT-AI-006 | Cek Status Ticket | Customer mengecek status ticket berdasarkan nomor ticket (read-only) | Draft |
| FEAT-AI-007 | Jam Operasional | Customer menanyakan jadwal buka/tutup outlet | Draft |
| FEAT-AI-008 | Hubungi Agent | Customer meminta koneksi langsung ke agen manusia tanpa syarat | Draft |
| FEAT-AI-009 | Promo Aktif | Customer melihat promo yang sedang berlangsung di outlet | Draft |
| FEAT-AI-010 | Info Member | Customer melihat paket membership yang tersedia di outlet | Draft |
| FEAT-AI-011 | Info Layanan Outlet | Customer menanyakan layanan yang tersedia di outlet | Draft |
| FEAT-AI-013 | Orkestrasi Self-Service | Menentukan perilaku lintas-capability untuk input, konteks outlet, koreksi, privasi, dan hasil ambigu | Draft |

## 4. Notes

- Semua intent tunduk pada Bot Runtime Behavior (PRD-AI-001): effective state 3-gate, silent handoff, in-flight interruption, timeout fallback.
- Semua intent configurable dikonfigurasi melalui Bot Control Panel (PRD-AI-002): toggle ON/OFF per outlet.
- `talk_to_agent` adalah intent non-configurable — selalu ON selama Master Switch & Outlet Switch ON.
- Display rules dan business rules spesifik ditulis di level Feature masing-masing — tidak diulang di Epic.
- Priority pengerjaan mengacu pada priority intent dari `aibot/docs/intents.md`: P1 (check_laundry_status), P2 (check_ticket_status, get_operating_hours, talk_to_agent), P3 (get_active_promos, get_memberships, get_outlet_services).
