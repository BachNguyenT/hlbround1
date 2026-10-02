# Problem-Solution Fit Validation
**Product:** HLB Point-of-Purchase Credit Decisioning (working name) | **Skill:** 07 | **Status:** Done (2026-09-30) — **Depth: Full**
**Depends on:** problem-statement.md | **Feeds into:** v1-product-scoping.md, solution-proposal.md

> **Depth decision.** `/pipeline` may abbreviate Skill 07 only if elicitation was specific and uncontradicted **and** research found no material counter-evidence. Research found real counter-evidence: the Ant Group regulatory reset, over-indebtedness from digital credit and BNPL, enforcement against misleading 0% instalment marketing, and Skill 04's finding that rules capture much of the decision-stage value. So this skill runs at **full depth**.
>
> Citations: `[R#]` = entry # in `_research-notes-07-13.md` Source Index (verified research notes, 2026-09-30). `[Brief p.N]`, `[Skill0N]`, `[L#]` as elsewhere. Rubric tags `[R1]`–`[R4]` refer to the competition rubric, not the research-note numbers. Where the research notes mark a figure "via search extract", it is treated one confidence notch lower.

---

## Pre-filled Context
- **Problem statement (Skill 06, chosen HMW):** How might we give Vietnamese shoppers a fair, right-sized credit offer at the moment they want to buy, based on what HLB and its partners already know about them, and keep improving that offer as they repay? This includes shoppers without a bureau file.
- **Solution hypothesis (user-specified, `_context.md` SD-1 to SD-7):**
  - **Engine:** one central AI credit-profile engine. It is hybrid: ML models, a policy and guardrail rule layer, and a human "refer" band.
  - **Inputs:** bureau (CIC) plus bank-held and consented alternative data.
  - **Output:** a Customer Credit Profile with six fields:
    - risk grade
    - affordability headroom
    - evidence confidence
    - propensity/need
    - identity integrity
    - decision tier
  - **Consumers of that output:** six journey stages (Awareness → Consideration → Application & Instant Decision → Purchase & Repayment → Retention → Advocacy).
  - **Entry paths:** existing HLB customers, and new or thin-file applicants.

---

## 1. Fit Evidence Summary

