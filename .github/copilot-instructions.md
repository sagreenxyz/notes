# Copilot Instructions — Nursing Notes Wiki

## Project Purpose

This is a **nursing student wiki** built with Astro 6, deployed to GitHub Pages at `https://sagreenxyz.github.io/notes/`.

- **Note capture**: GitHub Issues are the source of truth for notes. Every issue with the `note` label is a candidate note.
- **Atomization**: A GitHub Actions workflow uses GitHub Models (`gpt-4o` via `GITHUB_TOKEN`) to detect composite notes and break them into atomic units.
- **Dynamic wiki**: `src/data/notes.json` is regenerated from all labeled issues every time a note is processed; Astro rebuilds four hierarchical view pages automatically.

Readers can scan the same content through four hierarchical lenses — **so readers can scan by different mental models**.

---

## Four Hierarchies

| View page | URL | Label prefix |
|-----------|-----|-------------|
| By Subject | `/by-subject` | `subject:*` |
| By Body System | `/by-system` | `system:*` |
| By NCLEX Domain | `/by-nclex` | `nclex:*` |
| By Clinical Priority | `/by-priority` | `priority:*` |

---

## What Makes a Note "Atomic"

An **atomic note** covers **exactly one concept** — one drug, one disease, one lab value, one framework, or one procedure. It must be:

- **Independently understandable** — a reader with nursing background can comprehend it without another note.
- **Singular in focus** — one drug, one condition, one value, one skill.
- **Cannot be meaningfully split further** — it does not contain two or more distinct concepts that each deserve their own note.

A **composite note** contains multiple atomic concepts and must be split into child issues.

---

## Atomization Rules

1. When a new issue with the `note` label is opened or edited, the `note-processor.yml` workflow runs.
2. The AI analyzes the note content.
3. If `isAtomic: false`: post a comment with proposed atom titles as a markdown checklist, add label `needs-split`.
4. If `isAtomic: true`: add label `atomic`, proceed to classify.
5. Composite issues should be closed once all child issues are created.

---

## Full Label Taxonomy

### Subject (pick one)
- `subject:pharmacology`
- `subject:anatomy`
- `subject:fundamentals`
- `subject:pathophysiology`
- `subject:nclex`
- `subject:other`

### Body System (pick all that apply)
- `system:cardiovascular`
- `system:renal`
- `system:respiratory`
- `system:neurological`
- `system:endocrine`
- `system:gastrointestinal`
- `system:musculoskeletal`
- `system:immune`
- `system:integumentary`
- `system:reproductive`

### NCLEX Domain (pick one)
- `nclex:safe-care`
- `nclex:health-promotion`
- `nclex:psychosocial`
- `nclex:physiological-integrity`

### NCLEX Sub-category (pick all that apply)
- `nclex_sub:basic-care`
- `nclex_sub:pharmacological-therapies`
- `nclex_sub:reduction-of-risk`
- `nclex_sub:physiological-adaptation`
- `nclex_sub:management-of-care`
- `nclex_sub:safety`
- `nclex_sub:growth-development`
- `nclex_sub:mental-health`

### Clinical Priority (pick one)
- `priority:critical` — life-threatening, never-do errors, immediate intervention
- `priority:high` — always monitor, significant risk, important nursing implications
- `priority:routine` — stable care, standard procedures, foundational knowledge

### Format (pick one)
- `format:table`
- `format:card`
- `format:list`
- `format:reference`

---

## `src/data/notes.json` Schema

```json
{
  "generated": "<ISO 8601 timestamp>",
  "notes": [
    {
      "id": 123,
      "number": 123,
      "title": "Furosemide — Loop Diuretic",
      "slug": "furosemide-loop-diuretic",
      "summary": "One sentence summary suitable for a card preview.",
      "content": "Full markdown content from the issue body.",
      "subject": "pharmacology",
      "systems": ["cardiovascular", "renal"],
      "nclex": "physiological-integrity",
      "nclex_sub": ["pharmacological-therapies"],
      "priority": "high",
      "format": "table",
      "labels": ["atomic", "note", "subject:pharmacology", "system:cardiovascular"],
      "url": "https://github.com/sagreenxyz/notes/issues/123",
      "created_at": "2024-01-01T00:00:00Z",
      "updated_at": "2024-01-01T00:00:00Z"
    }
  ]
}
```

---

## Astro Page Structure Conventions

- All pages live in `src/pages/` and use `src/layouts/Layout.astro`.
- Static subject pages (`pharmacology.astro`, `anatomy.astro`, etc.) contain hardcoded reference content.
- Dynamic view pages (`by-subject.astro`, `by-system.astro`, `by-nclex.astro`, `by-priority.astro`) import `src/data/notes.json` at build time and group notes accordingly.
- Components live in `src/components/`. Use `TopicCard.astro` for subject navigation cards and `NoteCard.astro` for individual note cards.
- Always use CSS variables from `Layout.astro`: `--color-primary`, `--color-primary-dark`, `--color-accent`, `--color-bg`, `--color-surface`, `--color-text`, `--color-text-muted`, `--color-border`, `--radius`, `--shadow`.
- Base URL is `/notes/` — always prefix hrefs with `` `${import.meta.env.BASE_URL}` ``.

---

## Nursing Content Field Conventions

### Drug note
- Generic name + brand name in title (e.g., "Furosemide — Loop Diuretic")
- Fields: drug class, mechanism of action, indications, nursing implications, adverse effects, contraindications, reversal agent (if any)

### Disease / Condition note
- Condition name in title
- Fields: definition, pathophysiology, clinical manifestations, diagnostics, nursing interventions, patient education

### Lab value note
- Lab name + context (e.g., "Serum Potassium — Normal & Critical Values")
- Fields: normal range, critical values, causes of abnormality, nursing actions

### Framework / Concept note
- Framework name (e.g., "ADPIE Nursing Process")
- Fields: definition, components, clinical application, mnemonic (if any)

### Procedure note
- Procedure name (e.g., "Nasogastric Tube Insertion")
- Fields: purpose, supplies, steps, safety checks, complications, patient teaching

---

## Cross-Reference Conventions

Link related notes by GitHub issue number using `#<number>` in the issue body. The `notes.json` rebuild will preserve `related_issues` if listed as a comma-separated list in the issue body under the heading `## Related Notes`.

---

## CSS Variable Usage

Always use the existing CSS variables — never hardcode colors. For priority colors:
- Critical: `#dc2626` (red)
- High: `#d97706` (amber)
- Routine: `#16a34a` (green)
