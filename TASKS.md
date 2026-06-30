# Systematic-Review-Assist — TASKS.md

> Status: Draft · Version: 0.1.0 · Last updated: 2026-06-28 · Owner: TBD (maintainer) · Lane: donated

Backlog for **Systematic-Review-Assist** (slug: `systematic-review-assist`), open, PRISMA-2020-aware
tooling that **assists** human teams with screening and data extraction for cancer systematic reviews
— with the AI as a recall-first, provenance-grounded **second pair of eyes that never decides**. See
`PLAN.md` for full context.

Two **hard product requirements are the first build items**: (1) the **methodology & safety policy
layer** (no autonomous include/exclude or extraction; recall-first; "no span, no value"; source-rights
gate; PII/identifiable-data quarantine) and (2) the **provenance + rights** foundation. This is a
**MEDIUM** risk-tier project (domain accuracy matters) with a **HIGH** sub-surface: any patient-facing
plain-language output requires **blocking oncologist + patient-advocate sign-off** and "not medical
advice" framing. **Binding cancer guardrails:** open-access / aggregate / de-identified data only;
controlled-access (dbGaP/EGA/biobanks) + identifiable patient data out of scope; per-source licence
verification (TCGA/GEO/PRIDE open; COSMIC/OncoKB non-commercial); provenance on every assertion.

No review team / methodologist / pilot is yet secured, so **every** task carries `requestor: TO BE
SECURED` and `verifiedNeed: false`.

## How these tasks map to Elyos

Each task below becomes an Elyos **Task JSON** validated against `packages/schema/src/schemas.ts`.
Field mapping:

- **id** — stable slug `systematic-review-assist-<area>-NNN` (e.g. `systematic-review-assist-policy-001`).
- **title** — the task title in the milestone table.
- **project** — `systematic-review-assist`.
- **type** — one of `code | research | writing | data | design-spec | maintenance`.
- **lane** — `donated` (default; no funded tasks planned. Any `funded` task must add `fundedBudgetUsd`
  with a hard cap).
- **priority** — `high | medium | low`.
- **domain** — array, e.g. `["cancer","evidence-synthesis","systematic-review","software"]`.
- **riskTier** — `low | medium | high`. Screening/extraction accuracy + methodology = `medium`; any
  patient-facing plain-language output = `high`; pure infra/parsing/UI = `low`.
- **urgent** — boolean (no urgent tasks at cold-start).
- **deliverable** — `pr | dataset | document | translation`.
- **tokenEstimate** — `small | medium | large` (maps to the Size column).
- **status** — `open | in-progress | review | delivered | done` (all start `open`).
- **context / objective / acceptanceCriteria[] / resources[] / output** — per task.
- **requestor** — partner/steward/expert; `TO BE SECURED` everywhere (no partner secured).
- **verifiedNeed** — `false` everywhere (no review team/need confirmed yet).
- **outputLicense** — code: **Apache-2.0** (TBD vs MIT — governance); datasets/outputs/reference review:
  **CC-BY-4.0** (CC-BY-NC source content kept non-commercial); docs/specs: **CC-BY-4.0**.

Size legend: small ≈ tokenEstimate `small`, med ≈ `medium`, large ≈ `large`.
Reviewer roles: **Maintainer**; **Methodology/safety reviewer** (audits no-autonomy / recall-first /
no-span-no-value invariants); **Methodologist** (systematic-review methodologist / information
specialist — TO BE SECURED); **Oncology reviewer** (cancer-domain accuracy — TO BE SECURED);
**Oncologist + Advocate** (HIGH-tier patient-facing blocking gate — TO BE SECURED); **Steward**
(last-mile pilot owner — TO BE SECURED).

---

