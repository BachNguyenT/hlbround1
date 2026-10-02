# Prioritisation Framework
**Product:** HLB Point-of-Purchase Credit Decisioning (working name) | **Skill:** 11 | **Status:** Done (2026-09-30)
**Depends on:** success-metrics.md | **Feeds into:** solution-proposal.md, epic-feature-decomposition.md (feature scoring), product-prd.md (Section 8)

> **Citation keys.** `[R#]` = entry # in `_research-notes-07-13.md`. `[Brief p.N]` = the case brief via `_requirements.md`. `[Skill0N §x]` = upstream pipeline files. `[L#]` = `literature-review.md`. `[S-11-#]` = new sources for this skill (Source Index at end).
>
> **Rubric tags** are written `[Rubric R1]` to `[Rubric R4]` so they are not confused with `[R#]` research citations. R1 = Problem Diagnosis, R2 = Intervention Justification, R3 = Solution Concept, R4 = Data & Assumptions [Brief p.11].
>
> **Scope guard.** This skill scores and orders features. It does **not** change the v1 boundary. Skill 08's MoSCoW decides the tiers. Where RICE points to a different placement, §4 records it as a **dissenting view** for the user to decide in Skill 22. Nothing is moved silently.

---

## Pre-filled Context

- **Feature universe.** 50 features in Skill 08 §1, coded E-, AW-, CO-, AP-, PR-, RT-, AD-, OP-, MX-, GV- and X- [Skill08 §1].
  - **46 are scored here.**
  - **4 are excluded** because Skill 08 rejected them permanently (see §1.4).
- **v1 boundary [Skill08 §2]:**
  - 29 **Must** features
  - 8 **Should** features
  - 9 **Won't-have-v1** features: 2 blocked on regulatory readiness (E-04, GV-02) and 7 parked until the user assesses Claude's proposed pain points (PL-03 to PL-09)
- **Core bottleneck [Skill04 §4]:** one manual, document-and-bureau review applied to every application, whatever the segment, ticket size or available signal.
  - P1 (thin-file assessment) and P2 (speed and cost) both score the maximum Opportunity Score of 9, and they share this one mechanism.
  - P3 (identity) is a **precondition** for relieving it. P4 (targeting) is a **value-add** on top [Skill04 §4, §6]. `[Rubric R1]`
- **Non-AI test [Skill04 §5]:** a rules or tiered flow on new consented data captures most of the decision-stage value. AI's best-evidenced edge is:
  - scoring precision at the margin [L18][L19]
  - uplift targeting and fraud signals [L10]
- **Pain-point scope rule (user, 2026-09-30):** v1 is designed for the **user-suggested pain points**, which are:
  - Awareness targeting
  - Consideration packages
  - continuous reassessment
  - Retention recommendations
  - Advocacy referral

  Brief requirements R-1 to R-4 and binding conditions C1 to C6 are also mandatory. Claude's proposed pain points stay parked [`_context.md`; Skill08 §2].
- **Skill 10 (Success Metrics)** was still a placeholder when this skill ran. So the tiers here are **not** yet linked to KPIs. Skill 10 should map each Tier 1 feature to the bottleneck-relief KPIs in Skill 04 §7 (OQ-11-08).
- **Skill 14 (Technical Feasibility)** has not run [`_context.md` SD-5]. Every effort figure below is a **judgment-based estimate** (see §0.4).

---

## 0. Scoring Method & Assumptions

### 0.1 Framework source

RICE comes from Intercom [S-11-1]:

> **RICE = (Reach × Impact × Confidence) ÷ Effort**

It measures "total impact per time worked".

- **Impact** uses Intercom's scale: 3 = massive, 2 = high, 1 = medium, 0.5 = low, 0.25 = minimal.
- **Confidence** uses Intercom's anchors: 100% = high, 80% = medium, 50% = low, and below 50% = "moonshot".
- **Effort:** Intercom counts person-months. This pipeline's skill template counts person-weeks, so bands are expressed in person-weeks.
- **Deviation rule.** Intercom says "RICE scores shouldn't be used as a hard and fast rule". Dependencies, table-stakes features and strategy are valid reasons to deviate, provided the trade-off is made visible [S-11-1]. §4 applies that rule.

Kano classification follows Kano et al. (1984) [S-11-2]:

| Kano's term | Name used here |
|---|---|
| Must-be | Basic |
| One-dimensional | Performance |
| Attractive | Delighter |
| Indifferent | Indifferent |
| Reverse | Reverse |

**Limitation of the Kano classification.** The standard Kano method pairs a functional question with a dysfunctional one in a survey [S-11-3]. No such survey exists for this product. The classification in §2 is therefore **inferred from evidence**:
- the competitor minimum bar [Skill02 §5]
- pain-point evidence [Skill03 §1]
- regulation [Skill02 §6]

It is not survey-measured, and it is logged as OQ-11-06. `[Rubric R4]`

### 0.2 Reach: relative, not absolute

The brief gives **no HLB application volumes, customer counts or approval rates** [`_requirements.md` §6]. Reach is therefore a **Reach index from 0 to 100**. The index is the share, in percentage points, of the brief's inbound application mix that belongs to the segments a feature touches [Brief p.9]:

| Segment | Share |
|---|---|
| SAL-C | 35.5 |
| SAL-N | 20.5 |
| GIG | 15.0 |
| MSME | 14.5 |
| FTB | 14.5 |

**It is relative reach, not a count of users.** It ranks features against each other. It cannot be turned into users per quarter until HLB volumes are known (OQ-11-01). `[Rubric R4]`

| Reach basis | Index | Derivation (arithmetic on [Brief p.9] only) | Used for |
|---|---|---|---|
| All inbound (every application or decision) | **100** | All five segments | Decision-path features (every applicant passes through them) |
| **Cohort A proxy** (existing HLB customers) | **56.0** | SAL-C 35.5 + SAL-N 20.5. The brief calls SAL-C "most visible" through existing account data, and SAL-N "visible if existing account holders" [Brief p.7] | Awareness features (AW-01 to AW-04) and RT-04 |
| No-bureau segments | **64.5** | SAL-N + GIG + MSME + FTB | Refer-band exposure (AP-04, OP-01) |
| Thin-file segments (mostly Cohort B, new-to-bank) | **44.0** | GIG + MSME + FTB. The brief says these have low, moderate-to-low and "almost invisible" visibility respectively [Brief pp.7–8] | Starter limit, CASA onboarding, early warning, passport, Phase-3 data |
| Pain #6 segments | **50.0** | SAL-N + GIG + FTB, where Pain #6 is present or primary [Skill03 §1] | AP-09 |
| Salaried-elsewhere / gig inflows | **35.5** | SAL-N + GIG [Skill08 §2, E-03 rationale] | E-03 |
| Volatile-income segments | **29.5** | GIG + MSME, where Pain #10 is primary or present [Skill03 §1] | PR-05 |
| Single segment | 15.0 / 14.5 | GIG (platform partner); MSME (merchant referral); FTB (Pain B12 literacy) | AW-05, AD-02, PR-07 |

**Stated reach assumptions (none are silent):**

1. **Cohort A proxy is an upper bound.**
   - Not every SAL-C or SAL-N applicant holds an HLB account.
   - Some GIG or MSME applicants already bank with HLB, for example through Sổ Bán Hàng [R66].
   - Sensitivity check: at the conservative figure of 35.5 (SAL-C only), AW-01, AW-02, AW-03, AW-04 and RT-04 lose 37% of their score. Tier placements do not change, but AW-01 falls from rank 24 to rank 31, below the RICE cut line (see §1.5 and §4).
2. **Reach counts exposure, not participation.**
   - A referral feature (AD-01) "reaches" every eligible borrower.
   - Participation rates are unknown and are not invented.
3. **Post-approval features have upper-bound reach.** These are PR-, RT-, AD- and CO-01.
   - They touch only approved or pre-approved customers, and per-segment approval rates are not in the brief (OQ-03).
   - The unknown approval-rate factor applies **uniformly** within that group. So rankings *inside* the group hold, but the group is overstated relative to AP- and E- features (OQ-11-03).

### 0.3 Impact anchors (0.25–3), tied to the bottleneck, R-1 to R-4 and user pain points

