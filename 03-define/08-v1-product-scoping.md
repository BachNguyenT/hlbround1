# v1 Product Scoping
**Product:** HLB Point-of-Purchase Credit Decisioning (working name) | **Skill:** 08 | **Status:** Done (2026-09-30)
**Depends on:** problem-solution-fit.md | **Feeds into:** ALL downstream skills

> ⚠️ This is the BOUNDARY document. Once complete, all downstream skills respect this scope.
> Features labelled [Later] are PARKED — not deleted, not designed.

> Citations: `[R#]` = `_research-notes-07-13.md`; `[Brief p.N]`, `[Skill0N]`, `[L#]` as elsewhere; `[S-08-#]` = new sources for this skill (Source Index at end). Rubric tags `[R1]`–`[R4]` refer to the competition rubric.

---

## Pre-filled Context
- **Product type:** Other. A bank-internal credit decisioning capability, distributed B2B2C through merchant and partner checkouts. The user specified the solution: one central AI credit-profile engine that every stage of a six-stage PoP journey draws on (`_context.md` SD-1 to SD-7).
- **Business constraints:**
  - Commercially viable PoP business line at a cost per decision proportional to ticket size, with risk quality, responsible lending and compliance maintained [Brief p.6].
  - AI must show incremental value over the baseline [Brief p.10].
  - The final competition output is a one-page 1920×1080 PDF [Brief p.11]. It is outside this pipeline.
- **Technical and regulatory constraints:**
  - PDPL: purpose-specific, explicit consent [Skill02 RF-7][R46].
  - AI Law: human oversight and conformity assessment for automated credit decisions [Skill02 RF-8]. **Qualified by Skill 14 (U-07):** the 1 Sep 2027 grace period appears to cover only systems already operating before 15 Aug 2026 [Skill14 T17]. A newly launched CreditPulse may need its conformity assessment before launch (OQ-14-02).
  - Biometric chip-CCCD/VNeID eKYC [Skill02 RF-4, RF-5].
  - Circular 29/2026 permits automated electronic appraisal and lender-set limits [Skill02 RF-2].
  - Open API (Circular 64) full compliance by 1 Mar 2027 [R73].
  - Decree 94 sandbox for credit scoring [R74].
- **Binding conditions from Skill 07 (C1–C6):**
  - C1: consent is separate for each purpose.
  - C2: limit increases are offered, never applied automatically, and every offer and draw is checked for affordability and the customer's CIC record.
  - C3: an officer "refer" band, reason codes and drift monitoring.
  - C4: offers are targeted by uplift and suitability, with a holdout group.
  - C5: referral rewards depend on the referred customer repaying on time.
  - C6: data sources are rolled out in phases as they become legally ready.
- **Pain-point scope rule (user instruction, 2026-09-30):**
  - v1 features are designed for the **user-suggested pain points** (Awareness targeting, Consideration packages, continuous reassessment, Retention recommendations, Advocacy).
  - The case-brief requirements R-1 to R-4 and the Skill 07 binding conditions are also **mandatory**, whether or not they appear on the user's list.
  - Claude's **additional proposed pain points** are parked (§6) until the user assesses them. None of them is in v1 yet.

---

## 1. Full Feature Universe

Brainstormed from Skills 01–07, the research notes, and the user's solution direction. Codes: **E** = engine core, **AW** = Awareness, **CO** = Consideration, **AP** = Application & Instant Decision, **PR** = Purchase & Repayment, **RT** = Retention, **AD** = Advocacy, **OP** = operator (credit officer), **MX** = merchant/partner, **GV** = governance.