## Milestone M0 — Foundations, methodology contract & rights gate (cold-start)

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
|---|---|---|---|---|---|---|---|
| systematic-review-assist-policy-001 | Methodology & safety policy specification (no-autonomy, recall-first, no-span-no-value, rights gate, PII quarantine) | design-spec | medium | medium | document | — | Methodology/safety reviewer + Maintainer |
| systematic-review-assist-repo-002 | Local-first monorepo: pnpm + TS/ESM library + CLI + CI (build/test/lint) | code | small | low | pr | — | Maintainer |
| systematic-review-assist-policy-003 | Methodology policy layer: no-autonomy invariant + recall-first contract + no-span-no-value, enforced in code + CI | code | large | medium | pr | 001, 002 | Methodology/safety reviewer |
| systematic-review-assist-provenance-004 | Provenance/audit data model + portable project store (append-only, model/version/actor) | code | medium | medium | pr | 002 | Maintainer |
| systematic-review-assist-rights-005 | Source-rights gate + PII/identifiable-data quarantine at ingest boundary | code | medium | medium | pr | 001, 004 | Methodology/safety reviewer + Maintainer |
| systematic-review-assist-protocol-006 | PRISMA-aware protocol/PICO + machine-readable eligibility + extraction-field schema | code | medium | low | pr | 002 | Maintainer + Methodologist |

**Acceptance criteria — key tasks**

- **systematic-review-assist-policy-001** (methodology & safety policy spec)
  - Enumerates the non-negotiables in concrete, testable terms: **no autonomous include/exclude or
    extraction** (every disposition has a recorded human actor); **recall-first** behaviour
    (low-confidence/ambiguous → escalate to human as *potential include*; never silent exclude);
    **"no span, no value"** (no extracted value or screening rationale without a verbatim source span);
    the **source-rights gate** (per-source licence verified before ingest/processing/redistribution);
    the **PII/identifiable-data quarantine**; and the **"not medical advice" + oncologist/advocate**
    gate for any patient-facing output.
  - Defines the audit/provenance fields every assertion must carry and the untrusted-content stance
    (paper text is data, never instructions).
  - Defines the test taxonomy the policy-layer suite must enforce (no-autonomy invariant, recall-first,
    no-span-no-value, rights gate, PII quarantine, injection resistance) and that a violation **fails
    the build**.
  - Reviewed and signed off by the methodology/safety reviewer (recorded).

- **systematic-review-assist-policy-003** (methodology policy layer)
  - The data model has **no "AI-finalised" disposition state**; an exclusion cannot be committed
    without a recorded human actor (invariant test fails the build if violated).
  - Recall-first contract enforced: the assist can rank/triage and *propose*, but cannot exclude; ambiguous/
    low-confidence items are routed to human review as *potential include*.
  - "No span, no value" enforced for any AI-proposed assertion; unsourced assertions are rejected.
  - Policy is library-side and not overridable by document/source content (injection-resistant);
    injection cases included.

- **systematic-review-assist-rights-005** (rights gate + PII quarantine)
  - Each source classified (open-access full text / abstract-only / closed) and its licence recorded
    **before** any content is stored or sent to the model; non-compliant ingest is refused with an
    explanation.
  - Closed/subscriber content is flagged **process-locally-only, never redistribute**; CC-BY-NC content
    flagged non-commercial (never relicensed permissively).
  - Records that appear to contain identifiable patient data are **quarantined** and surfaced to a
    human, not sent to the model or written to shared outputs; controlled-access data is hard-refused.

- **systematic-review-assist-protocol-006** (protocol/eligibility/extraction schema)
  - Models a PICO(S) question, structured inclusion/exclusion criteria as machine-readable rules, a
    registered-protocol link (PROSPERO id), outcome definitions, and the per-review extraction field
    schema; criteria are referenceable by screening rationales.

**M0 Definition of Done:** methodology & safety policy spec (reviewer-signed) + policy layer merged
with its test suite passing in CI (no-autonomy invariant, recall-first, no-span-no-value, rights gate,
PII quarantine, injection resistance — build fails on violation); local-first pnpm/TS/ESM library+CLI
skeleton + green CI; provenance/audit data model + portable store; PRISMA-aware protocol/eligibility/
extraction schema; "AI-assisted, human-verified / not medical advice" framing wired in; candidate
review team + topic identified (for M5 sequencing).

---

## Milestone M1 — Ingest, dedup & the human screening workspace

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
|---|---|---|---|---|---|---|---|
| systematic-review-assist-ingest-007 | Record import (RIS/BibTeX/PubMed-XML/CSV + ≥1 of Embase/CENTRAL) + transparent reversible dedup | code | large | medium | pr | 005, 006 | Maintainer |
| systematic-review-assist-screening-008 | Human dual-reviewer screening workspace + conflict adjudication + append-only audit (no AI yet) | code | large | medium | pr | 003, 004, 007 | Maintainer + Methodologist |
| systematic-review-assist-fixture-009 | Labelled gold-standard review fixture from open-access cancer literature (for eval) | data | medium | medium | dataset | 005, 007 | Methodologist + Oncology reviewer |

