# Lean Canvas
**Product:** HLB Point-of-Purchase Credit Decisioning (working name) | **Skill:** 09 | **Status:** Done (2026-09-30)
**Depends on:** v1-product-scoping.md | **Feeds into:** product-prd.md (Section 8 — Business Context)

> **Citation legend.**
> - Research-note citations: `[R#]` = entry # in `_research-notes-07-13.md`.
> - Upstream citations: `[Brief p.N]` = `_requirements.md`; `[S#]` = `02-market-research.md` Source Index; `[Skill0N …]` = that skill's file; `[L#]` = `literature-review.md`.
> - New sources for this skill: `[LC#]` (Source Index at end).
> - **Competition-rubric tags are written `[Rubric R1]`–`[Rubric R4]`** (Diagnosis / Justification / Concept / Data & Assumptions) so they cannot be confused with `[R#]` research-note citations.
>
> **Number labels.** **(sourced)** = figure taken directly from a source. **(derived)** = arithmetic on sourced figures, formula shown. **(hypothetical — assumption)** = a parameter chosen only to show sensitivity; it is **not a finding** and must not be quoted as an HLB estimate. **[DECISION REQUIRED]** = HLB or the team must decide.
>
> **Business-model framing.** This is not a SaaS canvas. The product is a **bank-internal capability inside a bank business line**, distributed B2B2C through merchant checkouts. "Revenue" therefore means net interest margin, fees and merchant subsidy on instalment loans, and "cost" means cost of funds, credit losses, cost per decision, data, acquisition, integration and model governance (`_context.md` §Business Model Hypothesis).

---

## Pre-filled Context
- **Product type:** Other. A bank-internal credit decisioning capability owned by HLB Vietnam, distributed B2B2C through merchant and partner checkouts (`_context.md`).
- **Solution (user-specified, SD-1 to SD-7):** one central AI credit-profile engine (hybrid: ML models + policy/guardrail rules + human "refer" band). It produces a six-field **Customer Credit Profile (CCP)** that all six PoP journey stages consume: Awareness → Consideration → Application & Instant Decision → Purchase & Repayment → Retention → Advocacy. Two entry cohorts: **A** existing HLB customers (pre-approval), **B** new-to-bank applicants at partner checkouts (starter limit + CASA-first) [Skill08 §2].
- **Operating baseline:** tickets of 1.6M–90.1M VND; tenures of 3/6/9/12/18/24 months; centralised manual review at 380,000 VND per application; 1.8–4.6 business days to decide [Brief p.9].
- **Pricing hypothesis (from `_context.md`):** HLB earns net interest margin and/or fees on instalment loans, and possibly merchant discount/subsidy fees. **HLB's actual rate grid is not public and not in the brief** (OQ-02-01). This skill therefore uses market proxies from Skill 02 §2A and labels them as such.
- **Known competitors:** none named in the brief. Skill 02 mapped fincos (Home Credit, FE Credit, HD Saison, Mcredit), wallet pay-later (MoMo, ZaloPay, SPayLater), bank-led PayLater (MWG PayLater/Cake) and BNPL fintechs (Fundiin) [Skill02 §1].
- **v1 boundary respected:** everything below uses only v1 **Must/Should** features from Skill 08. Parked items (PL-01 to PL-13, e.g. Phase-3 telco/e-wallet data, merchant portal, flexible repayment) are not relied on for revenue or advantage claims.

---

## 0. High-Level Concept and UVP (headline)

**High-level concept (X for Y):** *India's pre-approved checkout EMI (Bajaj Finance "Easy EMI" [S53]) plus Nubank-style "low-and-grow" limits [R1], run on a Vietnamese bank's deposit-funded balance sheet.* `[Rubric R3]`

**Unique Value Proposition (customer-facing):**
> **"Pay over time right at the checkout: a bank decision in minutes, one all-in price shown up front, and a limit that grows as you repay — even if you have no credit history."**

**Value proposition by side of the market** (B2B2C, so each paying or operating side needs its own reason):

| Side | Value proposition | Evidence |
|---|---|---|
| **Shopper / borrower** | In-session decision at a transparent bank price. Assessed on money they can show they earn (CASA/salary inflows, sales ledger), not on paperwork they don't have. Tickets and tenors above wallet pay-later caps | Speed is table stakes (competitors decide in 5 s–30 min) [Skill02 F1]; wallets cap at 8–20M VND [S18][S19]; transparent-APR whitespace [Skill02 Whitespace 4] |
| **Merchant / partner** | More completed sales from a bank-funded instalment offer, with a lower subsidy needed for 0% promotions because the funding is cheaper | BNPL raises merchant sales ~20% [R57]; bank cost of funds ~4.8% vs Home Credit ~7.1% [S24] |
| **HLB (business line)** | Turns a flat 380,000 VND, multi-day review into a tiered decision whose cost is proportional to the ticket, so small tickets stop being loss-making by construction | [Brief pp.5–6]; §3 of this document |

---

## 1. Lean Canvas

| PROBLEM | SOLUTION | UNIQUE VALUE PROPOSITION | UNFAIR ADVANTAGE | CUSTOMER SEGMENTS |
|---|---|---|---|---|
| **Top 3 problems:**<br>1. **One manual, document-and-bureau review for every application** → decision after the purchase moment (1.8–4.6 business days) at a flat 380,000 VND, so short small tickets lose money on the review alone [Brief pp.5, 9][Skill04 §4][Skill02 F2].<br>2. **Thin-file applicants are declined despite visible digital income.** Gig + MSME + first-time = 44.0% of inbound; 64.5% have no bureau history [Brief pp.7–9].<br>3. **No targeting and no learning loop.** Offers aren't aimed at those who will "need, accept and responsibly use" credit [Brief p.6], and nothing updates an offer from repayment behaviour (user-identified gap, OQ-06-01).<br>*Precondition:* real-time identity and genuineness without document friction [Brief p.6 R-3]. | **Top 3 features (v1 Must, Skill 08):**<br>1. **Customer Credit Profile engine**: rules champion + ML challenger; tiers pre-approved / instant approve / refer / decline, with reason codes (E-05–E-09).<br>2. **In-session checkout decision**: chip-CCCD/VNeID eKYC + integrity score, starter limit, CASA-first onboarding, merchant API / hosted page / QR (AP-01–AP-07, MX-01).<br>3. **Lifecycle reuse of the profile**: pre-approved targeting with suitability filter + holdout (AW-01/02/04), personalised package + all-in cost (CO-01/02), re-scoring on repayment + consented graduation (PR-01/03), repayment-conditioned invite (AD-01). | **"Pay over time right at the checkout: a bank decision in minutes, one all-in price up front, and a limit that grows as you repay — even with no credit history."**<br><br>**High-level concept:** Bajaj-style pre-approved checkout EMI + Nubank-style low-and-grow, on a deposit-funded bank balance sheet [S53][R1]. | **Evidenced, in combination (none alone is uncopyable):**<br>1. Deposit funding: sector cost of funds ~4.8% vs finco ~7.1% and finco bonds 9.5–9.8% [S24][Skill02 F4].<br>2. CASA/salary-inflow data that **CIC does not hold** [R71].<br>3. Sổ Bán Hàng merchant-ledger channel (acceptance points + MSME income signal) [R66][S52].<br>4. Existing eKYC asset (National ID Registry; onboarding ~300 s → ~80 s) [R67].<br>5. HLB Group precedent for partner pre-selection (HLB MY × Grab, 2021) [R8]. | **Borrowers:**<br>• Cohort A: existing HLB customers never having used PoP credit (anchor: salaried-no-credit, SAL-N 20.5%).<br>• Cohort B: new-to-bank applicants at checkout, all five segments; design primary GIG (15.0%), then MSME (14.5%), FTB (14.5%) [Skill08 D-08-1][Brief p.9].<br>**Merchants/partners:** checkout owners hosting the offer; Sổ Bán Hàng merchants.<br>**Internal operator:** HLB credit-risk lead (refer band).<br><br>**Early adopters:** Cohort A account-holders with observable salary inflows, and Sổ Bán Hàng merchants (as acceptance points and MSME borrowers) [R26][R8][R66]. |

