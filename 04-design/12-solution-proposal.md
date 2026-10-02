# Solution Proposal
**Product:** HLB Point-of-Purchase Credit Decisioning — **HLB CreditPulse** | **Skill:** 12 | **Status:** Done (2026-09-30), **Depth: Abbreviated**
**Depends on:** prioritisation.md, success-metrics.md, v1-product-scoping.md, problem-solution-fit.md | **Feeds into:** solution-design.md

> **Why this skill is abbreviated.** The pipeline allows Skill 12 to be shortened to a single Recommended Option when the elicitation already describes a specific solution mechanism, not just a problem. On 2026-09-30 the user specified that mechanism: a central AI credit-profile engine whose output every stage of a six-stage point-of-purchase (PoP) journey uses (`_context.md`, SD-1 to SD-7). This file therefore records the **user's approach as the Recommended Option**, a short rationale, and the top 3 assumptions. It adds one extra section, the rubric-driven "why AI beats the baseline" test for each component, because the brief requires AI value to be proven, not asserted [Brief pp.10–11].
>
> **Supersedes** the preliminary version of this file (2026-09-29), which recommended "Lean Triage" (a rules-based flow). Those options are not discarded; §3 shows how they are absorbed into the chosen design.
>
> Citations: `[R#]` = `_research-notes-07-13.md`; `[N#]` = new sources for this skill; others as elsewhere. Rubric tags are written `[Rubric R#]`.

---

## Pre-filled Context
- **Problem statement (Skill 06, chosen HMW):** How might we give Vietnamese shoppers a fair, right-sized credit offer at the moment they want to buy, based on what HLB and its partners already know about them, and keep improving that offer as they repay? This includes shoppers without a bureau file.
- **Core bottleneck (Skill 04):** HLB runs a single, undifferentiated, manual, document-and-bureau underwriting workflow for every application.
- **Fit verdict (Skill 07):** GO, conditional on C1–C6:
  - C1: consent recorded per purpose
  - C2: offers, never automatic limit increases, with affordability and CIC checks at every draw
  - C3: human refer band, reason codes and drift monitoring
  - C4: targeting by uplift and suitability, with a holdout group
  - C5: referral rewards paid only after repayment
  - C6: data sources phased in as each becomes legally ready
- **v1 boundary (Skill 08):**
  - Must-haves: E-01, 02, 05–09; AW-01, 02, 04; CO-01, 02; AP-01–07; PR-01–04; RT-01, 02; AD-01; OP-01; MX-01; GV-01.
  - Proposed pain-point features not yet assessed by the user are parked (PL-03 to PL-09).
- **Metrics (Skill 10):** North Star = Good In-Moment Loans (GIML), supported by OKRs O1–O4 and guardrails G-1 to G-8.

---

## Recommended Option