**Acceptance criteria — key tasks**

- **systematic-review-assist-ingest-007** (ingest + dedup)
  - Parses RIS, BibTeX, PubMed/MEDLINE XML, CSV and at least one of Embase/Cochrane CENTRAL;
    normalises records; the **rights gate (005) runs at ingest** recording each source's licence.
  - Deduplication is transparent (DOI/PMID exact + conservative fuzzy title/author/year), **reversible**,
    and every merge is logged; counts feed the PRISMA flow.

- **systematic-review-assist-screening-008** (human screening workspace)
  - Dual-reviewer title/abstract → full-text screening with **human** conflict adjudication; every
    disposition is recorded with the human actor, reason, and timestamp (append-only audit).
  - **No AI suggestions exist at this stage** — establishes the human baseline and the audit trail the
    AI assist later plugs into.

- **systematic-review-assist-fixture-009** (gold-standard fixture)
  - A real, diverse **open-access** cancer review's records with known include/exclude labels (and a
    gold-standard extraction sheet for later) assembled under verified licences with provenance.
  - Representativeness documented; used to compute recall / WSS@95 / extraction accuracy; CC-BY-4.0.

**M1 Definition of Done:** records import cleanly from standard exports with the rights gate recording
licences; dedup is transparent + reversible + logged; humans can complete dual-reviewer screening with
a full append-only audit trail and **no AI involvement**; a labelled open-access gold-standard fixture
exists for eval.

---

## Milestone M2 — AI screening assist (recall-first, human-in-the-loop) + screening eval (kill-gate)

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
|---|---|---|---|---|---|---|---|
| systematic-review-assist-llm-010 | Provider-neutral LLM client adapter (Claude first, model tiering) | code | small | low | pr | 002 | Maintainer |
| systematic-review-assist-screening-011 | AI screening assist: per-record suggestion (disposition + criterion + rationale + confidence + span); cannot auto-exclude | code | large | medium | pr | 003, 008, 010 | Methodology/safety reviewer + Methodologist |
| systematic-review-assist-eval-012 | Screening eval harness (recall + WSS@95 vs gold standard) — recall kill-gate | code | medium | medium | pr | 009, 011 | Methodologist + Maintainer |

**Acceptance criteria — key tasks**

- **systematic-review-assist-screening-011** (AI screening assist)
  - For each record, returns a **structured** suggestion: proposed disposition, the **specific
    eligibility criterion**, a rationale, a **calibrated confidence**, and the **verbatim source span**
    relied upon — surfaced inside the dual-reviewer workflow.
  - **Cannot auto-exclude**: at title/abstract it ranks/triages and proposes only; every exclusion is
    human-confirmed with a recorded reason; low-confidence/ambiguous escalates as *potential include*.
  - Treats record/full-text content as untrusted data (injection-resistant); paper text cannot
    override the policy.

- **systematic-review-assist-eval-012** (screening eval + kill-gate)
  - Reports **recall/sensitivity (with 95% CI)** and **WSS@95** against the gold-standard fixture; an
    audit-log check asserts **0 eligible studies lost via any AI action without human review**.
  - **Kill-gate:** if recall < 95% (or any AI-action false exclusion), the assist is restricted to
    **advisory/triage-only** (ranking, no disposition claims) and recall/WSS claims are **withheld**
    until the bar is met and a methodologist signs off.

**M2 Definition of Done:** the AI screening assist proposes (disposition + criterion + rationale +
confidence + span) inside the human dual-reviewer workflow and **cannot auto-exclude** (enforced +
tested); the eval harness reports recall + WSS@95 on the gold standard; the recall kill-gate is
operative — if recall < 95% or any AI-action false exclusion occurs, the assist ships advisory/
triage-only and recall claims are withheld; methodologist sign-off recorded before any recall claim is
published.

---

