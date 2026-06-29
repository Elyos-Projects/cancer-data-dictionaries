# PLAN — cancer-data-dictionaries

> Status: Draft · Version: 0.2.0 · Last updated: 2026-06-29 · Owner: TBD (maintainer) · Lane: donated

## Executive summary

Public cancer datasets are a foundational resource for research, yet they are routinely shipped
with documentation that is incomplete, cryptic, or scattered across PDFs, wikis, and coding
manuals. A researcher opening a Genomic Data Commons clinical export, a Gene Expression Omnibus
series matrix, or a SEER aggregate table is confronted with field names like
`ajcc_pathologic_stage`, `vital_status`, `cgc_case_days_to_death`, or `behavior_recode`, encoded
values whose meaning lives only in a separate WHO/NCI coding manual, units that are implied rather
than stated, and "not reported" sentinels that silently corrupt analyses. The data is technically
**open**, but practically **illegible** — and illegible data produces irreproducible science and
slows work that real patients are waiting on.

This project produces **plain, machine-readable data dictionaries** for confusing *public* cancer
datasets: for each field, a human-readable label and description, data type and units, the full
coded value set with each code's meaning and its authoritative source, nullability and sentinel
conventions, caveats, and a provenance citation for **every** assertion. Dictionaries are emitted
in both human-readable (Markdown) and machine-readable (JSON, Frictionless Table Schema, Croissant
ML metadata) form, plus controlled-vocabulary crosswalks (e.g. ICD-O-3, NCI Thesaurus, HGVS, OMOP
CDM) where licensing permits, and small validators that let any reuser re-check a dataset against
its documented schema. Where an authoritative machine-readable mapping already exists (notably the
GDC Data Dictionary, which ships every property pre-linked to a caDSR CDE Public ID and NCI
Thesaurus concept code as open YAML), our crosswalk work is **harvest-and-cite**, not re-derivation;
we claim original credit only for mappings we derive and for **harmonization across datasets**.

The single highest-value output is a **harmonized cross-dataset field-equivalence table** — the same
clinical concept mapped across GDC, cBioPortal, SEER, and GEO to shared vocabularies (NCIt / caDSR
CDE / OMOP) — shipped as a **named deliverable**, not a byproduct. No incumbent provides it, and it
is the direct enabler of reproducible secondary analysis across sources.

The **deliverable is documentation about the data, never the data itself.** We never mirror,
clone, transform, re-host, or republish cancer data. Two hard, project-defining constraints govern
every task: (1) the **cancer data guardrail** — we work **only** with open-access / aggregate /
de-identified data; controlled-access sources (dbGaP, EGA, individual-level biobanks) and any
identifiable patient data are categorically **out of scope**; and (2) the **provenance guarantee**
— no field definition, value meaning, or unit is asserted without a cited authoritative source.

Risk tier is **low** for the core researcher-facing dictionaries (describing a published schema is
not medical advice and touches no patient). It escalates to **high** only for the optional,
explicitly gated **patient-facing educational glossary** in M3, which carries a "not medical
advice" notice and requires **oncologist and patient-advocate sign-off** before release. The plan
front-loads the access-tier / license / PII gate and the provenance discipline so the riskiest
failure modes — touching controlled data, mis-stating a clinical field, or laundering a
non-redistributable source as open — are blocked structurally, not by good intentions.

## Problem & beneficiaries

**Who is helped.**
- **Cancer researchers, bioinformaticians, and data scientists** doing secondary analysis of
  public data, who currently lose hours-to-days decoding fields and frequently misinterpret them.
- **Students and early-career scientists** learning to work with real oncology data without an
  institutional mentor to decode the conventions.
- **Reproducibility and meta-research** — clear, machine-readable dictionaries make published
  re-analyses auditable and re-runnable.
- **Patient advocates and advocacy-org analysts** who want to read public cancer data responsibly
  (served by the *researcher* layer directly, and by the optional M3 plain-language layer).
- **The original data-publishing bodies** (NCI/GDC, NCBI/GEO, EBI, Broad/DepMap, registries),
  whose data becomes more correctly and more attributably reused.

**The verified need.** That cancer data documentation is poor and that this blocks reuse is well
established in the bioinformatics literature and FAIR-data community. We treat the **general** need
as real. The **per-dataset, per-partner need is TO BE SECURED**: we have not yet confirmed a named
data commons, repository, or advocacy partner who has agreed to *accept and surface* the
dictionaries we produce. Until a specific maintainer confirms they will review and adopt
contributions, individual tasks carry `verifiedNeed: false`. This honesty is load-bearing: the
Elyos bar is "delivered, not merged" — a dictionary nobody adopts is not a delivered outcome.

**Partner org.** TO BE SECURED. Candidate channels: the **GDC/Cancer Research Data Commons** docs
team, **cBioPortal**, **Sage Bionetworks / Synapse**, **EBI** resources (Expression Atlas, PRIDE),
**Bioconductor / Galaxy** training communities, and patient-advocacy data groups for the M3 layer.
M0 includes explicit partner-outreach work; no partner is assumed.

## Goals and non-goals

**Goals**
- Produce a reusable, standards-aligned **dictionary template + canonical dictionary model** that
  all outputs (Markdown, JSON, Frictionless Table Schema, Croissant) are projections of.
- For each in-scope **open-access** cancer dataset, deliver a complete, source-verified data
  dictionary — every field, type, unit, coded-value meaning, sentinel/null convention, and caveat —
  with a provenance citation for every assertion.
- Make the **access-tier / license / PII gate** a non-skippable, auditable, blocking step, with the
  cancer guardrail (open-access only) as its first and hardest check.
- Provide controlled-vocabulary **crosswalks** (ICD-O-3, NCI Thesaurus, HGVS, **OMOP CDM**, and
  similar) where the vocabulary's own license permits redistribution of the mapping. Where an
  official machine-readable mapping already exists (e.g. GDC's caDSR CDE Public IDs + NCIt codes in
  open YAML), **reproduce-and-cite** it rather than re-derive it; mark each mapping's origin
  (`official` vs `derived`) so domain-review effort and credit focus on derived/harmonized mappings.
- Ship a **harmonized cross-dataset field-equivalence table** as a first-class, citable deliverable
  (canonical JSON + Markdown): the same concept mapped across GDC / cBioPortal / SEER / GEO to shared
  vocabularies (NCIt / caDSR CDE / OMOP). This is the project's primary differentiator.
- Provide small, dependency-light **validators** that re-check an open dataset against its
  documented schema and that verify the dictionary itself (schema-valid, every assertion sourced).
- Contribute dictionaries **back** to the dataset's commons/repository/portal in its native form.
- Optionally (M3, gated) produce an **oncologist- and advocate-reviewed plain-language glossary**
  for non-specialists, clearly marked "not medical advice."

**Non-goals**
- We do **not** host, mirror, clone, transform, clean, re-derive, or republish any cancer data.
- We do **not** touch **controlled-access** data (dbGaP, EGA, TCGA controlled tier, ICGC/TARGET
  controlled, individual-level biobanks) or **any identifiable patient data** — categorically out.
- We do **not** document datasets with non-redistributable, non-commercial, or unclear licenses by
  default — they are **flagged** and routed to policy review, never best-guessed (COSMIC, OncoKB,
  GENIE, IARC/GLOBOCAN are pre-flagged examples).
- We do **not** give medical, diagnostic, prognostic, or treatment advice. The researcher
  dictionaries describe *the dataset's schema*; the optional patient glossary is *education only*.
- We do **not** re-identify, link, or attempt to deanonymize any individual in any dataset.
- We do **not** auto-publish; a human contributes upstream after review.
- We do **not** invent definitions — anything not traceable to an authoritative source is omitted
  and flagged as a documentation gap, not filled by the model's prior.

## Success metrics (outcomes)

