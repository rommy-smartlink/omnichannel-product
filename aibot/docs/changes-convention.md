# Changes Convention

Setiap batch perubahan PRD/US/BR dicatat dalam folder `changes/` di bawah direktori PRD masing-masing.

## Format File

```
<PRD-folder>/changes/YYYY-MM-DD-<topik>.md
```

Contoh: `PRD-AI-001-bot-runtime-behavior/changes/2026-07-21-BR-AI-111-autoresolve.md`

## Template

```markdown
# Perubahan YYYY-MM-DD — <Judul Singkat>

## Ringkasan

<1-2 kalimat apa yang berubah dan kenapa>

## Files Changed

| File | Action |
|------|--------|
| `<path>` | NEW / +BR / +US / edit |
| `<path>` | NEW / +BR / +US / edit |

## Detail

<poin-poin parameter penting, timer, wording, aturan>
```

## Cara Pakai

1. Update dokumen PRD/US/BR
2. Buat file perubahan di `changes/`
3. Info ke team cukup kirim nama file: *"Ada update — baca `changes/2026-07-21-<topik>.md`"*

## Rules

- Satu file per batch perubahan (bisa mencakup beberapa file)
- Timestamp di nama file = tanggal commit/push
- Ringkasan cukup untuk dibaca 30 detik — detail di file PRD/US/BR masing-masing
- Action label: `NEW` (file baru), `+BR` (business rule baru), `+US` (user story baru), `edit` (ubahan minor)