## Milestone M3 — AI extraction assist (span-grounded) + risk-of-bias assist

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
|---|---|---|---|---|---|---|---|
| systematic-review-assist-extraction-013 | Span-grounded extraction assist (typed fields; proposed-unverified until human commit; "no span, no value") | code | large | medium | pr | 003, 006, 010 | Methodology/safety reviewer + Methodologist |
| systematic-review-assist-rob-014 | Risk-of-bias assist (surfaces verbatim evidence for RoB 2/ROBINS-I/QUADAS-2; human assigns rating) | code | medium | medium | pr | 013 | Methodologist + Oncology reviewer |

**Acceptance criteria — key tasks**

- **systematic-review-assist-extraction-013** (extraction assist)
  - Proposes typed values for the review's extraction schema, **each bound to a verbatim source span +
    location**; values land in a **"proposed, unverified"** state and require human verify/edit before
    being committed to the dataset.
  - **No span → no auto-fill** (field left for manual entry); numeric/unit fields plausibility-checked,
    never invented; extraction accuracy reported vs. the gold-standard sheet (final = human-verified).

- **systematic-review-assist-rob-014** (risk-of-bias assist)
  - Surfaces the verbatim text relevant to each item of the selected instrument; the **human assigns**
    the judgement; the tool records the supporting quote + the human's rating + reasoning.
  - Never outputs a RoB rating itself (no autonomy creep).

**M3 Definition of Done:** span-grounded extraction with "no span, no value" enforced and a
human-commit step (proposed-unverified → committed); extraction accuracy reported vs. gold standard;
RoB assist surfaces evidence with humans assigning all ratings; provenance carried on every value.

---

## Milestone M4 — PRISMA reporting, reproducibility & hardening

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
|---|---|---|---|---|---|---|---|
| systematic-review-assist-prisma-015 | PRISMA 2020 flow diagram + report scaffold + export (screening log, extraction dataset, licence/attribution manifest) + reproducibility manifest | code | medium | medium | pr | 008, 012, 013 | Maintainer + Methodologist |
| systematic-review-assist-reference-016 | Worked open-access cancer reference review (validation + teaching artefact + pivot fallback) | research | large | medium | document | 014, 015 | Methodologist + Oncology reviewer |

**Acceptance criteria — key tasks**

- **systematic-review-assist-prisma-015** (PRISMA + reproducibility + export)
  - Emits a **PRISMA 2020 flow diagram** whose counts **reconcile** to the identification/screening/
    eligibility/included logs; report scaffold covers PRISMA 2020 items.
  - Exports screening log + extraction dataset (CSV/JSON) with a **per-output licence + attribution
    manifest**; a **reproducibility manifest** (model id+version+prompt hash + human actors) lets the
    AI-assisted steps be re-derived.
  - WCAG 2.2 AA on the local review UI; expanded eval/policy suites green.

- **systematic-review-assist-reference-016** (reference review)
  - A complete, openly-licensed worked review on open-access cancer literature, produced with the tool,
    methodologist- and oncology-reviewed; doubles as the **pivot deliverable** if no pilot is secured.

**M4 Definition of Done:** reconciled PRISMA 2020 flow + report scaffold; exports with licence/
attribution + reproducibility manifests; accessibility + expanded evals green; the open-access
reference review completed and reviewed.

---

## Milestone M5 — Pilot with a real review team (the deed)

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
|---|---|---|---|---|---|---|---|
| systematic-review-assist-pilot-017 | Secure pilot review team + topic; run a real review with the tool; independent methodology + licence verification; record outcome | maintenance | medium | medium | document | 015, 016 | Steward + Methodologist |

**Acceptance criteria — key task**

- **systematic-review-assist-pilot-017** (secure pilot + close the loop — the deed)
  - A real cancer review team (or sponsoring organisation) is secured; steward assigned; `verifiedNeed`
    flips to `true`.
  - The team uses the tool to **complete or materially accelerate a real review**; measured **recall ≥
    target (≥95%) with 0 AI-only exclusions**; provenance + licence compliance verified; PRISMA
    artefacts produced; an independent **methodologist confirms rigour was maintained**; outcome recorded.
  - Driven by the **dated partner plan** (team+topic by 2026-08-31, methodologist by 2026-10-31, pilot
    by 2026-12-31). If no pilot by **~2027-03-31**, apply the **build-vs-mothball/pivot rule** (PLAN
    exec summary): pivot to releasing the open **reference review** (016) as a methods artefact, or
    mothball to maintenance-only — recorded in governance, rather than ship to no real beneficiary.