| Score | Anchor used in this file |
|---|---|
| **3 = massive** | The feature directly relieves the core bottleneck [Skill04 §4]. That means it converts the one undifferentiated manual review into a differentiated, in-moment decision, or it supplies the decision-relevant data that does so. It addresses P1 and/or P2, both Opportunity 9 [Skill04 §6]. It delivers R-1 or R-2 [Brief p.6]. |
| **2 = high** | The feature directly delivers a brief requirement or a precondition for one. Examples: R-3 identity (P3 ≈ 5–6), R-4 targeting (P4 ≈ 5), and responsible-lending protection named in R-2. A feature that directly delivers one of the **five user-suggested pain points** also scores 2. |
| **1 = medium** | The feature enables or protects another feature, meets a legal or governance obligation, or addresses a Claude-proposed pain point scoring Opportunity 4–6 [Skill03 §1]. |
| **0.5 = low** | An incremental improvement on a feature already in scope, or a convenience. |
| **0.25 = minimal** | Not used. No scored feature fell this low. |

### 0.4 Confidence anchors, tied to evidence strength

| % | Anchor |
|---|---|
| **100%** | Brief-mandated (R-1 to R-4 or "fair and transparent cost" [Brief pp.4, 6]) or a legal obligation, **and** the need is Confirmed (3+ independent source types) [Skill03 §1][Skill02 §5/§6]. |
| **80%** | The need or mechanism is Confirmed, or backed by a high-grade peer-reviewed or regulatory source. The Vietnam-specific magnitude is still unverified (for example A1, Vietnam predictive lift, is Medium [Skill06 §4]). |
| **50%** | Partial evidence (2 source types). Or a single high-grade source that is foreign only. Or a user-identified gap whose mechanism is standard but unmeasured (A11 Medium). |
| **30%** | Hypothesis (1 source type). Or a user-identified gap that research rated **Low** (A12, A13). Or the feature depends on an unevidenced partnership or unresolved law. Intercom calls this band a "moonshot" [S-11-1]. |

### 0.5 Effort: a judgment-based estimate, not sourced

> ⚠️ **Effort is a JUDGMENT-BASED ESTIMATE by this skill.** Skill 14 (Technical Feasibility) has not run. No engineering estimate, vendor quote or HLB IT input exists. Treat effort as relative sizing only. Re-score when Skill 14 runs (OQ-11-02). `[Rubric R4]`

| Band | Indicative person-weeks (pw) | Judgment basis |
|---|---|---|
| **S** | 4 | Reuses an existing HLB capability, or is a policy or rule parameter, a display, or a single integration. Examples: HLB eKYC [R67], CIC reporting HLB already performs, a rule inside E-07. |
| **M** | 8 | One new component with one or two integrations, or one statistical model on existing data. |
| **L** | 16 | Several integrations, a new ML model with its monitoring, or a new customer-facing channel. |
| **XL** | 32 | Several models or data partners, plus a regulatory or partner process. |

- The bands double at each step (4 / 8 / 16 / 32), so a one-band error changes a score by a factor of 2.
- **Sensitivity:** if AP-01 (the in-session decision) were M rather than L, its RICE would rise from 18.75 to 37.5 (rank 12 → rank 2). The effort bands for the bottleneck-core features are the least certain inputs in this file.

---

## 1. RICE Scoring Table

**Columns:**
- **Reach** = index 0–100 (§0.2)
- **Impact** = 0.25–3 (§0.3)
- **Conf.** = % (§0.4)
- **Effort** = judgment band → pw (§0.5)
- **Value** = R × I × C, the effort-free lens
- **RICE** = Value ÷ Effort

**MoSCoW** is Skill 08's decision and is not changed here.

### 1.1 Engine core (E-)

| Code | Feature | Reach | Impact | Conf. | Effort† | Value | **RICE** | MoSCoW | Scoring rationale |
|---|---|---|---|---|---|---|---|---|---|
| E-01 | Consent ledger, per purpose | 100 | 1 | 80% | M (8) | 80 | **10.00** | Must | Every profiled customer is covered. Impact 1 because it is a legal gate (PDPL [R46]; C1) and meets the consent need (Opportunity 4) [Skill03 §3]. Conf. 80%: the law is High, and consent uptake (A2) is unmeasured. |
| E-02 | Phase-1 data ingestion (CASA/salary, HLB loans/cards, CIC, HLB Connect, Sổ Bán Hàng) | 100 | 3 | 80% | L (16) | 240 | **15.00** | Must | Impact 3 because the data is the main lever: about ⅔ of alt-data model lift comes from data, not method [L9]. CIC holds no deposit data [R71]. Relieves P1 (Opportunity 9). Conf. 80%: Vietnam lift is unverified (A1). |
| E-03 | Phase-2 Open API cross-bank data | 35.5 | 2 | 50% | L (16) | 35.5 | **2.22** | Should | R-1 for SAL-N/GIG paid into other banks. Conf. 50%: HLB's Circular 64 readiness is unknown [R73] and consent is Low–Medium (A2). |
| E-04 | Phase-3 telco / e-wallet / platform data | 44.0 | 2 | 30% | XL (32) | 26.4 | **0.83** | Won't (PL-01) | The foreign evidence is strong [R80][L1]. Conf. 30% because Decree 94 applicability and the PDPL data-trading ban are unresolved [R74][R76] (OQ-07-01). |
| E-05 | Customer Credit Profile generator (6 fields) | 100 | 3 | 80% | L (16) | 240 | **15.00** | Must | Impact 3: the profile *is* the differentiated per-customer decision that replaces one-size review [Skill04 §4]. Every stage consumes it (SD-4). |
| E-06 | Hybrid scoring: rules champion + ML challenger | 100 | 2 | 80% | XL (32) | 160 | **5.00** | Must | Impact 2, not 3: ML beats logistic scorecards only modestly [L18][L19], and most lift is data (E-02). Conf. 80%: peer-reviewed benchmarks. Effort XL covers per-segment models plus challenger infrastructure. |
| E-07 | Decision-tier policy layer + risk-tier rate grid | 100 | 3 | 80% | M (8) | 240 | **30.00** | Must | Impact 3: tiered approve/refer/decline routing is *the* non-AI lever [Skill04 §5]. It is in use in Vietnam (ACB, Home Credit) [Skill03 S36, S46]. Automated CIC check is a competitor must-have [Skill02 §5]. |
| E-08 | Profile refresh loop | 100 | 1 | 50% | M (8) | 50 | **6.25** | Must | Enabler for PR-01 and PR-03 (user: continuous reassessment). Conf. 50%: A11 is Medium [Skill06 §4]. |
| E-09 | Reason codes | 100 | 1 | 80% | S (4) | 80 | **20.00** | Must | Every decision gets one. Needed for AI Law human oversight and explainability (must-have before deployment (1 Mar 2027 at the latest for a new system; RF-8 corrected 2026-09-30)) [Skill02 §5] and FM-10 [R40]. Meets Pain #6 (Opportunity 4, Partial) minimally. Effort S because codes come from scorecard attributes. |

### 1.2 Journey stages (AW-, CO-, AP-, PR-, RT-, AD-)

