# PLAN — cancer-data-dictionaries

> Status: Draft · Version: 0.1.0 · Last updated: 2026-06-28 · Owner: TBD (maintainer) · Lane: donated

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
ML metadata) form, plus controlled-vocabulary crosswalks (e.g. ICD-O-3, NCI Thesaurus, HGVS) where
licensing permits, and small validators that let any reuser re-check a dataset against its
documented schema.

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
- Provide controlled-vocabulary **crosswalks** (ICD-O-3, NCI Thesaurus, HGVS, and similar) where
  the vocabulary's own license permits redistribution of the mapping.
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
100%.

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
  (NCIt), HGVS, MONDO; LOINC with its open license recorded). SNOMED CT mappings only where a valid
  affiliate/member license covers redistribution — otherwise excluded.
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
4. **Controlled-vocabulary crosswalk** — map coded fields to standard ontologies *where the
   ontology license permits redistribution*; otherwise link out rather than republish.
5. **Machine-readable emission** — canonical JSON → projections: Frictionless Table Schema +
   Croissant ML (JSON-LD).
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
provenance{sourceRef} } }`, `crosswalks[]{field, targetVocabulary, license, mapping[]}`,
`qualityScore {before, after}`, `specVersions {frictionless, croissant}`.

**Tech stack.** TypeScript, ESM, pnpm workspaces (Elyos convention). Emitters, validators, and
crosswalk tooling are small Node packages with minimal dependencies. Dictionaries authored in
Markdown + JSON/JSON-LD. No runtime services; everything runs locally or in CI.

**Pinned target spec versions** (recorded in `specVersions`, bumped only by a deliberate task):
- **Frictionless Table Schema** — v1 / v2 (current published spec at adoption; pinned per dataset).
- **Croissant ML** — v1.0 (MLCommons), validated against the v1.0 JSON-LD context/SHACL.
- **Ontology/codelist versions** — ICD-O-3.2, NCI Thesaurus (release date pinned), HGVS recommended
  nomenclature version, MONDO release — each recorded per crosswalk so a mapping is reproducible.

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
runs: **redistribute mappings** for open vocabularies (NCIt, HGVS, MONDO, LOINC under its license
with attribution); for **restricted** vocabularies (notably **SNOMED CT**, and any non-commercial
ICD-O derivative concerns), **link out by code rather than republishing the term text**, unless a
valid license covers redistribution. The chosen vocabulary version is pinned per crosswalk.

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
- Pilot candidate (lowest-risk): a clearly open dataset — e.g. a **GEO series** dictionary, **GDC
  open-tier clinical** fields, or a **SEER\*Explorer aggregate** table.
- Exit criteria: (1) dictionary template + canonical model published; (2) access-tier+license+PII
  gate checklist (incl. ontology-licensing policy) exists and is applied to one dataset; (3)
  JSON/Frictionless/Croissant emitters + dictionary validator green in CI with golden fixtures
  (incl. the controlled-tier-rejection fixture); (4) the License+access reviewer is named; (5) one
  dataset fully documented end-to-end with **100% provenance coverage** and **adopted** via informal
  channel or self-serve fallback (acceptance artifact recorded) — or, if no channel materializes,
  **submitted** with the blocker surfaced; (6) ≥ 1 partner-outreach thread opened.

**M1 — Gate hardened, crosswalks, first adoptions**
- Goal: make the gate rigorous, add controlled-vocabulary crosswalks, and get real dictionaries
  adopted.
- Exit criteria: (1) gate codified as a reviewable artifact and applied to ≥ 3 datasets with
  committed dispositions; (2) crosswalk tooling enforces the ontology-licensing policy (restricted
  vocab = link-out, proven by fixtures); (3) ≥ 2 dictionaries **adopted** upstream; (4) ≥ 1
  confirmed partner; (5) license/source-doc snapshot capture working (committed copy + SHA-256 +
  Wayback).

**M2 — Scale + researcher plain-language layer**
- Goal: increase throughput and add a researcher-facing plain-language glossary; quantify effort
  reduction.
- Exit criteria: (1) ≥ 5 dictionaries adopted cumulatively across ≥ 2 distinct data sources; (2)
  researcher plain-language glossary layer shipped for ≥ 2 datasets (low–medium, domain-reviewed);
  (3) dictionary-quality scoring automated; (4) median per-dataset effort (AI-session minutes +
  human-review cycles, from the outcome ledger) measurably reduced vs. the M0/M1 baseline.

**M3 — Patient-facing layer (gated, high risk) + reuse outcomes + sustainability**
- Goal: demonstrate downstream reuse, establish a maintenance model, and (only if a qualified
  review panel is secured) release an oncologist- and advocate-reviewed patient glossary.
- Exit criteria: (1) ≥ 3 verifiable external reuse events; (2) ≥ 8 dictionaries adopted
  cumulatively; (3) documented staleness/refresh process + named steward; (4) **if and only if**
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
  Thesaurus, HGVS, MONDO, LOINC (SNOMED CT only under valid license). Versions recorded in
  `specVersions`/`crosswalks` and bumped only via a deliberate task.
- **Data sources (candidate, TO BE SELECTED via gate):** GDC/TCGA open tier, GEO, SEER aggregate
  (SEER\*Explorer), ClinVar, Expression Atlas, PRIDE, CPTAC open tier, DepMap, TCIA (per-collection).
  **Flagged/excluded:** COSMIC, OncoKB, GENIE, IARC/GLOBOCAN (flag); dbGaP, EGA, controlled
  TCGA/ICGC/TARGET, individual-level biobanks (excluded).
- **Adoption channels:** GDC/CRDC docs, cBioPortal, Sage Bionetworks/Synapse, EBI, Bioconductor/
  Galaxy training; GitHub, Hugging Face, Zenodo. Output-only contributions; no automated upload.
- **Elyos pieces:** Task JSON schema (`packages/schema`), donated-lane CLI workspace/PR flow
  (`packages/cli`), good-deed definition + refusal guardrails. No funded-lane/runner dependency.

## Risks & mitigations

| Risk | Likelihood | Impact | Mitigation | Owner |
| --- | --- | --- | --- | --- |
| Accidentally touching/documenting **controlled-access or identifiable** data | Low | Critical | Access-tier check is the first, blocking gate step; controlled = EXCLUDE; inspection forbidden on non-open data; CI fixture rejects `controlled` tier | Access+License+PII reviewer |
| Mis-classifying a license (treating non-redistributable as reusable) | Medium | High | Mandatory reviewer; `permitsDerivatives:true` only with cited clause; COSMIC/OncoKB/GENIE/IARC pre-flagged; exclude on doubt | Access+License+PII reviewer |
| Incorrect field meaning / coded-value interpretation (clinical fields) | Medium | High | Domain (cancer-informatics) review; source-verified only; gaps flagged not guessed; provenance 100% | Domain reviewer |
| Crosswalk republishes licensed vocabulary text (e.g. SNOMED CT) | Medium | Medium | Ontology-licensing policy fixed before crosswalks; restricted vocab = link-out; CI fixture proves term text not emitted | Maintainer |
| Patient-facing content drifts into medical advice | Low | High | Patient layer gated behind oncologist + advocate sign-off; "not medical advice" notice; refuse/flag advice-shaped requests | Oncologist + advocate |
| No adoption partner → dictionaries produced but never adopted (fails "delivered") | Medium | High | M0 outreach + self-serve fallback (PR/Zenodo); steward role; `verifiedNeed:false` until secured | Steward |
| Source data/schema changes, dictionaries go stale | Medium | Medium | Record version + retrieval date; validator detects drift; refresh milestone (M3) | Maintainer |
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

## Open questions

- Which commons/repo/advocacy partner will be the first confirmed adopter (GDC/CRDC, cBioPortal,
  Synapse, EBI, an advocacy data group)?
- For flagged non-commercial sources (COSMIC, OncoKB, GENIE, IARC/GLOBOCAN), does the NC/custom
  policy ever permit a dictionary (our original description) even when the source is NC, and under
  what attribution? (Default: flag/exclude pending policy decision.)
- Crosswalk targets: which ontology versions do we pin, and is a SNOMED CT affiliate license
  available to the project (else link-out only)?
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
- Frictionless Table Schema; Croissant ML metadata (MLCommons); schema.org/Dataset; SPDX
- Datasheets for Datasets (Gebru et al.) — companion documentation pattern

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