**M5 Definition of Done:** project-level **Definition of Shipped** met — a real team completes/
accelerates a real cancer systematic review with the tool, recall ≥ target with 0 AI-only exclusions,
provenance + licence compliance verified, PRISMA artefacts produced, independent methodologist confirms
rigour, outcome recorded. *(Gated on a secured partner — TO BE SECURED.)*

---

## Milestone M6 — Sustain, scale & (optional, HIGH-tier) patient-facing summaries

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
|---|---|---|---|---|---|---|---|
| systematic-review-assist-ops-018 | Ops runbook + outcomes tracking + maintenance rotation + source-licence review cadence + re-validation process for a new topic | maintenance | medium | medium | document | 017 | Maintainer + Steward |
| systematic-review-assist-pls-019 | (OPTIONAL, HIGH) Plain-language summary of a completed review — education only, "not medical advice", blocking oncologist + advocate sign-off | writing | medium | high | document | 016, 017 | Oncologist + Advocate (blocking) + Methodologist |

**Acceptance criteria — key tasks**

- **systematic-review-assist-ops-018** (sustain)
  - Runbook covers deploy/use, source-licence re-verification cadence, eval/gold-standard maintenance,
    and adding a new review topic with **re-validated** recall (not assumed to transfer).
  - Outcomes tracked are methodological (reviews completed/accelerated, recall maintained, provenance/
    licence compliance, reproducibility) — **not** engagement metrics; named maintenance rotation.

- **systematic-review-assist-pls-019** (optional HIGH-tier patient-facing summary)
  - Derived **only** from a *completed, published* review's own cited findings; education only; carries
    a persistent **"not medical advice"** notice and provenance to the review.
  - **Blocking gate:** ships **only** with recorded sign-off from a **credentialed oncologist AND a
    patient advocate** — a maintainer cannot override; sign-off is version-scoped and re-run on any
    material change. Built only if a partner needs it and the gate is secured.

**M6 Definition of Done:** the tool is sustainably maintained with methodological outcomes tracked, a
rotation owning it, a source-licence review cadence, and a re-validated process for new topics;
**if and only if** the oncologist + advocate gate is secured, an education-only, "not medical advice",
dual-signed-off patient-facing summary capability is available.

---

## Backlog / future

| ID | Title | Type | Size | Risk | Deliverable | Notes |
|---|---|---|---|---|---|---|
| systematic-review-assist-secondtopic-020 | Second review topic / second cancer pack (re-validated recall) | data | large | medium | dataset | Only after M6; full re-validation + methodologist sign-off |
| systematic-review-assist-livingreview-021 | "Living review" update mode (re-screen new records on a cadence) | code | medium | medium | pr | Re-runs assist on incremental search updates; same guardrails |
| systematic-review-assist-i18n-022 | Non-English record screening support | code | medium | medium | pr | Multilingual abstracts; per-language eval before any recall claim |
| systematic-review-assist-revman-023 | Clean data hand-off to metafor/RevMan | code | small | low | pr | Export only; no statistics computed in-tool |
| systematic-review-assist-noncancer-024 | Generalise beyond cancer (re-scoped guardrails) | design-spec | medium | medium | document | Only after cancer validation; re-do domain guardrails |

---

## Generated task index

> Auto-generated by the Elyos task-decomposition agent on 2026-06-29.
> All 24 files in `tasks/` are schema-valid (validated against `packages/schema/dist/index.js`).
> Fan-out dimensions: none (no explicitly enumerated fan-out axes in this backlog).