Outcome-based and beneficiary-centric. Vanity metrics ("dictionaries written") are excluded; what
counts is *adopted, correct, reused* documentation.

| Metric | Baseline | Target (first 6 months) |
| --- | --- | --- |
| Dictionaries **adopted upstream** (merged into a commons/repo, or maintainer-confirmed) | 0 | ≥ 6 adopted |
| Datasets passing the access-tier+license+PII gate with a recorded, verifiable disposition | n/a | 100% of *triaged* datasets have a committed gate artifact |
| **Provenance coverage** — share of dictionary assertions (field defs + coded values) with a cited source | n/a | **100%** on every delivered dictionary (hard gate, not a target) |
| **Field-coverage completeness** — documented fields / total fields in the dataset's open tier | n/a | ≥ 95% per delivered dictionary; gaps explicitly listed |
| Reuse signal — a dictionary cited/forked/referenced by a third party (externally verifiable) | 0 | ≥ 3 verifiable reuse events |
| Confirmed adoption partners (commons/repos/advocacy orgs) | 0 | ≥ 2 secured |
| Domain-review defects (incorrect field meaning / coded-value error) found in review | n/a | < 1 substantive error per 10 delivered; **0** controlled-data / license errors |
| Patient-glossary items (M3) released **without** oncologist+advocate sign-off | n/a | **0** (hard gate) |

**Quantifying "legibility improvement" (so DoDs are verifiable).** Each dictionary is scored 0–100
on a **dictionary-quality score**: field coverage (% of open-tier fields documented), value-set
coverage (% of coded fields with a complete, sourced value list), provenance completeness (must be
100% to ship), unit/sentinel completeness, and machine-readable validity (valid JSON + Frictionless
+ Croissant). A recorded **before-score** (the dataset as published) and **after-score** are stored
in the dictionary's gate/provenance artifact; target after-score **≥ 90/100** with provenance at
100%. To stay **externally reproducible**, the before/after scores are computed **mechanically** —
the count of fields with a complete, sourced definition + coded-value table — rather than from a
subjective judgement of the published dataset; any non-mechanical "legibility" impression is
narrative context only, not part of the score.

**What "adopted upstream" means, per channel** (the Steward records one canonical
`outcomes/<dataset-id>.json` per dictionary with channel, permalink/DOI/commit, timestamp, and
before/after scores):
- **Data commons / portal (GDC, cBioPortal, Synapse, EBI):** the dictionary is live on the
  dataset's page or a maintainer confirms acceptance in writing — evidence = permalink/message ref.
- **GitHub / Hugging Face repo:** a merged PR — evidence = merge-commit URL.
- **Zenodo / Figshare:** a citable record/version DOI — evidence = the DOI.
"Submitted but unconfirmed" and self-reported reuse never count as adopted.

## Scope

**In scope**
- Data-dictionary artifacts for **open-access** cancer datasets: per-field name, plain label,
  description, data type, units, allowed/coded value set with each value's meaning + source,
  nullability + sentinel conventions ("not reported", `-Inf`, `9999`), caveats, and per-assertion
  provenance.
- Machine-readable emission: canonical JSON dictionary, **Frictionless Table Schema**, **Croissant
  ML** metadata (JSON-LD).
- Controlled-vocabulary crosswalks where the vocabulary license permits (ICD-O-3, NCI Thesaurus
  (NCIt), caDSR CDE, **OMOP CDM**, HGVS, MONDO; LOINC with its open license recorded), each marked
  `official` (reproduced + cited) or `derived` (ours, domain-reviewed). SNOMED CT mappings only where
  a valid affiliate/member license covers redistribution — otherwise excluded.
- A **harmonized cross-dataset field-equivalence table** (named deliverable): the same concept mapped
  across GDC / cBioPortal / SEER / GEO to a shared vocabulary anchor (NCIt / caDSR CDE / OMOP), with
  per-member provenance and caveats.
- Small validators: (a) re-check an open dataset's bounded sample against the documented schema;
  (b) lint the dictionary (schema-valid, every assertion has a `provenance` ref, no orphan codes).
- Access-tier / license / PII triage and recording per candidate dataset.
- Optional, **gated** plain-language layer: researcher glossary (M2, low–medium) and patient-facing
  educational glossary (M3, **high**, oncologist + advocate reviewed, "not medical advice").

**Candidate dataset backlog.** A license- and access-tier-classified catalog of real public cancer
datasets is maintained in `DATASET-CATALOG.md` (seeded inline in this PLAN's compliance section and
in `TASKS.md`). It is a **candidate backlog only**: each entry's access-tier / license /
PII columns are first-pass triage signals; no entry becomes a task until it passes the per-dataset
gate (`gate-002`). The catalog biases toward clearly open sources (NIH/NCI public domain, CC0,
CC-BY) and honestly flags non-commercial/custom (COSMIC, OncoKB, GENIE, IARC) and excludes
controlled-access sources outright.

**Out of scope**
- The data itself (no hosting / mirroring / cloning / transforming / cleaning / republishing).
- **All controlled-access data** and **all identifiable patient data** — no exceptions, no IRB
  workaround attempted; such requests are refused and flagged.
- Datasets with non-redistributable / unclear licenses (flagged, not documented, unless policy
  review clears them).
- Analysis, interpretation, ranking, modeling, or clinical conclusions about the data.
- Medical / diagnostic / prognostic / treatment advice of any kind.
- Re-identification, record linkage, or deanonymization.
- Automated, unattended publishing.

## Competitive landscape & differentiation

**Incumbents are either authoritative-but-siloed/technical, or content-free format layers.** No one
ships a plain-language, cross-dataset, machine-readable, provenance-checked, harmonized field
dictionary. That seam is the opportunity.

**Authoritative dictionaries (the incumbents):**
- **GDC Data Dictionary (NCI).** Per-property definitions in open YAML/JSON-schema on GitHub
  (`NCI-GDC/gdcdictionary`), each property pre-linked to a caDSR CDE Public ID + NCIt concept code, a
  Dictionary Viewer UI. *Strengths:* canonical, already machine-readable, already CDE/NCIt-linked.
  *Weaknesses:* GDC-only; terse/technical; no plain-language layer; no cross-dataset harmonization;
  coded-value meanings often defer to separate manuals. (We **reproduce-and-cite** its mappings, not
  reinvent them.)