```
Proposed Solution Type: Central, reusable credit-risk profile engine (bureau + proprietary bank
  data + consented alternative data), consumed across the customer lifecycle for targeting,
  offer personalisation, instant decisioning, behavioural limit management, cross-sell and
  referral — owned and funded on a deposit-taking bank's balance sheet.

Supporting Evidence:
  - Grab/GrabFin solved credit access for gig drivers and MSMEs by reusing platform earnings data
    for underwriting and repaying via daily earnings deductions → loan book US$536M at end-2024
    (+64% YoY) [R4]; self-reported: 68% of driver borrowers accessed formal credit for the first
    time through Grab; "1 in 2" borrowed to avoid unlicensed lenders [R5] (company impact page,
    no survey method disclosed — Medium).
  - **HLB Group itself** ran partner-data pre-selection of gig workers: HLB Malaysia × GFin
    Services "GRAB It Now" (Jul 2021) messaged pre-selected Grab drivers (min. RM24,000 annual
    Grab earnings, no HLB arrears) in-app to apply for HLB personal loans [R8]. This is a direct
    internal precedent for the Awareness-stage mechanic.
  - Nubank reuses one customer data platform + proprietary models for first limit, "low and grow"
    limit increases, cross-sell and referral-led acquisition; ~80–90% of customers acquired
    organically/word of mouth (SEC F-1) [R1]; FY2024 >114M customers [R2].
  - Upstart (CFPB No-Action-Letter review, 2019): alt-data/ML model approved 27% more applicants
    at 16% lower average APR vs a traditional model; applicants under 25 were 32% more likely to
    be approved; no disparities requiring further fair-lending analysis [R16] — simulation on
    Upstart's own data, not an independent RCT.
  - Kredivo (Indonesia) blends the national bureau (SLIK) with alternative data in ML scoring;
    >60% of users got first credit access through it; NPF held <5% in 2024 [R13] (press —
    Medium).
  - KBank (Thailand) launched data-driven unsecured digital lending **first to existing customers
    on a selective basis**, with automation + human review [R26] — the incumbent-bank pattern
    SD-1 adopts.
  - Sea/Shopee SPayLater: consumer & SME loans US$5.1B outstanding end-2024, 90+ NPL 1.2–1.4%,
    extended offline to >1,000 Indonesian electronics stores for phone purchases [R9][R10] — a
    direct point-of-purchase analogue.
  - Academic: digital-footprint variables alone reach AUC 69.6% vs 68.3% for a bureau score and
    complement it [R80][L1]; BNPL at the merchant raises sales ~20% [R57]; referral rewards
    conditioned on the referee's repayment improve repayment (enforcement effect) [R61];
    randomised limit increases drive instalment borrowing (11¢ per extra dollar in Q1, 28¢ by
    year 3) [R50].
  - Vietnam: CIC does **not** hold deposit/transaction data [R71] — HLB's CASA and salary-inflow
    signals are proprietary inputs no bureau-only competitor can replicate. Circular 64/2024 (Open
    API, consented cross-bank transaction history; full compliance 1 Mar 2027) [R73] and Decree
    94/2025 (sandbox covering credit scoring, open to credit institutions) [R74] provide legal
    rails for the engine's data.

Counter-Evidence / Risk Signals:
  - Ant Group's ecosystem-wide credit (Huabei/Jiebei tied to Alipay) triggered a halted ~US$37B
    IPO (Nov 2020) and forced restructuring into a licensed consumer-finance company (2021)
    [R17][R18][R19] → a single profile reused across payments, marketing and lending draws
    regulatory scrutiny if data flows are not licensed and consented.
  - India's RBI Digital Lending Guidelines (2022) ban automatic credit-limit increases without
    explicit consent recorded for each increase [R30]; RBI's Nov 2023 risk-weight hike led Paytm
    to cut sub-₹50,000 postpaid loans ~50% [R28][R29] → small-ticket BNPL is capital- and
    rule-sensitive.
  - Over-indebtedness: CGAP Kenya — 47% of digital borrowers repaid late, 12% defaulted, 16%
    borrowed to repay another loan [R35]; CFPB (2023) — BNPL users more likely to be highly
    indebted and to stack loans invisible to bureaus [R36] → lifecycle "grow and re-offer"
    mechanics can harm the thin-file, already-indebted users they target.
  - Credit-builder RCT: null average effect on credit scores; borrowers **with** existing loans
    fared worse [R62] → the "credit passport"/graduation idea can backfire for indebted users.
  - CFPB fined Apple and Goldman Sachs US$89M in total (Oct 2024), partly for misleading
    consumers about interest-free Apple Card Monthly Installments [R41] → personalised 0%
    PoP packages are an enforcement target if disclosure is weak.
  - Upstart originations fell 59% to US$4.6B in 2023 when loan buyers withdrew [R14][R15];
    Klarna FY2022 net loss ~SEK 10.4B with ~SEK 5.7B credit losses [R20] → good models do not
    save a funding-dependent or loosely-underwritten model.
  - Model drift: COVID-era credit models were "significantly affected, at times overridden, or
    even turned off" [R42] (Low–Medium) → continuous reassessment needs drift monitoring and
    human override.
  - Referral does **not** screen: Bryan–Karlan–Zinman found no peer-screening effect [R61] →
    "referred" must not be a risk-lowering feature in the engine.
  - Vietnam-specific: the CIC suffered a confirmed data incident in Sep 2025 [R71]; PDPL (in
    force 1 Jan 2026) requires explicit, verifiable, purpose-specific consent, fines up to 5% of
    revenue for cross-border violations [R46][R47]; >200 black-credit lending apps reported by
    police [R37] → consumers are primed to distrust app-based alt-data lending; a Banking
    Academy review (Jul 2026) notes Vietnam has no enumerated list of permissible alternative
    data, no right-to-explanation standard, and unclear purpose limitation for reusing
    utility/e-wallet data [R76].
  - Skill 04 §5 / literature review: most decision-stage value comes from data, not algorithm
    [L9][L18][L19] → "AI everywhere" is not automatically superior at the decision stage; the
    rubric requires per-component justification [Brief p.11].

Net Fit Assessment: **Moderate-to-Strong.** The *concept* — a bank-owned engine blending bureau +
  proprietary + consented alternative data, reused across acquisition, decisioning, limit
  management and cross-sell — has multiple scaled precedents (Grab, Nubank, Kredivo, KBank,
  SPayLater) and an **HLB Group internal precedent** (HLB Malaysia × Grab). Every documented
  failure is a failure of *governance, funding, consent, disclosure or affordability control*,
  not of the engine concept itself — which makes the failure modes designable-against.
```

---

## 2. Analogous Company Table

