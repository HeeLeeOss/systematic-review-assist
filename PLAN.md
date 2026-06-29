# Systematic-Review-Assist — PLAN.md

> Status: Draft · Version: 0.1.0 · Last updated: 2026-06-28 · Owner: TBD (maintainer) · Lane: donated
> Project slug: `systematic-review-assist` · Track: Cancer research (8b) · Risk tier: **MEDIUM**
> (tooling) with a **HIGH** sub-surface for any patient-facing output.

Open, PRISMA-aware tooling that helps human teams **screen** studies and **extract** data for cancer
systematic reviews, with the AI acting strictly as an assistive **second pair of eyes** — never as an
autonomous decider. Every AI suggestion is recall-first, provenance-linked back to the exact source
text, and confirmed by a human reviewer before it counts. The project is bound by the Elyos **cancer
guardrails**: only open-access / aggregate / de-identified data; per-source license verification;
no medical advice; provenance on every assertion.

---

## Executive summary

A systematic review is the backbone of evidence-based oncology: it defines a clinical question,
searches the literature exhaustively, screens thousands of records down to the eligible studies,
extracts their data in a structured way, assesses their risk of bias, and synthesises the result.
Done to PRISMA 2020 standard it is slow, labour-intensive, and expensive — a single review routinely
takes a small team 6–18 months and tens of thousands of screening decisions. Under-resourced groups
who most need evidence synthesis — **rare-cancer researchers (e.g. sarcoma, paediatric oncology),
patient-advocacy organisations, and clinicians in low-resource settings** — are precisely the ones
who cannot afford that cost, so reviews in those areas are scarcer and go stale faster.

Systematic-Review-Assist is open-source tooling that compresses the two most labour-intensive,
error-prone stages — **title/abstract & full-text screening** and **structured data extraction** —
while *increasing*, not decreasing, methodological rigour. The AI proposes; a human disposes. The AI
operates as a **recall-first second screener** (it never auto-excludes a study) and a
**span-grounded extractor** (it never asserts a data value without a verbatim source location a human
can verify). The output is PRISMA-2020-compliant artefacts — a populated screening log, an extraction
dataset with provenance, risk-of-bias evidence, and an auto-generated PRISMA flow diagram — that a
review team can defend, reproduce, and publish.

The single most important design fact is the **inversion of the usual AI-tooling default**. Most
"AI for literature review" tools optimise for *throughput* and let the model make the call. In a
systematic review the catastrophic error is the opposite of a chatbot's: **wrongly excluding a
relevant study silently biases the entire body of evidence and can change clinical conclusions.** So
the tool is engineered to **fail toward inclusion and toward human review**, not toward automation.
Two hard product requirements are built **first**, before any feature: (1) the **human-in-the-loop +
recall-first methodology contract** (no autonomous include/exclude or extraction; mandatory human
adjudication; recall-prioritised assist) and (2) the **provenance + source-rights gate** (every
assertion linked to a verbatim source span; every source's licence verified before ingest or
redistribution). These are tested subsystems and release gates, not disclaimers.

This is a **MEDIUM** risk-tier project on its core surface (research tooling whose value depends on
domain accuracy and methodological correctness). It carries a **HIGH** risk sub-surface: any
**patient-facing plain-language output** (e.g. a plain-language summary of a completed review).
Patient-facing content is education only, must carry a persistent **"not medical advice"** notice,
and **must not ship without blocking sign-off from a credentialed oncologist *and* a patient
advocate**. That HIGH-tier capability is sequenced last, is optional, and is gated behind a
non-bypassable review gate.

Honesty note: **no review team, partner, or requestor is yet secured.** Every delivery-dependent task
is marked `TO BE SECURED` with `verifiedNeed: false` until a real cancer systematic review team
adopts the tool to do a real review. The project is not "shipped" on merge; it is shipped only when a
real team completes or materially accelerates a real cancer systematic review with it, with the
methodology, provenance, and licence controls independently verified.

**Partner-acquisition plan (dated) and build-vs-mothball/pivot decision rule.** Rather than an
open-ended `TBD`: by **2026-08-31** a candidate review team and review topic are identified and ≥ 2
partners (a rare-cancer foundation's research arm, a Cochrane-style review group, an academic
evidence-synthesis unit, or a patient-advocacy research program) are in active conversation; by
**2026-10-31** a methodologist reviewer (information specialist / systematic-review methodologist) is
secured (gates M2 acceptance); by **2026-12-31** a pilot review team and a real review topic are
secured (gates M5). **Decision rule:** if no methodologist is secured by the M2 entry date, M2 ships
as **advisory-only assist** (no recall claims made) and the eval gate holds. If no pilot team is
secured by **~2027-03-31**, the project does **not** ship to no one: it either (a) **pivots** the
last-mile beneficiary — release the tool plus a fully worked, openly-licensed *reference review* on a
rare cancer (built on PMC-OA literature) as a teaching/methods artefact — or (b) **mothballs** to
maintenance-only, with the decision recorded in governance.

---

## Problem & beneficiaries

**Who is helped (directly):** human teams who conduct or contribute to cancer systematic reviews and
evidence syntheses, especially under-resourced ones:

- **Rare- and paediatric-cancer researchers** (e.g. Ewing's sarcoma, other sarcomas, paediatric
  oncology) where small communities must still meet full PRISMA rigour with little staff.
- **Patient-advocacy organisations** running or commissioning evidence syntheses to inform patients
  and guideline panels.
- **Clinicians and methodologists in low-resource settings** who cannot license expensive commercial
  review platforms or screening services.
- **Volunteer review groups** (Cochrane-style) and graduate students learning to do reviews
  correctly.

**Who is helped (ultimately):** **patients and clinicians**, who benefit from faster, more current,
more reproducible, and more transparent evidence synthesis in cancer — and from reviews getting done
*at all* in neglected areas.

**The need.** Screening (title/abstract then full text) and data extraction are the dominant cost of
a systematic review: a review may begin with 3,000–30,000 deduplicated records, each independently
screened by two humans, with conflicts adjudicated, before extraction of dozens of structured fields
per included study. The work is repetitive, attention-fatiguing, and a known source of error — and
the error that matters most, a **missed eligible study (false exclusion)**, is silent and biases the
synthesis. Existing commercial tools that semi-automate screening are often closed, costly, or make
opaque automated exclusion decisions teams cannot audit or reproduce. An open, auditable,
recall-first, provenance-grounded tool that keeps the human firmly in the loop would lower the cost of
rigorous synthesis for exactly the groups priced out of it — **without** the methodological
corner-cutting that has given "AI screening" a deservedly cautious reputation.