```
Chosen: HLB CreditPulse — a central, hybrid AI credit-profile engine whose single output, the
  Customer Credit Profile (CCP), is consumed by features at every stage of the point-of-purchase
  journey (user-specified approach, 2026-09-30).

Core approach (2–3 sentences):
  CreditPulse ingests consented bank-held data (HLB CASA/salary inflows, existing HLB loans and
  cards, HLB Connect usage, Sổ Bán Hàng merchant ledger), the CIC bureau file, application and
  identity data, and — in later phases — consented cross-bank (Open API) and third-party data,
  and continuously maintains a six-field CCP per customer: Risk Grade (A–E), Affordability
  Headroom (max safe monthly instalment, VND), Evidence Confidence (bureau / bank-observed /
  consented-partner / thin), Propensity & Need, Identity & Application Integrity, and Decision
  Tier (pre-approved / instant approve / refer / decline-with-reason). Six stage-level feature
  sets read the CCP: Awareness (target never-used, low-risk, high-potential customers),
  Consideration (personalised packages), Application & Instant Decision (in-session decision +
  eKYC + refer band), Purchase & Repayment (continuous re-scoring, early warning, consented
  graduation), Retention (suitable-product recommendations, anti-stacking) and Advocacy
  (repayment-conditioned invites, refer-a-merchant, good-payer passport).

Rationale:
  1. It is the user's chosen mechanism, and Skill 07 found the concept has scaled precedents:
     Grab and GrabFin [R4][R5], Nubank [R1][R2], Kredivo [R13], KBank [R26], SPayLater [R9],
     plus an HLB Group internal precedent, HLB Malaysia × Grab pre-selection (2021) [R8]. Every
     documented failure (Ant, Klarna, Upstart, Paytm, Kenya digital credit, the Apple Card 0%
     case) traces to governance, funding, consent, disclosure or affordability failures. Those
     are addressed by binding conditions C1–C6, not by rejecting the engine [Skill07 §3–4].
  2. One profile reused across all six stages turns one data investment into value at every
     stage. It replaces a per-application cost (380,000 VND manual review [Brief p.9]) with a
     per-customer profile that is kept up to date. This is the direct structural answer to the
     core bottleneck (one undifferentiated workflow [Skill04 §4]). `[Rubric R1][Rubric R2]`
  3. HLB's deposit funding (sector cost of funds ~4.8% vs Home Credit ~7.1% [Skill02 F4]) and
     its proprietary CASA and salary-inflow data, which the CIC does not hold [R71], are
     advantages that finance companies and wallet pay-later providers lack. The engine is how
     HLB puts those advantages to work at the checkout.
  4. The hybrid design keeps a rules-and-scorecard champion running alongside every ML
     challenger. The AI's incremental value can therefore be measured per segment (KR 2.1)
     rather than claimed. This answers the Skill 04 §5 non-AI test inside the solution.
     `[Rubric R2]`

What we explicitly rejected, and why:
  - A pure rules "Lean Triage" as the whole product. This was the preliminary recommendation of
    2026-09-29. It is absorbed rather than rejected: it becomes the champion inside CreditPulse's
    decision layer (E-06/E-07). On its own it cannot deliver the user's lifecycle requirements
    (Awareness targeting, continuous re-scoring, Retention recommendations), where AI has its
    strongest evidenced edge [R49][R50][R53][L62].
  - A full AI platform with third-party data from day one. This was preliminary Option 3. Its
    telco, e-wallet and platform feeds are parked as Phase 3 (PL-01) until the applicability of
    the Decree 94 sandbox and vendor compliance with the personal-data law (PDPL) are resolved
    [R74][R76]; OQ-02-14.
  - Willingness-to-pay pricing, broadcast campaigns, cash-for-sign-up referrals and harvesting
    of phone contacts or galleries. All are rejected permanently (PL-10 to PL-13) [R56][R61][R34];
    [Brief p.6].

Key assumptions to validate immediately (top 3):
  1. Consent uptake (A2): enough existing and new customers consent to scoring, and separately
     to marketing, for the Awareness and Retention stages to have a population to act on.
     Evidence is mixed: 31–33% of customers resist providing documents, 49.5% name data misuse as
     their top worry, and consent rates are unmeasured [Skill03 S18, S42]; OQ-03-04.
  2. Predictive power in Vietnam (A1): HLB CASA/salary inflows, Sổ Bán Hàng sales data and
     checkout-native signals predict repayment for thin-file segments well enough to grade them.
     Strong foreign evidence exists [L1][L4][L9][R16]; no Vietnam-specific study was found
     (OQ-LR-01). Validate by back-testing, then a randomised champion/challenger pilot.
  3. Lawful marketing use of financial data (A10): a separate, explicit marketing-consent purpose
     under the PDPL is sufficient for Awareness and Retention targeting on bank data
     [Skill02 RF-7][R46]. Needs legal review.
```

---

## 2. Component-level "why AI beats the baseline" test `[Rubric R2]`

The rubric requires: "If proposing AI: Demonstrate how AI creates superior value and why it is operationally viable" [Brief p.11]. Skill 04 §5 showed that not every component needs ML. The table below assigns each CCP field and stage feature to the **least complex method that does the job**, and states the AI case only where the evidence supports one.

