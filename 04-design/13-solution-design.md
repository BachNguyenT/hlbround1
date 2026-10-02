# Solution Design — HLB CreditPulse
**Product:** HLB Point-of-Purchase Credit Decisioning — **HLB CreditPulse** | **Skill:** 13 | **Status:** Done (2026-09-30)
**Depends on:** solution-proposal.md, v1-product-scoping.md, success-metrics.md | **Feeds into:** technical-feasibility.md (Skill 14, not run in this pass), user-journey-map.md

> **Scope rules applied throughout**
> 1. **v1 boundary = Skill 08.** Features tagged `[v1]` are Must/Should in Skill 08. `[Later]` = Parking Lot. `[Parked — pending user]` = Claude's proposed pain-point features the user has not yet assessed (PL-03 to PL-09). These are listed, not designed.
> 2. **Pain points designed for** = the **user-suggested** ones (Awareness, Consideration, Purchase & Repayment, Retention, Advocacy), plus what the **case brief** makes mandatory (R-1 to R-4). The brief's requirements force the Application & Instant Decision stage in (in-session decision, real-time identity and genuineness).
> 3. **No HLB-internal fact is invented.** HLB data sources are "confirmed" only where evidenced (SD-7: CASA and salary inflows, existing HLB loans and cards, CIC, HLB Connect, Sổ Bán Hàng ledger [Brief pp.5–8][R66][R67][R68]). Anything else is labelled `ASSUMPTION A-13-#` and collected in §9.
> 4. **Technology choices are capability-level.** Skill 14 (Technical Feasibility) has not run, so named technologies are *illustrative examples*, not decisions.
>
> Citations: `[R#]` = `_research-notes-07-13.md`; `[D#]` = new sources for this skill (Source Index at end); others as elsewhere. Rubric tags are written `[Rubric R#]`.

---

## 0.A Reconciliation with Skill 14 (Technical Feasibility, 2026-09-30)

Skill 14 ran after this file, per the user-confirmed order SD-5. Its required changes U-01 to U-10 are applied below as follows:

- **Applied inline:** U-01, U-02, U-03, U-05 (branch only).
- **Applied as design rules:**
  - **U-04, rules first.** PR-02 early warning, RT-01 recommendations and the F5 anomaly layer all launch as rules. They move to ML only once labelled PoP outcomes exist. The E-06 challenger for THIN tiers trains on the randomised approval corridor.
  - **U-06, portable hosting.** Every component must be portable and hostable in Vietnam. Region-bound managed services are used only where an in-country offering exists (Decree 53/2022, Circular 09/2020, PDPL transfer rules [Skill14 §4]).
  - **U-07, AI Law timing.** The 1 Sep 2027 grace period appears to cover only systems already operating before 15 Aug 2026 [Skill14 T17]. A new CreditPulse may need its AI Law conformity assessment **before launch** (OQ-14-02).
  - **U-10, core-banking booking.** In-session booking depends on unverified core-banking APIs (B-07). An "approve-and-guarantee" option is on the Skill 22 decision list (OQ-14-11).
- **Owned by other skills:** U-08 (KR 1.2 definition) is applied in Skill 10. U-09 (effort) is applied in Skill 16.

## 0. Design at a glance

```
                    ┌──────────────────────────── DATA (consent-gated) ────────────────────────────┐
 Phase 1 [v1]:  HLB CASA & salary inflows · HLB loans/cards · HLB Connect usage · Sổ Bán Hàng ledger ·
                CIC bureau · application & eKYC (chip-CCCD/VNeID) · checkout-native signals · PoP repayments
 Phase 2 [Should]: Open API consented cross-bank transactions (Circular 64)
 Phase 3 [Later]:  telco · e-wallet · platform-income feeds (Decree 94 sandbox)
                    └──────────────────────────────────┬──────────────────────────────────────────────┘
                                                       ▼
                         ┌──────────── HLB CreditPulse ENGINE ────────────┐
                         │ Consent ledger → Feature store → Model layer   │
                         │ (risk champion/challenger · income estimator · │
                         │  uplift · behavioural · early-warning ·        │
                         │  integrity) → Policy & decision layer          │
                         │ → CUSTOMER CREDIT PROFILE (CCP), 6 fields      │
                         │ Governance: reason codes · drift · fairness ·  │
                         │ audit · human refer band                       │
                         └───────────────────────┬────────────────────────┘
          CCP consumed by ▼ (each stage reads only the CCP fields it needs, under the matching consent purpose)
 ┌───────────┬──────────────┬─────────────────────┬─────────────────────┬────────────┬────────────┐
 │ AWARENESS │ CONSIDERATION│ APPLICATION &       │ PURCHASE &          │ RETENTION  │ ADVOCACY   │
 │ Pre-      │ Personalised │ INSTANT DECISION    │ REPAYMENT           │ Suitable-  │ Repayment- │
 │ approval  │ package      │ In-session tier,    │ Continuous re-score,│ product    │ conditioned│
 │ targeting │ builder +    │ eKYC, integrity,    │ early warning,      │ recommend- │ invite,    │
 │ + holdout │ all-in cost  │ refer band          │ consented graduation│ ations,    │ refer-a-   │
 │           │              │                     │                     │ anti-stack │ merchant   │
 └───────────┴──────────────┴─────────────────────┴─────────────────────┴────────────┴────────────┘
```

---

## 1. The Engine: HLB CreditPulse

### 1.1 Customer Credit Profile (CCP): the output every stage consumes

> **Detailed engine design:** see [`13a-credit-assessment-engine.md`](13a-credit-assessment-engine.md). It covers the model targets, scorecard and challenger construction, income estimation, F3/F5 rules, the limit and counter-offer maths, reason codes, validation, the learning loop and the parameter register.

| # | Field | Type / values | Meaning | Produced by | Refresh trigger |
|---|---|---|---|---|---|
| F1 | **Risk Grade** | A / B / C / D / E, plus a PD band and a model version | Probability-of-default band over the loan horizon | Hybrid: scorecard champion + gradient-boosted challenger (E-06) | New application; each repayment event; monthly batch; any new consented data source |
| F2 | **Affordability Headroom** | VND per month (≥ 0), plus the income estimate with confidence interval | Maximum new instalment the customer can safely carry | Rules: stable-income estimate − existing obligations (CIC + HLB) − living-cost buffer, × DSR cap (policy) | Salary/inflow event; new CIC obligation; monthly |
| F3 | **Evidence Confidence** | BUREAU+BANK / BANK-OBSERVED / CONSENTED-PARTNER / THIN, plus a list of sources used | How much verified evidence supports F1 and F2 | Rules (source presence × consent) | Consent change; source added or removed |
| F4 | **Propensity & Need** | Uplift score (Should, AW-03) or suitability flag (v1), plus top purchase categories | Whether an offer would *incrementally* help this customer, and for what | ML uplift model (after holdout data exists) / rules suitability filter (v1) | Weekly batch; campaign cycle |
| F5 | **Identity & Application Integrity** | Score 0–100, plus flags (for example ID-mismatch, device-velocity) | Confidence that the applicant is who they claim to be and the application is genuine (R-3) | Rules + ML anomaly detection over eKYC result, device and velocity signals, CIC identity match | Every application / draw |
| F6 | **Decision Tier** | PRE-APPROVED (limit L) / INSTANT-APPROVE / REFER / DECLINE, plus reason codes and a path message | The action HLB takes | Policy layer over F1–F5 (E-07) | Whenever F1, F2, F3 or F5 changes |