| Code | Feature | Reach | Impact | Conf. | Effort† | Value | **RICE** | MoSCoW | Scoring rationale |
|---|---|---|---|---|---|---|---|---|---|
| AW-01 | Never-used / low-risk / high-potential target list | 56.0 | 2 | 50% | M (8) | 56 | **7.00** | Must | User-suggested Awareness pain point + R-4 [Brief p.6]. Conf. 50%: the positive-targeting upside is a Hypothesis [Skill04 §6 P4]. The HLB MY × Grab outcomes are undisclosed (OQ-07-02). |
| AW-02 | Suitability filter + random holdout | 56.0 | 1 | 80% | S (4) | 44.8 | **11.20** | Must | Guardrail against FM-5. Marginal propensity to borrow is 59% for FICO <660 [R49] (peer-reviewed). Binding condition C4. |
| AW-03 | Uplift model | 56.0 | 1 | 50% | L (16) | 28 | **1.75** | Should | Incremental over AW-01/02. Bank uplift evidence is vendor-grade (Low) [R53][R54]. The method has academic support [R55][L10]. |
| AW-04 | Pre-approved limit in HLB Connect / partner apps | 56.0 | 2 | 80% | M (8) | 89.6 | **11.20** | Must | Gives Cohort A an instant, pre-decided offer (P2 relief) and delivers the Awareness offer. Confirmed pattern: KBank [R26], HLB MY × Grab [R8], SC Vietnam pre-approved [R78], Techcombank 3 min [R77]. |
| AW-05 | Platform-partner pre-selection | 15.0 | 2 | 30% | L (16) | 9 | **0.56** | Should | Best route to GIG visibility [R8]. Conf. 30%: no Vietnam platform agreement is evidenced (D-08-2). |
| CO-01 | Personalised package builder | 100 | 2 | 50% | M (8) | 100 | **12.50** | Must | User-suggested Consideration pain point. Tenor choice and 0% eligibility are competitor must-haves [Skill02 §5]. Personalising by profile is a user-identified gap (50%). |
| CO-02 | All-in cost & schedule before acceptance | 100 | 1 | 100% | S (4) | 100 | **25.00** | Must | Brief-mandated "fair and transparent cost" [Brief p.4]. Pain #7 is Confirmed (Opportunity 6). FM-4 enforcement [R41]. Effort S: display only. |
| CO-03 | Side-by-side package comparison | 100 | 0.5 | 30% | S (4) | 15 | **3.75** | Should | Increment on CO-01. Backed only by the user's statement (Hypothesis). |
| AP-01 | In-session decision | 100 | 3 | 100% | L (16) | **300** | **18.75** | Must | The **core bottleneck** and R-2. Pain #4 is Confirmed (Opportunity 9). Five competitors decide in ≤3 min [Skill02 §5]. Highest Value of all 46. RICE is held down by the L effort estimate. |
| AP-02 | Chip-CCCD/VNeID eKYC (existing HLB capability) | 100 | 2 | 100% | S (4) | 200 | **50.00** | Must | R-3 precondition [Skill04 §4]. Biometrics are legally required [Skill02 §5]. HLB already cut onboarding from ~300 s to ~80 s [R67], hence effort S. |
| AP-03 | Application-integrity checks → integrity score | 100 | 2 | 80% | M (8) | 160 | **20.00** | Must | R-3 "application is genuine" [Brief p.6]. Pain #14 (Opportunity 6) is Partial, and no fraud-rate statistic exists, hence 80%. |
| AP-04 | Refer-band handling with SLA | 64.5 | 2 | 80% | M (8) | 103.2 | **12.90** | Must | AI Law human oversight [Skill02 RF-8]; C3. Reach is exposure, because the refer-band share is unknown (OQ-04-02). |
| AP-05 | Starter limit (low-and-grow), Cohort B | 44.0 | 3 | 80% | S (4) | 105.6 | **26.40** | Must | Directly relieves P1 for the segments R-1 names. It turns a decline into a small approval. Nubank [R1], Kredivo [R13], Upstart +27% approvals [R16]. Effort S: a policy parameter in E-07. |
| AP-06 | CASA-first onboarding at approval | 44.0 | 1 | 80% | S (4) | 35.2 | **8.80** | Must | The brief's own mitigation [Brief p.8]. Reuses HLB in-app account opening [R66][R67]. |
| AP-07 | CIC-unavailable fallback | 100 | 0.5 | 50% | S (4) | 25 | **6.25** | Must | Every application is exposed during an outage. One confirmed incident [R71], single source. Outage frequency unknown. |
| AP-08 | eKYC failure fallback flows | 100 | 1 | 50% | M (8) | 50 | **6.25** | Won't (PL-04) | Pain #5 (Opportunity 4) is Confirmed only with a foreign survey leg. HLB's failure rate is unknown. |
| AP-09 | Rich decline explanation / path-to-yes | 50.0 | 1 | 50% | S (4) | 25 | **6.25** | Won't (PL-05) | Pain #6 (Opportunity 4, Partial). The minimum is already covered by E-09. |
| PR-01 | Behavioural re-scoring on repayment | 100 | 2 | 50% | M (8) | 100 | **12.50** | Must | User-suggested Purchase & Repayment pain point. Standard behavioural-scoring mechanism; A11 Medium. |
| PR-02 | Early warning → hardship outreach | 44.0 | 2 | 50% | L (16) | 44 | **2.75** | Must | Responsible lending (R-2) and HMW 3 [Skill06]. Pain #11 is Confirmed. No evidence in our notes that early-warning outreach works, hence 50%. |
| PR-03 | Graduation ladder (offered, consented) | 100 | 2 | 80% | M (8) | 160 | **20.00** | Must | Refines the profile (user). Low-and-grow [R1]. Explicit consent per increase [R30]. Aydin RCT [R50] and Agarwal [R49] size both the effect and the risk. |
| PR-04 | CIC reporting of PoP loans | 100 | 1 | 100% | S (4) | 100 | **25.00** | Must | Legal obligation [Skill02 RF-11]. Builds bureau history for thin files [R63] and gives anti-stacking visibility (FM-3). Effort S: credit institutions must already report to CIC [Skill02 RF-11], so the reporting channel exists. Only the new product needs mapping. |
| PR-05 | Cash-flow-aligned / flexible repayment | 29.5 | 2 | 80% | L (16) | 47.2 | **2.95** | Won't (PL-06) | Pain #10 (Opportunity 6, Confirmed). RCT: repayment flexibility *lowers* default [R64]. Grab daily deduction [R6]; Shopify % of sales [R65]. Effort L: servicing and core-banking schedule changes. |
| PR-06 | Collection-conduct standards | 100 | 1 | 80% | S (4) | 80 | **20.00** | Won't (PL-07) | Pain #8 (Opportunity 6, Confirmed). OJK debt-shaming precedent [R34]; black-credit context [R37]. Effort S: policy, scripts, vendor terms. |
| PR-07 | Financial-literacy nudges | 14.5 | 0.5 | 30% | S (4) | 2.2 | **0.54** | Won't (PL-08) | Behavioural signal B12 only (Hypothesis). |
| RT-01 | Product / merchant-category recommendations | 100 | 2 | 30% | L (16) | 60 | **3.75** | Must | User-suggested Retention pain point. A12 was rated **Low** [Skill06 §4]. Pain #9 cross-sell resentment is Confirmed. Needs a recommender plus a partner catalogue. |
| RT-02 | Anti-stacking gate (CIC + internal cap) | 100 | 2 | 80% | S (4) | 160 | **40.00** | Must | C2 / FM-3. CGAP Kenya: 16% borrowed to repay another loan [R35]. CFPB BNPL stacking [R36]. Effort S: a rule on existing CIC pulls. |
| RT-03 | "Ready limit" dashboard | 100 | 0.5 | 50% | S (4) | 25 | **6.25** | Should | Makes PR-03 visible. Convenience only. |
| RT-04 | Marketing contact-frequency caps | 56.0 | 0.5 | 80% | S (4) | 22.4 | **5.60** | Won't (PL-09) | Pain #9 is Confirmed. E-01 and AW-02 already cover part of it, hence impact 0.5. |
| AD-01 | Repayment-conditioned invite | 100 | 2 | 50% | M (8) | 100 | **12.50** | Must | User-suggested Advocacy pain point. Enforcement effect: field RCT [R61]. Referred customers ≥16% more valuable [R60]. A13 is Low, and there is no Vietnam evidence. |
| AD-02 | Refer-a-merchant via Sổ Bán Hàng | 14.5 | 1 | 50% | M (8) | 7.25 | **0.91** | Should | Merchant flywheel: BNPL raises merchant sales ~20% [R57]. The Sổ Bán Hàng terms are not public [R66]. |
| AD-03 | "Good payer" credit passport | 44.0 | 1 | 30% | L (16) | 13.2 | **0.83** | Should | The credit-builder RCT shows a null average effect and harm for already-indebted borrowers [R62]. Mixed evidence [R63]. |

### 1.3 Operator, merchant and governance (OP-, MX-, GV-)