**Verified need / partner:** **TO BE SECURED.** No specific review team, methodologist reviewer, or
sponsoring organisation has yet agreed to adopt or co-develop the tool. Target partner profiles:
a rare-cancer foundation's research program; an academic evidence-synthesis / HTA unit; a
Cochrane-affiliated cancer review group; a university library's systematic-review service; or a
patient-advocacy organisation that commissions reviews. Until one is secured, the project builds the
agent-neutral tooling, the methodology + rights gates, and a *reference review* on open-access
literature for validation, and marks all delivery/adoption work `TO BE SECURED`. Outreach is **dated,
not open-ended** (see the partner-acquisition plan in the executive summary), with a documented
build-vs-mothball/pivot decision rule if dates slip.

---

## Goals and non-goals

**Goals**

- Build open-source, agent-neutral, PRISMA-2020-aware tooling that **assists** screening and
  extraction for cancer systematic reviews while keeping the human reviewer the sole decider.
- Ship the AI screening assist as a **recall-first second screener** that *never auto-excludes* and is
  evaluated against a human gold standard on **recall (sensitivity)** and workload saved.
- Ship the AI extraction assist as a **span-grounded** extractor where every proposed value links to a
  verbatim source location and **no value is recorded without human verification**.
- Enforce a **source-rights gate** so only correctly-licensed, open-access / aggregate / de-identified
  material is ingested, processed, and (where redistributed) re-shared — with provenance on everything.
- Produce reproducible, auditable PRISMA artefacts: screening log, extraction dataset with provenance,
  risk-of-bias evidence, and an auto-generated PRISMA 2020 flow diagram.
- Prove, via an eval harness against a labelled gold-standard review, that the assist achieves high
  recall and meaningful workload reduction *without* increasing false exclusions.
- Reach a real outcome: a partner team completes or materially accelerates a real cancer systematic
  review with the tool, with methodology and licence controls independently verified.

**Non-goals**

- **Not an autonomous reviewer.** It never finalises an include/exclude decision, never records an
  extraction value, and never authors a synthesis conclusion without a human.
- **Not a meta-analysis / statistics engine.** It assists screening and extraction; statistical
  synthesis is left to established tools (R `metafor`, RevMan, etc.), with clean data hand-off.
- **Not a literature search engine.** It ingests records exported from databases (PubMed/MEDLINE,
  Embase, Cochrane CENTRAL, etc.); it does not replace the information specialist's documented search.
- **Not a medical-advice or clinical-decision tool.** No diagnosis, treatment, prognosis, or patient
  guidance. Any patient-facing plain-language output is education only, "not medical advice", and
  oncologist + advocate sign-off-gated (HIGH tier).
- **Not a user of controlled-access or identifiable patient data.** No dbGaP/EGA/individual-level
  biobank data, no identifiable patient information; individual-patient-data (IPD) meta-analysis is
  out of scope at launch (it requires governed access + de-identification verification).
- **Not a closed or for-profit-first product.** Open-source code, open outputs, no primary benefit to
  a for-profit entity.
- **Not a replacement for dual human screening where a standard requires it** — it can *be* one of the
  two screeners only where the team's protocol and a methodologist explicitly permit AI-as-second-
  screener with human adjudication; otherwise it is an *assist on top of* two human screeners.

---

## Success metrics (outcomes)

Outcome-centric and methodology-first. Throughput alone is explicitly **not** success — a fast tool
that lowers recall is a failure.

| Metric | Baseline | Target (pilot) | How measured |
|---|---|---|---|
| Screening **recall / sensitivity** vs. human gold standard (the safety metric) | n/a | **≥ 95%** at title/abstract on a labelled gold-standard review, with a 95% CI reported; **0 confirmed eligible studies lost** to an AI action without human review | Eval harness vs. labelled corpus; per-version report |
| Workload saved at fixed recall (**WSS@95**) | 0 | report % screening burden reduced while holding recall ≥ 95% | Eval harness (WSS@95 standard metric) |
| Autonomous-decision rate (AI finalising include/exclude or extraction) | n/a | **0** — structurally impossible; every disposition has a human actor in the audit log | Audit-log invariant test in CI |
| Extraction **provenance coverage** (AI assertions linked to a verbatim source span) | n/a | **100%** — no value surfaces without a source location | Provenance-coverage test ("no span, no value") |
| Extraction first-pass field accuracy vs. human (pre-verification) | n/a | reported per field type; **final** accuracy is 100% (human verifies every field) | Eval vs. gold-standard extraction sheet |
| Source-licence verification before ingest/redistribution | n/a | **100%** of ingested/redistributed sources carry a recorded, verified licence; 0 non-compliant ingests | Rights-gate enforcement + audit |
| Reproducibility of a completed review | n/a | a review's screening + extraction is **re-derivable** from the audit log + model/version manifest | Reproducibility test on the reference review |
| PRISMA 2020 conformance of generated artefacts | n/a | flow-diagram counts reconcile to the logs; report scaffold covers PRISMA 2020 items | Conformance check + methodologist review |
| Patient-facing output (only if shipped) | n/a | **100%** carry "not medical advice" + recorded **oncologist *and* advocate** sign-off; 0 unreviewed claims served | Governance log + staleness/sign-off test |

The **defining success outcome** (Definition of Shipped): a real cancer systematic review team uses
the tool to **complete or materially accelerate a real review**, with measured recall ≥ target, full
provenance and licence compliance, PRISMA-conformant artefacts, and an independent methodologist's
confirmation that rigour was maintained — not merely "records screened faster."

---

## Scope

**In scope**

- A PRISMA-2020-aware **protocol / PICO model**: research question, eligibility (inclusion/exclusion)
  criteria as structured, machine-readable rules, and a link to a registered protocol (e.g. PROSPERO).
- **Ingest & deduplication** of bibliographic records from standard exports (RIS, BibTeX, PubMed/
  MEDLINE XML, Embase, Cochrane CENTRAL, CSV) with a transparent, auditable dedup step.
- **AI screening assist** (recall-first, human-in-the-loop): per-record suggested disposition with the
  **specific eligibility criterion**, a **rationale**, a **confidence**, and the **source span** it
  relied on — surfaced in a dual-reviewer + conflict-adjudication workflow; **never auto-excluding**.
