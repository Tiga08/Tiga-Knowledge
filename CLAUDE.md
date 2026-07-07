# CLAUDE.md — Tiga-Knowledge

@AGENTS.md

## Architecture Decisions

1. **Numbered directories (00-10):** Fixed-order prefixes keep `ls` output stable and make the scope of each domain immediately visible. Adding a new domain means picking the next number.
2. **Inbox-first workflow:** All unsorted material enters `00-inbox/` before being triaged into a numbered domain directory. This prevents misclassification and keeps the main tree clean.
3. **Nested sub-domains, created on demand:** Directories with broad scope (e.g., `02-ai/computer-vision/`) use a second level of nesting to keep topics navigable, but sub-directories are only created when the first note on that topic arrives — no pre-built empty skeletons.
4. **Content vs metadata separation:** Knowledge notes, reading records, and learning materials live here. Agent skills, workflow definitions, and prompt templates belong in `Tiga-Skills`. Environment configs belong in `Tiga-Configs`.

## Common Gotchas

- **`.gitkeep` vs real files:** Only empty top-level placeholder directories (`90-assets/`, `91-templates/`, `92-references/`, `99-archive/`) use `.gitkeep`. When adding real content, delete the `.gitkeep` — don't leave both. Never pre-create empty sub-directories.
- **Content ownership boundaries:** Technical notes and learning records go here. Prompts and agent skills go in `Tiga-Skills`. Resume and interview narratives go in `Tiga-Resume`. Personal identity facts go in `Tiga-OS`.
- **Paper notes:** Paper notes live under `05-papers/{sub-domain}/year-paper-short-name/`. Each paper gets its own directory with `README.md`, `summary.zh.md`, `notes.md`, etc. Never fabricate experimental results, formulas, or author claims.
- **Sensitive information:** Never commit company code, internal docs, customer data, or production data. Strip tokens, IPs, account names, and client names from screenshots, logs, and configs.

## Agent Collaboration Rules

1. Agent-generated plans go into `agent-plan/`.
2. Unsorted material goes into `00-inbox/` first.
3. Generate a plan before any large-scale move, delete, or rename operation.
4. Technical conclusions must distinguish facts, speculation, and experience-based judgment.
5. Paper notes must not fabricate experimental results, formulas, or author claims.
6. Code snippets must specify language, purpose, and applicable conditions.
7. Output a change summary after each reorganization.

## Privacy & Security Rules

1. Do not commit company-sensitive code, internal documents, customer data, or production data.
2. Project experience records contain only de-identified technical approaches, post-mortems, and general lessons.
3. Screenshots, logs, and configs must have tokens, IPs, account names, paths, and client names removed.
4. External references must include source links.
5. Do not copy large verbatim passages from copyrighted material.
