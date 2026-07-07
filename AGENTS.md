# AGENTS.md — Tiga-Knowledge

## Quick Facts

Knowledge repo · Markdown · macOS · single-user · no build system

## Project Structure

| Directory | Purpose |
|-----------|---------|
| `00-inbox/` | Unsorted material pending triage |
| `01-computer-science/` | CS fundamentals (OS, networking, databases, etc.) |
| `02-ai/` | AI, CV, LLM, model deployment, inference optimization |
| `03-engineering/` | Engineering practices, dev standards, debugging, deployment |
| `04-algorithms/` | Algorithms, data structures, math foundations |
| `05-papers/` | Paper reading notes, summaries, reproduction records |
| `06-books/` | Book reading notes |
| `07-courses/` | Course learning records |
| `08-interview/` | Technical interview knowledge |
| `09-english/` | English learning and technical English |
| `10-blogs/` | Blog drafts, published posts, platform archives |
| `90-assets/` | Shared images, screenshots, attachments |
| `91-templates/` | General-purpose note templates |
| `92-references/` | External resource index |
| `99-archive/` | Archived low-frequency content |
| `agent-plan/` | Agent-generated plans and reports (local-only, gitignored) |

## Commands

This is a pure knowledge repository — no build, lint, or test commands.

```bash
# Find notes by keyword
grep -r "keyword" --include="*.md" .

# List recently modified notes
find . -name "*.md" -mtime -7 -not -path './node_modules/*'
```

## Coding Standards

- UTF-8 encoding for all files.
- kebab-case for file and directory names (exceptions: conventional names like `README.md`).
- Date-prefixed files use `YYYY-MM-DD-title.md` format.
- Paper directories use `year-paper-short-name/` format.

## Markdown Generation

- Create Markdown files only when explicitly requested.
- Save agent-generated plans under `agent-plan/`.
- Exceptions: translations stay next to the source file; project files such as `CLAUDE.md`, `AGENTS.md`, `README.md` stay at their conventional locations.
- For summaries or explanations, reply in chat instead of creating files.

## Knowledge Note Standards

- Each numbered directory contains a `README.md` describing its scope and recommended taxonomy.
- Paper notes follow the per-paper directory structure defined in `05-papers/`.
- Technical conclusions must distinguish facts, speculation, and experience-based judgment.
- Code snippets must specify language, purpose, and applicable conditions.
- External references must include source links.

## Boundaries

### Always

- Route unsorted material through `00-inbox/` before placing in domain directories.
- Create sub-directories on demand — only when adding the first note on that topic; follow the recommended taxonomy in each numbered directory's `README.md`.
- Remove `.gitkeep` when adding the first real file to a directory.
- Output a change summary after reorganization operations.

### Ask First

- Large-scale moves, deletes, or renames across directories.
- Restructuring or renaming numbered directories.
- Deleting any numbered directory or its README.

### Never

- Commit secrets, tokens, private keys, company code, or customer data.
- Fabricate experimental results, formulas, or author claims in paper notes.
- Copy large verbatim passages from copyrighted material.
