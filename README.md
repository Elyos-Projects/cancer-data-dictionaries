# cancer-data-dictionaries

> Public cancer datasets are a foundational resource for research, yet they are routinely shipped with documentation that is incomplete, cryptic, or scattered across PDFs, wikis, and coding manuals. A r  ·  **Risk tier:** low  ·  **Status:** planning

Public cancer datasets are a foundational resource for research, yet they are routinely shipped with documentation that is incomplete, cryptic, or scattered across PDFs, wikis, and coding manuals. A researcher opening a Genomic Data Commons clinical export, a Gene Expression Omnibus series matrix, or a SEER aggregate table is confronted with field names like `ajcc_pathologic_stage`, `vital_status`, `cgc_case_days_to_death`, or `behavior_recode`, encoded values whose meaning lives only in a separate WHO/NCI coding manual, units that are implied rather than stated, and "not reported" sentinels that silently corrupt analyses. The data is technically **open**, but practically **illegible** — and illegible data produces irreproducible science and slows work that real patients are waiting on.

**Definition of shipped:** the commons/repo, merged PR, or DOI — per the per-channel definitions), with: access-tier+license+PII gate PASSed and artifact committed, **provenance completeness = 100%**, field coverage ≥ 95% (gaps listed), quality score ≥ 90/100, machine-readable outputs valid against pinned 

This is an **Elyos** good-deed project. Contributors pull a task, do it with their own coding agent, and open a PR. Platform: https://github.com/jdev1977/elyos

## Plan
- [PLAN.md](./PLAN.md) — robust enterprise plan (vision, architecture, roadmap, risks; includes an applied-improvements appendix + review sign-off)
- [TASKS.md](./TASKS.md) — schema-mapped task backlog
- [tasks/](./tasks/) — ready-to-pull task JSON(s)

## Contribute
```bash
elyos browse
elyos next --repo Elyos-Projects/cancer-data-dictionaries --no-fork
```

## Licensing & review
- Open license (see PLAN.md).
- Risk tier **low** — deeds are *delivered, not merged*; standard review applies.

> Planning stage; no adopting partner secured yet (`verifiedNeed: false` on delivery-dependent tasks).
