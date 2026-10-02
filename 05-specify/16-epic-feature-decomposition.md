# Epic & Feature Decomposition
**Product:** HLB Point-of-Purchase Credit Decisioning — **HLB CreditPulse** | **Skill:** 16 | **Status:** Done (2026-09-30)
**Depends on:** user-journey-map.md (Skill 15), v1-product-scoping.md (Skill 08), prioritisation.md (Skill 11), solution-design.md (Skill 13) | **Feeds into:** user-stories.md, nfr.md

> **Traceability rule.**
> - Every epic = one journey stage, or one cross-cutting enabler, from Skill 15 §5.
> - Every feature keeps its **Skill 08 code** (E-, AW-, CO-, AP-, PR-, RT-, AD-, OP-, MX-, GV-). It therefore traces back through Skill 11 (RICE/Kano) and Skill 13 (design) with no renumbering.
>
> **Labels.**
> - `[v1]` = Skill 08 Must.
> - `[v1-Should]` = Skill 08 Should (ships in v1 if time allows).
> - `[Later]` = Parking Lot.
> - `[Later — pending user]` = Claude-proposed pain-point features awaiting the user's assessment (PL-03 to PL-09).
>
> **Complexity** uses Skill 11's judgment-based effort bands: S≈4, M≈8, L≈16, XL≈32 person-weeks. Skill 14 (Technical Feasibility) is running in parallel and will re-estimate them. Any material difference will be reconciled into this file and flagged (OQ-16-01).
>
> **Priority Score** = Skill 11 RICE.
>
> **User Impact / Biz Value** (H/M/L) are derived from Skill 11's Impact anchor:
> - User Impact is scored against the user-facing pain.
> - Biz Value is scored against the Skill 04 bottleneck and brief requirements R-1 to R-4.
>
> Rubric tags: `[Rubric R#]`.

---

## Full Epic & Feature Hierarchy