**Tier policy (rules, explainable by construction; the thresholds are HLB policy parameters, `[DECISION REQUIRED]`, OQ-12-02):**
```
IF F5 integrity < integrity_floor                           → REFER (identity review) or DECLINE (confirmed mismatch)
ELSE IF F2 headroom < minimum instalment of cheapest package → DECLINE, reason "affordability", path = smaller basket / larger down-payment
ELSE IF F1 ∈ {A,B} AND F3 ∈ {BUREAU+BANK, BANK-OBSERVED, CONSENTED-PARTNER} → PRE-APPROVED (existing customer, Cohort A) or INSTANT-APPROVE (at checkout)
        [fix 2026-09-30: CONSENTED-PARTNER added — found by Skill 17C; previously an A/B MSME scored on Sổ Bán Hàng data fell through to DECLINE]
ELSE IF F1 ∈ {A,B,C} AND F3 = THIN                             → INSTANT-APPROVE at STARTER LIMIT (low-and-grow, AP-05)
ELSE IF F3 = THIN AND applicant is in the marginal band AND random draw < policy.corridor_share → INSTANT-APPROVE at STARTER LIMIT, flagged CORRIDOR (randomised approval corridor for learning; size & loss budget [DECISION REQUIRED], OQ-14-07) [U-05; L20, L36]
ELSE IF F1 = C OR (F1 = D AND F3 ≠ THIN)                       → REFER (human, OP-01), with an immediate counter-offer if one is affordable
ELSE                                                           → DECLINE with reason codes and path
Every approval: limit = min(policy limit for grade, F2 headroom × tenor-adjusted factor); CIC stacking check (RT-02) passes.
If the CIC stacking check fails at checkout → REFER when F1 ∈ {A,B}, otherwise DECLINE with reason code EXISTING_OBLIGATIONS
  [default added 2026-09-30, found by Skill 17A/B; confirm in Skill 22].

F2 for THIN applicants (fix 2026-09-30, found by Skill 17A: previously every THIN applicant failed the affordability rule,
  because F2 had no income evidence):
  F2_thin = min( declared_monthly_income × policy.declared_income_haircut , policy.thin_headroom_cap_vnd )
            − CIC-reported monthly obligations − policy.living_cost_buffer_vnd
  declared_monthly_income = one self-stated number captured in the Lane B application.
    It is not a document; R-3 "without document friction" is preserved.
    Where consented bank-observed inflows exist (S1/S4/S16), they replace the declared figure.
  The starter limit (AP-05) is sized so its instalment is ≤ F2_thin. ASSUMPTION A-13-12; confirm in Skill 22.
```

### 1.2 Data Signal Catalogue: assessing what the bank already knows, in new ways `[Rubric R3]`

This responds to the user's requirement that "the engine must assess innovatively different data that the bank generally have about a given bank's customer." The innovation is **not new data types**. It is **reading existing bank records as behavioural evidence** instead of asking for documents. The CIC holds no deposit or transaction data [R71], so every CASA-derived signal below is information **only HLB has**.

| # | Signal (engineered feature) | Raw source | Availability | Feeds | Why it's predictive / what it replaces | Evidence |
|---|---|---|---|---|---|---|
| S1 | **Inflow regularity index**: coefficient of variation of monthly inflows; months with an inflow in the last 6 and 12 | CASA transactions | Confirmed source (SD-7) | F1, F2 | Replaces the payslip. Stable inflows are the brief's own risk basis for SAL-N [Brief p.7] | [Brief p.7]; [L11][L13] |
| S2 | **Salary-day stability**: day-of-month variance of the largest recurring credit | CASA | Confirmed source | F1, F2 | Proxy for employment stability without a labour contract (31% can't or won't provide one) [Skill03 S18] | ASSUMPTION A-13-1: CASA records carry enough counterparty metadata to identify a recurring payer |
| S3 | **Pre-payday liquidity buffer**: minimum balance in the 5 days before the main inflow, relative to average monthly outflow | CASA | Confirmed source | F1, F2 | Measures how thin the customer runs, a direct affordability signal | Cash-flow underwriting literature [L11][L13] |
| S4 | **Platform-payout signature (GIG)**: count and regularity of credits from ride-hailing or delivery platforms, active weeks per month | CASA (payouts landing in an HLB account) | Confirmed source *if* the worker banks with HLB; otherwise Phase 2/3 | F1, F2, F3 | Brief: gig income "received through platforms; consistent… regular platform activity" [Brief p.7]. Values activity consistency over any single month's amount [Skill03 B13] | A-13-2: platform payouts identifiable by counterparty |
| S5 | **Sales-flow signature (MSME)**: QR and transfer receipts, Sổ Bán Hàng daily sales, e-invoice totals, refund ratio, seasonality | CASA + Sổ Bán Hàng (merchant consent) | Confirmed source (Sổ Bán Hàng partnership [R66]) | F1, F2, F4 | Brief: "high transaction activity, digital sales histories, recurring business cash inflows" [Brief p.8]; e-invoicing now mandatory for household businesses [Skill02 RF-13] | [R66]; [Skill02 RF-13] |
| S6 | **Bill-payment punctuality**: on-time share of utility, telco and school bills paid *from CASA* (auto-debit or bill-pay) | CASA bill-pay records | Confirmed source (CASA); the bill-pay feature at HLB is **ASSUMPTION A-13-3** | F1 | The brief names "on-time utility bill payments" as an FTB signal [Brief p.8]; read here from the bank side, no third-party feed needed | [Brief p.8] |
| S7 | **Wallet top-up rhythm**: frequency and regularity of CASA → e-wallet transfers (MoMo, ZaloPay and others identified by counterparty) | CASA | Confirmed source; counterparty identification = A-13-2 | F1, F4 | The brief names "e-wallet top-ups" as an FTB signal [Brief p.8]; observed from HLB's side of the transfer, **without** third-party e-wallet data (which stays Phase 3) | [Brief p.8] |
| S8 | **Savings discipline**: recurring transfers to savings or term deposits; share of inflow saved | CASA / deposits | Confirmed source | F1, F2 | Evidence of forward planning; a buffer against shocks | Cash-flow literature [L11] |
| S9 | **Essential-to-discretionary spend ratio and spend volatility** | HLB card and QR spend by merchant category | Cards = confirmed source (SD-7); category coding = **A-13-4** | F2, F4 | Affordability realism, and the purchase categories for Retention recommendations | [R51] (NPTB) |
| S10 | **Existing HLB credit conduct**: utilisation, on-time share and instalment history on HLB cards and loans | HLB loan and card systems | Confirmed source | F1 | Internal behavioural score, the strongest signal for Cohort A | Nubank survival and risk ranking [D1] |
| S11 | **Relationship depth and stability**: tenure with HLB, number of products, stability of KYC contact data | Core banking / KYC | Confirmed source | F1, F5 | Stability proxy; sudden KYC changes before an application are an integrity flag | FM-11 |
| S12 | **Digital engagement pattern**: HLB Connect login regularity, bill-pay self-service use | HLB Connect | Confirmed source [R67] | F4 (only) | Used for **propensity and channel choice only, never for risk**, to avoid penalising less digital customers (fairness) | Design decision DD-07 |
| S13 | **Checkout-native footprint**: basket category, down-payment chosen, device type, time of day, email/phone validity | Merchant checkout + application | Collected at application with consent | F1 (thin files), F5 | Digital-footprint variables alone reach AUC 69.6% vs 68.3% for a bureau score and complement it [R80][L1]. Excludes contacts and gallery (X-01) | [R80][L1] |
| S14 | **CIC file**: existing obligations, delinquency, number of lenders, recent enquiries | CIC | Confirmed source | F1, F2, F5, anti-stacking | Remains a core input, not replaced. Every scaled analogue blends bureau and proprietary data [R13] | [R13]; [Skill02 RF-11] |
| S15 | **PoP repayment behaviour**: days early or late, partial payments, payment-channel changes | HLB PoP loan ledger | Generated by the product | F1, F2, early-warning, graduation | Behavioural data after the first loan is the richest signal for thin files. Drives continuous reassessment (user requirement) | [D1][R50] |
| S16 | **Cross-bank inflows**: consented transaction history from other banks | Open API (Circular 64) | **Phase 2 [Should]** | F1, F2, F3 | Lets SAL-N and GIG customers who are salaried or paid elsewhere be assessed on inflows | [R73] |
| S17 | **Telco / e-wallet / platform feeds** | Third parties | **Phase 3 [Later]**, PL-01 | F1, F3 | Brief-named FTB signals [Brief p.8], deferred on legal readiness | [R74][R76] |

