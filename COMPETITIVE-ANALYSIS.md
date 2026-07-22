# Competitive & Improvement Analysis — `systematic-review-assist`

Open, PRISMA-aware tooling to assist screening + data extraction for cancer systematic reviews (SRs), human-in-the-loop, medium risk. Analysis grounded in web research (cited inline). Date: 2026-06-29.

---

## 1. Correctness & completeness review of PLAN.md

The plan is unusually strong: it already inverts the dangerous default (recall over throughput), makes "no autonomous exclude" a *structural* (data-model) invariant rather than a policy, builds the rights/PII/provenance gates first, and adopts WSS@95 and a recall kill-gate. The 25-item Appendix A shows real self-critique. The findings below are gaps, errors, and soft spots that remain.

**A. PRISMA 2020 / PRISMA-S compliance is under-specified.**
- The plan repeatedly says "PRISMA 2020" and auto-generates the flow diagram, but **never references PRISMA-S** (the search-reporting extension) even though it cites it in passing in TASKS. Since the tool explicitly scopes the *search itself* OUT (correctly), the handoff boundary must still produce a **PRISMA-S-conformant search-reporting record** (databases, dates, full strategies, filters, dedup method) or the downstream review is non-compliant. The flow diagram is a count reconciler; PRISMA-S is the part the tool actually touches via ingest/dedup. Make PRISMA-S conformance of the ingest/dedup log an explicit M4 artefact. ([PRISMA-S, Cochrane](https://www.cochrane.org/events/prisma-s-extension-prisma-statement-reporting-literature-searches-systematic-reviews))
- "PRISMA 2020 conformance" is asserted but the **27-item checklist is never enumerated**; the report scaffold should map to specific checklist items, and there is emerging work on automated PRISMA-adherence checking the plan could lean on. ([LLM PRISMA adherence checking](https://arxiv.org/pdf/2511.16707))

**B. The recall target (≥95%) is stated but the validation design is thin / arguably wrong.**
- A single gold-standard review fixture cannot support a generalizable recall *claim* — recall is dataset- and topic-specific and notoriously unstable across reviews. The plan acknowledges this as a risk but still headlines "≥95%." The literature shows LLM screening recall ranging widely (e.g. ChatGPT 87–93% in one study; GPTscreenR 71% sensitivity), so 95% is **not a given** and must be earned per topic. ([medRxiv sensitivity/specificity](https://www.medrxiv.org/content/10.1101/2023.12.15.23300018.full.pdf); [JMAI meta-analysis](https://jmai.amegroups.org/article/view/10102/html))
- **95% recall is below the SR norm.** Established screening-automation practice often targets ~100% recall (or "WSS@100%"/"safe to exclude" at 95% confidence the last relevant record is found), not 95%. A 95% recall bar means *up to 1 in 20 eligible studies missed* — for a confirmatory oncology review that is potentially unacceptable. The metric should be reframed around "how confident are we no relevant study is lost" (the missing-studies cardinal sin), with the bar set *with the methodologist* and likely higher than 95%. Note also the internal tension: the metric table says "0 confirmed eligible studies lost" yet sets recall at 95% — those are different guarantees and the plan should reconcile them (the "0 lost" holds only because AI can't exclude; the 95% is about *triage ranking quality*, which the plan should state explicitly).
- **No confidence-calibration validation.** The assist emits a "calibrated confidence" and routes low-confidence to human, but there is no plan to *validate* that calibration (reliability diagram / ECE). If confidence is miscalibrated, the recall-first routing is hollow.
- **No stopping-rule / prioritization-screening semantics.** The biggest real workload saving in tools like ASReview comes from *active-learning prioritization with a stopping criterion* (screen in relevance order, stop when confident). The plan's "rank/triage" never defines a stopping rule or how WSS@95 is even computed without one. This is a substantive methodological gap.

**C. Human-in-the-loop boundary is well-drawn but has two leaks.**
- **Automation bias is named but the mitigation is weak.** Showing criterion+span+confidence does not prevent rubber-stamping; evidence on RobotReviewer shows reviewers resist *and* over-trust automation. Stronger mitigations: hide the AI suggestion until the human commits a provisional call (independent-then-reveal), mandatory blind double-screening on a random audit subset, and tracking human-AI agreement drift. ([RobotReviewer real-time comparison](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC9174024/))
- **"AI-as-second-screener" recall accounting.** If AI is one of two screeners, the *combined* (AI ∪ human) recall is what matters, not AI-alone recall. The plan defers this to the methodologist (good) but the eval harness must measure *both* configurations explicitly, because dual human+AI can have *higher* recall than human+human if AI flags differently — that's the actual value proposition, and it's not measured.

**D. Risk-of-bias automation limits — correctly conservative, but evidence should be cited as the rationale.** The plan has the AI only *surface evidence* and the human assign the rating. Good — RobotReviewer-style auto-RoB agrees with humans only ~72% (moderate-to-slight by domain), so full automation is unjustified. The plan should cite this as the explicit justification and should warn that even *evidence surfacing* can anchor the human's judgment (same automation-bias risk). ([RobotReviewer agreement study](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC9174024/); [Hirt 2021](https://sigmapubs.onlinelibrary.wiley.com/doi/full/10.1111/jnu.12628))

**E. Hallucination in extraction — mitigations good, one gap.** "No span, no value" + proposed-unverified is the right architecture and matches the leading concern in the Elicit literature (hallucination 1–6%, including *misrepresentation via rounding/labelling* even when a span exists). The gap: a verbatim span does **not** guarantee the value is *correctly derived* from it (rounding, unit conversion, wrong arm). The plan needs a **span-faithfulness check** (does the asserted value actually follow from the quoted span?) beyond mere presence of a span. ([Elicit feasibility study](https://www.cambridge.org/core/journals/research-synthesis-methods/article/using-elicit-ai-research-assistant-for-data-extraction-in-systematic-reviews-a-feasibility-study-across-environmental-and-life-sciences/C97DAEC70C3173A260F0B12E729E7250); [Elicit proof-of-concept](https://journals.sagepub.com/doi/10.1177/08944393251404052))

**F. Reproducibility / audit trail — strong, but model drift unaddressed.** The reproducibility manifest records model id+version+prompt hash. But Claude model versions are deprecated/retired over time; a review "re-derivable" in 2026 may not be in 2028. Add: (1) cache the actual model outputs (not just the prompt) so re-derivation doesn't depend on the model still existing; (2) record temperature/seed/sampling params; (3) a "frozen evidence" export.

**G. Emerging-guidance alignment is missing entirely.** The plan predates / omits **RAISE** (Responsible AI in evidence Synthesis) and the **2025 joint Cochrane/Campbell/JBI/CEE position statement**, which now define the field's expectations: AI use must be *disclosed, human-overseen, justified, and reported*, and authors remain accountable. The plan satisfies the spirit but should **explicitly map its transparency artefacts to RAISE recommendations and the position statement** — this is both a correctness gap and a major credibility/differentiation lever for adoption. ([RAISE](https://www.cochrane.org/about-us/news/how-cochrane-advancing-responsible-ai-evidence-synthesis); [Position statement 2025, Cochrane Library](https://www.cochranelibrary.com/cdsr/doi/10.1002/14651858.ED000178/full))

**H. Smaller items.**
- "Not replacing methodologists" is well-honored, but the plan has **no information-specialist-facing feature** (search strategy QA, dedup transparency report) — a missed, low-risk, high-value surface that adjacent tools (Polyglot, SRA dedup) own.
- Cost/throughput of LLM calls at 3,000–30,000 records is never estimated; even cheap-tier Claude across tens of thousands of records per review topic has a real cost and latency profile that affects the "low-resource setting" beneficiary claim. Add a cost model.
- The metric "Extraction first-pass field accuracy reported per field type" lacks a *target* — fine, but it should at least be benchmarked against the Elicit numbers (81–91% overall; far lower on review-specific variables) so reviewers calibrate expectations.
- **No non-English handling.** Cancer SRs include non-English trials; silent exclusion of non-English records is itself a recall failure. Out-of-scope is fine, but it must be *declared* in the flow accounting.
- "0 autonomous-decision rate — structurally impossible" is the plan's strongest claim and is genuinely good; ensure the audit-log invariant test also covers the *extraction commit* path, not just screening.

---

## 2. Competitive landscape (researched, cited)

**Covidence** — The Cochrane-default commercial screening/extraction platform; dual screening, conflict resolution, RoB, basic ML prioritization, PRISMA flow export. *Strengths:* trusted, Cochrane-integrated, gentle learning curve, good collaboration. *Weaknesses:* closed/SaaS, subscription cost, only "basic-to-moderate" ML, cloud-hosted (closed full text leaves the machine), limited auditability/reproducibility of any AI step, no provenance-grade span grounding. ([comparison PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC12947929/); [DistillerSR vs Covidence](https://blog.hifivestar.com/posts/top-systematic-review-software-2025))

**Rayyan** — Popular, low-cost/free-tier screening tool with ML suggestions. *Strengths:* fast title/abstract screening, collaboration, widely adopted. *Weaknesses:* **no built-in data extraction or quality assessment** (screening-only), closed platform, limited audit/provenance, AI opaque. ([comparison PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC12947929/))

**EPPI-Reviewer** — Mature, full-lifecycle platform (EPPI-Centre, UCL); strong ML/priority screening, supports living reviews; free for UK institutions. *Strengths:* deep methodology features, classifiers, large-review scale. *Weaknesses:* closed, steep learning curve, cost outside UK, AI not provenance-grounded/reproducible by default. ([comparison](https://pmc.ncbi.nlm.nih.gov/articles/PMC12947929/))

**DistillerSR** — Enterprise leader (pharma/HTA/regulatory). Introduced reference-screening AI in 2009; now deterministic AI for dedup/classification + **purpose-built generative AI for human-in-the-loop full-text extraction & summarization**; active learning, custom classifiers, "verifying exclusions." *Strengths:* most advanced AI feature set, audit-oriented (regulatory market), scale. *Weaknesses:* expensive enterprise pricing, closed, cloud, primarily for well-resourced teams — the exact opposite of Hee-Lee Oss's beneficiary. Its AI "verifies exclusions" — close to the auto-exclude territory Hee-Lee Oss refuses. ([DistillerSR product](https://www.distillersr.com/products/distillersr-systematic-review-software); [skywork deep dive](https://skywork.ai/skypage/en/distillersr-ai-research-api/1976835974678769664))

**ASReview (LAB)** — **The main open-source competitor.** Active-learning title/abstract screener from Utrecht; Nature Machine Intelligence pedigree; v2 (2025) adds multi-agent collaborative screening ("crowd of oracles") and ~24% loss reduction. *Strengths:* open, transparent, academically validated, strong active-learning prioritization + WSS, free, large community, simulation mode for benchmarking. *Weaknesses:* **screening-only** (no extraction, no RoB, no PRISMA reporting, no provenance/licence layer), not LLM-rationale-based (classic ML — gives a relevance ranking, not a *why*), not domain-tuned, not provenance/audit-grade for publication defense. ([Nature MI](https://www.nature.com/articles/s42256-020-00287-7); [ASReview LAB v2, Patterns](https://www.cell.com/patterns/fulltext/S2666-3899(25)00166-7); [GitHub](https://github.com/asreview/asreview))

**Abstrackr** — Open-source ML citation-screening tool (older, Brown/CEBM lineage). *Strengths:* free, simple, research-validated. *Weaknesses:* dated, screening-only, limited maintenance, no extraction/provenance. ([Bond LibGuide](https://bond.libguides.com/systematic-reviews/tools))

**SWIFT-Review** — Free ML "workbench" for prioritization/topic modeling (NIEHS/Sciome). *Strengths:* prioritization, topic clustering, free. *Weaknesses:* screening/scoping focus, no extraction/RoB/PRISMA, limited active development. ([tools guide](https://ktdrr.org/resources/sr-resources/tools.html))

**RobotReviewer** — Free ML for RCT RoB + RCT classification. *Strengths:* pioneering auto-RoB, free, evidence-supporting. *Weaknesses:* RoB agreement with humans only ~72% (moderate→slight by domain); reviewers resist adoption; RCT-only; not a workflow platform. ([BMC real-time study](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC9174024/); [Hirt 2021](https://sigmapubs.onlinelibrary.wiley.com/doi/full/10.1111/jnu.12628))

**SR-Accelerator / TERA (Bond IEBH)** — Free modular tools: Polyglot (search translation), dedup, screening (RobotScreener), citation tracking, methods/results drafting. *Strengths:* free, modular, info-specialist-oriented, search-strategy tooling Hee-Lee Oss lacks. *Weaknesses:* not provenance/audit-grade, not a single auditable project artefact, AI components opaque. ([TERA, Bond](https://bond.edu.au/iebh/systematic-review-accelerator-sra); [SRA2 GitHub](https://github.com/IEBH/SRA2))

**Elicit** — LLM research assistant; now markets a "PRISMA 2020" SR workflow; strong at extraction with source linking; recent eval as semi-automated second extractor. *Strengths:* good UX, extraction with citations, fast, PRISMA-aware marketing. *Weaknesses:* closed/SaaS, cloud (closed full text leaves machine), **hallucination 1–6% incl. rounding/labelling misrepresentation**, accuracy drops on review-specific variables (73%), not licence/provenance-gated, not cancer-tuned. ([Elicit PRISMA blog](https://elicit.com/blog/systematic-review-for-prisma-2020); [proof-of-concept](https://journals.sagepub.com/doi/10.1177/08944393251404052); [feasibility study](https://www.cambridge.org/core/journals/research-synthesis-methods/article/using-elicit-ai-research-assistant-for-data-extraction-in-systematic-reviews-a-feasibility-study-across-environmental-and-life-sciences/C97DAEC70C3173A260F0B12E729E7250))

**Cochrane RevMan / Cochrane tools** — Synthesis/meta-analysis + write-up (correctly out-of-scope for Hee-Lee Oss as a hand-off target). Cochrane is also driving RAISE and the AI-in-evidence-synthesis platform study — i.e. the *standards body* Hee-Lee Oss should align to, not compete with. ([Cochrane AI](https://www.cochrane.org/about-us/news/cochrane-announces-selected-ai-tools-innovative-platform-study))

**LLM-for-SR research wave** — A 172-study review confirms GPT/LLMs are the dominant automation architecture, concentrated on search and extraction; screening accuracy is variable and criterion-dependent. Signal: the field is moving fast and *unevenly*, and rigor/transparency (not raw capability) is the open gap. ([JAMIA review](https://academic.oup.com/jamia/article/32/6/1071/8126534); [PNAS](https://www.pnas.org/doi/10.1073/pnas.2411962122))

---

## 3. Gaps we can fill

1. **Open + extraction + RoB + PRISMA in one auditable artefact.** The only strong *open* competitor (ASReview) is screening-only; the full-lifecycle tools are closed/paid. An open, end-to-end, provenance-first project store is an empty niche.
2. **Provenance-grade defensibility.** No competitor ships a per-assertion `Provenance` record (source + span + model/version + actor + timestamp) plus a reproducibility manifest as a *first-class, tested* artefact. This is exactly what peer reviewers and RAISE now demand.
3. **Local-first privacy.** Covidence/Rayyan/EPPI/DistillerSR/Elicit are cloud SaaS; closed full text leaves the machine. Local library+CLI+portable store is a genuine, defensible differentiator for privacy/TDM-compliance and low-resource/offline use.
4. **Licence/rights gate as code.** No competitor enforces per-source licence verification + CC-BY-NC non-relicensing + TDM compliance + PII quarantine at the ingest boundary. This is a unique trust surface, especially for the cancer-data-licence minefield (TCGA/GEO/PRIDE open vs COSMIC/OncoKB non-commercial).
5. **Cancer-tuned validation.** Generic tools aren't oncology-validated; an open, worked cancer reference review + cancer eligibility/extraction schemas (TNM staging, response criteria, survival endpoints) is differentiated and reusable.
6. **Standards alignment as a feature.** Auto-generated RAISE/position-statement disclosure block + PRISMA-S search report + PRISMA 2020 checklist mapping — turning compliance paperwork into a one-click output no one else produces.
7. **Recall-first *guarantee* (no auto-exclude) as architecture, not setting** — DistillerSR markets "verifying exclusions"; Hee-Lee Oss's structural impossibility of AI exclusion is a sharper, more trustworthy promise.

---

## 4. Differentiators to win (incl. vs ASReview)

**Single strongest differentiator:** *the only open-source SR-assist tool where every AI assertion is provenance-bound (verbatim span + model/version + human actor) and AI exclusion is structurally impossible — producing a publication-defensible, reproducible, RAISE/PRISMA-S-aligned audit trail that closed tools can't match and ASReview doesn't attempt.*

- **vs ASReview:** ASReview gives a *relevance ranking* (no rationale, no extraction, no RoB, no provenance, no PRISMA, no licence layer). Hee-Lee Oss gives **LLM rationales with the specific eligibility criterion + the source span**, *plus* extraction, RoB-evidence, PRISMA artefacts, and a licence/provenance audit in a single portable review artefact. But ASReview's active-learning prioritization + stopping rules are genuinely better-validated than a per-record LLM pass — so **don't compete on ranking; interoperate**: import ASReview's ranking/labels, or offer an ASReview-compatible mode, and add the layers ASReview lacks. Beating ASReview on *its* turf (screening efficiency) is hard; surrounding it is easy.
- **vs Elicit/DistillerSR (extraction):** match their extraction but add *span-faithfulness checks*, *local execution*, *licence gating*, and *full reproducibility* — the things their cloud/closed models can't credibly promise.
- **vs all:** "fails toward inclusion and toward human review," cancer-first validation, free + open + offline, and standards-disclosure-as-output.

---

## 5. Claude API leverage

**Where Claude clearly helps (assist only):**
1. **Title/abstract screening *with rationale*** — Claude's strength is the *explanation*, not just a score: per-record proposed disposition + the **specific eligibility criterion violated/met** + verbatim span + calibrated confidence. This is the headline gap vs ASReview's score-only output. (Use the cheap/fast tier for the triage pass.)
2. **Span-grounded data extraction drafting** — extract typed fields each bound to a verbatim source span + location; Claude's long context + tool-use/structured-output is well-suited to "quote-then-fill," with the value left *proposed-unverified*. Add a second Claude pass as a **span-faithfulness verifier** (does the value follow from the quoted span?) to catch the rounding/mislabelling hallucinations the Elicit literature flagged.
3. **Risk-of-bias *signal extraction*** — surface the verbatim sentences relevant to each RoB 2 / ROBINS-I / QUADAS-2 item for the human to judge (never assign the rating).
4. **PRISMA flow automation + report scaffolding** — reconcile identification→included counts into a PRISMA 2020 diagram, draft the methods/PRISMA-S search narrative and the RAISE AI-disclosure block from the audit log.
5. **Eligibility-criteria refinement** — research shows LLM-refined inclusion/exclusion criteria improve screening; Claude can help the team *sharpen the PICO rules* up front (human-approved), improving recall before any record is screened. ([PNAS](https://www.pnas.org/doi/10.1073/pnas.2411962122))
6. **Dedup adjudication & PII triage** — Claude as a *second opinion* on fuzzy-duplicate pairs and as the flag-raiser for the PII/identifiable-data quarantine.

**Where Claude must NOT decide:**
- **No autonomous include/exclude.** Inclusion/exclusion are human decisions; AI cannot finalize a disposition (structural invariant). Recall must never be sacrificed for throughput — fail toward inclusion/human review.
- **No final extraction value** without human verification; **no span → no value**; never invent/round/convert numbers silently.
- **No autonomous evidence synthesis / conclusions**; no RoB rating; no meta-analysis judgment.
- **No fabricated data or unsourced assertion** ever surfaces.
- **Full transparency of AI involvement** per RAISE / the 2025 position statement: every AI-touched step is disclosed, model/version logged, human accountable. Treat paper text as untrusted data (prompt-injection-resistant), library-side policy not overridable by content. ([Position statement](https://www.cochranelibrary.com/cdsr/doi/10.1002/14651858.ED000178/full))

---

## 6. Ten concrete optimizations

1. **Reframe the recall metric around the missing-studies guarantee.** Replace the flat "≥95%" with a methodologist-set, topic-specific target plus a *stopping-rule confidence* ("95% confident the last relevant record is found"), and report combined human+AI recall, not AI-alone. Adopt WSS@100% alongside WSS@95.
2. **Add active-learning prioritization + a documented stopping rule** (or interoperate with ASReview) so the workload-saving claim is methodologically real, not a per-record LLM pass with no stopping semantics.
3. **Validate confidence calibration** (reliability diagram / ECE) and gate the recall-first routing on demonstrated calibration; recalibrate per topic.
4. **Add a span-faithfulness verifier** (second pass) so extraction grounding checks the value *follows from* the span, not merely that a span exists — directly targeting the 1–6% Elicit-style hallucination/rounding errors.
5. **Independent-then-reveal screening UI** to fight automation bias: human records a provisional call before seeing the AI suggestion on an audit subset; track human-AI agreement drift over time.
6. **Map outputs to standards as first-class artefacts:** PRISMA-S search report, PRISMA 2020 27-item checklist mapping, and an auto-generated **RAISE / position-statement AI-disclosure block**. This is a unique, adoption-driving output.
7. **Add a cost+latency model** for 3k–30k-record reviews (token estimates per tier) so the "low-resource setting" claim is honest; offer a cheap-tier triage + selective strong-tier escalation policy.
8. **Persist model outputs (not just prompt hashes)** in the reproducibility manifest, with sampling params, so re-derivation survives model deprecation.
9. **Build an information-specialist surface** (transparent dedup report, optional Polyglot-style search-translation QA hand-off) — a low-risk feature that current open tools own and that strengthens the PRISMA-S story.
10. **Declare and account for non-English / unscreenable records** explicitly in the flow diagram so "out of scope" never becomes a silent recall failure.

---

## 7. Parallel & perpendicular spin-offs

- **Generalized open SR-assist toolkit** — the methodology/provenance/rights core is domain-agnostic; cancer-first validation, then re-validated expansion to other domains (the plan already gestures at this). The licence/provenance/RAISE-disclosure engine is the reusable crown jewel.
- **MCP server for evidence synthesis** — expose screening-suggestion, span-grounded-extraction, and provenance-logging as MCP tools so *any* agent/host (Claude Desktop, IDEs) can drive a human-in-the-loop review while the policy layer enforces no-autonomy. Turns the core into infrastructure.
- **Ties to `pmc-oa-cancer-corpus`** — the PMC Open Access cancer subset is the natural ingest source and the substrate for the worked reference review and the gold-standard fixture; co-develop the corpus as the licensed, provenance-tagged input layer.
- **Ties to `biomarker-extraction`** — the span-grounded extraction schema can specialize into structured biomarker/endpoint extraction (TNM, response criteria, HRs/CIs, survival), feeding both SR extraction and a biomarker dataset.
- **Ties to `ewing-outcomes-harmonization`** — a rare-cancer (Ewing sarcoma) reference review is an ideal pilot topic (high need, scarce reviews, tractable open-access corpus) and a harmonized-outcomes consumer; the SR tool produces the extraction dataset the harmonization project standardizes.
- **Living-review automation** — once an SR exists as a reproducible artefact, a scheduled re-ingest + re-screen of new records (PRISMA-LSR-aware flow diagram) is a natural extension; align to the PRISMA-LSR extension and RAISE living-review guidance. ([PRISMA-LSR](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC12036629/))
- **Eligibility-criteria refinement assistant** — a standalone, low-risk pre-screening tool that sharpens PICO inclusion/exclusion rules (shown to improve screening performance) — useful even to teams who won't adopt the full pipeline.

---

## 8. Open questions for the maintainer

1. **Is 95% recall the right bar, or should it be a confidence-based "no relevant study lost" stopping criterion at a higher level?** What does the target methodologist consider publishable?
2. **Per-record LLM screening vs active-learning prioritization** — will you build/interoperate a stopping-rule prioritizer, or stake the workload claim on a per-record pass (which has no natural WSS@95 semantics)?
3. **ASReview: compete or interoperate?** An import/export bridge or ASReview-compatible mode may be higher-leverage than re-implementing active learning.
4. **RAISE / 2025 position-statement alignment** — will you formally map the tool's transparency artefacts to these, and pursue endorsement/listing (e.g. Cochrane's AI platform study) as an adoption channel?
5. **Cost model for large reviews** — what's the realistic token cost for a 30k-record oncology review, and does it undercut the low-resource-beneficiary promise?
6. **Combined human+AI recall measurement** — will the eval harness measure both AI-alone and human∪AI configurations (the latter is the real value)?
7. **Span presence vs span faithfulness** — will extraction validate that values *follow from* spans, not just cite one?
8. **Pilot topic** — is a rare sarcoma (e.g. Ewing) the pilot, tying to `ewing-outcomes-harmonization` and `pmc-oa-cancer-corpus`?
9. **Model deprecation** — how is reproducibility preserved when the Claude model version used is retired?