- **NCI caDSR / CDE Browser.** ISO/IEC 11179 registry of standardized Common Data Elements (the
  source of GDC's CDE links). *Strengths:* authoritative, standards-compliant. *Weaknesses:*
  heavyweight, registry-oriented, steep UX; not "what does column X in this file mean."
- **NCI Thesaurus (NCIt) / EVS.** ~100k open biomedical concepts; ideal crosswalk target.
  *Weaknesses:* an ontology, not a dataset dictionary.
- **SEER coding/recode manuals.** Definitive for registry variables. *Weaknesses:* PDF-first,
  scattered across many manuals, not machine-readable.
- **cBioPortal data-format docs.** Practical for loaders, but explicitly *no fixed ontology* —
  clinical attributes are free-form per study, so per-study column meaning is undocumented
  centrally. Exactly the legibility gap this project targets.

**Harmonization / format infrastructure (adjacent):**
- **OHDSI OMOP vocabularies + ATHENA.** The dominant free cross-source harmonization vocabulary
  (10M+ concepts, 136 vocabularies). *Weaknesses:* requires expensive ETL *into* the CDM; not a
  per-published-dataset dictionary — oncology column semantics still need a bridge we can provide.
- **NCI Center for Cancer Data Harmonization (CCDH / cancerdhc).** Directly adjacent prior art and a
  potential partner/adoption channel — worth tracking.
- **Frictionless Table Schema / Croissant ML.** *Formats*, not content: no cancer definitions, no
  per-value semantics or provenance. We use them as emit targets (Croissant also gives free
  discovery via Google Dataset Search / Hugging Face indexing).
- **OpenML / data.world dictionaries.** Generic, crowd-edited; no domain authority or provenance
  discipline.

**Differentiators we win on:**
- **Harmonization is the moat (strongest single differentiator).** The cross-dataset
  field-equivalence table mapped to shared vocabularies is something *no* incumbent provides and the
  direct enabler of reproducible secondary analysis across GDC/cBioPortal/SEER/GEO.
- **Plain-language layer over authoritative, terse, PDF-scattered definitions** — the daily pain
  point, especially for cBioPortal's free-form per-study attributes.
- **One machine-readable artifact unifying meaning + value semantics + provenance** (today spread
  across YAML schemas, Excel CDEs, OWL ontologies, and PDF manuals).
- **Provenance-as-hard-gate + open licensing (CC-BY docs / MIT code)** — auditable, redistributable,
  more rigorous than community dictionaries and more reusable than registry exports.
- **Cancer-specific guardrails as a feature** — the access-tier/PII/license gate makes outputs *safe
  to adopt* by institutional data commons.
- **Native upstream contribution** — emitting in the source's own format (GDC YAML, Frictionless)
  means the dictionary can be merged back, not just published alongside.

**Relationship to the Elyos sibling `cancer-dataset-datasheets`:** datasheets answer "what *is* this
dataset, who made it, what are its biases/consent terms" (the Gebru et al. cover page); dictionaries
answer "what does *this column / this code* mean" (the field-by-field index). Non-overlapping lanes;
the two should cross-link (datasheet → dictionary per dataset).

## Solution approach & architecture

This is a **content/data-documentation project with light software** (template + canonical model +
emitters + validators + crosswalk tooling). It is **not** a data pipeline that moves data.

**Pipeline (per dataset)**
1. **Access-tier + license + PII gate** *(cancer guardrail first)* — classify the dataset's access
   tier (OPEN / CONTROLLED / MIXED), confirm it is open-access / aggregate / de-identified, verify
   the license permits derivative documentation, and run PII triage. PASS only if all three clear;
   otherwise FLAG (license/policy) or EXCLUDE (controlled / identifiable). Record the decision as a
   committed artifact.
2. **Provenance capture** — source URL(s), publisher, version/release, retrieval date, the
   authoritative documentation sources (data dictionary, coding manual, schema spec) with stable
   references, license URL + a captured license-text snapshot, attribution string.
3. **Field dictionary authoring** — for every field: name, plain label, description, data type,
   units, allowed/coded value set + each value's meaning, nullability + sentinel conventions,
   caveats. **Every assertion carries a `provenance` ref** to the source doc + section. Field
   meanings are read from *published documentation*, not inferred — gaps are flagged, never guessed.
   **Definition prose is paraphrased-with-citation, not copied:** source documentation (WHO ICD-O-3,
   SEER coding manuals, NCIt) is itself potentially copyrighted text *independent of the dataset's
   open status*, so we restate meanings in our own words with a `sourceRef` and reserve verbatim
   text to short, clearly-attributed snippets (enforced by the verbatim-copy lint, §Compliance).
4. **Controlled-vocabulary crosswalk** — map coded fields to standard ontologies (NCIt, caDSR CDE,
   **OMOP CDM**, ICD-O-3, HGVS, MONDO, LOINC) *where the ontology license permits redistribution*;
   otherwise link out rather than republish. **Reproduce official mappings rather than re-derive
   them:** for GDC, every property already carries a caDSR CDE Public ID + NCIt concept code in open
   YAML, so this step is largely *harvest-and-cite*. Each mapping records `mappingOrigin`
   (`official` = reproduced from an authoritative source, cite-only; `derived` = produced by us,
   requires domain review). OMOP `concept_id`/domain mappings (via ATHENA) anchor cross-dataset
   harmonization.
5. **Machine-readable emission** — canonical JSON → projections: Frictionless Table Schema +
   Croissant ML (JSON-LD), and, for sources with a native dictionary format (e.g. GDC YAML), that
   native form to enable upstream contribution. **Projections are lossy:** per-coded-value meaning
   and per-assertion provenance are fully expressible only in the **canonical JSON** — Frictionless
   carries field-level `enum`/`description` but no per-value source ref, and Croissant is oriented to
   ML-ready loading with coarse field semantics. A **round-trip/coverage test** asserts that
   value-level provenance either survives a projection or is documented as intentionally absent.
6. **Validation** — lint the dictionary (provenance completeness, code/value integrity); optionally
   re-check the dataset's **bounded open sample** against the documented schema, under the access
   protocol below.
7. **Plain-language layer (optional, gated)** — researcher glossary (low–medium); patient glossary
   (high, oncologist + advocate reviewed, "not medical advice").
8. **Review & contribute** — gate reviewer + technical reviewer (+ domain reviewer; + oncologist &
   advocate for patient layer), then a human contributes upstream.

**Canonical dictionary model** (single source of truth; every output is a projection):
`datasetId`, `title`, `publisher`, `accessTier` (`open`|`controlled`|`mixed`; only `open` proceeds),
`sourceUrls[]`, `license {id, url, permitsDerivatives:boolean, snapshotRef}`,
`provenance {retrievedAt, version, updateCadence, attribution, docSources[]{title,url,sectionRef}}`,
`pii {present:boolean, deidentified:boolean, basis, notes}`,
`tables[] { name, description, fields[] { name, label, dataType, units, codeSystem,
allowedValues[]{code, meaning, sourceRef}, nullable, sentinels[], description, caveats,
provenance{sourceRef} } }`, `crosswalks[]{field, targetVocabulary, license, mappingOrigin
(official|derived), sourceRef, mapping[]}`, `qualityScore {before, after}`,
`specVersions {frictionless, croissant, ncit, icdo, omop, gdcDictionary}`.

A sibling **harmonized field-equivalence** artifact (`equivalences[]{ concept, vocabularyAnchor
(NCIt/CDE/OMOP), members[]{datasetId, field, sourceRef}, caveats }`) is a named deliverable that
references the per-dataset dictionaries; it is produced and reviewed as its own work item, not a
silent byproduct.

**Tech stack.** TypeScript, ESM, pnpm workspaces (Elyos convention). Emitters, validators, and
crosswalk tooling are small Node packages with minimal dependencies. Dictionaries authored in
Markdown + JSON/JSON-LD. No runtime services; everything runs locally or in CI.

**Pinned target spec versions** (recorded in `specVersions`, bumped only by a deliberate task):
- **Frictionless Table Schema** — v1 / v2 (current published spec at adoption; pinned per dataset).
- **Croissant ML** — v1.0 (MLCommons), validated against the v1.0 JSON-LD context/SHACL.
- **Ontology/codelist versions** — ICD-O-3.2, NCI Thesaurus (**monthly** release id pinned, e.g.
  26.05d, since NCIt updates monthly), caDSR CDE versions, **OMOP CDM vocabulary release (via
  ATHENA)**, HGVS recommended nomenclature version, MONDO release — each recorded per crosswalk so a
  mapping is reproducible.
- **Source dictionary version** — the upstream dictionary release we documented (e.g. GDC Data
  Dictionary version, SEER recode release) is pinned so drift detection can diff against it.

**Dataset-inspection access protocol** (makes "describe, never store" enforceable — and is the
*only* point we touch data; it applies **only to open-access data** that has already PASSed the gate):
- **Open-access only.** Inspection of controlled-access data is forbidden; if a dataset turns out to
  be controlled/mixed, inspection halts and it is EXCLUDED.
- **Header/schema/streamed reads only.** Prefer published schema/dictionary endpoints (GDC API
  field metadata, GEO series-matrix headers, Frictionless descriptors); for type/value inference,
  stream and sample, never download a full extract.
- **Row cap.** At most a bounded sample (default **1,000 rows or ~5 MB**, whichever is smaller) —
  enough to confirm types, units, value sets, and sentinels.
- **Local-only and ephemeral.** Any bytes read live only in the contributor's local scratch for the
  session and are deleted after; never written to the repo, CI artifacts, receipts, or logs.
- **No committed samples / no patient rows.** Examples use synthetic or trivially-public
  illustrative rows, or cite a value by reference — never paste real cancer-data rows into commits.
- **Stop on identifiability signal.** Any sign of individual-level / identifiable content halts
  inspection, discards the sample, and routes the dataset to EXCLUDE/FLAG.

**Key decisions.**
- Canonical-model-first, so we never hand-maintain parallel formats.
- The gate is a *blocking* checklist artifact committed with the work, not an informal judgement;
  the **access-tier check is its first gate** and an EXCLUDE there ends the task.
- Crosswalks respect ontology licensing — we link rather than republish where a license forbids it.
- Provenance is a *hard* emit-time constraint: the validator fails the build if any assertion lacks
  a `sourceRef`.

**AI assistance (Claude) — where it drafts, and the hard boundaries.** This is a donated-lane
project; a human runs their own agent interactively. Claude is high-leverage for *drafting and
proposing*, never for deciding:
- **Draft plain-language field definitions from official docs, with citations + gap-flagging.** Feed
  the GDC property entry / SEER manual section / GEO header; Claude produces a paraphrased label +
  description + a candidate `sourceRef`, and explicitly flags anything it cannot source. (Directly
  attacks the legibility gap; paraphrase, not verbatim.)
- **Propose field→vocabulary mappings (NCIt / caDSR CDE / OMOP / ICD-O-3) as *candidates*.** For
  GDC, reconcile against the *existing* official CDE/NCIt links (verification, not invention); for
  cBioPortal's free-form per-study attributes, propose candidate concept codes for human
  confirmation.
- **Harmonize equivalent fields across datasets** — cluster `vital_status` / `OS_STATUS` / `Vital
  status recode`, draft the equivalence table + caveats (the differentiator), subject to domain
  review.
- (Supporting) **Extract coded-value sets and sentinels** from manuals into the canonical model, and
  **lint prose for verbatim-copy risk.**

Hard boundaries (match the no-invented-definitions rule): **no fabricated field/value meanings**
(unsourced = omitted + flagged; the *validator*, not the model, enforces 100% provenance);
**license & access-tier and PII/identifiability calls are human-verified** (Claude may summarize a
clause, but `permitsDerivatives:true` and OPEN/CONTROLLED determinations are the reviewer's, with
cited evidence); **crosswalk mappings are candidates, not commits** (domain reviewer signs off;
restricted-vocabulary term text is never emitted by the model); **no patient-facing medical phrasing**
outside the gated M3 channel.

## Data, licensing & compliance

**THIS IS THE CRITICAL SECTION. The cancer guardrails below are binding and override convenience.**

### Cancer data guardrails (binding, gate-enforced)

1. **Open-access / aggregate / de-identified data ONLY.** A dataset is eligible only if its access
   tier is genuinely open (publicly downloadable without authorization), or it is aggregate
   statistics, or it is publisher-de-identified at the individual level.
2. **Controlled-access is categorically OUT OF SCOPE.** dbGaP, EGA, TCGA/ICGC/TARGET **controlled
   tiers** (germline, raw reads/BAM/VCF), individual-level biobanks (e.g. UK Biobank record-level),
   and any source requiring a Data Use Agreement / DAC approval / IRB are excluded — we neither
   access nor document them from data. (We may describe a *publicly published schema spec* that
   happens to also cover controlled fields — e.g. the public GDC Data Dictionary — but **only from
   the public spec, and we never access controlled records.**)
3. **No identifiable patient data, ever.** Direct identifiers or re-identifiable individual-level
   records are an immediate EXCLUDE; we never attempt linkage or re-identification.
4. **Per-source license verification is mandatory.** Examples encoded into the gate's default rules:
   - **GDC / TCGA open tier, GEO** — open (NIH/NCI/NCBI public-domain-style terms): **eligible**,
     attribution + provenance recorded.
   - **SEER** — **aggregate** statistics (e.g. SEER\*Explorer) are eligible; **case-level SEER
     research data requires a signed DUA → EXCLUDE** (controlled).
   - **ClinVar, Expression Atlas, PRIDE, CPTAC open tier, DepMap (CC BY 4.0)** — eligible; record
     exact license + attribution.
   - **TCIA** — per-collection; many collections are CC BY but some are restricted → **verify per
     collection**.
   - **COSMIC, OncoKB** — non-commercial / custom terms → **FLAG** to policy review; not documented
     by default.
   - **AACR Project GENIE, IARC/GLOBOCAN (Cancer Today)** — registered-access / non-commercial /
     custom terms → **FLAG** to policy review.
5. **Provenance on every assertion.** Every field definition, coded-value meaning, and unit cites an
   authoritative source (the dataset's own docs, GDC Data Dictionary, SEER coding manuals, WHO
   ICD-O-3, NCIt, HGVS). Unsourced statements are not shippable.

### Objective "permits derivatives" criterion

A dataset PASSes the license check only if its license is on the accepted list (or cleared by the
NC/custom-source policy) **and** an explicit `license.permitsDerivatives: true` is recorded with a
cited clause/URL evidencing that derivative documentation/metadata is allowed. Missing evidence, an
unparseable license, or a `permitsDerivatives` that cannot be set `true` from the source =
FLAG/EXCLUDE, never default-allow.

### Ontology / codelist licensing (a real, easy-to-miss trap)

Crosswalks can inadvertently *republish licensed vocabulary content*. The
ontology-licensing policy (`ontology-policy-003`) fixes per-vocabulary rules before any crosswalk
runs: **redistribute mappings** for open vocabularies (NCIt, caDSR CDE, **OMOP CDM concepts via
ATHENA**, HGVS, MONDO, LOINC under its license with attribution); for **restricted** vocabularies
(notably **SNOMED CT**, and any non-commercial ICD-O derivative concerns), **link out by code rather
than republishing the term text**, unless a valid license covers redistribution. The chosen
vocabulary version is pinned per crosswalk. Note that **OMOP/ATHENA aggregates restricted
vocabularies** (SNOMED among them), so OMOP crosswalks redistribute the `concept_id`/domain mapping
and the *open* concept names only, never restricted term text — same link-out rule applies.

### Source-documentation copyright (distinct from dataset license)

A dataset can be fully open while its **authoritative documentation prose is copyrighted** — a field
definition lifted verbatim from a WHO ICD-O-3 manual or a SEER coding manual is potentially
protected text *independent of the dataset's open status*. The "read from published documentation,
not inferred" rule (pipeline 3) must therefore not become verbatim copying. Policy: **paraphrase
definition prose in our own words with a `sourceRef`**, and reserve verbatim text to short, clearly
attributed snippets. A **verbatim-copy lint** runs in CI, capping quoted-run length against source
manuals and requiring attribution; long unattributed runs fail the build. This is checked separately
from the dataset-license and vocabulary-term-text checks above.

### Provenance model

Every dictionary records: source URL(s), publisher, retrieval timestamp, dataset version/release,
license id + URL + a **captured snapshot** of the license text as it stood at retrieval (committed
copy + SHA-256 hash + Wayback save URL), the authoritative documentation sources used (with stable
section references), update cadence, and the required attribution string. Provenance is part of the
committed deliverable, and the validator blocks any assertion lacking a `sourceRef`.

### Privacy / PII stance

PII triage is mandatory and runs *before* documentation, within the inspection access protocol:
- **Access-tier check first** — controlled/mixed → EXCLUDE.
- **Direct-identifier scan** — any field that is or could carry direct identifiers (name, MRN,
  dates of birth/death at full precision tied to an individual, full-resolution geocodes, specimen
  barcodes that resolve to a person) → EXCLUDE unless the publisher documents de-identification.
- **Quasi-identifier / re-identification flag** — for individual-level (even if "de-identified")
  data, flag small-cell / linkage risk (k < 5 equivalence classes; rare-cancer + geography +
  demographics combinations are especially sensitive) → EXCLUDE/FLAG.
- **Genomic re-identification awareness** — individual-level germline/genotype data is treated as
  inherently identifiable and is out of scope regardless of stated de-identification.
We never de-identify data ourselves (that is transforming the data — out of scope). The methodology
output (which checks ran, what fired) is recorded in the committed gate artifact.

### Attribution & output licensing

All dictionaries attribute the original publisher per the source license, link to the source, and
state clearly that the **documentation — not the data — is our contribution**. Documentation /
metadata output is licensed **CC-BY-4.0**; emitter / validator / crosswalk **code is MIT**. Where a
source requires non-commercial use of *its own* materials, the dictionary still ships CC-BY (it is
our original description), but the constraint and any quoted source text are recorded.

## Quality, review & risk gates

**Risk tier: low** for the core researcher-facing dictionaries — describing a published, open
dataset's schema is documentation, touches no patient, and gives no advice. **medium** where domain
accuracy is load-bearing (clinical-field meanings, coded-value interpretation, ambiguous license).
**high** for the optional **patient-facing educational glossary** (M3).

**Required review before a deed is "done":**
- **Access-tier + License + PII reviewer** *(mandatory, every dataset; the hard gate)* — confirms
  the dataset is open-access / aggregate / de-identified, the license permits reuse + derivatives,
  and no identifiable data is present. **No deed ships without this sign-off.** Must be filled
  before the M0 pilot is reviewed (blocking prerequisite, not a parallel hire).
- **Technical reviewer** — confirms the JSON dictionary, Frictionless Schema, and Croissant
  metadata are valid, the validator runs clean (CI green), and provenance completeness = 100%.
- **Domain reviewer** (cancer-informatics / bioinformatician) — confirms field meanings and
  coded-value interpretations are correct (medium-risk tasks).
- **Oncologist + patient-advocate reviewers** *(M3 patient glossary only, high risk)* — credentialed
  sign-off required before any patient-facing item is released; output carries a "not medical
  advice" notice. No patient-facing item ships without both.

**Test fixtures & golden files (so "CI green" means something).** Each tool ships committed test
assets exercised in CI using only synthetic/public fixtures (never real inspected cancer data):
- **Dictionary validator** — golden valid dictionaries that must pass and malformed ones that must
  fail (missing `sourceRef`, orphan code, unknown `accessTier`), including a fixture that asserts a
  `controlled` access tier is rejected.
- **Emitters** — golden canonical-JSON → expected Frictionless / Croissant output pairs diffed in
  CI, with output validated against the pinned spec.
- **Crosswalk tooling** — fixtures asserting restricted-vocabulary term text is **not** emitted
  (link-out only), proving the ontology-licensing policy is enforced in code.

**Definition of Shipped.** A complete, source-verified data dictionary **adopted upstream** (live on
the commons/repo, merged PR, or DOI — per the per-channel definitions), with: access-tier+license+PII
gate PASSed and artifact committed, **provenance completeness = 100%**, field coverage ≥ 95% (gaps
listed), quality score ≥ 90/100, machine-readable outputs valid against pinned specs, and the
Steward's `outcomes/<dataset-id>.json` acceptance artifact recorded. For patient-facing items,
oncologist + advocate sign-off is an additional hard precondition. Producing the dictionary is *not*
shipped; recorded upstream adoption is.

## Roadmap & milestones

**M0 — Foundation & cold-start (thin)**
- Goal: build the reusable toolkit, stand up the gate, and prove the end-to-end flow on **one**
  open-access dataset; name the gate reviewer; begin partner outreach.
- **Cold-start de-risking.** To avoid producing a dictionary nobody adopts, the pilot is gated on a
  realistic adoption path *before* authoring, in priority order: (a) an **informal channel** — a
  maintainer who agreed to look at a contribution; failing that, (b) a **self-serve fallback** that
  does not depend on a third party — a **GitHub PR to the dataset's own repo** or a **Zenodo
  record/DOI** we publish ourselves. The pilot must use one so M0 yields a real *adopted* outcome.
- Pilot candidate (lowest-risk **and** richest field/value structure): **GDC open-tier clinical**
  fields or a **GEO series** dictionary — both exercise the per-field + coded-value + provenance
  model well, and GDC additionally lets us demonstrate harvest-and-cite of official caDSR-CDE/NCIt
  mappings. A **SEER\*Explorer aggregate** table is deferred to later milestones: aggregate tables
  lack the column/coded-value richness to exercise the dictionary model and would weakly test it.
- Exit criteria: (1) dictionary template + canonical model published; (2) access-tier+license+PII
  gate checklist (incl. ontology-licensing policy) exists and is applied to one dataset; (3)
  JSON/Frictionless/Croissant emitters + dictionary validator green in CI with golden fixtures
  (incl. the controlled-tier-rejection fixture); (4) the License+access reviewer is named; (5) one
  dataset fully documented end-to-end with **100% provenance coverage** and **adopted** via informal
  channel or self-serve fallback (acceptance artifact recorded) — or, if no channel materializes,
  **submitted** with the blocker surfaced; (6) ≥ 1 partner-outreach thread opened.

**M1 — Gate hardened, crosswalks, harmonization seed, first adoptions**
- Goal: make the gate rigorous, add controlled-vocabulary crosswalks (incl. OMOP + harvest of
  official mappings), stand up drift detection, seed the harmonization table, and get real
  dictionaries adopted.
- Exit criteria: (1) gate codified as a reviewable artifact and applied to ≥ 3 datasets with
  committed dispositions; (2) crosswalk tooling enforces the ontology-licensing policy (restricted
  vocab = link-out, proven by fixtures), reproduces official mappings with `mappingOrigin`, and
  includes **OMOP CDM** as a target; (3) ≥ 2 dictionaries **adopted** upstream; (4) ≥ 1
  confirmed partner; (5) license/source-doc snapshot capture working (committed copy + SHA-256 +
  Wayback); (6) **drift detection live (moved up from M3)** — hash/diff the upstream dictionary (GDC
  YAML / GDC API field list / SEER recode page) against the pinned source version and open a
  `maintenance` task on change; (7) a **first cross-dataset field-equivalence table** (≥ 1 concept
  harmonized across ≥ 2 sources, e.g. `vital_status`) published as a named deliverable.

**M2 — Scale + researcher plain-language layer**
- Goal: increase throughput and add a researcher-facing plain-language glossary; quantify effort
  reduction.
- Exit criteria: (1) ≥ 5 dictionaries adopted cumulatively across ≥ 2 distinct data sources; (2)
  researcher plain-language glossary layer shipped for ≥ 2 datasets (low–medium, domain-reviewed);
  (3) dictionary-quality scoring automated (mechanical before/after); (4) median per-dataset effort
  (AI-session minutes + human-review cycles, from the outcome ledger) measurably reduced vs. the
  M0/M1 baseline; (5) the **cross-dataset field-equivalence table expanded** (≥ 3 concepts across
  ≥ 3 sources) and tracked with its own coverage metric; (6) **round-trip/coverage test** in CI
  proving value-level provenance survives (or is documented as intentionally absent in) each lossy
  projection; (7) Croissant emitted per dictionary to gain free discovery (Google Dataset Search /
  Hugging Face indexing), widening the reuse-signal funnel.

**M3 — Patient-facing layer (gated, high risk) + reuse outcomes + sustainability**
- Goal: demonstrate downstream reuse, establish a maintenance model, and (only if a qualified
  review panel is secured) release an oncologist- and advocate-reviewed patient glossary.
- Exit criteria: (1) ≥ 3 verifiable external reuse events; (2) ≥ 8 dictionaries adopted
  cumulatively; (3) the M1 drift detection matured into a documented staleness/refresh process +
  named steward (the *mechanism* lands in M1; M3 formalizes the human refresh loop); (4) **if and only if**
  oncologist + advocate reviewers are secured, ≥ 1 patient-facing educational glossary released with
  both sign-offs and a "not medical advice" notice — otherwise this item is explicitly deferred, not
  shipped unreviewed.

Dependencies: M1 depends on M0 toolkit + gate; M2 depends on M1's canonical dictionaries; M3
patient layer depends on securing credentialed reviewers and on a body of M1–M2 dictionaries.

## Work breakdown

The itemized, schema-mapped backlog lives in `TASKS.md`, organized by the milestones above, each
with a task table (`ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer`),
acceptance criteria for the most important tasks, and a milestone Definition of Done. A
backlog of sized-but-unscheduled tasks, a license/access-tier-classified candidate dataset catalog,
and one complete, schema-valid example Task JSON are included there. Each per-dataset dictionary
task requires its own committed gate artifact before work proceeds — listing it does not pre-approve
it.

## Governance, roles & stakeholders

- **Maintainer (Owner):** TBD — owns the toolkit, triage, and backlog.
- **Access-tier + License + PII reviewer:** TBD (TO BE SECURED) — mandatory, **non-skippable**
  gatekeeper; no deed ships without this sign-off. Must be filled **before the M0 pilot is
  reviewed**. *How filled:* the Maintainer recruits/designates a named reviewer who can read
  open-data and bioinformatics licenses (NIH/NCI terms, CC, COSMIC/OncoKB custom terms) and apply
  the access-tier + PII methodology. May rotate among ≥ 2 qualified reviewers, but at least one
  named reviewer must exist at all times or triage halts. Until named, all tasks stay
  `verifiedNeed:false` and no dataset passes the gate.
- **Technical reviewer(s):** rotation verifying dictionaries, emitters, validators (CI green,
  provenance 100%).
- **Domain reviewer(s):** cancer-informatics / bioinformatician(s) verifying field-meaning and
  coded-value accuracy (medium-risk tasks).
- **Oncologist & patient-advocate reviewers (M3, high risk):** credentialed sign-off for any
  patient-facing educational content. Both required; without them, M3 patient items are not built.
- **Steward (last-mile owner):** TBD — owns relationships with commons/repos/advocacy orgs and
  confirms adoption (the "delivered" signal).
- **Partner / requestor:** TO BE SECURED — named commons/repo/advocacy partner(s).

## Dependencies & integrations

- **External standards/specs (pinned — see Tech stack):** Frictionless Table Schema, Croissant ML
  v1.0, schema.org/Dataset, SPDX license identifiers; controlled vocabularies ICD-O-3, NCI
  Thesaurus, caDSR CDE, **OMOP CDM (via OHDSI ATHENA)**, HGVS, MONDO, LOINC (SNOMED CT only under
  valid license). Source-native dictionary formats (GDC YAML) for upstream contribution. Versions
  recorded in `specVersions`/`crosswalks` and bumped only via a deliberate task.
- **Data sources (candidate, TO BE SELECTED via gate):** GDC/TCGA open tier, GEO, SEER aggregate
  (SEER\*Explorer), ClinVar, Expression Atlas, PRIDE, CPTAC open tier, DepMap, TCIA (per-collection).
  **Flagged/excluded:** COSMIC, OncoKB, GENIE, IARC/GLOBOCAN (flag); dbGaP, EGA, controlled
  TCGA/ICGC/TARGET, individual-level biobanks (excluded).
- **Adoption channels:** GDC/CRDC docs, cBioPortal, Sage Bionetworks/Synapse, EBI, **NCI CCDH
  (cancerdhc)** as prior-art collaborator/channel, Bioconductor/Galaxy training; GitHub, Hugging
  Face, Zenodo. Output-only contributions; no automated upload.
- **Elyos pieces:** Task JSON schema (`packages/schema`), donated-lane CLI workspace/PR flow
  (`packages/cli`), good-deed definition + refusal guardrails. No funded-lane/runner dependency.

## Risks & mitigations

| Risk | Likelihood | Impact | Mitigation | Owner |
| --- | --- | --- | --- | --- |
| Accidentally touching/documenting **controlled-access or identifiable** data | Low | Critical | Access-tier check is the first, blocking gate step; controlled = EXCLUDE; inspection forbidden on non-open data; CI fixture rejects `controlled` tier | Access+License+PII reviewer |
| Mis-classifying a license (treating non-redistributable as reusable) | Medium | High | Mandatory reviewer; `permitsDerivatives:true` only with cited clause; COSMIC/OncoKB/GENIE/IARC pre-flagged; exclude on doubt | Access+License+PII reviewer |
| Incorrect field meaning / coded-value interpretation (clinical fields) | Medium | High | Domain (cancer-informatics) review; source-verified only; gaps flagged not guessed; provenance 100% | Domain reviewer |
| Crosswalk republishes licensed vocabulary text (e.g. SNOMED CT) | Medium | Medium | Ontology-licensing policy fixed before crosswalks; restricted vocab = link-out; CI fixture proves term text not emitted | Maintainer |
| Verbatim copying of copyrighted source-documentation prose (WHO ICD-O-3 / SEER manuals) | Medium | Medium | Paraphrase-with-citation rule; verbatim-copy lint caps quoted runs + requires attribution (CI); separate from dataset-license check | Technical reviewer |
| Crosswalk work perceived as reinventing official mappings (GDC caDSR/NCIt already exist) | Low | Medium | Harvest-and-cite official mappings; `mappingOrigin: official\|derived`; credit/review focus on derived + harmonized only | Maintainer |
| Patient-facing content drifts into medical advice | Low | High | Patient layer gated behind oncologist + advocate sign-off; "not medical advice" notice; refuse/flag advice-shaped requests | Oncologist + advocate |
| No adoption partner → dictionaries produced but never adopted (fails "delivered") | Medium | High | M0 outreach + self-serve fallback (PR/Zenodo); steward role; `verifiedNeed:false` until secured | Steward |
| Source data/schema changes, dictionaries go stale | Medium | Medium | Record + pin source version; **drift detection lands in M1** (hash/diff upstream GDC YAML / API field list / SEER recode page, open `maintenance` task on change); refresh loop formalized in M3 | Maintainer |
| Spec/format drift (Croissant/Frictionless versions) | Medium | Low | Canonical-model-first; pinned spec versions; isolated version-bump tasks | Maintainer |
| Scope creep into cleaning/transforming/re-hosting data | Medium | Medium | Explicit non-goal; reviewers reject any data-movement work | Maintainer |
| Model invents an unsourced definition | Medium | High | Provenance is an emit-time hard constraint; validator fails build on any missing `sourceRef` | Technical reviewer |

## Security & privacy

- **Threat surface is small** (no runtime service, no data hosting). Main surfaces: CI and the
  metadata/dictionary files we publish.
- **Secrets handling:** tools require no credentials by default. If a portal/API token is ever
  needed to *read open metadata* or to contribute, it is supplied by the human and must never be
  written into logs, receipts, or committed files (Elyos rule).
- **PII / identifiable data:** the dominant concern is *upstream* — handled by the mandatory
  access-tier + PII exclusion gate. We never download, store, or process individual-level or
  identifiable cancer data; controlled-access data is never accessed at all.
- **Abuse/misuse prevention:** refuse and flag any task steering documentation toward
  re-identification, deanonymization, linkage of individuals, accessing controlled tiers, or
  laundering a non-open / non-redistributable dataset as open. Refuse advice-shaped requests on
  patient-facing material outside the gated, expert-reviewed channel.

## Sustainability & maintenance

- **Post-delivery ownership:** the steward maintains commons/repo/advocacy relationships; the
  maintainer keeps the toolkit (emitters, validators, crosswalks, template) current with spec and
  source changes.
- **Refresh:** validators let anyone re-check an open dataset against its documented schema;
  recorded version + cadence flags when a refresh is due. Stale dictionaries become `maintenance`
  tasks; ontology/spec version bumps are isolated tasks.
- **Outcome tracking:** the steward records adoption events and external reuse signals against the
  success metrics; reviewed each milestone. Patient-facing items are re-reviewed by the expert panel
  on a fixed cadence or when guidance changes.

## Adjacent opportunities

Spin-offs that reuse this project's artifacts without expanding its core scope (tracked, not
committed):
- **Harmonized cross-dataset field-mapping table** (parallel, highest value) — promoted here to a
  named deliverable; usable standalone by other tools.