- **AI extraction assist** (span-grounded): structured extraction of pre-defined fields (study design,
  population, interventions, outcomes, effect estimates, etc.) into a typed schema, each value linked
  to a verbatim location in the source, **requiring human verification** before it is recorded.
- **Risk-of-bias assist**: surfaces the verbatim evidence relevant to RoB items (RoB 2 / ROBINS-I /
  QUADAS-2 as applicable) for the **human** to judge — it does not assign the rating.
- **Provenance + audit layer**: every assertion (screening disposition, extracted value, RoB evidence)
  carries source, source span, model + version, reviewer, and timestamp; full reproducibility manifest.
- **Source-rights gate**: per-source licence verification; only open-access / aggregate / de-identified
  material ingested; redistribution only of openly-licensed content; closed full text the team has
  lawful access to is processed **locally** and **not redistributed**.
- **PRISMA artefacts**: auto-generated PRISMA 2020 flow diagram counts, screening log export, an
  extraction dataset (CSV/JSON) with provenance, and a report scaffold.
- **Eval harness** against a labelled gold-standard review (recall, WSS@95, extraction accuracy).
- A worked, openly-licensed **reference review** on open-access cancer literature for validation/teaching.

**Out of scope (explicitly will NOT do)**

- **Autonomous exclusion or autonomous extraction.** No AI-only include/exclude; no recorded value
  without human verification; no AI-authored synthesis conclusions.
- **The literature search itself** (the documented database search is the information specialist's job)
  and **statistical meta-analysis** (hand off clean data to `metafor`/RevMan/etc.).
- **Controlled-access data** (dbGaP, EGA, individual-level biobanks) and **any identifiable patient
  data**; **IPD meta-analysis** at launch.
- **Ingesting closed-access full text for redistribution**, or any text/data-mining that violates a
  publisher's TDM terms or a source's licence.
- **Medical advice / clinical decision support** of any kind; treatment, prognosis, or eligibility
  recommendations to patients or clinicians.
- **Non-cancer scope at launch** (the methodology generalises, but validation and guardrails are
  cancer-first); breadth later, never without re-validation.

When a request or input falls in the out-of-scope/refused set (e.g. a closed-access PDF the licence
does not permit ingesting, a request to "just decide the excludes for me", or an input containing
identifiable patient data), the tool refuses that part, explains why in methodology/licence terms, and
where possible offers the compliant alternative (e.g. "process this locally without redistribution",
"flag for the human screener", "this record contains apparent PII — quarantined for review").

---

## Solution approach & architecture

**Stack.** TypeScript, ESM, pnpm workspaces (mirrors Elyos conventions). Core delivered as a
**library + CLI** (`@elyos/sra-core`, `@elyos/sra-cli`) so it runs locally on a reviewer's machine
without sending closed full text to a server; an **optional local web UI** (the screening/extraction
review surface) runs against the local store. Persistence: a portable, file-based project store
(SQLite via a thin data layer; JSON/CSV exports) so a review is a self-contained, version-controllable
artefact — no required cloud database, important for both privacy and low-resource use. Reasoning:
Anthropic Claude behind a **provider-neutral LLM client** (`adapters/llm`, Claude first; model
selection/pricing per the Claude API skill) so the agent-neutral core has **no vendor-specific
logic** (Elyos core/adapter rule). Code licence **Apache-2.0** (patent grant; see *Data, licensing*);
content/datasets **CC-BY-4.0**.

**Components**

1. **Methodology & safety policy layer (`core/policy`) — built first.** The contract every other
   component implements and is tested against. It encodes the non-negotiables: (a) **no autonomous
   include/exclude or extraction** — every disposition requires a human actor recorded in the audit
   log; (b) **recall-first** screening behaviour — the assist is tuned and evaluated so that the
   default failure mode is "send to human / lean include", never silent exclude; (c) **no span, no
   value** — an extracted value or a screening rationale that cannot cite a verbatim source location is
   rejected; (d) the **source-rights gate** contract; (e) the **PII / identifiable-data quarantine**
   rule; (f) the **"not medical advice"** + oncologist/advocate gate for patient-facing output. This
   layer is enforced in code (not prose) and ships with its own test suite that fails the build on
   violation.

2. **Protocol & eligibility model (`core/protocol`).** PICO(S) question, structured inclusion/
   exclusion criteria as machine-readable rules, registered-protocol link (PROSPERO id), outcome
   definitions, and the extraction field schema for the review. Drives both screening rationales
   (which criterion) and extraction (which fields).

3. **Ingest & deduplication (`core/ingest`).** Parsers for RIS, BibTeX, PubMed/MEDLINE XML, Embase,
   Cochrane CENTRAL, and CSV; normalisation; transparent deduplication (DOI/PMID exact + fuzzy
   title/author/year) with every merge logged and reversible. The **rights gate runs here**: each
   source is classified (open-access full text / abstract-only / closed) and its licence recorded
   before any content is stored or sent to the model.

4. **Screening assist (`core/screening`).** For each record, the LLM (behind the neutral client) is
   given the eligibility criteria + the record's title/abstract (or full text where licence permits)
   and returns a **structured suggestion**: proposed disposition, the **specific criterion**, a
   rationale, a **calibrated confidence**, and the **source span** quoted. It is presented in a
   dual-reviewer workflow (two screeners — human+human, or human+AI-assist where the protocol and a
   methodologist permit) with **conflict adjudication by a human**. The assist **cannot exclude**: at
   title/abstract it can only *rank/triage* and *propose*; at full text every exclusion is
   human-confirmed with a recorded reason. Recall-first defaults: low-confidence / ambiguous →
   escalate to human as *potentially include*.

5. **Extraction assist (`core/extraction`).** Given an included study's (licence-permitted) full text
   and the field schema, proposes typed values **each bound to a verbatim source span and location**.
   Values land in a **"proposed, unverified"** state; a human verifies/edits each before it is
   committed to the extraction dataset. No span → the field is left for manual entry, never
   auto-filled. Numeric/unit fields are checked for plausibility but never invented.

6. **Risk-of-bias assist (`core/rob`).** Surfaces the verbatim text relevant to each RoB item for the
   selected instrument; the **human assigns** the judgement. The tool records the supporting quote and
   the human's rating + reasoning.

7. **Provenance & audit layer (`core/provenance`).** Append-only audit log: every assertion records
   source, span/location, model id + version + prompt hash, the human actor, action, and timestamp.
   Produces a **reproducibility manifest** so a completed review's AI-assisted steps are re-derivable.

8. **PRISMA & reporting (`core/prisma`).** Reconciles counts across identification → screening →
   eligibility → included, emits a **PRISMA 2020 flow diagram** and a report scaffold; exports the
   screening log and extraction dataset (CSV/JSON) for hand-off to statistical tools.

9. **LLM client (`adapters/llm`).** Thin provider-neutral interface; Claude first (cheaper/faster
   model for triage/extraction passes, stronger model for ambiguous adjudication-support), model
   tiering and pricing per the Claude API skill. No core module imports a vendor SDK directly.

10. **Eval harness (`scripts/eval.ts`).** Runs screening against a labelled gold-standard review and
    reports recall, precision, and **WSS@95**; runs extraction against a gold-standard sheet and
    reports per-field accuracy; both gate releases.

11. **(HIGH tier, optional, late) Plain-language summary (`core/pls`).** Generates an education-only,
    plain-language summary of a *completed, published* review, derived only from the review's own cited
    findings. Ships **only** behind a blocking **oncologist + patient-advocate** review gate, with a
    persistent **"not medical advice"** notice and provenance to the review.

**Key decisions**

- The methodology + provenance + rights gates are first-class, tested subsystems and **release gates**.
- **Recall-first, human-in-the-loop is structural**, not a setting: the data model has no "AI
  finalised" disposition state; every disposition row requires a human actor.
- **Local-first** execution and a portable project store so closed full text never leaves the
  reviewer's machine and a review is a reproducible, ownable artefact.
- Agent-neutral core; all Anthropic/Claude specifics behind the LLM client (Elyos core/adapter rule).
- Cancer-first validation; patient-facing output is a separate, hard-gated, late, optional capability.

---

## Data, licensing & compliance

**Cancer guardrails (binding — these lead).** This project handles published cancer literature and
references the datasets cancer reviews cite. The Elyos cancer guardrails are absolute:

- **Open-access / aggregate / de-identified data ONLY.** **Controlled-access sources (dbGaP, EGA,
  individual-level biobanks) and ANY identifiable patient data are OUT OF SCOPE** — they require
  authorised access + IRB and are never ingested by this tool. **IPD meta-analysis is out of scope at
  launch.**
- **Per-source licence verification is mandatory before ingest, processing, or redistribution.** The
  rights gate (component 3) classifies and records each source's licence and refuses non-compliant
  ingest. Reference points for datasets a review may cite: **TCGA, GEO, PRIDE are open**; **COSMIC and
  OncoKB are non-commercial** (their terms must be honoured and they are never redistributed under an
  open licence); abstract/metadata vs. full-text reuse differs by publisher.
- **No medical advice.** Patient-facing output is **education only**, carries a persistent **"not
  medical advice"** notice, and requires **oncologist *and* patient-advocate** sign-off (HIGH tier,
  blocking gate). The research tooling itself is for reviewers, not patients.
