# Risk Register (RAID Log)
**Product:** HLB Point-of-Purchase Credit Decisioning — **HLB CreditPulse** | **Skill:** 21 | **Status:** Done (2026-09-30)
**Depends on:** all prior skills (01–20) | **Feeds into:** assumption-closure.md (Skill 22), product-prd.md (Section 9)

> **How to read this file**
> - This file consolidates risks, assumptions, issues and dependencies from Skills 01–20. Each row cites its source skill, and the underlying evidence is in that skill's Source Index. No new facts are introduced.
> - **Owners are HLB roles.** The team is not known: Head of PoP Lending, CRO/Credit Risk, Model Risk (MR), DPO, CISO, Legal & Compliance, Head of Digital (HLB Connect), IT/Core Banking, Merchant Partnerships, Operations.
> - **Review dates are gates, not calendar dates.** No build timeline exists (OQ-05 in `_context.md`).
>   - **G0**: before build, spike or legal phase.
>   - **G1**: before pilot launch.
>   - **G2**: pilot MOB3 review.
>   - **G3**: before scale-up.
> - **Scoring.**
>   - Probability: High >70%, Med 30–70%, Low <30%.
>   - Impact: High = threatens v1, Med = degrades v1, Low = minor.
>   - Score: H/H = **Critical**; H/M or M/H = **High**; M/M = **Medium**; anything with L = **Low**.
> - Rubric tag: `[Rubric R4]` (Data & Assumptions). This log is the evidence for "explicitly state all assumptions".

---

## 1. Risk Register