| File | ID | Title | Type | Priority | Risk | Deliverable |
|---|---|---|---|---|---|---|
| tasks/systematic-review-assist-policy-001.json | systematic-review-assist-policy-001 | Methodology & safety policy specification | design-spec | high | medium | document |
| tasks/systematic-review-assist-repo-002.json | systematic-review-assist-repo-002 | Local-first monorepo: pnpm + TS/ESM library + CLI + CI | code | high | low | pr |
| tasks/systematic-review-assist-policy-003.json | systematic-review-assist-policy-003 | Methodology policy layer: no-autonomy invariant + recall-first + no-span-no-value | code | high | medium | pr |
| tasks/systematic-review-assist-provenance-004.json | systematic-review-assist-provenance-004 | Provenance/audit data model + portable project store | code | high | medium | pr |
| tasks/systematic-review-assist-rights-005.json | systematic-review-assist-rights-005 | Source-rights gate + PII/identifiable-data quarantine | code | high | medium | pr |
| tasks/systematic-review-assist-protocol-006.json | systematic-review-assist-protocol-006 | PRISMA-aware protocol/PICO + eligibility + extraction-field schema | code | high | low | pr |
| tasks/systematic-review-assist-ingest-007.json | systematic-review-assist-ingest-007 | Record import (RIS/BibTeX/PubMed-XML/CSV + Embase/CENTRAL) + reversible dedup | code | high | medium | pr |
| tasks/systematic-review-assist-screening-008.json | systematic-review-assist-screening-008 | Human dual-reviewer screening workspace + conflict adjudication | code | high | medium | pr |
| tasks/systematic-review-assist-fixture-009.json | systematic-review-assist-fixture-009 | Labelled gold-standard review fixture from open-access cancer literature | data | high | medium | dataset |
| tasks/systematic-review-assist-llm-010.json | systematic-review-assist-llm-010 | Provider-neutral LLM client adapter (Claude first, model tiering) | code | high | low | pr |
| tasks/systematic-review-assist-screening-011.json | systematic-review-assist-screening-011 | AI screening assist: per-record suggestion with span; cannot auto-exclude | code | high | medium | pr |
| tasks/systematic-review-assist-eval-012.json | systematic-review-assist-eval-012 | Screening eval harness (recall + WSS@95 vs gold standard) — recall kill-gate | code | high | medium | pr |
| tasks/systematic-review-assist-extraction-013.json | systematic-review-assist-extraction-013 | Span-grounded extraction assist (proposed-unverified until human commit) | code | medium | medium | pr |
| tasks/systematic-review-assist-rob-014.json | systematic-review-assist-rob-014 | Risk-of-bias assist (surfaces evidence for RoB 2/ROBINS-I/QUADAS-2) | code | medium | medium | pr |
| tasks/systematic-review-assist-prisma-015.json | systematic-review-assist-prisma-015 | PRISMA 2020 flow diagram + report scaffold + export + reproducibility manifest | code | medium | medium | pr |
| tasks/systematic-review-assist-reference-016.json | systematic-review-assist-reference-016 | Worked open-access cancer reference review | research | medium | medium | document |
| tasks/systematic-review-assist-pilot-017.json | systematic-review-assist-pilot-017 | Secure pilot review team + topic; run a real review; record outcome | maintenance | high | medium | document |
| tasks/systematic-review-assist-ops-018.json | systematic-review-assist-ops-018 | Ops runbook + outcomes tracking + maintenance rotation | maintenance | medium | medium | document |
| tasks/systematic-review-assist-pls-019.json | systematic-review-assist-pls-019 | (OPTIONAL, HIGH) Plain-language summary with blocking oncologist+advocate gate | writing | low | high | document |
| tasks/systematic-review-assist-secondtopic-020.json | systematic-review-assist-secondtopic-020 | Second review topic / second cancer pack (re-validated recall) | data | low | medium | dataset |
| tasks/systematic-review-assist-livingreview-021.json | systematic-review-assist-livingreview-021 | 'Living review' update mode (re-screen new records on a cadence) | code | low | medium | pr |
| tasks/systematic-review-assist-i18n-022.json | systematic-review-assist-i18n-022 | Non-English record screening support | code | low | medium | pr |
| tasks/systematic-review-assist-revman-023.json | systematic-review-assist-revman-023 | Clean data hand-off to metafor/RevMan | code | low | low | pr |
| tasks/systematic-review-assist-noncancer-024.json | systematic-review-assist-noncancer-024 | Generalise beyond cancer (re-scoped guardrails) | design-spec | low | medium | document |

---

## Example task JSON

Complete, schema-valid Task JSON for the first M0 task (`systematic-review-assist-policy-001`):