- **Provenance on every assertion.** No screening rationale, extracted value, or RoB evidence exists
  without a verbatim source span and a recorded source.

**Source material & its licences (specific, conservative).**

- **Bibliographic metadata** (titles, authors, journals, identifiers): PubMed/MEDLINE metadata is
  broadly reusable under NLM terms; record provenance and honour NLM's terms (no implication of NLM
  endorsement). Database-specific exports (Embase, Web of Science) carry **subscriber terms** — the
  tool ingests them **locally for the team's own review** and **does not redistribute** licensed
  records.
- **Abstracts**: copyright varies by publisher; many abstracts are **not** openly licensed. Abstracts
  are processed **locally** for screening and are **not redistributed**; only counts/decisions and
  openly-licensed text may appear in shared outputs.
- **Full text**: only the **PMC Open Access subset** and other clearly open-licensed (CC-BY /
  CC-BY-NC / etc.) full text may be ingested for processing **and** quoted/redistributed within
  licence terms (preserve the specific CC variant; CC-BY-NC content is **not** relicensed under a
  permissive licence). Closed full text the team has lawful subscription access to may be processed
  **locally** but **never redistributed** and never sent anywhere the licence/TDM terms forbid.
- **Text-and-data-mining (TDM) compliance**: ingestion respects each source's TDM/robots/licence
  terms; the rights gate blocks sources whose terms do not permit machine processing for this use.
- **Referenced cancer datasets** (when a review extracts from them): honour each dataset's licence —
  TCGA/GEO/PRIDE open (with attribution/provenance); COSMIC/OncoKB **non-commercial, not
  redistributed**.

**Provenance model.** Each assertion is backed by a `Provenance` record: source id, citation (DOI/
PMID), retrieval date, **verbatim span + character/section location**, licence/legal-status note,
model id + version + prompt hash (for AI-proposed items), human actor + decision, and timestamp.
Screening and extraction surfaces may not display or commit an assertion without it (enforced by a
coverage test — "no provenance, no assertion").

**Privacy / PII stance (conservative).** Systematic reviews work over *published* literature, so the
default PII footprint is low — but **case reports and some supplementary materials can contain
identifiable patient details**. The ingest pipeline runs a **PII / identifiable-data scan**; a record
that appears to contain identifiable patient information (names, MRNs, dates+locale, identifiable
images) is **quarantined** and surfaced to a human rather than sent to the model or stored in shared
outputs. No patient data is ever used for any purpose beyond the review, and **no controlled-access or
individual-level data is ingested at all**. No secrets, tokens, or PII are written to logs, receipts,
or committed files (Elyos rule); the audit log records *that* an action happened and *which source*,
not sensitive content.

**Output licensing.** Code: **Apache-2.0** (permissive + explicit patent grant, good for a research
commons). Generated datasets / extraction outputs and the reference review: **CC-BY-4.0**, with
upstream source attribution preserved and **CC-BY-NC source content kept non-commercial** (never
relicensed). Docs: **CC-BY-4.0**. The tool emits a per-output **licence + attribution manifest** so a
team can see exactly what may be shared and under what terms.

**Attribution.** All shared outputs cite their primary sources; redistribution preserves attribution
per the source licence. Expert/methodologist reviewers are credited (with consent) in a reviewers
ledger, scoped to the versions they reviewed.

---

## Quality, review & risk gates

**Risk tier: MEDIUM** on the core tooling surface — its value depends on **domain accuracy and
methodological correctness**, so it needs reviewers with systematic-review / oncology-evidence skill
(per `docs/good-deed-definition.md`, medium = needs domain accuracy). **HIGH** on the **patient-facing
sub-surface**: any plain-language / patient-education output is a health-information surface and
**requires credentialed oncologist *and* patient-advocate sign-off before it ships** (blocking gate).
Pure infra/parsing/UI tasks are low–medium.