- **"What does this column mean?" MCP server** (perpendicular) — serve the canonical dictionaries
  read-only over MCP so an analyst's agent answers field/value questions in-context with provenance;
  aligns with the emerging Croissant+MCP direction; strictly read-only, no data access (donated lane).
- **Tie-in with `cancer-dataset-datasheets`** — datasheet (cover page) links to dictionary (index);
  shared dataset-id registry and one reusable gate-artifact format across both.
- **`open-cohort-catalog`** — a registry of open cancer datasets keyed to their dictionaries +
  access-tier dispositions; this plan's candidate catalog is the seed.
- **`oncology-glossary-multilingual`** — the gated M3 plain-language glossary, translated; reuses the
  translation deliverable type and the "not medical advice" gating.
- **Schema-conformance validator as a reusable package** — re-check any open dataset against its
  documented schema; useful far beyond cancer.

## Open questions

- Which commons/repo/advocacy partner will be the first confirmed adopter (GDC/CRDC, cBioPortal,
  Synapse, EBI, an advocacy data group)?
- For flagged non-commercial sources (COSMIC, OncoKB, GENIE, IARC/GLOBOCAN), does the NC/custom
  policy ever permit a dictionary (our original description) even when the source is NC, and under
  what attribution? (Default: flag/exclude pending policy decision.)
