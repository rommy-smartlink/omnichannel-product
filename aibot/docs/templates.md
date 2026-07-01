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

## Scenario: [nama scenario]

**Given** [precondition: state outlet, bot context, pesan customer]

**When** [action/intent dari customer]

**Then** [expected bot response: label ramah customer, trigger slot filling, eskalasi, dst]
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