**Excluded by design:** phone contacts and gallery (X-01, [R34]); social-media scraping; protected attributes (gender, ethnicity, religion) as model inputs. Gender, age band and region are used **only** for fairness monitoring (G-5). "Referred by a friend" is **not** a risk feature, per the evidence that referrals don't screen [R61].

**How each segment gets assessed (R-1 coverage check):**

| Segment | Share [Brief p.9] | v1 primary signals | Evidence Confidence typically reached |
|---|---|---|---|
| SAL-C | 35.5% | S14 CIC, S10, S1, S2 | BUREAU+BANK → pre-approval / instant approve |
| SAL-N | 20.5% | S1, S2, S3, S8 (if an HLB account holder); S16 Phase 2 otherwise | BANK-OBSERVED (existing customers) / THIN (new-to-bank) |
| GIG | 15.0% | S4 (if paid into HLB), S1, S3, S7, S13, S14 | BANK-OBSERVED or THIN → starter limit |
| MSME | 14.5% | S5 (Sổ Bán Hàng + QR receipts), S1, S14 | CONSENTED-PARTNER / BANK-OBSERVED |
| FTB | 14.5% | S6, S7 (if an HLB account holder), S13, S11 | THIN → starter limit + CASA-first; S17 later |

### 1.3 Pain point → feature → CCP field → KPI traceability (user-suggested pain points)

| Stage | User-suggested pain point (quoted / paraphrased from user, 2026-09-30) | Feature(s) `[v1]` | CCP fields consumed | KPI (Skill 10) |
|---|---|---|---|---|
| Awareness | "identify which of the bank's customers have never used the PoP lending service but have high potential (low risk) as target audience… for marketing" | **AW-01** target-list generator; **AW-02** suitability filter + holdout; **AW-04** pre-approved limit in HLB Connect; AW-03 uplift model [Should] | F1, F2, F3, F4, F6 (PRE-APPROVED) | KR 3.1 incremental take-up vs holdout; G-6 complaint rate |
| Consideration | "analyse the customer's status to offer different lending packages according to their financial scores/status" | **CO-01** personalised package builder; **CO-02** all-in cost & schedule; CO-03 comparison [Should] | F1 (rate tier), F2 (max instalment → feasible tenors), F6 (limit) | KR 3.2 package acceptance; G-4 affordability breach = 0 |
| Purchase & Repayment | "keep track and continuously reassess and refine the customer profile based on their credit payment history" | **PR-01** behavioural re-scoring; **PR-02** early warning → hardship outreach; **PR-03** consented graduation ladder; **PR-04** CIC reporting | All six; S15 is the new input | KR 3.3 cure rate; G-1 MOB3 30+; G-8 drift |
| Retention | "indicate suitable products that have high potential of PoP purchase for marketing to suitable users" | **RT-01** recommendations (propensity × headroom × partner catalogue); **RT-02** anti-stacking gate; RT-03 ready-limit dashboard [Should] | F2, F4, F6 | KR 3.4 good-payer repeat rate; G-3 stacking |
| Advocacy | "customer is loyal and invites other people… think of something innovative" | **AD-01** repayment-conditioned invite; AD-02 refer-a-merchant [Should]; AD-03 good-payer passport [Should] | F1, F6 (inviter eligibility); invitee gets their own CCP | KR 3.5 referral quality |
| **Application & Instant Decision** (brief-mandated) | Brief R-2 / R-3: decide "within the purchase moment"; verify identity and genuineness "in real time… without document friction" [Brief p.6] | **AP-01** in-session decision; **AP-02** chip-CCCD/VNeID eKYC; **AP-03** integrity checks; **AP-04** refer band; **AP-05** starter limit; **AP-06** CASA-first; **AP-07** CIC fallback; **OP-01** officer workbench; **MX-01** checkout integration | All six | KR 1.1–1.4; G-2 fraud |

---

## 2. UX Flows — all six stages (happy, alternative and error paths)

Lanes: **Lane A = Cohort A**, existing HLB customer, profiled in advance. **Lane B = Cohort B**, new-to-bank applicant at a partner checkout. Consent purposes: **SC** = scoring, **MK** = marketing/targeting, **RF** = referral (E-01, condition C1).

### Flow 1: Awareness (Lane A) `[v1]`

**Happy path**
1. Nightly batch: CreditPulse refreshes the CCP for every Lane A customer with SC consent. The AW-01 target-list generator then selects customers who meet all four conditions:
   - they have never used PoP credit;
   - F1 ∈ {A, B};
   - F2 ≥ the minimum package instalment;
   - F3 ≠ THIN.
   → Candidate list.
