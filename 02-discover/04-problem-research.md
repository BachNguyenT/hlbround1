# Problem Research — Root Cause Analysis
**Product:** HLB Point-of-Purchase Credit Decisioning (working name) | **Skill:** 04 | **Status:** Done (2026-09-29)
**Depends on:** 01-product-strategy.md, 02-market-research.md, 03-user-research.md, literature-review.md | **Feeds into:** problem-statement.md, solution-proposal.md (preliminary)

> This skill is synthesis-first: Skills 02–03 and the literature review already conducted primary research at full depth. Findings here are built on their evidence rather than duplicating it; citations of the form `[Skill02 S#]`, `[Skill03 S#]`, `[Skill01 S#]`, `[L#]` point back to those files' Source Indexes. New evidence gathered specifically for this skill is cited `[N#]` in a short additional Source Index below. Rubric tags: `[R1]` Diagnosis · `[R2]` Justification · `[R3]` Concept · `[R4]` Assumptions.

---

## Pre-filled Context
- **Problem hypothesis (Skill 01):** HLB Vietnam's point-of-purchase lending relies on a branch-style, document- and bureau-based manual review (380,000 VND/application, 1.8–4.6 business days) that cannot assess thin-file applicants, cannot decide within the purchase moment, and costs too much per decision relative to small-ticket loans (1.6M–90.1M VND).
- **Top pain points (Skill 03):** four pain points scored the maximum Opportunity Score of 9 — "prove your income" doesn't fit how thin-file segments earn (#1); invisible to the bureau (#2); MSME cash flow seen but not recognised (#3); decision arrives after the purchase/stock moment (#4); plus two operator-side 9-scores — no standard data for thin-file cases (#12) and manual file assembly (#13). The read-across note in `03-user-research.md` §3 already points at one shared mechanism: *"Every file gets the same document-and-bureau manual review, so thin-file income evidence is never used (borrower side) and the officer re-assembles every case by hand (operator side)."* This skill tests that pointer rather than assumes it.

---

## 1. The Four Bundled Sub-Problems, Restated

| Code | Brief requirement | One-line problem |
|---|---|---|
| **P1** | R-1: assess creditworthiness beyond the bureau file | HLB cannot score gig workers, MSMEs, first-time borrowers and salaried-no-credit customers, even when they earn and transact digitally |
| **P2** | R-2: in-moment decision at proportional cost, with risk quality maintained | The fixed 380,000 VND / 1.8–4.6-day manual review is too slow and too expensive for small tickets |
| **P3** | R-3: real-time identity and application-genuineness verification without document friction | Document-based verification is slow, forgeable, and drives abandonment |
| **P4** | R-4: target customers likely to need, accept and responsibly use credit | HLB cannot yet tell which ecosystem customers to offer credit to, at what size, without broadcasting or over-lending |

---

## 2. Problem Hierarchy & 5 Whys

### P1 — Thin-file creditworthiness assessment

```
Symptom: Gig workers, online merchants and first-time borrowers are declined or never offered
credit, even when officers can see cash flow, and even though these three segments make up
44.0% of inbound applications (Gig 15.0% + MSME 14.5% + FTB 14.5%) [Brief p.9].

5 Whys:
1. Why are they declined? → Assessment draws only on "formal credit bureau history and
   collateral" [Brief p.5], and CIC bureau coverage reaches ~60% of adults on a 2022 figure that
   is now dated [Skill03 S35; OQ-03-07/OQ-02-13] — so a large minority has no scorable file, and
   the brief states first-time borrowers "cannot be assessed by standard scorecards" [Brief p.8].
2. Why doesn't the process use the income these segments do generate? → Platform income,
   e-wallet activity, and online sales histories "exist as records within the bank, its partners,
   or third parties, but a document-based review does not draw on them" [Brief p.5]. The evidence
   the officer sees (operational cash flows) is not a standardised, policy-approved input to the
   decision [Skill03 Pain #3, #12].
3. Why is there no standardised, policy-approved way to use it? → No consented data pipeline or
   scoring policy exists yet to turn platform/CASA/sales data into a defensible "approve" — a
   Vietnamese banker is quoted saying SME evaluation "lacks standardized systems for assessment"
   [Skill03 S38], and a finco CEO frames national trustworthiness scoring as filling exactly this
   gap for customers "chưa từng có lịch sử tín dụng" (never had a credit history) [Skill03 S34].
4. Why hasn't HLB built this already? → [Inference, not stated in the brief] The current journey
   was "designed for branch lending" [Brief p.4], a context where bureau + collateral is normal and
   sufficient; point-of-purchase lending to digitally-active, thin-file customers is a newer use
   case the process was never redesigned for.
5. Why does branch-lending logic still govern a checkout product? → Because no one has yet been
   forced to separate "who is creditworthy" from "who has a bureau file" as two different
   questions for this product line — the case brief itself is HLB's attempt to force that
   separation [Brief p.6].

Evidence: [Brief pp.5,7,8] · [Skill03 Pain #1 (Opp.9), #2 (Opp.9), #3 (Opp.9), #12 (Opp.9)] ·
[Skill02 §1.2 Fundiin×CIMB thin-file product, S15] · [L1, L2, L4, L9 — alternative-data lift is
largest for thin files]

Competitor attempts: Fundiin's Feb 2025 "Trả góp Tối ưu" with CIMB explicitly targets thin-file
customers using "credit records and transaction data" [Skill02 S15]. Home Credit and FE Credit
price thin-file risk into rate rather than declining outright ("CCCD only, no income proof",
finco-level pricing ~25–35% APR) [Skill02 §1.2]. No sourced Vietnamese bank (as opposed to finco)
does cash-flow/alternative-data underwriting for this segment yet [Skill02 Whitespace 2] —
supporting that this is unaddressed whitespace, not a solved problem being late-copied.
```