| KEY METRICS | CHANNELS |
|---|---|
| **North Star:** Good In-Moment Loans (GIML)/month = decided in-session, booked, and not 30+ DPD at MOB3 [Skill01 §2].<br>**Bottleneck relief:** in-session decision rate (baseline 0%); STP share (baseline 0%); refer-band share; blended cost per decision (baseline 380,000 VND) [Brief p.9][Skill04 §7].<br>**Economics:** contribution per booked loan by ticket × tenor band; share of bookings below break-even ticket; cost per *booked* loan (= cost per decision ÷ approval rate).<br>**Inclusion at controlled risk:** thin-file approval rate at an HLB-set MOB3 30+ ceiling.<br>**Lifecycle:** incremental take-up vs holdout; graduation / repeat-use rate; Cohort B → CASA conversion; active merchants.<br>**Guardrails:** stacking share at CIC; complaint rate; referee delinquency vs non-referred [Skill07 §4].<br>*Targets: Skill 10.* | 1. **Partner merchant checkouts**: physical stores, e-commerce and merchant apps via API / hosted page / QR deep link (MX-01) [Brief p.4].<br>2. **HLB Connect**: pre-approved limits and "ready limit" for Cohort A (AW-04, RT-03) [R67].<br>3. **Sổ Bán Hàng**: merchant acquisition, refer-a-merchant (AD-02, Should) and MSME borrowers [R66].<br>4. **Platform partners (conditional)**: Grab-style pre-selection only if a Vietnam data-sharing agreement exists (AW-05, D-08-2) [R8].<br>5. **Customer referral**: repayment-conditioned invite (AD-01) [R61]. |

| EXISTING ALTERNATIVES |
|---|
| **How customers solve this today:** finco counter loans (Home Credit "CCCD only", ~3 min; FE Credit real-time) [S9][S11] · wallet/marketplace pay-later (MoMo ≤20M, ZaloPay ≤8M, SPayLater ≤50M at 0% for 1–3 months) [S18][S19][S20] · retailer-captive bank PayLater (MWG PayLater by Cake, ≤40M, ~2 min) [S21] · bank instant loans for existing customers (Techcombank, "3 minutes", ≤100M) [R77] · platform-embedded loans deducted from driver wallets; Shopee SEasy seller loans [Skill03 S1, S41] · informal "black credit" apps [R37] · **delaying or abandoning the purchase** [Brief p.4] · **HLB today:** manual review, 1.8–4.6 days [Brief p.9]; HLB's unsecured personal loan starts at 30M VND with salary ≥10M VND/month [R68]. |

| COST STRUCTURE | REVENUE STREAMS |
|---|---|
| **Cost of funds:** sector ~4.8% (2025) [S24]; HLB's own not public (OQ-02-02).<br>**Credit losses:** proxies bracket 4.1% (bank consumer NPL ratio) to 11.5% (finco credit cost) [S6][S3][S4].<br>**Cost per decision:** 380,000 VND manual baseline [Brief p.9]; automated cost **unknown** (OQ-04-03, OQ-LR-03), hence the sensitivity in §3.<br>**Data/vendor:** CIC queries, eKYC/ID-registry checks (unit fees not public); Phase-3 vendor data parked (PL-01).<br>**Acquisition:** merchant integration (MX-01), targeted marketing, referral rewards paid only after on-time repayment (AD-01).<br>**Platform & governance:** engine build and run, model-risk/drift/fairness monitoring, AI-Law conformity before deployment (≤ 1 Mar 2027 for a new system), PDPL DPIA [Skill02 RF-7, RF-8].<br>**People:** refer-band officers, model-risk team, collections. | **1. Interest income (NIM)** on 3–24-month instalments via a published risk-tier rate grid (E-07). Market band: finco ~20–35% p.a. [S11][S13][S14]; HLB's rate **[DECISION REQUIRED]** (OQ-02-01).<br>**2. Merchant subsidy / discount** funding 0% promotions. Justified by merchant sales uplift ~20% [R57]; Vietnam BNPL merchant fees **not public** (OQ-02-12). §3.5 gives the *required* subsidy, not an assumed rate.<br>**3. Transparent fees**: no stacked fees (Whitespace 4); fee schedule **[DECISION REQUIRED]**.<br>**4. Indirect**: CASA deposits and cross-sell from Cohort B onboarding [Brief p.8]; repeat loans via graduation [R50].<br>**Lifetime value:** repeat-use and graduation rates are not yet measurable (OQ-09-10). Referred customers are worth ≥16% more in a bank study [R60].<br>**Gross margin:** per-loan contribution in §3.2. |

---

## 2. Field Notes & Evidence

### 2.1 Problem `[Rubric R1]`
1. **Core bottleneck: one undifferentiated manual workflow.** Skill 04 found (High confidence on the mechanism) that the root cause is "the single, undifferentiated, manual, document-and-bureau-anchored underwriting workflow … inherited unchanged from branch lending" [Skill04 §4]. It produces both the speed problem and the cost problem: 380,000 VND per application whatever the loan size, and 1.8–4.6 business days [Brief p.9]. The review is 23.75% of the smallest ticket (derived: 380,000 ÷ 1,600,000) [Brief p.9]. At market-level pricing, a 3-month loan needs roughly a 16.9M VND ticket just to recover the review cost (derived) [Skill02 F2, §2A].
2. **Thin-file exclusion.** Gig/platform workers and first-time borrowers have the "lowest approval rates". Online merchants are "frequently declined despite officers observing operational cash flows" [Brief pp.7–8]. Thin-file segments are 44.0% of inbound; all no-bureau segments are 64.5% (derived) [Brief p.9]. CIC covers >70% of adults, which leaves roughly 3 in 10 outside it [R70].
3. **Untargeted offers and no learning loop.** The brief requires offers that are "targeted and relevant rather than broadcast" [Brief p.6]. The lack of a mechanism that refines offers from repayment is a **user-identified** gap, not a brief fact (OQ-06-01, Skill 06 A11–A13).