```json
{
  "id": "systematic-review-assist-policy-001",
  "title": "Methodology & safety policy specification (no-autonomy, recall-first, no-span-no-value, rights gate, PII quarantine)",
  "project": "systematic-review-assist",
  "type": "design-spec",
  "lane": "donated",
  "priority": "high",
  "domain": ["cancer", "evidence-synthesis", "systematic-review", "research-tooling", "software"],
  "riskTier": "medium",
  "urgent": false,
  "deliverable": "document",
  "tokenEstimate": "medium",
  "status": "open",
  "context": "Systematic-Review-Assist is open, PRISMA-2020-aware tooling that ASSISTS human teams with title/abstract & full-text screening and structured data extraction for cancer systematic reviews, with the AI acting as a recall-first, provenance-grounded second pair of eyes that never decides. The catastrophic error in a systematic review is the inverse of a chatbot's: silently EXCLUDING a relevant study biases the whole body of evidence, so the tool must fail toward inclusion and toward human review, never toward automation. Binding cancer guardrails apply: open-access/aggregate/de-identified data only; controlled-access (dbGaP/EGA/biobanks) and identifiable patient data are out of scope; per-source licence verification (TCGA/GEO/PRIDE open; COSMIC/OncoKB non-commercial); no medical advice; provenance on every assertion. This is the cold-start task that specifies the methodology & safety policy layer all later code and content must implement and be tested against. No review team, methodologist, or pilot partner is yet secured.",
  "objective": "Write the authoritative methodology & safety policy specification that every later component must implement and be tested against: the no-autonomy invariant (no AI-finalised include/exclude or extraction; every disposition has a recorded human actor), the recall-first contract (ambiguous/low-confidence escalates to human as potential-include; never silent exclude), the 'no span, no value' provenance rule, the source-rights gate (per-source licence verified before ingest/processing/redistribution; closed content processed locally only; CC-BY-NC kept non-commercial), the PII/identifiable-data quarantine, the untrusted-content (paper text is data not instructions) stance, and the 'not medical advice' + oncologist/advocate blocking gate for any patient-facing output.",
  "acceptanceCriteria": [
    "Enumerates the non-negotiables in concrete, testable terms: no autonomous include/exclude or extraction (every disposition has a recorded human actor); recall-first behaviour (ambiguous/low-confidence escalates to human as potential-include, never silent exclude); 'no span, no value' (no extracted value or screening rationale without a verbatim source span)",
    "Specifies the source-rights gate: each source's licence verified and recorded before ingest/processing/redistribution; closed/subscriber content processed locally only and never redistributed; CC-BY-NC content kept non-commercial; controlled-access (dbGaP/EGA/biobanks) and identifiable patient data hard-refused",
    "Specifies the PII / identifiable-data quarantine at ingest for case reports and supplementary materials, and the untrusted-content stance (source text is data, never instructions; injection-resistant, library-side enforcement)",
    "Defines the audit/provenance fields every assertion must carry (source, span/location, model id+version+prompt hash, human actor, action, timestamp) and that no assertion may surface without them",
    "Defines the policy-layer test taxonomy (no-autonomy invariant, recall-first, no-span-no-value, rights gate, PII quarantine, injection resistance) and mandates that any violation FAILS THE BUILD",
    "Mandates the persistent 'AI-assisted, human-verified - not a substitute for methodological judgement' labelling, and the 'not medical advice' + blocking oncologist-AND-advocate sign-off gate for any patient-facing output",
    "Reviewed and signed off by the methodology/safety reviewer (recorded in the reviewers ledger)"
  ],
  "resources": [
    "planning/projects/systematic-review-assist/PLAN.md",
    "CLAUDE.md",
    "docs/good-deed-definition.md",
    "packages/schema/src/schemas.ts",
    "planning/ROADMAP.md"
  ],
  "output": "A reviewed policy-specification document (the methodology & safety charter) defining the no-autonomy invariant, recall-first contract, 'no span, no value' provenance rule, source-rights gate, PII/identifiable-data quarantine, untrusted-content stance, audit/provenance fields, the build-gating test taxonomy, and the 'not medical advice' + oncologist/advocate gate - the contract that systematic-review-assist-policy-003 (policy layer), -rights-005 (rights gate), and the screening/extraction assists implement and are tested against.",
  "requestor": "TO BE SECURED",
  "verifiedNeed": false,
  "outputLicense": "CC-BY-4.0"
}
```
