# TASKS — cancer-data-dictionaries

> Status: Draft · Version: 0.1.0 · Last updated: 2026-06-28 · Owner: TBD (maintainer) · Lane: donated

## How these tasks map to Elyos

Each task below becomes an Elyos **Task JSON** validated against
`packages/schema/src/schemas.ts`. Field mapping:

- `id` — stable slug from the tables (e.g. `cancer-data-dictionaries-model-001`).
- `title` — the table's Title.
- `project` — `cancer-data-dictionaries`.
- `type` — one of `code | research | writing | data | design-spec | maintenance` (per table).
- `lane` — `donated` for all tasks here (no funded escrow). A funded task would add `fundedBudgetUsd`.
- `priority` — `high | medium | low`.
- `domain` — array, e.g. `["cancer-research","open-data","bioinformatics","open-science"]`.
- `riskTier` — `low | medium | high`. Core dictionaries `low`; field-meaning/license judgement
  `medium`; **patient-facing educational content `high`** (oncologist + advocate sign-off).
- `urgent` — boolean; `false` for all current tasks.
- `deliverable` — `pr | dataset | document | translation`. We **never** deliver `dataset` (data is
  out of scope); code → `pr`, dictionaries/docs → `document`, translations → `translation`.
- `tokenEstimate` — `small | medium | large` (Size column).
- `status` — `open | in-progress | review | delivered | done`; all start `open`.
- `context`, `objective`, `acceptanceCriteria[]`, `resources[]`, `output` — per task.
- `requestor` — TO BE SECURED until a partner is confirmed.
- `verifiedNeed` — **`false`** until a named commons/repo/advocacy partner agrees to adopt
  contributions (general need is real; per-task delivery need is unproven).
- `outputLicense` — `CC-BY-4.0` for dictionaries/docs; `MIT` for code (emitters/validators/crosswalks).

**Binding cancer guardrail on every per-dataset task:** open-access / aggregate / de-identified data
only; controlled-access and identifiable data are EXCLUDED at `gate-002`; provenance on every
assertion. Listing a per-dataset task here does **not** pre-approve it — each needs its own committed
gate artifact before work starts.

---

## Milestone M0 — Foundation & cold-start

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
| --- | --- | --- | --- | --- | --- | --- | --- |
| cancer-data-dictionaries-model-001 | Dictionary template + canonical dictionary model | writing | small | low | document | — | Technical |
| cancer-data-dictionaries-gate-002 | Access-tier + license + PII triage gate (blocking) | design-spec | small | medium | document | — | Access+License+PII |
| cancer-data-dictionaries-ontology-policy-003 | Ontology/codelist + NC/custom-source acceptance policy | design-spec | small | medium | document | gate-002 | Access+License+PII |
| cancer-data-dictionaries-reviewer-004 | Name/secure the Access+License+PII reviewer (blocking role) | research | small | low | document | — | Maintainer |
| cancer-data-dictionaries-emit-005 | Emitters: JSON dictionary + Frictionless Table Schema + Croissant | code | medium | low | pr | model-001 | Technical |
| cancer-data-dictionaries-validator-006 | Dictionary validator (provenance-100%, code integrity) + bounded-sample check | code | small | low | pr | model-001 | Technical |
| cancer-data-dictionaries-outreach-007 | Partner outreach + candidate dataset shortlist | research | small | low | document | — | Maintainer |
| cancer-data-dictionaries-pilot-008 | End-to-end dictionary for one open-access pilot dataset | data | medium | medium | document | model-001, gate-002, ontology-policy-003, emit-005, validator-006, outreach-007, reviewer-004 | Access+License+PII, Technical, Domain |

**Acceptance criteria — key tasks**