**Required reviews before a deed is "done":**

- **Maintainer** review on all PRs (engineering quality, agent-neutral core, no secrets/PII in logs,
  tests/CI green).
- **Methodologist review** (systematic-review methodologist / information specialist) — **TO BE
  SECURED** — confirms screening/extraction behaviour, recall claims, and PRISMA conformance are
  methodologically sound before those features are trusted/published. Recall and WSS@95 claims are not
  asserted publicly without this sign-off.
- **Oncology-evidence reviewer** for cancer-domain accuracy of the reference review and any
  domain-specific extraction logic.
- **HIGH-tier gate — oncologist *and* patient-advocate sign-off (blocking)** for any patient-facing
  plain-language output, plus a persistent "not medical advice" notice. **No dual sign-off, no ship**
  — this gate cannot be bypassed by a maintainer.
- **Methodology/safety review** — the policy-layer test suite (no-autonomy invariant, recall-first,
  no-span-no-value, rights gate, PII quarantine) passes in CI; a regression that lets the AI act
  autonomously or surface an unsourced assertion **fails the build**.

Every screening/extraction surface is labelled **"AI-assisted, human-verified — not a substitute for
methodological judgement"**; any patient-facing surface carries **"not medical advice."**

**Definition of Shipped (project):** a real cancer systematic review team uses the tool to complete or
materially accelerate a real review, with measured recall ≥ target and 0 AI-only exclusions,
full provenance + licence compliance, PRISMA-conformant artefacts, an independent methodologist's
confirmation that rigour was maintained, and the outcome recorded. (Any patient-facing output additionally
requires recorded oncologist + advocate sign-off.)

---

## Roadmap & milestones

Phased: methodology/provenance/rights gates and skeleton first; assist features only behind those
gates and behind the eval; pilot adoption last and gated on a secured partner. The HIGH-tier
patient-facing capability is last and optional.

- **M0 — Foundations, methodology contract & rights gate (cold-start).**
  *Goal:* the safety/methodology subsystem and an agent-neutral, local-first skeleton exist before any
  feature. *Exit:* methodology & safety policy spec + policy layer merged with its test suite passing
  in CI (no-autonomy invariant, recall-first contract, no-span-no-value, source-rights gate, PII
  quarantine); pnpm/TS/ESM monorepo + green CI; portable project store + provenance/audit data model;
  PRISMA-aware protocol/eligibility schema; "AI-assisted, human-verified / not medical advice" framing
  wired in. **Candidate review team + topic identified** (gates M5 sequencing). 

- **M1 — Ingest, dedup & the human screening workspace.**
  *Goal:* records flow in cleanly and humans can screen with a full audit trail — *before* any AI
  suggestion exists. *Exit:* RIS/PubMed-XML/CSV (+ at least one of Embase/CENTRAL) import; transparent,
  reversible dedup; dual-reviewer + conflict-adjudication screening workflow with append-only audit;
  rights gate classifies + records each source's licence at ingest; a **labelled gold-standard review
  fixture** assembled (from open-access literature) for later eval. No AI decisions anywhere yet.

- **M2 — AI screening assist (recall-first, human-in-the-loop) + screening eval (kill-gate).**
  *Goal:* the assist proposes; humans decide; recall is measured. *Exit:* per-record structured
  suggestion (disposition + criterion + rationale + confidence + source span) inside the dual-reviewer
  workflow; **AI cannot auto-exclude** (enforced + tested); eval harness reports **recall + WSS@95** on
  the gold standard. **Kill-gate:** if recall < 95% (or false exclusions occur via any AI action), the
  assist ships **advisory/triage-only** (ranking, no disposition claims) and recall claims are withheld
  until the bar is met — HIGH/medium-tier reliance does not proceed on an unproven recall.

- **M3 — AI extraction assist (span-grounded) + risk-of-bias assist.**
  *Goal:* structured, provenance-bound extraction and RoB evidence surfacing, all human-verified.
  *Exit:* extraction proposes typed values each bound to a verbatim span; values are "proposed,
  unverified" until a human commits them ("no span, no value" enforced + tested); RoB assist surfaces
  evidence for the chosen instrument with the human assigning ratings; extraction accuracy reported vs.
  a gold-standard sheet.

- **M4 — PRISMA reporting, reproducibility & hardening.**
  *Goal:* defensible, reproducible, accessible outputs. *Exit:* PRISMA 2020 flow-diagram + report
  scaffold with reconciled counts; screening-log + extraction-dataset export with licence/attribution
  manifest; reproducibility manifest re-derives the AI-assisted steps; accessibility (WCAG 2.2 AA on
  the local UI) + expanded eval green; the worked open-access **reference review** completed and
  methodologist-reviewed.

- **M5 — Pilot with a real review team (the deed).**
  *Goal:* a real team benefits on a real review. *Exit (Definition of Shipped):* a secured cancer
  review team uses the tool to complete or materially accelerate a real review; recall ≥ target with 0
  AI-only exclusions; provenance + licence compliance verified; PRISMA artefacts produced; independent
  methodologist confirms rigour maintained; outcome recorded. *(Gated on a secured partner — TO BE
  SECURED; build-vs-mothball/pivot rule applies if no pilot by ~2027-03-31.)*

- **M6 — Sustain, scale & (optional, HIGH-tier) patient-facing summaries.**
  *Goal:* durable maintenance and, only if a partner needs it, hard-gated patient-facing output.
  *Exit:* maintenance rotation + ops runbook + outcomes tracking; documented, re-validated process for
  a second review topic/cancer; **optionally** the plain-language-summary capability shipped **only**
  behind the blocking oncologist + advocate gate with "not medical advice" framing.

Dependencies flow M0 → M1 → M2 → M3 → M4 → M5; the **candidate-team decision (M0) gates M5**; M2's
recall kill-gate gates trustworthy reliance in M3–M5; the patient-facing capability (M6) is gated on
the dual oncologist/advocate sign-off and is never on the critical path.

---

## Work breakdown