**Evidence sources:** [Brief pp.4–9]; Skill 04 §3–§4, §6 (P1 and P2 both Opportunity Score 9, Confirmed); Skill 03 Pains #1–#4, #12, #13.

### 2.2 Existing Alternatives `[Rubric R1]`
| Alternative | What it offers | Why it falls short for HLB's target | Source |
|---|---|---|---|
| Finco counter loans (Home Credit, FE Credit, HD Saison, Mcredit) | 1–100M VND; decisions in minutes; "CCCD only" | Risk-priced at ~20–35% p.a.; sector credit cost 11.5%; finco cost of funds ~7.1% | [S9][S11][S13][S14][S3][S24] |
| Wallet / marketplace pay-later | 0% short terms; instant | Low limits (8–20M, SPayLater ≤50M); MoMo requires a clean CIC history; fee stacks (33k/month, 7.5–30% conversion, late fees 5.25–21%) | [S18][S19][S20] |
| Retailer-captive bank PayLater (MWG/Cake) | ≤40M, ~2 min, ≤24 months | Captive to one retail group; shows bank-led PoP credit already works in Vietnam | [S21] |
| Bank instant loans to existing customers | Techcombank: approval and disbursement "within 3 minutes", ≤100M VND | Needs existing relationship and income ≥10M VND/month; not embedded at third-party checkouts | [R77] |
| Platform-embedded loans (driver wallets, Shopee SEasy) | No income proof; auto-deduction | Costly (GIG quotes ~20–30%/yr) and tied to one platform | [Skill03 S1, S41][Skill06 §2] |
| Informal / "black credit" apps | Fast, no checks | >200 apps flagged by police; APRs up to ~2,346% in one busted ring | [R37][R39] |
| Delay or abandon | — | Lost sale for the merchant, lost loan for HLB | [Brief p.4] |
| HLB today | Manual review; unsecured personal loan from 30M VND, salary ≥10M VND/month, 3 provinces | Excludes most of the 1.6–90.1M range and all GIG/MSME/FTB segments | [Brief p.9][R68] |

**Reading:** speed is **parity**, not differentiation. Every sourced competitor already decides in 5 s–30 min [Skill02 F1]. HLB's openings are the whitespaces Skill 02 identified: 10–90M VND tickets over 6–24 months at a fair bank price (Whitespace 1), thin-file customers with evidenced cash flow (Whitespace 2), MSME ledger-linked credit (Whitespace 3), and transparent pricing (Whitespace 4).

### 2.3 Customer Segments `[Rubric R3]`
**Primary (borrowers):**
- **Cohort A: existing HLB customers who have never used PoP credit.** Profiled proactively from Phase-1 bank data. The anchor persona for Awareness and Retention is the salaried-no-credit account-holder (SAL-N, 20.5% of inbound), whom the brief calls "visible if existing account holders" [Brief p.7][Skill08 D-08-1].
- **Cohort B: new-to-bank applicants at a partner checkout.** All five segments. **Design primary: GIG "Anh Hùng" (15.0%)**; strongest secondary: MSME "Chị Phụng" (14.5%); FTB "Hữu Tùng" (14.5%) comes second-wave because it carries the highest harm pattern [Skill05 Primary Persona Selection][Skill08 D-08-1].

**Secondary (non-borrower sides):**
- **Merchants/partners:** checkout owners who host the offer and may co-fund 0% promotions. Sổ Bán Hàng merchants are both acceptance points and potential MSME borrowers [R66][Skill08 AD-02].
- **Internal operator:** "Chị Lan", HLB credit-risk/underwriting lead, who owns the refer band and policy [Skill05 Persona 4].

**Early adopters:**
1. **Cohort A account-holders with observable salary inflows.** This is the incumbent-bank pattern: KBank offered data-driven loans "first to existing customers on a selective basis" [R26], and HLB Group has run partner-data pre-selection before [R8]. The data is legally ready (Phase 1, C6) and needs no new consent rail beyond marketing consent (C1).
2. **Sổ Bán Hàng merchants.** HLB already has in-app account opening, bookkeeping and e-invoicing with this partner [R66].

*Note:* this early-adopter choice follows from Skill 08's provisional default D-08-1 and the phased data rule C6. If the user overrides D-08-1 at Skill 22, this field changes with it.

### 2.4 Unique Value Proposition `[Rubric R2]` `[Rubric R3]`
- **Customer UVP:** "Pay over time right at the checkout: a bank decision in minutes, one all-in price shown up front, and a limit that grows as you repay — even if you have no credit history."
- **Why each clause is defensible:**
  - *"Bank decision in minutes"*: the in-session requirement [Brief p.6 R-2]. This is parity with competitors [Skill02 F1], so it is stated as a promise, not as the differentiator.
  - *"One all-in price shown up front"*: CO-02 (Must). It answers the brief's "fair and transparent cost" [Brief p.4], contrasts with wallet fee stacks [S18], and protects against the enforcement pattern around misleading 0% instalment marketing [R41].
  - *"A limit that grows as you repay"*: PR-03 graduation, **offered with explicit consent, never auto-applied** (C2) [R30][R50]. Low-and-grow is Nubank's evidenced mechanism [R1].
  - *"Even if you have no credit history"*: starter limit plus CASA-first onboarding for Cohort B (AP-05/06) [Brief p.8], and assessment on inflows CIC does not see [R71].
- **What the UVP deliberately does not claim:** "AI-powered". More than 70% of Vietnamese credit institutions already use AI/ML [S46], so it is not a differentiator. The rubric credits AI only for incremental value over the baseline [Brief p.11].
- **Merchant and HLB value propositions:** see §0.

### 2.5 Solution `[Rubric R3]`
Top three features, all **v1 Must** in Skill 08:
1. **Customer Credit Profile engine (E-05 to E-09).**
   - Output: six fields (risk grade A–E, affordability headroom, evidence confidence, propensity/need, integrity score, decision tier).
   - Hybrid scoring: rules/scorecard **champion** + ML **challenger**, so AI's incremental value is measured rather than asserted. This reflects Skill 04 §5's finding that most decision-stage value comes from data, not the algorithm [L9][L18][L19].
   - Reason codes on every outcome.
2. **In-session checkout decision (AP-01 to AP-07, MX-01).**
   - Chip-CCCD/VNeID eKYC reuses HLB's existing capability [R67]. Application-integrity score (R-3).
   - Refer band handled with an SLA (AI-Law human oversight) [Skill02 RF-8].
   - Starter limit + CASA-first onboarding for Cohort B [Brief p.8]. CIC-unavailable fallback [R71].