- **model-001 (template + canonical model)**
  - [ ] Canonical model documents every field from PLAN: `datasetId`, `title`, `publisher`,
        `accessTier`, `sourceUrls`, `license{id,url,permitsDerivatives,snapshotRef}`,
        `provenance{...,docSources[]}`, `pii{...}`, `tables[].fields[]{name,label,dataType,units,
        codeSystem,allowedValues[]{code,meaning,sourceRef},nullable,sentinels[],description,caveats,
        provenance{sourceRef}}`, `crosswalks[]`, `qualityScore{before,after}`, `specVersions`.
  - [ ] **Sentinel / "not reported" conventions** are a first-class field attribute.
  - [ ] **Every field-level assertion requires a `provenance.sourceRef`** in the model.
  - [ ] Markdown template covers field dictionary, coded-value tables, provenance, license record,
        known caveats; states the deliverable is documentation, not data; output `CC-BY-4.0`.
  - [ ] One filled-in worked-example skeleton included.

- **gate-002 (access-tier + license + PII gate)**
  - [ ] **Access-tier check runs first and is blocking**: `controlled`/`mixed` → EXCLUDE; only
        `open` proceeds. Enumerated exclusions: dbGaP, EGA, controlled TCGA/ICGC/TARGET,
        individual-level biobanks, any DUA/DAC/IRB-gated source, any identifiable data.
  - [ ] License rule: PASS only if `license.permitsDerivatives:true` from a cited clause/URL;
        COSMIC/OncoKB/GENIE/IARC pre-flagged; missing/unparseable = FLAG/EXCLUDE (no default-allow).
  - [ ] Per-source defaults encoded: GDC/TCGA open tier + GEO + ClinVar + DepMap(CC BY) + EBI
        (Expression Atlas/PRIDE) + CPTAC open = eligible; **SEER aggregate eligible, case-level SEER
        (DUA) EXCLUDED**; TCIA per-collection verify.
  - [ ] PII methodology: direct-identifier scan, quasi-identifier/k<5 + small-cell flag, genomic
        re-identification awareness (individual-level germline = identifiable). Never de-identify
        ourselves.
  - [ ] Inspection (open data only) follows the access protocol (header/streamed, ≤1,000-row cap,
        local-only ephemeral, no committed samples, halt on identifiability).
  - [ ] Produces a committed PASS/FLAG/EXCLUDE artifact per dataset recording which checks ran and
        what fired, plus the recorded `accessTier`.

- **emit-005 (emitters)**
  - [ ] Emits canonical JSON → valid **Frictionless Table Schema** + **Croissant ML v1.0** (pinned
        in `specVersions`).
  - [ ] Golden input→output fixtures diffed in CI; outputs validated against pinned specs.
  - [ ] Code MIT; `pnpm build && pnpm test && pnpm lint` green; DCO signed-off.

- **validator-006 (dictionary validator)**
  - [ ] **Fails the build if any assertion lacks a `sourceRef`** (provenance = 100% enforced).
  - [ ] Checks code/value integrity (no orphan codes, valid enums) and **rejects `accessTier:
        controlled`** via a committed fixture.
  - [ ] Optional bounded-sample schema check honors the inspection access protocol; no real data
        committed. Code MIT; CI green; no credentials embedded.