- Crosswalk targets: which ontology versions do we pin, and is a SNOMED CT affiliate license
  available to the project (else link-out only)?
- **OMOP CDM** is now a crosswalk target (harmonization moat) — confirm the cross-dataset semantic
  anchor (NCIt vs caDSR CDE vs OMOP `concept_id`) used as the canonical equivalence key.
- For GDC, the plan **reproduces-and-cites** the official caDSR-CDE/NCIt mappings (not independent
  mapping) — confirm scope framing so the work reads as harmonization + plain-language, not
  reinventing the official dictionary.
- Definition-prose copyright policy for WHO ICD-O-3 / SEER manual text: confirm the paraphrase-only
  default and the length cap for short attributed quotes enforced by the verbatim-copy lint.
- Is **Croissant** worth its cost for clinical tables, or should the second emit target be the
  source's **native format** (GDC YAML) to enable native upstream PRs? (Plan now does both;
  prioritization TBD.)
- The **cross-dataset field-equivalence table** is now a named M1/M2 deliverable with its own
  coverage metric — confirm the metric threshold and which sources/concepts are in the first cut.
- Is **CCDH (cancerdhc)** best engaged as prior-art collaborator or as an adoption partner channel?
- Can the oncologist + patient-advocate review panel be secured for M3, or is the patient-facing
  glossary deferred indefinitely?
