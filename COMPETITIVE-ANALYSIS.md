# Competitive + Improvement Analysis — `cancer-data-dictionaries`

Scope reviewed: `PLAN.md` v0.1.0 (2026-06-28). TASKS.md not present in the scratchpad and not
fetched (gh not invoked); review of the work breakdown is therefore inferred from PLAN §"Work
breakdown" and the inline catalog/metrics. Web research conducted 2026-06-29; sources cited inline.

---

## 1. Correctness & completeness review of PLAN.md

The plan is unusually disciplined: the access-tier-first gate, provenance-as-hard-constraint,
canonical-model-first emission, and the SEER aggregate-vs-case-level split are all correct and
ahead of the field. The issues below are real gaps, not nitpicks.

**1.1 — Biggest gap: the plan under-reuses official machine-readable crosswalks it treats as new
work.** For its flagship source (GDC), every property in the GDC Data Dictionary is *already*
assigned a caDSR CDE Public ID and linked to NCI Thesaurus concept codes, and the whole dictionary
ships as open YAML / JSON-schema on GitHub (`NCI-GDC/gdcdictionary`)
(https://docs.gdc.cancer.gov/Data_Dictionary/, https://github.com/NCI-GDC/gdcdictionary). So "map
fields to NCIt/CDE codes" (Goals; pipeline step 4) is, for GDC, largely a *harvest-and-cite*
operation, not derivation. The plan never says this. Risk: a reviewer perceives the crosswalk work
as reinventing an official mapping. Fix: the canonical model should carry a `sourceMapping` field
distinguishing **official mapping reproduced** (cite GDC/caDSR) from **mapping we derived**
(needs domain review) — and the plan should claim credit only for the latter plus harmonization.

**1.2 — OMOP is conspicuously absent from crosswalk targets.** Crosswalk targets list ICD-O-3,
NCIt, HGVS, MONDO, LOINC, SNOMED — but not OMOP CDM, the dominant *cross-dataset harmonization*
standard (10M+ concepts, 136 vocabularies, free via ATHENA)
(https://ohdsi.github.io/TheBookOfOhdsi/StandardizedVocabularies.html,
https://github.com/OHDSI/Athena). Since the project's differentiator is cross-dataset
harmonization (§3-4), mapping fields to OMOP domains/concepts is the single most valuable crosswalk
and its omission is a substantive gap.

**1.3 — Format choice is lossy and partly mismatched to the artifact.** Neither emit format
natively expresses the plan's core unit (per-coded-value meaning + per-assertion provenance).
Frictionless Table Schema gives per-field `enum`/`description` but no per-value semantics or
source ref (https://frictionlessdata.io/specs/table-schema/). Croissant's structure/semantic
layers are oriented to *ML-ready* dataset loading, with coarse field descriptions, not clinical
coded-value dictionaries (https://docs.mlcommons.org/croissant/docs/croissant-spec.html,
https://arxiv.org/pdf/2403.19546). So the canonical-model-first decision is *correct*, but the
plan should state explicitly that JSON/Frictionless/Croissant projections are **lossy** (value-level
provenance lives only in canonical JSON) and add a round-trip/coverage test. Also: emitting GDC
dictionaries in GDC's own YAML form (for native upstream contribution) is arguably more useful than
Croissant, which the plan over-weights; Croissant fits GEO/Hugging Face better than clinical tables.

**1.4 — License risk of the *source documentation*, not just the dataset, is under-addressed.**
The compliance section rigorously checks dataset licenses and vocabulary term-text (SNOMED), but a
field *definition* lifted verbatim from a WHO ICD-O-3 manual or a SEER coding manual is itself
potentially copyrighted text, independent of the dataset's open status (SEER manuals/recode docs:
https://seer.cancer.gov/registrars/recodes.html). The "read from published documentation, not
inferred" rule (pipeline 3) can produce verbatim copying. Fix: mandate **paraphrase-with-citation**
for definition prose and reserve verbatim quotes to short, clearly-attributed snippets — and add a
CI/lint check against long verbatim runs from source manuals.

**1.5 — Versioning/staleness is deferred too late.** GDC releases dictionary versions regularly and
SEER recodes change across releases; the plan records `version`/`retrievedAt` but pushes the refresh
process to M3. Given annual+ cadence, drift detection belongs in M1, not M3. The validator's "detect
drift" claim (Risks) needs a concrete mechanism (e.g., hash the upstream dictionary YAML / API
field list and diff).

**1.6 — Pilot/metric mismatch for SEER aggregate.** SEER\*Explorer aggregate tables don't have a
rich column/coded-value structure the way GDC clinical or GEO series matrices do, so choosing a SEER
aggregate table as the M0 pilot would weakly exercise the field-dictionary model. Recommend the
pilot be GDC open-tier clinical or a GEO series (better fit), keeping SEER aggregate for later.

**1.7 — The "legibility before-score" is subjective and externally weak.** The 0–100
dictionary-quality score (§Success metrics) mixes objective sub-scores (machine-validity, provenance
100%) with a "before-score" of the published dataset that is judgment-based; an external party can't
reproduce it. Either make the before/after purely mechanical (count of fields with a complete,
sourced definition) or drop the before-score's quantitative claim.

**1.8 — Minor:** (a) NCIt is open/public-domain and good for redistribution
(https://evs.nci.nih.gov/evs-download/thesaurus-downloads) — the plan correctly lists it as open but
should pin the monthly release id (e.g., 26.05d) since NCIt updates monthly. (b) "Croissant v1.0"
plus "schema.org/Dataset" is fine but the plan should note Google Dataset Search indexes Croissant
(a free distribution channel — see §6). (c) `verifiedNeed:false` discipline is excellent; keep it.

---

## 2. Competitive landscape

**Official / authoritative dictionaries (the incumbents):**

- **GDC Data Dictionary (NCI).** Authoritative per-property definitions, YAML/JSON-schema, open on
  GitHub, with caDSR CDE Public IDs and NCIt concept codes attached to properties; a Dictionary
  Viewer UI. *Strengths:* canonical source of truth, already machine-readable, already cross-linked
  to NCIt/CDE. *Weaknesses:* GDC-only; terse/technical; no plain-language layer; no cross-dataset
  harmonization; coded-value meanings often point out to separate manuals.
  (https://docs.gdc.cancer.gov/Data_Dictionary/, https://github.com/NCI-GDC/gdcdictionary)
- **NCI caDSR / CDE Browser.** ISO/IEC 11179 metadata registry of standardized Common Data Elements;
  search/download CDEs as Excel/XML. *Strengths:* authoritative, standards-compliant, the actual
  source of GDC's CDE links. *Weaknesses:* heavyweight, registry-oriented, not a per-dataset
  ready-to-use dictionary; steep UX; not "what does column X in this file mean."
  (https://wiki.nci.nih.gov/display/caDSR/caDSR+CDE+Browser,
  https://gdc.cancer.gov/about-data/data-standards)
- **NCI Thesaurus (NCIt) / EVS.** ~100k biomedical concepts, codes, definitions, relationships;
  OWL + flat-file downloads; EVS Explore browser. *Strengths:* open, definitive concept definitions,
  ideal crosswalk target. *Weaknesses:* an ontology, not a dataset dictionary; doesn't tell you what
  a specific dataset's column encodes. (https://evs.nci.nih.gov/,
  https://evs.nci.nih.gov/evs-download/thesaurus-downloads)
- **SEER coding/recode manuals.** Authoritative definitions for site recode, behavior recode,
  staging, etc. *Strengths:* definitive for registry variables. *Weaknesses:* PDF-first, scattered
  across many manuals/recodes, not machine-readable, hard to cross-reference.
  (https://seer.cancer.gov/registrars/recodes.html, https://seer.cancer.gov/behavrecode/)
- **cBioPortal data-format docs.** Documents the file/meta-file formats and clinical attribute
  conventions. *Strengths:* practical for loaders. *Weaknesses:* explicitly *no fixed ontology* —
  clinical attributes are free-form per study, so per-study column meaning is undocumented centrally;
  exactly the legibility gap this project targets.
  (https://docs.cbioportal.org/file-formats/, https://github.com/cBioPortal/cbioportal/blob/master/docs/File-Formats.md)

**Harmonization / standards infrastructure (adjacent):**

- **OHDSI OMOP vocabularies + ATHENA.** The big cross-source harmonization play: a free, unified
  concept space (SNOMED/LOINC/RxNorm/ICD-O via CDM). *Strengths:* genuine cross-dataset
  interoperability, huge coverage. *Weaknesses:* requires mapping *into* the CDM (expensive ETL);
  not a per-published-dataset dictionary; oncology-specific column semantics still need a bridge —
  which the project could provide. (https://github.com/OHDSI/Athena)
- **NCI Center for Cancer Data Harmonization (CCDH / cancerdhc).** Directly adjacent: harmonizes
  cancer data models. Worth tracking as both prior art and a potential partner.
  (https://cancerdhc.github.io/ccdh-resources.html)
- **Frictionless Table Schema / Data Package.** A *format* for tabular schemas (fields, types,
  constraints). *Strengths:* simple, widely supported, JSON. *Weaknesses:* format not content —
  ships no cancer definitions; no per-value semantics or provenance. The project uses it as an emit
  target, correctly. (https://frictionlessdata.io/specs/table-schema/)
- **Croissant ML (MLCommons).** ML-dataset metadata vocabulary; exported by Hugging Face, Kaggle,
  OpenML; indexed by Google Dataset Search; emerging MCP integration. *Strengths:* discovery +
  AI-readiness + a free index channel. *Weaknesses:* coarse field semantics; not designed for
  clinical coded-value dictionaries. (https://mlcommons.org/working-groups/data/croissant/,
  https://mlcommons.org/2025/10/croissant-mcp/)
- **OpenML / data.world dictionaries.** Generic per-feature metadata and crowd dictionaries.
  *Strengths:* low-friction, machine-readable, community editing. *Weaknesses:* generic, no domain
  authority, no provenance discipline, no cancer-specific vocabulary mapping.
  (https://docs.openml.org/data/, https://docs.data.world/en/99066-data-dictionary.html)

**Verdict:** every incumbent is either (a) authoritative-but-siloed/technical (GDC, caDSR, NCIt,
SEER), or (b) a format/infra layer with no cancer content (Frictionless, Croissant, OMOP, OpenML).
No one ships a **plain-language, cross-dataset, machine-readable, provenance-checked, harmonized**
field dictionary. That seam is real.

---

## 3. Gaps we can fill

1. **Cross-dataset harmonization at the field level.** "`vital_status` in GDC = `OS_STATUS` in
   cBioPortal = `Vital status recode` in SEER," with each mapped to a shared concept (NCIt/OMOP).
   No incumbent spans sources; that is the unmet need.
2. **Plain-language layer over authoritative definitions.** GDC/caDSR/SEER are correct but terse and
   PDF-scattered; a human-readable label + paraphrased description + sourced coded-value table is
   the daily pain point, especially for cBioPortal's free-form per-study attributes.
3. **One machine-readable artifact that unifies meaning + value semantics + provenance.** Today this
   is spread across YAML schemas, Excel CDEs, OWL ontologies, and PDF manuals. A single canonical
   JSON (projected to Frictionless/Croissant) consolidates it.
4. **Sentinel / "not reported" conventions as first-class metadata** — a documented top cause of
   silent analysis errors that no incumbent dictionary surfaces uniformly.
5. **Provenance-checked-by-CI definitions** — turning "trust me" docs into build-failing,
   source-linked assertions; nobody else gates on this.
6. **Validators that re-check a real open dataset against its documented schema** — bridging
   "documentation exists" to "documentation is true," which official dictionaries don't ship.

---

## 4. Differentiators to win

- **Field-level vs dataset-level (vs the Elyos sibling `cancer-dataset-datasheets`).** Datasheets
  answer "what *is* this dataset, who made it, what are its biases/consent terms" (the Gebru et al.
  pattern). Dictionaries answer "what does *this column / this code* mean." They compose: a datasheet
  is the cover page; the dictionary is the field-by-field index. Clear, non-overlapping lane — and
  the two should cross-link (datasheet → dictionary per dataset).
- **Harmonization is the moat (strongest single differentiator).** A cross-dataset field-equivalence
  table mapped to shared vocabularies is something *no* incumbent provides and that directly enables
  reproducible secondary analysis across GDC/cBioPortal/SEER/GEO.
- **Provenance-as-hard-gate + open licensing (CC-BY docs / MIT code).** Auditable, redistributable,
  contributable-upstream — more rigorous than community dictionaries (data.world/OpenML) and more
  reusable than registry exports.
- **Cancer-specific guardrails as a feature, not overhead.** The access-tier/PII/license gate makes
  outputs *safe to adopt* by data commons — a trust differentiator for institutional partners.
- **Native upstream contribution.** Emitting in the source's own format (GDC YAML, Frictionless)
  means the dictionary can be merged back, not just published alongside.

---

## 5. Claude API leverage

**Where Claude clearly helps (with provenance + human verification):**

1. **Draft plain-language field definitions from official docs, with citations.** Feed Claude the
   GDC property entry / SEER manual section / GEO header and have it produce a paraphrased label +
   description + a `sourceRef`, flagging anything it cannot source. This is the highest-leverage use
   and directly attacks the legibility gap.
2. **Propose field→vocabulary mappings (NCIt/caDSR CDE/OMOP/ICD-O-3) as candidates.** For GDC,
   reconcile against the *existing* official CDE/NCIt links (verification, not invention); for
   cBioPortal's free-form attributes, propose candidate concept codes for human confirmation.
3. **Harmonize equivalent fields across datasets.** Claude is well-suited to cluster
   `vital_status`/`OS_STATUS`/`Vital status recode` and draft the equivalence table + caveats — the
   moat in §4 — subject to domain review.
4. (Supporting) **Extract coded-value sets and sentinels** from manuals/PDFs into the canonical model
   and **lint prose for verbatim-copy risk** (§1.4).

**Where Claude must NOT decide (hard boundaries — match the plan's no-invented-definitions rule):**

- **No fabricated field/value meanings.** Any definition not traceable to an authoritative source is
  omitted and flagged as a documentation gap — never filled from the model's prior. The validator,
  not the model, enforces 100% provenance.
- **License & access-tier calls are human-verified.** Claude may *summarize* a license clause but the
  `permitsDerivatives:true` and OPEN/CONTROLLED determinations are the human reviewer's, with cited
  evidence. Same for PII/identifiability decisions.
- **Crosswalk mappings are candidates, not commits** — domain reviewer signs off, especially where
  no official mapping exists; restricted-vocabulary term text is never emitted by the model.
- **No patient-facing medical phrasing** outside the gated M3 channel with oncologist + advocate
  sign-off.

---

## 6. Ten concrete optimizations

1. **Add OMOP CDM as a first-class crosswalk target** (concept_id + domain), via ATHENA, to make the
   harmonization claim real (§1.2).
2. **Harvest, don't re-derive, GDC's existing caDSR-CDE/NCIt links**; add a `mappingOrigin`
   (`official` vs `derived`) field so credit and review effort focus on derived/harmonized mappings.
3. **Ship the cross-dataset field-equivalence table as a named deliverable** (canonical JSON +
   Markdown), not an implicit byproduct — it is the differentiator.
4. **Publish each dictionary's Croissant to get free discovery** via Google Dataset Search /
   Hugging Face indexing, widening the "reuse signal" funnel.
5. **Move drift detection to M1**: hash/diff the upstream dictionary (GDC YAML, GDC API field list,
   SEER recode page) and open a `maintenance` task on change (§1.5).
6. **Add a paraphrase-vs-verbatim lint** to protect against copying WHO/SEER manual prose verbatim
   (§1.4); cap quoted runs and require attribution.
7. **Make before/after legibility scoring purely mechanical** (count of fields with complete sourced
   definition + coded-value table) so it is externally reproducible (§1.7).
8. **Pin monthly/annual release ids** for NCIt (e.g., 26.05d), ICD-O-3.2, SEER release, GDC
   dictionary version in `specVersions`/`crosswalks` for every artifact.
9. **Add a round-trip/coverage test** proving value-level provenance survives (or is documented as
   intentionally absent in) each lossy projection (§1.3).
10. **Re-target the M0 pilot to GDC open-tier clinical or a GEO series** (rich field/value structure)
    rather than a SEER aggregate table (§1.6); keep self-serve Zenodo fallback.

---

## 7. Parallel & perpendicular spin-offs

- **Harmonized cross-dataset field-mapping table** (parallel; highest value) — a standalone,
  citable crosswalk (GDC ↔ cBioPortal ↔ SEER ↔ GEO ↔ OMOP/NCIt) that other tools consume.
- **"What does this column mean?" MCP server** (perpendicular) — serve the canonical dictionaries
  over MCP so an analyst's agent answers field/value questions in-context with provenance; aligns
  with the emerging Croissant+MCP direction (https://mlcommons.org/2025/10/croissant-mcp/). Strictly
  read-only, no data access — fits the donated lane.
- **Tie-in with `cancer-dataset-datasheets`** — datasheet (cover page) links to dictionary (index);
  shared dataset-id registry; one gate artifact format reused across both.
- **`open-cohort-catalog`** — a registry of open cancer datasets keyed to their dictionaries +
  access-tier dispositions; the candidate catalog in this plan is the seed.
- **`oncology-glossary-multilingual`** — the gated M3 plain-language glossary, translated; reuses the
  translation deliverable type and the "not medical advice" gating.
- **Schema-conformance validator as a reusable package** — re-check any open dataset against its
  documented schema; useful far beyond cancer.

---

## 8. Open questions for the maintainer

1. Will you add **OMOP CDM** as a crosswalk target, given harmonization is the moat? If not, what is
   the cross-dataset semantic anchor?
2. For GDC, do you **reproduce** the official caDSR-CDE/NCIt mappings (cite + verify) or attempt
   independent mapping? How is credit/scope framed to avoid "reinventing the official dictionary"?
3. What is the **definition-prose copyright** policy for WHO ICD-O-3 / SEER manual text —
   paraphrase-only, or short attributed quotes with a length cap?
4. Is **Croissant** worth the cost for clinical tables, or should the second emit target be the
   source's **native format** (GDC YAML) to enable native upstream PRs?
5. Should the **cross-dataset field-equivalence table** be promoted to a top-line M1/M2 deliverable
   with its own metric, rather than emerging implicitly?
6. Is **CCDH (cancerdhc)** a prior-art collaborator or a partner channel for adoption?
7. Concretely, what is the **drift-detection mechanism** and at which milestone does it land?

---

### Summary (for the maintainer)

This is a strong, safety-forward plan; the gate-first, provenance-as-hard-constraint, and
canonical-model design are ahead of the field. The competitive picture is clear: every incumbent is
either authoritative-but-siloed/technical or a content-free format. **Top 3 competitors:** the
official **GDC Data Dictionary** (open YAML, already CDE/NCIt-linked), **NCI caDSR / CDE Browser**
(the standards registry GDC draws from), and **OHDSI OMOP + ATHENA** (the dominant free
cross-source harmonization vocabulary) — with SEER manuals, cBioPortal docs, NCIt, Frictionless and
Croissant as the adjacent layer. None ships a plain-language, cross-dataset, machine-readable,
provenance-checked field dictionary, so the seam is genuine.

**Single strongest differentiator:** the **harmonized cross-dataset field-equivalence table**
(the same clinical concept mapped across GDC/cBioPortal/SEER/GEO to shared vocabularies) — something
no incumbent provides and the direct enabler of reproducible secondary analysis. It should be a
named deliverable, not a byproduct. This pairs cleanly with the sibling `cancer-dataset-datasheets`
(dataset-level cover page) since dictionaries are strictly field-level.

**Top 3 Claude-API ideas:** (1) draft plain-language field definitions from official docs *with
citations and gap-flagging*; (2) propose field→NCIt/CDE/OMOP mappings as *candidates* (verifying
GDC's existing official links rather than inventing); (3) harmonize equivalent fields across datasets
into the crosswalk table. In all three Claude drafts and proposes; provenance, license/access-tier,
and mapping sign-off stay human — the validator, not the model, enforces 100% sourcing.

**Two most important plan-correctness findings:** (1) the plan under-reuses official machine-readable
crosswalks — for GDC, caDSR CDE Public IDs and NCIt codes already exist in open YAML, so "map fields
to NCIt/CDE" is largely harvest-and-cite, and the plan should say so (and add OMOP, which is missing
from crosswalk targets); (2) source-*documentation* copyright (verbatim WHO ICD-O-3 / SEER manual
prose) is a license risk distinct from the dataset license and is under-addressed — require
paraphrase-with-citation plus a verbatim-copy lint. Secondary: lossy emit formats (value-level
provenance lives only in canonical JSON) and too-late (M3) drift detection both warrant fixes.