3. **Lifecycle reuse of the same profile.**
   - Awareness: target list + suitability filter + random holdout (AW-01/02). Pre-approved limit in HLB Connect (AW-04).
   - Consideration: personalised package with all-in cost (CO-01/02); risk-based, never willingness-to-pay pricing [R56].
   - Purchase & Repayment: re-scoring on each repayment (PR-01), early warning (PR-02), consented graduation (PR-03).
   - Retention: recommendations behind an anti-stacking gate (RT-01/02).
   - Advocacy: repayment-conditioned invite (AD-01) [R61].

### 2.6 Channels `[Rubric R3]`
| Channel | Role | Cohort | Evidence / condition |
|---|---|---|---|
| Partner merchant checkouts (in-store, e-commerce, merchant apps) via API / hosted page / QR (MX-01) | Primary acquisition at the moment of need | B (and A at checkout) | "Available within the shopping journey" [Brief p.4]; omnichannel embedding is a competitive must-have [Skill02 §5] |
| HLB Connect | Owned channel for pre-approved offers, ready limit, repayment and graduation offers | A | HLB's digital platform and eKYC [R67][Skill01 S4] |
| Sổ Bán Hàng | Merchant acquisition; MSME borrower channel; refer-a-merchant (AD-02, Should) | B (MSME), merchants | In-app HLB account opening, bookkeeping, e-invoice, "financing" [R66] |
| Platform partners (e.g. ride-hailing) | Pre-selection of gig workers | B → A | **Conditional (AW-05, D-08-2).** HLB MY × Grab precedent [R8]; **no Vietnam agreement evidenced** |
| Customer referral | Invite loop, rewarded only after the invitee's on-time repayment | A → B | Enforcement effect [R61]; referred customers ≥16% higher value [R60] |

**Channel risk (evidence-based):** marketplace checkouts are concentrated, with Shopee and TikTok at about 97% of marketplace GMV [Skill02 Trend 8]. Shopee charges sellers 0% on its own SPayLater orders against 4.91–6% on card or instalment orders [S22], and MWG's chains are served by Cake [S21]. Large platforms are therefore steering volume toward their own pay-later rails. The realistic first channels are **direct and offline merchants, Sổ Bán Hàng merchants and HLB Connect**, not the dominant marketplaces (OQ-09-11).

### 2.7 Revenue Streams `[Rubric R2]` `[Rubric R4]`
1. **Net interest income on instalments.**
   - Priced through a published **risk-tier rate grid** (E-07), which is risk-based, not willingness-to-pay [R56].
   - Market reference points (sourced):
     - FE Credit 0–2.69%/month flat; its own example is ~29.8% nominal APR / ~34.3% EAR (derived) [S11].
     - Mcredit average lending rate 25.44–29.03% [S14]. HD Saison 20.41–34.78% p.a. (Low confidence) [S13].
     - Bank references: Techcombank online instalment loan "from 1.2%/month" declining balance [R77]; HLB's unsecured personal loan "from 15%/yr" [R68], which is a different product.
   - **HLB's own PoP rate is [DECISION REQUIRED] (OQ-02-01).** §3 shows that this choice moves the break-even ticket as much as cost per decision does.
2. **Merchant subsidy / discount for 0% promotions.**
   - *Justification:* offering BNPL raises merchant sales by about **20%**, driven by lower-creditworthiness customers and products where the merchant has more market power. For the merchant, benefits outweigh costs [R57] (Keil, Berg, Burg & Puri, JFE 2025, peer-reviewed). A 0% promotional option is a must-have for durables and electronics [Skill02 §5].
   - *Rate:* **Vietnam BNPL merchant discount rates are not public (OQ-02-12). No rate is assumed here.** §3.5 instead derives the subsidy HLB would *need* to break even on a 0% loan, as a negotiating floor. For orientation only: Shopee charges sellers 4.91–6% on card/instalment orders [S22], and global BNPL merchant fees run ~3.3–6% + US$0.30 [S23]. Neither is a Vietnam BNPL rate.
3. **Fees.** A transparent, non-stacked schedule is the positioning (Whitespace 4). HLB's existing personal loan has a 3% early-repayment fee in the first 3 years [R68]. **The PoP fee schedule is [DECISION REQUIRED] (OQ-09-12).**
4. **Indirect value.**
   - CASA-first onboarding brings Cohort B into HLB's deposit base [Brief p.8]. Deposit value is not quantified here (OQ-09-06).
   - Graduation offers drive repeat instalment use: randomised limit increases led to borrowing of 11¢ per extra $ of limit in Q1 and 28¢ by year 3, mostly via instalments [R50]. This is capped by C2 (offered, consented, affordability-checked).
   - Referred customers: ≥16% higher value, ~18% lower churn (German bank study) [R60].

**Pricing hypothesis (for Skill 22 to confirm):** a published risk-tier APR grid for 3–24-month tenors, plus merchant-subsidised 0% eligibility by risk grade (CO-01), plus one all-in cost disclosure. Tiers, levels and the fee list are all `[DECISION REQUIRED]`.

### 2.8 Cost Structure `[Rubric R2]` `[Rubric R4]`
| Cost line | What is known | Status |
|---|---|---|
| Cost of funds | Banking-sector average ~4.8% (2025) [S24]. A second source reports sector COF of 3.68% in Q4 2025 on a different basis [LC1]. HLB's own is not public | Proxy; OQ-02-02, OQ-09-06 |
| Credit losses | Finco credit cost 11.5% (2025) [S3][S4]; bank consumer NPL ratio ~4.1% (Feb 2024) [S6]. An NPL ratio is not a loss rate; it is used only as a lower-risk proxy | Proxy; OQ-02-03, OQ-09-07 |
| Cost per decision | Manual 380,000 VND/application [Brief p.9]. ACB auto-approves ~60% "based on pre-set criteria" [Skill03 S36], a reference point for straight-through share, not cost. **HLB's automated cost per decision is unknown** | Sensitivity only (§3.3); OQ-04-03, OQ-LR-03, OQ-09-01 |
| Refer-band cost | Human review of uncertain cases, with a pre-assembled file (OP-01) | Unknown whether below 380,000 VND; OQ-09-02 |
| Data / vendor | CIC query fees; ID-registry / eKYC checks; Phase-3 vendor data parked (PL-01) | Unit prices not public; OQ-09-01 |
| Acquisition | Merchant integration (MX-01); targeted marketing (AW); referral rewards paid only after on-time repayment (AD-01), so reward cost follows good loans | No Vietnam CAC benchmark sourced; OQ-09-08 |
| Platform & governance | Engine build and run; champion–challenger, drift and fairness monitoring (GV-01); AI-Law conformity assessment before deployment (1 Mar 2027 at the latest for a new system; RF-8 corrected 2026-09-30); PDPL DPIA within 60 days [Skill02 RF-7, RF-8] | Mandatory; not costed |
| Capital | Small-ticket unsecured credit is sensitive to capital rules (Paytm cut sub-₹50k lending ~50% after India raised risk weights) [R28][R29] | Vietnam risk weights not researched; OQ-09-09 |
| Servicing & collections | Collection conduct is under regulatory focus (draft Circular 43 amendment: 7am–9pm) [S29] | Not costed |