2. AW-02 splits the candidates:
   - It applies a suitability filter: exclude anyone with an early-warning flag, a CIC stacking signal, a recent hardship episode, or MK consent absent.
   - It holds out a random share, for example 10%. The holdout share is `[DECISION REQUIRED]` and can be set as a campaign parameter.
   → Treatment group and holdout group.
3. Treatment customers with MK consent receive the offer, for example a push or in-app card in HLB Connect: "You can pay over time up to {L} VND at {partner categories}". The copy shows the all-in cost *range* and a link to the full terms (CO-02 principle, FM-4) → Offer delivered.
4. The customer taps the offer and sees their pre-approved limit, example packages and eligible partner merchants → Consideration (Flow 2).

**Alternative paths**
- **Customer has SC but not MK consent.** No push is sent. The pre-approved limit is shown **only** when the customer opens the "Pay Later" hub themselves; that is a service display, not marketing. This split is ASSUMPTION A-13-5 and needs legal confirmation (OQ-13-01).
- **Customer is eligible through a partner** (for example a platform) and a partner data agreement exists (AW-05, Should, D-08-2). The partner shows an "HLB pay-over-time invitation" in its own app. Pattern: HLB Malaysia × Grab 2021 [R8].
- **Once an uplift model exists** (AW-03, Should; built after ≥1 cycle of holdout data): step 1 ranks candidates by uplift score rather than by grade.

**Error paths**
- **Consent withdrawn between the list build and delivery.** Delivery-time consent re-check suppresses the offer → logged (KR 4.1).
- **Customer's CCP changes adversely before they open the offer** (for example a new CIC obligation). The offer card re-validates on open and shows "Your offer has been updated", with the new limit or no offer.
- **Complaint or opt-out.** The customer receives no MK contact for a cooling period `[DECISION REQUIRED]`. G-6 counts the complaint. Frequency caps (RT-04) are `[Parked — pending user]`.

### Flow 2: Consideration (Lanes A and B) `[v1]`

**Happy path**
1. The customer selects "Pay over time" at the merchant checkout (MX-01: API, hosted page, or QR deep-link into HLB Connect), or from the Awareness offer → Package request with the basket value and merchant.
2. CO-01 reads F1 (rate tier from the published risk-tier grid), F2 (maximum affordable instalment), F6 (limit) and the merchant's 0% subsidy eligibility. It builds **2–4 packages**. Each package fits:
   - tenor ∈ {3, 6, 9, 12, 18, 24} [Brief p.9];
   - instalment ≤ F2;
   - down-payment options;
   - 0% where the merchant subsidises it.
   → Package list.
3. CO-02 shows, for each package:
   - monthly instalment;
   - number of payments and their dates;
   - **total amount payable**;
   - all-in APR;
   - fees;
   - late-payment consequences.
   Nothing is bundled; no insurance is forced (FM-4, [R41]) → The customer compares (CO-03, Should).
4. The customer picks a package → Application & Instant Decision (Flow 3).

**Alternative paths**
- **Basket exceeds the limit or F2.** The system offers a larger down-payment or a longer tenor that fits F2, or a split basket. It never shows a package above F2 (G-4).
- **Lane B customer with no CCP yet.** Packages are shown as "indicative" ranges (grade-agnostic) until Flow 3 produces a CCP. The final package is confirmed after the decision.

**Error paths**
- **Grid or merchant subsidy data unavailable.** The system falls back to the standard risk-tier grid without 0% offers and tells the customer "promotional offer unavailable".
- **Session timeout.** The chosen package is saved for 30 minutes. That number is ASSUMPTION A-13-6.

### Flow 3: Application & Instant Decision (Lanes A and B) `[v1]`, the core bottleneck `[Rubric R1]`

**Happy path, Lane A (pre-approved)**
1. The customer confirms the package in HLB Connect.
2. Biometric authentication runs (face match against the chip-CCCD already on file) (AP-02, [R67], [Skill02 RF-4/RF-5]).
3. CreditPulse re-validates F5 (integrity) and F2, and runs a CIC stacking check (RT-02) → **INSTANT-APPROVE**.
4. The customer signs the e-contract with OTP. The merchant receives a payment guarantee and the order completes → Purchase & Repayment. Target: p50 ≤ 3 min (KR 1.2).

**Happy path, Lane B (new-to-bank)**
1. The customer enters minimal fields: phone, CCCD NFC scan, and consent choices (SC required; MK and RF optional). Document uploads are not required (R-3).
2. eKYC runs: NFC chip-CCCD read, then liveness and face match. HLB's existing National-ID-Registry integration has cut onboarding from ~300 s to ~80 s [R67]. The result feeds F5.
   **Web checkout:** browsers cannot read the CCCD chip over NFC [Skill14 T24]. On a web widget or hosted page, the flow hands off to HLB Connect by QR code or deep link, or uses a merchant-app SDK or VNeID authentication, whichever the method chosen in spike SP-3 (OQ-14-05). The hand-off time counts toward KR 1.2 [U-01].
   **Parallel CIC check:** once the chip read returns the ID number, the CIC check starts in parallel with liveness and face match, subject to `policy.cic_timeout_s` [DECISION REQUIRED] [U-03].
3. CreditPulse assembles the evidence: CIC (S14), checkout footprint (S13), and application data. S16 cross-bank Open API data is used only if the customer opts in (Phase 2) → CCP built. F3 is typically THIN.
4. The policy layer returns **INSTANT-APPROVE at STARTER LIMIT** (AP-05). The customer can open a CASA account, and the disbursement or refund account defaults to the new HLB account (AP-06, CASA-first [Brief p.8]).
5. e-contract, OTP, order completes → Purchase & Repayment.

**Alternative paths**
- **REFER tier.** The customer sees "We need a little longer to review — we'll reply within {SLA}". In the same session they are offered an **immediate counter-offer** where one is affordable: lower amount, shorter tenor, or larger down-payment (lit. review M7).
  - If the customer accepts the counter-offer, the flow continues on the happy path.
  - If not, the case goes to the officer workbench (OP-01) with the evidence pre-assembled. The merchant places a reservation hold, per merchant policy (A-13-7). The officer decides within the SLA (KR 1.4 target ≥ 90% within 2 business hours). The customer is notified in-app and by SMS.
- **Customer already in the Parked lane.** Nothing extra is designed here. AP-08 eKYC fallback flows and AP-09 rich decline explanations are `[Parked — pending user]`. The minimum v1 behaviour is in the error paths below.

**Error paths**
- **eKYC fails** (face not recognised, NFC read failure; a real pattern in Vietnam [Skill03 S44]). Minimum v1: the customer can retry up to 3 times (A-13-8). After that, the case goes to REFER with an "identity review" reason. A full assisted fallback is PL-04.
- **CIC unavailable** (FM-12). No public CIC availability SLA exists, so fallback is prudent design. The Sep 2025 CIC incident was a **data breach**, and CIC services continued [Skill14 T21][R71]. Its lesson is **CIC data minimisation**, not outage frequency [U-02]. AP-07 fallback rules apply:
  - Lane A customers with F3 = BUREAU+BANK and a CIC pull under 30 days old: decide on the cached pull.
  - Otherwise: REFER, or a starter limit capped lower (`[DECISION REQUIRED]`).