### P2 — Decision speed and cost proportional to loan size

```
Symptom: Every application gets the same manual, centralised review — 380,000 VND and
1.8–4.6 business days — regardless of loan size (1.6M–90.1M VND) or segment risk clarity
[Brief pp.5,9]. For the smallest tickets the review costs more than the loan earns [Brief p.5].

5 Whys:
1. Why does a 1.6M VND ticket cost the same to review as a 90.1M VND ticket? → The workflow has
   no segmentation or fast lane: "Each review is performed manually" with a "fixed cost per
   application, regardless of loan size" [Brief p.5].
2. Why is there no fast lane for clearly low-risk cases (e.g. a salaried applicant with 5 years of
   clean CIC history)? → The process routes 100% of applications through the same
   document-submission → verification → officer-review sequence [Brief p.4]; no automated
   straight-through path exists today, unlike competitors: ACB auto-approves ~60% of applications
   in 5–10 minutes "based on pre-set criteria" and Home Credit sends only lower-score cases to a
   human [Skill03 S36, S46].
3. Why is the review manual rather than automated even for easy cases? → [Inference] The journey
   was built for branch lending, where volume is lower and per-application human review is
   proportionate; it was not redesigned when point-of-purchase volume (5 inbound segments,
   [Brief p.9]) was added.
4. Why does the fixed cost make small tickets uneconomic? → Derived: at a market-representative
   net spread (~29.8% nominal APR less ~4.8% bank cost of funds less ~11.5% finco-level credit
   cost), a 3-month loan needs a ticket of about 16.9M VND just to cover the 380,000 VND review;
   a 6-month loan needs about 9.6M VND [Skill02 §2A]. HLB's range starts at 1.6M VND — below every
   sourced break-even scenario at short tenures [Skill02 §2A table].
5. Why does this matter operationally, not just financially? → Because the decision also arrives
   "after the purchase moment has passed" [Brief p.5] — cost and speed are the same symptom
   (a manual, undifferentiated process), not two separate problems.

Evidence: [Brief pp.4,5,9] · [Skill02 §2A break-even table, F2] · [Skill03 Pain #4 (Opp.9), #13
(Opp.9), B14] · [L5, L30 — automated lenders decide faster without higher defaults]

Competitor attempts: every sourced Vietnamese pay-later/POS competitor decides in 5 seconds to
~30 minutes (Home Credit 3 min, Home PayLater 60 s, MoMo 3 min, MWG PayLater 2 min, Fundiin ~5 s)
[Skill02 F1]. None of them uses a multi-day, uniform manual process for point-of-purchase tickets.
```

### P3 — Real-time identity and application-genuineness verification

