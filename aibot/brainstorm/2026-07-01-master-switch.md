# Master Switch — Bot Active/Inactive per WABA

> Brainstorm 2026-07-01 (sesi 2, lanjutan Control Panel)
> Trigger: user kepikiran switch besar untuk toggle active bot semua outlet.

## Decision (awal)

1. **Master switch per-WABA**, bukan per-outlet.
   - Lokasi: di halaman Control Panel, header atas (di atas search/sort).
   - State: ON (default) / OFF.
2. **Akses**: Owner per-WABA. Tidak ada role lain (non-goal MVP).
3. **Saat OFF**:
   - Bot skip greeting.
   - Bot skip intent classification.
   - Conversation langsung di-eskalasi ke agent.
   - Customer tidak terima response bot apapun.
4. **Override priority**:
   - Master switch OFF > per-outlet setting apapun (jika per-outlet intent ON tapi master OFF, bot tetap off).
   - Master switch ON > per-outlet setting (jika master ON tapi per-outlet intent OFF, bot off untuk outlet itu saja).
5. **No schedule, no bulk-per-intent** (sama seperti Control Panel).

## Use Case

| Skenario | Tindakan Owner | Expected |
|----------|----------------|----------|
| Maintenance window (1 jam) | Master switch OFF | Semua outlet di WABA → agent. Owner switch ON lagi setelah maintenance. |
| Emergency (bug, abuse) | Master switch OFF | Stop bot secepat mungkin, semua outlet aman. |
| WABA baru (setelah aktivasi) | Master switch ON (default) | Bot langsung aktif untuk semua outlet, per-outlet setting ikuti default ON. |
| WABA pause sementara | Master switch OFF | Customer masih bisa chat, tapi langsung ke agent. |

## Affected Intents

Semua intent terpengaruh saat master switch OFF:

| Intent | Toggleable per-outlet | Affected by master OFF |
|--------|----------------------|------------------------|
| `check_laundry_status` | ✓ | ✓ |
| `check_ticket_status` | ✓ | ✓ |
| `get_outlet_services` | ✓ | ✓ |
| `get_operating_hours` | ✓ | ✓ |
| `get_active_promos` | ✓ | ✓ |
| `get_member_info` | ✓ | ✓ |
| `talk_to_agent` | ✗ (always ON per-outlet) | ✗ — but irrelevant karena bot skip semua, conversation langsung ke agent anyway |
| `unknown` | ✗ (internal) | ✓ — internal, tidak ke customer |

## Data Model

Tambah tabel baru (atau extend tabel settings yang ada):

```sql
waba_bot_settings:
  waba_id        uuid NOT NULL REFERENCES wabas(id) ON DELETE CASCADE
  is_enabled     boolean NOT NULL DEFAULT true
  updated_by     uuid REFERENCES users(id)
  updated_at     timestamptz NOT NULL DEFAULT now()

PRIMARY KEY (waba_id)
```

Default: `is_enabled = true` saat WABA baru dibuat / diaktivasi.

## Bot Flow Integration

Saat customer mulai percakapan di outlet X (X.waba_id = W):

```
1. Customer send message ke outlet X
2. Bot load:
   a. waba_bot_settings WHERE waba_id = W → master_switch_state
   b. outlet_intent_settings WHERE outlet_id = X → per_intent_state
3. Decision:
   - master_switch_state = OFF → langsung escalate ke agent, skip greeting + classification
   - master_switch_state = ON → lanjut ke logic per-outlet seperti PRD Control Panel saat ini
4. talk_to_agent: implicit always-on di level customer (meskipun bot off, command "hubungi agen" tetap bisa karena agent yang handle)
```

## UI / UX

### Owner Control Panel — Layout update

```
┌─────────────────────────────────────────────────────────┐
│  🤖 Bot Control Panel                       Owner (User) │
├─────────────────────────────────────────────────────────┤
│  Status Bot:   [ ●──── ON  ]  ← master switch           │
│  Last updated: 2 jam lalu oleh Owner                     │
│  ℹ️  Saat OFF, semua outlet di WABA ini langsung ke agent│
├─────────────────────────────────────────────────────────┤
│  Search: [____________________]  Sort: [Nama A-Z ▾]     │
├─────────────────────────────────────────────────────────┤
│  ... card outlet list (sama seperti Control Panel) ...  │
└─────────────────────────────────────────────────────────┘
```