| CCP field / feature | Method | Baseline it beats | Why AI adds value here (or why it doesn't) | Evidence |
|---|---|---|---|---|
| **Risk Grade** (A–E) | **Hybrid**: logistic scorecard champion plus gradient-boosted challenger, with reason codes | Manual officer review against CIC only [Brief p.5] | The ML advantage over scorecards is **largest for thin files** and shrinks as credit history lengthens. Most of the lift comes from new data. The challenger only overrides the champion where it proves better on a randomised split. | [L4][L9][L18][L19]; Upstart NAL +27% approvals, −16% APR [R16] |
| **Affordability Headroom** (VND/month) | **Rules plus a statistical income estimate**: headroom = estimated stable monthly income − existing obligations (CIC + HLB) − living-cost buffer, then × a policy DSR cap | Payslip-based income proof, which 31–33% of applicants can't or won't provide [Skill03 S18] | AI helps estimate **stable income from irregular inflows** (gig, MSME). The cap itself stays a transparent rule, because affordability is a legal and ethical boundary, not something to optimise. | Pain #10 [Skill03]; HLB VN's existing product uses a max DSR of 60% [R68] |
| **Evidence Confidence** | **Rules** (which sources were present and consented) | None; not visible today | Not an AI task. It is metadata that drives tiering and graduation. | SD-4 |
| **Propensity & Need** | **ML**: uplift model, trained after holdout data exists; suitability filter from launch | Broadcast or untargeted offers [Brief p.6] | This is where the AI case is **strongest**. Propensity is not incrementality: marginal propensity to borrow is 59% for FICO <660 vs ~0 for >740, so naive targeting finds the riskiest customers. Uplift modelling targets the customers an offer actually helps. | [R49][R53][R55][L62] |
| **Identity & Application Integrity** | **Rules plus ML anomaly detection** on top of mandatory chip-CCCD/VNeID biometric eKYC | Paper documents, which are forgeable (455 fake IDs across 607 applications) [Skill03 S31] | Biometric eKYC is regulatory infrastructure, not AI. ML adds anomaly and velocity detection for patterns rules miss. | [R67]; [Skill02 RF-4]; SBV: fraud incidents down ~50% after biometrics became mandatory [L52] |
| **Decision Tier** | **Rules**: a policy layer over the fields above | Single manual path for every case [Skill04 §4] | Not AI. Tiers are HLB policy, explainable by construction, and the **human refer band** meets the AI Law's human-oversight requirement. | [Skill02 RF-8]; ACB auto-approves ~60% on pre-set criteria [Skill03 S36] |
| **Continuous re-scoring** (Purchase & Repayment) | **ML**: behavioural score, survival or time-to-default modelling, refreshed on each repayment event | No refresh after booking (not described in the brief) | Behavioural data after the first loan is the richest signal a thin-file customer will produce. Nubank uses survival modelling and risk ranking for limit decisions across ~122M customers. | [N1][N2]; [R50] |
| **Graduation step-ups** | **Rules plus ML**: step-ups sized on risk-adjusted marginal profit, always **offered** with explicit consent | None | Limit increases drive instalment borrowing for years (11¢ then 28¢ per extra dollar), so sizing must be risk-aware. The consent rule follows the strictest benchmark found (India's RBI). | [R50][R49][R30] |
| **Retention recommendations** | **ML**: propensity × affordability headroom × partner catalogue, marketing consent only | Untargeted cross-sell, the source of Pain #9 resentment [Skill03] | Next-product-to-buy models have increased buying likelihood up to 3× in one bank (consultancy source, Medium). The affordability headroom caps what can be recommended. | [R51] |
| **Early-warning** | **ML**: probability of a missed payment in the next cycle | No proactive contact; collection happens after delinquency | Allows supportive outreach before default. The evidence on flexible repayment shows lower default [R64], although the flexible-schedule feature itself (PL-06) is parked pending your assessment. | [R64] |
| **Referral (Advocacy)** | **Rules**: reward is released only after the referee's on-time instalments; "referred" is never a risk feature | None | Not AI. Evidence shows a repayment **enforcement** effect, not a **screening** effect. | [R61] |

**Summary for the one-page submission:** CreditPulse uses AI in **five** places where the evidence shows it beats rules:
1. thin-file risk precision
2. income estimation from irregular inflows
3. uplift targeting
4. behavioural re-scoring
5. fraud anomaly detection

It deliberately keeps **rules** for affordability caps, decision tiers and referral rewards, where transparency matters more than optimisation. This split is the answer to rubric R2's "why AI, why viable". `[Rubric R2]`

---

## 3. How the preliminary options were absorbed

| Preliminary option (2026-09-29) | Where it lives now |
|---|---|
| Option 1, Lean Triage (rules, tiered) | The **champion** in E-06 and the Decision Tier policy in E-07. Also the fallback whenever an ML challenger underperforms or drifts (G-8). |
| Option 2, Hybrid scorecard (champion/challenger ML) | **Adopted** as E-06, the risk-grade method. |
| Option 3, Full-stack platform (third-party data, uplift targeting, advanced fraud) | **Split.** Uplift targeting and ML fraud detection are in scope (AW-03 Should; integrity score). Third-party telco, e-wallet and platform data is parked as Phase 3 (PL-01). |

---

## Open Items Raised by This Skill

| # | Item | Carry to |
|---|---|---|
| OQ-12-01 | The name "HLB CreditPulse" is a working name proposed by Claude. The user may rename it. | Skill 22 (cosmetic) |
| OQ-12-02 | The Affordability Headroom living-cost buffer and DSR cap are HLB policy parameters. The only public anchor is HLB VN's existing unsecured loan, with a max DSR of 60% [R68]. That product targets salaried customers, not thin-file ones. | Skill 13 / 22 `[DECISION REQUIRED]` |
| OQ-12-03 | The preliminary-version open items OQ-12-01 to 03 (dated 2026-09-29) are superseded. Skills 06–11 have now run, and RICE scoring is in Skill 11. | Closed |

---

## Source Index

| # | Source | URL | Used for |
|---|---|---|---|
| N1 | Building Nubank, "How Nubank models risk for smarter, scalable credit limit increases" | https://building.nu.com/how-nubank-models-risk-for-smarter-scalable-credit-limit-increases/ | Survival modelling and risk ranking for limit decisions; continuous monitoring; ~122M customers |
| N2 | Building Nubank, "How Nubank uses causality, machine learning and Python to support credit limit increase decisions" | https://building.nu.com/how-nubank-uses-causality-machine-learning-and-python-to-support-credit-limit-increase-decisions/ | Predictive + causal analysis + prioritisation; continuous monitoring |
| — | `_research-notes-07-13.md` [R#] | local | Precedents, failure modes, lifecycle evidence |
| — | Upstream Skills 01–11, `literature-review.md` [L#], `_requirements.md` [Brief] | local | All other claims |