| Company | Problem | Solution Approach | Outcome | Key Learning |
|---|---|---|---|---|
| **Grab / GrabFin** (SEA) | Gig drivers & MSMEs lack formal credit | Underwrite on platform earnings; daily deduction from earnings wallet; merchant loans | Loan book US$536M end-2024 (+64%) [R4]; 68% of driver borrowers first-time formal credit (self-reported) [R5] | Platform-income data + earnings-linked repayment work for GIG; cash-flow-aligned schedules matter |
| **HLB Malaysia × Grab** (2021) | Reach creditworthy gig workers | Partner-data pre-selection; in-app invitation to apply for HLB personal loan [R8] | Campaign ran (outcome not disclosed) | **HLB Group already has the playbook** for Awareness-stage pre-selection via a partner |
| **Nubank** (LatAm) | Thin-file, underbanked consumers | Proprietary models on one data platform; low-and-grow limits; referral-led growth | >114M customers FY2024 [R2]; 80–90% organic acquisition [R1] | Low-and-grow + referral can scale; limits grow with history |
| **Kredivo** (Indonesia) | First-time borrowers | ML on bureau (SLIK) + alternative data | >60% first-time credit access; NPF <5% (2024) [R13] | Blend bureau + alt-data; don't replace the bureau |
| **KBank** (Thailand) | Unbanked/self-employed | Data-driven digital loans, first to selected existing customers; automation + human review [R26][R27] | Scaled programme (US$2.7B) [R27] | Start with known CASA customers, then extend to new-to-bank |
| **Sea / SPayLater** (SEA) | Checkout financing | Platform-data BNPL, extended offline to electronics stores [R9][R10] | US$5.1B loans; 90+ NPL 1.2–1.4% [R9] | PoP instalments at physical stores work when underwriting uses ecosystem data |
| **Mercado Pago** (LatAm) | E-commerce credit | Moved from generic bureau-style scores to in-house models on marketplace data [R12] | US$6.0B portfolio, 15–90 NPL 7.8% (Q4 2024) [R11] | Generic scorecards underperform on ecosystem data (expert interview — Low) |
| **Upstart** (US) | Near-prime/young borrowers | Alt-data ML underwriting via marketplace funding | +27% approvals, −16% APR (CFPB NAL review) [R16]; originations −59% in 2023 when funding withdrew [R14] | Model lift is real, but funding model matters — HLB's deposit base is the structural advantage |
| **Ant Group** (China) | Ecosystem consumer credit | One profile tied to payments + lending, off-balance-sheet funding | IPO halted 2020; forced restructuring 2021 [R17–R19] | Licensed, consented, on-balance-sheet data flows are non-negotiable |
| **Klarna** (EU/US) | Checkout BNPL | Fast approval, merchant-paid | FY2022 net loss ~SEK 10.4B, credit losses ~SEK 5.7B [R20] | Speed without underwriting discipline is expensive |
| **Paytm Postpaid** (India) | Small-ticket BNPL | Platform-originated, partner-funded | Sub-₹50k disbursals cut ~50% after risk-weight hike [R28] | Small-ticket unsecured credit is exposed to capital-rule shifts |
| **MoMo Ví Trả Sau** (Vietnam) | Instant wallet credit | In-app behaviour scoring + bank partners; limits ≤20M VND [R21–R23] | ~30M MoMo users [R24] | Vietnamese users already know instant wallet-scored BNPL; HLB must differentiate on ticket size, tenor, price and trust |

---

## 3. Failure Mode Catalogue

