# Template

## Format PRD

```markdown
# [Nama Intent] — PRD

## Overview
[1-2 kalimat deskripsi]

## User Stories
- As a [role], saya ingin [capability] agar [benefit]

## Flow
[Step-by-step alur percakapan]

## Edge Cases
- [Scenario] → [Bot response]

## Acceptance Criteria
- [ ] [Criterion]
```

## Format GWT

```markdown
# [Nama Intent] — GWT Scenarios

> Generated dari PRD [kode] — [Nama PRD].
> Pattern mengikuti table Scenario di page "Detail User Stories": setiap skenario = 1 row, field Given/When/Then plain text.
> Kode SC auto-generate dari formula di Coda ("SC-"+RowId(thisRow)) — tidak perlu ditulis manual.

## [Nama Section]

**Goals**: [1 kalimat tujuan].

> **Sebagai [Role],**
> saya ingin [keinginan],
> sehingga [dampak/manfaat].

**Detail**:
1. [aturan bisnis / catatan perilaku — bentuknya mirip acceptance criteria]

| Prio SC | Given | When | Then |
|---------|-------|------|------|
| SC Utama | [precondition] | [aksi] | [hasil] |
| SC Pendukung | ... | ... | ... |

---

### Supporting Information (optional, ditaruh paling bawah)
- Lo-fi design / wireframe (ASCII atau referensi gambar)
- Referensi ke section PRD terkait
- Data fixture / contoh konkret (ID, nama outlet)
- Catatan asumsi atau dependency
```

Simpan PRD ke `prd/<nama-intent>.md`, GWT ke `gwt/<nama-intent>.md`.

## Penamaan File

Gunakan prefix priority `P1-`, `P2-`, dst:

```
prd/
├── P1-check_laundry_status.md   ← P1 = core MVP
├── P2-check_ticket_status.md    ← P2 = high value
├── P3-get_outlet_services.md
└── ...

gwt/
├── P1-check_laundry_status.md
├── P2-check_ticket_status.md
└── ...
```

Priority berdasarkan:
- **P1** — Core loop (hampir semua user alami)
- **P2** — High frequency
- **P3+** — Supporting intent

## Penamaan Brainstorm

```
brainstorm/
├── YYYY-MM-DD-<topik>.md   ← untuk sesi diskusi / eksplorasi
├── backlog.md               ← ide-ide yang belum diprioritaskan
└── decisions.md             ← keputusan desain yang sudah final
```
