# Mapping Arsitektur PRD AI Bot Omnichannel

Dibuat: 2026-07-16 | Format baru mengikuti template `template/templates/`

---

## Struktur Lengkap

```
PRD-AI-001: Bot Runtime Behavior                    ← bot-runtime-behavior/
  └── EPIC-AI-001: Conversation Flow & Routing
        └── FEAT-AI-001: Greeting, Intent Classification, Slot Filling & Handoff
              ├── US-AI-027: Evaluasi Effective State Bot (3-Gate)
              ├── US-AI-028: Greeting Berdasarkan Intent Siap
              ├── US-AI-007: Silent Handoff — Customer Terdampak Master OFF
              ├── US-AI-024: Silent Handoff — Customer Terdampak Outlet OFF
              ├── US-AI-017: Silent Handoff — Semua Intent OFF
              ├── US-AI-016: Fallback Intent OFF
              ├── US-AI-029: Explicit Handoff — Customer Minta Agent
              ├── US-AI-030: Unknown Intent Fallback
              ├── US-AI-031: Timeout Escalation
              ├── US-AI-032: Data Not Ready Fallback
              ├── US-AI-018: Command Override
              └── US-AI-033: Post-Handoff Bot Silence

PRD-AI-002: Bot Control Panel                       ← P8-control-panel/
  └── EPIC-AI-002: Bot Control Panel
        ├── FEAT-AI-002: Master Switch WABA
        ├── FEAT-AI-005: Outlet Switch
        ├── FEAT-AI-003: Konfigurasi Intent per Outlet
        └── FEAT-AI-004: Informasi Jam Operasional

PRD-AI-003: Customer Self-Service Capabilities      ← gabungan 7 intent PRD
  └── EPIC-AI-003: Intent Capabilities
        ├── FEAT-AI-005: Cek Status Laundry          ← P1-check_laundry_status/
        ├── FEAT-AI-006: Cek Status Ticket           ← P2-check_ticket_status/
        ├── FEAT-AI-007: Jam Operasional             ← P2-get_operating_hours/
        ├── FEAT-AI-008: Hubungi Agent               ← P2-talk_to_agent/
        ├── FEAT-AI-009: Promo Aktif                 ← P3-get_active_promos/
        ├── FEAT-AI-010: Info Member                 ← P3-get_memberships/
        └── FEAT-AI-011: Info Layanan Outlet         ← P3-get_outlet_services/

PRD-AI-004: Pengaturan Outlet                       ← P8-pengaturan-jam-operasional/
  └── EPIC-AI-004: Pengaturan Outlet
        └── FEAT-AI-012: Pengaturan Jam Operasional Outlet
              ├── US-AI-034: Melihat Status Konfigurasi Jam Operasional
              ├── US-AI-035: Memilih Zona Waktu Outlet
              ├── US-AI-036: Mengatur Jadwal Mingguan
              ├── US-AI-037: Jadwal Melewati Tengah Malam
              ├── US-AI-038: Membuat Jadwal Khusus
              ├── US-AI-039: Menyimpan & Mengaktifkan Jadwal
              ├── US-AI-040: Mengubah Jadwal Saat Intent Aktif
              ├── US-AI-041: Validasi Konflik & Hapus Jadwal Aktif
              └── US-AI-042: Navigasi Aktivasi dari Control Panel
```

---

## Status Pengerjaan