- **pilot-008 (pilot dataset, end-to-end)**
  - [ ] Pilot selected on a realistic adoption path first: informal channel or self-serve fallback
        (GitHub PR to the dataset's repo, or a Zenodo record/DOI).
  - [ ] Dataset PASSed `gate-002` (`accessTier: open`, license permits derivatives, no identifiable
        data) with the artifact committed.
  - [ ] Complete field dictionary with coded-value meanings + sentinels; **100% provenance
        coverage**; field coverage ≥ 95% (gaps listed); quality score recorded (after ≥ 90/100).
  - [ ] Valid JSON + Frictionless + Croissant emitted; validator clean in CI.
  - [ ] Provenance recorded (source URLs, publisher, version, retrieval date, attribution; license
        snapshot = committed copy + SHA-256 + Wayback URL).
  - [ ] **Adopted** via informal channel or self-serve fallback with the Steward's
        `outcomes/<dataset-id>.json` recorded — or **submitted** with the blocker surfaced.

**M0 Definition of Done:** Access+License+PII reviewer named (before pilot review); template +
canonical model + gate + ontology/NC policy published; emitters + validator green in CI with golden
fixtures (incl. controlled-tier-rejection); one open-access pilot dictionary documented end-to-end
with 100% provenance and **adopted** (evidence recorded) — or submitted with blocker surfaced; ≥ 1
partner-outreach thread opened.

---

## Milestone M1 — Gate hardened, crosswalks, first adoptions

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
| --- | --- | --- | --- | --- | --- | --- | --- |
| cancer-data-dictionaries-snapshot-009 | License + source-doc snapshot capture in provenance flow | code | small | low | pr | model-001, gate-002 | Technical |
| cancer-data-dictionaries-crosswalk-010 | Controlled-vocabulary crosswalk tooling + ontology-license enforcement | code | medium | medium | pr | model-001, ontology-policy-003 | Technical, Domain |
| cancer-data-dictionaries-triage-011 | Triage ≥ 3 candidate datasets through the gate | research | medium | medium | document | gate-002, ontology-policy-003, outreach-007 | Access+License+PII |
| cancer-data-dictionaries-dict-012 | Dictionary for open dataset #2 | data | medium | medium | document | pilot-008, triage-011 | Access+License+PII, Technical, Domain |
| cancer-data-dictionaries-dict-013 | Dictionary for open dataset #3 | data | medium | medium | document | pilot-008, triage-011 | Access+License+PII, Technical, Domain |
| cancer-data-dictionaries-partner-014 | Secure first confirmed adoption partner | research | small | low | document | outreach-007 | Steward |

**Acceptance criteria — key tasks**

- **snapshot-009 (license + source-doc snapshot)**
  - [ ] Implements the decided format: committed copy of license text/page + each authoritative
        source-doc reference + SHA-256 + Wayback save URL; `license.snapshotRef` and
        `provenance.docSources[]` record path, hash, timestamp. Bare URL alone insufficient.
  - [ ] Code MIT; tests + CI green; no credentials embedded.

- **crosswalk-010 (crosswalk tooling)**
  - [ ] Maps coded fields to pinned ontology versions (ICD-O-3, NCIt, HGVS, MONDO).
  - [ ] **Enforces the ontology-licensing policy in code**: restricted vocab (e.g. SNOMED CT)
        emits link-out by code, never republished term text — proven by a CI fixture.
  - [ ] Crosswalk version pinned per mapping; code MIT; CI green.

- **triage-011 (triage ≥ 3 candidates)**
  - [ ] Each dataset gets a committed gate artifact (access-tier + PASS/FLAG/EXCLUDE + rationale),
        applying the ontology/NC policy.
  - [ ] Each PASS records `accessTier: open`, license id/URL/snapshot, `permitsDerivatives:true`
        with cited evidence.
  - [ ] PII methodology applied; any identifiable/controlled/unclear-license dataset is
        EXCLUDED/FLAGGED with the concern surfaced.

- **partner-014 (first confirmed partner)**
  - [ ] A named commons/repo/advocacy maintainer confirms they will review and adopt dictionaries.
  - [ ] Contribution mechanism documented (PR vs. commons docs process vs. Zenodo companion).
  - [ ] Tasks for that partner updated to `verifiedNeed:true` with `requestor` set.

**M1 Definition of Done:** gate applied to ≥ 3 datasets with committed artifacts; crosswalk tooling
enforces ontology licensing (fixture-proven); ≥ 2 dictionaries adopted upstream (evidence recorded);
≥ 1 confirmed partner; snapshot capture working (committed copy + SHA-256 + Wayback).

---

## Milestone M2 — Scale + researcher plain-language layer

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
| --- | --- | --- | --- | --- | --- | --- | --- |
| cancer-data-dictionaries-score-015 | Automated dictionary-quality / coverage scoring | code | small | low | pr | validator-006 | Technical |
| cancer-data-dictionaries-glossary-016 | Researcher plain-language glossary layer (≥ 2 datasets) | writing | medium | medium | document | dict-012, dict-013 | Domain, Technical |
| cancer-data-dictionaries-catalog-017 | Triage candidate catalog → approved shortlist | research | medium | medium | document | gate-002, ontology-policy-003 | Access+License+PII |
| cancer-data-dictionaries-dict-018 | Dictionaries for open datasets #4 and #5 | data | large | medium | document | crosswalk-010, partner-014, catalog-017 | Access+License+PII, Technical, Domain |

**Acceptance criteria — key tasks**

- **score-015 (quality scoring)**
  - [ ] Computes field coverage, value-set coverage, provenance completeness (must be 100% to pass),
        unit/sentinel completeness, machine-validity; records before/after in the artifact.
  - [ ] Code MIT; CI green.

- **glossary-016 (researcher glossary)**
  - [ ] Plain-language entries for confusing fields/terms in ≥ 2 delivered dictionaries, each
        sourced (provenance), framed for *researchers/analysts* — **not** patient advice.
  - [ ] Domain-reviewed for accuracy; `CC-BY-4.0`.

- **dict-018 (datasets #4 and #5)**
  - [ ] Both PASS the gate with committed artifacts (`accessTier: open`).
  - [ ] Dictionaries emitted with crosswalks where licensing permits; adopted upstream with
        acceptance evidence recorded.
  - [ ] Per-dataset effort logged (AI-session minutes + review cycles) to show reduction vs. M0/M1
        baseline median.

**M2 Definition of Done:** ≥ 5 dictionaries adopted cumulatively across ≥ 2 sources; researcher
glossary shipped for ≥ 2 datasets; quality scoring automated; measurable median per-dataset effort
reduction vs. the M0/M1 baseline.

---

## Milestone M3 — Patient-facing layer (gated, high) + reuse outcomes + sustainability

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
| --- | --- | --- | --- | --- | --- | --- | --- |
| cancer-data-dictionaries-patient-glossary-019 | Patient-facing educational glossary (oncologist + advocate reviewed) | writing | medium | high | document | glossary-016 | Oncologist, Patient-advocate, Domain |
| cancer-data-dictionaries-reuse-020 | Track and verify external reuse events | research | small | low | document | dict-012, dict-013, dict-018 | Steward |
| cancer-data-dictionaries-refresh-021 | Staleness/refresh process + schema-drift detection | maintenance | small | low | document | validator-006 | Maintainer |
| cancer-data-dictionaries-dict-022 | Dictionaries for open datasets #6–#8 | data | large | medium | document | dict-018, refresh-021 | Access+License+PII, Technical, Domain |

**Acceptance criteria — key tasks**

- **patient-glossary-019 (patient-facing, HIGH RISK)**
  - [ ] **Hard gate:** released only with **both** oncologist **and** patient-advocate sign-off;
        without the panel, the item is **deferred, not shipped**.
  - [ ] Every entry carries a prominent **"not medical advice"** notice and routes readers to their
        care team; education only — no diagnostic/prognostic/treatment guidance.
  - [ ] Every assertion sourced (provenance 100%); plain language verified with an advocate.
  - [ ] `riskTier: high`; `CC-BY-4.0`.

- **reuse-020 (reuse tracking)**
  - [ ] ≥ 3 verifiable external reuse events recorded (citation / commons adoption / PR reference),
        each linking to externally verifiable evidence (no self-reported reuse).

- **refresh-021 (staleness/refresh)**
  - [ ] Documented process to detect when a documented dataset has drifted from its recorded
        version/release; validator flags schema drift; stale dictionaries become `maintenance` tasks.

**M3 Definition of Done:** ≥ 3 verifiable reuse events; ≥ 8 dictionaries adopted cumulatively;
staleness/refresh process documented + steward named; **if** oncologist + advocate reviewers
secured, ≥ 1 patient glossary released with both sign-offs and "not medical advice" — otherwise
explicitly deferred.

---

## Candidate dataset catalog (license + access-tier classified)

First-pass triage signals only — no entry becomes a task until it PASSes `gate-002`. Per-dataset
dictionary tasks instantiate the shared template (field dictionary + coded values + provenance +
license snapshot + JSON/Frictionless/Croissant + crosswalks where licensed); all are
`type: data`, `deliverable: document`, `lane: donated`, `verifiedNeed:false`,
`requestor: TO BE SECURED`. **Priority:** A = clearly open + low/no individual-level data
(good-first-deed); B = license `verify`/per-item; C = aggregate-sensitive (reviewer required);
FLAG = NC/custom (policy review); EXCLUDE = controlled/identifiable (no task).

| Catalog ID | Dataset | Access tier | License signal | Disposition |
| --- | --- | --- | --- | --- |
| cat-gdc-001 | GDC / TCGA **open tier** clinical & biospecimen fields | open | NIH/NCI open | A |
| cat-gdc-002 | GDC open-tier expression / copy-number derived fields | open | NIH/NCI open | A |
| cat-geo-001 | GEO series-matrix metadata fields (open series) | open | NCBI public | A |
| cat-clinvar-001 | ClinVar variant/clinical-significance fields | open | NCBI public | A |
| cat-depmap-001 | DepMap cell-line dependency / model metadata | open | CC BY 4.0 | A |
| cat-ea-001 | EBI Expression Atlas experiment metadata | open | CC BY 4.0 | A |
| cat-pride-001 | PRIDE proteomics submission metadata | open | open (EBI) | A |
| cat-cptac-001 | CPTAC open-tier proteomic metadata (PDC) | open | open | A |
| cat-seer-001 | SEER\*Explorer **aggregate** incidence/mortality tables | aggregate | NCI open | A |
| cat-tcia-001 | TCIA imaging collection metadata (CC BY collections) | open | CC BY (per-collection) | B |
| cat-cosmic-001 | COSMIC mutation catalogue fields | open (registration) | non-commercial/custom | FLAG |
| cat-oncokb-001 | OncoKB annotation fields | open (terms) | custom/non-commercial | FLAG |
| cat-genie-001 | AACR Project GENIE fields | registered | custom / NC-ND | FLAG |
| cat-iarc-001 | IARC GLOBOCAN / Cancer Today aggregate fields | aggregate | custom / NC | FLAG |
| cat-seer-002 | SEER **case-level** research data | controlled (DUA) | DUA required | EXCLUDE |
| cat-dbgap-001 | dbGaP individual-level study data | controlled | DAC/IRB | EXCLUDE |
| cat-ega-001 | EGA individual-level data | controlled | DAC | EXCLUDE |
| cat-tcga-ctrl-001 | TCGA **controlled tier** (germline / raw reads) | controlled | dbGaP-gated | EXCLUDE |
| cat-biobank-001 | Individual-level biobank record data (e.g. UK Biobank) | controlled | application/approval | EXCLUDE |

**Catalog triage task — catalog-017 acceptance criteria**
- [ ] Every entry assigned a disposition with a committed gate artifact: `APPROVED` (`accessTier:
      open`, `permitsDerivatives:true` cited, PII acceptable), `VERIFY-LATER`, `FLAG` (policy), or
      `EXCLUDED` (controlled/identifiable).
- [ ] `verify` rows resolved with a cited license URL; unresolved rows stay `VERIFY-LATER`.
- [ ] FLAG rows (COSMIC/OncoKB/GENIE/IARC) decided strictly per the NC/custom policy with rationale.
- [ ] EXCLUDE rows (controlled/identifiable) get **no** dictionary task — confirmed and recorded.
- [ ] Output is a committed shortlist (≥ 6 `APPROVED` candidates) ordered by realistic adoption path.

---

## Backlog / future

| ID | Title | Type | Size | Risk | Deliverable | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| cancer-data-dictionaries-linkml-023 | LinkML / CDISC mapping export | code | medium | low | pr | If structured-modeling consumers enter scope |
| cancer-data-dictionaries-i18n-024 | Translate a delivered glossary (domain/advocate reviewer) | translation | small | medium | translation | Researcher glossary `medium`; patient glossary would be `high` |
| cancer-data-dictionaries-dash-025 | Outcome dashboard (adoptions + reuse events) | code | medium | low | pr | Reads the outcome ledger; supports success metrics |
| cancer-data-dictionaries-lookup-026 | Static dictionary-lookup site/API over delivered dictionaries | code | medium | low | pr | Read-only over CC-BY dictionaries; no data hosting |

> Note: `i18n-024` is authored as an Elyos task with `type: writing` + `deliverable: translation`
> (`translation` is a deliverable, not a `type`).

---

## Acceptance criteria — generated rows (previously without explicit bullets)

These mirror the committed `tasks/<id>.json` so every table row has checkable criteria.

- **ontology-policy-003 (ontology/NC-source policy)**
  - [ ] Names the accepted ontologies/codelists and pinned-version policy (ICD-O-3, NCIt, HGVS,
        MONDO) and the restricted-vocabulary rule (e.g. SNOMED CT: link-out by code only, never
        republish licensed term text).
  - [ ] Decides COSMIC/OncoKB/GENIE/IARC per the NC/custom rule, consistent with gate-002
        (no default-allow; missing/unparseable license = FLAG/EXCLUDE).
  - [ ] Every coded-value meaning carries a `provenance.sourceRef`; referenced by gate-002 and crosswalk.

- **reviewer-004 (Access+License+PII reviewer)**
  - [ ] A named individual/role recorded as reviewer, secured before the pilot-008 review.
  - [ ] Review scope documented (confirms accessTier, license `permitsDerivatives` with evidence, PII).
  - [ ] Escalation/refusal path: controlled/identifiable/unclear-license datasets EXCLUDED/FLAGGED.

- **outreach-007 (partner outreach + shortlist)**
  - [ ] ≥ 1 partner-outreach thread opened (contact + ask recorded).
  - [ ] Shortlist limited to open-access/aggregate sources, each with a realistic adoption path.
  - [ ] `verifiedNeed` stays `false` until a partner confirms; no dataset approved without its gate artifact.

- **dict-012 / dict-013 (open datasets #2 / #3)** — per-dataset template (one task per dataset)
  - [ ] Dataset PASSed gate-002 (`accessTier: open`, permits derivatives, no identifiable data), artifact committed.
  - [ ] 100% provenance; field coverage ≥ 95% (gaps listed); quality score recorded (after ≥ 90/100).
  - [ ] Valid JSON + Frictionless + Croissant; validator clean; crosswalks where licensing permits.
  - [ ] License snapshot (committed copy + SHA-256 + Wayback); adopted upstream or submitted with blocker.

- **dict-022 (open datasets #6–#8)** — same per-dataset template applied to each of the three datasets.

- **linkml-023 (LinkML / CDISC export)**
  - [ ] Exports the canonical model to valid LinkML + CDISC artifacts, versions pinned in `specVersions`.
  - [ ] Golden fixtures diffed in CI; code MIT; `pnpm build && pnpm test && pnpm lint` green; DCO signed-off.

- **i18n-024 (translate a delivered glossary)**
  - [ ] Researcher glossary (only; patient glossary excluded — that would be `high`) translated into a named language.
  - [ ] Domain/advocate reviewer fluent in the target language; meaning + provenance preserved.
  - [ ] Source-compatible license (CC-BY-4.0); no third-party copyrighted source text relicensed.

- **dash-025 (outcome dashboard)**
  - [ ] Reads the outcome ledger (adoptions + verified reuse events); read-only; hosts no dataset content; code MIT; CI green.

- **lookup-026 (static dictionary-lookup site/API)**
  - [ ] Read-only over delivered CC-BY dictionaries (no data hosting); attribution preserved; static build; code MIT; CI green.

---

## Fan-out note

Per-dataset dictionary tasks (`dict-012`, `dict-013`, `dict-018`, `dict-022`) are **representative**:
the concrete datasets are not enumerated here. The candidate catalog above is triage-signal only and
**no entry becomes a task until it PASSes `gate-002`** with its own committed artifact, so per-dataset
tasks are deliberately **not fanned out** into per-catalog-entry JSONs (that would pre-approve
datasets the gate has not cleared). They expand into concrete per-dataset tasks once `triage-011` /
`catalog-017` produce an approved shortlist and a partner is confirmed.

---

## Generated task index

All milestone/backlog rows now have a committed, schema-valid `tasks/<id>.json` (validated against
`packages/schema` taskSchema; filenames match ids; no duplicates). Index:

- M0: `cancer-data-dictionaries-model-001` (seed), `-gate-002`, `-ontology-policy-003`, `-reviewer-004`,
  `-emit-005`, `-validator-006`, `-outreach-007`, `-pilot-008`
- M1: `-snapshot-009`, `-crosswalk-010`, `-triage-011`, `-dict-012`, `-dict-013`, `-partner-014`
- M2: `-score-015`, `-glossary-016`, `-catalog-017`, `-dict-018`
- M3: `-patient-glossary-019`, `-reuse-020`, `-refresh-021`, `-dict-022`
- Backlog: `-linkml-023`, `-i18n-024`, `-dash-025`, `-lookup-026`

All tasks: `lane: donated`, `urgent: false`, `verifiedNeed: false`, `requestor: TO BE SECURED`,
`status: open`. `outputLicense`: `MIT` for code, `CC-BY-4.0` for docs/translations.
`patient-glossary-019` carries `riskTier: high` with the oncologist + patient-advocate hard-gate and
"not medical advice" framing preserved.

---

## Example task JSON

```json
{
  "id": "cancer-data-dictionaries-model-001",
  "title": "Dictionary template + canonical dictionary model",
  "project": "cancer-data-dictionaries",
  "type": "writing",
  "lane": "donated",
  "priority": "high",
  "domain": ["cancer-research", "open-data", "bioinformatics", "open-science"],
  "riskTier": "low",
  "urgent": false,
  "deliverable": "document",
  "tokenEstimate": "small",
  "status": "open",
  "context": "Public cancer datasets (e.g. GDC/TCGA open tier, GEO, SEER aggregate) are widely reused but poorly documented: cryptic field names, coded values whose meanings live in separate manuals, implied units, and silent 'not reported' sentinels. Before documenting any specific dataset, the project needs one reusable template and a canonical dictionary model that every output (Markdown, JSON, Frictionless Table Schema, Croissant) is a projection of. The deliverable is documentation about the data, never the data itself; only open-access/aggregate/de-identified datasets are ever in scope, and every assertion must cite an authoritative source.",
  "objective": "Create the reusable dictionary template and the canonical dictionary model that every per-dataset task will use, with provenance and sentinel handling as first-class, mandatory attributes.",
  "acceptanceCriteria": [
    "Canonical model documents all fields: datasetId, title, publisher, accessTier (open|controlled|mixed; only open proceeds), sourceUrls, license {id, url, permitsDerivatives, snapshotRef}, provenance {retrievedAt, version, updateCadence, attribution, docSources[]}, pii {present, deidentified, basis, notes}, tables[].fields[] {name, label, dataType, units, codeSystem, allowedValues[]{code, meaning, sourceRef}, nullable, sentinels[], description, caveats, provenance{sourceRef}}, crosswalks[], qualityScore {before, after}, specVersions.",
    "Every field-level assertion (definition, coded-value meaning, unit) requires a provenance.sourceRef; the model treats unsourced assertions as invalid.",
    "Sentinel / 'not reported' conventions are a first-class field attribute (sentinels[]).",
    "Markdown template covers the field dictionary, coded-value tables, provenance record, license record, and known caveats; explicitly states the deliverable is documentation, not data, and that documentation output is licensed CC-BY-4.0.",
    "At least one filled-in worked-example skeleton is included to guide contributors.",
    "pnpm build && pnpm test && pnpm lint pass for any committed tooling; commit is DCO signed-off."
  ],
  "resources": [
    "C:\\code\\elyos\\planning\\projects\\cancer-data-dictionaries\\PLAN.md",
    "C:\\code\\elyos\\planning\\ROADMAP.md",
    "NCI Genomic Data Commons (GDC) Data Dictionary",
    "Frictionless Table Schema specification",
    "Croissant ML metadata specification (MLCommons)"
  ],
  "output": "A dictionary template plus a canonical dictionary model definition, committed to the project repo and ready for reuse by per-dataset dictionary tasks.",
  "requestor": "TO BE SECURED",
  "verifiedNeed": false,
  "outputLicense": "CC-BY-4.0"
}
```