| Code | Feature | Source of need |
|---|---|---|
| E-01 | Consent ledger: per-customer, per-purpose (scoring / marketing / referral), revocable | C1; PDPL [R46] |
| E-02 | Data ingestion, Phase 1 sources: HLB CASA/salary inflows, existing HLB loans/cards, CIC, HLB Connect usage, Sổ Bán Hàng ledger (merchant consent), application data, checkout-native signals | SD-7; C6; [R71][R66][L1] |
| E-03 | Data ingestion, Phase 2: Open API consented cross-bank transaction history | C6; Circular 64 [R73] |
| E-04 | Data ingestion, Phase 3: telco / e-wallet / platform-income third-party data | C6; Decree 94 [R74]; [Brief p.8] |
| E-05 | Customer Credit Profile (CCP) generator: 6 fields (risk grade, affordability headroom, evidence confidence, propensity/need, integrity score, decision tier) | SD-4 |
| E-06 | Hybrid scoring: rules/scorecard champion + ML challenger, per segment and evidence tier | SD-3; Skill 04 §5; [L9][L18][L19] |
| E-07 | Decision-tier policy layer: pre-approved / instant approve / refer / decline; risk-tier rate grid | SD-4; C3; [R56] |
| E-08 | Profile refresh loop: event-driven on repayment or new data, plus periodic batch | User: continuous reassessment |
| E-09 | Reason-code generator (explainability) for every tier outcome | C3; FM-10 [R40] |
| AW-01 | "Never-used, low-risk, high-potential" target-list generator for existing customers (on marketing consent) | User: Awareness; R-4 [Brief p.6] |
| AW-02 | Suitability filter + random holdout on every target list | C4; FM-5 [R49] |
| AW-03 | Uplift model: target customers whose take-up is *incremental* | C4; [R53][R55] |
| AW-04 | Pre-approved limit surfaced in HLB Connect and partner apps ("You can pay over time up to X") | User: Awareness; HLB × Grab precedent [R8]; KBank [R26] |
| AW-05 | Partner pre-selection (e.g. platform partner messages eligible workers) | HLB MY × Grab precedent [R8] |
| AW-06 | Mass/broadcast campaigns | — (rejected: contradicts R-4 "rather than broadcast" [Brief p.6]) |
| CO-01 | Personalised package builder: limit, tenor (3/6/9/12/18/24), down-payment, eligibility for merchant-subsidised 0%, rate from the published risk-tier grid | User: Consideration; [Brief p.9]; [R56] |
| CO-02 | All-in cost and repayment schedule shown before acceptance | Brief "fair and transparent cost" [Brief p.4]; FM-4 [R41] |
| CO-03 | Side-by-side package comparison at checkout | User: Consideration ("considers many lending options") |
| CO-04 | Willingness-to-pay (personalised) pricing | — (rejected: fairness risk [R56]) |
| AP-01 | In-session decision returning a decision tier at checkout | R-2 [Brief p.6]; core bottleneck [Skill04 §4] |
| AP-02 | Chip-CCCD/VNeID biometric eKYC (reuses HLB's existing eKYC) | R-3 [Brief p.6]; [R67]; RF-4 |
| AP-03 | Application-integrity checks (device, velocity, identity-data consistency, CIC match) → integrity score | R-3; FM-11 |
| AP-04 | Refer-band handling: provisional hold, officer decision within SLA, customer notified | C3; AI Law |
| AP-05 | Starter limit (low-and-grow) for new-to-bank thin-file applicants | R-1; Nubank low-and-grow [R1] |
| AP-06 | CASA-first onboarding offered at approval (disbursement into a new HLB account) | [Brief p.8] mitigation; SD-1 |
| AP-07 | CIC and bureau-unavailable fallback rules | FM-12 [R71] |
| AP-08 | eKYC failure fallback flows (retry guidance, assisted path) | Parked proposed pain #5 |
| AP-09 | Rich decline explanation and "path to yes" | Parked proposed pain #6 (minimum reason code is in E-09) |
| PR-01 | Behavioural re-scoring on every repayment event, plus a monthly batch | User: continuous reassessment |
| PR-02 | Early-warning signal (predicted missed payment) → proactive, non-coercive hardship outreach | HMW 3 "struggling payers protected early" [Skill06] |
| PR-03 | Graduation ladder: offered limit or term step-ups after N on-time instalments plus refreshed affordability, each with explicit consent | User: refine profile; C2; [R30][R49][R50] |
| PR-04 | CIC reporting of PoP loans (builds bureau history) | Legal obligation [Skill02 RF-11]; FM-3 |
| PR-05 | Cash-flow-aligned or flexible repayment schedules (daily/weekly deduction, skip-a-payment) | Parked proposed pain #10; [R64][R6] |
| PR-06 | Collection-conduct standards ("never contact relatives/references") | Parked proposed pain #8 |
| PR-07 | Financial-literacy nudges (late-payment consequences) | Parked proposed pain (B12) |
| RT-01 | Product / merchant-category recommendations: profile propensity × affordability headroom × partner merchant catalogue (on marketing consent) | User: Retention |
| RT-02 | Anti-stacking gate: CIC exposure check plus internal exposure cap before any re-offer | C2; FM-3 [R35][R36] |
| RT-03 | "Ready limit" dashboard in HLB Connect (current headroom, next graduation step) | User: Retention; supports PR-03 |
| RT-04 | Contact-frequency cap and quiet periods for marketing | Parked proposed pain #9 (partly served by AW-02 and E-01) |
| AD-01 | Repayment-conditioned invite: invitee gets an instant eligibility check; referrer rewarded only after the invitee's on-time instalments; no reference contacts | SD-6a; C5; [R61] |
| AD-02 | Refer-a-merchant: customer nominates a shop, which is onboarded via Sổ Bán Hàng as a PoP acceptance point (and potential MSME borrower) | SD-6b; [R66][R57] |
| AD-03 | "Good payer" credit passport: customer-controlled record of on-time PoP repayment, used for graduation at partner merchants and bridged to CIC history; gated on total-debt affordability | SD-6c; FM-7 [R62] |
| AD-04 | Referral leaderboards / cash-for-sign-up | — (rejected: rewards borrowing, not repayment [R61]) |
| OP-01 | Officer refer-band workbench: pre-assembled evidence, CCP fields, reason codes, override with justification | C3; OFFICER persona [Skill05]; Pain #12/#13 |
| OP-02 | Policy/rule management console (risk appetite, rate grid, cut-offs) | E-07; OQ-02-16 |
| MX-01 | Merchant checkout integration: API / hosted page / QR deep link into HLB Connect | "Within the shopping journey" [Brief p.4] |
| MX-02 | Merchant portal (0%-subsidy campaigns, settlement) | Parked proposed pain #16 |
| GV-01 | Model governance: champion–challenger, drift (PSI) monitoring, fairness monitoring by segment/gender/region, audit log, AI Law technical documentation | C3; FM-9; [L21][L23]; AI Law |
| GV-02 | Decree 94 sandbox application (for Phase 3 data) | C6; OQ-07-01 |
| X-01 | Phone contact/gallery harvesting | — (rejected permanently: FM-11 [R34]) |

---

## 2. v1 Boundary (MoSCoW)

### v1 entry cohorts (how SD-1's two entry paths are scoped)
- **Cohort A: existing HLB customers.** Profiled proactively from Phase-1 bank data.
  - This is chiefly salaried account-holders, whom the brief calls "most visible" [Brief p.7].
  - It also covers any GIG or MSME customer whose income already flows into an HLB account, and MSMEs using Sổ Bán Hàng.
  - Served by every stage, including pre-approval.
- **Cohort B: new-to-bank applicants at a partner checkout.** All five segments.
  - Profiled at application from CIC (where a file exists), eKYC, application data and consented checkout-native signals.
  - They receive a **starter limit** (low-and-grow) and CASA-first onboarding.
  - They move into Cohort A once observable.

### MUST HAVE (v1 blocks on these)

| Feature | Rationale | Evidence |
|---|---|---|
| **E-01** Consent ledger (per purpose) | Without it, the Awareness/Retention/Advocacy uses of the profile are unlawful (C1) | PDPL [R46]; [Skill02 RF-7] |
| **E-02** Phase-1 data ingestion | Legally ready, bank-owned data. CASA/salary inflows are an input CIC lacks | [R71]; C6 |
| **E-05** Customer Credit Profile (6 fields) | Central output every stage consumes (user requirement) | SD-4 |
| **E-06** Hybrid scoring (rules champion + ML challenger) | Satisfies "AI with incremental value" by making the value measurable | [Brief p.10]; Skill 04 §5; FM-13 |
| **E-07** Decision-tier policy layer + risk-tier rate grid | Turns scores into actions; risk-based (not WTP) pricing | [R56]; C3 |
| **E-08** Profile refresh loop | User: continuous reassessment | User direction |
| **E-09** Reason codes | Human oversight and explainability | C3; AI Law; FM-10 |
| **AW-01** Never-used / low-risk / high-potential target list | User-suggested Awareness pain point; brief R-4 | [Brief p.6] |
| **AW-02** Suitability filter + random holdout | Binding C4; stops risk-seeking targeting | [R49]; FM-5 |
| **AW-04** Pre-approved limit surfaced in HLB Connect | Delivers the Awareness offer through an owned channel | [R8][R26]; HLB Connect [R67] |
| **CO-01** Personalised package builder | User-suggested Consideration pain point | User; [Brief p.9] tenures |
| **CO-02** All-in cost & schedule before acceptance | Brief "fair and transparent cost"; FM-4 | [Brief p.4]; [R41] |
| **AP-01** In-session decision | Core bottleneck; R-2 | [Skill04 §4]; [Brief p.6] |
| **AP-02** Chip-CCCD/VNeID eKYC (existing HLB capability) | R-3; legally mandatory biometrics | [R67]; RF-4 |
| **AP-03** Application-integrity checks → integrity score | R-3 "application is genuine" | [Brief p.6] |
| **AP-04** Refer-band handling with SLA | AI Law human oversight; C3 | [Skill02 RF-8] |
| **AP-05** Starter limit (low-and-grow) for Cohort B | R-1 for thin-file new-to-bank applicants | [Brief p.6]; [R1] |
| **AP-06** CASA-first onboarding at approval | The brief's own mitigation; moves Cohort B into Cohort A | [Brief p.8] |
| **AP-07** CIC-unavailable fallback | FM-12 | [R71] |
| **PR-01** Behavioural re-scoring on repayment | User-suggested Purchase & Repayment pain point | User |
| **PR-02** Early-warning → proactive hardship outreach | "Protect struggling payers early" (HMW 3); responsible lending | [Skill06]; [Brief p.6] |
| **PR-03** Graduation ladder (offered, consented step-ups) | User: refine profile; C2 | [R30][R50] |
| **PR-04** CIC reporting | Legal obligation; anti-stacking visibility | [Skill02 RF-11] |
| **RT-01** Product/merchant-category recommendations (marketing consent) | User-suggested Retention pain point | User |
| **RT-02** Anti-stacking gate | C2; FM-3 | [R35][R36] |
| **AD-01** Repayment-conditioned invite | User-suggested Advocacy pain point; C5 | [R61]; SD-6a |
| **OP-01** Officer refer-band workbench | Human-oversight execution; OFFICER persona | [Skill05]; C3 |
| **MX-01** Merchant checkout integration (API / hosted page / QR) | Credit "within the shopping journey" | [Brief p.4] |
| **GV-01** Model governance (drift, fairness, audit, AI Law docs) | C3; FM-9; AI Law deadline before deployment (1 Mar 2027 at the latest for a new system; RF-8 corrected 2026-09-30) | [Skill02 RF-8]; [R45] |

### SHOULD HAVE (v1 includes if time allows)

| Feature | Rationale | Condition |
|---|---|---|
| **AW-03** Uplift model | Better than a suitability filter alone (C4); needs holdout data first | Build after ≥1 campaign cycle of holdout data from AW-02 |
| **AW-05** Partner pre-selection (platform partner) | HLB MY × Grab precedent [R8]; the best route to GIG visibility | Only if a Vietnam platform data-sharing agreement exists (not evidenced; see D-08-2) |
| **E-03** Open API cross-bank data | Lets SAL-N/GIG salaried elsewhere be assessed on inflows | HLB's own Circular 64 readiness; full compliance due 1 Mar 2027 [R73] |
| **CO-03** Side-by-side package comparison | User: "considers many lending options" | After CO-01 is stable |
| **AD-02** Refer-a-merchant via Sổ Bán Hàng | SD-6b; grows the merchant network, not debt | Sổ Bán Hàng partnership terms allow it (not public) |
| **AD-03** Good-payer credit passport | SD-6c; the Retention→Advocacy bridge | Gated on total-debt affordability (FM-7) |
| **RT-03** "Ready limit" dashboard | Makes graduation visible | After PR-03 |
| **OP-02** Policy/rule console | Lets HLB tune risk appetite without code changes | Can start as configuration files |

### WON'T HAVE v1 — Parking Lot

See §5 register. Summary:
- **E-04** Phase-3 third-party data (telco/e-wallet/platform feeds). Needs Decree 94 sandbox clarity.
- **GV-02** Sandbox application.
- **MX-02** merchant portal.
- **All user-unassessed proposed pain-point features:** AP-08, AP-09, PR-05, PR-06, PR-07, RT-04.
- **Rejected permanently:** AW-06 broadcast campaigns, CO-04 WTP pricing, AD-04 cash-for-sign-up, X-01 contact harvesting.

---

## 3. v1 Definition Statement

> **v1 of the HLB point-of-purchase credit engine does the following:**
> - builds one consented, explainable Customer Credit Profile for every existing HLB customer and every PoP applicant, including those without a bureau file;
> - uses that profile to invite the right existing customers, tailor their instalment package, decide within the checkout session, and keep refining limits as they repay.
>
> **For** HLB's point-of-purchase customers, with thin-file gig workers, online merchants and first-time borrowers as the priority groups.
>
> **So that** creditworthy shoppers get a fair, right-sized offer at the moment they want to buy, and HLB lends at a cost per decision proportional to the ticket, within its risk appetite.

---

## 4. Scope Decision Log

| Feature | Decision | Rationale | Evidence |
|---|---|---|---|
| AW-01/AW-04 existing-customer pre-approval | **Must** | Incumbent-bank pattern (KBank) and HLB Group's own precedent start with observable customers | [R26][R8] |
| AW-03 uplift model | **Should** (after holdout data) | An uplift model needs a randomised holdout to learn from; the suitability filter + holdout (AW-02) ships first | [R53][R55]; C4 |
| AW-06 broadcast campaigns | **Rejected** | Contradicts brief R-4 ("targeted and relevant rather than broadcast to all") | [Brief p.6] |
| CO-04 WTP pricing | **Rejected** | Personalised pricing by willingness to pay is a fairness and consumer-protection concern; risk-based pricing only | [R56] |
| CO-02 all-in cost display | **Must** (although it maps to a parked proposed pain point, #7) | The brief itself requires "fair and transparent cost" [Brief p.4], and it is an FM-4 countermeasure [R41] | Brief-mandated |
| AP-02/AP-03 identity & integrity | **Must** (although the user's list didn't name them) | Brief R-3 is mandatory | [Brief p.6] |
| AP-08/AP-09 eKYC fallback, rich decline explanation | **Parked** (pending user) | Proposed pain points #5/#6; the minimum legally needed (reason codes) is in E-09 | User instruction 2026-09-30 |
| PR-03 limit increases | **Must, as offers with explicit consent** | Never auto-applied (RBI DLG benchmark; Aydin: increases drive borrowing for years) | [R30][R50] |
| PR-05 flexible schedules | **Parked** (pending user) — **flag: strong evidence** | RCT evidence that flexibility *lowers* default [R64]; Grab daily deduction [R6]. Strongly recommended for the user's assessment | [R64][R6] |
| AD-01 repayment-conditioned invite | **Must** | Enforcement effect is evidenced; screening is not | [R61] |
| AD-04 cash-for-sign-up | **Rejected** | Rewards borrowing, not repayment; debt-pushing risk | [R61]; Pain #8 |
| AD-03 credit passport | **Should, gated** | Credit-builder RCT: harms borrowers with existing loans | [R62] |
| E-04 telco/e-wallet data | **Later** | Decree 94 applicability unresolved; PDPL data-trading ban; vendor due diligence pending | [R74][R76]; OQ-02-14 |
| E-06 ML challenger vs rules champion | **Must (hybrid)** | User SD-3; lets HLB *prove* incremental AI value per segment rather than assert it | [Brief p.10–11]; Skill 04 §5 |
| X-01 contact/gallery harvesting | **Rejected permanently** | OJK precedent; black-credit stigma | [R34][R37] |

---

## 5. Parking Lot Register

| ID | Feature | Reason Deferred | Revisit Condition | Deferred From | Status |
|---|---|---|---|---|---|
| PL-01 | E-04 Telco / e-wallet / platform third-party data (Phase 3) | Decree 94 sandbox applicability unresolved; PDPL data-trading ban; vendor provenance | Legal confirmation + sandbox entry (OQ-02-17, OQ-07-01) | Skill 08 | Open |
| PL-02 | GV-02 Decree 94 sandbox application | Depends on PL-01 decision | HLB decides to pursue Phase 3 | Skill 08 | Open |
| PL-03 | MX-02 Merchant portal | Proposed pain #16 (Hypothesis-level evidence) | User assessment of proposed pain points | Skill 08 | Open |
| PL-04 | AP-08 eKYC failure fallback flows | Proposed pain #5, awaiting user assessment | User assessment | Skill 08 | Open |
| PL-05 | AP-09 Rich decline explanation / path-to-yes | Proposed pain #6, awaiting user assessment (minimum reason codes stay in v1 via E-09) | User assessment | Skill 08 | Open |
| PL-06 | PR-05 Cash-flow-aligned / flexible repayment | **Promoted to v1 (narrowed): a weekly repayment option aligned to payout day, for GIG and MSME only** [R64][R6] | — | Skill 08 | **Promoted to v1** (User decision, Skill 22, 2026-09-30) |
| PL-07 | PR-06 Collection-conduct standards | **Promoted to v1** (VNBA Code of Conduct, Decision 15/QĐ-HHNH [Skill20 E2]) | — | Skill 08 | **Promoted to v1** (User decision, Skill 22, 2026-09-30) |
| PL-08 | PR-07 Financial-literacy nudges | Proposed (B12), awaiting user assessment | User assessment | Skill 08 | Open |
| PL-09 | RT-04 Marketing contact-frequency caps | Proposed pain #9, awaiting user assessment (partly covered by E-01, AW-02) | User assessment | Skill 08 | Open |
| PL-10 | AW-06 Broadcast campaigns | Contradicts brief R-4 | — | Skill 08 | Rejected permanently |
| PL-11 | CO-04 WTP-personalised pricing | Fairness/consumer-protection risk [R56] | — | Skill 08 | Rejected permanently |
| PL-12 | AD-04 Cash-for-sign-up referral | Rewards borrowing, not repayment | — | Skill 08 | Rejected permanently |
| PL-13 | X-01 Contact/gallery harvesting | FM-11 | — | Skill 08 | Rejected permanently |

**Revisit triggers**

| Trigger | Related IDs |
|---|---|
| User completes assessment of Claude's proposed pain points | PL-03 to PL-09 |
| Legal confirmation on Decree 94 applicability / vendor PDPL due diligence | PL-01, PL-02 |
| First campaign cycle's holdout data available | AW-03 (Should → build) |
| Circular 64 Open API readiness at HLB | E-03 (Should → build) |

---

## Decisions Required (carried to Skill 22; provisional defaults applied so work can continue)

| # | Decision | Provisional default applied | Why it's the user's call |
|---|---|---|---|
| D-08-1 | **Primary design persona.** Skill 05 recommended GIG (Anh Hùng) before the solution direction existed. Under SD-1 and C6, Phase-1 data best covers existing HLB account-holders (Cohort A). GIG workers are "low visibility unless linked via platform partners" [Brief p.7], and no Vietnam platform partnership is evidenced. | **GIG remains the primary design persona for the new-to-bank path (Cohort B)**, because R-1 names gig workers explicitly. **An existing HLB account-holder with no PoP history** (typically salaried-no-credit, SAL-N, 20.5%) is the **anchor for Awareness and Retention (Cohort A)**. Skill 15 maps both as two lanes. | Changes which persona the one-page submission leads with |
| D-08-2 | Pursue a Vietnam platform partnership (a Grab-style pre-selection, AW-05) in v1? | Should-have, conditional. Not assumed to exist | Commercial and partnership decision |
| D-08-3 | Include Open API cross-bank data (E-03) in v1? | Should-have, since HLB must comply with Circular 64 by 1 Mar 2027 anyway | Depends on HLB's API roadmap (unknown) |
| D-08-4 | Loan purpose for MSME: consumer purchase, business inventory, or both? (inherits OQ-03-10 / OQ-05-06) | **Both, but v1 treats MSME PoP loans as purchase finance at partner merchants.** Sổ Bán Hàng sales data is used as an income signal only | Changes the product and legal treatment |

---

## Source Index

| # | Source | URL | Used for |
|---|---|---|---|
| S-08-1 | Basecamp, *Shape Up* (Ryan Singer) — appetite and no-gos method | https://basecamp.com/shapeup | Scoping method (no-gos, minimum valuable scope) |
| — | `_research-notes-07-13.md` [R#] (80 entries) | local | Precedents, failure modes, lifecycle mechanics, Vietnam enablers |
| — | Upstream skills 01–07, `_requirements.md`, `literature-review.md` | local | All other claims |

## Skill 22 Resolutions (User decision, Skill 22, 2026-09-30)

| Item | Resolution |
|---|---|
| D-08-1 Primary persona | **Confirmed:** Anh Hùng (GIG) for the new-to-bank lane (Cohort B); an existing HLB account-holder without credit history (SAL-N) for the existing-customer lane (Cohort A) |
| D-08-2 Platform partnership (AW-05) | Stays Should-have, conditional. It is not assumed to exist |
| D-08-3 Open API (E-03) | Stays Should-have |
| D-08-4 MSME loan purpose | **Confirmed:** purchase finance at partner merchants only. Sổ Bán Hàng data is used as an income signal |
| Parked pain points | **PR-06 promoted to Must.** **PR-05 promoted to Must, narrowed** to a weekly payout-aligned schedule option for GIG and MSME. AP-08, AP-09, PR-07, RT-04 and MX-02 **remain parked** (user accepted defaults) |
| 0% promotions without a merchant portal (OQ-16-02) | Configured per merchant by HLB staff through versioned policy config (OP-02 / E-07) |