The itemized, schema-mapped backlog lives in **`TASKS.md`**: ~17 tasks across milestones M0–M6 plus a
future backlog, each mapped to the Elyos Task JSON schema, with per-task acceptance criteria for the
most important items, milestone Definitions of Done, and a complete example Task JSON for the first M0
task (the methodology & safety policy specification). The **first build item** is that policy spec —
reflecting its status as the hard product requirement — followed by the local-first skeleton and the
rights/provenance data model; an early **gold-standard fixture** (M1) and a **screening-recall
kill-gate** (M2) are sequenced so feature reliance is gated on proven recall, not assumed.

---

## Governance, roles & stakeholders

- **Maintainer (Owner): TBD.** Owns architecture, the agent-neutral core, CI, and merge quality.
- **Reviewers / rotation:** at least one engineering reviewer plus a designated **methodology/safety
  reviewer** who audits the no-autonomy / recall-first / no-span-no-value invariants independently of
  feature authors.
- **Methodologist reviewer (MEDIUM-tier domain gate): TO BE SECURED** — a systematic-review
  methodologist / information specialist. Confirms screening/extraction behaviour, recall/WSS@95
  claims, and PRISMA conformance are sound. **Version-scoped** sign-off (attaches to a specific
  version + eval run; does not carry forward to later changes). Recall claims are not published without
  it.
- **Oncology-evidence reviewer (MEDIUM-tier domain gate): TO BE SECURED** — for cancer-domain accuracy
  of the reference review and domain extraction logic.
- **HIGH-tier expert gate (patient-facing output): TO BE SECURED** — a **credentialed oncologist** and
  a **patient advocate**, both required, sign off any plain-language/patient-facing output before it
  ships; this is a **blocking veto** a maintainer cannot override on substance. Name-use is
  version-scoped and consented; reviewers credited in the reviewers ledger only for what they approved.
- **Steward (last-mile owner): TO BE SECURED** — owns the pilot review-team relationship and the
  adoption/handoff that constitutes shipping.
- **Partner / requestor: TO BE SECURED** — the pilot review team / sponsoring organisation (rare-cancer
  foundation research arm, evidence-synthesis/HTA unit, Cochrane-style group, advocacy research program).
- **Community / board:** licence choices and edge-cases (e.g. AI-as-second-screener acceptability) go
  through Elyos governance. **Disagreement fallback:** on substance, the relevant expert (methodologist
  for rigour; oncologist+advocate for patient-facing) holds the veto; disagreements are logged and
  escalated to Elyos governance / a second reviewer for a tie-break.

---

## Dependencies & integrations

- **External services:** Anthropic Claude API (reasoning, behind the neutral LLM client; model
  selection/pricing per the Claude API skill). Optional and local-first — closed full text is processed
  locally and only licence-permitted text is sent to the model.
- **Datasets / sources:** PMC Open Access subset and other open-licensed full text; PubMed/MEDLINE
  metadata; database exports (RIS/BibTeX/PubMed-XML/Embase/CENTRAL/CSV) the team lawfully holds;
  referenced open cancer datasets (TCGA/GEO/PRIDE open; COSMIC/OncoKB non-commercial) — each with
  verified reuse terms and recorded provenance.
- **Standards / upstreams:** PRISMA 2020 statement + flow diagram; PROSPERO protocol registration;
  risk-of-bias instruments (RoB 2, ROBINS-I, QUADAS-2); WSS@95 screening-evaluation metric. Hand-off to
  statistical tools (R `metafor`, RevMan).
- **Elyos pieces:** `packages/schema` (Task JSON), `CLAUDE.md` work rules + refusal guardrails,
  `docs/good-deed-definition.md` (risk tiers), Elyos governance for licence/edge-case decisions.
- **Human/decision dependencies (critical path):** a secured **methodologist reviewer** (blocks public
  recall claims / M2 acceptance), a secured **pilot review team + topic** (blocks M5), and — only for
  the optional patient-facing capability — a secured **oncologist + patient advocate** (blocks M6 PLS).

---

## Risks & mitigations

| Risk | Likelihood | Impact | Mitigation | Owner |
|---|---|---|---|---|
| AI silently **excludes a relevant study** (false exclusion biases the review) | High | Critical | Recall-first design; **AI cannot auto-exclude** (no "AI-finalised" state; human-confirmed exclusions only); recall eval (≥95%) gates trust; low-confidence escalates to human as *potential include*; audit log proves a human acted on every exclusion | Methodology/safety reviewer |
| Reviewers **over-trust** the AI and rubber-stamp suggestions (automation bias) | High | High | UI shows criterion + span + confidence, not a bare verdict; conflict workflow; spot-check sampling of "verified" items in eval; methodologist review; metric is recall **maintained**, not just speed | Methodologist / Maintainer |
| **Hallucinated extraction values** | Medium | High | Span-grounded extraction; "no span, no value"; values are *proposed-unverified* until a human commits; plausibility checks; extraction-accuracy eval | Maintainer |
| **Licence / TDM violation** (ingesting or redistributing non-open content) | Medium | High | Rights gate verifies + records each source's licence before ingest; closed full text processed **locally only**, never redistributed; CC-BY-NC kept non-commercial; per-output licence/attribution manifest | Maintainer |
| **Controlled-access or identifiable patient data** enters the pipeline | Low | Critical | Hard out-of-scope; ingest **PII/identifiable-data quarantine**; no dbGaP/EGA/IPD; refuse + flag | Maintainer / Methodologist |
| **Patient-facing output gives de-facto medical advice** | Medium | Critical | Patient-facing is a separate, late, **optional** HIGH-tier capability; blocking **oncologist + advocate** sign-off; persistent "not medical advice"; education-only, derived only from a completed review's cited findings | Oncologist + advocate (veto) |
| **No pilot team secured** → cannot reach Definition of Shipped | High | High | Honest `TO BE SECURED`/`verifiedNeed:false`; **dated partner plan** (team+topic by 2026-08-31, methodologist by 2026-10-31, pilot by 2026-12-31) + **build-vs-mothball/pivot rule** (~2027-03-31: pivot to releasing the open reference review as a methods artefact, or mothball) | Steward / Maintainer |
| **No methodologist secured** → recall claims unvalidated | Medium | High | Ship assist **advisory/triage-only** without a methodologist; do not publish recall/WSS claims unsigned; recruit via SR networks, libraries, HTA units | Maintainer |
| **Dedup errors** drop or merge records wrongly | Medium | Medium | Transparent, reversible, fully-logged dedup; conservative fuzzy thresholds; human review of merges; counts reconcile in PRISMA flow | Maintainer |
| **Domain inaccuracy** in cancer extraction/RoB logic | Medium | High | Oncology-evidence reviewer; gold-standard validation; RoB ratings assigned by humans, AI only surfaces evidence | Oncology reviewer |
| **Eval gold standard is unrepresentative** (recall looks good but generalises poorly) | Medium | Medium | Build the gold standard from a real, diverse open-access review; report CIs; treat recall as version-scoped and re-validate per review topic | Methodologist |
| **Prompt injection via paper full text** steers screening/extraction | Medium | Medium | Treat source text as untrusted data, not instructions; server/library-side policy not overridable by content; injection cases in the eval/test suite | Maintainer |