```
EPIC 0: CreditPulse Engine & Customer Credit Profile  (cross-cutting; all stages)
Goal: Every existing HLB customer and every PoP applicant has an up-to-date, consent-gated,
      explainable six-field Customer Credit Profile (CCP) that any stage service can read.
Success Signal: 100% of applications and pre-approval candidates carry a current CCP version;
      challenger-vs-champion lift measurable per segment (KR 2.1); PSI drift < 0.10 (G-8).
v1 Scope: Yes

  Feature 0.1 [E-02]: Phase-1 data ingestion
  Description: Batch + event ingestion of HLB CASA/salary inflows, HLB loans/cards, HLB Connect
    usage, Sổ Bán Hàng ledger (merchant consent), CIC pulls, application/eKYC data and
    checkout-native signals into an online/offline feature store (signals S1–S15, Skill 13 §1.2).
  Label: [v1] | Complexity: XL (Skill 14: 24–40 pw) | User Impact: M | Biz Value: H | RICE 7.50 (re-scored)

  Feature 0.2 [E-05]: Customer Credit Profile generator & CCP API
  Description: Computes and versions the six CCP fields (F1 Risk Grade, F2 Affordability
    Headroom, F3 Evidence Confidence, F4 Propensity & Need, F5 Integrity, F6 Decision Tier) and
    serves them through a purpose-checked read API.
  Label: [v1] | Complexity: L | User Impact: H | Biz Value: H | RICE 15.00

  Feature 0.3 [E-06]: Hybrid risk scoring (scorecard champion + GBM challenger)
  Description: Per-segment logistic scorecard as champion; gradient-boosted challenger on a
    randomised split; stable-income estimator for F2; integrity anomaly model for F5.
  Label: [v1] | Complexity: XL | User Impact: M | Biz Value: H | RICE 5.00
  Note: Skill 11 dissent — RICE alone would ship the scorecard in v1 and the challenger in
    Tier 2; kept v1 by user decision SD-3 (logged for Skill 22).
    Skill 14: challenger training for THIN tiers waits for randomised-corridor labels (B-04, U-05).

  Feature 0.4 [E-07]: Decision-tier policy layer + risk-tier rate grid
  Description: Deterministic rules over F1–F5 → F6 tier (pre-approved / instant approve / refer /
    decline), limit and package constraints (Skill 13 §1.1 tier policy); published rate grid.
  Label: [v1] | Complexity: M | User Impact: H | Biz Value: H | RICE 30.00

  Feature 0.5 [E-08]: Profile refresh loop
  Description: Event-driven (repayment, salary inflow, new CIC obligation, consent change) and
    monthly batch refresh of the CCP; versioned history.
  Label: [v1] | Complexity: M | User Impact: M | Biz Value: M | RICE 6.25

  Feature 0.8 [PF-01]: Platform foundation (new, from Skill 14 §7)
  Description: In-country environments, container platform, CI/CD, observability, Circular 09/2020 security-level
    classification and risk assessment; portable components only (U-06).
  Label: [v1] | Complexity: L (16–24 pw) | User Impact: L | Biz Value: H

  Feature 0.6 [E-03]: Phase-2 Open API cross-bank data (Circular 64)
  Label: [v1-Should] | Complexity: L | Condition: HLB Circular 64 readiness (full compliance due 1 Mar 2027 [R73]) | RICE 2.22

  Feature 0.7 [E-04]: Phase-3 telco / e-wallet / platform-income data
  Label: [Later] | Reason deferred: Decree 94 sandbox applicability and PDPL vendor provenance unresolved (PL-01)


EPIC 1: Pre-approval Targeting  (Journey stage: Awareness)
Goal: Existing HLB customers who have never used PoP credit, are low-risk and can afford it
      learn — only when relevant — that they can pay over time, with a pre-approved limit.
Success Signal: statistically significant incremental take-up vs random holdout (KR 3.1) with
      marketing complaint rate not above baseline (G-6).
v1 Scope: Yes (uplift model = Should)

  Feature 1.1 [AW-01]: Never-used / low-risk / high-potential target-list generator
  Description: Nightly selection of Cohort A customers with no PoP history, F1 ∈ {A,B},
    F2 ≥ minimum package instalment, F3 ≠ THIN, marketing consent.
  Label: [v1] | Complexity: M | User Impact: M | Biz Value: H | RICE 7.00

  Feature 1.2 [AW-02]: Suitability filter + random holdout
  Description: Excludes early-warning, stacking, recent-hardship and no-MK-consent customers;
    holds out a random share for incrementality measurement.
  Label: [v1] | Complexity: S | User Impact: M | Biz Value: H | RICE 11.20

  Feature 1.3 [AW-04]: Pre-approved limit surfaced in HLB Connect
  Description: In-app offer card (MK consent) and "My limit" service display on self-initiated
    hub visit (DD-11); partner-app display where a partner agreement exists.
  Label: [v1] | Complexity: M | User Impact: H | Biz Value: H | RICE 11.20

  Feature 1.4 [AW-03]: Uplift model
  Label: [v1-Should] | Complexity: L | Condition: ≥ 1 campaign cycle of holdout data | RICE 1.75

  Feature 1.5 [AW-05]: Platform-partner pre-selection
  Label: [v1-Should] | Complexity: L | Condition: a Vietnam platform data-sharing agreement exists (D-08-2) | RICE 0.56


EPIC 2: Personalised Packages & Transparent Cost  (Journey stage: Consideration)
Goal: The customer sees 2–4 packages that fit their affordability headroom and knows the exact
      total cost before accepting.
Success Signal: package acceptance rate tracked (KR 3.2); 0 affordability breaches (G-4);
      100% of acceptances preceded by all-in cost display.
v1 Scope: Yes

  Feature 2.1 [CO-01]: Personalised package builder
  Description: Builds packages from F1 (rate tier), F2 (max instalment), F6 (limit), tenors
    {3,6,9,12,18,24} [Brief p.9], down-payment options, merchant 0% eligibility; never above F2.
  Label: [v1] | Complexity: M | User Impact: H | Biz Value: H | RICE 12.50

  Feature 2.2 [CO-02]: All-in cost & schedule display
  Description: Monthly instalment, payment dates, total payable, all-in APR, fees, late-payment
    consequences; no bundled insurance.
  Label: [v1] | Complexity: S | User Impact: H | Biz Value: H | RICE 25.00

  Feature 2.3 [CO-03]: Side-by-side package comparison
  Label: [v1-Should] | Complexity: S | Condition: after 2.1 stable | RICE 3.75


EPIC 3: In-Session Decision & Identity  (Journey stage: Application & Instant Decision) — CRITICAL PATH
Goal: The applicant receives an approve / refer / decline decision within the checkout session,
      verified by chip-ID without documents; uncertain cases reach a human quickly.
Success Signal: in-session decision rate ≥ 70% (KR 1.1); p50 ≤ 3 min, p90 ≤ 10 min (KR 1.2);
      refer SLA ≥ 90% within 2 business hours (KR 1.4); confirmed fraud not above baseline (G-2).
v1 Scope: Yes

  Feature 3.1 [AP-01]: In-session decision
  Description: Checkout decision service: request CCP (Lane A) or build one (Lane B), apply
    policy, return tier + packages + reason codes within the session.
  Label: [v1] | Complexity: L | User Impact: H | Biz Value: H | RICE 18.75 (Value rank #1)

  Feature 3.2 [AP-02]: Chip-CCCD / VNeID biometric eKYC (reuse HLB capability)
  Description: NFC chip read + liveness + face match (new-to-bank); face match against CCCD on
    file (existing customers); ~80 s today [R67]. Includes the web-to-app hand-off, because browsers
    cannot read the CCCD chip [Skill14 T24, U-01].
  Label: [v1] | Complexity: M (Skill 14: 8–12 pw) | User Impact: H | Biz Value: H | RICE 25.00 (re-scored)

  Feature 3.3 [AP-03]: Application-integrity checks → F5 integrity score
  Description: Device, velocity, identity-data consistency, CIC identity match, anomaly model.
  Label: [v1] | Complexity: M | User Impact: M | Biz Value: H | RICE 20.00

  Feature 3.4 [AP-04]: Refer-band handling with counter-offer and SLA
  Description: Immediate affordable counter-offer; otherwise case to officer workbench, merchant
    hold, customer notified within SLA.
  Label: [v1] | Complexity: M | User Impact: H | Biz Value: H | RICE 12.90

  Feature 3.5 [AP-05]: Starter limit (low-and-grow) for new-to-bank thin files
  Label: [v1] | Complexity: S | User Impact: H | Biz Value: H | RICE 26.40

  Feature 3.6 [AP-06]: CASA-first onboarding at approval
  Description: Offer HLB account opening at approval; disbursement/repayment account defaults to it.
  Label: [v1] | Complexity: S | User Impact: M | Biz Value: H | RICE 8.80

  Feature 3.7 [AP-07]: CIC-unavailable fallback rules
  Label: [v1] | Complexity: S | User Impact: M | Biz Value: M | RICE 6.25

  Feature 3.8 [AP-08]: eKYC failure fallback flows
  Label: [Later — pending user] | Reason deferred: Claude-proposed pain #5 (PL-04); v1 minimum = 3 retries → refer

  Feature 3.9 [AP-09]: Rich decline explanation / path-to-yes
  Label: [Later — pending user] | Reason deferred: Claude-proposed pain #6 (PL-05); v1 minimum = reason codes (Feature 7.2)


EPIC 4: Continuous Reassessment & Graduation  (Journey stage: Purchase & Repayment)
Goal: Every repayment refines the customer's profile; good payers are offered better terms with
      explicit consent; struggling payers are spotted early and supported.
Success Signal: MOB3 30+ within ceiling (G-1); early-warning cure rate (KR 3.3); graduation
      offers accepted without stacking increase (G-3).
v1 Scope: Yes

  Feature 4.1 [PR-01]: Behavioural re-scoring on repayment events
  Label: [v1] | Complexity: M | User Impact: M | Biz Value: H | RICE 12.50

  Feature 4.2 [PR-02]: Early-warning → supportive outreach + suppression
  Description: v1 = rule triggers (missed/partial instalment, new CIC obligation, inflow drop) → reminder +
    "talk to us"; suppress marketing/graduation; pause pre-approval. ML predictor once PoP delinquency labels exist (U-04).
  Label: [v1] | Complexity: M (Skill 14, rules-first) | User Impact: H | Biz Value: M | RICE 5.50 (re-scored)
  Note: Skill 11 dissent (RICE Tier 2); kept v1 for responsible lending (R-2).

  Feature 4.3 [PR-03]: Graduation ladder (offered, explicitly consented step-ups)
  Label: [v1] | Complexity: M | User Impact: H | Biz Value: H | RICE 20.00

  Feature 4.4 [PR-04]: CIC reporting of PoP loans
  Label: [v1] | Complexity: S | User Impact: M | Biz Value: H | RICE 25.00

  Feature 4.5 [PR-05]: Payout-aligned weekly repayment option (GIG, MSME) — PROMOTED (User decision, Skill 22, 2026-09-30)
  Description: At package selection, eligible GIG/MSME customers can choose weekly instalments due the day after
    their usual payout/settlement day (detected from S4/S5); same total cost disclosed via CO-02. Evidence: repayment
    flexibility lowered default in an RCT [R64]; Grab daily earnings deduction [R6].
  Label: [v1] | Complexity: M | User Impact: H | Biz Value: M | RICE 5.90 (narrowed scope, Skill 11)

  Feature 4.6 [PR-06]: Collection-conduct standards — PROMOTED (User decision, Skill 22, 2026-09-30)
  Description: Every reminder, outreach and collection contact follows the VNBA Code of Conduct (Decision 15/QĐ-HHNH,
    effective 31 Mar 2026): no third-party/reference contact, contact only within ew.contact_hours, frequency caps,
    approved templates, complaint channel.
  Label: [v1] | Complexity: S | User Impact: H | Biz Value: H | RICE 20.00

  Feature 4.7 [PR-07]: Financial-literacy nudges
  Label: [Later — pending user] | Reason deferred: PL-08


EPIC 5: Suitable Repeat Offers & Anti-Stacking  (Journey stage: Retention)
Goal: Good payers see a few relevant, affordable repeat-purchase suggestions at partner
      merchants; customers accumulating debt elsewhere are protected from re-offers.
Success Signal: good-payer repeat rate (KR 3.4) rising without stacking rising (G-3).
v1 Scope: Yes (dashboard = Should)

  Feature 5.1 [RT-01]: Product / merchant-category recommendations
  Description: F4 categories × F2 headroom × partner catalogue; MK consent only; ≤ 3 per week.
    v1 = rules-based category suggestion; recommender model later (Skill 11 mitigation; Skill 14 U-04).
  Label: [v1] | Complexity: M (Skill 14, rules-first) | User Impact: M | Biz Value: M | RICE 7.50 (re-scored)
  Note: Skill 11 dissent (RICE Tier 2); kept v1 as a user-suggested pain point.

  Feature 5.2 [RT-02]: Anti-stacking gate
  Label: [v1] | Complexity: S | User Impact: H | Biz Value: H | RICE 40.00

  Feature 5.3 [RT-03]: "Ready limit" dashboard
  Label: [v1-Should] | Complexity: S | Condition: with Feature 4.3 | RICE 6.25

  Feature 5.4 [RT-04]: Marketing contact-frequency caps
  Label: [Later — pending user] | Reason deferred: PL-09


EPIC 6: Responsible Referral & Merchant Flywheel  (Journey stage: Advocacy)
Goal: Loyal good payers can invite others and suggest shops without exposing anyone to debt
      pressure or reference harassment; rewards follow repayment, not borrowing.
Success Signal: referred cohort MOB3 30+ ≤ non-referred (KR 3.5); 100% of rewards released only
      after K on-time instalments.
v1 Scope: Partial

  Feature 6.1 [AD-01]: Repayment-conditioned invite
  Label: [v1] | Complexity: M | User Impact: M | Biz Value: M | RICE 12.50

  Feature 6.2 [AD-02]: Refer-a-merchant via Sổ Bán Hàng
  Label: [v1-Should] | Complexity: M | Condition: Sổ Bán Hàng partnership terms allow it | RICE 0.91

  Feature 6.3 [AD-03]: Good-payer credit passport (debt-gated)
  Label: [v1-Should] | Complexity: L | Condition: total-debt affordability gate (FM-7) | RICE 0.83


EPIC 7: Consent, Governance & Human Oversight  (cross-cutting)
Goal: Every use of customer data is consented per purpose; every decision is explainable and
      auditable; humans oversee uncertain cases; models are monitored for drift and fairness —
      meeting PDPL and the AI Law (conformity before deployment (1 Mar 2027 at the latest for a new system; RF-8 corrected 2026-09-30)).
Success Signal: KR 4.1 (100% reason codes, 100% consent-matched reads); KR 4.2 (AI Law
      documentation complete); KR 4.3 fairness monitoring live.
v1 Scope: Yes

  Feature 7.1 [E-01]: Per-purpose consent ledger (SC / MK / RF)
  Label: [v1] | Complexity: M | User Impact: H | Biz Value: H | RICE 10.00

  Feature 7.2 [E-09]: Reason-code generator
  Label: [v1] | Complexity: M (Skill 14) | User Impact: M | Biz Value: H | RICE 10.00 (re-scored)

  Feature 7.3 [OP-01]: Officer refer-band workbench
  Label: [v1] | Complexity: M | User Impact: H (operator) | Biz Value: H | RICE 19.35

  Feature 7.4 [GV-01]: Model governance (champion/challenger, drift, fairness, audit log, AI Law docs)
  Label: [v1] | Complexity: XL (Skill 14: 20–32 pw; AI Law conformity prep may be pre-launch, U-07) | User Impact: L | Biz Value: H | RICE 3.13 (re-scored)

  Feature 7.5 [OP-02]: Policy / rule console
  Label: [v1-Should] | Complexity: M | Condition: may start as version-controlled config | RICE 3.13

  Feature 7.6 [GV-02]: Decree 94 sandbox application
  Label: [Later] | Reason deferred: only needed for Feature 0.7 (PL-02)


EPIC 8: Merchant & Partner Integration  (cross-cutting channel)
Goal: A shopper can choose "pay over time with HLB" inside any partner's online checkout, merchant
      app or physical store, and the merchant receives a confirmed decision in-session.
Success Signal: share of partner checkouts offering HLB PoP; merchant confirmation latency within
      the KR 1.2 envelope.
v1 Scope: Yes

  Feature 8.1 [MX-01]: Checkout integration — API widget, hosted page, QR deep link
  Label: [v1] | Complexity: XL (Skill 14: 24–32 pw) | User Impact: H | Biz Value: H | RICE 7.50 (re-scored)

  Feature 8.3 [HUB-01]: HLB Connect "Pay Later" hub shell (new, from Skill 14 §7)
  Description: Navigation plus My plans, Why this decision, and Privacy & consent screens (Skill 13 IA §3).
  Label: [v1] | Complexity: M (6–10 pw) | User Impact: H | Biz Value: M

  Feature 8.2 [MX-02]: Merchant portal (0% campaigns, settlement)
  Label: [Later — pending user] | Reason deferred: PL-03; see OQ-16-02 on 0% campaign set-up in v1
```