- **Integrity flag** (F5 < floor): REFER for identity review. A confirmed mismatch is a DECLINE, and the event is logged for the fraud team (G-2).
- **DECLINE.** The customer is shown the top reason codes (E-09, AI-Law transparency) plus a generic path, for example "a smaller basket or a larger down-payment may be approved". Richer "path-to-yes" guidance is PL-05.

### Flow 4: Purchase & Repayment, continuous reassessment `[v1]`

**Happy path**
1. After booking, the loan is reported to CIC (PR-04) and the repayment schedule appears in HLB Connect.
2. On each repayment event (S15), PR-01 re-scores the customer. F1, F2 and F4 are updated and the CCP history is versioned.
3. After N on-time instalments (N is `[DECISION REQUIRED]`; example 3) **and** a refreshed F2 **and** a clean CIC stacking check, PR-03 **offers** a graduation step. Options are a higher limit, access to longer tenors, or a better rate tier. The customer must **explicitly accept** (C2, [R30]); nothing applies automatically.
4. The customer's "Ready limit" updates (RT-03, Should) → Retention.

**Alternative paths**
- **Early-warning flag** (PR-02: predicted missed payment next cycle). The customer receives a supportive, non-coercive message: a payment reminder with the date and amount, plus a link to contact HLB about difficulty. Suppressions apply while the flag is active: no marketing, no graduation offers, and a pause on the customer's pre-approved limit. Structured flexible-repayment options (PL-06) are `[Parked — pending user]`; strong evidence recommends them [R64].
- **Customer repays early.** The customer's profile is refreshed, and the early-settlement terms shown are the ones disclosed in CO-02.

**Error paths**
- **Missed payment.** The standard collections process applies, and F1 is downgraded on the next re-score. Collection-conduct standards (PL-07, "never contact references") are `[Parked — pending user]`. By design, the product requires no reference contacts at application.
- **Model drift alert** (G-8 PSI > 0.25). Re-scoring falls back to the champion model, and the graduation offers that the drifting model generated are paused.

### Flow 5: Retention `[v1]`

**Happy path**
1. Weekly, RT-01 builds recommendations for customers with MK consent, an active or recently completed PoP loan, and no early-warning flag. The logic is:
   - F4: which purchase categories the customer is likely to buy, from S9 spend categories and past PoP baskets;
   - filtered by F2 headroom;
   - matched to the **partner merchant catalogue** (partner products eligible for PoP, including merchant-subsidised 0% promotions).
   → Up to 3 recommendations.
2. RT-02 anti-stacking gate: the customer is suppressed if CIC shows ≥ 2 concurrent consumer loans or rising total obligations (G-3).
3. **Random holdout (C4 / FM-5):** a random share of eligible customers, set by the same holdout parameter as AW-02, receives no recommendations. Incremental repeat take-up is measured against this group (KR 3.4). *Fix 2026-09-30, found by Skill 17C: binding condition C4 applies to Retention targeting as well as Awareness.*
4. The recommendation card in HLB Connect shows, for example, "{Partner} {category} — pay over 6 months from {instalment} VND/month, total {X} VND". It includes the all-in cost and links to Consideration (Flow 2).

**Alternative paths**
- **Customer without MK consent.** No recommendations are shown. The "Ready limit" dashboard is still visible when they open the hub (service display).
- **Customer with a completed loan and no new purchase in 90 days.** At most one re-engagement nudge is sent. The frequency caps policy is PL-09, `[Parked — pending user]`.

**Error paths**
- **Partner catalogue feed missing.** Category-level recommendations are shown without products.
- **Customer dismisses the recommendation 3 times.** Recommendations are suppressed for that category (A-13-9).

### Flow 6: Advocacy `[v1 = AD-01; Should = AD-02, AD-03]`

**Happy path, AD-01 repayment-conditioned invite**
1. The invite option is available only to customers who meet all of these conditions:
   - at least N on-time instalments;
   - F1 ∈ {A, B};
   - no early-warning flag;
   - RF consent given.
   The customer taps "Invite someone" → HLB generates a personal link.
2. The invitee opens the link and gets an **instant eligibility check**: Lane B, which requires their own SC consent. The invitee gets their own CCP. The inviter's data is never used to score the invitee, and "referred" is not a risk feature [R61].
3. Rewards: the referrer's reward (for example a fee waiver or merchant voucher; type is `[DECISION REQUIRED]`) is **released only after the invitee's first K on-time instalments** (K is `[DECISION REQUIRED]`; example 3). Nothing is paid at sign-up or at borrowing (C5, the enforcement effect in [R61]).
4. The inviter sees the status "Your friend's reward unlocks after 3 on-time payments" and never sees the friend's credit details. This is privacy by design.

**Happy path, AD-02 refer-a-merchant (Should)**
1. The customer taps "Suggest a shop" and enters the shop's name and phone, with the shop owner's consent to be contacted → The lead goes to the HLB merchant team.
2. The shop is onboarded via Sổ Bán Hàng (HLB account opening, bookkeeping, QR, e-invoice [R66]). The shop becomes a PoP acceptance point. With the merchant's consent, the shop can also become an MSME borrower scored on S5.
3. Once the shop processes its first PoP sale, the customer receives a merchant-funded perk at that shop. Merchant value: BNPL raises merchant sales by about 20% [R57].

**Happy path, AD-03 good-payer credit passport (Should, gated)**
1. The passport is available after the customer completes a PoP loan with every instalment paid on time, **provided** total CIC obligations are within affordability. The gating comes from FM-7: the credit-builder RCT harmed borrowers with existing debt [R62].
2. The customer can present the passport in HLB Connect at partner merchants to unlock their graduation tier. It shows their on-time record, which is also reflected in CIC reporting.

**Error paths**
- **Invitee declined.** The inviter sees only "Your invite wasn't used"; no decline details are shared (privacy).
- **Referral abuse** (self-referral, duplicate device or identity). F5 integrity flags apply, and no reward is released.
- **Invitee becomes delinquent.** No reward is released. KR 3.5 tracks this, and if the referred cohort's MOB3 30+ exceeds the non-referred cohort's, invites are paused.

---

## 3. Information Architecture