```
Symptom: Verification today relies on physical or scanned documents [Brief p.4], which is
forgeable and adds friction that "causes applicants to abandon the process" [Brief p.6].

5 Whys:
1. Why is document-based verification a problem? → It is slow (part of the multi-day cycle),
   loses to biometric-first competitors, and does not reliably catch fraud: a finco agent forged
   455 ID cards and submitted 607 loan applications because the app "could not verify the personal
   information and photo on ID cards" [Skill03 S31]; an insider bank appraiser fabricated 20 credit
   files worth ~48.8bn VND [Skill03 S32].
2. Why hasn't HLB moved to biometric/chip-ID verification already? → [Inference — not stated in
   brief.] Chip-CCCD/VNeID biometric authentication only became mandatory for parts of the sector
   from 2024–2026 [Skill02 RF-4, RF-5]; the case brief's baseline process pre-dates full adoption
   of this infrastructure for this product line.
3. Why does this matter more for point-of-purchase lending specifically than for branch lending?
   → Branch lending has an in-person moment where a document can be checked face-to-face; a
   checkout/digital journey has no equivalent, so the document step becomes pure friction with a
   weaker fraud guarantee, not a stronger one.
4. Why is P3 evidenced as "friction" more than as "the cost driver"? → The brief frames P3 as
   about abandonment and genuineness ("without introducing the document friction that causes
   applicants to abandon" [Brief p.6]), not as the source of the 380,000 VND cost or the multi-day
   delay — those are attributed to the manual bureau/document *review*, a related but distinct
   step from identity verification itself.
5. Why keep P3 as a separate problem rather than folding it into P1/P2? → Because it has its own
   mechanism (forgeable documents, no real-time check) and its own regulatory anchor (mandatory
   biometric eKYC circulars [Skill02 RF-4, RF-5]), even though relieving it is a precondition for
   safely relieving P1/P2 at speed (see §4).

Evidence: [Brief pp.4,6] · [Skill03 Pain #5 (Opp.4), #14 (Opp.6, Partial)] · [Skill02 §1.3 row
"Documents", RF-4, RF-5] · [L8 — biometric ID cuts onboarding cost and fraud]

Competitor attempts: every in-moment competitor pairs a fast decision with chip-ID/CCCD-only
input; none requires physical documents at the point of decision [Skill02 §1.3]. SBV reported
fraud incidents down ~50% and fraud-receiving accounts down ~72% after biometric authentication
became mandatory [Skill01 S5 / Skill02 regulatory research].
```

### P4 — Targeting who needs, accepts and responsibly uses credit

```
Symptom: HLB has no stated mechanism to identify, within merchant/partner ecosystems, which
customers are likely to need, accept and responsibly use embedded credit, so — by implication —
offers today are not targeted [Brief p.6].

5 Whys:
1. Why does untargeted offering matter? → Two Vietnam-evidenced failure modes on either side of
   "no targeting": (a) creditworthy customers who could use credit responsibly are not reached
   with a relevant offer, and (b) broadcast/easy credit reaches people who over-borrow — a student
   accumulated >100M VND of debt across banks, fincos and illegal apps [Skill03 S16], and 46% of
   borrowers don't know late payment affects their credit rating [Skill03 S18].
2. Why is targeting hard here specifically? → Segments differ sharply in observability: SAL-C
   customers are "most visible" via existing account data, while gig workers, MSMEs and
   first-time borrowers are "low" to "almost invisible" until they apply [Brief pp.7–8] — so the
   population HLB most wants to reach responsibly is the population it can see least.
3. Why can't existing bureau-based screening do this targeting? → CIC-based risk screening
   answers "is this person likely to default", not "does this specific purchase moment suit this
   person, and will an offer here be accepted and used responsibly" — a different question,
   requiring behavioural/propensity signals the bureau doesn't carry [L10, L64].
4. Why hasn't a Vietnamese bank solved this yet? → No sourced Vietnamese bank does embedded,
   propensity-based targeting for point-of-purchase credit; wallet in-app limits and Bajaj-style
   pre-approved EMI offers are the closest analogues, both from outside this exact context
   [Skill02 Competitive Minimum Bar].
5. Why treat P4 as lower-leverage than P1/P2 for this diagnosis? → Because P4 assumes P1 and P2
   are already solved: you cannot target "who to offer embedded credit to" until you have a way
   to decide those offers in-moment and beyond the bureau file. P4 is a value-add layered on a
   working decision engine, not a substitute for building one.

Evidence: [Brief pp.6,7,8] · [Skill03 Pain #9 (Opp.4), #11 (Opp.6), B2, B13] · [L10, L12, L62, L64
— uplift/propensity targeting and framing effects]

Competitor attempts: none sourced for Vietnam specifically; global analogues (Bajaj Finserv
pre-approved EMI, e-wallet in-app limits) exist but weren't independently verified in depth here
[Skill02 Competitive Minimum Bar row "Pre-approved limits and targeted offers"].
```