### 2.9 Key Metrics `[Rubric R3]`
Skill 10 sets targets. The metrics below are chosen so that each **directly measures relief of the bottleneck** or of the business-model risk, as the rubric asks [Brief p.11]:
- **North Star:** Good In-Moment Loans per month [Skill01 §2].
- **Bottleneck relief:**
  - in-session decision rate (baseline 0%)
  - STP share (baseline 0%)
  - refer-band share
  - blended cost per decision (baseline 380,000 VND)
  - median and p90 time-to-decision (baseline 1.8–4.6 business days) [Brief p.9][Skill04 §7]
- **Unit economics (new in this skill):**
  - **cost per booked loan** = blended cost per decision ÷ approval rate
  - contribution per booked loan by ticket × tenor band
  - **share of bookings below the break-even ticket** for the current cost per decision (§3.3)
  - realised merchant subsidy vs the required subsidy (§3.5)
- **Inclusion at controlled risk:** thin-file approval rate at an HLB-set MOB3 30+ ceiling.
- **Lifecycle:**
  - incremental take-up vs random holdout (C4)
  - graduation acceptance rate
  - repeat-use rate
  - Cohort B → active CASA conversion
  - active merchants
- **Guardrails:** share of PoP customers with ≥2 concurrent consumer loans at CIC; marketing-complaint rate; referee vs non-referred delinquency; model drift (PSI) [Skill07 §4].

### 2.10 Unfair Advantage `[Rubric R2]`
Only evidenced claims are listed. **None is uncopyable on its own.** The advantage is the *combination*, held by a licensed bank. Confidence: **Moderate**.

| # | Advantage | Evidence | Who cannot easily copy it | Limit / caveat |
|---|---|---|---|---|
| UA-1 | **Deposit funding and cost of funds** | Banks ~4.8% vs Home Credit ~7.1% and finco bonds 9.5–9.8% [S24][Skill02 F4]. Funding-dependent lenders failed when funding withdrew (Upstart originations −59%) [R14][R15] | Fincos, BNPL fintechs, wallets (which rent bank balance sheets) | Other banks share it. HLB's own cost of funds is not public (OQ-02-02) |
| UA-2 | **CASA and salary-inflow data outside the bureau** | "CIC does not collect deposit/payment-account data, balances or transaction histories" (SBV statement) [R71] | Bureau-only and CCCD-only lenders | Covers **Cohort A only** until Cohort B is onboarded to CASA. Use needs purpose-specific consent (C1) [R46] |
| UA-3 | **Sổ Bán Hàng merchant-ledger channel** | In-app HLB account opening, auto-bookkeeping, e-invoice and "financing" in one merchant app [R66]; account opening ~300 s → ~80 s [S52] | Consumer pay-later players (none serves merchants as borrowers) [Skill02 Whitespace 3] | Other banks run similar ledger tie-ups (KiotViet–KBank/VPBank/VietinBank [S51], Techcombank T-Shop [Skill03]). User counts are not disclosed [R66] |
| UA-4 | **Existing eKYC asset** | National ID Registry integration + eKYC cut onboarding ~80% (~300 s → ~80 s); 2026 Digital CX awards [R67][Brief p.3] | New entrants still building it | Biometric eKYC is mandatory industry-wide [Skill02 RF-4], so this is a head start, not a moat |
| UA-5 | **HLB Group playbook for partner pre-selection** | HLB MY × GFin (Grab) "GRAB It Now" 2021: pre-selected drivers messaged in-app [R8] | Banks without a precedent | Outcomes are not public (OQ-07-02). No Vietnam platform deal exists (D-08-2) |

**Explicitly not claimed as unfair advantages:**
- The AI model itself: >70% of credit institutions already use AI/ML [S46].
- Decision speed: parity [Skill02 F1].
- The brand: no sourced consumer-trust data for HLB Vietnam specifically.

---

## 3. Unit Economics — Per-Loan Contribution and Cost-per-Decision Sensitivity `[Rubric R1]` `[Rubric R2]` `[Rubric R4]`

> **Read this first.** Every input below is either **(sourced)** from Skill 02 §2A or the brief, or a **(hypothetical — assumption)** parameter used only to show sensitivity. **HLB's automated cost per decision is unknown** (OQ-04-03, OQ-LR-03): no peer-reviewed emerging-market per-decision cost benchmark exists [literature-review OQ-LR-03]. It is therefore shown **only as a sensitivity range, never as a point estimate**. HLB's real APR, cost of funds, loss rates and approval rates are also not public (OQ-02-01 to 02-03). The outputs are illustrations of the *mechanism*, not forecasts.

### 3.1 Inputs