---

## Security & privacy

**Threat surface.** Licence/TDM violation via ingest; controlled-access or identifiable patient data
entering the pipeline; over-trust leading to silent false exclusions; hallucinated/unsourced
assertions; prompt-injection embedded in paper full text attempting to steer the assist; leakage of
closed full text to a remote model/service; secrets leakage.

**Local-first, data-minimising design.** The core runs as a **local library + CLI** with a portable
project store, so a team's **closed full text never has to leave their machine**; only
licence-permitted text is sent to the model, and that boundary is enforced by the rights gate. A review
is a self-contained, ownable, version-controllable artefact.

**Controls.** Methodology & safety policy layer as a tested, build-gating subsystem (the top control):
no-autonomy invariant, recall-first contract, no-span-no-value, rights gate, PII/identifiable-data
quarantine. **Untrusted-content stance:** paper text is data, never instructions — the policy/prompt
construction is library-side and cannot be overridden by document content; injection cases are in the
test suite. Per-source licence verification before ingest; closed content processed locally only,
never redistributed. **PII/identifiable-data quarantine** at ingest; no controlled-access or
individual-level data at all. **No secrets, tokens, or PII in logs, receipts, or committed files**
(Elyos rule); the append-only audit log records action + source + actor + model/version, not sensitive
content. Dependency + secret scanning in CI. The funded lane is **not used** here (donated lane); if
ever used for batch screening it would carry a hard per-task budget cap (Elyos rule).

**Abuse/misuse prevention.** The refused set — autonomous exclusion/extraction, ingesting/
redistributing non-open content, controlled-access/identifiable data, medical-advice generation,
unreviewed patient-facing output — is enforced and tested, not merely documented. The tool never
finalises a methodological decision and never authors a clinical recommendation.

## Sustainability & maintenance

After delivery, a named **maintenance rotation** owns the tooling; the **steward** owns the pilot
relationship and outcome tracking. The eval harness, gold-standard fixtures, and policy-layer test
suite are maintained as **living tests** and expanded as new failure modes (injection vectors, dedup
edge cases, new export formats) are found. Source licences and the cancer-dataset reuse notes carry a
**review cadence** (terms change); recall is **version-scoped** and re-validated per new review topic
rather than assumed to transfer. Outcomes tracked are **methodological** (reviews completed/
accelerated, recall maintained, provenance/licence compliance, reproducibility) — **not** engagement
metrics. Expansion to a second cancer/topic or to non-cancer domains follows a documented,
re-validated, expert-gated process; the patient-facing capability, if live, re-runs the oncologist +
advocate gate on every material change.

## Open questions

- **Pilot review team + topic?** Profiles and a dated plan exist; the specific team/topic is **TO BE
  SECURED** (decision by 2026-08-31). A rare cancer (e.g. a sarcoma) is attractive — high need, scarcer
  reviews, tractable open-access corpus.
- **Is AI-as-one-of-two-screeners acceptable for the pilot's protocol,** or must the AI remain an
  *assist on top of* two human screeners? Decided with the methodologist + per the team's registered
  protocol; affects how recall claims may be used. (Default: assist-on-top until a methodologist
  approves AI-as-second-screener.)
- **Recall threshold & evidence:** is ≥ 95% the right bar for the pilot topic, and is the gold-standard
  review representative enough to support a published recall claim? Confirmed with the methodologist.
- **Code licence: Apache-2.0** (proposed, for patent grant) — confirm vs. MIT via Elyos governance.
- **How far to support closed full text locally** (e.g. local-only OCR/parsing) without ever creating
  a redistribution or TDM-terms risk?
- **Patient-facing summaries — build at all?** Optional and HIGH-tier; only if a partner needs it and
  the oncologist + advocate gate is secured.
- **Lane:** donated by default; is there any future funded lane for large batch-screening runs (with a
  hard per-task budget cap) without compromising the human-in-the-loop guarantee?

## References

- Proposal: `governance/proposals/systematic-review-assist.md` *(TO BE WRITTEN — not yet drafted)*
- Roadmap entry: `planning/ROADMAP.md` (Track 8b — `systematic-review-assist`, ⚪ medium)
- Elyos work rules & refusal guardrails: `CLAUDE.md`
- Good-deed definition & risk tiers: `docs/good-deed-definition.md`
- Task JSON schema: `packages/schema/src/schemas.ts`
- House-style sibling plans: `planning/projects/public-official-guide/{PLAN,TASKS}.md`,
  `planning/projects/open-data-datasheets/{PLAN,TASKS}.md`
- Methodology standards: PRISMA 2020 statement + flow diagram; PROSPERO protocol registry; RoB 2 /
  ROBINS-I / QUADAS-2 risk-of-bias instruments; WSS@95 screening-evaluation metric
- Source/licence references: PMC Open Access subset terms; NLM/PubMed data terms; Creative Commons
  licence variants; TCGA/GEO/PRIDE (open) and COSMIC/OncoKB (non-commercial) data-use terms

---

## Appendix A — Improvements applied

The following 25 specific improvements were identified during drafting and **applied** to this plan
and to `TASKS.md` (not left as suggestions):

1. **Reframed the central safety property as "false exclusion", not throughput.** Made recall/
   sensitivity the headline safety metric and stated the catastrophic-error model explicitly
   (*Executive summary*, *Success metrics*).
2. **Made "no autonomous exclusion" structural, not a policy.** The data model has no "AI-finalised"
   disposition state; every disposition requires a human actor (audit-log invariant test) — moved from
   a promise to a CI-gated invariant (*Solution approach* §1/§4, *Success metrics*, M2 exit).
3. **Added a quantified recall kill-gate at M2.** Recall < 95% (or any AI-action false exclusion) →
   assist ships advisory/triage-only and recall claims are withheld (*Roadmap* M2, TASKS M2 DoD).
4. **Adopted WSS@95 as the workload metric** instead of a vanity "time saved", anchoring to the
   established systematic-review evaluation literature (*Success metrics*).