| Code | Feature | Reach | Impact | Conf. | Effort† | Value | **RICE** | MoSCoW | Scoring rationale |
|---|---|---|---|---|---|---|---|---|---|
| OP-01 | Officer refer-band workbench | 64.5 | 3 | 80% | M (8) | 154.8 | **19.35** | Must | Impact 3: the operator side of the bottleneck. Pains #12 and #13 are both Opportunity 9 [Skill03 §1], and together they concentrate the 380,000 VND of human effort on the refer band only [Skill04 §5]. |
| OP-02 | Policy / rule console | 100 | 0.5 | 50% | M (8) | 25 | **3.13** | Should | Operational agility. Can start as configuration files [Skill08 §2]. |
| MX-01 | Merchant checkout integration (API / hosted / QR) | 100 | 3 | 80% | L (16) | 240 | **15.00** | Must | Without it no offer reaches the purchase moment [Brief p.4]. Omnichannel is a competitor must-have [Skill02 §5]. Merchant willingness (A8) is Medium. |
| MX-02 | Merchant portal (0% campaigns, settlement) | 100 | 0.5 | 30% | L (16) | 15 | **0.94** | Won't (PL-03) | Pain #16 (Opportunity 2, Hypothesis). But see OQ-11-07 on 0% campaign setup. |
| GV-01 | Model governance (drift, fairness, audit, AI Law docs) | 100 | 1 | 100% | L (16) | 100 | **6.25** | Must | AI Law conformity before deployment (1 Mar 2027 at the latest for a new system; RF-8 corrected 2026-09-30) [Skill02 RF-8]. SR 11-7 [R45]. COVID-era drift [R42]. |
| GV-02 | Decree 94 sandbox application | 44.0 | 0.5 | 30% | M (8) | 6.6 | **0.83** | Won't (PL-02) | Only a process step that unlocks E-04. The participant list is unknown (OQ-07-01). |

† **Effort is a judgment-based estimate** (§0.5). Skill 14 has not run.

### 1.4 Excluded from scoring: rejected permanently by Skill 08

| Code | Feature | Why not scored | Source |
|---|---|---|---|
| AW-06 | Mass / broadcast campaigns | Contradicts R-4 ("targeted and relevant rather than broadcast") | [Brief p.6]; PL-10 |
| CO-04 | Willingness-to-pay personalised pricing | Fairness and consumer-protection risk. Risk-based pricing only | [R56]; PL-11 |
| AD-04 | Cash-for-sign-up / referral leaderboards | Rewards borrowing, not repayment | [R61]; PL-12 |
| X-01 | Phone contact / gallery harvesting | FM-11: OJK precedent, black-credit stigma | [R34][R37]; PL-13 |

### 1.5 Ranked by RICE (all 46 scored features)

Ties share a rank. "Value rank" re-ranks the same features on R × I × C with effort removed. This shows what RICE would say if Skill 14 found the efforts roughly equal.

| Rank | Code | RICE | Value rank | Skill 08 MoSCoW → Tier | Kano (§2) |
|---|---|---|---|---|---|
| 1 | AP-02 | 50.00 | 6 | Must → Tier 1 | Basic |
| 2 | RT-02 | 40.00 | 7 | Must → Tier 1 | Indifferent to customer; Basic for regulator |
| 3 | E-07 | 30.00 | 2 | Must → Tier 1 | Basic |
| 4 | AP-05 | 26.40 | 12 | Must → Tier 1 | Performance |
| 5 | CO-02 | 25.00 | 14 | Must → Tier 1 | Performance |
| 5 | PR-04 | 25.00 | 14 | Must → Tier 1 | Basic |
| 7 | E-09 | 20.00 | 21 | Must → Tier 1 | Basic |
| 7 | AP-03 | 20.00 | 7 | Must → Tier 1 | Basic (officer) |
| 7 | PR-03 | 20.00 | 7 | Must → Tier 1 | Performance |
| 7 | **PR-06** | **20.00** | 21 | **Won't (parked) → Tier 3** ⚑ | Basic |
| 11 | OP-01 | 19.35 | 11 | Must → Tier 1 | Performance (officer) |
| 12 | AP-01 | 18.75 | **1** | Must → Tier 1 | Basic |
| 13 | E-02 | 15.00 | 2 | Must → Tier 1 | Performance |
| 13 | E-05 | 15.00 | 2 | Must → Tier 1 | Performance |
| 13 | MX-01 | 15.00 | 2 | Must → Tier 1 | Basic |
| 16 | AP-04 | 12.90 | 13 | Must → Tier 1 | Basic (regulatory) |
| 17 | CO-01 | 12.50 | 14 | Must → Tier 1 | Performance |
| 17 | PR-01 | 12.50 | 14 | Must → Tier 1 | Indifferent (felt via PR-03) |
| 17 | AD-01 | 12.50 | 14 | Must → Tier 1 | Delighter |
| 20 | AW-02 | 11.20 | 29 | Must → Tier 1 | Indifferent to customer; Basic for regulator |
| 20 | AW-04 | 11.20 | 20 | Must → Tier 1 | Delighter |
| 22 | E-01 | 10.00 | 21 | Must → Tier 1 | Basic |
| 23 | AP-06 | 8.80 | 32 | Must → Tier 1 | Indifferent |
| 24 | AW-01 | 7.00 | 25 | Must → Tier 1 | Delighter (Reverse risk) |
| 25 | E-08 | 6.25 | 26 | Must → Tier 1 | Indifferent (felt via PR-03) |
| 25 | AP-07 | 6.25 | 35 | Must → Tier 1 | Basic |
| 25 | GV-01 | 6.25 | 14 | Must → Tier 1 | Basic (regulatory) |
| 25 | **AP-08** | 6.25 | 26 | **Won't (parked) → Tier 3** ⚑ | Basic |
| 25 | **AP-09** | 6.25 | 35 | **Won't (parked) → Tier 3** ⚑ | Delighter |
| 25 | **RT-03** | 6.25 | 35 | **Should → Tier 2** ⚑ | Delighter |
| 31 | RT-04 | 5.60 | 39 | Won't (parked) → Tier 3 | Basic |
| 32 | **E-06** | 5.00 | 7 | **Must → Tier 1** ⚑ | Performance |
| 33 | CO-03 | 3.75 | 40 | Should → Tier 2 | Delighter |
| 33 | **RT-01** | 3.75 | 24 | **Must → Tier 1** ⚑ | Delighter (Reverse risk) |
| 35 | OP-02 | 3.13 | 35 | Should → Tier 2 | Performance (risk team) |
| 36 | PR-05 | 2.95 | 28 | Won't (parked) → Tier 3 | Delighter |
| 37 | **PR-02** | 2.75 | 30 | **Must → Tier 1** ⚑ | Delighter (Reverse risk) |
| 38 | E-03 | 2.22 | 31 | Should → Tier 2 | Performance |
| 39 | AW-03 | 1.75 | 33 | Should → Tier 2 | Indifferent |
| 40 | MX-02 | 0.94 | 40 | Won't (parked) → Tier 3 | Performance (merchant) |
| 41 | AD-02 | 0.91 | 44 | Should → Tier 2 | Delighter |
| 42 | E-04 | 0.83 | 34 | Won't (reg.) → Tier 3 | Performance |
| 42 | AD-03 | 0.83 | 42 | Should → Tier 2 | Delighter |
| 42 | GV-02 | 0.83 | 45 | Won't (reg.) → Tier 3 | Indifferent |
| 45 | AW-05 | 0.56 | 43 | Should → Tier 2 | Delighter |
| 46 | PR-07 | 0.54 | 46 | Won't (parked) → Tier 3 | Indifferent |

⚑ = RICE rank disagrees with the Skill 08 tier. Recorded as a dissenting view in §4. It is not acted on.

**Readings** `[Rubric R2]`

1. **The cheapest guardrails rank at the very top.** AP-02 reuses HLB's existing eKYC [R67]. RT-02 is a rule on CIC pulls the bank already makes. E-07 is the rules-based tiered routing.
   - This is RICE's arithmetic doing what Skill 04 §5 predicted: the non-AI, rules-plus-data layer gives the most value per unit of effort.
   - It is also the strongest rubric R2 argument for the "hybrid" design. The rules champion is the cheap, high-leverage core. ML (E-06) is the expensive margin.