---

## 3. Bottleneck Mechanism Analysis

### 3.1 The current journey, mapped

```
[Merchant / partner checkout]
        │  customer wants to pay over time [Brief p.4]
        ▼
[Redirect out of the purchase flow into a separate, branch-designed application process] [Brief p.4]
        │
        ▼
[Physical or scanned document submission] ── P3 friction begins; abandonment risk starts here
        │
        ▼
[Verification] ── forgeable; no real-time chip-ID/biometric check evidenced in the baseline
        │
        ▼
[Credit officer review against CIC bureau records + collateral] ── P1 begins here: alternative
        │        signals (platform income, e-wallet activity, sales history) are NOT drawn on,
        │        even where they exist in the bank/partner/third-party systems [Brief p.5]
        ▼
[Manual file assembly, verification calls, write-up] ── labour-intensive step, evidenced
        │        generically by Vietnamese bank practice (VietinBank cut file-prep from 4–5 days
        │        to ~half a day with ML [Skill03 S37]; ACB auto-approves ~60% [Skill03 S36]) but
        │        NOT broken down for HLB itself (OQ-03-03) — see open items.
        ▼
[Decision, 1.8–4.6 business days later, 380,000 VND cost, same for every ticket size] [Brief p.9]
        │
        ▼
[Purchase moment has usually passed] → customer delays, abandons, or seeks credit outside the
        formal banking system [Brief p.4] → for MSMEs specifically, a missed stock/inventory
        window [Skill03 Pain #4, S40]
```

**What is and isn't evidenced.** The brief and upstream research establish the *shape* of this chain (document submission → verification → officer/CIC review → decision) and its aggregate cost/time (380,000 VND; 1.8–4.6 days). Neither the brief nor any sourced Vietnamese material gives HLB's own breakdown of where inside that chain the 380,000 VND and the days actually go (labour vs. CIC/vendor query fees vs. verification calls vs. write-up). This is inherited as open item OQ-03-03 / OQ-02-01–03 and is not invented here.

### 3.2 Unit economics: how the fixed cost interacts with ticket size and segment mix

Reusing Skill 02's sourced/derived break-even table (§2A), at a market-representative net spread (~29.8% nominal APR − ~4.8% bank cost of funds − ~11.5% finco-level credit cost):

| Tenure | Break-even ticket to cover the 380,000 VND review alone (derived, [Skill02 §2A]) | HLB's minimum ticket (1.6M VND) is… |
|---|---|---|
| 3 months | ~16.9M VND | ~9.5× below break-even |
| 6 months | ~9.6M VND | ~5.4× below break-even |
| 12 months | ~5.2M VND | ~2.9× below break-even |