---

## Feature Scoring Table

| Epic | Feature | Code | v1/Later | Complexity | User Impact | Biz Value | Priority Score (RICE) |
|---|---|---|---|---|---|---|---|
| EP-0 | Phase-1 data ingestion | E-02 | v1 | XL | M | H | 7.50 |
| EP-0 | Platform foundation | PF-01 | v1 | L | L | H | — (no RICE; enabler) |
| EP-0 | CCP generator & API | E-05 | v1 | L | H | H | 15.00 |
| EP-0 | Hybrid risk scoring | E-06 | v1 | XL | M | H | 5.00 |
| EP-0 | Decision-tier policy + rate grid | E-07 | v1 | M | H | H | 30.00 |
| EP-0 | Profile refresh loop | E-08 | v1 | M | M | M | 6.25 |
| EP-0 | Open API cross-bank data | E-03 | v1-Should | L | M | M | 2.22 |
| EP-0 | Telco / e-wallet / platform data | E-04 | Later | XL | — | — | 0.83 |
| EP-1 | Target-list generator | AW-01 | v1 | M | M | H | 7.00 |
| EP-1 | Suitability filter + holdout | AW-02 | v1 | S | M | H | 11.20 |
| EP-1 | Pre-approved limit in HLB Connect | AW-04 | v1 | M | H | H | 11.20 |
| EP-1 | Uplift model | AW-03 | v1-Should | L | M | M | 1.75 |
| EP-1 | Platform-partner pre-selection | AW-05 | v1-Should | L | M | M | 0.56 |
| EP-2 | Package builder | CO-01 | v1 | M | H | H | 12.50 |
| EP-2 | All-in cost & schedule | CO-02 | v1 | S | H | H | 25.00 |
| EP-2 | Package comparison | CO-03 | v1-Should | S | L | L | 3.75 |
| EP-3 | In-session decision | AP-01 | v1 | L | H | H | 18.75 |
| EP-3 | Chip-CCCD/VNeID eKYC (+ web-to-app hand-off) | AP-02 | v1 | M | H | H | 25.00 |
| EP-3 | Integrity checks | AP-03 | v1 | M | M | H | 20.00 |
| EP-3 | Refer band + counter-offer | AP-04 | v1 | M | H | H | 12.90 |
| EP-3 | Starter limit | AP-05 | v1 | S | H | H | 26.40 |
| EP-3 | CASA-first onboarding | AP-06 | v1 | S | M | H | 8.80 |
| EP-3 | CIC fallback | AP-07 | v1 | S | M | M | 6.25 |
| EP-3 | eKYC fallback flows | AP-08 | Later — pending user | M | — | — | 6.25 |
| EP-3 | Path-to-yes explanation | AP-09 | Later — pending user | S | — | — | 6.25 |
| EP-4 | Behavioural re-scoring | PR-01 | v1 | M | M | H | 12.50 |
| EP-4 | Early warning + outreach (rules-first) | PR-02 | v1 | M | H | M | 5.50 |
| EP-4 | Graduation ladder | PR-03 | v1 | M | H | H | 20.00 |
| EP-4 | CIC reporting | PR-04 | v1 | S | M | H | 25.00 |
| EP-4 | Payout-aligned weekly repayment (GIG/MSME) | PR-05 | v1 | M | H | M | 5.90 |
| EP-4 | Collection-conduct standards | PR-06 | v1 | S | H | H | 20.00 |
| EP-4 | Literacy nudges | PR-07 | Later — pending user | S | — | — | 0.54 |
| EP-5 | Recommendations (rules-first) | RT-01 | v1 | M | M | M | 7.50 |
| EP-5 | Anti-stacking gate | RT-02 | v1 | S | H | H | 40.00 |
| EP-5 | Ready-limit dashboard | RT-03 | v1-Should | S | L | L | 6.25 |
| EP-5 | Frequency caps | RT-04 | Later — pending user | S | — | — | 5.60 |
| EP-6 | Repayment-conditioned invite | AD-01 | v1 | M | M | M | 12.50 |
| EP-6 | Refer-a-merchant | AD-02 | v1-Should | M | L | M | 0.91 |
| EP-6 | Good-payer passport | AD-03 | v1-Should | L | M | L | 0.83 |
| EP-7 | Consent ledger | E-01 | v1 | M | H | H | 10.00 |
| EP-7 | Reason codes | E-09 | v1 | M | M | H | 10.00 |
| EP-7 | Officer workbench | OP-01 | v1 | M | H | H | 19.35 |
| EP-7 | Model governance | GV-01 | v1 | XL | L | H | 3.13 |
| EP-7 | Policy console | OP-02 | v1-Should | M | L | M | 3.13 |
| EP-7 | Sandbox application | GV-02 | Later | M | — | — | 0.83 |
| EP-8 | Checkout integration | MX-01 | v1 | XL | H | H | 7.50 |
| EP-8 | Pay Later hub shell | HUB-01 | v1 | M | H | M | — (no RICE; enabler) |
| EP-8 | Merchant portal | MX-02 | Later — pending user | L | — | — | 0.94 |