| # | Failure mode | Evidence | Which journey stage it threatens | Design countermeasure (carried to Skills 08/13) |
|---|---|---|---|---|
| FM-1 | **Regulatory reset of an ecosystem-wide profile** reused beyond its consented purpose | Ant Group [R17–R19]; China P2P → zero [R31][R32] | All stages | Per-purpose consent (scoring ≠ marketing ≠ referral), on-balance-sheet lending, no pass-through accounts [R30][R46] |
| FM-2 | **Silent limit increases** that users didn't ask for | RBI DLG explicit-consent-per-increase rule [R30]; Aydin: increases drive instalment borrowing for years [R50] | Purchase & Repayment, Retention | Graduation limit increases are *offered*, never auto-applied; affordability re-checked at each step |
| FM-3 | **Over-indebtedness and loan stacking** | CGAP Kenya 47% late / 12% default [R35]; CFPB BNPL stacking [R36]; Skill 03 Pain #11 | Retention, Consideration | CIC pull at each new draw + internal exposure cap; affordability headroom field gates every offer |
| FM-4 | **Misleading 0% / instalment marketing** | CFPB Apple/Goldman US$89M [R41] | Awareness, Consideration | All-in cost and schedule shown before acceptance; risk-tier rate grid published; no WTP-based pricing [R56] |
| FM-5 | **Targeting on propensity rather than incrementality/suitability** — marketing credit to those most likely to borrow, who are often the riskiest | Agarwal et al.: marginal propensity to borrow 59% for FICO <660 vs ~0 for >740 [R49]; literature caution X5 | Awareness, Retention | Uplift + suitability targeting with a permanent random holdout [R53][R55] |
| FM-6 | **Referral treated as screening** / debt pushed onto friends | No peer-screening effect [R61]; Skill 03 Pain #8 (reference harassment) | Advocacy | Reward only after referee's on-time repayments (enforcement effect [R61]); "referred" excluded as a risk feature; no reference contacts |
| FM-7 | **Graduation/credit-builder harms indebted users** | Burke et al. RCT [R62] | Retention → Advocacy (credit passport) | Passport/graduation gated on total-debt affordability; targeted to no-existing-debt thin files |
| FM-8 | **Funding/capital fragility** | Upstart [R14][R15]; Paytm [R28] | All | HLB deposit funding; engine and book owned by the bank |
| FM-9 | **Model drift / regime shift** | COVID-era models overridden [R42]; SR 11-7 monitoring [R45] | Purchase & Repayment (continuous reassessment) | Champion–challenger, drift monitoring, human override, periodic recalibration |
| FM-10 | **Opacity / trust failure** | Apple Card NYDFS: no unlawful discrimination found, but perceived lack of transparency undermined trust [R40]; Skill 03 Pain #6 | Application & Instant Decision | Reason codes for every decision; "what would change this" path |
| FM-11 | **Invasive data harvesting** (contacts, gallery) | OJK ban after debt-shaming [R34]; Vietnam black-credit apps [R37–R39] | Application | No contact/gallery access; only enumerated, consented sources |
| FM-12 | **Single bureau dependency** | CIC data incident, Sep 2025 [R71] | Application & Instant Decision | Bureau-unavailable fallback rules; minimal bureau-data retention |
| FM-13 | **AI where rules suffice** — cost and governance without incremental value | Skill 04 §5; [L9][L18][L19]; Brief rubric R2 | Application & Instant Decision | Per-component "why AI beats baseline" test; rules layer retained as champion where ML adds no lift |

---

## 4. Go / Pivot / Stop Recommendation

```
Recommendation: GO — conditional (with 6 binding conditions)
Confidence: Medium
Rationale:
  1. The engine concept has scaled, sourced precedents across SEA/LatAm (Grab, Kredivo, KBank,
     SPayLater, Nubank) and an HLB Group internal precedent (HLB Malaysia × Grab 2021) [R4][R5]
     [R8][R13][R26][R9][R1].
  2. Each documented failure (FM-1 to FM-13) traces to governance, consent, funding, disclosure or
     affordability controls — all designable-against — not to the concept of a reusable profile.
  3. HLB has two structural advantages the failures lacked: deposit funding (vs Upstart/Paytm) and
     proprietary CASA/salary-inflow data that CIC does not hold [R71][Skill02 F4].
  4. Confidence is Medium, not High, because the Vietnam-specific predictive lift of alternative
     data (OQ-LR-01), consent uptake (OQ-03-04) and HLB's own cost breakdown (OQ-04-03) remain
     unverified.

Binding conditions (must be reflected in Skill 08 scope and Skill 13 design):
  C1. Per-purpose, revocable consent: scoring, marketing/targeting, and referral are separate
      consent purposes (PDPL [R46]); marketing uses of the profile (Awareness, Retention) run only
      on marketing consent.
  C2. Limit increases and new offers are *offered*, never auto-applied; affordability headroom
      re-computed at every offer/draw; CIC checked at each new draw (FM-2, FM-3).
  C3. Human "refer" band + reason codes + drift monitoring (AI Law human oversight [Skill02 RF-8];
      FM-9, FM-10).
  C4. Targeting on uplift + suitability with a random holdout, not raw propensity (FM-5).
  C5. Referral rewards conditioned on the referee's on-time repayment; "referred" never lowers a
      risk grade; no reference contacts (FM-6).
  C6. Phased data rollout by legal readiness: Phase 1 — HLB CASA/salary inflows + CIC + Sổ Bán
      Hàng ledger (merchant consent); Phase 2 — Open API consented cross-bank data (Circular
      64, full compliance 1 Mar 2027); Phase 3 — telco/e-wallet via Decree 94 sandbox [R73][R74].

If Pivot: not recommended. The closest "pivot" signal is Skill 04 §5 (rules capture much of the
  decision-stage value). It is accommodated *inside* the chosen solution via condition FM-13 /
  per-component justification rather than by abandoning the engine.

Risks to monitor:
  - Consent uptake for alternative-data sources (A2, OQ-03-04)
  - MOB3 30+ delinquency of thin-file approvals vs the risk-appetite ceiling (A1, A6)
  - Incremental (not total) take-up from targeted offers vs holdout (FM-5)
  - Stacking: share of PoP customers with ≥2 concurrent consumer loans at CIC (FM-3)
  - Complaint rate on marketing contacts (Skill 03 Pain #9)
  - Referee delinquency vs non-referred cohort (FM-6)
  - Model drift indicators (PSI, approval-rate shift) (FM-9)
```