| ID | Level | Nama | Status |
|----|-------|------|--------|
| PRD-AI-001 | PRD | Bot Runtime Behavior | ✅ `prd.md` |
| EPIC-AI-001 | Epic | Conversation Flow & Routing | ✅ `EPIC-AI-001-conversation-flow-routing.md` |
| FEAT-AI-001 | Feature | Greeting, Intent Classification, Slot Filling & Handoff | ✅ `FEAT-AI-001-greeting-classification-handoff.md` |
| US-AI-027 | User Story | Evaluasi Effective State Bot (3-Gate) | ✅ |
| US-AI-028 | User Story | Greeting Berdasarkan Intent Siap | ✅ |
| US-AI-007 | User Story | Silent Handoff — Customer Terdampak Master OFF | ✅ (dipindahkan dari Control Panel) |
| US-AI-024 | User Story | Silent Handoff — Customer Terdampak Outlet OFF | ✅ (dipindahkan dari Control Panel) |
| US-AI-017 | User Story | Silent Handoff — Semua Intent OFF | ✅ (dipindahkan dari Control Panel) |
| US-AI-016 | User Story | Fallback Intent OFF | ✅ (dipindahkan dari Control Panel) |
| US-AI-029 | User Story | Explicit Handoff — Customer Minta Agent | ✅ |
| US-AI-030 | User Story | Unknown Intent Fallback | ✅ |
| US-AI-031 | User Story | Timeout Escalation | ✅ |
| US-AI-032 | User Story | Data Not Ready Fallback | ✅ |
| US-AI-018 | User Story | Command Override | ✅ (dipindahkan dari Control Panel) |
| US-AI-033 | User Story | Post-Handoff Bot Silence | ✅ |
| | | | |
| PRD-AI-002 | PRD | Bot Control Panel | ✅ `prd.md` |
| EPIC-AI-002 | Epic | Bot Control Panel | ✅ `EPIC-AI-002-bot-control-panel.md` |
| FEAT-AI-002 | Feature | Master Switch WABA | ✅ |
| US-AI-004 | User Story | Melihat Status Master Switch | ✅ |
| US-AI-005 | User Story | Mematikan Bot WABA | ✅ |
| US-AI-006 | User Story | Mengaktifkan Kembali Bot WABA | ✅ |
| US-AI-008 | User Story | Konfigurasi Outlet Tetap Saat Master OFF | ✅ |
| US-AI-009 | User Story | Edit Konfigurasi Saat Master OFF | ✅ |
| FEAT-AI-005 | Feature | Outlet Switch | ✅ |
| US-AI-021 | User Story | Melihat Status Outlet Switch | ✅ |
| US-AI-022 | User Story | Mematikan Bot Per Outlet | ✅ |
| US-AI-023 | User Story | Mengaktifkan Kembali Bot Per Outlet | ✅ |
| US-AI-025 | User Story | Konfigurasi Outlet Tetap Saat Outlet OFF | ✅ |
| US-AI-026 | User Story | Edit Konfigurasi Saat Outlet OFF | ✅ |
| FEAT-AI-003 | Feature | Konfigurasi Intent per Outlet | ✅ |
| US-AI-010 | User Story | Melihat Daftar Outlet di Control Panel | ✅ |
| US-AI-011 | User Story | Membuka Modal Edit Outlet | ✅ |
| US-AI-012 | User Story | Mengubah Toggle Intent | ✅ |
| US-AI-013 | User Story | Menyimpan Perubahan Intent | ✅ |
| US-AI-014 | User Story | Membatalkan Perubahan Intent | ✅ |
| US-AI-015 | User Story | Melihat Intent talk_to_agent Terkunci | ✅ |
| US-AI-016 | User Story | Fallback Intent OFF untuk Customer | ✅ |
| US-AI-017 | User Story | Silent Handoff saat Semua Intent OFF | ✅ |
| US-AI-018 | User Story | Command Override | ✅ |
| FEAT-AI-004 | Feature | Informasi Jam Operasional | ✅ |
| US-AI-019 | User Story | Melihat Info Jam Operasional di Card | ✅ |
| US-AI-020 | User Story | Navigasi ke Pengaturan Jam Operasional | ✅ |
| | | | |
| PRD-AI-003 | PRD | Customer Self-Service Capabilities | ❌ |
| EPIC-AI-003 | Epic | Intent Capabilities | ❌ |
| FEAT-AI-005 | Feature | Cek Status Laundry | ❌ |
| FEAT-AI-006 | Feature | Cek Status Ticket | ❌ |
| FEAT-AI-007 | Feature | Jam Operasional | ❌ |
| FEAT-AI-008 | Feature | Hubungi Agent | ❌ |
| FEAT-AI-009 | Feature | Promo Aktif | ❌ |
| FEAT-AI-010 | Feature | Info Member | ❌ |
| FEAT-AI-011 | Feature | Info Layanan Outlet | ❌ |
| | | | |
| PRD-AI-004 | PRD | Pengaturan Outlet | ✅ `prd.md` |
| EPIC-AI-004 | Epic | Pengaturan Outlet | ✅ `EPIC-AI-004-pengaturan-outlet.md` |
| FEAT-AI-012 | Feature | Pengaturan Jam Operasional Outlet | ✅ `FEAT-AI-012-pengaturan-jam-operasional-outlet.md` |
| US-AI-034 | User Story | Melihat Status Konfigurasi Jam Operasional | ✅ |
| US-AI-035 | User Story | Memilih Zona Waktu Outlet | ✅ |
| US-AI-036 | User Story | Mengatur Jadwal Mingguan | ✅ |
| US-AI-037 | User Story | Jadwal Melewati Tengah Malam | ✅ |
| US-AI-038 | User Story | Membuat Jadwal Khusus | ✅ |
| US-AI-039 | User Story | Menyimpan & Mengaktifkan Jadwal | ✅ |
| US-AI-040 | User Story | Mengubah Jadwal Saat Intent Aktif | ✅ |
| US-AI-041 | User Story | Validasi Konflik & Hapus Jadwal Aktif | ✅ |
| US-AI-042 | User Story | Navigasi Aktivasi dari Control Panel | ✅ |

---

## Ringkasan

| Level | ✅ Selesai | ❌ Belum | Total |
|-------|:--:|:--:|:--:|
| PRD | 3 | 1 | 4 |
| Epic | 3 | 1 | 4 |
| Feature | 6 | 7 | 13 |
| User Story | 39 | ~25+ | ~64+ |