```
HLB Connect (customer app — existing channel [R67])
└── "Pay Later / Trả sau" hub  [new]
    ├── My limit            — pre-approved / ready limit (F6, F2), graduation progress (PR-03) [RT-03 Should]
    ├── Offers for you      — Awareness offer card (AW-04) · Retention recommendations (RT-01)   [MK consent only]
    ├── My plans            — active PoP loans, schedule, total payable, pay now, early-settlement terms
    ├── Invite & earn       — AD-01 invite link + reward status · AD-02 suggest a shop [Should]
    ├── Good-payer passport — AD-03 [Should, gated]
    ├── Why this decision   — reason codes for latest decision (E-09)
    └── Privacy & consent   — per-purpose toggles SC / MK / RF, data sources used, withdraw (E-01, PDPL [R46])

Merchant / partner checkout (MX-01)
├── Option A: embedded API widget   "Pay over time with HLB — from {x} VND/month"
├── Option B: hosted HLB page       (redirect-and-return, same session)
└── Option C: QR deep-link          (in-store: scan → HLB Connect → decision → merchant confirmation)
    Checkout screens: package list (CO-01) → all-in cost (CO-02) → eKYC/biometric (AP-02) → decision (AP-01) → e-contract

Officer workbench (OP-01) — internal
├── Refer queue (SLA timer, KR 1.4)
├── Case view: CCP fields + sources + reason codes + model version + counter-offer tried
├── Decision: approve / counter-offer / decline — mandatory justification on override (audit, AI Law human oversight)
└── Fraud escalation

Risk & governance console (GV-01; OP-02 Should)
├── Model registry & champion/challenger split
├── Monitoring: PSI drift (G-8), fairness ratios (G-5), MOB3 cohorts (G-1), stacking (G-3)
├── Policy parameters: tier thresholds, DSR cap, starter limit, rate grid  [DECISION REQUIRED values]
└── Audit log export (AI Law technical documentation, KR 4.2)

Campaign workspace (AW-01/02/03, RT-01) — internal
└── Target list builder · holdout config · uplift report (KR 3.1) · consent-at-send check
```

---

## 4. System Architecture Overview

> Capability-level. Technology names are **illustrative examples only**; Skill 14 (Technical Feasibility) has not run.

```
Components:
  Channels (Frontend):
    - HLB Connect mobile app (existing [R67]) — new "Pay Later" hub modules
    - Merchant checkout SDK/widget + hosted page + QR deep-link (MX-01)
    - Internal web apps: officer workbench, risk console, campaign workspace
    Rationale: reuse the existing, award-winning HLB digital channel [Brief p.3]; meet customers
    "within the shopping journey" [Brief p.4].

  Backend services:
    1. Consent Service (E-01) — per-customer, per-purpose (SC/MK/RF) consent ledger; every read of
       the CCP by a consuming service passes a purpose check. Rationale: PDPL purpose-specific,
       verifiable consent [R46]; KR 4.1.
    2. Data Ingestion — batch (core banking, cards/loans, Sổ Bán Hàng daily) + event stream
       (CASA transactions, repayments, application events). Example tech: an event-streaming bus
       (e.g. Kafka-class) as used by Nubank for real-time event sourcing [D3].
    3. Feature Store (online + offline) — engineered signals S1–S17 with point-in-time
       correctness; online store serves checkout-time features, offline store serves training.
       Rationale: online/offline feature parity is the standard real-time credit pattern [D4][D5];
       Nubank cites reusable feature stores as a foundation [D1].
    4. Model Layer — (a) risk champion scorecard, (b) risk challenger GBM, (c) stable-income
       estimator, (d) behavioural/survival re-score [D1], (e) early-warning, (f) uplift (Should),
       (g) integrity anomaly model. Served behind a single scoring API with model versioning.
    5. Policy & Decision Service (E-07) — deterministic rules over F1–F5 → F6 tier + limit +
       package constraints + reason codes (E-09). Rationale: explainable by construction; the
       place HLB risk appetite lives (OP-02).
    6. CCP Store & API — current CCP + versioned history per customer; read APIs by purpose.
    7. Stage services (consumers): Campaign (AW), Offer/Package (CO), Checkout Decision (AP),
       Servicing & Graduation (PR), Recommendation (RT), Referral Ledger (AD).
    8. Case Management (OP-01) — refer queue, SLA, overrides with justification.
    9. Governance & Monitoring (GV-01) — drift, fairness, audit log, model registry.

  Database / storage:
    - CCP store (low-latency key-value for current profile) + versioned history (analytical store)
    - Consent ledger (append-only, auditable)
    - Decision log (every decision: inputs snapshot hash, model versions, tier, reason codes) —
      required for AI-Law documentation and dispute handling [Skill02 RF-8]; FM-10 [R40]
    - Loan ledger integration with HLB core banking (A-13-10: core banking exposes the needed APIs)

  External APIs:
    - CIC (bureau pull + reporting) [Skill02 RF-11]; fallback rules on unavailability (AP-07) [R71]
    - National ID / VNeID / chip-CCCD verification (existing HLB integration [R67]; Decree 69/2024 [Skill02 RF-6])
    - Sổ Bán Hàng (merchant ledger; merchant consent) [R66]
    - Merchant/partner checkout integrations (MX-01)
    - Phase 2: Open API (Circular 64) cross-bank data [R73]
    - Phase 3 [Later]: telco / e-wallet / platform feeds (Decree 94 sandbox) [R74]

  Auth:
    - Customers: HLB Connect login + biometric authentication for disbursement-grade actions
      (Circular 50/2024, Decision 2345 tiers [Skill02 RF-5]); new-to-bank: chip-CCCD NFC + liveness
    - Merchants: API keys + signed requests (mutual TLS) — A-13-11
    - Staff: role-based access; officers see only refer-queue cases; data minimisation for CIC data
      (CIC incident lesson [R71])

Data flows:
  DF-1 Nightly pre-approval (Awareness):
    core banking/cards/HLB Connect/Sổ Bán Hàng → ingestion → feature store (offline) → model layer
    (F1, F2, F4) → policy (F6 PRE-APPROVED) → CCP store → Campaign service reads with MK purpose
    → holdout split → HLB Connect delivery → response events back to feature store (uplift training).

  DF-2 Checkout decision (Consideration + Application), target p50 ≤ 3 min end-to-end (KR 1.2):
    merchant checkout → Offer/Package service reads CCP (SC purpose) or requests new CCP (Lane B)
    → eKYC ∥ CIC pull (parallel after chip read; cached if valid; timeout policy.cic_timeout_s) → online features → scoring API → policy →
    tier + packages + reason codes → customer accepts → e-contract → loan booked → merchant confirmed.

  DF-3 Repayment re-score (continuous reassessment):
    repayment event → stream → feature update (S15) → behavioural model → CCP version n+1 →
    if graduation criteria met → Servicing service creates *offer* (not applied) → customer consent →
    limit updated; if early-warning → suppression flags propagate to Campaign/Recommendation.

  DF-4 Refer path:
    policy REFER → counter-offer attempt → case created (evidence snapshot) → officer decision
    (override justification logged) → customer & merchant notified → outcome fed back as a label
    for champion/challenger evaluation.

  DF-5 Referral:
    inviter link (RF purpose) → invitee Lane B flow → loan → K on-time instalments → Referral
    ledger releases reward → KR 3.5 cohort tracking.

  DF-6 Governance:
    decision log + outcomes (MOB3) → monitoring jobs (PSI G-8, fairness G-5, stacking G-3) →
    alerts → fallback to champion / pause feature.
```