- For GDC/GEO, do we contribute dictionaries via PR, the commons' own docs process, or a Zenodo
  companion record?
- What counts as a sufficiently "verifiable external reuse event" for the outcome metric?

## References

- Elyos work rules — `C:\code\elyos\CLAUDE.md`
- Good Deed Definition + risk tiers — `C:\code\elyos\docs\good-deed-definition.md`
- Task JSON schema — `C:\code\elyos\packages\schema\src\schemas.ts`
- Portfolio roadmap (Track 8 cancer guardrails) — `C:\code\elyos\planning\ROADMAP.md`
- Analogous project — `C:\code\elyos\planning\projects\open-data-datasheets\PLAN.md`
- NCI Genomic Data Commons (GDC) Data Dictionary & access tiers
- NCBI Gene Expression Omnibus (GEO); NCBI ClinVar
- SEER / SEER\*Explorer (aggregate) and SEER coding manuals; SEER research-data DUA terms
- EBI Expression Atlas, PRIDE; CPTAC / Proteomic Data Commons; DepMap (CC BY 4.0)
- The Cancer Imaging Archive (TCIA) per-collection licensing
- COSMIC, OncoKB, AACR Project GENIE, IARC/GLOBOCAN (flagged license terms)
- WHO ICD-O-3; NCI Thesaurus (NCIt); HGVS nomenclature; MONDO; LOINC; SNOMED CT (licensed)
- NCI GDC Data Dictionary repo (open YAML) — `NCI-GDC/gdcdictionary`; caDSR / CDE Browser (ISO/IEC 11179)
- OHDSI OMOP CDM standardized vocabularies + ATHENA — `OHDSI/Athena`
- NCI Center for Cancer Data Harmonization (CCDH / cancerdhc) — prior art / adoption channel
- cBioPortal data-format docs (free-form per-study clinical attributes)
- Frictionless Table Schema; Croissant ML metadata (MLCommons; Google Dataset Search indexing); schema.org/Dataset; SPDX
- Datasheets for Datasets (Gebru et al.) — companion documentation pattern; sibling `cancer-dataset-datasheets`

