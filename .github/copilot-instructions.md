# GitHub Copilot Instructions

Bot AI Omnichannel untuk WhatsApp — chatbot slot-filling berbasis LLM, melayani CS lapis pertama setelah customer memilih outlet. Customer chat natural, bot pahami intent, kumpulkan info yang kurang, jawab dari data tersedia, atau eskalasi ke agen manusia.

## Bahasa & Gaya

- **Preferred**: Bahasa Indonesia untuk semua output (PRD, GWT, brainstorm notes, komentar)
- Istilah teknis (intent, slot-filling, escalation) boleh tetap bahasa Inggris bila lebih tepat
- Format markdown tetap English-friendly (headers, table)

## Referensi Utama

| File | Isi |
|------|-----|
| [aibot/docs/intents.md](aibot/docs/intents.md) | Daftar intent MVP |
| [aibot/docs/bot-behavior.md](aibot/docs/bot-behavior.md) | Behavior rules, customer-friendly labels |
| [aibot/docs/conversation-flow.md](aibot/docs/conversation-flow.md) | Arsitektur & alur percakapan |
| [aibot/docs/open-questions.md](aibot/docs/open-questions.md) | Open questions — butuh jawaban dari product |
| [aibot/docs/templates.md](aibot/docs/templates.md) | Template PRD & GWT format |
| [aibot/docs/main.md](aibot/docs/main.md) | Feature overview utama |

## Scope Kerja

Semua artefak kerja berada di dalam folder `aibot/`.

- Generate **PRD** per intent/fitur → `aibot/prd/<topic>/prd.md`
- Generate **GWT** per intent → `aibot/gwt/<topic>/gwt.md`
- **Brainstorming** produk → `aibot/brainstorm/`
- **Reference docs** (intents, behavior, flow, templates) → `aibot/docs/`

Tidak untuk development/coding.
