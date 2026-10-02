━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
# PRODUCT REQUIREMENTS DOCUMENTATION (PRD)
## HLB CreditPulse: AI Credit-Profile Engine for Point-of-Purchase Lending
**Product:** HLB CreditPulse (Hong Leong Bank Vietnam) | **Version:** 1.0 | **Date:** 2026-09-30 | **Status:** In Review
**Context:** Business Challenge 2026, Ideation Round: *AI in Finance and Banking* (case client: Hong Leong Bank Vietnam) | **Track:** Proposing AI
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

> **How this PRD is organised (user instruction, 2026-09-30).** The document is ordered to answer the competition rubric [Brief p.11], starting with research-backed **strategic direction**:
> - **§1** Strategic direction
> - **§2** Problem Diagnosis (R1, 20%)
> - **§3** Intervention Justification, AI track (R2, 30%)
> - **§4** Solution Concept (R3, 35%)
> - **§5** Data & Assumptions (R4, 10%)
> - **§6** One-page Visualization plan (R5, 5%)
>
> §7–§16 hold the full product specification (personas, functional spec, system design, failure behaviour, NFRs, risks, parking lot). An engineer or reviewer can build and verify v1 from them, and they prove the concept in §2–§4 is buildable.
>
> **Evidence rule.** Nothing here is a silent assumption. Every claim cites its source skill file or an external source, and every unknown is either a named HLB parameter or a logged assumption. Skill 22 certified **zero unresolved items** (§13.2).
>
> **Citation keys:**
> - `[Brief p.N]`: the case brief (`_requirements.md`)
> - `[SkillNN …]`: the pipeline file for that skill
> - `[R#]`: `_research-notes-07-13.md`
> - `[L#]`: `literature-review.md`
> - `[S#]`: `02-market-research.md` sources, cited as `[Skill02 S#]`

---

# 0. ONE-PAGE SUMMARY

### 0.1 What v1 does, for whom (3 sentences)
**HLB CreditPulse** is a central, hybrid AI engine. It builds one consented, explainable **Customer Credit Profile (CCP)** for every existing HLB customer and every point-of-purchase (PoP) applicant, including those with no credit-bureau file, from data the bank already holds plus CIC and partner data. Six journey stages use that profile:
- **Awareness:** invite the right existing customers.
- **Consideration:** tailor affordable instalment packages.
- **Application:** decide **inside the checkout session**.
- **Purchase & Repayment:** refine the profile after every repayment.
- **Retention:** recommend suitable repeat purchases.
- **Advocacy:** grow through repayment-conditioned referrals.

It serves HLB's PoP customers, prioritising the gig workers, online merchants and first-time borrowers that bureau-only review declines today [Brief pp.6–8].

### 0.2 Why (problem + evidence)
HLB's PoP lending runs **one manual, document-and-bureau review for every application**. It costs **380,000 VND** and takes **1.8–4.6 business days**, whether the ticket is 1.6M or 90.1M VND [Brief pp.5, 9]. The consequences:
- Thin-file segments (**44.0%** of inbound) are declined despite visible digital income [Brief pp.7–9].
- The decision arrives after the purchase moment, so customers delay, abandon, or turn to informal credit [Brief p.4].
- Short-tenor tickets below roughly **10–17M VND lose money on the review cost alone** [Skill02 §2A].
- Every sourced Vietnamese competitor decides in **5 seconds to ~30 minutes** [Skill02 F1].

### 0.3 Success looks like
- **North Star: Good In-Moment Loans (GIML)/month.** These are loans decided in-session, booked, and not 30+ days past due at month-on-book 3 [Skill10 §1].
- **Bottleneck-relief KPIs:**
  - In-session decision rate: from 0% to ≥ 70%.
  - End-to-end checkout time: p50 ≤ 3 min, p90 ≤ 10 min (from 1.8–4.6 days).
  - Blended cost per decision: from a flat 380,000 VND to a refer-share-weighted cost.
  - Thin-file approval rate held within an HLB-set delinquency ceiling [Skill10 §2].
- **Absolute volume targets** are HLB parameters. The brief gives no volumes (D-04).

### 0.4 What is explicitly NOT in v1
- Telco / e-wallet / platform third-party data (Phase 3, pending Decree 94 sandbox clarity).
- Merchant self-service portal.
- Assisted eKYC fallback.
- Rich "path-to-yes" decline coaching.
- Financial-literacy nudges.
- Marketing frequency caps beyond consent and suitability.

**Rejected permanently:**
- broadcast campaigns;
- willingness-to-pay pricing;
- cash-for-sign-up referrals;
- phone contact or gallery harvesting.

See §14.

### 0.5 Rubric scorecard: where each criterion is answered

| Rubric criterion [Brief p.11] | Weight | Answered in | Strongest evidence |
|---|---|---|---|
| **R1 Problem Diagnosis**: isolate the core bottleneck, root cause and mechanism | 20% | §2 | A single undifferentiated workflow drives both thin-file exclusion and loss-making unit economics. Break-even arithmetic [Skill02 §2A]; a segment × problem matrix [Skill04 §3.3] |
| **R2 Intervention Justification**: how the mechanism resolves the bottleneck; measurable benefits; feasibility; **why AI is superior and operationally viable** | 30% | §3 | A per-component "AI vs rules" test; scaled analogues (Grab, Nubank, Kredivo, KBank, SPayLater) and an **HLB Group precedent** (HLB MY × Grab 2021) [R8]; a latency and feasibility verdict [Skill14 §2] |
| **R3 Solution Concept**: data-to-decision architecture; community impact and sustainability; operational KPIs measuring bottleneck relief | 35% | §4 | 17 bank-data signals (the CIC holds **no deposit data**, so the CASA signals are HLB-only [R71]); a six-field CCP; an explainable tier policy; 6 stage feature sets; KPIs tied to the bottleneck |
| **R4 Data & Assumptions**: explicit, realistic, anchored | 10% | §5 | 28-row assumption log; named parameters in place of invented numbers; Skill 22 closure certificate |
| **R5 Visualization**: 1-page 1920×1080 PDF | 5% | §6 | Storyboard for the one-pager |

---

# 1. STRATEGIC DIRECTION (research-backed)

*Sources: Skills 01, 02, 07, 09, the literature review, and the research notes.*

### 1.1 Vision
> **For** Vietnamese shoppers and online sellers who earn and transact digitally but are invisible to bureau-based underwriting, **HLB CreditPulse** gives a **fair, right-sized credit decision inside the purchase moment**, and keeps improving the offer as customers repay. **Unlike** HLB's current branch-designed review, and unlike informal apps that approve fast with no affordability check, it **decides on consented data the bank already holds**. It sends only genuinely uncertain cases to people, and it verifies identity in real time without documents. [Skill01 §1][Skill06 §1]

### 1.2 Why now: the market is moving to in-moment, bank-funded credit
- **Demand is large and growing.** Vietnam BNPL GMV was about **US$2.14–2.61B in 2025**. Forecast CAGR is 21–27%, and the 2021–2024 CAGR was 58.3%. About US$1.41–1.72B of that is instalment volume compatible with HLB's 3–24-month tenors [Skill02 §2].
- **In-moment decisions are table stakes.**
  - Home Credit decides in about 3 min, Home PayLater in 60 s, MoMo in about 3 min, MWG PayLater in about 2 min, and Fundiin in about 5 s [Skill02 F1].
  - Techcombank advertises "approval and disbursement within 3 minutes" [R77].
  - **HLB takes 1.8–4.6 business days** [Brief p.9].
- **Pay-later has consolidated onto regulated balance sheets.**
  - Atome and about 10 start-ups exited.
  - SPayLater, MoMo, ZaloPay and MWG PayLater are all funded by banks or finance companies [Skill02 F3].
  - A bank entering is **on-trend, not contrarian**.