2. **On Value (R × I × C), the bottleneck core leads.** AP-01 is Value rank 1. E-02, E-05, E-07 and MX-01 tie at rank 2.
   - AP-01 sits at RICE rank 12 only because its effort is judged L. Skill 14's estimate for AP-01, E-02, E-05 and MX-01 is the input most likely to reorder the top of the list (§0.5).
3. **Reach barely separates features.** 27 of the 46 features touch every inbound application (Reach 100). So RICE here is driven mainly by Impact, Confidence and Effort.
   - Absolute volumes (OQ-11-01) would not change this. It follows from a single-channel product where every applicant passes the same path.

**Sensitivity (Cohort A proxy 35.5 instead of 56.0):**
- AW-02 and AW-04 fall to 7.10 (rank 22), AW-01 to 4.44, AW-03 to 1.11 and RT-04 to 3.55.
- AW-01 drops from rank 24 to rank 31, just below E-06 (rank 30 under this sensitivity). That puts it below the RICE cut line.
- No tier placement changes. AW-01 would join the dissent list in §4, where it is recorded. It stays Tier 1 as a user-suggested pain point and as brief requirement R-4.

---

## 2. Kano Classification

**Perspective used.** Kano measures how satisfied the *customer* is when an attribute is present or absent [S-11-2]. For bank-internal features, the feature is classified from the viewpoint of whoever experiences it: the officer (OFFICER persona [Skill05]), the merchant, or the regulator as stakeholder. The table says which. Categories can **migrate over time**: Delighter → Performance → Basic [S-11-2][S-11-4]. Expected shifts are listed at the end of this section.

**Evidence basis.** The competitor minimum bar [Skill02 §5] marks what is already *Basic* in Vietnamese PoP credit:
- decision in ≤~3 min
- no documents / chip-CCCD eKYC
- automated CIC check
- omnichannel partner embedding
- 0% merchant-subsidised option
- tenures to 24 months
- explainability and human oversight (before deployment)

Pain-point evidence [Skill03 §1] identifies what causes dissatisfaction when missing.