---

## 5. Design Decision Log

| # | Decision | Chosen Approach | Rationale | Alternatives Considered | Source |
|---|---|---|---|---|---|
| DD-01 | Engine output format | One six-field CCP shared by all stages, rather than stage-specific scores | User SD-4. A single profile means one data investment serves all stages, with consistent explanations | Separate scores per stage (rejected: inconsistent decisions, duplicated models) | `_context.md` SD-4 |
| DD-02 | Risk scoring method | Scorecard champion + GBM challenger on a randomised split | Makes AI value measurable per segment (KR 2.1). The ML edge is largest for thin files | Pure ML (governance risk; AI Law); pure rules (loses thin-file precision) | [L4][L9][L18][L19]; [R16] |
| DD-03 | Affordability | Rules-based cap over an ML income estimate | Affordability is a legal/ethical boundary, so it stays transparent. ML only estimates income from irregular inflows | ML-optimised limit (rejected: opaque, could optimise toward over-lending) | [R50]; Pain #10 |
| DD-04 | Targeting | Suitability filter + random holdout at launch; uplift model once holdout data exists | Propensity ≠ incrementality; naive propensity targets the riskiest customers | Propensity-only targeting (rejected, FM-5); broadcast (rejected, brief R-4) | [R49][R53][R55]; [Brief p.6] |
| DD-05 | Limit increases | Offered with explicit consent; sized on risk-adjusted profit | Increases drive borrowing for years; strictest benchmark is explicit consent per increase | Auto-increase (rejected, FM-2) | [R50][R49][R30] |
| DD-06 | Refer band at checkout | Immediate affordable counter-offer, else human review within SLA with merchant hold | Keeps most referred customers in-session; meets AI Law human oversight | Decline all uncertain cases (loses thin files); hold every case (loses the purchase moment) | Lit. review M7; [Skill02 RF-8] |
| DD-07 | Digital engagement data (S12) | Used for propensity/channel only, **never** risk | Avoid penalising less-digital customers (fairness) | Use in risk grade (rejected) | [L21][L23] |
| DD-08 | Referral design | Reward released after the invitee's on-time instalments; "referred" excluded as a risk feature; no reference contacts | Enforcement effect is evidenced, screening is not; reference-contact harassment is a Confirmed harm in Vietnam | Sign-up bonuses (rejected, AD-04); group liability (rejected, Pain #8) | [R61][R60]; [Skill03 Pain #8] |
| DD-09 | Data phasing | Phase 1 bank-held + CIC + Sổ Bán Hàng; Phase 2 Open API; Phase 3 third-party via sandbox | Start where data is legal and owned. CIC lacks deposit data, so HLB's CASA is proprietary | All sources at launch (rejected: PDPL/sandbox uncertainty) | [R71][R73][R74][R76]; C6 |
| DD-10 | Pricing | Published risk-tier rate grid; 0% only where merchant-subsidised; all-in cost shown before acceptance | Risk-based pricing is lawful and fair; WTP pricing is not. 0% marketing is an enforcement target | Personalised WTP rates (rejected, CO-04) | [R56][R41]; [Brief p.4] |
| DD-11 | Pre-approval display without MK consent | Shown only when the customer opens the hub (service), not pushed | Keeps marketing use under MK consent while letting customers see their own status | Push to everyone with SC consent (rejected: purpose creep) | [R46]; A-13-5 (legal confirmation needed) |
| DD-12 | Checkout integration | Three options: API widget, hosted page, QR deep-link | Covers e-commerce, merchant apps and physical stores, the three channels the brief names [Brief p.4]. Baymard: group BNPL with similar payment methods and keep forms short | Single integration type (would exclude in-store) | [Brief p.4]; [D6] |
| DD-13 | Model monitoring | PSI drift alerts (0.10 / 0.25), fairness ratio alerts, automatic fallback to champion | COVID-era models were overridden or turned off; continuous monitoring is standard | Periodic manual review only (rejected, FM-9) | [R42][R45]; [M1 in Skill10]; [D1] |
| DD-14 | Existing-customer entry first | Pre-approval for Cohort A in v1; Cohort B via starter limit + CASA-first | Incumbent-bank pattern (KBank) and HLB Group precedent start with observable customers | New-to-bank only (weak data); existing only (fails R-1 for thin-file new applicants) | [R26][R8]; [Brief p.8] |

---

## 6. Explicit Exclusion List: "We chose NOT to…"