---

## Appendix A — Improvements applied

The following 25 specific improvements were identified during planning and are **already applied**
in the body above (not left as suggestions).

1. **Access-tier check made the *first* gate step**, not bundled into "license" — an EXCLUDE there
   ends the task before any inspection (Solution §pipeline, Compliance, Risks).
2. **Controlled-access sources enumerated explicitly** (dbGaP, EGA, controlled TCGA/ICGC/TARGET,
   individual-level biobanks) so "out of scope" is concrete, not abstract (Non-goals, Compliance).
3. **Per-source license rules encoded** with named examples (GDC/GEO open; SEER aggregate vs.
   case-level DUA; COSMIC/OncoKB/GENIE/IARC flagged) so triage applies fixed rules (Compliance).
4. **Provenance elevated to a hard, emit-time constraint** — the validator fails the build on any
   assertion missing a `sourceRef`; provenance coverage target is 100%, not "high" (Metrics, Q&R).
5. **Ontology/codelist licensing trap surfaced and policied** (`ontology-policy-003`): restricted
   vocab (SNOMED CT) is link-out, proven by a CI fixture (Compliance, Q&R, Risks).
6. **SEER split into aggregate-eligible vs. case-level-excluded** rather than treating "SEER" as one
   thing — a common real-world misclassification (Compliance, Dependencies).
7. **Genomic re-identification awareness** added: individual-level germline/genotype treated as
   inherently identifiable regardless of stated de-identification (Privacy/PII).
8. **"Describe but never store" made enforceable** via a bounded inspection access protocol scoped
   to open-access data only, with halt-on-identifiability (Solution).
9. **Patient-facing layer isolated into its own gated M3 milestone** with oncologist + advocate
   sign-off and "not medical advice," so the low-risk core is not contaminated by high-risk work.
10. **M3 patient item made conditional** — explicitly *deferred, not shipped unreviewed* if the
    expert panel cannot be secured (Roadmap M3, Open questions).
11. **Cold-start self-serve fallback** (GitHub PR / Zenodo DOI) so M0 can yield a real *adopted*
    outcome without depending on a third party (Roadmap M0).