| Code | Feature | Type | Rationale |
|---|---|---|---|
| E-01 | Consent ledger | **Basic** | 49.5% of Vietnamese users name data misuse as their top worry [Skill03 S42]. PDPL makes consent mandatory [R46]. Absence → distrust and illegality. Presence → no delight. |
| E-02 | Phase-1 data ingestion | **Performance** | Customers feel it as approval breadth and limit size: more consented, relevant data → more "yes" at the same risk [L9][R16]. |
| E-03 | Open API data | **Performance** | Same mechanism as E-02, for people paid into other banks. |
| E-04 | Phase-3 third-party data | **Performance** | Same mechanism, for the thinnest files. |
| E-05 | Customer Credit Profile | **Performance** | Felt as how well the offer fits (right-sized limit and price). A better profile gives a better fit. |
| E-06 | Hybrid scoring | **Performance** | Better discrimination → more approvals at the same risk (Upstart +27% approvals, −16% APR [R16]). |
| E-07 | Decision tiers + rate grid | **Basic** | Automated CIC check and instant tiered decisions are the competitor minimum bar [Skill02 §5]. |
| E-08 | Profile refresh loop | **Indifferent** (felt only through PR-03) | The customer never sees it directly. Its value shows up in the graduation offers. |
| E-09 | Reason codes | **Basic** | Pain #6: an opaque rejection causes dissatisfaction [Skill03 §1]. Explainability is a must-have before deployment (1 Mar 2027 at the latest for a new system; RF-8 corrected 2026-09-30) [Skill02 §5]. |
| AW-01 | Target list | **Delighter**, with **Reverse risk** | A relevant, affordable offer the customer didn't expect is attractive. An *untargeted* offer is resented (Pain #9, Confirmed), which is a Reverse attribute. AW-02 is what keeps it on the Delighter side. |
| AW-02 | Suitability filter + holdout | **Indifferent** to customer; **Basic** for regulator | Invisible to customers. Required by C4 / FM-5 [R49]. |
| AW-03 | Uplift model | **Indifferent** | Invisible. Its value is to HLB, as fewer and better contacts [R53]. |
| AW-04 | Pre-approved limit surfaced | **Delighter** (moving toward Performance) | Skill 02 lists pre-approved limits as a *differentiator* [Skill02 §5]. Wallets already show in-app limits (MoMo), so the delight is fading [R21]. |
| AW-05 | Partner pre-selection | **Delighter** | An unexpected invitation for gig workers who are otherwise declined [R8]. |
| CO-01 | Package builder | **Performance** (tenor / 0% parts are Basic) | Tenures to 24 months and a 0% option are the minimum bar [Skill02 §5]. Fitting the package to the customer's profile is Performance: the better the fit, the more satisfied the customer. |
| CO-02 | All-in cost & schedule | **Performance** (moving to Basic) | Skill 02 marks transparent APR as a Vietnamese *differentiator* but a must-have in India [Skill02 §5]. Hidden cost is Confirmed as a strong irritant (Pain #7). |
| CO-03 | Side-by-side comparison | **Delighter** | No competitor evidence. Backed only by the user's statement. |
| AP-01 | In-session decision | **Basic** | Every sourced competitor decides in 5 s to 3 min [Skill02 §5]. HLB's 1.8–4.6 days is below the bar [Brief p.9]. |
| AP-02 | eKYC | **Basic** | "No documents" is the minimum bar and a legal requirement [Skill02 §5]. |
| AP-03 | Integrity checks | **Basic** (officer) | The officer needs real-time genuineness assurance: Opportunity 6 [Skill03 §3]. Customers only notice its absence, as fraud in their name (Skill03 S17). |
| AP-04 | Refer-band handling | **Basic** (regulatory) | Human oversight is mandatory [Skill02 RF-8]. For the customer, a provisional hold with notice avoids silent limbo. |
| AP-05 | Starter limit | **Performance** | For thin-file applicants, "some yes" beats "no", and a larger starter limit satisfies more. Alt-data underwriting is a differentiator only Fundiin/CIMB claims [Skill02 §5]. |
| AP-06 | CASA-first onboarding | **Indifferent** (**Reverse** if forced) | The customer came to buy, not to open an account. Offered, not required [Skill08 §2]. |
| AP-07 | CIC fallback | **Basic** | Invisible until an outage. Its absence means no decisions at all [R71]. |
| AP-08 | eKYC failure fallback | **Basic** | Pain #5: a failed eKYC causes abandonment [Skill03 §1]. |
| AP-09 | Path-to-yes explanation | **Delighter** | Beyond the minimum reason code (E-09). No competitor evidence [Skill02 §5]. |
| PR-01 | Behavioural re-scoring | **Indifferent** (felt through PR-03) | Invisible. Customers feel it as graduation offers or protection. |
| PR-02 | Early-warning hardship outreach | **Delighter**, with **Reverse risk** | Unexpected help when struggling is attractive. If it is perceived as collection pressure, it becomes Reverse (Pain #8). Non-coercive tone is essential [Skill08 §1]. |
| PR-03 | Graduation ladder | **Performance** | Bigger or better terms as the customer earns them. Low-and-grow [R1]. |
| PR-04 | CIC reporting | **Basic** (legal); Performance for thin files | Mandatory [Skill02 RF-11]. For thin files, a positive bureau history is a reward that grows over time [R63]. |
| PR-05 | Flexible repayment | **Delighter** (for GIG / MSME) | Not offered by bank PoP competitors in Skill 02. Offered by platforms (Grab daily deduction [R6]; Shopify [R65]). |
| PR-06 | Collection-conduct standards | **Basic** | Pain #8, fear of harassment of relatives, is Confirmed [Skill03 §1]. Absence causes strong dissatisfaction. Presence is simply expected. |
| PR-07 | Financial-literacy nudges | **Indifferent** | Hypothesis-level evidence only (B12). |
| RT-01 | Product recommendations | **Delighter** if opt-in; **Reverse risk** otherwise | A12 is rated Low. Pain #9 cross-sell resentment is Confirmed [Skill06 §4][Skill03 §1]. |
| RT-02 | Anti-stacking gate | **Indifferent** to customer; **Basic** for regulator | C2 / FM-3 [R35][R36]. |
| RT-03 | Ready-limit dashboard | **Delighter** | A visible "next step" motivates on-time repayment. No competitor evidence. |
| RT-04 | Contact-frequency caps | **Basic** | The absence of a cap *is* Pain #9 [Skill03 §1]. |
| AD-01 | Repayment-conditioned invite | **Delighter** | Rewards that no Vietnamese competitor in Skill 02 offers. The field RCT supports the design [R61]. |
| AD-02 | Refer-a-merchant | **Delighter** | Novel. For the merchant, Performance (BNPL raises merchant sales ~20% [R57]). |
| AD-03 | Credit passport | **Delighter** | Novel. Gated because the evidence is mixed [R62]. |
| OP-01 | Officer workbench | **Performance** (officer) | The more complete the pre-assembled file, the faster and more defensible the decision. Pains #12 and #13 [Skill03 §1]. |
| OP-02 | Policy console | **Performance** (risk team) | Tuning speed. |
| MX-01 | Checkout integration | **Basic** | Omnichannel partner embedding is the minimum bar [Skill02 §5]. |
| MX-02 | Merchant portal | **Performance** (merchant) | Pain #16 is Hypothesis-level [Skill03 §1]. |
| GV-01 | Model governance | **Basic** (regulatory) | AI Law conformity before deployment (1 Mar 2027 at the latest for a new system; RF-8 corrected 2026-09-30) [Skill02 §5]. |
| GV-02 | Sandbox application | **Indifferent** | A process step. |

### Kano Grid Summary

- **Basic, 14 features:**
  - Engine and decision path: E-01, E-07, E-09, AP-01, AP-02, AP-03, AP-04, AP-07, AP-08, MX-01
  - Borrower protection: PR-04, PR-06, RT-04
  - Governance: GV-01
  - Regulator-facing Basic (customer-Indifferent): AW-02, RT-02
  - CO-01's tenor and 0% components are also Basic
- **Performance, 12 features:** E-02, E-03, E-04, E-05, E-06, CO-01, CO-02, AP-05, PR-03, OP-01, OP-02, MX-02. PR-04 is also Performance for thin files.
- **Delighter, 12 features:** AW-01, AW-04 (fading), AW-05, CO-03, AP-09, PR-02, PR-05, RT-01, RT-03, AD-01, AD-02, AD-03.
- **Indifferent (customer-invisible), 6 features:** E-08, PR-01, AW-03, AP-06, PR-07, GV-02. AW-02 and RT-02 are also Indifferent from the customer's view.
- **Reverse risk:** AW-01, RT-01, PR-02, AP-06. Each becomes Reverse if it is done without consent, suitability or a non-coercive tone. Binding conditions C1, C2 and C4 are the controls [Skill07 §4].

**Kano × MoSCoW check.**
- Every Skill 02 minimum-bar capability maps to a Tier 1 feature.
- The exception is the **0% merchant-subsidised option**. CO-01 covers *eligibility*, but the campaign set-up tool (MX-02) is parked (OQ-11-07).
- Five Tier 1 features are Delighters: AW-01, AW-04, PR-02, RT-01, AD-01. The skill template puts Delighters in Tier 2. They are Tier 1 because four of them are **user-suggested pain points** (a user instruction) and PR-02 is a responsible-lending protection under R-2. This is logged in §4.

### Notes on Category Shifts

| Feature | Current category | Expected future category | Timeframe / trigger |
|---|---|---|---|
| E-09 / AP-04 / GV-01 | Basic (regulatory) | Stays Basic, with enforceable obligations | AI Law conformity deadline: before deployment (1 Mar 2027 at the latest for a new system; RF-8 corrected 2026-09-30) [Skill02 RF-8] |
| CO-02 All-in cost | Performance | **Basic** | Already a must-have in India [Skill02 §5]. CFPB Apple/Goldman enforcement [R41]. Vietnamese timing unknown. |
| AW-04 Pre-approved limit | Delighter | **Performance** | Already eroding: wallets show in-app limits [R21]. Techcombank 3-minute loans [R77]. |
| E-02 / E-03 / AP-05 Alt-data & starter limits | Performance (differentiator) | **Basic** | As Circular 64 Open API matures (full compliance 1 Mar 2027 [R73]) and competitors copy it |
| PR-05 Flexible repayment | Delighter | **Performance** for GIG / MSME | Platform lenders already normalise earnings-linked repayment [R6][R65] |
| AD-01 / AD-03 | Delighter | Delighter | No competitor movement observed |

---

## 3. Priority Tiers

**Tiers follow Skill 08's MoSCoW exactly:**
- Tier 1 = Must
- Tier 2 = Should
- Tier 3 = Won't-have-v1 / Parking Lot

Within each tier, features are ordered by RICE. The order is advisory for Skill 16 sequencing, not a scope decision. RICE disagreements are in §4.

```
Tier 1 — Build now (v1) — 29 features (Skill 08 MUST)
  Decision core & R-3 precondition (Basic / Performance):
    AP-02 eKYC (50.0) · E-07 decision tiers + rate grid (30.0) · AP-05 starter limit (26.4)
    · AP-03 integrity checks (20.0) · E-09 reason codes (20.0) · OP-01 officer workbench (19.35)
    · AP-01 in-session decision (18.75; Value rank 1) · E-02 Phase-1 data (15.0)
    · E-05 Customer Credit Profile (15.0) · MX-01 checkout integration (15.0)
    · AP-04 refer band (12.9) · E-01 consent ledger (10.0) · AP-06 CASA-first (8.8)
    · AP-07 CIC fallback (6.25) · GV-01 model governance (6.25) · E-06 hybrid scoring (5.0 ⚑)
  Responsible-lending guardrails:
    RT-02 anti-stacking (40.0) · CO-02 all-in cost (25.0) · PR-04 CIC reporting (25.0)
    · AW-02 suitability + holdout (11.2) · PR-02 early warning (2.75 ⚑)
  User-suggested pain points (lifecycle):
    PR-03 graduation ladder (20.0) · CO-01 package builder (12.5) · PR-01 re-scoring (12.5)
    · AD-01 repayment-conditioned invite (12.5) · AW-04 pre-approved limit (11.2)
    · AW-01 target list (7.0) · E-08 refresh loop (6.25) · RT-01 recommendations (3.75 ⚑)

Tier 2 — Build next (Skill 08 SHOULD; v1 if time allows, each with its Skill 08 condition) — 8
    RT-03 ready-limit dashboard (6.25; after PR-03) · CO-03 comparison (3.75; after CO-01)
    · OP-02 policy console (3.13) · E-03 Open API data (2.22; Circular 64 readiness)
    · AW-03 uplift model (1.75; after ≥1 holdout cycle) · AD-02 refer-a-merchant (0.91)
    · AD-03 credit passport (0.83; debt-gated) · AW-05 partner pre-selection (0.56; D-08-2)

Tier 3 — Parking Lot (Skill 08 WON'T v1) — 9
  Awaiting user assessment of Claude-proposed pain points (PL-03…PL-09):
    PR-06 collection conduct (20.0 ⚑) · AP-08 eKYC fallback (6.25 ⚑) · AP-09 path-to-yes (6.25 ⚑)
    · RT-04 contact caps (5.6) · PR-05 flexible repayment (2.95; strongest evidence, see §4)
    · MX-02 merchant portal (0.94) · PR-07 literacy nudges (0.54)
  Awaiting regulatory readiness (PL-01, PL-02):
    E-04 Phase-3 data (0.83) · GV-02 sandbox application (0.83)

Excluded (rejected permanently, not scored): AW-06, CO-04, AD-04, X-01
```

**Indicative dependency order within Tier 1.** This is a proposal for Skill 13/16 to confirm, not a scope decision. RICE rank alone would put RT-02 before AP-01, but RT-02 has nothing to gate until draws exist. Dependencies override RICE for sequencing [S-11-1].

1. **Wave 1: decision path.** E-01 → E-02 → E-06 (rules champion first) → E-05 → E-07 → E-09 → AP-02, AP-03 → AP-01 → AP-04, OP-01 → AP-05, AP-06, AP-07 → MX-01 → CO-01, CO-02 → PR-04 → GV-01.
2. **Wave 2: lifecycle.** E-08 → PR-01 → PR-02, PR-03 → RT-02 → RT-01. Also AW-02 → AW-01 → AW-04, and AD-01.

`[Rubric R3]` This order mirrors the data-to-decision chain the rubric asks for: data inputs (E-01/02) → logic (E-06/07) → outputs (E-05/09) → business actions (AP/CO/PR/RT/AD).

---

## 4. Prioritisation Decision Log

**"Dissenting view"** = where RICE (or Kano) points to a different placement than Skill 08. Each is recorded for the user's decision in Skill 22 (OQ-11-04, OQ-11-05). **None changes scope here.**

**Boundary test.** If tiers were set purely by RICE rank, taking the top 29 (the number of Musts):
- **Would come in:** PR-06 (rank 7), plus a six-way tie at 6.25 on the cut line that includes AP-08, AP-09 and RT-03.
- **Would drop out:** E-06 (rank 32), RT-01 (rank 33) and PR-02 (rank 37).

| Feature | Score (RICE / Value rank) | Decision | Rationale | Dissenting view |
|---|---|---|---|---|
| **AP-01** In-session decision | 18.75 / Value **#1** | **Tier 1** (Must) | The core bottleneck and R-2 [Skill04 §4][Brief p.6]. Kano Basic [Skill02 §5]. | *None on placement.* A RICE rank of 12 understates it. Its effort band (L) is a judgment estimate. At M it would be rank 2. Skill 14 must re-estimate (OQ-11-02). |
| **AP-02** eKYC | 50.0 / #6 | **Tier 1** | RICE #1 because it reuses HLB's existing capability [R67]. | None. |
| **RT-02** Anti-stacking gate | 40.0 / #7 | **Tier 1** | C2 / FM-3 [R35][R36]. A cheap rule with high protection. | None. |
| **E-06** Hybrid scoring (rules champion + ML challenger) | 5.0 (rank 32) / #7 | **Tier 1** (Must, kept) | User decision SD-3. The challenger is how HLB *proves* incremental AI value per segment, which rubric R2 requires [Brief p.11][Skill08 §4]. `[Rubric R2]` | **Dissent (RICE):** its XL effort places it below the RICE cut line. Evidence says most lift comes from data, not method [L9][L18][L19]. RICE alone would ship the **rules champion in Tier 1** and move the **ML challenger to Tier 2**, trained once v1 outcome data exists. Recorded for the user. SD-3 stands. |
| **RT-01** Product recommendations | 3.75 (rank 33) / #24 | **Tier 1** (Must, kept) | User-suggested Retention pain point (user instruction) [Skill08 §2]. | **Dissent (RICE + Kano):** Confidence is 30% (A12 rated Low) and Pain #9 resentment is Confirmed. Kano carries a Reverse risk. RICE would place it in Tier 2. **Mitigation that keeps it Tier 1 but cheaper:** a simple rules-based category suggestion (affordability headroom × merchant category), opt-in only under marketing consent, before any recommender model. That would move effort toward M. Skill 13 to decide. |
| **PR-02** Early warning → hardship outreach | 2.75 (rank 37) / #30 | **Tier 1** (Must, kept) | Brief R-2 requires "responsible lending standards" [Brief p.6]. HMW 3 [Skill06]. Pain #11 is Confirmed. | **Dissent (RICE):** low confidence that outreach works (no efficacy evidence in [R#]), L effort and 44.0 reach. RICE would place it in Tier 2. Kept, because dropping the only proactive protection for struggling payers would weaken the rubric R3 "community impact" case. Suggest starting with rule-based triggers (missed or partial instalment, CIC new-loan flag) before a predictive model. |
| **PR-06** Collection-conduct standards | **20.0 (rank 7)** / #21 | **Tier 3** (parked, kept: PL-07) | Claude-proposed pain #8. It stays parked until the user assesses it (user instruction) [Skill08 §5]. | **Dissent (RICE + Kano), the strongest in this file.** It ties for rank 7: 100% reach, Confirmed Pain #8, S effort (policy, scripts, vendor terms). Kano **Basic**. Absence carries black-credit / debt-shaming reputational risk [R34][R37]. **Recommend the user promotes it to Tier 1.** Part of it is already implied by C5 ("no reference contacts") and X-01's rejection. |
| **AP-08** eKYC failure fallback | 6.25 (rank 25, tie) / #26 | **Tier 3** (PL-04, kept) | Claude-proposed pain #5. Awaiting the user. | **Dissent (weak):** it sits in the tie on the cut line, and Kano is **Basic**. HLB's eKYC already takes ~80 s [R67], but its failure rate is unknown. Promote if HLB data shows material eKYC drop-off. |
| **AP-09** Path-to-yes explanation | 6.25 (rank 25, tie) / #35 | **Tier 3** (PL-05, kept) | Claude-proposed pain #6. E-09 already covers the legal minimum. | **Dissent (weak):** it is on the cut line, with S effort. A natural extension of E-09 once reason codes exist. |
| **RT-03** Ready-limit dashboard | 6.25 (rank 25, tie) / #35 | **Tier 2** (Should, kept) | Depends on PR-03 [Skill08 §2]. | **Dissent (weak):** RICE ties with three Musts (E-08, AP-07, GV-01). Build it with PR-03 if the HLB Connect UI capacity exists. |
| **PR-05** Flexible / cash-flow-aligned repayment | 2.95 (rank 36) / #28 | **Tier 3** (PL-06, kept) | Claude-proposed pain #10. Parked until the user assesses it [Skill08 §5]. | **Evidence flag for the user (not a RICE dissent).** Its RICE is low only because reach is narrow (GIG + MSME = 29.5) and effort is judged L. Its **confidence (80%) is the highest of any parked feature**: an RCT found repayment flexibility *lowered* default [R64], and Grab [R6] and Shopify [R65] show the model at scale. **Caveat:** the RCT was on Bangladeshi microfinance with a monthly delay option [R64], so carrying it over to Vietnamese PoP instalments is an extrapolation. A narrower build would score **5.90**: an earnings-aligned weekly schedule option for GIG and MSME at effort M (29.5 × 2 × 0.8 ÷ 8). That is above E-06, RT-01 and PR-02. Recommended for promotion at the user's assessment, as Skill 08 also flagged. |
| **RT-04** Contact-frequency caps | 5.6 (rank 31) / #39 | **Tier 3** (PL-09, kept) | E-01 and AW-02 already cover part of it. | Kano **Basic** (Pain #9). Its RICE is above three Tier 1 features. Low-cost promotion candidate at the user's assessment. |
| **AW-01 / AW-04 / AD-01** (Tier 1 Delighters) | 7.0 / 11.2 / 12.5 | **Tier 1** | User-suggested pain points (Awareness, Advocacy) [`_context.md`]. AW-01 is also brief requirement R-4 [Brief p.6]. | **Template deviation.** The skill template puts Delighters in Tier 2. They stay Tier 1 by user instruction. **Conditional dissent on AW-01:** under the conservative Cohort A proxy (35.5), AW-01 scores 4.44 (rank 31), below the RICE cut line (§1.5 sensitivity). Its confidence (50%) rests on a Hypothesis-level targeting upside [Skill04 §6]. Skill 10 should put a holdout-measured uplift KPI on it (C4). |
| **E-04 / GV-02** Phase-3 data, sandbox | 0.83 / 0.83 | **Tier 3** | Regulatory readiness (PL-01/02) [R74][R76]. | None. RICE agrees. |
| **MX-02** Merchant portal | 0.94 | **Tier 3** (PL-03) | Pain #16 is Hypothesis-level. | None on RICE. **Gap flag:** a 0% merchant-subsidised option is a competitor must-have [Skill02 §5]. How v1 configures 0% campaigns without a portal (OP-02 config? bilateral set-up?) is unresolved (OQ-11-07). |
| **AW-03 / E-03 / AW-05 / AD-02 / AD-03** (Should) | 0.56–2.22 | **Tier 2** | Each has a Skill 08 condition (holdout data, Circular 64, partnership, Sổ Bán Hàng terms, debt gate). | None. RICE agrees they sit below Tier 1. |
| Excluded AW-06, CO-04, AD-04, X-01 | n/a | **Not scored** | Rejected permanently [Skill08 §5]. | None. |

**Summary for the submission** `[Rubric R2][Rubric R4]`

- Skill 08's boundary is largely **confirmed by RICE**:
  - 26 of the 29 Must features sit at or above the RICE cut line.
  - Every Should except RT-03 sits below it. RT-03 sits in the tie on the line.
  - Every regulatory-blocked feature sits below it.
- The three low-RICE Musts (E-06, RT-01, PR-02) are kept for stated reasons: a user decision, a user pain point, and responsible lending respectively.
- The one strong counter-signal is **PR-06, a parked Basic feature that ranks 7th**. It is flagged for promotion.
- **PR-05** carries the strongest evidence among parked items.
- All of these go to the user in Skill 22.

---

## Open Items Raised by This Skill

| # | Item | Why it matters | Carry to |
|---|---|---|---|
| OQ-11-01 | HLB PoP application volumes per quarter, the size of the existing-customer base (Cohort A), and the never-used-PoP share are not in the brief. Reach is therefore a **relative index**, not users per period. | Needed to turn RICE into absolute terms and to set Skill 10 targets. `[Rubric R4]` | Skill 10 / 22 (state as an assumption in the submission) |
| OQ-11-02 | **Every effort figure is a judgment-based estimate.** Skill 14 has not run. The top-of-list order is most sensitive to the AP-01, E-02, E-05, MX-01 and E-06 bands. | Re-score any feature whose band moves once Skill 14 runs. | Skill 14 (pending) / 16 |
| OQ-11-03 | Per-segment approval rates are unknown (links OQ-03). Reach for post-approval features (PR-, RT-, AD-, CO-01) is an upper bound. | Could lower lifecycle-feature scores relative to decision-path features. | Skill 22 |
| OQ-11-04 | **Dissents on Tier 1 Musts:** E-06 (ML challenger could be Tier 2, rules champion Tier 1), RT-01 (low confidence, Reverse risk), PR-02 (low RICE). The user should confirm they stay Tier 1. | Keeps SD-3 and the user pain-point scope explicit rather than assumed. | Skill 22 |
| OQ-11-05 | **Parked-feature evidence for the user's assessment of PL-03 to PL-09.** Recommended to promote: PR-06 (RICE 20.0, Basic) and PR-05 (80% confidence, RCT [R64]). Low-cost candidates: RT-04 (5.6) and AP-08/AP-09 (6.25). | The user's assessment decides v1 scope. This file supplies the scores only. | Skill 22 (with Skill 08 revisit trigger) |
| OQ-11-06 | Kano types are **inferred from evidence**, not measured with a Kano functional/dysfunctional survey [S-11-3]. | Reverse-risk items (AW-01, RT-01, PR-02, AP-06) most need validation. | Pilot research / Skill 22 (state as a limitation) |
| OQ-11-07 | A 0% merchant-subsidised option is a competitor **must-have** [Skill02 §5], but the merchant portal (MX-02) is parked. How v1 sets up and settles 0% campaigns is undefined. Whether HLB already runs 0% instalments is unverified (OQ-07-03). | A possible gap in the Kano Basic bar for Tier 1. | Skill 13 / 22 |
| OQ-11-08 | Skill 10 (Success Metrics) was not complete when this ran. Tier 1 features still need to be linked to the bottleneck-relief KPIs in Skill 04 §7 (in-session decision rate, STP share, cost per decision, thin-file approval at a delinquency ceiling, refer-band share). | The rubric R3 requirement for "operational KPIs that directly measure bottleneck relief". | Skill 10 / 16 |
| OQ-11-09 | The Cohort A proxy (56.0 = SAL-C + SAL-N) is an upper bound. At 35.5 (SAL-C only), Awareness scores fall 37% and AW-01 drops below the RICE cut line (rank 31). No tier changes. Whether HLB's actual Cohort A mix differs materially is unknown. | Sizes the Awareness and Retention value case. | Skill 22 |

---

## Source Index

**New sources for this skill**

| # | Source | URL | Used for |
|---|---|---|---|
| S-11-1 | Sean McBride, "RICE: Simple prioritization for product managers", Intercom blog (5 Jan 2018) | https://www.intercom.com/blog/rice-simple-prioritization-for-product-managers/ | RICE formula; Impact scale (3/2/1/0.5/0.25); Confidence anchors (100/80/50%, <50% "moonshot"); effort in person-months; "shouldn't be used as a hard and fast rule" (dependencies, table stakes) |
| S-11-2 | Kano, N., Seraku, N., Takahashi, F., Tsuji, S. (1984), "Attractive quality and must-be quality", *Journal of the Japanese Society for Quality Control* 14(2): 39–48. Bibliographic details via Wikipedia's Kano model entry | https://en.wikipedia.org/wiki/Kano_model | Kano categories (must-be, one-dimensional, attractive, indifferent, reverse) |
| S-11-3 | Conjointly, "Kano Model of Feature Selection"; Kano.plus, "About Kano" | https://conjointly.com/blog/kano-model-excel-template/ ; https://kano.plus/about-kano | The functional/dysfunctional questionnaire method, which was not run here (OQ-11-06) |
| S-11-4 | Learning Loop, "Kano model" glossary (secondary, Low–Medium) | https://learningloop.io/glossary/kano-model | Attributes migrate over time (attractive → one-dimensional → must-be) |

**Upstream sources (not re-cited individually)**

| Key | File | Used for |
|---|---|---|
| [R1]–[R80] | `_research-notes-07-13.md` (80 entries, each with URL and confidence label) | Evidence behind the Confidence scores and Kano rationales. Key entries: [R1] Nubank, [R6] Grab daily deduction, [R8] HLB MY × Grab, [R16] Upstart CFPB NAL, [R26] KBank, [R30] RBI DLG, [R35] CGAP Kenya, [R36] CFPB BNPL, [R41] CFPB Apple/Goldman, [R49] Agarwal et al., [R50] Aydin, [R53]–[R55] uplift, [R57] Keil et al., [R60] Schmitt et al., [R61] Bryan–Karlan–Zinman, [R62] Burke et al., [R63] FICO × Affirm, [R64] Battaglia–Gulesci–Madestam, [R65] Shopify Capital, [R66] HLB × Sổ Bán Hàng, [R67] HLB eKYC, [R71] CIC incident / no deposit data, [R73] Circular 64, [R74] Decree 94, [R76] Banking Academy review, [R77] Techcombank, [R78] SC Vietnam, [R80] Berg et al. |
| [Brief p.N] | `_requirements.md` (from the case brief PDF) | Segment mix (p.9), requirements R-1 to R-4 (p.6), visibility by segment (pp.7–8), customer needs (p.4), rubric (p.11) |
| [Skill02 §5 / §6] | `02-discover/02-market-research.md` | Competitor minimum bar (Kano Basic evidence), regulatory flags |
| [Skill03 §1 / §3] | `02-discover/03-user-research.md` | Pain-point Opportunity Scores and evidence strength |
| [Skill04 §4–§7] | `02-discover/04-problem-research.md` | Bottleneck verdict, non-AI test, segment × problem matrix, KPI candidates |
| [Skill05] | `02-discover/05-persona-research.md` | OFFICER persona |
| [Skill06 §4] | `03-define/06-problem-statement.md` | Assumptions A1–A13 (confidence levels) |
| [Skill07] | `03-define/07-problem-solution-fit.md` | FM-1 to FM-13; binding conditions C1–C6 |
| [Skill08] | `03-define/08-v1-product-scoping.md` | Feature universe, MoSCoW boundary, Parking Lot PL-01 to PL-13 |
| [L#] | `02-discover/literature-review.md` | [L1] digital footprints, [L9] data-vs-method lift, [L10] uplift, [L18][L19] ML vs scorecard benchmarks |

## Skill 22 Resolutions (User decision, Skill 22, 2026-09-30)
- **OQ-11-04:** Tier 1 dissents resolved. E-06 is kept (D-08; the challenger goes live per segment once corridor labels exist). RT-01 and PR-02 launch rules-first (Skill 14).
- **OQ-11-05:** PR-06 and a narrowed PR-05 are promoted to v1 (D-01).
- **OQ-11-07:** 0% promotions are configured by HLB staff via policy config (D-01 resolution in Skill 08).