- Master switch di paling atas, sebelum list outlet.
- Saat OFF: visual cue (warna merah / icon warning), banner info.
- Saat ON: default state, warna hijau / icon normal.

### Interaction

- Master switch adalah toggle langsung, **TIDAK** perlu modal konfirmasi (sama prinsip dengan toggle per-outlet: simple, reversible).
- Audit: `updated_by` + `updated_at` di tabel `waba_bot_settings`.
- Optional: tampil "last updated" + siapa yang toggle (untuk transparansi owner).

## Edge Cases

| Scenario | Behavior |
|----------|----------|
| Master OFF, customer chat | Conversation langsung di-eskalasi ke agent. Customer tidak terima response bot. |
| Master OFF, customer sedang dalam flow bot | Flow selesai normal, request berikutnya akan skip. |
| Master OFF, agent juga tidak tersedia | Fallback: customer terima pesan "Mohon maaf, semua tim sedang sibuk. Silakan coba lagi nanti." (TBD — di luar scope MVP, atau default behaviour platform) |
| Toggle master saat bot proses chat aktif | Mirip per-outlet: apply di request berikutnya. |
| 2 device toggle master bersamaan | Last-write-wins, sama dengan per-outlet. |
| Owner lupa switch ON lagi setelah maintenance | Tidak ada auto-revert. Owner bertanggung jawab. |
| WABA baru aktivasi | Default master switch ON. |
| WABA delete (deaktivasi) | Cascade ke `waba_bot_settings`. |

## Acceptance Criteria (draft)

### Functional
- [ ] Owner bisa lihat status master switch di header Control Panel.
- [ ] Owner bisa toggle master switch ON/OFF langsung dari header.
- [ ] Saat master switch OFF, semua outlet di WABA tersebut bot-nya off.
- [ ] Saat master switch ON, per-outlet setting berlaku normal.
- [ ] Perubahan master switch ter-audit (`updated_by`, `updated_at`).
- [ ] Default state WABA baru: master switch ON.

### Bot Behavior
- [ ] Master OFF → customer message → langsung ke agent, skip greeting + classification.
- [ ] Master ON + per-outlet intent ON → bot berjalan normal.
- [ ] Master ON + per-outlet intent OFF → fallback message (sama PRD Control Panel).
- [ ] Master toggle baru apply di request customer berikutnya.

### Security
- [ ] Endpoint toggle master switch: require auth + ownership WABA check.
- [ ] Server-side: `wabas.owner_id = current_user.id`.

## Implikasi ke Existing

- **PRD P8 — Control Panel** perlu update section "Bot Flow Integration" untuk hierarki keputusan (master > per-outlet).
- **DB schema**: tambah tabel `waba_bot_settings`.
- **API**: tambah endpoint `PATCH /api/control-panel/wabas/{waba_id}/settings` untuk toggle master.
- **Frontend**: tambah section header di halaman Control Panel.

## Open Questions

1. **Apakah master switch juga mematikan greeting & quick reply automation?**
   - Greeting: kemungkinan ya (skip greeting kalau bot off total).
   - Quick reply automation (auto-reply inbound): perlu diskusi — apakah ini "bagian dari bot" atau "fitur terpisah"?
2. **Apakah customer perlu terima pesan "Bot sedang maintenance" sebelum di-eskalasi?**
   - Alternatif: langsung silent escalate. Customer cuma lihat agent yang join.
   - Trade-off: transparansi vs. clean handoff.
3. **Apakah master switch perlu konfirmasi (confirm dialog) saat OFF?**
   - Pro: cegah accidental toggle, karena dampaknya luas (semua outlet).
   - Kontra: friction saat emergency.
   - Default MVP: tanpa konfirmasi, tapi bisa tambah later.
4. **Master switch OFF — apakah customer masih bisa kirim pesan?**
   - Default: ya, langsung ke agent (tidak ada blocking di WhatsApp level).
5. **Apakah perlu notifikasi ke owner kalau master switch di-toggle orang lain?**
   - Non-goal MVP. Audit log cukup.

## Out of Scope (Future)

- Schedule (auto-toggle master switch by hour/day).
- Per-feature master switch (mis. ON greeting tapi OFF escalation).
- Multi-level master (WABA > Group Outlet > Outlet).
- Slack/email notification saat master di-toggle.
- Auto-toggle kalau agent count = 0.