| ID | Risk | Source Skill | Prob. | Impact | Score | Mitigation | Owner | Review |
|---|---|---|---|---|---|---|---|---|
| RK-01 | **AI Law conformity must be complete before launch.** The 1 Sep 2027 grace period appears to cover only systems operating before 15 Aug 2026, so a new CreditPulse may need assessment first. This puts GV-01 and an external assessment on the critical path | 14 (B-01), 18 (UC-18-01) | High | High | **Critical** | Start AI Law technical file and conformity pathway at G0. Hybrid design with a human refer band. Reason codes on 100% of decisions | Legal & Compliance; MR | G0 |
| RK-02 | **Hosting / data-localisation constraints** (Decree 53/2022, Circular 09/2020, Cybersecurity Law 116/2025, PDPL transfer rules) block region-bound managed services, or delay platform build | 14 (B-02), 18 | Med | High | **High** | Portable, in-country-hostable components (U-06). Decide the hosting model at G0 (OQ-14-01) | CISO; IT | G0 |
| RK-03 | **CASA transaction metadata** (counterparty IDs, merchant category codes) is too thin for signals S2, S4, S7 and S9, which weakens the "innovative bank-data" assessment | 13 (A-13-1–4), 14 (B-03) | Med | High | **High** | Data spike SP-1 at G0. Fallback to inflow-level signals (S1, S3, S8) | IT; MR | G0 |
| RK-04 | **Cold start.** No thin-file or PoP repayment history exists to train ML, so the challenger cannot be trained for THIN tiers | 14 (B-04), lit review (L20) | High | Med | **High** | Scorecard champion. Starter limits. A randomised approval corridor with a loss budget (U-05; OQ-14-07). Rules-first early warning and recommendations (U-04) | MR; CRO | G1 |
| RK-05 | **Alternative data predicts less well in Vietnam** than foreign evidence suggests, so thin-file approvals carry more loss | 06 (A1), lit review (OQ-LR-01), 07 | Med | High | **High** | Champion/challenger on a randomised split (KR 2.1). MOB3 30+ ceiling (G-1). Starter limits | MR; CRO | G2 |
| RK-06 | **Low consent uptake** for scoring, marketing or partner data, leaving profiles thin and Awareness/Retention with little reach | 03 (OQ-03-04), 06 (A2) | Med | High | **High** | Clear "share for a better offer" consent copy. A/B tests. Consent funnel metric | Head of Digital; DPO | G1 |
| RK-07 | **Marketing use of financial data (MK purpose) challenged under the PDPL.** Decree 356 requires scoring and profiling to be disclosed in consent notices | 06 (A10), 13 (OQ-13-01), 19 | Med | High | **High** | Separate MK consent. Service-display rule DD-11 pending legal confirmation. Consent notices disclose scoring and profiling [Skill19 P2] | DPO; Legal | G0 |
| RK-08 | **Over-indebtedness and loan stacking** among thin-file and first-time borrowers (documented harm pattern) | 03 (Pain #11), 07 (FM-3) | Med | High | **High** | Affordability headroom gate (G-4 = 0 breaches). CIC anti-stacking at every draw. Graduation offered, never auto-applied. Early warning | CRO | G2 |
| RK-09 | **Misleading 0% / instalment marketing** triggers enforcement or complaints (CFPB Apple/Goldman precedent) | 07 (FM-4) | Low | High | **Low** (monitor) | All-in cost before acceptance (CO-02). Published rate grid. Disclosure hash | Legal & Compliance | G1 |
| RK-10 | **Fraud and identity misuse** (forged documents, biometric harvesting) through the faster, document-free flow | 03 (Pain #14, S17), 04 (P3) | Med | Med | **Medium** | Chip-CCCD NFC + liveness. F5 integrity score. Velocity limits (≤ 3 checks/24 h). G-2 monitoring | Fraud team; CISO | G1 |
| RK-11 | **Web-checkout eKYC hand-off** (browsers can't read the chip) adds friction and abandonment for new-to-bank shoppers | 14 (B-06), 20 (UC-20-02) | High | Med | **High** | Spike SP-3 (app hand-off vs merchant SDK vs VNeID). Measure hand-off time in KR 1.2 | Head of Digital | G0 |
| RK-12 | **Core banking cannot book in real time**, which breaks the "order completes in-session" promise | 14 (B-07) | Med | High | **High** | Spike SP-4. Approve-and-guarantee option (BD-20-10). Hold ≤ 30 min then cancel | IT/Core Banking | G0 |
| RK-13 | **Refer band larger than planned (>30%)**, which erodes in-session rate and cost savings | 04 (OQ-04-02), 10 (A-10-2) | Med | Med | **Medium** | Back-test cut-offs. Counter-offers. KR 1.1 daily alert | CRO; Ops | G2 |
| RK-14 | **Automated cost per decision unknown**, so the unit economics may still fail at the smallest tickets | 04 (OQ-04-03), 09 (OQ-09-01) | Med | Med | **Medium** | Sensitivity tables (Skill 09 §3). Minimum ticket policy review | Finance | G1 |
| RK-15 | **Merchant distribution**: marketplaces steer to their own pay-later (0% seller fee on SPayLater) and big chains are captive (MWG–Cake) | 09 (OQ-09-11), 02 | Med | High | **High** | Start with Sổ Bán Hàng merchants and independent retailers. Refer-a-merchant (AD-02). Merchant value = BNPL +~20% sales [R57] | Merchant Partnerships | G1 |
| RK-16 | **Merchants won't hold goods during review**, so referred customers are lost | 13 (A-13-7), 20 (BD-20-05) | Med | Med | **Medium** | Counter-offer first. Three-choice screen (ERR-351) | Merchant Partnerships | G1 |
| RK-17 | **Model drift / regime shift** (COVID-type) mis-grades customers | 07 (FM-9), 10 (G-8) | Low | High | **Low** (monitor) | PSI alerts. Champion fallback. Drift-safe mode (BD-20-20) | MR | G3 |
| RK-18 | **Fairness disparity** by gender, age or region with no Vietnamese benchmark | lit review (OQ-LR-06), 10 (G-5) | Med | Med | **Medium** | Monthly fairness snapshots. 0.8 ratio alert. Protected attributes excluded from inputs | MR; Compliance | G2 |
| RK-19 | **CIC dependency**: outage (no public SLA) or data breach (Sep 2025 incident) | 14 (B-05), 13 (AP-07) | Low | Med | **Low** | Fallback rules. 30-day raw-payload limit. Post-recovery re-check (EC-10) | IT; DPO | G1 |
| RK-20 | **Capital treatment of unsecured PoP loans** tightens (India risk-weight precedent cut small-ticket BNPL ~50%) | 09 (OQ-09-09), 07 | Low | Med | **Low** | Monitor SBV. Pricing sensitivity | Finance; CRO | G3 |
| RK-21 | **Referral pushes debt onto friends**, or is abused | 07 (FM-6), 06 (A13) | Low | Med | **Low** | Reward only after the invitee's on-time instalments. "Referred" is never a risk feature. Abuse checks | Head of PoP Lending | G2 |
| RK-22 | **Collection conduct harms** (reference harassment is a documented Vietnamese harm) if parked PR-06 isn't adopted | 03 (Pain #8), 11 (dissent) | Med | Med | **Medium** | VNBA Code of Conduct (31 Mar 2026) applied to all messages (Skill 20). No reference contacts collected. PR-06 promotion is a user decision | Collections; Compliance | G1 |
| RK-23 | **Late-discovered legal detail** (Circular 04/2025 appendix retention, Decree 356 article-level duties, Circular 39/29 disclosure fields) forces rework | 18, 19, 20 | Med | Med | **Medium** | Legal review sprint at G0 (see Issues log) | Legal | G0 |
| RK-24 | **Effort overrun**: estimate is 283–442 person-weeks with a wide range | 14 §7, 16 | Med | Med | **Medium** | Phase Should items. Re-estimate after spikes SP-1 to SP-9 | Head of PoP Lending; IT | G0 |

---

## 2. Assumption Log

Consolidated. Rows with Confidence **Low** or **Medium** are Skill 22 candidates.

| ID | Assumption | Source Skill | Confidence | Validation Method | Timeline | Owner |
|---|---|---|---|---|---|---|
| AS-01 | Thin-file digital signals predict repayment in Vietnam (A1) | 06 | Medium | Back-test; randomised pilot | G2 | MR |
| AS-02 | Customers consent to scoring/marketing/partner data at useful rates (A2) | 06 | Low–Med | Consent funnel A/B | G1 | Head of Digital |
| AS-03 | In-moment = minutes; p50 ≤ 3 min, p90 ≤ 10 min (A3, A-10-3) | 06, 10 | High | Pilot timing | G1 | Head of Digital |
| AS-04 | Automation makes cost per decision proportional to ticket (A4) | 06 | Medium | Cost breakdown + refer-share back-test | G1 | Finance |
| AS-05 | HLB holds enough existing-customer data for Awareness targeting (A5) | 06 | Medium | Data audit (SP-1) | G0 | IT |
| AS-06 | Declines of visibly-earning applicants are a process gap, not deliberate risk appetite (A6, OQ-04-04) | 04, 06 | Medium | HLB credit policy confirmation | G0 | CRO |
| AS-07 | Targeted offers raise acceptance without raising harm (A7) | 06 | Medium | Uplift + guardrails | G2 | Head of PoP Lending |
| AS-08 | Merchants will embed an HLB offer (A8) | 06 | Medium | Merchant interviews / pilot sign-ups | G1 | Merchant Partnerships |
| AS-09 | AI Law human oversight met via refer band without losing speed (A9) | 06, 14 | Medium | Legal reading; pilot metrics | G0 | Legal |
| AS-10 | Marketing use of financial data lawful with a separate MK consent (A10) | 06 | Medium | Legal review | G0 | DPO |
| AS-11 | Repayment behaviour improves profile accuracy (A11) | 06 | Medium | Model lift by MOB cohort | G2 | MR |
| AS-12 | Repeat users welcome relevant recommendations (A12) | 06 | Low | Opt-in rate; complaint rate | G2 | Head of Digital |
| AS-13 | Repayment-conditioned referral doesn't encourage irresponsible borrowing (A13) | 06 | Low | Referred vs non-referred MOB3 | G2 | Head of PoP Lending |
| AS-14 | GIML ≥ 60% of booked loans; refer band ≤ 30% (A-10-1, A-10-2) | 10 | Medium | Pilot | G2 | Head of PoP Lending |
| AS-15 | Refer SLA ≥ 90% within 2 business hours (A-10-4) | 10 | Low | Ops pilot | G1 | Ops |
| AS-16 | Positive uplift within the first campaign cycle (A-10-5) | 10 | Low | Holdout test | G2 | Marketing |
| AS-17 | Fairness alert at a 0.8 approval-rate ratio (A-10-7) | 10 | Medium (as a trigger) | MR review | G1 | MR |
| AS-18 | CASA metadata supports S2, S4, S6, S7, S9 (A-13-1–4) | 13 | Low–Med | Spike SP-1 | G0 | IT |
| AS-19 | Pre-approval shown on self-initiated hub visit counts as service use (A-13-5) | 13 | Low | Legal | G0 | DPO |
| AS-20 | Merchants hold goods during refer (A-13-7) | 13 | Low | Merchant interviews | G1 | Merchant Partnerships |
| AS-21 | Core banking exposes booking/ledger APIs (A-13-10) | 13, 14 | Low–Med | Spike SP-4 | G0 | IT |
| AS-22 | One self-declared income figure with haircut/cap is acceptable for F2_thin (A-13-12) | 13 | Medium | Credit policy sign-off; pilot loss | G1 | CRO |
| AS-23 | Availability 99.9% for the decision path (A-18-11) | 18 | Medium | Circular 09 level assessment | G0 | CISO |
| AS-24 | CIC p95 ≤ 15 s with 15 s timeout (A-18-8) | 18 | Low | CIC SLA inquiry (SP-2) | G0 | IT |
| AS-25 | Raw CIC payload kept 30 days (A-18-1) | 18 | Medium | Legal / DPO | G0 | DPO |
| AS-26 | Error-handling timings A-20-1 to A-20-10 | 20 | Medium (design defaults) | Pilot | G1 | Head of Digital |
| AS-27 | Primary design persona: GIG for Lane B; SAL-N account-holder for Lane A (D-08-1) | 05, 08 | Medium | User decision (Skill 22) | now | User |
| AS-28 | MSME PoP loans are purchase finance at partner merchants (D-08-4) | 08 | Medium | User decision | now | User |

---

## 3. Issues Log (current problems, not future risks)

| ID | Issue | Impact now | Action |
|---|---|---|---|
| IS-01 | **No HLB operating data** in the brief: volumes, approval rates by segment, loss rates, pricing, cost of funds, cost breakdown | Targets, cost numbers and capacity figures can only be parameters or stated assumptions | User decision in Skill 22: how the submission states them |
| IS-02 | **Parked pain-point features (PL-03 to PL-09) await the user's assessment**; Skill 11 recommends promoting PR-06 and PR-05 | v1 scope not final for Repayment/Collections | Skill 22 decision |
| IS-03 | **Legal items unverified from primary sources**: Circular 04/2025 appendix retention periods; Decree 356 article-level duties; Circular 39/29 disclosure fields; Decision 2345 supersession; AI Law pathway; Art. 38(3) applicability to credit | Retention, disclosure and compliance details are marked `[DECISION REQUIRED — legal]` | Legal review sprint at G0; accepted as logged risk RK-23 unless the user decides otherwise |
| IS-04 | **Design gaps fixed during specification** (tier rule for CONSENTED-PARTNER; F2_thin; Retention holdout; stacking outcome) | Fixed in Skill 13; stories patched; defaults need user confirmation | Confirm in Skill 22 |
| IS-05 | **9 technical spikes (SP-1 to SP-9)** outstanding from Skill 14 | Effort range is wide (283–442 pw) | Run at G0 |
| IS-06 | **~28 named HLB policy parameters** have no values (thresholds, starter limit, DSR cap, holdout share, N, K, corridor size, etc.) | Stories are written against named parameters | Skill 22: accept as configurable, HLB-set at launch, or provide values |

---

## 4. Dependency Register

| Dependency | Type | Owner | Risk level | Mitigation |
|---|---|---|---|---|
| CIC (credit information enquiry and reporting) | Regulatory / API | SBV CIC ↔ HLB IT | Medium | Fallback rules; post-recovery re-check |
| C06 / VNeID / chip-CCCD verification (existing HLB integration) | API | Ministry of Public Security ↔ HLB Digital | Medium | Retry; refer; not counted as attempts |
| HLB core banking (CIF, CASA, booking, ledger) | Infrastructure | HLB IT | **High** | Spikes SP-1/SP-4; approve-and-guarantee option |
| HLB Connect app (hub, eKYC, notifications) | Infrastructure | HLB Digital | Medium | Existing award-winning channel [Brief p.3] |
| Sổ Bán Hàng partnership (ledger data, merchant onboarding) | Partner | Merchant Partnerships | Medium | Terms not public; daily feed with staleness downgrade |
| Partner merchants / checkouts | Partner | Merchant Partnerships | **High** | Three integration types; merchant value proposition |
| SMS / OTP gateways | Partner (processor) | HLB IT | Low | Secondary gateway; Smart OTP |
| Open API counterparties (Circular 64, Phase 2) | Regulatory / API | HLB IT | Medium | Asynchronous only; Should-have |
| AI Law conformity body / pathway | Regulatory | Legal | **High** | Start at G0 |
| PDPL / Decree 356 compliance (DPIA, consent, logs) | Regulatory | DPO | **High** | Consent ledger; processing logs; annual assessment |
| Decree 94 sandbox (Phase 3 data only) | Regulatory | Legal | Low (v1) | Parked (PL-01/PL-02) |
| Data platform / hosting in Vietnam | Infrastructure | CISO / IT | **High** | Portable components; decide at G0 |
| Credit-risk policy owners (parameter values) | Team | CRO | **High** | Parameter set with four-eyes approval |
| Model Risk function (validation, monitoring) | Team | MR | Medium | GV-01 |

---

## 5. Top 10 Critical Items (before v1 launch)

1. **RK-01 AI Law conformity pathway and timing.** Start the technical file and assessment at G0.
2. **RK-02 Hosting model decision** under data-localisation rules (OQ-14-01).
3. **RK-12 Core-banking real-time booking** spike (SP-4), plus the approve-and-guarantee decision.
4. **RK-03 CASA metadata spike** (SP-1). This decides how much of the "innovative bank data" catalogue is real in v1.
5. **RK-07 / AS-19 legal confirmation** of marketing-consent scope and the pre-approval display rule.
6. **RK-04 Cold-start plan.** Set the corridor size and loss budget (OQ-14-07) and the starter limit.
7. **RK-11 Web-checkout eKYC path** (SP-3).
8. **IS-06 Policy parameter values** (risk appetite, DSR cap, thresholds), set by the CRO.
9. **RK-15 Merchant distribution.** Secure first partner merchants (Sổ Bán Hàng channel).
10. **IS-02 Parked pain points**, especially PR-06 collection conduct (Kano Basic, RICE rank 7) and PR-05 flexible repayment (strongest RCT evidence).

---

## Source Index
No new external sources. Every row cites its source skill; evidence and URLs are in:
- `01-product-strategy.md`, `02-market-research.md`, `03-user-research.md`, `04-problem-research.md`, `05-persona-research.md`, `literature-review.md`
- `06`–`11` (Define)
- `12`–`14` (Design)
- `15`–`20` (Specify)
- `_research-notes-07-13.md`

## Skill 22 Resolutions (User decision, Skill 22, 2026-09-30)
- **RK-23 / IS-03:** the user **explicitly accepted** the ~15 unverified legal items as logged, non-blocking risks, closed by a legal review sprint at G0 (D-05).
- **IS-01 / IS-06:** HLB-internal numbers and ~28 policy parameters stay as named parameters set by HLB at launch (D-04).
- **IS-02:** parked pain points resolved. PR-05 (narrowed) and PR-06 are promoted to v1; the others stay parked (D-01).
- **IS-04:** design-fix defaults accepted (D-06).
- **AS-27 / AS-28:** confirmed by user (D-02, D-03).
- **RK-22:** mitigated by promoting PR-06.
- **RK-12:** approve-and-guarantee not adopted by default (D-07 / BD-20-10).