Because HLB's tenure menu runs 3–24 months and its ticket range runs 1.6M–90.1M VND [Brief p.9], and because no HLB-specific distribution of ticket size by tenure or by segment exists in the brief, the exact share of the 380,000-VND-losing zone cannot be computed precisely — only bounded. `[R4 Assumptions]` **Working assumption (stated, not silent):** thin-file segments (gig, MSME, first-time — 44.0% of inbound [Brief p.9]) are more likely to request smaller, shorter-tenure tickets than salaried segments, because they are new to formal credit and because Skill 03's evidence shows their credit use is need-timed and small (e.g. a gig worker's 11.6M VND phone loan repaid daily [Skill03 S1]; MSME stock-timed purchases [Skill03 S40]). If this assumption holds, the segments most excluded by P1 are disproportionately also the segments most exposed to the P2 break-even problem — the two sub-problems compound on the same population rather than affecting separate ones. This compounding is the core empirical claim behind the bottleneck verdict in §4, and it should be validated against HLB's actual segment-by-ticket-size data before the final submission if that data becomes available (`[DECISION REQUIRED]`, carried to Skill 22).

### 3.3 Segment × Problem Matrix

| Segment | Share of inbound [Brief p.9] | P1 Thin-file assessment | P2 Speed/cost | P3 Identity/fraud/friction | P4 Targeting |
|---|---|---|---|---|---|
| **SAL-C** (salaried, credit history) | 35.5% | Low — bureau history exists [Brief p.7] | **Medium** — even the easiest cases wait 1.8–4.6 days; pure process waste, no P1 justification | Low–Medium — still needs identity check, lower fraud concern | Low — already reached (cross-sell fatigue, Skill03 Pain #9) |
| **SAL-N** (salaried, no credit history) | 20.5% | **Medium–High** — "requires assessing regular income inflows" [Brief p.7] | High | Medium | Medium |
| **GIG** (gig/platform workers) | 15.0% | **High** — "lacks traditional documentation" despite "consistent purchasing patterns" [Brief p.7] | High | Medium | **High** — volatile income needs affordability-aware, not blanket, targeting [Skill03 Pain #10] |
| **MSME** (online merchants) | 14.5% | **High** — cash flow observed but not recognised [Brief p.8] | High, and time-critical (stock windows) [Skill03 Pain #4] | Medium — business/shop identity genuineness | Medium |
| **FTB** (first-time digital borrowers) | 14.5% | **High** — "cannot be assessed by standard scorecards" [Brief p.8] | High | **High** — most exposed to identity misuse (biometric-harvesting fraud case, Skill03 S17) and most abandonment-prone | **High** — over-borrowing/debt-spiral risk is concentrated here [Skill03 Pain #11] |

**Reading:** P2 (speed/cost) bites every segment, including the 35.5% that P1 doesn't touch at all — this is the strongest evidence that the process itself, not just thin-file risk assessment, is the shared mechanism. P1, P3 and P4 concentrate on the 44.0% thin-file share plus SAL-N (64.5% combined), with FTB carrying the highest combined burden across P1, P3 and P4.

---

## 4. Core Bottleneck Verdict

> **The core bottleneck is the single, undifferentiated, manual, document-and-bureau-anchored underwriting workflow — one process applied to every application regardless of segment, ticket size, or how much decision-relevant signal already exists — inherited unchanged from branch lending into the point-of-purchase journey.** `[R1 Diagnosis]`
>
> **Confidence: High**, on the mechanism. **Medium**, on the exact magnitude of relief achievable, because HLB's own cost breakdown, approval/default rates and ticket-size distribution are not in the brief (OQ-02-01–03, OQ-03-03).

**Why this, and not P1, P2, P3 or P4 individually, as "the" bottleneck:** P1 and P2 are two symptoms of the same root cause, not two separate problems — §3.3 shows P2 (speed/cost) affects 100% of volume including the 35.5% with no P1 issue at all, while P1 (thin-file exclusion) affects only the segments the *same* undifferentiated process cannot evaluate. Fixing the workflow (adding a triage/fast-lane step and a consented alternative-data pathway) is the one intervention that relieves both, which is exactly the rubric's ask for "the tightly coupled decision point(s) with the highest operational leverage" [Brief p.11].

**Where P3 and P4 fit:** P3 (identity/genuineness) is a **precondition**, not a competing bottleneck — you cannot safely speed up or broaden the workflow in §4's fix without a real-time identity layer, or speed gains would come with new fraud exposure (evidenced by the two forged-document cases, [Skill03 S31, S32]). P4 (targeting) is a **value-add layered on top** — it presupposes a working in-moment decision engine to target offers into, and the brief's own ordering places it last among the four requirements [Brief p.6]. Both are necessary to a complete solution (Skill 12 onward must design for all four), but neither is the highest-leverage lever on its own.

**What would change this verdict:**
1. If HLB's own data showed the 380,000 VND cost is dominated by non-labour components (e.g. fixed CIC/vendor query fees that don't fall with automation), P2's relief from a workflow redesign would be much smaller than assumed here, and the verdict would shift toward P2 being a pricing/vendor-contract problem rather than a process problem. **Not verified — OQ-04-03.**
2. If back-testing showed that most declined thin-file applicants genuinely are bad risks (i.e. officers are declining correctly, not defaulting to caution for lack of a defensible policy), P1 would be a smaller opportunity than §3 estimates, and the bottleneck would shift toward P2 alone. **Not verified — OQ-04-01.**
3. If Vietnam's data-protection or AI-Law regime turned out to make consented alternative-data scoring impractical at HLB's timeline (rather than merely requiring consent UX and governance, as Skill 02's regulatory research suggests — RF-2, RF-9, RF-10), the bottleneck would shift from "process design" to "regulatory feasibility." Skill 02's reading is that the rules are enabling, not blocking, but this rests on the current interpretation of Decree 94/2025 and the AI Law's grace period, both flagged open in Skill 02 (OQ-02-11, OQ-02-17).

---

## 5. The Non-AI Test

**Question:** Could a lean process or rule-based redesign relieve the core bottleneck without machine learning? The competition rubric requires this test explicitly: AI is credited only if it shows "clear, incremental value compared to the current operational baseline" [Brief p.10], and a non-AI answer is scored on the same bar as an AI one [Brief p.11].

**Finding: yes, largely.** The literature review's clearest and best-evidenced takeaway is that **most of the value in alternative-data underwriting comes from the data, not the model.** A fintech lender's data-driven model cut rejection probability sharply against a traditional model — and about two-thirds of that difference came from the additional data, not the more sophisticated method [L9]. Consistently, ensembles like XGBoost outperform logistic scorecards only modestly in benchmark studies, and deep learning adds nothing further [L18, L19]. Digital-footprint variables alone (device, channel, time-of-day, email) discriminate about as well as a bureau score, and the discriminatory power holds up for applicants with no bureau score at all [L1].

This means a **rules engine or logistic scorecard, applied to a genuinely new set of consented inputs** (CASA/salary inflows, partner platform income, e-commerce sales/e-invoice data, plus the existing CIC file), **plus a tiered approve/refer/decline routing** (so only the genuinely uncertain band reaches a human — the pattern ACB and Home Credit already run in Vietnam [Skill03 S36, S46]) would relieve most of both P1 and P2 without requiring a machine-learning model:
- **P1 relief:** officers gain a standardised, policy-approved way to say "yes" on cash flow they can already see [Skill03 Pain #3, #12], even under simple rules (e.g. minimum months of observed inflow, inflow regularity thresholds).
- **P2 relief:** a rules-based fast lane removes the fixed-cost, multi-day step for the clear-cut majority of cases (starting with the 35.5% SAL-C segment, where no P1 issue exists at all), concentrating the 380,000-VND-equivalent human effort only on the refer band.

**Where AI plausibly adds value beyond that baseline, per the evidence:**
- **Scoring precision at the margin** — gradient-boosted models modestly outperform logistic scorecards in general benchmarks [L18, L19], which matters most exactly where a rules cutoff would otherwise misclassify borderline thin-file cases.
- **P4 targeting** — uplift/propensity-based targeting (who benefits from an offer, not just who is low-risk) is a machine-learning-native technique with weaker rules-based substitutes [L10, L62, L64]; this is the requirement where AI's case is strongest.
- **Fraud/identity signals beyond chip-ID matching** — behavioural biometrics and anomaly detection are AI-native; simple rules catch known patterns (e.g. the two forged-document cases) but not novel ones.

**Conclusion for Skill 12:** the honest, rubric-aligned framing is that **the process redesign (tiered flow + consented alternative data) is the primary lever, achievable without AI**, while **AI's incremental-value case should be argued specifically for scoring precision at the margin and for P4 targeting/fraud**, not asserted generically. Skill 12 (preliminary) must therefore present at least one option that is non-AI or AI-light, and score any AI-heavier option explicitly against this baseline rather than against the (already-relieved-by-rules) status quo. `[R2 Justification]`

---

## 6. Problem Severity × Frequency Matrix

Following Skill 03's convention: **Frequency** and **Severity** are rated High=3 / Medium=2 / Low=1 (Frequency = share of inbound volume affected; Severity = how binding the problem is for that volume, per the case's four requirements). **Opportunity Score = Frequency × Severity** (1–9). We do not use Ulwick's Importance–Satisfaction formulation, because no user survey gives those two ratings for this product, and inventing them would break the zero-assumption rule — this is the same deviation Skill 03 made, for the same reason, applied consistently here. **Evidence Strength** reuses Skill 03's Confirmed / Partial / Hypothesis rule (3+ / 2 / 1 independent source types).

| Problem | Frequency | Severity | Evidence Strength | Opportunity Score | Rolls up Skill 03 pains |
|---|---|---|---|---|---|
| **P1 — Thin-file assessment** | High (bites 64.5% of inbound: SAL-N+GIG+MSME+FTB [Brief p.9]) | High (drives outright decline, not just delay) | **Confirmed** | **9** | #1, #2, #3, #12 (all Opp. 9) |
| **P2 — Speed/cost proportional to ticket** | High (bites 100% of inbound, including SAL-C) | High (loss-making below break-even; purchase moment lost) | **Confirmed** | **9** | #4, #13 (both Opp. 9) |
| **P3 — Identity/genuineness without friction** | Medium (universal exposure, but lower per-case severity than P1/P2 for most applicants) | Medium–High (safety precondition; two documented fraud cases) | Medium (Confirmed for friction; Partial for fraud rate) | **~5–6** | #5 (Opp. 4), #14 (Opp. 6) |
| **P4 — Targeting** | Medium (concentrated in FTB/GIG; less relevant to SAL-C) | Medium (over-borrowing harm is high-severity but lower-frequency; under-targeting is diffuse) | Medium (Confirmed for over-borrowing harm; Hypothesis for positive-targeting upside) | **~5** | #9 (Opp. 4), #11 (Opp. 6) |

**Reading:** P1 and P2 tie at the maximum score and, per §4, are mechanistically the same lever — this is the quantitative face of the qualitative bottleneck verdict. P3 and P4 sit meaningfully lower but are not negligible; both are load-bearing for a complete, responsible solution (P3 as precondition, P4 as value-add), consistent with §4's ordering.

---

## 7. Bottleneck-Relief KPI Candidates

Per the rubric's requirement for "operational KPIs that directly measure bottleneck relief, avoiding disconnected macro statistics" [Brief p.11]. Each ties to a named sub-problem and states the sourced baseline or flags where none exists.

| KPI | Definition | Relieves | Baseline (sourced) | Target |
|---|---|---|---|---|
| **In-session decision rate** | % of applications decided while the customer is still in the checkout/application session | P2 | 0% (1.8–4.6 business days today) [Brief p.9] | `[DECISION REQUIRED]` — Skill 10 |
| **Straight-through-processing (STP) share** | % of decisions made with no human touch | P2 | 0% ("centralised manual review", 100% human) [Brief p.5] | `[DECISION REQUIRED]` — Skill 10, informed by refer-band sizing (OQ-04-02) |
| **Blended cost per decision (VND)** | Total decisioning cost ÷ number of decisions, across the full segment mix | P2 | 380,000 VND, flat [Brief p.9] | Target should be proportional to ticket size, not a single flat number — `[DECISION REQUIRED]` |
| **Thin-file approval rate at controlled risk** | Approval rate for GIG+MSME+FTB (44.0% of inbound), held against an MOB3 30+ delinquency ceiling HLB sets | P1 | Not numerically stated — brief says only "lowest approval rates" [Brief pp.7–8] | `[DECISION REQUIRED]` — no numeric baseline exists to target against (OQ-01-… /OQ-03 family) |
| **Refer-band share** | % of applications routed to human review under a tiered flow (the band that still needs judgment) | P1 + P2 | No current equivalent (100% is manual today) | To be set from back-testing — this number directly determines how much of the 380,000 VND is actually avoidable (OQ-04-02) |
| **Application completion rate** | % of started applications that reach submission | P3 | Not measured for HLB; foreign proxies only (Signicat: 68% of applicants abandon a financial application *somewhere*; Europe) [Skill03 S24] | `[DECISION REQUIRED]` — needs an HLB or partner funnel baseline (OQ-03-01) |
| **Median / p90 time-to-decision** | Wall-clock time from application start to decision | P2 | Range only: 1.8–4.6 business days [Brief p.9] (not a median/p90 — the brief gives a range, not a distribution) | `[DECISION REQUIRED]` |

---

## 8. Problem Statement Candidates

Three framings of the same underlying bottleneck, offered for Skill 06 to choose among or synthesise. Each is fully traceable to the evidence above.

**Candidate 1 — Process/root-cause framing (recommended, most rubric-aligned for R1):**
> HLB Vietnam applies one manual, document-and-bureau-anchored underwriting workflow to every point-of-purchase application, regardless of segment or ticket size — so thin-file customers whose income is visible in digital records are declined, and every ticket, however small, carries the same multi-day, fixed-cost human process. This single process gap is what makes point-of-purchase lending both under-inclusive and commercially unviable for HLB's target ticket range.

**Candidate 2 — Persona/inclusion framing:**
> Gig workers, online merchants and first-time digital borrowers in Vietnam generate real, observable income and transaction signals — through platform payouts, e-commerce sales, and e-wallet activity — but HLB's credit process can only recognise bureau history and collateral. As a result, it declines applicants its own officers can see are earning, losing 44.0% of inbound demand to a process limitation rather than a genuine credit-risk finding.

**Candidate 3 — Unit-economics framing:**
> A fixed 380,000 VND manual review cost, applied uniformly, makes short-tenure loans below roughly 5–17M VND (depending on tenure) structurally loss-making on the review alone — before any credit loss is counted. Since HLB's stated ticket range starts at 1.6M VND, HLB cannot profitably serve the point-of-purchase moment its own customers are asking for, independent of how creditworthy any given applicant is.

---

## 9. Open Items Raised by This Skill

| # | Item | Why it matters | Suggested validation / owner | Carry to |
|---|---|---|---|---|
| OQ-04-01 | No data (HLB's own or third-party) shows what share of today's declined thin-file applicants would clear a rules-based cash-flow/alternative-data policy. The §3.2/§4 compounding claim (thin-file segments are also disproportionately small-ticket) is a stated, evidence-consistent assumption, not a measurement. | Directly sizes how much of P1's Opportunity Score (9) is actually recoverable. | Back-test a candidate policy against HLB's historical declined-application file, if accessible; otherwise state as an explicit assumption with a sensitivity range. `[R4 Assumptions]` | Skill 07 / 12 / 22 |
| OQ-04-02 | The size of the "refer band" under a tiered auto/human flow — i.e. what share of applications genuinely cannot be auto-decided even with better data — is unknown. This determines how much of the 380,000 VND blended cost is actually avoidable. | Central to quantifying P2's relief and to the "Intervention Justification" rubric criterion (30% weight) [Brief p.11]. | Champion–challenger back-test once a scorecard/rules policy is drafted (Skill 12–14). | Skill 12 / 14 / 09 |
| OQ-04-03 | Whether the 380,000 VND cost is dominated by officer labour time, CIC/bureau query fees, verification-call costs, or something else is unknown (inherits Skill 03's OQ-03-03). | Without this breakdown, "automation cuts cost by X%" can only be bounded, not stated as a point estimate. | Ask HLB/organisers for a cost breakdown, or present a sensitivity range in the final submission. `[R4 Assumptions]` | Skill 09 / 14 / 22 |
| OQ-04-04 | The case brief does not distinguish whether declines of visibly-earning thin-file applicants reflect (a) a process gap (no standardised way to act on the evidence) or (b) a deliberate, uncalibrated risk-appetite choice by HLB for these segments, or both. This diagnosis treats it primarily as (a), consistent with the brief's own framing ("frequently declined despite officers observing operational cash flows" [Brief p.8]), but cannot rule out (b) contributing. | Changes whether the fix is purely operational (workflow + data) or also requires a credit-policy/risk-appetite decision from HLB leadership. | Log as an explicit assumption for the submission; flag for Skill 22 as a standing open question if the user has visibility into HLB's actual risk-appetite stance. `[R4 Assumptions]` | Skill 22 |
| OQ-04-05 | HLB's segment-by-ticket-size distribution (does salaried demand skew to larger tickets, thin-file demand to smaller ones, as assumed in §3.2?) is not in the brief. | Without it, the claim that P1 and P2 concentrate on the same population (not just correlate loosely) is a reasoned inference, not a measured fact. | State as an assumption with rationale (done in §3.2); revisit if segment-level ticket data becomes available. `[R4 Assumptions]` | Skill 22 |

*Inherited, still-open items this skill relies on but does not re-resolve:* OQ-01 (bottleneck ranking — this skill is that ranking), OQ-02-01–03 (HLB's own pricing/cost-of-funds/loss-rate figures), OQ-02-13 (current CIC coverage), OQ-03-03 (officer time-per-file breakdown), OQ-03-07 (dated bureau-coverage figure), OQ-LR-01/03/04 (no Vietnam-specific lift/cost-per-decision benchmark for alternative-data scoring).

---

## Source Index

This skill's claims are carried forward from the Source Indexes of `01-product-strategy.md` (cited `[Skill01 S#]`), `02-market-research.md` (`[Skill02 S#]`), `03-user-research.md` (`[Skill03 S#]`), and `literature-review.md` (`[L#]`) — see those files for full citation details (publisher, title, date, URL). No new web sources were required for this synthesis; all claims either restate the case brief `[Brief p.N]`, restate a prior skill's sourced finding, or are explicitly labelled as this skill's own derived arithmetic or stated assumption (`[R4 Assumptions]`, `[DECISION REQUIRED]`).