- …harvest phone contacts, photo galleries or social-media data (X-01; [R34]; Vietnam black-credit stigma [R37]).
- …use gender, ethnicity, religion or region as model inputs. They are used for fairness monitoring only (G-5).
- …use "was referred by a friend" as a signal that lowers risk [R61].
- …auto-apply limit increases or new offers (DD-05).
- …price by willingness-to-pay, or bundle insurance with the loan (DD-10; [Skill03 Pain #7]).
- …broadcast PoP credit to all customers (brief R-4 [Brief p.6]).
- …pay referral rewards at sign-up or at the point of borrowing (DD-08).
- …integrate telco, e-wallet or platform third-party data in v1 (PL-01; Decree 94 and PDPL readiness).
- …design the parked, user-unassessed pain-point features: eKYC fallback flows, rich decline guidance, flexible repayment schedules, collection-conduct standards, literacy nudges, contact-frequency caps, merchant portal (PL-03 to PL-09). They are listed, not designed.
- …require reference contacts, guarantors or document uploads in the digital journey [Brief p.6 R-3; Skill03 Pain #8].

---

## 7. How the design meets each case-brief requirement (compliance check) `[Rubric R2][Rubric R3]`

| Brief requirement [Brief p.6] | Where it is met |
|---|---|
| **R-1** Assess beyond the bureau for young professionals, gig workers and online merchants | Signal catalogue S1–S13 (bank-held behavioural evidence), S5 Sổ Bán Hàng, starter limit + CASA-first (AP-05/06), Phase 2/3 data; segment coverage table §1.2 |
| **R-2** Decide within the purchase moment, at proportional cost, maintaining risk quality, responsible lending and compliance | Flow 3 (in-session tiers, p50 ≤ 3 min); refer band only for uncertain cases (KR 1.3 blended cost); G-1 MOB3 ceiling; G-4 affordability = 0 breaches; AI Law human oversight (OP-01); PDPL consent (E-01) |
| **R-3** Verify identity and application genuineness in real time without document friction | Chip-CCCD NFC + liveness + VNeID (AP-02, HLB's existing ~80 s eKYC [R67]); F5 integrity model (AP-03); no document uploads |
| **R-4** Identify customers most likely to need, accept and responsibly use embedded credit; targeted, not broadcast | Flow 1 (AW-01/02/03): suitability + uplift with holdout; Retention RT-01 gated by F2 and anti-stacking |
| "Fair and transparent cost" [Brief p.4] | CO-02 all-in cost and schedule; published rate grid (DD-10) |
| "Data-to-decision architecture (inputs, logic, outputs, business actions)" [Brief p.11] | §0 overview, §1 CCP + signal catalogue, §4 data flows DF-1 to DF-6, tier policy §1.1 |
| "Sustainable… long-term" / "comprehensive impact on the community" [Brief p.11] | CASA-first graduation of invisible customers into bank-observable ones (KR 2.3); safer alternative to black credit [Brief p.4][R37]; merchant flywheel via Sổ Bán Hàng (AD-02) |

---

## 8. Open Items & Ambiguities Raised by This Skill

| # | Item | Why it matters | Carry to |
|---|---|---|---|
| OQ-13-01 | **Legal:** is showing a pre-approved limit inside the app (on customer-initiated visit) a service use under scoring consent, or does it need marketing consent? (DD-11, A-13-5) | Decides how many Cohort A customers can see Awareness offers | Skill 22 |
| OQ-13-02 | Policy parameters are HLB decisions: tier thresholds, integrity floor, DSR cap, living-cost buffer, starter limit, N instalments for graduation, K instalments for referral reward, holdout %, reward type | Needed to finalise the tier policy §1.1 and flows | Skill 22 `[DECISION REQUIRED]` |
| OQ-13-03 | Merchant reservation hold during the refer band (A-13-7): will partner merchants hold goods for up to the SLA? | If not, referred customers are lost at checkout | Skill 22 / merchant interviews |
| OQ-13-04 | Whether HLB core banking and HLB Connect expose the transaction metadata (counterparty IDs, merchant category codes) that S2, S4, S7 and S9 rely on (A-13-1 to A-13-4) | Determines how many of the "innovative" CASA signals are feasible in v1 | Skill 14 / 22 |

---

## 9. Assumptions Introduced by This Skill (carried to Skill 22)

| # | Assumption | Basis | Risk if wrong |
|---|---|---|---|
| A-13-1 | CASA records carry enough counterparty metadata to identify a recurring salary payer | Standard bank transaction data; not HLB-verified | S2 unusable; SAL-N assessed on inflow level only |
| A-13-2 | Platform payouts and e-wallet top-ups are identifiable by counterparty in CASA | As above | S4 and S7 weaker for GIG and FTB |
| A-13-3 | HLB offers bill-pay or auto-debit from CASA | Not verified for HLB VN | S6 unavailable; FTB relies more on S13 |
| A-13-4 | Card and QR spend can be coded by merchant category | Standard card data; QR coding not verified | S9 weaker; Retention recommendations less precise |
| A-13-5 | In-app display of one's own pre-approval on self-initiated visit counts as a service use, not marketing | Legal interpretation needed | Awareness reach smaller |
| A-13-6 | Package selection saved for 30 minutes | Design default | Minor |
| A-13-7 | Partner merchants will hold goods during the refer SLA | Not evidenced | Refer-band customers lost |
| A-13-8 | 3 eKYC retries before REFER | Design default | Minor |
| A-13-9 | Suppress a recommendation category after 3 dismissals | Design default | Minor |
| A-13-10 | HLB core banking exposes APIs for loan booking and ledger events | Not verified | Integration effort (Skill 14) |
| A-13-11 | Merchants integrate via API keys + mutual TLS | Common practice; not HLB-verified | Minor |
| A-13-12 | Lane B collects one self-declared monthly income figure, used with a haircut and cap for F2 on THIN files | Design fix for Skill 17A OQ-17A-02; FE Credit's product states a minimum-income eligibility rule (≥3M VND/month) [Skill02 S11] | Without it, THIN files cannot pass affordability; if declared income is gamed, the haircut and cap limit the exposure |

---

## Source Index

| # | Source | URL | Used for |
|---|---|---|---|
| D1 | Building Nubank, "How Nubank models risk for smarter, scalable credit limit increases" | https://building.nu.com/how-nubank-models-risk-for-smarter-scalable-credit-limit-increases/ | Survival modelling + risk ranking for limit decisions; feature stores; continuous monitoring (~122M customers) |
| D2 | Building Nubank, "How Nubank uses causality, machine learning and Python to support credit limit increase decisions" | https://building.nu.com/how-nubank-uses-causality-machine-learning-and-python-to-support-credit-limit-increase-decisions/ | Predictive + causal + prioritisation layers for limit decisions |
| D3 | Building Nubank, "Avalanche stack and real-time streaming applications at Nu" | https://building.nubank.com/avalanche-stack-and-real-time-streaming-applications-at-nu/ | Kafka-based real-time event sourcing (illustrative) |
| D4 | RisingWave, "Underwriting in Under a Second: Real-Time Feature Engineering for Lending" (vendor blog, Low) | https://risingwave.com/blog/underwriting-real-time-features-lending-sql/ | Hybrid async-bureau + streaming behavioural features pattern |
| D5 | V2 Solutions, "AI-Native Lending Platforms" (vendor whitepaper, Low) | https://www.v2solutions.com/whitepapers/ai-native-lending-platform-guide/ | Event-driven + feature-store online/offline parity pattern |
| D6 | Baymard Institute, "Payment UX: Research-Backed Standards for Ecommerce Checkouts" | https://baymard.com/blog/payment-ux | Group BNPL with similar payment methods; keep checkout forms short |
| — | `_research-notes-07-13.md` [R#] (80 entries) | local | Precedents, failure modes, lifecycle mechanics, Vietnam enablers |
| — | Upstream: `_requirements.md` [Brief], Skills 02–12, `literature-review.md` [L#] | local | All other claims |

## Skill 22 Resolutions (User decision, Skill 22, 2026-09-30)

| Item | Resolution |
|---|---|
| D-06 design-fix defaults | **Accepted:** F2_thin from one declared income figure with haircut and cap (A-13-12); stacking-fail → REFER if F1 ∈ {A,B} else DECLINE; CONSENTED-PARTNER approvable like BANK-OBSERVED; randomised approval corridor (size and loss budget = HLB parameters) |
| D-09 hosting | **Assumed:** in-country hosting on portable components; HLB Group receives de-identified extracts only |
| D-12 / DD-11 pre-approval display | Kept as designed. **Legal to confirm** (logged risk RK-23) |
| D-13 name | "HLB CreditPulse" confirmed |
| D-01 promoted features | PR-05 (weekly payout-aligned repayment option, GIG/MSME) and PR-06 (collection-conduct standards per the VNBA Code) are now v1. Flow 4 adds them: see stories US-231 to US-236 |
| A-13-1 to A-13-12 | Accepted as stated assumptions; A-13-1 to A-13-4 and A-13-10 to be validated by spikes SP-1/SP-4 |