| # | Input | Value | Label | Source |
|---|---|---|---|---|
| I-1 | Market price (FE Credit's own example) | 10M VND, 12 months, 1.41%/month flat → ~974k VND/month → **~29.8% nominal APR** (~34.3% EAR) | sourced example; APR derived by IRR | [S11][Skill02 §2A] |
| I-2 | Bank cost of funds | **~4.8%** p.a. (sector average, 2025) | sourced (proxy for HLB) | [S24] |
| I-3a | Credit cost, finco level | **11.5%** p.a. (finco sector, 2025) | sourced (conservative proxy for thin-file) | [S3][S4] |
| I-3b | Credit cost, bank level | **4.1%** (bank consumer-lending NPL ratio, Feb 2024), used as an annual loss proxy | sourced ratio; its use as a loss rate is an **assumption** | [S6] |
| I-4 | Manual review cost | **380,000 VND** per application | sourced | [Brief p.9] |
| I-5 | Alternative bank price point | HLB unsecured personal loan "from **15%/yr**" (a different product, used only as a "fair bank price" scenario) | sourced | [R68] |
| I-6 | Straight-through reference | ACB auto-approves ~60% of applications on pre-set criteria | sourced (a reference point, not HLB) | [Skill03 S36] |
| H-1 | Automated cost per decision | **10k / 30k / 60k / 100k VND** | **hypothetical — assumption** | none (OQ-09-01) |
| H-2 | Refer-band share | **10% / 20% / 40%** of applications (40% ≈ the complement of ACB's ~60% auto-approval) | **hypothetical — assumption** | none (OQ-04-02) |
| H-3 | Cost of one refer-band case | 380,000 VND (the full manual cost is kept for referred cases) | **assumption**, likely conservative since OP-01 pre-assembles the file | none (OQ-09-02) |

**Method (same as Skill 02 §2A, for consistency):**
- **Net spread** = APR − cost of funds − credit cost.
- **Average balance-years** of an amortising loan ≈ ticket × (n + 1) ÷ 24, where n = tenure in months.
- **Contribution before decision cost** = ticket × net spread × (n + 1) ÷ 24.
- Check against the exact annuity for the FE Credit example: the approximation gives 5.42M balance-years vs 5.66M exact, so it **understates** income by ~4%. All break-evens below are therefore slightly conservative.
- **Excluded:** acquisition, servicing, collections, capital charge, fee income, merchant subsidy (except in §3.5) and deposit value. Real break-evens are higher than those shown.

**Derived spreads:**
- **Scenario A** (29.8% APR, finco-level losses): 29.8 − 4.8 − 11.5 = **13.5%**.
- **Scenario B** (29.8% APR, bank-level loss proxy): 29.8 − 4.8 − 4.1 = **20.9%**.
- **Scenario C** (15% "fair bank price", bank-level losses): 15 − 4.8 − 4.1 = **6.1%**.
- **Scenario D** (15% price, finco-level losses): 15 − 4.8 − 11.5 = **−1.3%**. This is negative, so it is not viable at any decision cost.

### 3.2 Per-loan contribution illustration (derived)

Contribution per **booked** loan in VND, before acquisition, servicing and capital, and assuming every decision leads to a booking (i.e. 100% approval; see the §3.3 note).

| Ticket × tenor | Balance-years | Spread A (13.5%): before decision cost | A: after manual 380k | Spread B (20.9%): before | B: after manual 380k | Spread C (6.1%): before | C: after manual 380k |
|---|---|---|---|---|---|---|---|
| **1.6M × 3 m** (smallest ticket) | 0.267M | 36,000 | **−344,000** | 55,733 | **−324,267** | 16,267 | **−363,733** |
| **3M × 6 m** | 0.875M | 118,125 | **−261,875** | 182,875 | **−197,125** | 53,375 | **−326,625** |
| **10M × 12 m** (FE Credit example) | 5.417M | 731,250 | 351,250 | 1,132,083 | 752,083 | 330,417 | **−49,583** |
| **30M × 12 m** | 16.25M | 2,193,750 | 1,813,750 | 3,396,250 | 3,016,250 | 991,250 | 611,250 |
| **90.1M × 24 m** (largest ticket) | 93.85M | 12,670,312 | 12,290,312 | 19,615,521 | 19,235,521 | 5,725,104 | 5,345,104 |

Formula: ticket × spread × (n + 1) ÷ 24, then minus 380,000.

**Reading:**
1. Under manual review, **small, short tickets lose money under every sourced price and loss scenario.** This confirms the brief's "the review may cost more than the loan earns" [Brief p.5] and Skill 02 F2.
2. **The price HLB chooses matters as much as the decision cost.** If HLB prices near its own personal-loan floor (15%, Scenario C), even a 10M × 12-month loan loses money under manual review. At finco-level losses (Scenario D), no ticket is viable at that price.
3. So the "fair, transparent bank price" in the UVP is only sustainable if two things hold: **cost per decision falls** *and* **losses on approved thin-file loans are held near bank levels, not finco levels.** This is the commercial reason the engine's risk grade and affordability headroom matter, not only its speed. `[Rubric R2]`

### 3.3 Sensitivity: how a tiered engine moves the break-even ticket

Under the tiered engine, only the refer band incurs the full human review. So:

**Blended cost per decision = (1 − refer share) × automated cost + refer share × 380,000** (formula; inputs H-1, H-2, H-3).

**Table 3.3a: Blended cost per decision (VND). All cells are hypothetical-assumption arithmetic.**

| Refer share ↓ / automated cost → | 10,000 | 30,000 | 60,000 | 100,000 |
|---|---|---|---|---|
| 10% | 47,000 | 65,000 | 92,000 | 128,000 |
| 20% | 84,000 | 100,000 | 124,000 | 156,000 |
| 40% | 158,000 | 170,000 | 188,000 | 212,000 |
| *Baseline: 100% manual* | *380,000* | | | |

**Break-even ticket = blended cost ÷ (net spread × (n + 1) ÷ 24).** Values are in millions of VND. They are derived from sourced spreads and hypothetical costs.

**Table 3.3b: Scenario A (29.8% APR, finco-level losses; spread 13.5%)**

| Tenor | Refer share | Auto 10k | Auto 30k | Auto 60k | Auto 100k | Manual baseline |
|---|---|---|---|---|---|---|
| 3 m | 10% / 20% / 40% | 2.09 / 3.73 / 7.02 | 2.89 / 4.44 / 7.56 | 4.09 / 5.51 / 8.36 | 5.69 / 6.93 / 9.42 | **16.89** |
| 6 m | 10% / 20% / 40% | 1.19 / 2.13 / 4.01 | 1.65 / 2.54 / 4.32 | 2.34 / 3.15 / 4.77 | 3.25 / 3.96 / 5.38 | **9.65** |
| 12 m | 10% / 20% / 40% | 0.64 / 1.15 / 2.16 | 0.89 / 1.37 / 2.32 | 1.26 / 1.70 / 2.57 | 1.75 / 2.13 / 2.90 | **5.20** |

**Table 3.3c: Scenario B (29.8% APR, bank-level loss proxy; spread 20.9%)**

| Tenor | Refer share | Auto 10k | Auto 30k | Auto 60k | Auto 100k | Manual baseline |
|---|---|---|---|---|---|---|
| 3 m | 10% / 20% / 40% | 1.35 / 2.41 / 4.54 | 1.87 / 2.87 / 4.88 | 2.64 / 3.56 / 5.40 | 3.67 / 4.48 / 6.09 | **10.91** |
| 6 m | 10% / 20% / 40% | 0.77 / 1.38 / 2.59 | 1.07 / 1.64 / 2.79 | 1.51 / 2.03 / 3.08 | 2.10 / 2.56 / 3.48 | **6.23** |
| 12 m | 10% / 20% / 40% | 0.42 / 0.74 / 1.40 | 0.57 / 0.88 / 1.50 | 0.81 / 1.10 / 1.66 | 1.13 / 1.38 / 1.87 | **3.36** |

**Table 3.3d: Scenario C (15% "fair bank price", bank-level losses; spread 6.1%), 12-month tenor only**

| Refer share | Auto 10k | Auto 30k | Auto 60k | Auto 100k | Manual baseline |
|---|---|---|---|---|---|
| 10% / 20% / 40% | 1.42 / 2.54 / 4.78 | 1.97 / 3.03 / 5.15 | 2.78 / 3.75 / 5.69 | 3.87 / 4.72 / 6.42 | **11.50** (3 m: 37.38; 6 m: 21.36) |

**Table 3.3e: Maximum affordable all-in cost per decision for a loan to break even (derived: ticket × spread × (n + 1) ÷ 24)**

| Ticket × tenor | Scenario A | Scenario B | Scenario C |
|---|---|---|---|
| 1.6M × 3 m | 36,000 | 55,733 | 16,267 |
| 1.6M × 24 m | 225,000 | 348,333 | 101,667 |
| 5M × 6 m | 196,875 | 304,792 | 88,958 |
| 10M × 12 m | 731,250 | 1,132,083 | 330,417 |

**Headline (the sensitivity result, not a forecast).**
- Moving from a flat 380,000 VND review to a tiered engine lowers the **3-month break-even ticket from ~10.9–16.9M VND to roughly 1.4–9.4M VND**, and the **12-month break-even from ~3.4–5.2M to roughly 0.4–2.9M VND** (Scenarios A–B, across the hypothetical grid).
- **Within this grid, the refer-band share moves the result more than the automated cost does.** Going from a 10% to a 40% refer share raises the 3-month break-even by ~2.4–4.9M VND. Going from 10k to 100k VND automated cost, at a fixed refer share, raises it by ~1.6–3.6M VND. That makes refer-band sizing (OQ-04-02) the most important number to back-test.
- **Even a very cheap engine cannot make the shortest, smallest tickets pay.** The smallest ticket (1.6M × 3 months) can absorb only ~16–56k VND of all-in decision cost (Table 3.3e). A pre-approved Cohort A customer, whose profile is computed in advance, is the case most likely to come in that low, but this is unverified (OQ-09-01). The alternative is a policy of steering the smallest tickets to longer tenors or 0% merchant-subsidised offers `[DECISION REQUIRED]`.

**Approval-rate note (arithmetic identity, not an estimate).** Decision cost is incurred on *every* application, including declines. **Cost per booked loan = blended cost per decision ÷ approval rate.** For example, at a hypothetical 50% approval rate every break-even ticket above doubles. HLB's approval rates by segment are not in the brief (OQ-03, OQ-09-03).

### 3.4 What the engine changes, in business-model terms `[Rubric R2]`
1. **It turns decision cost from a fixed cost into a mostly variable one.** The human cost is confined to the refer band. This is the brief's "cost per decision proportional to small-ticket loan sizes" [Brief p.6], expressed as a lower break-even ticket.
2. **Pricing and risk quality become the binding constraints.** Tables 3.2 and 3.3d show that a fair bank price is viable only at bank-level losses. The engine's contribution to margin therefore comes as much from *who* it approves (risk grade, affordability headroom, anti-stacking) as from *how cheaply* it decides.
3. **The AI-vs-rules question is economic.** The rules champion and ML challenger (E-06) should be compared on **contribution per application** (approval lift × spread − loss − decision cost), not on AUC alone. This makes rubric R2's "why AI creates superior value" measurable [Brief p.11][Skill04 §5].

### 3.5 Merchant subsidy required for a 0% promotion (derived; no merchant fee rate is assumed)

For a 0% loan, HLB's income is the merchant subsidy *m* (as % of ticket). To break even:

**m ≥ (cost of funds + credit cost) × (n + 1) ÷ 24 + cost per decision ÷ ticket**

| Loan | Scenario | Funding + credit cost only | + manual 380k | + tiered (hypothetical: 30k auto, 20% refer → 100k) |
|---|---|---|---|---|
| 10M × 6 m | Finco-level losses (4.8% + 11.5%) | 4.75% | 8.55% | 5.75% |
| 10M × 6 m | Bank-level proxy (4.8% + 4.1%) | 2.60% | 6.40% | 3.60% |
| 10M × 12 m | Finco-level losses | 8.83% | 12.63% | 9.83% |
| 10M × 12 m | Bank-level proxy | 4.82% | 8.62% | 5.82% |
| 3M × 6 m | Finco-level losses | 4.75% | 17.42% | 8.09% |
| 3M × 6 m | Bank-level proxy | 2.60% | 15.26% | 5.93% |

**Reading.**
- These are **floors HLB would need to negotiate**, not market rates. The actual Vietnam BNPL merchant fee is unknown (OQ-02-12).
- Under manual review, a 3M VND 0% promotion needs a merchant subsidy of ~15–17%. A tiered engine brings the same promotion to ~6–8% under the hypothetical grid, and bank-level funding and losses bring the 10M × 6-month case to ~2.6–3.6%.
- The merchant's willingness to pay rests on the ~20% sales uplift from offering BNPL [R57]. Whether a given merchant's margin can carry a given subsidy is a merchant-by-merchant question (OQ-09-05).
- Shopee's 4.91–6% card/instalment fee [S22] shows only what one large platform charges sellers for payment processing. It is not evidence of what merchants would pay HLB.

### 3.6 Context: sector margins (why a PoP line is attractive to a bank, and why cost control decides it)
- Vietnam banking-sector NIM fell to **3.03% in Q4 2025** (3.36% a year earlier). The retail-bank group averaged **3.74%**. Yield on earning assets was 6.42% and cost of funds (on that source's basis) 3.68% [LC1].
- Q2 2025 sector NIM was 3.05%, "the lowest in 5 years". Retail- and small-merchant-focused HDBank reached 5.2% [LC2].
- These are **whole-balance-sheet averages, not product margins**. They are not comparable to the product-level spreads in §3.2, which are before operating cost.
- They show why banks are pushing into higher-yield retail and consumer lending while margins compress. That fits Skill 02's finding that consumer lending grew ~26% in 2025, with banks growing about twice as fast as fincos [S4].
- HLB Vietnam's standalone NIM and cost of funds are not disclosed separately from HLB Group's results [LC3] (OQ-02-02).

---

## Open Items Raised by This Skill

| # | Open item | Why it matters | Validation method | Carry to |
|---|---|---|---|---|
| OQ-09-01 | **HLB's automated all-in cost per decision** (CIC query fee, ID-registry/eKYC fee, compute, vendor, amortised build). Inherits OQ-04-03 and OQ-LR-03 | Sets the column in Tables 3.3a–e. Determines whether the smallest tickets (limit ~16–56k VND) can ever break even | Bottom-up cost model from HLB procurement and IT; otherwise present the §3.3 range in the submission as an explicit assumption `[Rubric R4]` | Skill 14 / 22 |
| OQ-09-02 | Cost of one **refer-band** case once the file is pre-assembled (OP-01): is it still 380,000 VND or less? | H-3 assumes the full manual cost, which may overstate blended cost | Time-and-motion sample of HLB officers on pre-assembled vs current files | Skill 13 / 22 |
| OQ-09-03 | **Approval rate by segment** (inherits OQ-03) | Cost per booked loan = cost per decision ÷ approval rate, so break-evens scale inversely | HLB pilot data; otherwise a stated assumption | Skill 10 / 22 |
| OQ-09-04 | **HLB PoP APR grid and risk tiers** (inherits OQ-02-01). Near finco pricing (~25–35%) or near a "fair bank price" (~15%)? | §3.2 shows the price choice moves break-even as much as decision cost does. At 15% with finco-level losses the product is unviable | HLB product/treasury decision `[DECISION REQUIRED]` | Skill 22 |
| OQ-09-05 | **Merchant subsidy HLB can obtain** for 0% promotions (inherits OQ-02-12) | §3.5 gives only the required floor | Merchant/partner quotes; Sổ Bán Hàng merchant interviews | Skill 22 |
| OQ-09-06 | **Cost-of-funds basis.** 4.8% [S24] vs 3.68% [LC1] on different bases; HLB's own cost of funds, and the value of CASA deposits captured from Cohort B (inherits OQ-02-02) | A ~1.1 pp gap changes spreads. Deposit value is excluded from §3 | HLB treasury funds-transfer-pricing (FTP) rate for consumer lending | Skill 22 |
| OQ-09-07 | **Loss rate of approved thin-file loans at HLB.** Proxies bracket 4.1% (an NPL ratio, not a loss rate) to 11.5% (finco credit cost) (inherits OQ-02-03) | Decides whether Scenario A or B (or C vs D) applies | Champion–challenger back-test; pilot MOB3/MOB6 vintages | Skill 10 / 22 |
| OQ-09-08 | **Customer and merchant acquisition cost.** No Vietnam PoP CAC benchmark sourced. Nubank's "<US$5" CAC is a Low-confidence blog figure and is **not used** [R3] | Excluded from §3, so the true break-even is higher | HLB marketing and integration budgets; referral reward size (AD-01) | Skill 10 / 22 |
| OQ-09-09 | **Capital treatment (risk weight) of unsecured PoP loans in Vietnam.** Not researched. India's risk-weight hike cut small-ticket BNPL ~50% [R28][R29] | A capital charge reduces the spread in §3 | SBV capital-adequacy circular review | Skill 21 / 22 |
| OQ-09-10 | **Lifetime value.** Repeat-use and graduation-acceptance rates are unknown for HLB | LTV cannot be computed. Only per-loan contribution is shown | Pilot cohort tracking | Skill 10 / 22 |
| OQ-09-11 | **Channel access.** Can HLB obtain checkout placement where marketplaces steer to their own pay-later (0% seller fee on SPayLater [S22]) and large chains are captive (MWG–Cake [S21])? Links to D-08-2 | Determines whether channel 1 is direct/offline merchants or marketplaces | Partner outreach; team decision `[DECISION REQUIRED]` | Skill 22 |
| OQ-09-12 | **Fee schedule and referral reward size**: late fee, early repayment, any service fee; AD-01 reward amount | Revenue line 3 and acquisition cost; must stay consistent with the "one all-in price" UVP | HLB product decision `[DECISION REQUIRED]` | Skill 22 |

---

## Source Index

**New sources for this skill**

| # | Source | URL | Used for |
|---|---|---|---|
| LC1 | Tuổi Trẻ, "Cho vay không còn 'hái ra tiền' với ngân hàng, vì đâu 'người giàu cũng khóc'?" (8 Mar 2026), charts citing Widata | https://tuoitre.vn/cho-vay-khong-con-hai-ra-tien-voi-ngan-hang-vi-dau-nguoi-giau-cung-khoc-20260308112049813.htm | Sector NIM 3.03% (Q4 2025) vs 3.36% (Q4 2024); retail-bank group NIM 3.74%; YEA 6.42%; COF 3.68% (Medium: press citing a data vendor) |
| LC2 | Thời báo Tài chính Việt Nam, "NIM dò đáy, áp lực lợi nhuận lộ diện đằng sau đà tăng tín dụng" (9 Sep 2025), citing MB Securities and FiinGroup | https://thoibaotaichinhvietnam.vn/nim-do-day-ap-luc-loi-nhuan-lo-dien-dang-sau-da-tang-tin-dung-182936.html | Q2 2025 sector NIM 3.05% ("lowest in 5 years"); HDBank 5.2% (Medium) |
| LC3 | Hong Leong Bank Berhad, "HLB Announces FY2025 Results" | https://www.hlb.com.my/en/personal-banking/news-updates/hlb-announces-fy2025-results.html | Confirms that group results are reported without separate HLB Vietnam NIM or cost-of-funds figures (via search extract; Low–Medium) |

**Research notes (`_research-notes-07-13.md`) cited:** [R1] Nubank F-1 (low-and-grow; organic acquisition) · [R3] Whitesight Nubank CAC (cited only to exclude it) · [R8] HLB Malaysia "GRAB It Now" T&C · [R14][R15] Upstart FY2023 / 10-K · [R26] KBank digital lending · [R28][R29] Paytm / RBI risk weights · [R30] RBI Digital Lending Guidelines · [R37][R39] Vietnam black-credit apps · [R41] CFPB Apple/Goldman enforcement · [R46] Tilleke, Vietnam PDPL · [R50] Aydin, AER 2022 · [R56] personalised vs risk-based pricing · [R57] Keil, Berg, Burg & Puri, JFE 2025 / NBER w33152 (BNPL +~20% merchant sales) · [R60] Schmitt, Skiera & Van den Bulte, JM 2011 · [R61] Bryan, Karlan & Zinman, AEJ Micro 2015 · [R66] HLB VN × Sổ Bán Hàng · [R67] HLB VN eKYC / Digital CX release (Apr 2026) · [R68] HLB VN unsecured personal loan page · [R70] CIC 55.2M records · [R71] Báo Chính phủ, CIC incident; CIC holds no deposit data · [R77] Techcombank fast-loan page. Full URLs are in that file's Source Index.

**Market research (`02-discover/02-market-research.md`) cited:** [S3][S4] FiinGroup / VnEconomy (finco credit cost 11.5%; consumer lending +26%) · [S6] Nhân Dân (bank consumer NPL ~4.1%) · [S9][S10] Home Credit · [S11] FE Credit pricing example · [S13] HD Saison · [S14] Mcredit · [S18] MoMo · [S19] ZaloPay · [S20] CafeF lenders behind pay-later · [S21] MWG PayLater / Cake · [S22] Shopee seller fees · [S23] global BNPL merchant fees · [S24] Thời báo Ngân hàng (cost of funds) · [S29] Circular 43 draft · [S46] VIR (>70% of credit institutions use AI) · [S51] KiotViet bank loans · [S52] Vietnam.vn (HLB Sales Ledger) · [S53] Bajaj Finance / RBI / OJK analogues. Also Skill 02 F1, F2, F4, §2A, §5, Whitespaces 1–5, Trend 8, RF-4/7/8. Full URLs are in that file's Source Index.

**Other upstream (local):** `_requirements.md` [Brief] · `_context.md` (SD-1 to SD-7) · `01-foundation/01-product-strategy.md` (North Star; S4) · `02-discover/03-user-research.md` (Pains; S1, S36, S41) · `02-discover/04-problem-research.md` (§4 bottleneck, §5 non-AI test, §7 KPIs, OQ-04-02/03) · `02-discover/05-persona-research.md` · `02-discover/literature-review.md` (L9, L18, L19; OQ-LR-03) · `03-define/06-problem-statement.md` · `03-define/07-problem-solution-fit.md` (C1–C6, FM catalogue) · `03-define/08-v1-product-scoping.md` (feature codes, cohorts, D-08-1/2).

*Case-study files (`00-case-studies/…/lean-canvas-examples.md`) were used for format only and are not cited as evidence.*