---

## Consolidated Parking Lot

| ID | Feature | Epic | Status | Revisit condition |
|---|---|---|---|---|
| PL-01 | E-04 Telco / e-wallet / platform data | EP-0 | Later | Decree 94 applicability + vendor PDPL due diligence resolved |
| PL-02 | GV-02 Sandbox application | EP-7 | Later | HLB decides to pursue PL-01 |
| PL-03 | MX-02 Merchant portal | EP-8 | Later — pending user | User assesses proposed pain #16 |
| PL-04 | AP-08 eKYC fallback flows | EP-3 | Later — pending user | User assessment; HLB eKYC failure data |
| PL-05 | AP-09 Path-to-yes explanation | EP-3 | Later — pending user | User assessment |
| PL-06 | PR-05 | EP-4 | **Promoted to v1** | — |
| PL-07 | PR-06 | EP-4 | **Promoted to v1** | — |
| PL-08 | PR-07 Literacy nudges | EP-4 | Later — pending user | User assessment |
| PL-09 | RT-04 Frequency caps | EP-5 | Later — pending user | User assessment |
| PL-10–13 | Broadcast campaigns, WTP pricing, cash-for-sign-up, contact harvesting | — | Rejected permanently | — |

The v1-Should items (E-03, AW-03, AW-05, CO-03, RT-03, AD-02, AD-03, OP-02) are **in v1 scope if time allows**. They are not parked, and each has its Skill 08 condition.

