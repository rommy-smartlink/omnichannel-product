# Perubahan 2026-07-21 — BR-AI-111 Autoresolve Idle Customer

## Ringkasan

Bot dapat menutup sesi percakapan secara otomatis jika customer tidak merespons.

## Files Changed

| File | Action |
|------|--------|
| `EPIC-AI-001-conversation-flow-routing.md` | +BR-AI-111 |
| `features/FEAT-AI-001-greeting-classification-handoff.md` | +BR di tabel, +US di tabel, +scope baris |
| `user-stories/US-AI-072-autoresolve-idle.md` | NEW |
| `prd.md` | +expected condition, +in scope, +proposed solution |

## Detail

- **Timer flow selesai**: 10 menit idle → notif → 5 menit grace → resolve
- **Timer mid-slot-filling**: 5 menit idle → notif → 5 menit grace → resolve
- Notifikasi 1x tanpa countdown: *"Kak, karena tidak ada balasan, saya tutup percakapan ini ya. Kalau butuh bantuan lagi, silakan chat kembali."*
- Setelah resolve → sesi baru dengan 3-gate check
- Tidak berlaku untuk sesi agent atau handoff berlangsung
- Berbeda dengan BR-AI-106 (timeout escalation = bot gagal respon → handoff agent)