12. **Definition of Shipped = adopted upstream, not produced**, with per-channel evidence artifacts
    (`outcomes/<dataset-id>.json`) — matches the Elyos "delivered, not merged" bar.
13. **`verifiedNeed:false` until a named partner confirms adoption**, with the rationale stated
    honestly (general need real, per-task need unproven) (Problem, TASKS mapping).
14. **Reviewer role is blocking and must be filled before the M0 pilot review**, with rotation but a
    minimum of one named reviewer at all times (Governance, Q&R).
15. **Canonical-model-first architecture** so JSON/Frictionless/Croissant never drift apart
    (Solution, Risks).
16. **Spec + ontology versions pinned and recorded** per dictionary/crosswalk for reproducibility,
    with isolated version-bump tasks (Solution, Sustainability).
17. **Dictionary-quality score defined** (field coverage, value-set coverage, provenance 100%,
    machine-validity) with before/after capture so DoDs are measurable (Metrics).
18. **CI golden fixtures specified to encode the guardrails in code** — including a fixture that
    asserts a `controlled` access tier is rejected and that restricted-vocab term text is not emitted.
19. **No-invented-definitions rule** made explicit: gaps are flagged as documentation debt, never
    filled from the model's prior (Non-goals, Solution, Risks).
20. **License-text + source-doc snapshot format decided** (committed copy + SHA-256 + Wayback)
    rather than linking a bare URL (Compliance, M1 snapshot task).
21. **Sentinel / "not reported" conventions made a first-class field attribute** — a top cause of
    silent analysis errors in cancer data — in the canonical model and acceptance criteria.
22. **Two-tier plain-language layering** (researcher glossary low–medium in M2; patient glossary
    high in M3) so legibility for non-specialists is staged by risk (Scope, Roadmap).
23. **Refusal guardrails wired to concrete cancer abuse cases** (re-identification, accessing
    controlled tiers, laundering non-open data as open) (Security & privacy).
24. **Outcome ledger + effort metric** (AI-session minutes + review cycles) so M2's "effort reduced"
    claim is verifiable against a recorded baseline (Metrics, Roadmap M2).
25. **Attribution / dual-licensing clarified** — docs CC-BY-4.0, code MIT, source NC constraints
    recorded even though our description is independently CC-BY (Compliance).

---

## Review sign-off

**Reviewer pass (self-review against PLAN_SPEC.md, the cancer guardrails, CLAUDE.md, and the Task
schema).**

- **Completeness:** All 17 required H2 sections present and in order. `Data, licensing & compliance`
  leads with the binding cancer guardrails as required. TASKS.md exists with the schema mapping,
  per-milestone tables, acceptance criteria, backlog, candidate catalog, and a schema-valid example
  Task JSON. Appendix A lists 25 applied improvements. *PASS.*
- **Cancer-guardrail correctness:** Open-access/aggregate/de-identified only; controlled-access
  (dbGaP/EGA/individual-level biobanks) and identifiable data categorically excluded; per-source
  license rules (GDC/GEO open; SEER aggregate vs. DUA; COSMIC/OncoKB/GENIE/IARC flagged);
  provenance-on-every-assertion enforced; patient-facing = education only + "not medical advice" +
  oncologist/advocate review at `high`. *PASS.*
- **Schema correctness:** Verified the example Task JSON and all task rows use only enum-valid values
  for `type`, `lane`, `priority`, `riskTier`, `deliverable`, `tokenEstimate`, `status`; all required
  fields present; `verifiedNeed:false`; no funded tasks (so `fundedBudgetUsd` not required);
  deliverables restricted to `pr` (code) / `document` (dictionaries) / `translation` (i18n) — never
  `dataset`, since data is out of scope. *PASS.*
- **Fix applied during review:** Clarified that documenting a *publicly published schema spec*
  (e.g. the GDC Data Dictionary, which also covers controlled fields) is permitted **only from the
  public spec with no access to controlled records**, removing an ambiguity that could have read as a
  controlled-data loophole (Compliance §guardrail 2). Confirmed `deliverable` never set to `dataset`
  anywhere. Confirmed M3 patient item is conditional on securing reviewers.
- **Residual human decisions (flagged, not resolved):** secure adoption partner(s); secure the
  access/license/PII reviewer; secure the M3 oncologist+advocate panel; decide the NC/custom-source
  policy outcome for COSMIC/OncoKB/GENIE/IARC; confirm SNOMED CT licensing for crosswalks.

**Sign-off:** Ready for maintainer review and partner outreach. No fabricated partners, needs, or
licenses; all unknowns marked TO BE SECURED.

---

## Changelog — v0.2 (analysis merged)

Merges `COMPETITIVE-ANALYSIS.md` into the plan. Fixes applied (correctness/license/safety/schema)
and strategy additions, one line each.

**Fixes applied**
- Harvest-and-cite official mappings: GDC ships caDSR CDE Public IDs + NCIt codes as open YAML, so "map to NCIt/CDE" is reproduce-and-cite, not derive (Exec summary, Goals, Solution pipeline 4).
- Added `mappingOrigin` (`official`|`derived`) to the canonical model + crosswalks so credit/review focus on derived/harmonized mappings (Solution model; Risks).
- Added **OMOP CDM** (via ATHENA) as a first-class crosswalk target — the missing cross-dataset harmonization anchor (Exec summary, Goals, Scope, Solution, Compliance, Dependencies).
- Source-documentation copyright treated as a license risk distinct from the dataset license: paraphrase-with-citation rule + verbatim-copy CI lint for WHO ICD-O-3 / SEER manual prose (Solution pipeline 3; new Compliance subsection; Risks).
- Lossy emit formats made explicit: value-level provenance survives only in canonical JSON; added a round-trip/coverage test; native GDC YAML added as an emit target (Solution pipeline 5; M2).
- Drift detection moved from M3 to **M1** with a concrete mechanism (hash/diff upstream GDC YAML / API field list / SEER recode page) (Roadmap M1/M3; Risks).
- Before/after legibility score made mechanical (count of fields with complete sourced definitions) so it is externally reproducible (Success metrics).
- Pinned monthly/annual release ids (NCIt e.g. 26.05d, ICD-O-3.2, OMOP/ATHENA release, GDC dictionary version, SEER recode) in `specVersions` (Solution).
- M0 pilot re-targeted to GDC open-tier clinical or a GEO series (richer field/value structure); SEER aggregate deferred (Roadmap M0).

**Strategy integrated**
- New "## Competitive landscape & differentiation" section: GDC Data Dictionary, NCI caDSR/CDE Browser, NCIt, SEER manuals, cBioPortal, OHDSI OMOP/ATHENA, CCDH, Frictionless/Croissant.
- Named differentiator deliverable: the harmonized cross-dataset field-equivalence table (GDC↔cBioPortal↔SEER↔GEO to shared vocabularies), promoted from byproduct to M1/M2 deliverable with its own coverage metric.
- Claude-API leverage folded into the Solution/architecture section (plain-language definitions with citations + gap-flagging; field→NCIt/CDE/OMOP mapping candidates; cross-dataset harmonization) keeping trace-to-source + human-verify boundaries.
- Top optimizations folded into the Roadmap/Work-breakdown (OMOP target, harvest official mappings, equivalence table, Croissant discovery, drift detection, verbatim lint, mechanical scoring, pinned versions, round-trip test, pilot re-target).
- New "## Adjacent opportunities" note from the spin-offs (harmonization table, MCP lookup server, datasheets tie-in, open-cohort-catalog, multilingual glossary, reusable schema-conformance validator).
- Open Questions merged (OMOP anchor, GDC reproduce-vs-derive framing, definition-prose copyright policy, Croissant-vs-native-format, equivalence-table metric, CCDH engagement).
- Relationship to sibling `cancer-dataset-datasheets` clarified (field-level index vs dataset-level cover page; cross-link).