---

## Epic Summary Cards

Effort uses the **Skill 14 engineering estimates** (person-weeks, pw, estimated). They replace Skill 11's judgment bands; this reconciliation resolves OQ-16-01. The figures exclude HLB policy-setting, legal work, independent model validation, and fees for an external conformity assessment [Skill14 §7].

| Epic | Goal (short) | v1 features | Success signal | v1 effort (pw, est.) | Should effort (pw, est.) |
|---|---|---|---|---|---|
| **EP-0** Engine & CCP | Current, consent-gated six-field CCP for everyone | E-02, E-05, E-06, E-07, E-08, PF-01 | KR 2.1 lift; G-8 drift | 100–156 | 16–24 |
| **EP-1** Pre-approval Targeting | Suitable, incremental invitations to never-used customers | AW-01, AW-02, AW-04 | KR 3.1; G-6 | 17–25 | 24–32 |
| **EP-2** Packages & Transparent Cost | Affordable packages, total cost first | CO-01, CO-02 | KR 3.2; G-4 | 9–15 | 3–5 |
| **EP-3** In-Session Decision & Identity ★ | Decision within the checkout session, no documents | AP-01 to AP-07 | KR 1.1, 1.2, 1.4; G-2 | 47–71 | 0 |
| **EP-4** Reassessment & Graduation | Repayment refines profile; consented step-ups; early support; humane collections | PR-01 to PR-06 | G-1; KR 3.3; G-3 | 33–54 (+PR-05 6–12, PR-06 4–4 est.) | 0 |
| **EP-5** Repeat Offers & Anti-Stacking | Relevant, affordable repeat offers; stacking protection | RT-01, RT-02 | KR 3.4; G-3 | 9–15 | 3–5 |
| **EP-6** Referral & Merchant Flywheel | Rewards follow repayment | AD-01 | KR 3.5 | 6–10 | 16–26 |
| **EP-7** Consent, Governance & Oversight | Lawful, explainable, overseen decisions | E-01, E-09, OP-01, GV-01 | KR 4.1–4.3 | 42–70 | 6–10 |
| **EP-8** Merchant & Partner Integration | HLB PoP inside every partner checkout + app hub | MX-01, HUB-01 | Merchant coverage; latency | 30–42 | 0 |
| **Total** | | **33 v1 features** (29 + PF-01 + HUB-01 + PR-05 + PR-06) | | **≈ 293–458 pw** | **68–102 pw** |