---

## Updates to earlier open items from this research

| Item | Update |
|---|---|
| OQ-02-13 / OQ-03-07 / OQ-08 / OQ-LR-02 (dated CIC coverage) | **Partially resolved:** CIC database reached **55.2 million records, covering >70% of the adult population**, per a CIC 2022–2025 review reported Jan 2026 [R70] (press citing CIC/SBV — Medium). This updates the older "~60% (2022)" and "51% (c.2018)" figures. Roughly 3 in 10 adults remain outside CIC coverage. |
| Skill 01 Pillar 1 context | HLB Vietnam's existing unsecured personal loan requires VND 30M–500M, salary ≥ VND 10M/month, ≥12 months employment, Hanoi/HCMC/Bình Dương only, max DSR 60% [R68]. That excludes most of the PoP ticket range (1.6M–90.1M VND) and all GIG/MSME/FTB segments, which is independent evidence of the gap the engine targets. |
| Skill 02 speed benchmark | Techcombank online instalment loan: "approval and disbursement within 3 minutes", up to VND 100M [R77] is an additional in-market bank benchmark. |
| HLB eKYC capability | HLB Vietnam's National ID Registry integration + eKYC cut onboarding ~80%, from ~300 s to ~80 s (company release, Apr 2026) [R67]. This is an existing asset for requirement R-3. |

---

## Open Items Raised by This Skill

| # | Item | Carry to |
|---|---|---|
| OQ-07-01 | No public list of Decree 94 credit-scoring sandbox participants was found. Whether HLB must enter the sandbox to use telco/e-wallet data (Phase 3) remains unresolved (links to OQ-02-17). | Skill 08 / 22 |
| OQ-07-02 | HLB Malaysia × Grab (2021) campaign outcomes are not public, so the internal precedent proves the mechanism existed, not its results. | Skill 22 (state as precedent only) |
| OQ-07-03 | Whether an HLB Vietnam 0% card-instalment product already exists could not be verified [R notes Topic 4]. | Skill 08 / 22 |
| OQ-07-04 | Several analogue figures are company self-reported or taken from search extracts: Grab's 68% [R5], Kredivo [R13], Mercado Pago's expert view [R12]. The submission should cite them as company claims. | Skill 22 |

---

## Source Index
All `[R#]` citations resolve to the numbered Source Index in [`_research-notes-07-13.md`](../_research-notes-07-13.md) (80 entries, compiled 2026-09-30, each with URL and confidence/source-type label). Upstream citations `[Brief]`, `[Skill02]`, `[Skill03]`, `[Skill04]`, `[L#]` resolve to `_requirements.md`, `02-market-research.md`, `03-user-research.md`, `04-problem-research.md` and `literature-review.md` respectively.

Key primary sources used (subset, for convenience):
- Grab 20-F FY2024: https://www.sec.gov/Archives/edgar/data/1855612/000185561225000013/ck0001855612-20241231.htm
- HLB Malaysia "GRAB It Now" T&C: https://www.hlb.com.my/content/dam/hlb/my/images/campaigns/2021/grab-pl-03/hlb-grab-it-now-tnc-en.pdf
- CFPB Apple/Goldman enforcement: https://www.consumerfinance.gov/enforcement/actions/apple-inc/
- RBI Digital Lending Guidelines: https://www.rbi.org.in/scripts/FS_Notification.aspx?Id=12382
- Bryan, Karlan & Zinman (NBER w17883): https://www.nber.org/papers/w17883
- Aydin (AER 2022): https://www.aeaweb.org/doi/10.1257/aer.20191178
- Agarwal et al. (NBER w21567): https://www.nber.org/system/files/working_papers/w21567/w21567.pdf
- Burke et al. (NBER w26110): https://www.nber.org/papers/w26110
- CGAP Kenya digital credit survey: https://cgap.org/node/3430
- Báo Chính phủ, CIC data incident / CIC holds no deposit data: https://en.baochinhphu.vn/viet-nams-central-bank-confirms-credit-data-incident-at-credit-information-center-111250912192742851.htm
- HLB VN unsecured personal loan: https://hlbank.com.vn/vi/personal-banking/loan/unsecured-personal-loan.html