- **AI is expected, but must prove its value.**
  - 58% of finance functions used AI in 2024 (Gartner, the source of the brief's p.2 statistic) [Skill01 S1].
  - Over 70% of Vietnamese credit institutions use AI [Skill02 S46].
  - The rubric rewards **incremental value over the baseline**, not AI for its own sake [Brief pp.10–11].
- **The rules now enable consented, data-driven lending, and make unconsented data illegal.**
  - Circular 29/2026 (effective 15 Aug 2026) raises the small-value loan threshold to 400M VND and lets lenders set their own electronic-lending limits [Skill02 RF-2].
  - Open API (Circular 64/2024) enables consented cross-bank data, with full compliance by 1 Mar 2027 [R73].
  - The personal data protection law (PDPL, Law 91/2025, effective 1 Jan 2026) requires consent for each purpose and treats financial data as sensitive [Skill02 RF-7].
  - The AI Law classes automated credit decisions as **high-risk**. A **new system must pass conformity assessment before deployment** [Skill14 B-01][Skill18 N6].

### 1.3 North Star and strategic pillars

**North Star: Good In-Moment Loans (GIML)/month** [Skill10 §1]. The metric only rises when three things improve together: speed (requirement R-2), reach into thin files (R-1), and responsible outcomes (the delinquency part of R-2). Approval rate alone could be bought with loose credit.

| Pillar | Thesis | Evidence | What would invalidate it |
|---|---|---|---|
| **1. Decide on data, not documents** | Thin-file applicants are **scored on the wrong evidence at the wrong time**, not unscoreable. Their income sits in HLB accounts, partner platforms and sales ledgers [Brief p.5] | The CIC holds **no deposit or transaction data** [R71]. Digital-footprint signals alone reach AUC 69.6% vs 68.3% for a bureau score [R80][L1]. About ⅔ of the approval gain from alternative-data lending comes from the **data**, not the algorithm [L9] | Low consent uptake (RK-06); weak predictive power in Vietnam (RK-05) |
| **2. Cost proportional to the ticket** | A tiered engine concentrates the 380,000 VND of human effort on the uncertain band only | ACB auto-approves ~60% in 5–10 min [Skill03 S36]. Home Credit refers only lower-score cases [Skill03 S46]. The break-even ticket falls sharply as the refer share drops (§3.3) | A refer band larger than 30% (RK-13) |
| **3. Responsible, targeted inclusion across the lifecycle** | One profile reused for targeting, pricing, re-scoring, graduation and referral turns a one-off decision into a relationship. Guardrails keep speed from recreating informal-credit harms | Nubank "low-and-grow" [R1]. Randomised limit expansion sustains instalment use (11¢ per extra dollar in Q1, 28¢ by year 3) [R50]. Referral rewards tied to repayment improve repayment [R61]. Kenya digital credit: 47% late, 12% default where guardrails were absent [R35] | Over-indebtedness (RK-08) |

### 1.4 Where HLB wins (unfair advantage, in combination)
1. **Cheaper funding.** Sector cost of funds is about 4.8%, against about 7.1% for Home Credit and 9.5–9.8% on its 2026 bonds. So HLB can price fairly **and** subsidise 0% promotions more cheaply [Skill02 F4][Skill09 §2.10].
2. **Proprietary behavioural data.** CASA and salary-inflow data is not in the CIC [R71]. HLB can read existing records as evidence of behaviour (§4.2).
3. **Merchant-ledger channel.** Sổ Bán Hàng (in-app HLB account opening, bookkeeping, QR, e-invoice) gives both PoP acceptance points and MSME income signals [R66].
4. **Identity rails already built.** HLB's National-ID-Registry eKYC cut onboarding from ~300 s to ~80 s [R67].
5. **Group precedent.** HLB Malaysia × Grab (2021) pre-selected Grab drivers using partner data and invited them in-app [R8].
6. **Regulatory trust.** A bank brand with no forced insurance, transparent all-in pricing and no third-party collection contact positions against wallet fee stacks (late fees up to 21%) and "black credit" apps (>200 flagged by police) [Skill02 S18][R37].

### 1.5 Strategic bets validated by analogues (Skill 07: GO, conditional)

| Analogue | What it proves for HLB |
|---|---|
| **Grab / GrabFin** (loan book US$536M, +64%; 68% of driver borrowers first-time formal credit, self-reported) [R4][R5] | Platform-earnings underwriting plus earnings-aligned repayment works for gig workers in Southeast Asia |
| **HLB Malaysia × Grab** (2021) [R8] | **HLB Group has already run** partner-data pre-selection of gig workers |
| **KBank** (Thailand) [R26] | Incumbent-bank pattern: start digital lending with known current/savings account customers, then extend to new-to-bank |
| **Nubank** (>114M customers; 80–90% organic acquisition) [R1][R2] | One data platform reused for first limit, low-and-grow, cross-sell and referral |
| **Kredivo** (>60% first credit access; non-performing financing under 5%) [R13] | Blend the bureau and alternative data; don't replace the bureau |
| **Upstart** (CFPB review: +27% approvals, −16% APR) [R16] | Alternative-data ML lifts approvals at a lower price. Its later funding collapse (−59% originations) [R14] shows a **deposit-funded bank** is structurally safer |

**Designed against the documented failures** (Ant, Klarna, Paytm, Kenya digital credit, the Apple/Goldman 0% instalment case). Every failure traces to governance, consent, funding, disclosure or affordability, so each is a binding design condition (C1–C6) [Skill07 §3–4]:
- consent recorded separately per purpose;
- limit increases offered, never applied automatically;
- a human refer band;
- targeting by uplift and suitability, with a holdout group;
- referral rewards conditioned on repayment;
- data sources phased in as each becomes legally ready.

### 1.6 Strategic arc

| Horizon | What's true |
|---|---|
| **v1 (pilot)** | Phase-1 data: HLB CASA and cards, CIC, Sổ Bán Hàng, application and checkout signals. In-session decisions at partner merchants; pre-approved offers to existing customers; the learning loop runs (champion/challenger, randomised corridor, holdouts) |
| **12 months** | Most PoP applications decided in-session. Blended cost per decision far below 380,000 VND. Thin-file approvals rising within HLB's risk ceiling. The ML challenger is live in the segments where it beats the rules. Open API cross-bank data added (Circular 64 deadline 1 Mar 2027) [Skill01 §5][Skill08 §2] |
| **3 years** | PoP is a profitable HLB business line. Thin-file customers **graduate** into bank-observable account holders with CIC history (the brief's CASA-first mitigation becomes a growth loop [Brief p.8]). Phase-3 data enters only through the Decree 94 sandbox [R74] |

---

# 2. PROBLEM DIAGNOSIS: Rubric R1 (20%)

*Source: `04-problem-research.md` (verdict confidence: High on mechanism, Medium on magnitude).*

### 2.1 The core bottleneck
> **One undifferentiated, manual, document-and-bureau underwriting workflow is applied to every point-of-purchase application, whatever the segment, ticket size or available signal. It was inherited unchanged from branch lending.** [Skill04 §4]

It is one **tightly coupled decision point** with two symptoms:
- **Exclusion (P1):** the workflow reads only bureau files and collateral, so earning thin-file applicants are declined.
- **Unviable economics and speed (P2):** the same fixed-cost, multi-day human review hits a 1.6M VND ticket and a salaried applicant with five years of clean history alike.

Fixing the workflow relieves both. That is the "highest operational leverage" the rubric asks for [Brief p.11].

### 2.2 Mechanism: where time, cost and customers are lost
```
Merchant checkout ─► redirect OUT of the purchase flow into a branch-designed process [Brief p.4]
   ─► physical/scanned documents  (31% can't/won't give a labour contract; 33% a payslip [Skill03 S18])
   ─► verification (forgeable: 455 fake IDs, 607 applications in one finco case [Skill03 S31])
   ─► officer review against CIC + collateral (platform income, e-wallet activity and sales data
      "exist… but a document-based review does not draw on them" [Brief p.5])
   ─► manual file assembly, calls, write-up
   ─► decision after 1.8–4.6 business days, 380,000 VND, same for every ticket [Brief p.9]
   ─► the purchase moment has passed → delay, abandon, or informal credit [Brief p.4]
```

### 2.3 Root cause (5 Whys, condensed)
1. **Why are thin-file customers declined and small tickets unprofitable?** Every case goes through one manual bureau-and-collateral review.
2. **Why is there no fast lane?** There is no triage step. No automated path exists even for clear-cut cases, unlike ACB (~60% auto-approved) and Home Credit [Skill03 S36, S46].
3. **Why isn't visible cash flow used?** No standardised, consented, policy-approved pathway turns it into a defensible "yes" [Skill03 Pain #3, #12].
4. **Why does branch logic still govern a checkout product?** The journey was designed for branch lending, where per-file review is proportionate [Brief p.4].
5. **Root cause:** the decision **process** was never redesigned for high-volume, small-ticket, digitally evidenced demand.

### 2.4 The economics prove it (derived from sourced inputs)

| Tenor | Break-even ticket just to recover the 380,000 VND review | HLB minimum ticket (1.6M VND) is… |
|---|---|---|
| 3 months | ~16.9M VND | ~9.5× below break-even |
| 6 months | ~9.6M VND | ~5.4× below |
| 12 months | ~5.2M VND | ~2.9× below |

Assumed net spread: 29.8% APR (FE Credit's own example) − 4.8% bank cost of funds − 11.5% finance-company credit cost. **The review alone is 23.75% of the minimum ticket** [Skill02 §2A][Brief p.9].

### 2.5 Segment × problem matrix

| Segment (share of inbound [Brief p.9]) | P1 Thin-file assessment | P2 Speed/cost | P3 Identity/friction | P4 Targeting |
|---|---|---|---|---|
| Salaried with credit (35.5%) | Low | **Medium: easy cases still wait days** | Low–Med | Low |
| Salaried without credit (20.5%) | **Med–High** | High | Medium | Medium |
| Gig / platform workers (15.0%) | **High** | High | Medium | **High** |
| Online merchants, MSME (14.5%) | **High** | High (stock windows) | Medium | Medium |
| First-time borrowers (14.5%) | **High** | High | **High** | **High** |

The speed/cost problem hits **100%** of inbound, including salaried customers with clean credit. So the bottleneck is the process itself, not only the thin files. Identity (P3) is a **precondition** for moving faster safely. Targeting (P4) is **layered value** on a working decision engine [Skill04 §3–4].

### 2.6 What would change the verdict (tested, not assumed)
- **If the 380,000 VND is mostly fixed bureau or vendor fees**, automation saves less. HLB's cost breakdown is unknown, so this is logged (RK-14).
- **If declined thin-file applicants are genuinely bad risks**, the P1 opportunity shrinks. The randomised corridor measures exactly this (RK-05).
- **If the rules blocked alternative data**, the bottleneck would be regulatory. Research shows the rules are **enabling with consent**, not blocking [Skill02 RF-2, RF-9, RF-10].

---

# 3. INTERVENTION JUSTIFICATION (AI track): Rubric R2 (30%)

*Sources: Skills 07, 09, 12, 14, the literature review, and the research notes.*

### 3.1 How the core mechanism resolves the bottleneck
CreditPulse replaces **one undifferentiated review per application** with **one continuously maintained profile per customer**, plus a **tiered decision**:

| Bottleneck element (§2) | CreditPulse mechanism | Effect |
|---|---|---|
| Every case gets the same manual review | Decision tiers (pre-approved / instant approve / refer / decline). A **human refer band only for uncertain cases** | Human cost is concentrated where judgment adds value. Clear cases are decided in seconds |
| Documents and bureau-only evidence | **Consented bank-held behavioural evidence** (17 signals, §4.2) + CIC + one declared income figure for new-to-bank applicants | Thin-file applicants are assessed on the income they actually receive |
| Decision after the purchase moment | In-session orchestration: eKYC ∥ CIC → features → scoring → policy | Checkout p50 ≤ 3 min end-to-end (feasible: ~1.5 min system time [Skill14 §2]) |
| Forgeable paper verification | Chip-CCCD NFC + liveness + VNeID (existing HLB rails) + an integrity score | Real-time identity without documents (R-3) |
| Untargeted offers, no learning | Awareness, Retention and graduation reuse the same profile; every repayment re-scores it | Targeted rather than broadcast offers (R-4); a learning loop |

### 3.2 Why AI creates superior value, component by component
The rubric asks us to *demonstrate* AI's value [Brief p.11]. Skill 04's non-AI test found a rules redesign captures much of the decision value [Skill04 §5][L9]. So CreditPulse uses **AI only where evidence shows it beats rules**, and keeps rules where transparency matters more [Skill12 §2].

| Component | Method | Why AI beats the baseline here (or why it doesn't) | Evidence |
|---|---|---|---|
| **Risk grade (thin files)** | Scorecard **champion** + gradient-boosted **challenger** on a randomised split | The ML advantage is **largest for thin files** and shrinks as credit history lengthens. The challenger goes live per segment only where it measurably beats the champion | [L4][L18][L19]; Upstart +27% approvals / −16% APR [R16] |
| **Income from irregular inflows** | Statistical/ML stable-income estimator; the affordability cap stays a **rule** | Gig and merchant income is volatile, and payslips don't exist. The estimator reads payout and sales rhythm. The cap is a legal and ethical boundary, so it stays transparent | [Skill03 Pain #10]; [Brief p.7] |
| **Targeting (Awareness, Retention)** | Suitability filter + random holdout at launch → **uplift model** | **The strongest AI case.** Propensity is not incrementality: marginal propensity to borrow is 59% for FICO <660 vs ~0 for >740, so naive targeting finds the riskiest customers. Uplift modelling targets customers an offer actually helps | [R49][R53][R55][L62] |
| **Behavioural re-scoring** | Behavioural refresh from launch → survival / time-to-default modelling once PoP history exists | Post-loan repayment is the richest signal a thin-file customer will ever produce. Nubank uses survival modelling + risk ranking for limit decisions across ~122M customers | [Skill12 N1]; [R50] |
| **Fraud / integrity** | Rules + unsupervised anomaly score over mandatory biometric eKYC | ML catches novel patterns that rules miss. Biometrics is regulatory infrastructure, not AI | SBV: fraud incidents down ~50% after mandatory biometrics [L52] |
| **Decision tier, affordability cap, referral rewards** | **Rules (deliberately not AI)** | Explainable by construction. Satisfies the AI Law's human-oversight and transparency duties | [Skill02 RF-8]; [R61] |

**The AI value is measured, not asserted.** Every ML component runs against a rules champion or a random holdout, so each decision records its incremental value per segment:
- KR 2.1: thin-file approval, challenger vs champion;
- KR 3.1: uplift, treatment vs holdout;
- KR 3.4: retention, treatment vs holdout [Skill10 §2].

### 3.3 Measurable business benefits

| Benefit | Baseline [Brief p.9] | v1 target / effect | Evidence and derivation |
|---|---|---|---|
| **Speed** | 1.8–4.6 business days | p50 ≤ 3 min, p90 ≤ 10 min end-to-end (D-11) | eKYC ~80 s [R67]; CIC <10 s in parallel [Skill14 T6]; scoring in milliseconds [Skill14 T9][T10] |
| **In-session decision rate** | 0% | ≥ 70% (refer band ≤ 30%) | ACB ~60% auto-approved [Skill03 S36] (A-10-2) |
| **Cost per decision** | 380,000 VND flat | e.g. **84,000–124,000 VND** blended at a 20% refer share and 10k–60k VND automated cost *(sensitivity, not a forecast)* | Blended = (1 − refer) × automated + refer × 380k [Skill09 Table 3.3a]. HLB's automated cost is an HLB parameter |
| **Unit economics** | 3-month break-even ≈ 16.9M VND | Falls to **~2.1–7.0M VND** across 10–40% refer share at 10k automated cost; 12-month to **~0.6–2.2M VND** | [Skill09 Table 3.3b] (scenario A spread 13.5%; hypothetical automated cost) |
| **Inclusion** | Thin-file segments have the "lowest approval rates" | Starter-limit approvals for thin files, within the MOB3 ceiling | Nubank low-and-grow [R1]; Kredivo >60% first-time access [R13] |
| **Merchant sales** | — | Merchant value proposition | Offering BNPL raises merchant sales ~20% [R57] |
| **Customer experience** | Documents, redirect, days | No documents, all-in cost before acceptance, reasons on decline | [Skill03 Pains #1, #4, #6, #7] |

### 3.4 Operational viability
- **Technically feasible, with conditions.** No component is infeasible; 13 of 29 need a spike (9 spikes) [Skill14 §0].
- **Latency feasible.** System time is ~1.5 min at p50 for a new-to-bank applicant. The binding risk is the **web-to-app hand-off**: browsers can't read the ID chip, so a web shopper switches to HLB Connect [Skill14 §2, B-06].
- **Build vs buy.**
  - Build the models, consent ledger and CCP on open-source parts: Feast, CausalML / scikit-uplift [Skill14 T14–T16].
  - **Reuse** HLB's eKYC.
  - The decision engine is build-or-reuse: FICO, Experian or Provenir only if HLB already licenses one [Skill14 §5].
- **Hosting.** In-country, on portable components, to meet data-localisation and security rules (D-09) [Skill14 §4].
- **Regulatory pathway.**
  - Automated electronic appraisal is **expressly permitted** (Circular 29/2026) [Skill02 RF-2].
  - Consent is per purpose (PDPL + Decree 356, which also requires finance firms to keep end-to-end processing logs and disclose scoring and profiling in consent notices) [Skill19 P2].
  - AI Law conformity must be complete **before deployment** [Skill14 B-01].
  - Collections follow the VNBA Code of Conduct (effective 31 Mar 2026) [Skill20 E2].
- **Effort.** About **293–458 person-weeks** estimated for v1 Must scope including platform work, with 9 spikes to narrow the range [Skill14 §7][Skill16].
- **Cold start is handled by design.** Scorecard champion + starter limits + a **randomised approval corridor** create unbiased thin-file outcome labels before the ML challenger goes live, avoiding the reject-inference trap [L20][L36][Skill14 B-04].

### 3.5 What is innovative
1. **Reading the bank's own records as behavioural evidence.** Examples: salary-day stability, the cash cushion before payday, platform-payout rhythm, e-wallet top-up rhythm and bills paid from the current account. All come from the current/savings account, which the bureau doesn't hold (§4.2).
2. **One profile, six uses.** The same CCP drives targeting, pricing, the instant decision, re-scoring, graduation and referral. Most competitors bolt a score onto a single step.
3. **Learning built in.** Champion/challenger, a randomised corridor and permanent holdouts mean the system **proves its own incremental value** to regulators and to HLB.
4. **Responsible growth mechanics.**
   - Referral rewards are released only after the *invitee* repays on time (the enforcement effect [R61]).
   - Graduation is always **offered, never auto-applied** [R30].
   - Gig and merchant borrowers can choose **weekly repayment aligned to their payout day** (flexibility lowered default in an RCT [R64]).

### 3.6 Failure modes designed out (from Skill 07)

| Failure mode | Countermeasure in CreditPulse |
|---|---|
| Regulatory reset of an ecosystem profile (Ant) [R17] | Consent per purpose; on-balance-sheet; no pass-through accounts |
| Silent limit increases [R30] | Graduation is only offered, with explicit consent |
| Over-indebtedness and stacking [R35][R36] | Affordability gate (0 breaches); CIC anti-stacking at every draw; early warning |
| Misleading 0% marketing [R41] | All-in cost before acceptance; published rate grid |
| Propensity targeting finds the riskiest customers [R49] | Uplift + suitability + holdout |
| Referral pushes debt on friends [R61] | Rewards conditioned on repayment; no reference contacts |
| Model drift [R42] | PSI alerts; champion fallback ≤ 5 min; drift-safe mode |
| Contact/gallery harvesting [R34] | Excluded permanently |

---

# 4. SOLUTION CONCEPT: Rubric R3 (35%)

*Sources: Skills 10, 12, 13, 15, 19.*

### 4.1 Data-to-decision architecture (inputs → logic → outputs → business actions)
```
INPUTS (consent-gated)                LOGIC (HLB CreditPulse)                   OUTPUT                     BUSINESS ACTIONS (6 journey stages)
─────────────────────                 ───────────────────────                   ──────                     ──────────────────────────────────
Phase 1 [v1]                          Consent ledger (SC / MK / RF)             CUSTOMER CREDIT PROFILE    AWARENESS   pre-approved offer to never-used,
 • HLB CASA & salary inflows (S1–S8)   ↓                                        (versioned, explainable)               low-risk, affordable customers (+holdout)
 • HLB cards & loans (S9–S10)         Feature store (online/offline,            F1 Risk Grade A–E          CONSIDER.   2–4 packages ≤ F2, all-in cost first
 • HLB Connect usage (S12, propensity  point-in-time)                          F2 Affordability headroom  APPLICATION in-session tier; eKYC; refer band;
   only)                               ↓                                        F3 Evidence confidence                 starter limit; CASA-first
 • Sổ Bán Hàng ledger (S5)            Models: scorecard champion ·               F4 Propensity & need      PURCHASE &  re-score on every repayment; early
 • CIC bureau (S14)                    GBM challenger · income estimator ·      F5 Identity integrity      REPAYMENT   warning; offered graduation;
 • Application + declared income +     behavioural · early-warning (rules v1) · F6 Decision tier                       weekly option (GIG/MSME); humane collections
   eKYC (chip-CCCD/VNeID)              uplift (after holdout) · integrity        + reason codes           RETENTION   suitable repeat products (+holdout);
 • Checkout footprint (S13)            ↓                                                                               anti-stacking
 • PoP repayments (S15)               Policy & decision layer (rules):                                    ADVOCACY    repayment-conditioned invite;
Phase 2 [Should]: Open API (S16)       tiers · limits · rate grid · reason codes                                      refer-a-merchant; good-payer passport
Phase 3 [Later]: telco/e-wallet/       ↓                                                                  OPERATOR    officer workbench (refer band);
  platform (S17)                      Governance: decision log · drift · fairness · human oversight                   risk console
```

### 4.2 Assessing the data HLB already has, innovatively (signal catalogue, condensed)

| Signal | Source | What it replaces / proves | Segment most served |
|---|---|---|---|
| S1 Inflow regularity index | CASA | Replaces the payslip; stable inflows are the brief's own basis for salaried-no-credit [Brief p.7] | Salaried no credit, gig |
| S2 Salary-day stability | CASA | Employment stability without a labour contract | Salaried no credit |
| S3 Pre-payday liquidity buffer | CASA | Direct affordability realism | All |
| S4 Platform-payout signature | CASA (payouts into HLB) | "Income received through platforms… regular platform activity" [Brief p.7] | **Gig** |
| S5 Sales-flow signature (QR, Sổ Bán Hàng, e-invoice) | CASA + Sổ Bán Hàng | "Digital sales histories, recurring business cash inflows" [Brief p.8] | **MSME** |
| S6 Bill-payment punctuality (paid from the current/savings account) | CASA | "On-time utility bill payments", read from the bank side [Brief p.8] | **First-time** |
| S7 Wallet top-up rhythm | CASA → e-wallet transfers | "E-wallet top-ups", read **without** third-party wallet data [Brief p.8] | **First-time** |
| S8 Savings discipline | Deposits | Forward planning, shock buffer | All |
| S9 Essential vs discretionary spend | Cards / QR | Affordability; categories for recommendations | Salaried |
| S10 Existing HLB credit conduct | HLB loans/cards | Internal behavioural score | Existing customers |
| S11 Relationship stability | KYC / core banking | Stability; sudden-change integrity flag | All |
| S12 Digital engagement | HLB Connect | **Propensity only, never risk** (fairness) | — |
| S13 Checkout footprint | Merchant + application | Complements the bureau for thin files (AUC 69.6% vs 68.3% bureau [R80]) | First-time |
| S14 CIC file | CIC | Kept as a core input, not replaced | All |
| S15 PoP repayment behaviour | HLB ledger | The continuous-reassessment engine | All |
| S16 Open API cross-bank (Phase 2) / S17 third-party (Phase 3) | Other banks / telco etc. | Future reach | Salaried no credit, gig, first-time |

**Excluded by design:** contacts, gallery and social scraping; protected attributes as model inputs (used only for fairness monitoring); "referred" as a risk feature [Skill13 §1.2]. A spike (SP-1) confirms that CASA metadata supports S2, S4, S6, S7 and S9 (RK-03).

### 4.3 Customer Credit Profile and tier policy

> Full engine specification: [`04-design/13a-credit-assessment-engine.md`](../04-design/13a-credit-assessment-engine.md). It covers model targets and segmentation, the WoE scorecard champion, the monotonic GBM challenger with SHAP reason codes, income-estimation paths, the headroom and annuity-limit formulas, F3/F5 rules, the decision table, validation metrics, the learning loop and the parameter register.

| Field | Meaning | Method |
|---|---|---|
| F1 Risk Grade (A–E) | Probability-of-default band | Scorecard champion + GBM challenger |
| F2 Affordability headroom (VND/month) | Maximum safe new instalment | Rules over the income estimate. Thin files: min(declared income × haircut, cap) − CIC obligations − living-cost buffer (D-06) |
| F3 Evidence confidence | BUREAU+BANK / BANK-OBSERVED / CONSENTED-PARTNER / THIN | Rules |
| F4 Propensity & need | Incremental benefit of an offer; likely categories | Suitability (v1) → uplift |
| F5 Identity & integrity (0–100) | Applicant is genuine | Rules + anomaly model over eKYC |
| F6 Decision tier | Pre-approved / instant approve / refer / decline + reason codes | Policy rules |

**Tier policy (explainable; thresholds are HLB parameters)** [Skill13 §1.1, with the Skill 22 confirmations]:
1. **Low integrity** → REFER (identity review), or DECLINE if the mismatch is confirmed.
2. **Headroom below the cheapest package** → DECLINE (affordability), with a path message.
3. **Grade A/B with BUREAU+BANK, BANK-OBSERVED or CONSENTED-PARTNER evidence** → PRE-APPROVED (existing customer) or INSTANT-APPROVE.
4. **Grade A/B/C with THIN evidence** → INSTANT-APPROVE at a **starter limit** (low-and-grow). A randomised **corridor** also approves some marginal THIN cases, so the bank can learn their real risk.
5. **Grade C, or grade D with non-THIN evidence** → REFER, with an immediate affordable counter-offer.
6. **Everything else** → DECLINE with reason codes and a path message.

Every approval also has to pass the CIC stacking check. If the check fails, grade A/B cases are REFERred and all others are DECLINEd.

### 4.4 The six journey stages: pain point → feature → profile fields → action
Features are designed first for the **user-specified pain points**, plus what the brief makes mandatory (R-1 to R-4) [Skill13 §1.3].

| Stage | Pain point addressed | v1 features | CCP fields used | Business action |
|---|---|---|---|---|
| **Awareness** | Identify never-used, high-potential, low-risk customers to target (user) | AW-01 target list · AW-02 suitability + holdout · AW-04 pre-approved limit in HLB Connect · AW-03 uplift model (Should) | F1, F2, F3, F4, F6 | Suitable, **incremental** invitations; broadcast is rejected (R-4) |
| **Consideration** | Offer different packages according to financial status (user); fair, transparent cost (brief) | CO-01 package builder (tenors 3–24 months; never above F2) · CO-02 all-in cost and schedule | F1 (rate tier), F2, F6 | 2–4 affordable packages; total payable shown first |
| **Application & Instant Decision** | In-moment decision; identity without documents (brief R-2/R-3) | AP-01 in-session decision · AP-02 chip-CCCD/VNeID eKYC (+ web-to-app hand-off) · AP-03 integrity · AP-04 refer band + counter-offer · AP-05 starter limit · AP-06 CASA-first · AP-07 CIC fallback · MX-01 merchant API / hosted page / QR | All | Decision within the session; uncertain cases to an officer within SLA |
| **Purchase & Repayment** | Keep track and continuously reassess and refine the profile (user) | PR-01 re-score per repayment · PR-02 early warning (rules v1) · PR-03 **offered** graduation · PR-04 CIC reporting · **PR-05 weekly payout-aligned option (GIG/MSME)** · **PR-06 collection conduct (VNBA code)** | All; S15 new | Good payers graduate; struggling payers supported early; humane collections |
| **Retention** | Suitable products with high PoP-purchase potential, marketed to suitable users (user) | RT-01 recommendations (rules-first) + holdout · RT-02 anti-stacking · RT-03 ready-limit dashboard (Should) | F2, F4, F6 | Up to 3 affordable, relevant suggestions; suppressed when the customer is stacking loans |
| **Advocacy** | "Something innovative" (user) | AD-01 **repayment-conditioned invite** · AD-02 refer-a-merchant via Sổ Bán Hàng (Should) · AD-03 good-payer passport (Should, debt-gated) | F1, F6 | Growth without pushing debt onto friends; merchant network flywheel |

### 4.5 Community impact and sustainability
- **Financial inclusion.**
  - 44.0% of inbound applicants are thin-file [Brief p.9], and about 3 in 10 Vietnamese adults are outside CIC coverage [R70].
  - CreditPulse gives them a fair first "yes" (starter limits), then a path to a **bank relationship and a CIC credit history** (CASA-first plus CIC reporting). This turns "almost invisible" customers into bank-observable ones [Brief p.8].
- **A safe alternative to black credit.**
  - Consumers turn to informal apps that approve in "20–30 minutes without income verification" [Skill01 S5].
  - Police flagged >200 such apps [R37].
  - A regulated lender deciding **equally fast, at transparent bank prices, with humane collection** directly competes with this harm.
- **Small-business growth.**
  - Merchants gain sales from BNPL (~+20% [R57]).
  - Sổ Bán Hàng merchants are onboarded as acceptance points and as MSME borrowers scored on their own ledgers. Their stock-timing problem ("a few days late and it's sold out" [Skill03 S40]) is solved by speed.
- **Financial sustainability.**
  - Proportional cost per decision fixes the loss-making small ticket (§3.3).
  - Deposit funding avoids the funding collapses of Upstart and Paytm [R14][R28].
  - Guardrails (G-1 to G-8) keep growth inside risk appetite.
- **Environmental.**
  - Replaces physical and scanned document handling and branch visits with a fully digital journey [Brief p.4].
  - This benefit is qualitative only. No sourced carbon figure is claimed.

### 4.6 Operational KPIs that directly measure bottleneck relief (no macro statistics)

| KPI | Measures relief of… | Baseline [Brief p.9] | v1 target |
|---|---|---|---|
| **In-session decision rate** (KR 1.1) | The multi-day manual path | 0% | ≥ 70% |
| **End-to-end checkout time p50 / p90** (KR 1.2, D-11) | Decision after the purchase moment | 1.8–4.6 business days | ≤ 3 min / ≤ 10 min |
| **Blended cost per decision** (KR 1.3) | Fixed 380,000 VND per file | 380,000 VND | ≤ ~114,000 VND + automated cost (at a 30% refer share; derived) |
| **Refer-band SLA** (KR 1.4) | Human effort only where it's needed | n/a | ≥ 90% decided within 2 business hours |
| **Thin-file approval rate at MOB3 30+ ≤ ceiling** (KR 2.1) | Exclusion of earning thin-file applicants | "Lowest approval rates" | Uplift vs rules champion; ceiling is an HLB parameter |
| **Evidence-confidence upgrade rate** (KR 2.3) | Invisible → bank-observable | n/a | HLB parameter |
| **Incremental take-up vs holdout** (KR 3.1) | Untargeted offers | n/a | Significant positive uplift |
| **North Star GIML** | Speed + reach + responsible outcomes together | 0 | ≥ 60% of booked loans by month 3 (A-10-1) |

**Guardrails:**
- MOB3 30+ ceiling;
- confirmed fraud;
- stacking;
- **0 affordability breaches**;
- fairness ratio ≥ 0.8 (alert);
- complaints;
- consent withdrawal;
- drift PSI (0.10 / 0.25) [Skill10 §3].

Every KPI maps to instrumented events [Skill19 §5.4].

---

# 5. DATA & ASSUMPTIONS: Rubric R4 (10%)

*Sources: Skills 21 and 22. The brief allows "reasonable assumptions… stated clearly" [Brief p.10].*

### 5.1 How assumptions are handled
1. **Case data first.** Every baseline number comes from the brief: 380,000 VND per application; 1.8–4.6 days; 1.6M–90.1M VND tickets; 3–24-month tenors; segment mix 35.5 / 20.5 / 15 / 14.5 / 14.5 [Brief p.9].
2. **Credible benchmarks next.** Derived figures show their inputs:
   - FE Credit pricing, sector cost of funds and finance-company credit cost [Skill02 §2A];
   - ACB auto-approval [Skill03 S36];
   - Home Credit and Techcombank decision times [Skill02 S9][R77].
3. **No invented HLB numbers.** Volumes, approval and loss rates, the APR grid and about 28 policy parameters are **named, configurable parameters that HLB sets at launch**, never guessed (D-04).
4. **Sensitivity tables, not point estimates,** wherever an input is unknown (cost per decision [Skill09 §3.3]).
5. **Unverifiable legal details are marked "legal to confirm".** They are logged as accepted risk RK-23 for a legal review at gate G0 (D-05).

### 5.2 Key assumptions (from the 28-row log [Skill21 §2])

| # | Assumption | Anchor | Confidence | Validated by |
|---|---|---|---|---|
| AS-01 | Thin-file digital signals predict repayment in Vietnam | Foreign peer-reviewed evidence [L1][L4][L9] | Medium | Randomised champion/challenger pilot |
| AS-02 | Customers consent to data sharing at useful rates | Mixed: 31–33% resist documents; 49.5% worry about data misuse [Skill03 S18, S42] | Low–Med | Consent-funnel A/B |
| AS-04 | Automation makes cost proportional to the ticket | ACB ~60% auto [Skill03 S36] | Medium | HLB cost breakdown + refer-share back-test |
| AS-14 | GIML ≥ 60% of booked loans; refer band ≤ 30% | ACB benchmark | Medium | Pilot |
| AS-18 | CASA metadata supports signals S2, S4, S6, S7, S9 | Standard bank data; not HLB-verified | Low–Med | Spike SP-1 |
| AS-21 | Core banking exposes booking/ledger APIs for in-session booking | Not verified | Low–Med | Spike SP-4 |
| AS-22 | One declared income figure (haircut + cap) is acceptable for thin-file affordability | Design fix; FE Credit also uses a declared income floor [Skill02 S11] | Medium | Credit-policy sign-off; pilot loss |
| AS-06 | Declines of earning thin files are a process gap, not deliberate risk appetite | Brief: declined "despite officers observing operational cash flows" [Brief p.8] | Medium | HLB credit policy |
| A-10-3 | p50 ≤ 3 min end-to-end is achievable | ~1.5 min system time [Skill14 §2]; in-market 3-min claims [R77] | High | Pilot timing |

### 5.3 Named HLB parameters (set at launch, not assumed)
**Credit policy:**
- tier thresholds, `policy.integrity_floor`, `policy.dsr_cap`, `policy.living_cost_buffer_vnd`;
- `policy.starter_limit_vnd`, `policy.declared_income_haircut`, `policy.thin_headroom_cap_vnd`;
- `policy.corridor_share` plus the corridor loss budget, `policy.cic_timeout_s`, `policy.cic_fallback_mode`;
- the risk-tier rate grid and fee schedule;
- `policy.graduation_on_time_instalments_N`, `policy.referral_reward_on_time_instalments_K`.

**Marketing, servicing and operations:**
- `campaign.holdout_share`;
- `refer.approval_validity`, `ops.business_calendar`;
- `ew.contact_hours`, `ew.max_supportive_messages_per_cycle`;
- `repayment.weekly_pattern_min_weeks`.

**Targets and ceilings:** the per-segment MOB3 30+ ceiling (G-1), and volume and scaling tiers.

Full list: Skill 17 §0, Skill 19 §4–6, Skill 20 §8.

### 5.4 Evidence quality
**Sources:**
- 5 discovery files (about 250 sources);
- a 65-source literature review (mostly peer-reviewed or regulator material);
- 80 research notes for fit and design;
- per-skill source indexes.

**Verification:** figures taken from search summaries are flagged as such. Company self-reported figures are labelled as company claims (Grab's 68% [R5], Kredivo [R13]). Claims that couldn't be traced were **removed** rather than cited [literature review OQ-LR-12][Skill19 Source Index note].

---

# 6. VISUALIZATION PLAN: Rubric R5 (5%)

**Format:** one-page 1920×1080 (16:9) PDF [Brief p.11]; HLB logo, Noto Sans [Brief p.12]. The one-pager itself is a separate deliverable. This section is its storyboard, ordered by rubric weight and reading flow.

| Zone (left→right, top→bottom) | Content | Source section | Visual hierarchy |
|---|---|---|---|
| Header strip | "HLB CreditPulse: one AI credit profile, a decision inside the purchase moment" + UVP line | §1.1 | Primary |
| **A. Diagnosis (R1)** | Before-flow (redirect → documents → CIC review → 1.8–4.6 days) with the bottleneck highlighted; break-even mini-table (3-month ticket must be ≥16.9M VND just to cover review); 44% thin-file callout | §2.2, §2.4 | Primary |
| **B. Architecture (R3 core)** | Inputs → CreditPulse → **6-field CCP** → 6 journey-stage actions (§4.1 diagram, simplified) | §4.1, §4.3 | **Largest zone** |
| **C. Why AI (R2)** | Component table in 5 icons: thin-file scoring, income estimation, uplift targeting, re-scoring, fraud. "Rules where transparency matters" note; champion/challenger + holdout = proven incremental value | §3.2 | Primary |
| **D. KPIs (R3)** | Four bottleneck KPIs, before → after: 0% → ≥70% in-session; days → ≤3 min; 380k VND → blended; thin-file approvals within ceiling; plus the GIML North Star | §4.6 | Primary |
| E. Impact & sustainability (R3) | Inclusion → CASA → CIC history loop; safe alternative to black credit; merchant +~20% sales | §4.5 | Secondary |
| F. Feasibility & risk (R2) | Analogues strip (Grab, HLB MY×Grab, KBank, Nubank); AI Law before deployment; in-country hosting | §1.5, §3.4 | Secondary |
| Footer (R4) | "Key assumptions" (5 bullets from §5.2) + "HLB-set parameters" note + source line | §5 | Tertiary |

---

# 7. PRIMARY PERSONAS & JOURNEY

*Sources: Skills 05 and 15 (persona choice confirmed in Skill 22, D-02).*

### 7.1 Persona cards (condensed)

| | **Anh Hùng**: gig/ride-hailing driver (Lane B, new-to-bank) | **Existing HLB account-holder, no credit history** (Lane A) | **Chị Lan**: HLB credit-risk / underwriting lead (operator) |
|---|---|---|---|
| Segment share | GIG 15.0% | SAL-N 20.5% | — |
| Goal | Cover lumpy costs (e.g. an 11.6M VND phone) without payslips or losing riding time [Skill03 S1] | Pay over time for a planned purchase, only when relevant and affordable | Approve thin files defensibly; spend team time only on uncertain cases |
| Frustration | "Giờ ế lắm… 12 tiếng… 300.000 đồng" (slow days: 12 hours for 300k VND); fixed instalments vs volatile income [Skill03 S1]; no contract or payslip (31% / 33%) [Skill03 S18] | Blanket cross-sell calls [Skill03 S9]; data-misuse worry 49.5% [Skill03 S42] | "Evaluation data lacks standardized systems" [Skill03 S38]; every file rebuilt by hand [Brief p.5] |
| Tech proficiency | Medium–High (platform and e-wallet apps) | Medium–High (HLB Connect) | Medium |
| What CreditPulse gives them | In-session starter limit on declared income + payout evidence; weekly payout-aligned option (PR-05) | Pre-approved limit; tailored packages; offered graduation | Refer band with the evidence pre-assembled; policy she can defend |

Secondary persona: **Chị Phụng**, online MSME seller. She follows Lane A if she is on Sổ Bán Hàng with an HLB account, otherwise Lane B. Loan purpose is purchase finance at partner merchants (D-03) [Skill05].

### 7.2 Aha moment and critical path
- **Aha moment:** the Lane B applicant is **approved in-session at a starter limit that fits their income, with the total cost shown, and without documents**.
  - Time-to-aha target: p50 ≤ 3 min end-to-end.
  - Secondary aha: the first **graduation offer** ("my limit grows because I paid on time") [Skill15 §2].
- **Critical path, Lane B:**
  1. Choose "Pay over time" at the partner checkout.
  2. Pick a package, with the all-in cost shown.
  3. Give scoring consent and a declared income figure.
  4. Chip-CCCD NFC + liveness (a web checkout hands off to HLB Connect).
  5. INSTANT-APPROVE at the starter limit.
  6. e-contract + OTP.
- **Critical path, Lane A:** open the offer → pick a package → face biometric → INSTANT-APPROVE (seconds of system time).

### 7.3 What breaks the experience if the journey isn't respected
- Asking for documents.
- Redirecting out of the checkout.
- Showing a package above affordability.
- Silent limit increases.
- Marketing without consent.
- Contacting relatives or references.
- A technical failure shown as a decline (it must read as "we need a little longer").

[Skill15][Skill20 C-1–C-9]

---

# 8. FUNCTIONAL SPEC: EPICS, FEATURES, STORIES

*Sources: Skills 16 and 17.* **The normative, implementable specification is [`05-specify/17-user-stories.md`](../05-specify/17-user-stories.md):** 123 stories and about 545 Given/When/Then ACs, every policy value written as a named parameter. This section indexes it in dependency order so an engineer can navigate and plan. Every story there has at least 2 ACs, including negative and edge cases.

| Epic (journey stage) | v1 features | Stories | Key behaviour (representative ACs) |
|---|---|---|---|
| **EP-7 Consent, Governance & Oversight** (built first) | E-01 consent ledger · E-09 reason codes · OP-01 officer workbench · GV-01 model governance · OP-02 console (Should) | US-021–023 (consent) · US-024–025 (reasons) · US-026–029 (workbench) · US-030–035 (governance) · US-036 (console) | Every CCP read checks the consent purpose, otherwise it is refused and logged (US-008). Every decision carries ≥1 reason code, otherwise it is not released (US-024). An override requires a justification (US-028). PSI > 0.25 triggers champion fallback (US-031) |
| **EP-0 Engine & CCP** | E-02 ingestion · E-05 CCP + API · E-06 hybrid scoring · E-07 policy + rate grid · E-08 refresh · PF-01 platform · E-03 Open API (Should) | US-001–009 (ingestion, features, CCP) · US-010–013 (models) · US-014–017 (policy, limits, grid, parameters) · US-018–019 (refresh) · US-020 (Open API) | Six-rule tier policy incl. the CONSENTED-PARTNER approve rule (US-014 AC 7). F2_thin from declared income (US-009 AC 4/4b). Protected attributes and S12 cannot be risk inputs (US-005). Challenger only on a randomised split (US-011) |
| **EP-3 In-Session Decision & Identity** ★ critical path | AP-01 to AP-07 | US-110–117 (decision, e-contract, latency) · US-118–123 (eKYC, integrity, fraud) · US-124–127 (refer band) · US-128 (starter limit) · US-129 (CASA-first) · US-130–131 (CIC fallback) | Lane B form: phone, chip NFC, one declared income, consents; no document upload (US-111). 3 eKYC attempts → REFER, and network drops don't count (US-120, NET-06). Refer → immediate affordable counter-offer (US-124). CIC timeout → cached / REFER / capped (US-130) |
| **EP-8 Merchant & Partner Integration** | MX-01 API widget / hosted page / QR · HUB-01 Pay Later hub shell | US-140–145 | Signed sessions; the merchant sees only APPROVED_BOOKED / REFERRED_PENDING / DECLINED / CANCELLED / EXPIRED, never reason codes (US-140, US-144) |
| **EP-2 Packages & Transparent Cost** | CO-01 · CO-02 · CO-03 (Should) | US-100–108 · US-109 | Never show a package above F2 (US-100). Show the all-in cost before acceptance, with no bundled add-ons, and store a disclosure hash (US-106–107) |
| **EP-4 Reassessment & Graduation** | PR-01 to PR-06 | US-220–221 (re-score) · US-222–225 (early warning) · US-226–228 (graduation) · US-229–230 (CIC reporting) · US-231–233 (**weekly repayment**) · US-234–236 (**collection conduct**) | Graduation only after N on-time instalments + refreshed F2 + a clean CIC check, **explicitly accepted** (US-226–227). No third-party contact; contact hours respected; only approved templates (US-234) |
| **EP-1 Pre-approval Targeting** | AW-01 · AW-02 · AW-04 · AW-03 / AW-05 (Should) | US-200–201 (target list) · US-202–205 (suitability, holdout, re-check, measurement) · US-206–209 (offer card, re-validation, self-view, opt-out) · US-210–211 (uplift) · US-212 (partner) | Holdout never contacted (US-203). Consent and suitability re-checked at delivery (US-204). The self-initiated "My limit" view needs no MK consent; legal to confirm (US-208) |
| **EP-5 Repeat Offers & Anti-Stacking** | RT-01 · RT-02 · RT-03 (Should) | US-240–243 · US-244–246 · US-247 | ≤3 recommendations, capped by F2, with a Retention holdout (US-240 AC 1b). Anti-stacking gate on every offer path (US-245) |
| **EP-6 Referral & Merchant Flywheel** | AD-01 · AD-02 / AD-03 (Should) | US-260–265 · US-266–268 · US-269–270 | Reward released only after the invitee's K on-time instalments (US-262). The inviter never sees invitee credit details (US-263). "Referred" is not a risk feature (US-264) |

**Story counts:**
- **123 stories**: 109 Must, about 14 Should.
- **About 545 ACs.**
- Sizes: S 55 · M 56 · L 13 · XL 0 (addendum included).

---

# 9. SYSTEM DESIGN

*Sources: Skills 13, 14 and 19.*

### 9.1 UX flows and information architecture
- **Flows:** full happy, alternative and error paths for all six stages, Lanes A and B [Skill13 §2]. These include the Skill 14 corrections:
  - web-to-app hand-off for chip NFC;
  - parallel CIC call with a timeout;
  - weekly repayment and collection conduct (Skill 22).
- **Information architecture** [Skill13 §3]:
  - the HLB Connect "Pay Later" hub: My limit, Offers, My plans, Invite & earn, Passport, Why this decision, Privacy & consent;
  - merchant checkout: widget, hosted page or QR;
  - officer workbench;
  - risk console;
  - campaign workspace.

### 9.2 Architecture and stack
**Services:**
- Consent service
- Ingestion (batch + event stream)
- Feature store (online/offline, point-in-time)
- Model layer
- Policy & decision service
- CCP store/API
- Stage services: campaign, package, checkout decision, servicing/graduation, recommendation, referral ledger
- Case management
- Governance and monitoring [Skill13 §4]

**Stack (in-country, portable):**
- Kafka-class event bus [Skill13 D3]
- Feast feature store [Skill14 T14]
- In-house models, with CausalML or scikit-uplift for uplift [Skill14 T15][T16]
- Rules-as-code decision service, or HLB's existing decision platform if licensed [Skill14 §5]

**Rejected alternatives:**
- Region-bound managed services (data localisation) [Skill14 §4]
- Buying third-party alternative-data scores in v1 (PDPL provenance; Phase 3) [Skill14 §5]
- Deep learning (no gain over GBM [L19])

### 9.3 Data model
**37 entities (DM-01 to DM-37)**:
- Customer data is referenced by core-banking customer ID (CIF), never duplicated.
- The consent ledger, decision log and CCP history are append-only.
- Raw CIC payload is kept for at most 30 days.
- Nine sensitive-data categories are flagged under Decree 356, including transaction history, credit records, biometrics and ID images.
- Protected attributes sit in a separate fairness-only store.

CRUD matrix, ownership map, PII register, 40+ analytics events (every KPI covered), retention rules R-01 to R-17 and training lineage are in [Skill19].

### 9.4 Integration surface

| Dependency | Used for | If unavailable |
|---|---|---|
| CIC | Bureau file, stacking check, reporting | Cached pull (Lane A) / REFER / capped starter; re-check within 24 h |
| C06 / VNeID / chip-CCCD (existing HLB integration) | eKYC | Hold package 30 min; retry or refer; not counted as an attempt |
| HLB core banking | CIF, current/savings accounts, loan booking, ledger | Hold approval ≤ 30 min, then cancel (approve-and-guarantee not adopted, D-07) |
| Sổ Bán Hàng | Merchant ledger (S5), merchant onboarding | Evidence downgraded if data is stale > 48 h |
| Merchants | Checkout sessions, webhooks | Status endpoint; webhook retries to 24 h |
| SMS/OTP | Signing, notifications (no links unless requested, per Circular 50/2024) | Secondary gateway; Smart OTP |
| Open API banks (Phase 2) | Cross-bank inflows | Asynchronous only; not on the checkout path |

[Skill14 §3][Skill20 §4]

### 9.5 Build vs buy
- **Build:** models, consent ledger, CCP, stage services, merchant checkout.
- **Use open source:** Feast; Kafka-class bus; CausalML / scikit-uplift.
- **Reuse:** HLB eKYC; any existing HLB decision platform or loan-origination case tool (spike SP-9).
- **Buy if required:** AI Law conformity-assessment service.

[Skill14 §5]

---

# 10. BEHAVIOUR UNDER FAILURE

*Source: Skill 20.* About 120 error states (ERR-xxx), 15 empty states, 38 edge cases, 17 dependency fallbacks and 14 network rules are defined, each with Vietnamese copy.

**Principles:**
- **A technical failure never reads as a decline** (REFER wording).
- **Fail closed** on consent and decision-log failures.
- No blame, no threats, no third parties.
- **No links in SMS** unless the customer requested one (Circular 50/2024 Art. 17(3)).
- Network drops never count as eKYC attempts.
- The server is the source of truth; writes are idempotent.

**Business rules confirmed (D-07):**
- A full return cancels the loan with no interest or fees; a partial return reduces principal.
- An instant approval is valid until the session ends; an officer approval is valid for 24 h.
- CIC down with no cache → REFER.
- A due date missed because of an HLB outage has its late fee waived and triggers no negative CIC report.
- One application in flight per customer.
- Disclosed terms are honoured within the validity window.
- Champion drift → drift-safe mode.

---

# 11. NON-FUNCTIONAL REQUIREMENTS

*Source: Skill 18. Every target has a measurement method there.*

| Area | Requirement |
|---|---|
| **Compliance (Vietnam)** | 17 instruments. Launch gates: **AI Law conformity before deployment**; PDPL DPIA; purpose-specific verifiable consent on every CCP read; Decree 356 end-to-end processing logs and annual assessment; eKYC biometrics (Circulars 17/2024, 50/2024); electronic lending (Circular 39 as amended, Circular 29/2026); CIC reporting (Circular 15/2023); e-transactions law 2023; VNBA collection code |
| **Security** | OWASP ASVS 5.0 **L3** for the decision, identity, consent and CCP paths (L2 elsewhere); TLS ≥ 1.2; AES-256 at rest with HSM keys; staff MFA; penetration tests before go-live and annually; no PII in logs |
| **Performance** | Checkout **p50 ≤ 3 min / p90 ≤ 10 min end-to-end** (D-11); submit → decision p50 ≤ 8 s / p99 ≤ 20 s; scoring API p95 ≤ 200 ms; widget LCP ≤ 2.5 s |
| **Reliability** | Decision path **99.9%/month** (D-10); RTO ≤ 1 h, RPO ≤ 5 min; **RPO 0** for the decision log and consent ledger; degraded modes per dependency |
| **Scalability** | Parameterised tiers (launch / 6 m / 12 m; HLB volumes); load test at 3× the forecast peak |
| **Accessibility** | WCAG 2.1 AA on the hub, hosted checkout and workbench; Vietnamese first; plain-language cost disclosure |
| **AI/model** | 100% reason codes; monthly fairness snapshots (0.8 ratio alert); PSI 0.10 / 0.25; champion fallback ≤ 5 min; model cards; override logging; lineage |
| **Retention** | Accounting ≥ 10 years; AML 5 years; raw CIC ≤ 30 days; credit-dossier periods **legal to confirm** (Circular 04/2025 appendix) |

---

# 12. DEFINITION OF DONE & EFFORT

- **DoD** applies to all 123 stories [Skill17 §5]:
  - all ACs pass;
  - unit and integration tests pass;
  - peer review, plus a credit-risk reviewer for policy or model code;
  - consent-purpose test on every CCP read;
  - reason code and decision-log entry on every decision path;
  - no hard-coded policy values;
  - Skill 20 error states handled;
  - WCAG 2.1 AA;
  - analytics events firing;
  - ASVS level met;
  - feature flag with champion fallback;
  - docs and model card updated.
- **Effort (estimated):**
  - v1 Must: **≈ 293–458 person-weeks**, including the platform foundation and the hub shell;
  - Should: 68–102 person-weeks;
  - 9 spikes (SP-1 to SP-9) narrow the range [Skill14 §7][Skill16].
- **Build order:** PF-01 → EP-7 → EP-0 → EP-3 + EP-8 → EP-2 → EP-4 → EP-1, EP-5 → EP-6.
- **Sprint count:** an HLB parameter (team velocity is unknown).

---

# 13. RISKS, ASSUMPTIONS & CLOSURE RECORD

### 13.1 Top 10 critical items [Skill21 §5]
1. **AI Law conformity pathway and timing.** A new system must comply before deployment (RK-01, Critical).
2. **Hosting model** under data-localisation rules (RK-02).
3. **Core-banking real-time booking** (RK-12; spike SP-4).
4. **CASA metadata richness**, which decides how many bank-data signals are real in v1 (RK-03; spike SP-1).
5. **Legal confirmation of marketing-consent scope** and the self-view rule (RK-07).
6. **Cold-start corridor size and loss budget** (RK-04).
7. **Web-checkout eKYC hand-off** (RK-11; spike SP-3).
8. **HLB policy parameter values** (IS-06).
9. **Merchant distribution**, starting with the Sổ Bán Hàng channel (RK-15).
10. **Over-indebtedness guardrails** in operation (RK-08).

**All 24 risks, 28 assumptions, 6 issues and 14 dependencies**, with owners and gates: [Skill21].

### 13.2 Assumption Closure Certification (copied from Skill 22)
```
Files swept: 27
Decisions raised to user: 14 (grouped)
Decisions resolved: 14 (user, 2026-09-30: accept all defaults)
Items resolved by research instead of assumption: 11
Items explicitly accepted as open risk (not blocking): ≈15 legal items (RK-23, explicitly accepted by the user)
  + ~35 unknowable-before-launch items with validation methods in risk-register.md
Remaining unresolved items: 0  ✅
```

---

# 14. PARKING LOT

| ID | Feature | Status | Why | Revisit when |
|---|---|---|---|---|
| PL-01 / PL-02 | Telco / e-wallet / platform data; Decree 94 sandbox application | Later | Sandbox applicability and PDPL vendor provenance unresolved | Legal confirmation + sandbox entry |
| PL-03 | Merchant self-service portal | Later (user) | Hypothesis-level pain; 0% promotions configured by HLB staff in v1 | Merchant scale |
| PL-04 | Assisted eKYC fallback | Later (user) | v1 minimum: 3 attempts → refer | HLB eKYC failure data |
| PL-05 | Rich "path-to-yes" decline coaching | Later (user) | v1 minimum: reason codes | Post-pilot |
| PL-08 | Financial-literacy nudges | Later (user) | Hypothesis-level evidence | Post-pilot |
| PL-09 | Marketing frequency caps beyond consent and suitability | Later (user) | Partly covered by consent, holdout and suppression | Complaint data (G-6) |
| — | Uplift model, platform-partner pre-selection, Open API, package comparison, ready-limit dashboard, refer-a-merchant, passport, policy console | **v1 Should** (not parked) | Conditions in Skill 08 | — |
| PL-10 to PL-13 | Broadcast campaigns; WTP pricing; cash-for-sign-up; contact/gallery harvesting | **Rejected permanently** | Brief R-4; fairness; responsible lending; privacy | — |

*PL-06 (flexible repayment) and PL-07 (collection conduct) were **promoted to v1** in Skill 22.*

---

# 15. SIGN-OFF & CHANGE LOG

### 15.1 Sign-off checklist

| Stakeholder | Role | Sign-off date | Comments |
|---|---|---|---|
| | Product (Head of PoP Lending) | | |
| | Credit Risk (CRO) | | Policy parameters (§5.3) |
| | Model Risk | | Champion/challenger, fairness, AI Law file |
| | Legal & Compliance / DPO | | RK-23 legal items; consent scope |
| | Engineering Lead / IT (core banking) | | Spikes SP-1 to SP-9 |
| | Digital (HLB Connect) / Design | | Web-to-app hand-off; hub |

### 15.2 Open questions log
None blocking. See §13.2. Legal items are tracked as RK-23.

### 15.3 Change log

| Date | Change |
|---|---|
| 2026-09-28 | Discover phase (Skills 01–05, literature review) |
| 2026-09-29 | Problem research and personas; preliminary solution options |
| 2026-09-30 | User specified the CreditPulse direction. Define, Design and Specify completed (Skills 06–21). Skill 22 closure (14 decisions accepted). PRD v1.0 assembled with rubric-first emphasis (AI track) |

---

# 16. SOURCE INDEX

**Case:** `Business Challenge 2026 _ Ideation Round Case _ AI in Finance and Banking.pdf` → [`_requirements.md`](../_requirements.md).

**Full per-skill source indexes** (every URL used):
- Discover:
  - [01](../01-foundation/01-product-strategy.md), [02](../02-discover/02-market-research.md), [03](../02-discover/03-user-research.md), [04](../02-discover/04-problem-research.md), [05](../02-discover/05-persona-research.md)
  - [literature review](../02-discover/literature-review.md)
- Define: [06](../03-define/06-problem-statement.md) to [11](../03-define/11-prioritisation.md)
- Design:
  - [12](../04-design/12-solution-proposal.md), [13](../04-design/13-solution-design.md), [14](../04-design/14-technical-feasibility.md)
  - [research notes](../_research-notes-07-13.md) (80 sources)
- Specify: [15](../05-specify/15-user-journey-map.md) to [21](../05-specify/21-risk-register.md)
- Closure: [22](../06-closure/22-assumption-closure.md)

**Key sources relied on in this PRD:**

| Key | Source | URL |
|---|---|---|
| Skill02 S9 / S11 | Home Credit Vietnam POS loans; FE Credit phone loans (pricing example) | see `02-market-research.md` Source Index |
| Skill01 S1 | Gartner, "58% of Finance Functions Using AI in 2024" | https://www.gartner.com/en/newsroom/press-releases/2024-09-11-gartner-survey-shows-58-percent-of-finance-functions-use-ai-in-2024 |
| Skill03 S36 | DNSE / VietnamFinance: ACB 10 days → 5–10 min, ~60% auto-approved | https://www.dnse.com.vn/senses/tin-tuc/duyet-ho-so-10-phut-thay-vi-10-ngay-ai-cuop-viec-lam-cua-nhan-vien-ngan-hang-35125165 |
| Skill03 S18 | Decision Lab × Home Credit: 31% / 33% document reluctance (n=1,200) | https://www.decisionlab.co/blog/new-report-highlights-need-for-more-accessible-credit-and-better-financial-literacy |
| R8 | HLB Malaysia "GRAB It Now" T&C (2021) | https://www.hlb.com.my/content/dam/hlb/my/images/campaigns/2021/grab-pl-03/hlb-grab-it-now-tnc-en.pdf |
| R4 / R5 | Grab 20-F FY2024; Grab financial-inclusion page | https://www.sec.gov/Archives/edgar/data/1855612/000185561225000013/ck0001855612-20241231.htm ; https://www.grab.com/sg/sustainability/partners/financial-inclusion |
| R16 | CFPB Upstart No-Action-Letter results | https://www.consumerfinancemonitor.com/2019/08/08/cfpb-gives-boost-to-use-of-alternative-data-and-machine-learning/ |
| R49 | Agarwal et al., "Do Banks Pass Through Credit Expansions…" (QJE) | https://www.nber.org/system/files/working_papers/w21567/w21567.pdf |
| R50 | Aydin, AER 2022: randomised credit-line increases | https://www.aeaweb.org/doi/10.1257/aer.20191178 |
| R57 | Keil, Berg, Burg & Puri, JFE 2025: BNPL +~20% merchant sales | https://nber.org/system/files/working_papers/w33152/w33152.pdf |
| R61 | Bryan, Karlan & Zinman: referral enforcement effect | https://www.nber.org/papers/w17883 |
| R64 | Battaglia, Gulesci & Madestam, REStud: repayment flexibility lowers default | https://povertyactionlab.org/node/3713031 |
| R66 / R67 | HLB VN × Sổ Bán Hàng; HLB VN eKYC ~300 s → ~80 s | https://www.hlbank.com.vn/en/about-us/news-and-updates/hlb-partners-with-so-ban-hang.html ; https://aapnews.aap.com.au/news/cision20260428AE45287 |
| R70 / R71 | CIC 55.2M records (>70% of adults); CIC holds no deposit data; Sep 2025 incident | https://sbv.gov.vn/en/w/cic-tong-ket-hoat-dong-2025 ; https://en.baochinhphu.vn/viet-nams-central-bank-confirms-credit-data-incident-at-credit-information-center-111250912192742851.htm |
| R73 / R74 | Circular 64/2024 (Open API); Decree 94/2025 (sandbox) | https://english.luatvietnam.vn/tai-chinh/circular-64-2024-tt-nhnn-deployment-of-open-application-programming-interfaces-in-the-banking-sector-387124-d1.html ; https://www.dfdl.com/insights/legal-and-tax-updates/vietnam-new-decree-no-94-2025-nd-cp-on-regulatory-sandbox-in-the-banking-sector/ |
| R77 | Techcombank: approval and disbursement within 3 minutes | https://techcombank.com/thong-tin/blog/app-vay-tien-nhanh |
| R80 / L1 | Berg, Burg, Gombović & Puri (RFS 2020): digital footprints | https://nber.org/papers/w24551 |
| L9 | Di Maggio, Ratnadiwakara & Carmichael: data vs algorithm | see `literature-review.md` |
| Skill14 T17 / Skill18 N6 | AI Law: grace period only for systems operating before 15 Aug 2026 | see `14-technical-feasibility.md` / `18-nfr.md` |
| Skill19 P2 | Vietnam Briefing, Decree 356 (finance-sector logs, scoring disclosure) | https://www.vietnam-briefing.com/news/vietnam-personal-data-protection-regulation-decree-356.html/ |
| Skill20 E1 / E2 | Circular 50/2024 Art. 17(3) (no SMS links); VNBA Decision 15/QĐ-HHNH (effective 31 Mar 2026) | https://thuvienphapluat.vn/phap-luat-doanh-nghiep/bai-viet/ngan-hang-khong-gui-tin-nhan-sms-thu-dien-tu-co-chua-link-cho-khach-hang-tu-01-01-2025-9845.html ; https://luatvietnam.vn/tin-van-ban-moi/da-co-bo-quy-tac-ung-xu-trong-hoat-dong-thu-hoi-no-trong-he-thong-ngan-hang-186-108663-article.html |

**User decisions (Skill 22, 2026-09-30):** D-01 to D-14, all accepted at recommended defaults. PRD emphasis on the competition rubric (AI track) and research-backed strategic direction (user instruction).

---

# APPENDIX: PRD Quality Rubric check (Skill 23)

| Check | Result |
|---|---|
| An engineer can start building from the functional spec without a clarifying question | ✅ **Via §8 plus the normative story file** `17-user-stories.md` (123 stories, ~545 ACs). *Deviation noted:* by user instruction (2026-09-30), the PRD body emphasises the rubric and strategy, and §8 indexes the stories rather than reproducing every AC inline |
| Every story has ≥ 2 Given/When/Then ACs | ✅ (Skill 17; addendum US-231 to US-236 included) |
| Every entity has fields, relationships and PII flags | ✅ (Skill 19 §1, §4; summarised in §9.3) |
| Every external dependency is named with its use and fallback | ✅ (§9.4; Skill 20 §4) |
| No TBD sections | ✅. Unknowns are named HLB parameters (§5.3) or logged risks (RK-23) |
| Every risk has a mitigation and owner; every dependency has a risk level | ✅ (§13; Skill 21) |
| Every section names its source skill; every feature traces to a journey stage | ✅ (§4.4, §8) |
| Every market/competitive claim has a source; Source Index complete | ✅ (§16 plus per-skill indexes) |
| Skill 22 certification shows 0 unresolved | ✅ (§13.2) |
| v1/Later explicit; Parking Lot complete; "NOT in v1" documented | ✅ (§0.4, §14) |
| **Rubric coverage (user emphasis)** | ✅ R1 §2 · R2 §3 · R3 §4 · R4 §5 · R5 §6; scorecard in §0.5 |
