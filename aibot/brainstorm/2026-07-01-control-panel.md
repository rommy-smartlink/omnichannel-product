# Bot Control Panel — MVP Spec

## Decision

MVP scope (2026-07-01):
1. ON/OFF **per intent**, per outlet
2. Hanya **owner** yang bisa toggle
3. **No bulk action**
4. **No schedule**

## Use Case

Owner dengan multiple outlet. Setiap outlet punya subset intent yang aktif/mati. Contoh:

| Outlet | check_laundry_status | check_ticket_status | get_outlet_services | ... |
|--------|---------------------|--------------------|--------------------|----|
| Outlet A | ON | ON | ON | ... |
| Outlet B | ON | OFF | ON | ... |
| Outlet C | OFF | OFF | ON | ... |

## Implikasi Intent Scope

Kalau intent OFF:
- Bot **skip** intent classification untuk itu intent
- Customer request → bot kasih fallback: "Fitur ini sedang tidak tersedia di outlet ini"
- Atau: bot auto-escalate ke agent

## Open Questions

1. Default state saat outlet baru? Semua intent ON atau OFF?
   - **Decision**: Semua ON

2. Kalau semua intent OFF, bot mati total atau still greeting?
   - **Decision**: Langsung ke agent

3. ON/OFF per intent apakah berlaku juga untuk slot-filling?
   - **Decision**: Discuss per intent later

## Data Model

```sql
outlet_intent_settings:
  outlet_id       -- FK ke outlets
  intent_name      -- 'check_laundry_status', 'check_ticket_status', dst
  is_enabled      -- boolean, default true
  created_at
  updated_at
  updated_by       -- owner user_id
```

Default: semua intent = true saat outlet baru dibuat.