5. **Made extraction "no span, no value" a tested invariant** with a "proposed-unverified" value
   state requiring human commit, rather than a best-effort grounding (*Solution* §5, *Success metrics*).
6. **Built a source-rights gate into the ingest path** that classifies + records each source's licence
   *before* any content is stored or sent to the model (*Solution* §3, *Data/licensing*).
7. **Led the Data/licensing section with the binding cancer guardrails** (open-access/aggregate/
   de-identified only; controlled-access + identifiable data out of scope; per-source verification).
8. **Specified the COSMIC/OncoKB non-commercial vs. TCGA/GEO/PRIDE open distinction** and the rule
   that CC-BY-NC content is never relicensed permissively (*Data/licensing*).
9. **Separated abstract vs. full-text vs. metadata licensing** and the "process locally, don't
   redistribute" rule for closed/subscriber content (*Data/licensing*, *Security*).
10. **Added a PII / identifiable-data quarantine at ingest** for case reports / supplementary materials
    that can contain identifiable patient detail (*Data/licensing*, *Security*, Risks).
11. **Made the design local-first** (library + CLI + portable store) so closed full text never has to
    leave the reviewer's machine — privacy + low-resource benefit (*Solution*, *Security*).
12. **Promoted patient-facing output to a separate, late, OPTIONAL HIGH-tier capability** with a
    *blocking* oncologist **and** advocate sign-off — not bundled into the medium-risk core
    (*Executive summary*, *Quality gates*, *Roadmap* M6).
13. **Added automation-bias as a first-class risk** with concrete mitigations (show criterion+span+
    confidence not a verdict; spot-check sampled "verified" items) — the human-in-the-loop failure mode
    most tools ignore (*Risks*).
14. **Added prompt-injection-via-paper-full-text** to the threat model with an untrusted-content stance
    (source text is data, never instructions) and test cases (*Security*, *Risks*).
15. **Made the eval gold standard a real, diverse open-access review** and flagged
    representativeness/generalisation as its own risk; recall reported with CIs and version-scoped
    (*Solution* §10, *Risks*, *Sustainability*).
16. **Scoped statistical meta-analysis OUT** with a clean data hand-off to `metafor`/RevMan, keeping the
    project focused on the two stages it can de-risk (*Goals/non-goals*, *Scope*, *Dependencies*).
17. **Scoped the literature search OUT** (it stays the information specialist's documented job) to
    avoid undermining reproducibility of the search (*Non-goals*, *Scope*).
18. **Added a dated partner-acquisition plan + build-vs-mothball/pivot decision rule** so the project
    can't drift in `TBD`; the pivot is releasing the open *reference review* as a methods artefact
    (*Executive summary*, *Problem*, *Risks*).
19. **Added a worked open-access reference review** as both a validation fixture and the fallback
    deliverable if no pilot is secured (*Scope*, *Roadmap* M4, pivot rule).
20. **Specified PRISMA 2020 flow-count reconciliation** (counts must reconcile to the logs) as a
    conformance check, not a hand-drawn diagram (*Success metrics*, *Solution* §8).
21. **Added a reproducibility manifest** (model id+version+prompt hash per assertion) so an AI-assisted
    review is re-derivable and defensible at publication/peer review (*Solution* §7, *Success metrics*).
22. **Made expert sign-off version-scoped** (methodologist, oncology, oncologist+advocate) so it does
    not silently carry forward across changes — mirrors the staleness discipline (*Governance*).
23. **Chose Apache-2.0 for code** (explicit patent grant for a research-tooling commons) over a bare
    MIT, with the choice flagged for governance (*Solution*, *Data/licensing*, *Open questions*).
24. **Added a per-output licence + attribution manifest** so teams can see exactly what is shareable
    and under which terms before they publish (*Data/licensing*, M4 export task).
25. **Tied risk-of-bias to human judgement explicitly** — the AI only surfaces verbatim evidence; the
    human assigns the RoB rating — preventing a subtle autonomy creep (*Solution* §6, *Risks*).

---

## Review sign-off

**Reviewer:** drafting engineer + TPM (self-review pass for completeness and correctness against
`PLAN_SPEC.md`, `CLAUDE.md`, `docs/good-deed-definition.md`, and the Task JSON schema).

**Completeness check.** All 17 required H2 sections are present and in order: Executive summary;
Problem & beneficiaries; Goals and non-goals; Success metrics (outcomes); Scope; Solution approach &
architecture; Data, licensing & compliance; Quality, review & risk gates; Roadmap & milestones; Work
breakdown; Governance, roles & stakeholders; Dependencies & integrations; Risks & mitigations;
Security & privacy; Sustainability & maintenance; Open questions; References. Appendix A (25 applied
improvements) and this sign-off follow.

**Correctness / guardrail check.** (a) The cancer guardrails lead the Data/licensing section and are
stated as binding: open-access/aggregate/de-identified only; controlled-access (dbGaP/EGA/biobanks) +
identifiable data out of scope; per-source verification; COSMIC/OncoKB non-commercial vs. TCGA/GEO/
PRIDE open. (b) HIGH-risk patient-facing output has a **blocking** oncologist **and** advocate gate
that a maintainer cannot override, with persistent "not medical advice". (c) The core is agent-neutral
with all Claude specifics behind the LLM adapter (Elyos rule); the CLI/library never runs autonomous
decisions. (d) Honest `TO BE SECURED` / `verifiedNeed: false` throughout — no partner is invented.
(e) Outcome-based success metrics (reviews completed/accelerated, recall maintained), not vanity.

**Fixes applied during review.** Aligned the M2 kill-gate wording between *Roadmap* and *Success
metrics* (recall threshold + "advisory/triage-only" fallback); ensured the Definition of Shipped in
*Quality gates*, *Roadmap* M5, and TASKS M5 DoD match (recall ≥ target, 0 AI-only exclusions,
provenance + licence compliance, methodologist confirmation); confirmed the example Task JSON in
TASKS.md is schema-valid (all required fields present, enum values legal, `verifiedNeed:false`,
`lane:donated` so no `fundedBudgetUsd` required); confirmed every task in TASKS.md carries
`verifiedNeed: false` and `requestor: TO BE SECURED`.

**Outstanding (human decisions, not blockers):** secure pilot team + methodologist + (if patient-facing)
oncologist + advocate; confirm code licence (Apache-2.0 vs MIT) and recall threshold via governance.
Status: **Draft approved for circulation** — ready for partner outreach and governance review.