★ = critical path (Skill 15 §3). Build-order dependency: PF-01 → EP-7 (E-01 consent) → EP-0 → EP-3 + EP-8 → EP-2 → EP-4 → EP-1, EP-5 → EP-6.

**Skill 14 reconciliation:**
- **Bands raised:** E-02, MX-01 and GV-01 go to XL; AP-02 and E-09 go to M.
- **Bands lowered:** PR-02 and RT-01 go to M because they launch rules-first (U-04). This removes most of the Skill 11 RICE dissent.
- **Unchanged:** AP-01 stays L.
- **RICE and tiers:** RICE is re-computed in the scoring table. Tiers are unchanged; Skill 08's MoSCoW stands.

---

## Open Items Raised by This Skill

| # | Item | Carry to |
|---|---|---|
| OQ-16-01 | ~~Complexity bands pending Skill 14~~. **Resolved 2026-09-30:** reconciled with Skill 14 §7 (see Epic Summary Cards). | Closed |
| OQ-16-02 | 0% merchant-subsidised promotions are a competitor must-have [Skill02 §5], but the merchant portal is parked (PL-03). In v1, 0% eligibility would have to be configured per merchant by HLB staff, through OP-02 config or a bilateral set-up (inherits OQ-11-07). The user should confirm. | Skill 22 |

---

## Source Index
No new external sources. Every feature, score and label traces to `08-v1-product-scoping.md` (codes, MoSCoW, parking lot), `11-prioritisation.md` (RICE, Kano, effort bands, dissents), `13-solution-design.md` (feature behaviour), `15-user-journey-map.md` (epic seeds) and `10-success-metrics.md` (success signals). External citations inside feature notes ([R#], [Brief]) resolve to `_research-notes-07-13.md` and `_requirements.md`.
