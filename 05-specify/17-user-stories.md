# User Stories
**Product:** HLB Point-of-Purchase Credit Decisioning — **HLB CreditPulse** | **Skill:** 17 | **Status:** Done (2026-09-30)
**Depends on:** epic-feature-decomposition.md (Skill 16), solution-design.md (Skill 13), success-metrics.md (Skill 10) | **Feeds into:** nfr.md, data-requirements.md, error-states.md, product-prd.md (Section 2)

> **How this file was produced (Pattern D, parallel by epic).** Three parts were written in parallel and merged below. Each keeps its own story-ID range:
> - **Part A** (US-001–099): EP-0 Engine & CCP; EP-7 Consent, Governance & Oversight.
> - **Part B** (US-100–199): EP-2 Packages; EP-3 In-Session Decision; EP-8 Merchant Integration.
> - **Part C** (US-200–299): EP-1 Pre-approval; EP-4 Reassessment; EP-5 Repeat Offers; EP-6 Referral.
>
> **Every HLB policy value marked `[DECISION REQUIRED]` upstream is written as a named configurable parameter.** Examples: `policy.starter_limit_vnd`, `policy.integrity_floor`, `campaign.holdout_share`. No story invents a number. Skill 22 collects the parameter values.

---

## 1. Post-merge corrections (2026-09-30)

The story agents found five design gaps in Skill 13. They are fixed at source, and the affected ACs are patched:

| Gap | Fix in Skill 13 | Stories patched |
|---|---|---|
| F1 A/B with F3 = CONSENTED-PARTNER (typical Sổ Bán Hàng MSME) fell through the tier policy to DECLINE (OQ-17A-01, OQ-17C-01) | CONSENTED-PARTNER added to the approve rule, §1.1 | US-014 AC 7; US-200 AC 4 |
| No income input for THIN new-to-bank applicants, so every one was declined on affordability (OQ-17A-02) | F2_thin from one self-declared monthly income figure, with a haircut and cap (A-13-12, to confirm in Skill 22) | US-009 AC 4/4b; US-111 (form now includes the income figure) |
| Retention recommendations had no random holdout, contrary to C4/FM-5 (OQ-17C-15) | Flow 5 step 3 holdout | US-240 AC 1b |
| Outcome when the CIC stacking check fails at checkout was undefined (OQ-17A, OQ-17B) | REFER if F1 ∈ {A,B}, else DECLINE (EXISTING_OBLIGATIONS); default to confirm in Skill 22 | Covered by the §1.1 rule; stories reference the policy layer |
| Web checkout cannot read the chip-CCCD over NFC (Skill 14 B-06) | Web-to-app hand-off, U-01 | Applies to US-118, US-141, US-142 (hand-off is part of AP-02) |

Every other gap the agents raised (for example refunds/returns after booking, merchant settlement, repayment method when CASA is declined, QR customers without HLB Connect, "recent hardship" and "rising obligations" definitions, graduation offer validity, what the passport unlocks) is listed in the part-level open items. Skill 20 (error states) and Skill 22 (decisions) pick them up.

---

## 2. Story Count Summary

```
Total Epics:           9  (EP-0 … EP-8)
Total v1 Features:     29 Must + 8 Should with stories (PF-01 platform foundation and HUB-01 hub shell are
                          engineering enablers from Skill 14; they are covered by part A/B stories plus the DoD)
Total v1 Stories:      117  (Part A 36 · Part B 38 · Part C 43)
  Must-have:           ≈ 106   Should-have: ≈ 11
Total ACs:             ≈ 528  (Part A 166 · Part B 171 · Part C 191, after the post-merge patches)
Size distribution:     S 49 · M 55 · L 13 · XL 0
Estimated story points: [DECISION REQUIRED — no HLB points scale or team velocity exists]
Estimated sprints:      [DECISION REQUIRED — needs team size and velocity.] Engineering effort envelope from
                          Skill 14 §7: ≈ 283–442 person-weeks (Must, incl. platform) + 68–102 (Should)
```

---

## 3. Story Map Grid (summary; full per-part grids are in the part sections below)

| Epic | Features → story ranges |
|---|---|
| EP-0 Engine & CCP | E-02, E-05, E-06, E-07, E-08, E-03 (Should) → US-001 … US-020 range (part A) |
| EP-7 Consent, Governance & Oversight | E-01, E-09, OP-01, GV-01, OP-02 (Should) → US-021 … US-036 range (part A) |
| EP-2 Packages & Transparent Cost | CO-01, CO-02, CO-03 (Should) → US-100 – US-109 |
| EP-3 In-Session Decision & Identity ★ | AP-01 – AP-07 → US-110 – US-131 |
| EP-8 Merchant & Partner Integration | MX-01 → US-140 – US-145 |
| EP-1 Pre-approval Targeting | AW-01, AW-02, AW-04, AW-03 / AW-05 (Should) → US-200 – US-212 |
| EP-4 Reassessment & Graduation | PR-01 – PR-04 → US-220 – US-230 |
| EP-5 Repeat Offers & Anti-Stacking | RT-01, RT-02, RT-03 (Should) → US-240 – US-247 |
| EP-6 Referral & Merchant Flywheel | AD-01, AD-02 / AD-03 (Should) → US-260 – US-270 |

(Exact story-to-feature mapping is in each part's own map and grid.)

---

## 4. Cross-part Dependency Graph (critical path)

```
PF-01 platform ─┐
E-01 consent ledger (A) ─→ E-02 ingestion (A) ─→ E-05 CCP + API (A) ─→ E-06 scoring (A) ─→ E-07 policy (A) ─→ E-09 reason codes (A)
                                                                   │
                     ┌─────────────────────────────────────────────┘
                     ▼
   MX-01 checkout session API US-140 (B) ─→ CO-01/CO-02 packages US-100–108 (B) ─→ AP-02 eKYC US-118/119 (B)
        ─→ AP-01 decision US-110/112/113 (B) ─→ AP-05 starter limit US-128 (B) ─→ e-contract US-114 (B)   ← AHA
        └→ AP-04 refer US-124–127 (B) ─→ OP-01 workbench (A)
   E-08 refresh (A) ─→ PR-01 re-score (C) ─→ PR-03 graduation (C); PR-02 early-warning (C) ─→ suppression flags → AW (C), RT (C)
   E-05 CCP (A) + E-01 (A) ─→ AW-01/02/04 (C) ─→ RT-01/02 (C) ─→ AD-01 (C)
```

---

## 5. Definition of Done (applies to all 117 stories)

- [ ] All ACs pass (automated where feasible). Every negative/edge AC has a test.
- [ ] Unit tests written and passing. Integration tests passing against the stubbed CIC / C06-VNeID / core-banking / merchant sandboxes.
- [ ] Code reviewed by one peer. Changes to the **policy layer or model code** also need a credit-risk reviewer (four-eyes).
- [ ] **Consent purpose check** covered by a test for every new read of the CCP (KR 4.1).
- [ ] **Reason codes and decision-log entry** produced for every decision path touched (KR 4.1; AI Law logging).
- [ ] No hard-coded policy values. Every threshold is read from versioned policy config.
- [ ] Error states handled per Skill 20; degraded-mode behaviour per Skill 18 §5.2.
- [ ] No new accessibility violations (WCAG 2.1 AA) on customer and officer screens; Vietnamese copy reviewed.
- [ ] Analytics events firing (Skill 19 event schema) for every KPI the story instruments.
- [ ] Security: OWASP ASVS level per Skill 18 §2; no PII in logs; secrets not in code.
- [ ] Feature flag configured for anything customer-facing or model-driven (champion fallback possible).
- [ ] Documentation updated (API spec, model card where relevant, runbook).

(Part B and part C add stage-specific DoD items. They are in their sections below and also apply.)

---

## 6. Later Story Parking Lot (consolidated)

| Parked feature | Parking-lot ID | Stub location | v1 minimum written instead |
|---|---|---|---|
| E-04 telco / e-wallet / platform data | PL-01 | Part A | — |
| GV-02 sandbox application | PL-02 | Part A | — |
| MX-02 merchant portal | PL-03 | Part B | 0% promos configured by HLB staff (OQ-16-02) |
| AP-08 eKYC fallback flows | PL-04 | Part B | US-120: 3 attempts → REFER (identity review) |
| AP-09 path-to-yes explanation | PL-05 | Part B | US-115: top reason codes + generic path |
| PR-05 flexible repayment | ~~PL-06~~ **Promoted** | Addendum US-231–US-233 | — |
| PR-06 collection-conduct standards | ~~PL-07~~ **Promoted** | Addendum US-234–US-236 | — |
| PR-07 literacy nudges | PL-08 | Part C | — |
| RT-04 frequency caps | PL-09 | Part C | US-240 dismissal suppression |

---

## 7. Open items

Consolidated from the parts. Full text is in each part's section.
- **Part A:** OQ-17A-01 … 13. 01 and 02 are resolved.
- **Part B:** OQ-17B-01 … 30.
- **Part C:** OQ-17C-01 … 19. 01 and 15 are resolved.

Skill 22 triages them.

---
---



---

# ═══ PART A ═══

# User Stories & Acceptance Criteria — Part A (EPIC 0 Engine & CCP · EPIC 7 Consent, Governance & Human Oversight)
**Product:** HLB Point-of-Purchase Credit Decisioning — **HLB CreditPulse** | **Skill:** 17 (part A of 3) | **Status:** Done (2026-09-30)
**Depends on:** 16-epic-feature-decomposition.md, 13-solution-design.md, 10-success-metrics.md, 08-v1-product-scoping.md, 15-user-journey-map.md, _context.md | **Feeds into:** 17-user-stories.md (consolidation), 18-nfr.md, 19-data-requirements.md, 20-error-states.md, product-prd.md

> **How to read this part.**
> - **Story IDs:** US-001 to US-099 are reserved for this part. Part B (EP-2, EP-3, EP-8) uses US-100 to US-199. Part C (EP-1, EP-4, EP-5, EP-6) uses US-200 to US-299. Stories in other parts are referenced **by feature code** (for example "AP-01 (part B)"), because those parts are being written in parallel.
> - **Labels:** `[v1]` = Skill 08 Must → story priority **Must-have**. `[v1-Should]` = Skill 08 Should → **Should-have**. `[Later]` = parked (listed at the end, no ACs).
> - **Sizes (S/M/L/XL)** are story-level judgments inside the Skill 16 feature complexity band. Skill 14 (Technical Feasibility) is still a stub, so every size is provisional (OQ-16-01 applies).
> - **No invented numbers.** Concrete values come only from Skill 13 or Skill 10. Examples: the tier rules; tenors {3, 6, 9, 12, 18, 24}; PSI 0.10 / 0.25; the approval-rate ratio 0.8; KR 1.4's 2 business hours; the 30-day cached CIC pull; 100% reason codes and consent-matched reads. Every HLB policy value marked `[DECISION REQUIRED]` in Skill 13 (OQ-13-02) or Skill 10 is written as a **named configurable parameter** (`policy.*`, `governance.*`, `ops.*` …). The **Parameter Register** below lists each one. A deployment with a required parameter unset must fail its configuration validation; it must never fall back to a silent default.
> - **Personas used:**
>   - **Existing HLB customer (Lane A):** existing HLB account-holder with no PoP history (SAL-N anchor, D-08-1).
>   - **Anh Hùng:** gig driver, new to bank (Lane B).
>   - **Chị Lan:** HLB credit-risk / underwriting lead (officer).
>   - **HLB model-risk officer.**
>   - **HLB compliance officer / DPO.**
>   - **HLB data engineer.**
>   - **System stories** ("As the Checkout Decision service, I need…") are used only where a genuine service contract is needed.
> - **Consent purposes (E-01, C1):** **SC** = scoring, **MK** = marketing/targeting, **RF** = referral.

---

## Parameter Register (named parameters used in ACs; values are HLB decisions or Skill 14 outputs)

| Parameter | Meaning | Source of the gap |
|---|---|---|
| `policy.integrity_floor` | F5 threshold below which the tier policy returns REFER/DECLINE | OQ-13-02 |
| `policy.dsr_cap` | Debt-service-ratio cap used in F2 | OQ-13-02 |
| `policy.living_cost_buffer` | Living-cost buffer deducted in F2 (VND/month) | OQ-13-02 |
| `policy.starter_limit` | Starter limit for THIN files (AP-05) | OQ-13-02 |
| `policy.limit_by_grade[A..E]` | "Policy limit for grade" in the Skill 13 limit formula | OQ-13-02 (tier thresholds) |
| `policy.tenor_adjusted_factor[tenor]` | "Tenor-adjusted factor" in the Skill 13 limit formula | Named in Skill 13 §1.1; no value given |
| `policy.min_package_instalment` | Minimum package instalment used when no basket exists (batch pre-approval, AW-01) | Skill 13 §1.1/Flow 1 names it; no value or derivation given |
| `policy.tier_rule.consented_partner_ab` | Tier for F1 ∈ {A,B} with F3 = CONSENTED-PARTNER | Design gap OQ-17A-01 |
| `policy.thin_file_no_income_rule` | Treatment of an applicant with no income evidence (F2 cannot be estimated) | Design gap OQ-17A-02 |
| `policy.stacking_fail_outcome` | Tier returned at checkout when the RT-02 stacking check fails | Design gap OQ-17A-05 |
| `policy.cic_unavailable_starter_cap` | Lower starter-limit cap when CIC is unavailable (used by AP-07, part B; stored in US-017) | Skill 13 Flow 3 `[DECISION REQUIRED]` |
| `ccp.bank_observed_min_months` | Minimum observed CASA history for F3 = BANK-OBSERVED | Design gap OQ-17A-03 |
| `ccp.f3_precedence` | Precedence when several F3 categories could apply | Design gap OQ-17A-03 |
| `signals.s1_cv_window_months` | Window for the S1 coefficient of variation | Skill 13 gives the 6/12-month counts only |
| `signals.s11_kyc_change_window_days` | Look-back window for "sudden KYC change before an application" | Skill 13 S11 names it; no window given |
| `governance.challenger_split_pct` | Share of eligible decisions routed to the GBM challenger | Skill 13 DD-02: "randomised split", no share given |
| `governance.challenger_mob3_tolerance` | Tolerance in "Challenger MOB3 30+ > champion + tolerance" | Skill 10 §4 |
| `governance.fairness_reference_group` | Reference group for each fairness ratio (gender, age band, region) | Skill 10 G-5 does not name it |
| `governance.fairness_mob3_ratio_alert` | Alert trigger for the MOB3 30+ fairness ratio | Skill 10 G-5 gives 0.8 for approval rate only |
| `governance.champion_drift_action` | Action when the **champion** itself exceeds PSI 0.25 (no fallback model exists) | Design gap OQ-17A-07 |
| `guardrail.mob3_30_ceiling[segment]` | G-1 ceiling per segment and cohort | Skill 10 G-1 `[DECISION REQUIRED]` |
| `guardrail.consent_withdrawal_spike` | Month-on-month spike that triggers the G-7 alert | Skill 10 G-7 names no threshold |
| `ops.business_calendar` | HLB business hours and holidays for the refer-SLA clock | Not given |
| `ops.refer_sla_business_hours` | Refer SLA; the design target is 2 (KR 1.4, A-10-4) | Skill 10 KR 1.4 |
| `reason_codes.display_count` | How many top reason codes the customer sees | Skill 13 says "top reason codes" only |
| `privacy.sc_withdrawal_policy` | What happens to stored CCP history and live loans when SC is withdrawn | Design gap OQ-17A-04 |
| `audit.retention_period` | Retention period for the decision log and the consent ledger | Not given |
| `ingest.max_staleness[source]`, `refresh.event_to_ccp_latency` | Freshness and latency budgets | Skill 14 pending |
| `consent.self_view_purpose` | Purpose used when a customer views their own limit in the hub (design: SC, per DD-11) | OQ-13-01 (legal) |
| `governance.model_card_template` | Required model-registry / documentation fields | OQ-17A-11 |
| `pause.segment_auto_approval[segment]`, `pause.graduation_offers`, `pause.retention_reoffers` | Guardrail pause switches (Skill 10 §3) | Switches, not values; set by breach |
| `feature.open_api_enabled` | Gate for E-03 until HLB is Circular 64 ready | Skill 08 condition |

---

# EPIC 0: CreditPulse Engine & Customer Credit Profile (cross-cutting)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 0.1 [E-02]: Phase-1 data ingestion
Epic: EP-0 CreditPulse Engine & CCP | Label: [v1] | Complexity: L
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Story US-001: As an **HLB data engineer**, I want **nightly batch ingestion of the Phase-1 bank-held sources into the offline feature store**, so that **the engine is built only on legally ready data that HLB already holds (SD-7, C6).** The sources are CASA and salary inflows, HLB loans and cards, HLB Connect usage, and the Sổ Bán Hàng daily ledger.
Size: L
Priority: Must-have
Dependencies: US-023 (source-level consent records)

  AC 1: Given the nightly batch window opens,
        when the ingestion job runs,
        then it loads each Phase-1 source: core-banking CASA, HLB loans/cards, HLB Connect usage and Sổ Bán Hàng.
        And for each source it writes a load record: source name, as-of timestamp, row count and status (SUCCESS / FAILED).

  AC 2: Given a feature value is written to the offline store,
        when a training or back-test job reads it "as of" date T,
        then only records with an event timestamp ≤ T are returned (point-in-time correctness, Skill 13 §4 component 3).

  AC 3: Given a Sổ Bán Hàng merchant has **no active merchant-consent record** in the consent ledger,
        when the Sổ Bán Hàng batch runs,
        then that merchant's ledger rows are not loaded,
        and a skip count is written to the load record.

  AC 4 (negative — exclusions): Given the ingestion schema registry,
        when anyone attempts to register a field sourced from phone contacts, the photo gallery or social media,
        then registration is rejected with error "EXCLUDED_SOURCE (X-01)".
        No such field can reach the feature store (Skill 13 §6).

  AC 5 (edge — source failure): Given one source load fails (for example the HLB Connect usage extract is missing),
        when the batch completes,
        then the previous snapshot of that source is kept and flagged `stale=true` with its age,
        an alert goes to the data-engineering on-call, and **no feature derived from that source is zero-filled**.
        Consumers see the staleness flag.
        The maximum acceptable age is `ingest.max_staleness[source]`, pending Skill 14.

Story US-002: As an **HLB data engineer**, I want **event-stream ingestion of CASA transactions, PoP repayments and application events into the online feature store**, so that **checkout-time decisions and profile refreshes use current data (DF-2, DF-3).**
Size: L
Priority: Must-have
Dependencies: US-001

  AC 1: Given a CASA credit, a PoP repayment or an application event is published on the event stream,
        when the ingestion consumer processes it,
        then the affected online features are updated,
        and the event is available to the refresh loop (US-018) within `refresh.event_to_ccp_latency` (Skill 14 pending).

  AC 2: Given the same event is delivered twice (same event ID),
        when it is processed,
        then the online store is updated once only (idempotent),
        and the duplicate is counted in a `duplicate_events` metric.

  AC 3 (edge — out of order): Given an event with an older event timestamp arrives after a newer one for the same account,
        when it is processed,
        then features are computed in event-time order, not arrival order.
        The resulting online value equals the value the offline store computes for the same as-of time.

  AC 4 (parity check): Given a daily parity job samples customers,
        when it compares the online and offline values of the same feature at the same as-of time,
        then any mismatch rate above 0 is reported per feature,
        and features with mismatches are listed on the governance console (US-034).

Story US-003: As **Chị Lan, HLB credit-risk / underwriting lead**, I want **CASA records engineered into the behavioural signals S1–S8 exactly as defined in the Skill 13 signal catalogue**, so that **salaried-no-credit, gig and MSME customers are assessed on observed cash-flow evidence instead of payslips (R-1).**
Size: L
Priority: Must-have
Dependencies: US-001, US-002

  AC 1 (S1): Given a customer whose CASA shows inflows in 5 of the last 6 months and 10 of the last 12 months,
        when S1 is computed,
        then `s1_months_with_inflow_6m = 5`, `s1_months_with_inflow_12m = 10`,
        and `s1_inflow_cv` = standard deviation ÷ mean of monthly inflow totals over `signals.s1_cv_window_months`.

  AC 2 (S3): Given a customer's main inflow date is identified,
        when S3 is computed,
        then it equals the minimum end-of-day balance in the **5 days before** the main inflow, divided by the average monthly outflow.

  AC 3 (S5 — consent): Given an MSME customer with an active Sổ Bán Hàng merchant consent,
        when S5 is computed,
        then it combines four elements: QR/transfer receipts, Sổ Bán Hàng daily sales, e-invoice totals, and refund ratio and seasonality.
        Given the merchant consent is **absent or withdrawn**,
        then S5 uses CASA QR/transfer receipts only,
        and `s5_sources` omits Sổ Bán Hàng.

  AC 4 (edge — assumption-dependent signals): S2 (salary-day stability), S4 (platform-payout signature), S6 (bill-pay punctuality) and S7 (wallet top-up rhythm) rely on assumptions A-13-1 to A-13-3. Given core banking does not supply the counterparty metadata these signals need,
        when the signal job runs,
        then the signal is written as `null`, with `null_reason = "METADATA_UNAVAILABLE"`.
        It is **never** written as 0,
        and the null rate per signal is reported for OQ-13-04.

  AC 5 (S8): Given recurring transfers from CASA to a savings or term-deposit account,
        when S8 is computed,
        then it outputs two values: whether a recurring transfer exists, and the share of inflow saved over the S1 window.

Story US-004: As **Chị Lan**, I want **credit-conduct, relationship, checkout-footprint, CIC and PoP-repayment signals (S9–S11, S13–S15) engineered and S12 tagged "propensity only"**, so that **Cohort A is assessed on its strongest internal evidence, and thin files get the checkout footprint that complements the bureau [R80].**
Size: L
Priority: Must-have
Dependencies: US-001, US-002

  AC 1 (S14 CIC): Given a CIC pull is made,
        when S14 is written,
        then it stores existing obligations, delinquency, number of lenders and recent enquiries.
        It also stores the **pull timestamp**, so that AP-07 (part B) can test the "cached CIC pull under 30 days old" rule.

  AC 2 (S11 integrity flag): Given the customer's KYC phone or address changed within `signals.s11_kyc_change_window_days` before an application,
        when S11 is computed for that application,
        then `s11_recent_kyc_change = true` is emitted as an input to F5 (via AP-03, part B).

  AC 3 (S13 scope): Given a Lane B applicant at checkout,
        when S13 is captured,
        then it contains only basket category, down-payment chosen, device type, time of day and email/phone validity.
        Any payload field outside this list is dropped and counted (`s13_dropped_fields`).

  AC 4 (S15): Given a PoP repayment event,
        when S15 is updated,
        then days early/late, partial-payment indicator and payment-channel change are recorded against the loan.

  AC 5 (negative — S12): Given S12 (HLB Connect digital engagement) is registered in the feature store,
        when its metadata is read,
        then `permitted_consumers = [F4]`,
        and US-005 enforcement prevents it from reaching any F1, F2 or F5 model (DD-07).

Story US-005: As an **HLB model-risk officer**, I want **the feature store to enforce which fields may be model inputs**, so that **protected attributes, the "referred" flag and digital engagement can never influence risk (Skill 13 §1.2 exclusions, §6, DD-07, DD-08).**
Size: M
Priority: Must-have
Dependencies: US-003, US-004

  AC 1: Given the feature registry,
        when a field is registered,
        then it must declare a namespace: `model_input`, `propensity_only` or `fairness_monitoring_only`.
        Gender, ethnicity, religion, age band and region can only be registered in `fairness_monitoring_only`.

  AC 2 (negative): Given a training or scoring pipeline for F1, F2 or F5,
        when its feature list includes any field outside `model_input`, including gender, ethnicity, religion, age band, region, S12, or `referred_by_customer` / any referral attribute,
        then the pipeline build fails with "PROHIBITED_MODEL_INPUT",
        and the attempt is written to the governance audit log.

  AC 3: Given the F4 propensity pipeline,
        when it requests S12,
        then the request succeeds (S12 is `propensity_only`).
        Given the same pipeline requests a `fairness_monitoring_only` field,
        then it is refused.

  AC 4 (verification): Given a model version is submitted to the registry (US-030),
        when registration runs,
        then an automated check compares the model's declared input list with the namespace registry.
        Registration is blocked if any prohibited input is present.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 0.2 [E-05]: Customer Credit Profile generator & CCP API
Epic: EP-0 CreditPulse Engine & CCP | Label: [v1] | Complexity: L
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Story US-006: As the **Checkout Decision service** (AP-01, part B), I need **every CCP to carry all six fields in one versioned schema**, so that **every stage consumes the same profile, with consistent explanations (SD-4, DD-01).**
Size: M
Priority: Must-have
Dependencies: US-007, US-010, US-013, US-014

  AC 1 (schema): Given a CCP is written,
        when it is validated,
        then it contains all six fields:
        - F1: grade ∈ {A,B,C,D,E} + PD band + model version.
        - F2: VND/month ≥ 0 + income estimate + confidence interval.
        - F3: ∈ {BUREAU+BANK, BANK-OBSERVED, CONSENTED-PARTNER, THIN} + list of sources used.
        - F4: suitability flag (v1) + top purchase categories.
        - F5: integer 0–100 + flags.
        - F6: ∈ {PRE-APPROVED (limit L), INSTANT-APPROVE, REFER, DECLINE} + reason codes + path message.
        A CCP missing any field is rejected and not stored.

  AC 2 (versioning): Given a customer has CCP version n,
        when any field changes,
        then version n+1 is written. It records the timestamp, the trigger type (for example `REPAYMENT_EVENT`, `MONTHLY_BATCH`, `CONSENT_CHANGE`, `APPLICATION`), the input-snapshot hash and the model/policy versions used.
        Version n stays retrievable, unchanged.

  AC 3 (F6 recompute): Given F1, F2, F3 or F5 changes in a new version,
        when the version is written,
        then F6 is re-evaluated by the policy layer (US-014) in the same write.
        Given only F4 changes,
        then F6 is carried over unchanged.

  AC 4 (edge — F2 floor): Given obligations and buffer exceed the income estimate,
        when F2 is computed,
        then F2 = 0, never negative.

Story US-007: As **Chị Lan**, I want **F2 (Affordability Headroom) and F3 (Evidence Confidence) derived by transparent rules**, so that **the affordability boundary stays explainable and the evidence behind each grade is visible (DD-03).**
Size: M
Priority: Must-have
Dependencies: US-003, US-004, US-012, US-023

  AC 1 (F2): Given a stable-income estimate I (US-012), existing obligations O_CIC (S14) and O_HLB (S10),
        when F2 is computed,
        then F2 = max(0, (I − O_CIC − O_HLB − `policy.living_cost_buffer`) × `policy.dsr_cap`).
        The formula version is written to the CCP.
        *Skill 13's literal wording is kept here. Operator precedence and whether I is the point estimate or the lower confidence bound are confirmed under OQ-17A-06.*

  AC 2 (F3 — unambiguous cases): Given the sources and consents present,
        when F3 is computed,
        then:
        - (a) a CIC file plus HLB CASA history of ≥ `ccp.bank_observed_min_months` → BUREAU+BANK;
        - (b) HLB CASA history of ≥ `ccp.bank_observed_min_months` without a CIC file → BANK-OBSERVED;
        - (c) the primary evidence is consented partner data (Sổ Bán Hàng) → CONSENTED-PARTNER;
        - (d) none of the above → THIN.
        When several cases apply, `ccp.f3_precedence` decides; there is no hard-coded default (OQ-17A-03).

  AC 3 (sources list): Given F3 is computed,
        when the CCP is written,
        then `f3_sources` lists **only** sources whose consent is active at compute time.
        Given a source's consent is withdrawn,
        then the next computation excludes it (see US-018 AC 3).

  AC 4 (instrumentation — G-4): Given any CCP version is written,
        when F2 is set,
        then `f2_value`, `income_estimate` and `income_ci` are written to the analytical history.
        The G-4 breach check (US-015 AC 4) can then compare every booked instalment with the F2 in force at booking.

  AC 5 (negative — CIC obligations unknown): Given `cic_status = UNAVAILABLE` (US-009 AC 3) and no cached CIC pull,
        when F2 is computed,
        then O_CIC is **not** treated as 0.
        F2 is written with `f2_status = CIC_OBLIGATIONS_UNKNOWN`, and the AP-07 fallback rules (part B) govern the tier.

Story US-008: As the **Checkout Decision service**, I need **a CCP read API that checks the declared consent purpose on every read**, so that **every profile use has a matching consent in the ledger (C1, KR 4.1).**
Size: M
Priority: Must-have
Dependencies: US-006, US-023

  AC 1: Given a customer with active SC consent,
        when the Checkout Decision service calls `GET /ccp/{customer}` with `purpose=SC`,
        then the current CCP version is returned.
        It contains only the fields on that caller's field allowlist (for example, the Referral Ledger service receives only F1 and F6, per Skill 13 §1.3).

  AC 2 (negative — purpose missing): Given the customer has **no active consent** for the declared purpose (for example MK withdrawn yesterday),
        when the Campaign service calls with `purpose=MK`,
        then the API returns "CONSENT_NOT_GRANTED" with **no CCP fields**, and the denial is logged.

  AC 3 (negative — no purpose): Given a request with no `purpose` parameter, or a purpose value other than SC, MK or RF,
        when it is received,
        then it is rejected with "PURPOSE_REQUIRED" and no fields are returned.

  AC 4 (edge — self-view): Given a Lane A customer opens the "Pay Later → My limit" screen themselves,
        when HLB Connect reads the CCP,
        then it uses the purpose configured in `consent.self_view_purpose`.
        The design value is SC, as a service display (DD-11), and it stays pending legal confirmation under OQ-13-01.

  AC 5 (instrumentation — KR 4.1): Given any read, allowed or denied,
        when it completes,
        then a `ccp_read` event is written. It records caller service, customer ID, purpose, consent-ledger entry ID matched (or none), CCP version, fields returned and timestamp.
        The daily KR 4.1 job (US-023 AC 4) uses these events.

  AC 6 (edge — consent service unreachable): Given the consent service cannot be reached,
        when a read is attempted,
        then the read is **denied (fail closed)** with "CONSENT_CHECK_UNAVAILABLE" and an alert fires.
        The customer-facing handling at checkout is defined by AP-01 error states (part B).

Story US-009: As the **Checkout Decision service**, I need **to request an on-demand CCP build for a new-to-bank (Lane B) applicant**, so that **Anh Hùng can be decided within the checkout session (DF-2, R-2).**
Size: M
Priority: Must-have
Dependencies: US-006, US-007, US-008, US-021; cross-part: AP-02 eKYC result, AP-03 integrity (part B)

  AC 1: Given Anh Hùng has given SC consent, the eKYC result is available and the CIC pull has returned,
        when the service requests `POST /ccp/build` with `purpose=SC`,
        then CCP version 1 is created with all six fields in the US-006 schema.
        For an applicant with no HLB history and no CIC file, F3 = THIN.

  AC 2 (negative — no SC consent): Given the applicant has **not** given SC consent,
        when a build is requested,
        then no CCP is built, no CIC pull is triggered,
        and the API returns "SC_CONSENT_REQUIRED".

  AC 3 (edge — CIC unavailable): Given the CIC pull times out or CIC is unavailable,
        when the CCP is built,
        then S14 is marked `cic_status = UNAVAILABLE`, not "no file".
        F3 is computed without CIC, and the CCP carries the status so the AP-07 fallback rules (part B) can apply.

  AC 4 (edge — no observed income; Skill 13 F2_thin fix 2026-09-30, OQ-17A-02 resolved pending Skill 22 A-13-12):
        Given there is no bank-observed or consented income evidence, and the applicant has entered a declared monthly income (US-111),
        when F2 is computed,
        then F2 = min(declared_monthly_income × `policy.declared_income_haircut`, `policy.thin_headroom_cap_vnd`)
             − CIC monthly obligations − `policy.living_cost_buffer_vnd`,
        and `f2_status = DECLARED_INCOME`.
  AC 4b (negative): Given the declared income is missing or zero,
        when F2 is computed,
        then F2 = 0 and the tier is DECLINE with reason AFFORDABILITY. The engine never fabricates an income value.

  AC 5 (instrumentation — KR 1.2): Given a build request,
        when it completes or fails,
        then `ccp_build_started` and `ccp_build_completed` events are emitted with timestamps and the application ID.
        The engine's share of the p50 ≤ 3 min / p90 ≤ 10 min budget can then be measured.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 0.3 [E-06]: Hybrid risk scoring (scorecard champion + GBM challenger)
Epic: EP-0 CreditPulse Engine & CCP | Label: [v1] | Complexity: XL
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Story US-010: As **Chị Lan**, I want **a per-segment logistic scorecard champion served behind a single versioned scoring API**, so that **every F1 grade is explainable factor by factor and serves as the baseline the AI must beat (SD-3, DD-02).**
Size: L
Priority: Must-have
Dependencies: US-003, US-004, US-005, US-030

  AC 1: Given an applicant in segment SAL-C, SAL-N, GIG, MSME or FTB,
        when the scoring API is called,
        then the champion scorecard for that segment and evidence tier returns grade ∈ {A,B,C,D,E}, the PD band, the model version ID and the ranked factor contributions.
        The factor contributions feed the reason codes (US-024).

  AC 2 (negative — unregistered model): Given a model version that is not in the registry with status `APPROVED` (US-030),
        when the scoring API is asked to load it,
        then loading is refused, and the currently approved version keeps serving.

  AC 3 (edge — missing optional signal): Given an assumption-dependent signal is `null` (US-003 AC 4, for example S2 unavailable),
        when the scorecard scores,
        then the scorecard applies the missing-value treatment declared in its registry model card, and the score is still produced.
        The scorecard never reads the null as 0.

  AC 4 (determinism): Given the same input snapshot and the same model version,
        when scored twice,
        then the grade, PD band and factor contributions are identical.

Story US-011: As an **HLB model-risk officer**, I want **a gradient-boosted challenger scored on a randomised split alongside the champion**, so that **the AI's incremental value for thin files is measured per segment rather than asserted (KR 2.1, Brief pp.10–11).**
Size: XL
Priority: Must-have
Dependencies: US-010, US-030, US-033

  AC 1 (split): Given an eligible decision,
        when it is scored,
        then it is assigned to `CHAMPION` or `CHALLENGER` by random draw at share `governance.challenger_split_pct`.
        The arm is written to the decision log (US-033).
        For challenger-arm decisions the champion score is also computed and logged, so both are comparable on the same case.

  AC 2 (instrumentation — KR 2.1): Given decisions have accumulated,
        when the weekly job runs,
        then it reports approval rate by arm × segment (SAL-C, SAL-N, GIG, MSME, FTB), with thin files (GIG + MSME + FTB) shown separately.
        A monthly job reports MOB3 30+ by arm × segment.

  AC 3 (negative — challenger loses): Given challenger MOB3 30+ for a segment exceeds champion MOB3 30+ + `governance.challenger_mob3_tolerance`,
        when the monthly job runs,
        then an alert goes to Credit risk (Skill 10 §4).
        The segment's challenger share can then be set to 0% through the governance console (US-030 AC 3).

  AC 4 (edge — drift fallback): Given the challenger's score PSI exceeds 0.25 (US-031),
        when the alert fires,
        then all traffic routes to the champion automatically (DD-13).
        The routing change is logged with the triggering PSI value.

  AC 5 (negative — inputs): Given the challenger's feature list,
        when it is registered,
        then the same namespace check as the champion applies (US-005).
        A challenger using a prohibited input cannot be served.

Story US-012: As **Chị Lan**, I want **an ML stable-income estimator that reads irregular inflows and outputs a monthly income estimate with a confidence interval**, so that **gig and MSME applicants get an affordability headroom without a payslip, while the limit itself stays rule-based (DD-03).**
Size: L
Priority: Must-have
Dependencies: US-003, US-030

  AC 1: Given a customer with CASA inflow signals (S1, S2, S4, S5, S8, where available),
        when the estimator runs,
        then it returns `income_estimate` (VND/month), `income_ci_low`, `income_ci_high` and the model version.

  AC 2 (negative — scope): Given the estimator's output contract,
        when inspected,
        then it contains **no** limit, instalment or tier field.
        Only the rules in US-007 and US-015 turn income into headroom and limits (DD-03).

  AC 3 (edge — no inflow evidence): Given a customer with no bank-observed or consented inflows (typical for Lane B before S16),
        when the estimator is called,
        then it returns `null` with `reason = NO_INCOME_EVIDENCE`. It never returns a population default.

  AC 4 (monitoring): Given the estimator is live,
        when the weekly drift job runs (US-031),
        then PSI is computed on the estimator's output distribution as well as on the F1 score.

Story US-013: As the **Checkout Decision service**, I need **the integrity anomaly model served through the same scoring API**, so that **AP-03 (part B) can combine its output into the F5 score (0–100) with flags (R-3, G-2).**
Size: M
Priority: Must-have
Dependencies: US-004, US-030; cross-part: AP-03 (part B) consumes the output

  AC 1: Given an application with its eKYC result, device and velocity signals, CIC identity-match result and S11,
        when the anomaly model is called,
        then it returns an anomaly score, the contributing signal names and the model version, within the same scoring API call pattern as US-010.

  AC 2 (negative — inputs): Given the anomaly model's feature list,
        when registered,
        then it must not include protected attributes, S12 or referral attributes (US-005).

  AC 3 (edge — referral abuse): Given an application arrives through an invite link and shares a device or identity attribute with the inviter (self-referral pattern, Skill 13 Flow 6),
        when scored,
        then the output includes the flag `REFERRAL_IDENTITY_OVERLAP`.
        AD-01 (part C) consumes this flag to withhold rewards.

  AC 4 (instrumentation — G-2): Given an application later confirmed as fraud by the fraud team (US-029),
        when the label is recorded,
        then it is joined back to the anomaly score at decision time, so that the model's contribution to G-2 (confirmed fraud per 1,000 approved loans) can be evaluated.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 0.4 [E-07]: Decision-tier policy layer + risk-tier rate grid
Epic: EP-0 CreditPulse Engine & CCP | Label: [v1] | Complexity: M
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Story US-014: As the **Checkout Decision service**, I need **the Policy & Decision service to evaluate the Skill 13 §1.1 tier rules deterministically and in order over F1–F5**, so that **every tier is explainable by construction, and HLB's risk appetite lives in one place.**
Size: M
Priority: Must-have
Dependencies: US-007, US-010, US-013, US-017

  AC 1 (rule 1 — integrity): Given F5 < `policy.integrity_floor`,
        when the policy is evaluated,
        then tier = REFER with reason "identity review".
        If the integrity flags include a **confirmed** identity mismatch, tier = DECLINE, and a `fraud_event` is logged for the fraud team (G-2).
        No later rule is evaluated.

  AC 2 (rule 2 — affordability): Given F5 ≥ `policy.integrity_floor`, and F2 < the minimum instalment of the cheapest package,
        when the policy is evaluated,
        then tier = DECLINE, with reason "affordability" and path "smaller basket / larger down-payment".
        The minimum instalment comes from CO-01 (part B) at checkout, or from `policy.min_package_instalment` in batch pre-approval.

  AC 3 (rule 3 — strong evidence): Given F1 ∈ {A,B} and F3 ∈ {BUREAU+BANK, BANK-OBSERVED},
        when evaluated in **batch context** (Cohort A),
        then tier = PRE-APPROVED with limit L (US-015).
        When evaluated in **checkout context**, then tier = INSTANT-APPROVE.

  AC 4 (rule 4 — thin file): Given F1 ∈ {A,B,C} and F3 = THIN,
        when evaluated,
        then tier = INSTANT-APPROVE at starter limit.
        The limit is min(`policy.starter_limit`, the US-015 limit formula).

  AC 5 (rule 5 — refer): Given F1 = C with F3 ≠ THIN, or F1 = D with F3 ≠ THIN,
        when evaluated,
        then tier = REFER, and the response includes an **immediate counter-offer** when a package with instalment ≤ F2 exists (built by AP-04 / CO-01, part B).
        Otherwise it has `counter_offer = none`.

  AC 6 (rule 6 — else): Given any other combination (for example F1 = E, or F1 = D with F3 = THIN),
        when evaluated,
        then tier = DECLINE, with reason codes and a path message.

  AC 7 (CONSENTED-PARTNER; Skill 13 fixed 2026-09-30, OQ-17A-01 resolved): Given F1 ∈ {A,B} and F3 = CONSENTED-PARTNER
        (for example a Sổ Bán Hàng merchant), and the integrity, affordability and stacking checks pass,
        when evaluated,
        then tier = PRE-APPROVED (Cohort A batch) or INSTANT-APPROVE (checkout), exactly as for BANK-OBSERVED.

  AC 8 (determinism and audit): Given the same F1–F5 inputs and the same policy version,
        when evaluated twice,
        then the tier, limit and fired rule are identical.
        The fired rule ID and the policy version are written to the decision log (US-033).

Story US-015: As **Chị Lan**, I want **every approval's limit and package constraints computed by one formula, with the stacking check enforced**, so that **no customer is ever approved beyond their affordability headroom (G-4 = 0%, C2).**
Size: M
Priority: Must-have
Dependencies: US-014; cross-part: RT-02 anti-stacking gate (part C)

  AC 1 (limit): Given an approval tier (PRE-APPROVED or INSTANT-APPROVE) for grade g and tenor t,
        when the limit is computed,
        then limit = min(`policy.limit_by_grade[g]`, F2 × `policy.tenor_adjusted_factor[t]`).
        For THIN starter approvals, the result is also ≤ `policy.starter_limit`.

  AC 2 (package constraints): Given an approval,
        when constraints are returned to CO-01 (part B),
        then they contain:
        - maximum instalment = F2;
        - permitted tenors ⊆ {3, 6, 9, 12, 18, 24} [Brief p.9];
        - the limit;
        - the rate tier from the rate grid (US-016).

  AC 3 (negative — stacking): Given the RT-02 CIC stacking check **fails** for this customer,
        when the policy is evaluated,
        then no approval tier is returned.
        The outcome is `policy.stacking_fail_outcome` (OQ-17A-05), with reason code "stacking".

  AC 4 (G-4 invariant): Given a loan is about to be booked, including an officer-approved one (US-028),
        when the booking request passes the policy service,
        then booking is **blocked** if the instalment > the F2 in force at that moment.
        Every booked loan emits `affordability_check` with instalment, F2 and result.
        G-4 = blocked-attempts reported; breaches booked = 0.

  AC 5 (negative — no auto increase): Given a refreshed CCP now supports a higher limit for a customer with an active PoP limit,
        when the policy is re-evaluated,
        then the active limit is **not** changed.
        A `graduation_candidate` record is emitted for PR-03 (part C), which must obtain the customer's explicit acceptance (DD-05).

Story US-016: As **Chị Lan**, I want **a published, versioned risk-tier rate grid keyed only by risk grade and product terms**, so that **pricing is risk-based and never willingness-to-pay (DD-10).**
Size: S
Priority: Must-have
Dependencies: US-017

  AC 1: Given a rate-grid version is published with an effective-from timestamp,
        when CO-01 (part B) requests a rate for grade g and tenor t,
        then the rate of the version in force at request time is returned, with its version ID.
        The version ID is written to the decision log.

  AC 2 (negative — no WTP inputs): Given two customers with the same F1 grade and tenor but different F4 propensity, S12 engagement or basket value,
        when rates are requested,
        then both receive the **same** rate.
        The grid lookup accepts no other customer-level keys.

  AC 3 (negative — 0%): Given a grid version containing a 0% entry,
        when it is submitted for publication,
        then publication is rejected with "0% ONLY VIA MERCHANT SUBSIDY".
        0% offers come only from merchant-subsidy eligibility in CO-01 (part B).

  AC 4 (edge — historical reproducibility): Given a grid is superseded,
        when a past decision is replayed (US-033 AC 3),
        then the rate from the grid version recorded on that decision is used, not the current grid.

Story US-017: As **Chị Lan**, I want **all policy parameters held as one versioned, validated parameter set**, so that **HLB's risk appetite can change without code changes, and every decision records which parameters produced it.** The parameters are tier thresholds, `policy.integrity_floor`, `policy.dsr_cap`, `policy.living_cost_buffer`, `policy.starter_limit`, `policy.limit_by_grade`, `policy.tenor_adjusted_factor` and the gap-guard parameters.
*This is the minimum v1 form of OP-02 permitted by Skill 08 ("can start as configuration files"). It is Must-have because US-014 cannot run without it. The console UI stays Should-have (US-036).*
Size: S
Priority: Must-have
Dependencies: None

  AC 1: Given a parameter-set change is committed,
        when it is deployed,
        then a new policy version ID is created, with author, timestamp, change reason and effective-from time.
        Decisions made after effective-from record the new version ID.

  AC 2 (negative — unset decision parameter): Given any parameter listed in the Parameter Register as `[DECISION REQUIRED]` has no value,
        when deployment validation runs,
        then deployment fails and names the missing parameter.
        No built-in default is substituted.

  AC 3 (negative — type/range): Given a submitted value violates its declared type, for example `policy.dsr_cap` outside (0, 1], or a negative VND amount for `policy.starter_limit` or `policy.living_cost_buffer`,
        when validated,
        then it is rejected with the parameter name and the violated constraint.

  AC 4 (rollback): Given version n+1 is live,
        when Chị Lan triggers a rollback to version n,
        then version n becomes effective again as a new version n+2 with identical values.
        The rollback is itself written to the audit trail.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 0.5 [E-08]: Profile refresh loop
Epic: EP-0 CreditPulse Engine & CCP | Label: [v1] | Complexity: M
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Story US-018: As an **existing HLB customer (Lane A)**, I want **my Credit Profile to update when something material happens**, so that **my offers and limits reflect how I actually pay and earn today (user requirement: continuous reassessment).** Material events are a repayment, a salary inflow, a new obligation at CIC, or a change in my consents.
Size: M
Priority: Must-have
Dependencies: US-002, US-006, US-014, US-023

  AC 1 (triggers): Given one of the Skill 13 §1.1 refresh triggers occurs:
        - a PoP repayment event (F1, F2);
        - a salary/inflow event (F2);
        - a new CIC obligation found at a draw-time pull or the monthly portfolio CIC refresh (F2);
        - a consent change or a source added or removed (F3);
        - a new application or draw (F5).
        When the refresh loop processes it,
        then only the fields mapped to that trigger are recomputed, a new CCP version is written with the trigger type,
        and F6 is re-evaluated if F1, F2, F3 or F5 changed.

  AC 2 (latency): Given a trigger event is ingested,
        when the refresh completes,
        then the new version is readable through the CCP API within `refresh.event_to_ccp_latency` (Skill 14 pending).
        Each refresh emits `ccp_refreshed` with trigger type, event timestamp and completion timestamp.

  AC 3 (consent change): Given a merchant withdraws Sổ Bán Hàng consent, or a customer withdraws SC consent,
        when the consent-change event is processed,
        then:
        - for Sổ Bán Hàng: S5 is recomputed without Sổ Bán Hàng, F3 and `f3_sources` are recomputed, and F6 is re-evaluated;
        - for SC: no further refreshes run for that customer, and the stored history is handled per `privacy.sc_withdrawal_policy` (OQ-17A-04).

  AC 4 (negative — no automatic increase): Given a refresh raises F1 or F2 for a customer with an active PoP limit,
        when F6 is re-evaluated,
        then the active limit is unchanged, and only a `graduation_candidate` is emitted to PR-03 (part C) (US-015 AC 5, DD-05).

  AC 5 (edge — adverse change on an outstanding offer): Given a Lane A customer has an undelivered or unopened pre-approval offer,
        when a refresh lowers F6 (for example, a new CIC obligation reduces F2),
        then the offer record points to the new CCP version.
        On open, AW-04 (part C) shows "Your offer has been updated" with the new limit, or no offer (Skill 13 Flow 1).

Story US-019: As **Chị Lan**, I want **a scheduled batch refresh and a queryable version history for every profiled customer**, so that **profiles never go stale between events, and the engine's effect on thin files can be measured over time (KR 2.3).**
Size: M
Priority: Must-have
Dependencies: US-006, US-018

  AC 1 (cadence): Given the batch scheduler,
        when it runs,
        then it follows the Skill 13 cadences:
        - F1 and F2 monthly;
        - F4 weekly and per campaign cycle;
        - the Lane A pre-approval pipeline nightly (DF-1).
        Only customers with active SC consent are included.
        *The nightly vs monthly relationship is reconciled under OQ-17A-08.*

  AC 2 (history query): Given a customer ID and an as-of timestamp,
        when the history API is queried,
        then it returns the CCP version that was current at that time, with its trigger, inputs hash and model/policy versions.

  AC 3 (instrumentation — KR 2.3): Given the version history,
        when the monthly KR 2.3 job runs,
        then it reports the share of Cohort B customers whose F3 moved from THIN to BANK-OBSERVED within 6 months of their first CCP, split by segment.

  AC 4 (edge — batch failure): Given the monthly batch fails part-way,
        when it is re-run,
        then customers already refreshed in the failed run are not given a duplicate version for the same as-of date.
        Customers not yet refreshed keep their previous version, marked `refresh_overdue = true`, until the re-run succeeds.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 0.6 [E-03]: Phase-2 Open API cross-bank data (Circular 64)
Epic: EP-0 CreditPulse Engine & CCP | Label: [v1-Should] | Complexity: L
Condition: HLB Circular 64 readiness (full compliance due 1 Mar 2027 [R73])
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Story US-020: As **Anh Hùng, a gig driver whose platform pays into another bank**, I want **to optionally share my other-bank transaction history through Open API**, so that **my regular platform income counts toward my limit even though I am new to HLB.**
Size: L
Priority: Should-have
Dependencies: US-003, US-018, US-023, US-009

  AC 1 (feature gate): Given the feature flag `feature.open_api_enabled = false` (HLB not yet Circular 64 ready),
        when a Lane B applicant reaches the data step,
        then no Open API option is shown, and the flow is unchanged.

  AC 2 (opt-in): Given the flag is on and Anh Hùng explicitly authorises a named source bank,
        when the authorisation completes,
        then a source-level consent entry is appended to the ledger (US-023), with source bank, scope and expiry.
        The cross-bank transactions are ingested as S16, feed F1, F2 and F3, and the CCP is rebuilt.
        The F3 value that S16 supports follows `ccp.f3_precedence` (OQ-17A-03).

  AC 3 (negative — decline to share): Given Anh Hùng declines Open API sharing,
        when his CCP is built,
        then it is built without S16, exactly as for an applicant never offered the option.
        No reason code or F-field references the refusal.

  AC 4 (edge — source bank unavailable): Given the Open API call times out or returns an error,
        when the in-session decision proceeds,
        then the CCP is built without S16, and the decision is **not blocked**.
        An `open_api_unavailable` event is logged with the source bank.

  AC 5 (revocation): Given Anh Hùng later revokes the Open API authorisation in "Privacy & consent",
        when the revocation is processed,
        then no further S16 pulls occur, and the next refresh excludes S16 (US-018 AC 3).

---

# EPIC 7: Consent, Governance & Human Oversight (cross-cutting)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 7.1 [E-01]: Per-purpose consent ledger (SC / MK / RF)
Epic: EP-7 Consent, Governance & Human Oversight | Label: [v1] | Complexity: M
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Story US-021: As **Anh Hùng (new-to-bank, at a partner checkout)**, I want **to choose scoring, marketing and referral consent as separate choices**, so that **I can get a credit decision without agreeing to be marketed to (C1, PDPL purpose-specific consent [R46]).**
Size: M
Priority: Must-have
Dependencies: US-023; cross-part: MX-01 / AP-01 checkout screens (part B)

  AC 1: Given Anh Hùng reaches the consent step of the Lane B flow,
        when it is displayed,
        then SC, MK and RF are three separate controls, each with its own purpose text.
        SC is marked as required for a decision; MK and RF are optional and **not pre-selected**.

  AC 2: Given he gives SC and leaves MK and RF unselected,
        when he continues,
        then three ledger entries are appended (SC = granted, MK = not granted, RF = not granted).
        Each entry records customer/application ID, purpose, status, timestamp, channel (checkout / hosted page / QR) and consent-text version.
        The application proceeds.

  AC 3 (negative — no SC): Given he does not give SC,
        when he tries to continue,
        then no CCP is built and no CIC pull is made (US-009 AC 2).
        He sees that a credit decision needs scoring consent, with the option to return to the merchant's other payment methods.

  AC 4 (negative — no coercion): Given two applicants with identical F1–F5 inputs, one granting MK and RF and one granting neither,
        when both are decided,
        then both receive the same tier, limit and packages.
        MK and RF status is not an input to the policy layer.

  AC 5 (instrumentation — Skill 10 §5 leading indicator): Given the consent step completes or is abandoned,
        when the event is emitted,
        then the analytics store records `consent_step_result` with the grant status per purpose, or `abandoned`.
        This gives the consent rate per purpose.

Story US-022: As an **existing HLB customer (Lane A)**, I want **a "Privacy & consent" screen in the HLB Connect "Pay Later" hub**, so that **I stay in control of how HLB uses my data (Skill 13 §3).** On it I can see, grant or withdraw SC, MK and RF, and see which data sources my profile uses.
Size: M
Priority: Must-have
Dependencies: US-023, US-007

  AC 1: Given the customer opens "Privacy & consent",
        when the screen loads,
        then it shows the current status of SC, MK and RF, the date each was last changed, and the data sources in `f3_sources` of the current CCP.

  AC 2 (grant): Given SC is not granted,
        when the customer turns SC on and confirms,
        then a ledger entry is appended, and the customer becomes eligible for the next nightly CCP build (US-019).
        *The proactive consent-acquisition journey for existing customers is not designed in Skill 13 (OQ-17A-09).*

  AC 3 (withdraw MK): Given MK is granted,
        when the customer withdraws it,
        then a ledger entry is appended immediately.
        Every subsequent MK read is denied (US-008 AC 2), including delivery-time re-checks of already-built campaign lists (AW-02 / AW-04, part C).

  AC 4 (withdraw SC — edge): Given the customer has an active PoP loan and withdraws SC,
        when confirmed,
        then no new scoring, pre-approval or graduation offer is produced for that customer.
        The treatment of the existing loan's servicing data and of the stored CCP history follows `privacy.sc_withdrawal_policy` (OQ-17A-04).
        The screen tells the customer that new PoP decisions need scoring consent.

  AC 5 (instrumentation — G-7): Given any withdrawal,
        when it is recorded,
        then `consent_withdrawn` (purpose, channel) is emitted.
        The monthly G-7 job alerts Compliance/DPO when the month-on-month increase exceeds `guardrail.consent_withdrawal_spike`.

Story US-023: As an **HLB compliance officer / DPO**, I want **an append-only consent ledger holding purpose consents (SC/MK/RF) and source-level consents, with an automated daily reconciliation against every CCP read**, so that **I can prove a matching consent existed for 100% of profile uses (KR 4.1).** Source-level consents are Sổ Bán Hàng merchant consent and Open API authorisations.
Size: M
Priority: Must-have
Dependencies: None (first in build order: EP-7 E-01 → EP-0)

  AC 1 (negative — append-only): Given an existing ledger entry,
        when any service or user attempts to update or delete it,
        then the operation is rejected.
        Changes are only possible by appending a new entry that supersedes the previous status.

  AC 2 (point-in-time): Given a customer, a purpose and a past timestamp T,
        when the ledger is queried as of T,
        then it returns the status in force at T and the entry ID that set it.

  AC 3 (source consent): Given a Sổ Bán Hàng merchant consent, or an Open API authorisation, is granted or withdrawn,
        when recorded,
        then the entry carries `scope = source:<name>`.
        Ingestion (US-001 AC 3) and signal jobs (US-003 AC 3) read these entries before loading or using that source.

  AC 4 (KR 4.1 reconciliation): Given the previous day's `ccp_read` events (US-008 AC 5),
        when the daily job joins them to the ledger as of each read's timestamp,
        then it reports "consent-matched reads ÷ total allowed reads".
        Any allowed read without a matching active consent raises an alert to Compliance/DPO with the read ID (target 100%, Skill 10 §4).

  AC 5 (retention): Given `audit.retention_period`,
        when ledger entries reach it,
        then they are handled per that policy.
        No entry is purged earlier.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 7.2 [E-09]: Reason-code generator
Epic: EP-7 Consent, Governance & Human Oversight | Label: [v1] | Complexity: S
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Story US-024: As the **Policy & Decision service**, I need **every tier outcome to carry reason codes drawn from a controlled catalogue**, so that **100% of decisions are explainable to the customer, the officer and the regulator (KR 4.1, C3, AI Law).**
Size: S
Priority: Must-have
Dependencies: US-010, US-014

  AC 1: Given any decision with tier PRE-APPROVED, INSTANT-APPROVE, REFER or DECLINE,
        when the decision response is assembled,
        then it contains ≥ 1 reason code.
        The **first** code identifies the tier rule that fired (for example `R2_AFFORDABILITY`, `R1_IDENTITY_REVIEW`, `R4_STARTER_LIMIT`).
        The following codes map from the top-ranked F1 factor contributions (US-010 AC 1).

  AC 2 (catalogue): Given the reason-code catalogue,
        when a code is added,
        then it must carry a customer-facing text (Vietnamese), an officer-facing text and the rule or factor it maps to.
        A code without all three is rejected.

  AC 3 (negative — prohibited content): Given the catalogue validation,
        when a code maps to gender, ethnicity, religion, age band, region, S12 or a referral attribute,
        then the code is rejected.
        This is consistent with US-005: such fields are never model inputs.

  AC 4 (negative — missing code): Given a decision response with zero reason codes,
        when schema validation runs,
        then the response is not emitted, a `reason_code_missing` error is logged, and an alert is raised.
        Customer-facing handling follows AP-01 error states (part B).

  AC 5 (instrumentation — KR 4.1): Given the previous day's decision log,
        when the daily job runs,
        then it reports "decisions with ≥ 1 reason code ÷ all decisions" (target 100%), and lists any exceptions to Compliance/DPO.

Story US-025: As **Anh Hùng**, I want **to see the main reasons for my decision, at checkout and later in "Why this decision"**, so that **I understand why I was declined or referred, and what might change the outcome.**
Size: S
Priority: Must-have
Dependencies: US-024; cross-part: AP-01 decision screen (part B)

  AC 1: Given Anh Hùng is declined at checkout,
        when the decision screen shows,
        then it displays the customer-facing text of the top `reason_codes.display_count` reason codes and a generic path message.
        For `R2_AFFORDABILITY` the message is "a smaller basket or a larger down-payment may be approved" (Skill 13 Flow 3).

  AC 2: Given a Lane A or Lane B customer with at least one decision,
        when they open "Pay Later → Why this decision",
        then the reason codes of their **latest** decision are shown with the same customer-facing text shown at decision time.

  AC 3 (consistency): Given the same decision,
        when it is viewed by the customer ("Why this decision") and by Chị Lan (case view, US-027),
        then both views list the same reason codes in the same order, each in its own audience's text.

  AC 4 (edge — no decision yet): Given a customer with no decision on record,
        when they open "Why this decision",
        then the screen states there is no decision to explain. It shows no placeholder reasons.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 7.3 [OP-01]: Officer refer-band workbench
Epic: EP-7 Consent, Governance & Human Oversight | Label: [v1] | Complexity: M
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Story US-026: As **Chị Lan, HLB credit-risk / underwriting lead**, I want **a refer queue with a business-hours SLA timer on every case**, so that **≥ 90% of referred customers get a decision within 2 business hours (KR 1.4).**
Size: M
Priority: Must-have
Dependencies: US-014; cross-part: AP-04 refer-band handling creates cases (part B)

  AC 1: Given a decision returns REFER, and the customer declines the counter-offer or no counter-offer exists (DF-4),
        when AP-04 (part B) creates the case,
        then the case appears in the queue with an SLA deadline = creation time + `ops.refer_sla_business_hours` (design value 2), counted on `ops.business_calendar`.
        By default the queue is sorted by time remaining, ascending.

  AC 2 (edge — outside business hours): Given a case is created after business hours on a Friday,
        when the deadline is computed,
        then the clock starts at the next business-hour opening in `ops.business_calendar`, not at creation time.

  AC 3 (edge — SLA breached): Given a case passes its deadline undecided,
        when the timer expires,
        then the case is marked `SLA_BREACHED`, stays at the top of the queue and counts as a KR 1.4 miss.

  AC 4 (instrumentation — KR 1.4, KR 1.3): Given the previous day's closed cases,
        when the daily job runs,
        then it reports the share decided within the SLA, and alerts the credit-ops lead if < 90% (Skill 10 §4).
        It also reports refer-case volume and officer handling time as inputs to KR 1.3 blended cost per decision.

  AC 5 (negative — access): Given an officer tries to open a customer's CCP that has no open refer case assigned to the officer's queue,
        when the request is made,
        then access is denied, and the attempt is logged (Skill 13 §4 Auth: officers see only refer-queue cases).

Story US-027: As **Chị Lan**, I want **each referred case pre-assembled with the full evidence**, so that **I review uncertain cases instead of rebuilding files by hand (Pain #13).** The evidence is the CCP fields, sources, reason codes, model versions, integrity flags and the counter-offer already tried.
Size: M
Priority: Must-have
Dependencies: US-026, US-024, US-033

  AC 1: Given Chị Lan opens a case,
        when the case view loads,
        then it shows, without any manual retrieval:
        - F1 (grade, PD band) and F2 (headroom, income estimate and confidence interval);
        - F3 (with sources) and F4;
        - F5 (with flags) and F6 (tier and reason codes);
        - the model version(s) and challenger arm;
        - the counter-offer attempted and the customer's response;
        - the eKYC result summary.

  AC 2 (snapshot integrity): Given the customer's CCP is refreshed after the case was created,
        when Chị Lan views the case,
        then she sees the **evidence snapshot taken at case creation** (DF-4), with a banner that a newer CCP version exists.

  AC 3 (data minimisation): Given the case includes CIC data,
        when displayed,
        then only the CIC fields used by the engine (S14: obligations, delinquency, number of lenders, recent enquiries) are shown (CIC incident lesson [R71]).

  AC 4 (negative — protected attributes): Given the case view,
        when rendered,
        then it does not display fields in the `fairness_monitoring_only` namespace (US-005).

Story US-028: As **Chị Lan**, I want **to approve, counter-offer or decline a referred case, giving a justification for any override**, so that **human oversight is real and auditable (AI Law human oversight, C3), and my decisions improve the models (DF-4).**
Size: M
Priority: Must-have
Dependencies: US-027, US-015, US-033; cross-part: AP-04 customer and merchant notification (part B)

  AC 1: Given an open case,
        when Chị Lan selects Approve, Counter-offer or Decline and submits,
        then the decision is written to the decision log with officer ID, timestamp, action, amount/tenor (if any) and reason codes.
        A `refer_decided` event is emitted, so AP-04 (part B) notifies the customer in-app and by SMS, and the merchant.

  AC 2 (override justification): Skill 13 requires a justification "on override", but a REFER case has no automated final tier. So v1 treats **every** officer Approve, Counter-offer or Decline on a refer case as an override (definition to confirm, OQ-17A-10).
        Given Chị Lan submits a decision with an empty justification,
        when she clicks Submit,
        then submission is blocked with "Justification required".
        Given a justification is entered, it is stored verbatim in the decision log with the officer ID.

  AC 3 (negative — affordability and stacking): Given Chị Lan approves an amount/tenor whose instalment > F2, or the RT-02 stacking check fails,
        when she submits,
        then the approval is blocked (US-015 AC 3, AC 4; G-4 = 0% applies to officer approvals).

  AC 4 (feedback label): Given a case is decided,
        when the loan later reaches MOB3,
        then the officer decision and the outcome are available as a labelled record for champion/challenger evaluation (US-011 AC 2).
        The approve / counter-offer / decline mix per officer is reported on the governance console (US-034).

Story US-029: As **Chị Lan**, I want **to escalate a suspicious case to the fraud team and record the confirmed outcome**, so that **identity fraud is stopped and G-2 is measured (R-3).**
Size: S
Priority: Must-have
Dependencies: US-026, US-013

  AC 1: Given a case with integrity flags,
        when Chị Lan selects "Escalate to fraud",
        then the case moves to the fraud-team queue with its evidence snapshot, and it leaves the officer SLA queue marked `ESCALATED_FRAUD`.

  AC 2: Given the fraud team confirms an identity mismatch,
        when they record the outcome,
        then the application's final tier is DECLINE, a `fraud_confirmed` label is written (feeding US-013 AC 4), and the customer is notified via AP-04 (part B).

  AC 3 (edge — cleared): Given the fraud team clears the case,
        when they record "not fraud",
        then the case returns to the officer queue with its original SLA deadline, and the escalation time is visible on the case.

  AC 4 (instrumentation — G-2): Given confirmed-fraud labels and approved-loan counts,
        when the monthly job runs,
        then it reports confirmed fraud per 1,000 approved loans, by cohort and segment.
        The rate is compared with the manual baseline (baseline `[DECISION REQUIRED]`, Skill 10 G-2).

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 7.4 [GV-01]: Model governance (champion/challenger, drift, fairness, audit log, AI Law docs)
Epic: EP-7 Consent, Governance & Human Oversight | Label: [v1] | Complexity: L
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Story US-030: As an **HLB model-risk officer**, I want **a model registry that every served model must pass through, with a governed champion/challenger split**, so that **no unreviewed model ever scores a customer.** The models covered are the risk champion, the challenger, the income estimator, the behavioural/early-warning models and the integrity model.
Size: M
Priority: Must-have
Dependencies: US-005

  AC 1: Given a new model version is submitted,
        when registration runs,
        then it must include every field in `governance.model_card_template` (to be aligned with the AI Law technical documentation, OQ-17A-11).
        That covers owner, purpose, training-data window, input list, missing-value treatment, validation results and approval status.
        The US-005 input check must also pass.
        Otherwise registration fails and lists the missing items.

  AC 2: Given a registered version with status `PENDING`,
        when the scoring API tries to load it,
        then loading is refused (US-010 AC 2).
        Only a model-risk officer can set status `APPROVED`, and the approval is logged with user and timestamp.

  AC 3 (split control): Given the governance console,
        when a model-risk officer changes `governance.challenger_split_pct`, globally or for one segment, including setting it to 0%,
        then the change takes effect for new decisions only and is recorded with user, timestamp, old and new values, and reason.

  AC 4 (negative — separation): Given a user with the officer (OP-01) role only,
        when they try to change a model status or the split,
        then the action is denied.

Story US-031: As an **HLB model-risk officer**, I want **weekly PSI drift monitoring on scores and key inputs, with automatic protective actions**, so that **mis-grading is caught before it reaches MOB3 losses (G-8, DD-13, FM-9).**
Size: M
Priority: Must-have
Dependencies: US-030, US-033

  AC 1: Given the weekly monitoring job,
        when it computes PSI for each served model's score distribution and each registered key input against the development sample,
        then results are stored per model × segment and shown on the governance console.

  AC 2 (investigate band): Given a PSI value where 0.10 ≤ PSI ≤ 0.25,
        when the job completes,
        then an "investigate" alert goes to the model-risk officer. No automatic routing change is made.
        Given PSI < 0.10, no alert is raised.

  AC 3 (action — challenger): Given the challenger's score PSI > 0.25,
        when the job completes,
        then the challenger share is set to 0% automatically (US-011 AC 4).
        Graduation offers generated from that model version that are still in status `OFFERED` are paused (Skill 13 Flow 4).
        An "act" alert is raised.

  AC 4 (edge — champion drifts): Given the **champion's** score PSI > 0.25 (no fallback model exists),
        when the job completes,
        then an "act" alert is raised to model risk and Chị Lan, and the action in `governance.champion_drift_action` is applied (OQ-17A-07).
        Skill 10 names "pause the specific segment's auto-approval" as an available guardrail action.

  AC 5 (edge — income estimator): Given the income estimator's output PSI > 0.25,
        when the job completes,
        then an "act" alert is raised.
        F2 computations continue under the rules layer (US-007), and the event is logged. No silent change is made to F2.

Story US-032: As an **HLB model-risk officer**, I want **monthly fairness monitoring of approval rate and MOB3 30+ by gender, age band and region**, so that **disparities are detected and investigated, without protected attributes ever being model inputs (KR 4.3, G-5).**
Size: M
Priority: Must-have
Dependencies: US-005, US-033

  AC 1: Given the month's decisions and the fairness-monitoring namespace,
        when the monthly job runs,
        then it reports, for every gender, age-band and region group:
        - the approval rate and its ratio against `governance.fairness_reference_group`;
        - MOB3 30+ and its ratio.
        The report is live from launch (KR 4.3).

  AC 2 (alert): Given any group's approval-rate ratio < 0.8,
        when the job completes,
        then a G-5 alert goes to model risk.
        The alert names the group, ratio, segment and model version.
        The 0.8 figure is an alert trigger, not a legal standard (A-10-7).

  AC 3 (edge — MOB3 ratio): Given a group's MOB3 30+ ratio breaches `governance.fairness_mob3_ratio_alert`,
        when the job completes,
        then a G-5 alert is raised in the same way.

  AC 4 (negative — access path): Given the monitoring job reads protected attributes,
        when any scoring, policy or campaign service requests the same fields,
        then the request is refused (US-005).
        Only the monitoring job's service identity may read the `fairness_monitoring_only` namespace.

Story US-033: As an **HLB compliance officer / DPO**, I want **an append-only decision log entry for every decision**, so that **any decision can be reproduced and explained for disputes and AI Law documentation (KR 4.2, FM-10).** Every automated tier, officer action and graduation offer is logged.
Size: M
Priority: Must-have
Dependencies: US-014, US-017

  AC 1: Given any decision is made,
        when it is logged,
        then the entry contains:
        - decision ID, customer/application ID, cohort and segment;
        - **input-snapshot hash** and snapshot reference;
        - model versions (champion and, if applicable, challenger), with arm;
        - policy version, rate-grid version and fired rule;
        - tier and limit;
        - reason codes;
        - consent purpose and the ledger entry ID checked;
        - timestamp;
        - for officer actions, officer ID and justification.

  AC 2 (negative — append-only): Given an existing log entry,
        when any user or service attempts to modify or delete it before `audit.retention_period`,
        then the operation is rejected and the attempt is logged.

  AC 3 (replay): Given a logged decision,
        when an auditor replays it from the stored snapshot with the recorded model, policy and grid versions,
        then the stored snapshot re-hashes to the logged hash, and the replay yields the same tier, limit and reason codes.

  AC 4 (export): Given a date range and optional filters (segment, tier, model version),
        when the compliance officer requests an export,
        then a machine-readable file of the matching entries is produced, and the export itself is logged with requester and timestamp.

Story US-034: As **Chị Lan**, I want **a guardrail console showing G-1, G-3, G-4 and champion-vs-challenger results, with pause switches that act when a guardrail is breached**, so that **"breaching one pauses the relevant feature" (Skill 10 §3) happens reliably, not ad hoc.**
Size: M
Priority: Must-have
Dependencies: US-031, US-032, US-033, US-017

  AC 1: Given the monthly portfolio data,
        when the console loads,
        then it shows:
        - MOB3 30+ by segment × cohort against `guardrail.mob3_30_ceiling[segment]` (G-1);
        - the stacking rate, quarter on quarter (G-3);
        - affordability-breach bookings, which must be 0 (G-4, from US-015 AC 4);
        - champion vs challenger approval and MOB3 30+ (KR 2.1);
        - the officer decision mix, meaning approve / counter-offer / decline per officer (US-028 AC 4);
        - the online/offline parity mismatches (US-002 AC 4).

  AC 2 (G-1 breach): Given a segment's MOB3 30+ exceeds `guardrail.mob3_30_ceiling[segment]`,
        when the monthly job runs,
        then an alert goes to Chị Lan and model risk, and `pause.segment_auto_approval[segment]` is set **automatically** (Skill 10 §3: a breach pauses the feature until fixed).
        While it is set, new applications in that segment that would be INSTANT-APPROVE are returned as REFER, with reason code `GUARDRAIL_PAUSE`.
        Lifting the pause is manual (AC 4).

  AC 3 (G-3 breach): Given the stacking rate rises quarter on quarter,
        when the job runs,
        then an alert is raised, and `pause.graduation_offers` and `pause.retention_reoffers` are set automatically.
        While set, PR-03 and RT-01 (part C) produce no new offers.

  AC 4 (negative — audit of switches): Given any pause switch is turned on or off,
        when the change is saved,
        then the change is written with user, timestamp, switch, guardrail reading and justification.
        A change without a justification is rejected.

Story US-035: As an **HLB compliance officer / DPO**, I want **the AI Law technical documentation pack and human-oversight procedure generated from the live system of record**, so that **the engine's conformity assessment is completed before deployment (≤ 1 Mar 2027 for a new system, corrected RF-8) (KR 4.2).**
Size: M
Priority: Must-have
Dependencies: US-030, US-031, US-032, US-033, US-023

  AC 1: Given the compliance officer requests the documentation pack for a period,
        when it is generated,
        then it contains:
        - the model registry entries (US-030);
        - data sources and their consent basis (US-023);
        - policy parameter versions (US-017);
        - PSI and fairness reports (US-031, US-032);
        - the officer decision statistics (US-028);
        - the written human-oversight procedure for the refer band;
        - a decision-log sample (US-033).

  AC 2 (negative — incomplete pack): Given any required section is empty,
        when the pack is generated,
        then its status is `INCOMPLETE`, with the missing sections listed, and it cannot be marked `SUBMITTED`.

  AC 3 (edge — deadline tracking): Given today's date,
        when the governance console loads,
        then it shows KR 4.2 status (`NOT STARTED` / `INCOMPLETE` / `COMPLETE`) and the days remaining to the deployment deadline (`governance.ai_law_deadline`, currently 1 Mar 2027 at the latest per the corrected RF-8).
        The required section list is configurable, because the AI Law's detailed documentation requirements are not yet specified in this pipeline (OQ-17A-11).

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 7.5 [OP-02]: Policy / rule console
Epic: EP-7 Consent, Governance & Human Oversight | Label: [v1-Should] | Complexity: M
Condition: may start as version-controlled config (the v1 minimum is US-017)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Story US-036: As **Chị Lan**, I want **a console screen for viewing and changing policy parameters and the rate grid**, so that **I can tune risk appetite without an engineering release, with every change validated and traceable (Skill 13 §3, risk & governance console).**
Size: M
Priority: Should-have
Dependencies: US-017, US-016, US-033

  AC 1: Given Chị Lan opens "Policy parameters",
        when the screen loads,
        then it lists every parameter in the Parameter Register with its current value, the version ID, the author and date of the last change, and any parameter still unset, flagged `[DECISION REQUIRED]`.

  AC 2: Given Chị Lan edits `policy.starter_limit` with an effective-from time and a change reason,
        when she saves,
        then the same validation as US-017 AC 3 runs, a new policy version is created (US-017 AC 1), and the version history shows old value, new value, author and reason.

  AC 3 (negative — missing reason): Given a change without a change reason,
        when she saves,
        then the save is rejected with "Change reason required".

  AC 4 (negative — role): Given a user without the policy-owner role (for example an OP-01 officer),
        when they open the console,
        then parameters are read-only, and edit controls are not rendered.

  AC 5 (rate grid): Given Chị Lan uploads a new rate-grid version,
        when it is submitted,
        then the US-016 AC 3 validation (no 0% entries) runs before publication, and the new grid gets its own version ID.

---

## Dependency Graph (part A)

This is a topological build order derived from each story's `Dependencies` line; every story depends only on stories in earlier waves.

| Wave | Stories |
|---|---|
| 1 | US-023 consent ledger · US-017 policy parameter set |
| 2 | US-001 batch ingestion · US-016 rate grid · US-021 consent capture (Lane B) |
| 3 | US-002 event ingestion |
| 4 | US-003 CASA signals S1–S8 · US-004 signals S9–S15 |
| 5 | US-005 model-input namespace guard |
| 6 | US-030 model registry |
| 7 | US-010 scorecard champion · US-012 income estimator · US-013 integrity anomaly model |
| 8 | US-007 F2/F3 rules |
| 9 | US-014 tier rules · US-022 Privacy & consent (Lane A) |
| 10 | US-006 CCP schema & versioning · US-015 limit/package/G-4 · US-024 reason codes · US-026 refer queue · US-033 decision audit log |
| 11 | US-008 CCP read API · US-018 event refresh · US-025 "Why this decision" · US-029 fraud escalation · US-011 GBM challenger · US-031 PSI drift · US-032 fairness · US-036 policy console (Should) |
| 12 | US-009 Lane B on-demand CCP · US-019 batch refresh & history · US-027 case view · US-034 guardrail console · US-035 AI Law pack |
| 13 | US-020 Open API (Should) · US-028 officer decision & override |

**Critical path to the first in-session decision (AP-01, part B):**
US-023 → US-001 → US-002 → US-003/US-004 → US-005 → US-030 → US-010 + US-012 + US-013 → US-007 → US-014 (+ US-017) → US-006 → US-008 → US-009.

### Cross-part dependencies (by feature code)

| This part provides | Consumed by (other part) | Contract |
|---|---|---|
| US-008 CCP read API (purpose-checked) | AP-01, CO-01, CO-02 (part B, SC); AW-01, AW-02, AW-04, RT-01 (part C, MK); AD-01 (part C, RF) | Every read declares a purpose; denial returns no fields |
| US-009 on-demand Lane B CCP | AP-01, MX-01 (part B) | SC consent required; `cic_status`, `f2_status` carried |
| US-013 integrity anomaly output | AP-03 → F5 (part B); AD-01 referral-abuse flag (part C) | Anomaly score + flags |
| US-004 CIC pull timestamp, US-009 AC 3 `cic_status` | AP-07 CIC fallback (part B) | Cached pull under 30 days (Skill 13 Flow 3) |
| US-014 tier + counter-offer constraint; US-015 limit/package constraints; US-016 rate grid | AP-01, AP-04, AP-05 (part B); CO-01 (part B) | Max instalment = F2; tenors ⊆ {3,…,24}; starter limit parameter |
| US-015 AC 5 / US-018 AC 4 `graduation_candidate` | PR-03 (part C) | Offer only; explicit acceptance required |
| US-018 refresh on repayment events | PR-01 (part C) | New CCP version per S15 event |
| US-024 reason codes | AP-01 decision screens, AP-04 (part B) | ≥ 1 code; customer text |
| US-026–US-029 workbench | AP-04 (part B) creates cases and sends notifications on `refer_decided` | DF-4 |
| US-034 pause switches | AW (campaigns), PR-03, RT-01 (part C); AP-01 (part B) | Honour `pause.*` flags |
| **Needs from other parts:** RT-02 stacking check (part C) for US-015 AC 3 / US-028 AC 3; CO-01 cheapest-package instalment (part B) for US-014 AC 2; AP-02 eKYC result (part B) for US-009 / US-013; AP-04 case creation (part B) for US-026; PR-02 early-warning flags (part C) for suppressions honoured by US-008 consumers | | |

---

## Story Count Summary (part A)

| Epic | Feature | Label | Stories | ACs |
|---|---|---|---|---|
| EP-0 | 0.1 Phase-1 data ingestion (E-02) | v1 | 5 (US-001–005) | 23 |
| EP-0 | 0.2 CCP generator & API (E-05) | v1 | 4 (US-006–009) | 20 |
| EP-0 | 0.3 Hybrid risk scoring (E-06) | v1 | 4 (US-010–013) | 17 |
| EP-0 | 0.4 Tier policy + rate grid (E-07) | v1 | 4 (US-014–017) | 21 |
| EP-0 | 0.5 Profile refresh loop (E-08) | v1 | 2 (US-018–019) | 9 |
| EP-0 | 0.6 Open API cross-bank data (E-03) | v1-Should | 1 (US-020) | 5 |
| EP-7 | 7.1 Consent ledger (E-01) | v1 | 3 (US-021–023) | 15 |
| EP-7 | 7.2 Reason codes (E-09) | v1 | 2 (US-024–025) | 9 |
| EP-7 | 7.3 Officer workbench (OP-01) | v1 | 4 (US-026–029) | 17 |
| EP-7 | 7.4 Model governance (GV-01) | v1 | 6 (US-030–035) | 24 |
| EP-7 | 7.5 Policy console (OP-02) | v1-Should | 1 (US-036) | 5 |
| **Total** | **11 features (9 v1 + 2 v1-Should)** | | **36 stories (34 Must-have, 2 Should-have)** | **165 ACs** |

```
Total Epics (this part): 2 (EP-0, EP-7)
Total v1 Features (this part): 9 [v1] + 2 [v1-Should]
Total v1 Stories: 36   (Must-have 34 · Should-have 2)
Total ACs: 165         (every story has ≥ 1 labelled negative / edge / gap-guard AC)
Size mix: S 5 · M 23 · L 7 · XL 1
Estimated story points / sprints: not computed. Skill 14 re-estimation is pending (OQ-16-01),
  and no points-per-size scale or team velocity has been given. Left for the consolidation step.
```

### KPI / guardrail instrumentation delivered by this part

| Metric (Skill 10) | Instrumented in |
|---|---|
| KR 1.2 engine share of p50/p90 time to decision | US-009 AC 5, US-018 AC 2 |
| KR 1.3 refer volume and handling time (cost inputs) | US-026 AC 4 |
| KR 1.4 refer SLA ≥ 90% within 2 business hours | US-026 AC 1–4 |
| KR 2.1 champion vs challenger approval and MOB3 30+ per segment | US-011 AC 1–3, US-028 AC 4, US-034 AC 1 |
| KR 2.3 THIN → BANK-OBSERVED within 6 months | US-019 AC 3 |
| KR 4.1 100% reason codes · 100% consent-matched reads | US-024 AC 1, AC 5 · US-008 AC 5, US-023 AC 4 |
| KR 4.2 AI Law documentation before deployment (≤ 1 Mar 2027) | US-035 |
| KR 4.3 / G-5 fairness live at launch; ratio < 0.8 alert | US-032 |
| G-1 MOB3 30+ ceiling → segment auto-approval pause | US-034 AC 2 |
| G-2 confirmed fraud per 1,000 approved loans | US-013 AC 4, US-029 AC 4 |
| G-3 stacking → graduation / re-offer pause | US-034 AC 3 (gate itself: RT-02, part C) |
| G-4 affordability breach = 0% | US-015 AC 4, US-028 AC 3, US-007 AC 4 |
| G-7 consent-withdrawal spike | US-022 AC 5 |
| G-8 PSI 0.10–0.25 investigate / > 0.25 act | US-031, US-011 AC 4, US-012 AC 4 |
| Leading indicator: consent rate per purpose | US-021 AC 5 |

### Design constraints enforced (traceability)

| Constraint (Skill 13) | Enforcing AC(s) |
|---|---|
| Consent purpose checked on every CCP read (C1, E-01) | US-008 AC 1–3, AC 6; US-023 AC 4 |
| S12 digital engagement never used for risk (DD-07) | US-004 AC 5; US-005 AC 2–3 |
| Protected attributes never model inputs (§6) | US-005 AC 1–2; US-013 AC 2; US-024 AC 3; US-032 AC 4 |
| "Referred" never a risk feature (DD-08) | US-005 AC 2; US-024 AC 3 |
| Limit increases never auto-applied (DD-05, C2) | US-015 AC 5; US-018 AC 4 |
| CIC-unavailable fallback (AP-07) | US-004 AC 1; US-007 AC 5; US-009 AC 3 |
| Audit log per decision: inputs hash, model versions, tier, reason codes | US-033 AC 1–3; US-014 AC 8 |
| Officer override requires justification | US-028 AC 2; US-034 AC 4 (pause switches) |
| No contacts / gallery / social data (X-01) | US-001 AC 4; US-004 AC 3 |
| Risk-tier pricing, no WTP; 0% only merchant-subsidised (DD-10) | US-016 AC 2–3 |
| Affordability as a rule, with ML only estimating income (DD-03) | US-012 AC 2; US-007 AC 1 |

---

## Later Stories Parked (listed only, no ACs)

| Feature | Code | Label | Parked story (one line) | Revisit condition |
|---|---|---|---|---|
| 0.7 Phase-3 telco / e-wallet / platform-income data | E-04 | [Later] PL-01 | As Anh Hùng, I want to share consented telco, e-wallet and platform-income data (S17) so that my thin file can be assessed on more signals. | Decree 94 sandbox applicability and PDPL vendor due diligence resolved |
| 7.6 Decree 94 sandbox application | GV-02 | [Later] PL-02 | As an HLB compliance officer, I want to prepare and file a Decree 94 sandbox application, so that Phase-3 data can be trialled lawfully. | HLB decides to pursue PL-01 |

*Not written, pending user confirmation:* 0% merchant-subsidy eligibility configured per merchant by HLB staff through OP-02 (OQ-16-02). If confirmed, it becomes a Should-have story under Feature 7.5.

---

## Open Items Raised by This Part (carry to Skill 22 unless noted)

| # | Item | Blocks | Carry to |
|---|---|---|---|
| OQ-17A-01 | **RESOLVED 2026-09-30.** Skill 13 §1.1 now includes CONSENTED-PARTNER in the A/B approve rule. **Original: Tier-rule gap.** Under the literal Skill 13 §1.1 rule order, F1 ∈ {A,B} with F3 = CONSENTED-PARTNER (for example a Sổ Bán Hàng MSME with no CIC file) fails rules 3 and 4. It falls through to ELSE → DECLINE; rule 5 only catches F1 ∈ {C,D}. This conflicts with the segment table, which expects MSMEs at CONSENTED-PARTNER to be approvable. Written as a gap guard (`policy.tier_rule.consented_partner_ab`, US-014 AC 7). | US-014 go-live | Skill 22 / HLB credit policy |
| OQ-17A-02 | **RESOLVED 2026-09-30 (design); confirm in Skill 22 (A-13-12).** Skill 13 adds F2_thin from one declared-income field. **Original: Thin-file income gap.** The Lane B happy path expects THIN → INSTANT-APPROVE at starter limit. But Lane B collects no income evidence (phone, CCCD, consents only), so F2 has no income input. Rule 2 (F2 < min instalment → DECLINE) runs **before** rule 4 (starter limit), so as written every no-income THIN applicant is declined. Needs `policy.thin_file_no_income_rule`, or an income source (for example a declared-income field, or S16), decided by HLB (US-009 AC 4). | US-009, US-014; the Aha moment in Skill 15 | Skill 22 |
| OQ-17A-03 | **F3 derivation is under-specified.** Open questions: the minimum CASA history for BANK-OBSERVED (`ccp.bank_observed_min_months`); precedence when several categories apply; the value for a new-to-bank applicant with a CIC file but no HLB data; the value S16 Open API data supports. | US-007 AC 2, US-020 AC 2 | Skill 22 |
| OQ-17A-04 | **Effect of withdrawing SC:** on stored CCP history, on active-loan servicing and monitoring, and the PDPL retention/erasure treatment (`privacy.sc_withdrawal_policy`). | US-018 AC 3, US-022 AC 4 | Skill 22 / DPO |
| OQ-17A-05 | **Checkout outcome when the RT-02 stacking check fails** (DECLINE vs REFER vs reduced limit). Skill 13 only says the check must pass for any approval. | US-015 AC 3 | Skill 22 |
| OQ-17A-06 | **F2 formula wording.** "income − obligations − buffer, × DSR cap" is ambiguous. It is unclear whether the DSR cap multiplies income before obligations are deducted, and whether the point estimate or the CI lower bound is used. | US-007 AC 1 | Skill 22 / credit policy |
| OQ-17A-07 | **Action when the champion itself drifts** (PSI > 0.25): Skill 13 only defines fallback *to* the champion. | US-031 AC 4 | Skill 22 / model risk |
| OQ-17A-08 | **Refresh cadence conflict.** Flow 1 / DF-1 run a nightly CCP refresh for Lane A, while the §1.1 table says F1/F2 refresh monthly. It is unclear whether the nightly job re-scores or only re-applies policy to current fields. | US-019 AC 1 | Skill 22 / Skill 14 |
| OQ-17A-09 | **SC/MK consent acquisition journey for existing Lane A customers** (how the nightly pool gets SC consent in the first place) is not designed in Skill 13. | US-022 AC 2; part C AW-01 reach | Skill 22 |
| OQ-17A-10 | **Definition of "override" in the refer band.** v1 treats every officer decision as requiring justification. | US-028 AC 2 | Skill 22 |
| OQ-17A-11 | **AI Law technical-documentation contents** (model-card template, required sections) are not specified in this pipeline. | US-030 AC 1, US-035 | Skill 22 / legal |
| OQ-17A-12 | **Consent-service outage.** The CCP read API fails closed, which means a consent-service outage blocks checkout decisions. Availability target to be set in Skill 18. | US-008 AC 6 | Skill 18 NFR |
| OQ-17A-13 | **Parameters without values:** `governance.challenger_split_pct` (share and assignment unit), `governance.fairness_reference_group`, `governance.fairness_mob3_ratio_alert`, `reason_codes.display_count`, `ops.business_calendar`, `audit.retention_period`, `signals.*` windows, `policy.min_package_instalment` (the no-basket batch context). | Named ACs | Skill 22 |
| OQ-16-01 (inherited) | Story sizes are provisional until Skill 14 re-estimates. | Sizing | Skill 14 |

---

## Source Index
No new external sources. Every behaviour, value and constraint traces to:
- `04-design/13-solution-design.md`: §1.1 CCP fields and tier policy, §1.2 signals S1–S17 and exclusions, §2 flows, §3 IA, §4 architecture and DF-1 to DF-6, §5 DD-01 to DD-14, §6 exclusions, §8 OQ-13-01 to 04, §9 A-13-1 to A-13-11.
- `03-define/10-success-metrics.md`: KR 1.1–4.3, G-1 to G-8, §4 measurement plan, A-10-4, A-10-7.
- `03-define/08-v1-product-scoping.md`: feature codes, C1–C6, Parking Lot.
- `05-specify/16-epic-feature-decomposition.md`: features, labels, complexity.
- `05-specify/15-user-journey-map.md`: lanes, personas, steps 3.R and 3.D.
- `_context.md`: SD-1 to SD-7.

External citations carried inside ACs ([R46] PDPL, [R71] CIC, [R73] Circular 64, [R80] digital footprint, [Brief p.9] tenors) resolve to `_research-notes-07-13.md` and `_requirements.md`. The format follows `.agents/skills/00-case-studies/by-output-type/user-story-examples.md` (structure only; not cited as evidence).


---

# ═══ PART B ═══

# User Stories & Acceptance Criteria — Part B (EP-2, EP-3, EP-8)
**Product:** HLB Point-of-Purchase Credit Decisioning — **HLB CreditPulse** | **Skill:** 17 (part B of 3) | **Status:** Done (2026-09-30)
**Depends on:** `16-epic-feature-decomposition.md` (feature list), `13-solution-design.md` (behaviour), `10-success-metrics.md` (KRs, guardrails), `08-v1-product-scoping.md`, `15-user-journey-map.md` (stages 2–3), `_context.md` (SD-1..SD-7), `_requirements.md` | **Feeds into:** `17-user-stories.md` (consolidation), `18-nfr.md`, `20-error-states.md`, `22-assumption-closure.md`, `23-product-prd.md`

> **Scope of this part.** EPIC 2 Personalised Packages & Transparent Cost (CO-01, CO-02, CO-03), EPIC 3 In-Session Decision & Identity — **critical path** (AP-01 to AP-07; AP-08/AP-09 parked with their v1 minimum written into AP-02/AP-01 stories), EPIC 8 Merchant & Partner Integration (MX-01; MX-02 parked). Story IDs **US-100 to US-199**.
> - **Part A** (EP-0, EP-7; US-001–099) provides: CCP generator & API (E-05), hybrid scoring (E-06), decision-tier policy layer + rate grid (E-07), profile refresh (E-08), data ingestion incl. CIC client (E-02), per-purpose consent ledger SC/MK/RF (E-01), reason-code generator (E-09), officer workbench (OP-01), model governance / decision log (GV-01), policy console (OP-02). Cross-part dependencies below are cited **by feature code**, because part A story IDs are assigned in parallel.
> - **Part C** (EP-1, EP-4, EP-5, EP-6; US-200–299) provides: AW-04 offer card (an entry point into Flow 2), RT-02 anti-stacking gate (called in Flow 3), PR-04 CIC reporting and "My plans" schedule (after booking), PR-03 graduation (the "grow" half of low-and-grow), AD-01 invite link (an entry point into the Lane B flow).
>
> **Zero-invention rule for policy numbers.** Every HLB risk-appetite value marked `[DECISION REQUIRED]` in Skill 13 (OQ-13-02) or Skill 10 is written as a **named configurable parameter** (catalogue in §0.3). ACs test *behaviour against the parameter*, never an invented value. Values the design *does* give (tenors, 30-minute hold, 3 eKYC attempts, 30-day CIC cache, 2-business-hour SLA, p50/p90 latency targets, 1.6M–90.1M VND pilot financing range) are used as defaults and cited.
>
> **Grounding.** Format follows `00-case-studies/by-output-type/user-story-examples.md` (INVEST, concrete Given/When/Then). Integration edge-case patterns (idempotency keys, server-side status verification, signed webhooks, deep-link web fallback) are grounded in public payment/BNPL integration documentation (Source Index [US-B1]–[US-B6]).

---

## 0. Conventions

### 0.1 Personas used in this part

| Persona | Who | Source |
|---|---|---|
| **Anh Hùng** | Ride-hailing/delivery driver, GIG segment, **new to HLB (Lane B)**, typically F3 = THIN | Skill 05 Persona 1; Skill 15 Lane B; D-08-1 |
| **Existing HLB customer** | HLB account-holder with no PoP history, typically SAL-N (**Lane A**), profiled in advance | Skill 15 Lane A; D-08-1 |
| **Chị Lan** | HLB credit-risk / underwriting lead acting on the refer band (credit officer role) | Skill 05 Persona 4; Skill 15 step 3.R |
| **Partner merchant (checkout owner)** | Store / e-commerce / merchant-app owner who offers "Pay over time with HLB" | Skill 08 MX-01; Skill 13 IA §3. **No Skill 05 persona card exists** (OQ-06 in `_context.md` left it optional) → OQ-17B-01 |
| **Merchant integration developer** | Engineer at the partner who integrates widget / hosted page / QR | Skill 13 §4 (API keys + mTLS, A-13-11). Role persona only → OQ-17B-01 |
| **HLB PoP product owner / fraud & risk analysts** | Internal owners of KR 1.1, 1.2, 3.2, G-2, G-4 dashboards | Skill 10 §4 Measurement Plan (owners) |

### 0.2 Shared definitions (from Skill 13)
- **CCP fields:** F1 Risk Grade (A–E), F2 Affordability Headroom (VND/month, max new instalment), F3 Evidence Confidence (BUREAU+BANK / BANK-OBSERVED / CONSENTED-PARTNER / THIN), F4 Propensity & Need, F5 Integrity (0–100 + flags), F6 Decision Tier (PRE-APPROVED / INSTANT-APPROVE / REFER / DECLINE + reason codes + path).
- **Consent purposes:** SC scoring (mandatory to apply), MK marketing (optional), RF referral (optional) — E-01, C1.
- **Tier policy** (§1.1) is evaluated by the part-A policy layer (E-07); stories here consume its output and never re-implement thresholds.
- **Financed amount** = basket value − chosen down-payment.

### 0.3 Configurable parameters referenced by ACs

| Parameter | Value used in ACs | Status / source |
|---|---|---|
| `package.tenors_months` | {3, 6, 9, 12, 18, 24} | Given [Brief p.9] |
| `package.min_count` / `package.max_count` | 2 / 4 | Given (Skill 13 Flow 2) |
| `package.hold_minutes` | 30 | ASSUMPTION A-13-6 |
| `package.down_payment_options` | set of options | `[DECISION REQUIRED]` → OQ-17B-04 |
| `policy.min_financed_vnd` / `policy.max_financed_vnd` | 1,600,000 / 90,100,000 | Pilot baseline [Brief p.9]; confirm it is the v1 product range → OQ-17B-05 |
| `pricing.rate_grid` (per F1 grade × tenor) | published grid | Values `[DECISION REQUIRED]` (DD-10, OQ-13-02) |
| `pricing.fee_schedule`, `pricing.late_payment_terms`, `pricing.early_settlement_terms` | per product | Values `[DECISION REQUIRED]` → OQ-17B-06 |
| `schedule.first_due_rule` | rule for first due date | Not designed → OQ-17B-07 |
| `merchant.zero_pct_eligibility` | per merchant, set by HLB staff in v1 | OQ-16-02 |
| `merchant.supports_refer_hold` | per merchant true/false | A-13-7 → OQ-13-03 |
| `policy.integrity_floor` | F5 threshold | `[DECISION REQUIRED]` (OQ-13-02) |
| `policy.grade_limit_vnd[A..E]`, `policy.tenor_limit_factor[tenor]` | limit = min(grade limit, F2 × tenor factor) | `[DECISION REQUIRED]` (§1.1) |
| `policy.starter_limit_vnd` | starter limit | `[DECISION REQUIRED]` (OQ-13-02) |
| `policy.cic_cache_max_age_days` | 30 | Given (Skill 13 Flow 3 error path) |
| `policy.cic_fallback_mode` | REFER or STARTER_CAPPED | `[DECISION REQUIRED]` (Flow 3) |
| `policy.cic_fallback_starter_cap_vnd` | cap below starter limit | `[DECISION REQUIRED]` (Flow 3) |
| `ekyc.max_attempts` | 3 | ASSUMPTION A-13-8 |
| `refer.sla_business_hours` | 2 | KR 1.4 (A-10-4) |
| `ops.business_calendar` | HLB business hours & holidays | `[DECISION REQUIRED]` → OQ-17B-10 |
| `refer.approval_validity` | how long an officer approval stays acceptable | Not designed → OQ-17B-11 |
| `decision.in_session_budget_seconds` | budget after which the slow path triggers | Not designed → OQ-17B-08 |
| `integrity.velocity_rules` | velocity windows / counts | `[DECISION REQUIRED]` → OQ-17B-09 |
| `refer.notify_max_seconds` | max delay from officer decision to customer SMS/in-app | Not designed → OQ-17B-11 |
| `checkout.session_expiry` | merchant checkout-session lifetime | Not designed → OQ-17B-27 |
| `cic.outage_alert_rule` | CIC error/timeout rate that triggers an outage alert | Not designed → OQ-17B-26 |
| `g2.baseline_rate` | manual-process confirmed-fraud baseline per 1,000 loans | `[DECISION REQUIRED: baseline unknown]` (Skill 10 G-2) |
| `mx.webhook_success_alert` | webhook delivery success floor per merchant | Not designed → OQ-17B-29 |

### 0.4 Size key
S / M / L / XL per story, relative within this part. Skill 16 feature bands (S≈4 … XL≈32 person-weeks) are pending Skill 14 re-estimation (OQ-16-01); story points are therefore **not** assigned here (see §6).

---

## 1. EPIC 2 — Personalised Packages & Transparent Cost (Journey stage: Consideration)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 2.1: Personalised package builder [CO-01]
Epic: EP-2 | Label: [v1] | Complexity: M
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### US-100 — See 2–4 packages that fit my affordability (Lane A)
**Feature:** 2.1 Personalised package builder [CO-01] | **Epic:** EP-2 | **Size:** M
**Priority:** Must-have | **Label:** v1

**Story**
As an **existing HLB customer (Lane A)**,
I want to **see 2–4 instalment packages built from my own credit profile when I choose "Pay over time"**,
so that **I only consider plans I can actually afford at my risk-tier price**.

**Acceptance Criteria**

**AC 1:**
Given I am a Lane A customer with SC consent and a current CCP, and my basket's financed amount is within `policy.min_financed_vnd`–`policy.max_financed_vnd` and within my F6 limit,
when I select "Pay over time" at a partner checkout (or from the AW-04 offer card),
then the Offer/Package service reads my CCP under the SC purpose and returns between 2 and 4 packages, each with a tenor from {3, 6, 9, 12, 18, 24} months, a rate taken from `pricing.rate_grid` for my F1 grade, and a monthly instalment ≤ my F2 headroom.

**AC 2:**
Given the package set has been computed,
when any candidate package has a monthly instalment greater than F2 by any amount (including 1 VND),
then that package is excluded from the response and is never rendered (G-4).

**AC 3:**
Given the down-payment options in `package.down_payment_options`,
when packages are built,
then each package states its down-payment amount and the financed amount (basket − down-payment), and no package's financed amount exceeds the F6 limit.

**AC 4 (edge — only one package fits):**
Given only one tenor/down-payment combination satisfies instalment ≤ F2 and financed amount ≤ F6 limit,
when packages are built,
then exactly that one package is shown and the system does **not** add any package above F2 to reach `package.min_count` (OQ-17B-03 records the design's "2–4" as a display target, not a reason to breach G-4).

**AC 5 (negative — no SC purpose):**
Given the consent ledger has no active SC consent for me,
when the Package service requests my CCP,
then the read is refused by the purpose check, no CCP field is used, and I am asked to give SC consent before packages are shown; if I decline, "Pay over time" ends and I return to the merchant's other payment methods.

**Dependencies**
Depends on: Part A E-05 (CCP API with purpose check), E-01 (consent ledger), E-07 (rate grid, limit rule); US-140 (checkout session carries basket & merchant).
Blocks: US-101, US-103, US-104, US-105, US-106, US-109, US-110.

**Open Questions:** OQ-17B-03, OQ-17B-04.
**Notes:** Skill 13 Flow 2 step 2; Skill 15 step 2.1.

---

### US-101 — Get affordable alternatives when my basket does not fit
**Feature:** 2.1 [CO-01] | **Epic:** EP-2 | **Size:** M
**Priority:** Must-have | **Label:** v1

**Story**
As **Anh Hùng or an existing HLB customer**,
I want to **be shown a larger down-payment, a longer tenor or a smaller financed amount when my basket is above my limit or headroom**,
so that **I can still buy responsibly instead of being rejected outright or over-borrowing**.

**Acceptance Criteria**

**AC 1:**
Given my basket's financed amount exceeds my F6 limit, or every package at the default down-payment has an instalment > F2,
when packages are built,
then the system searches the allowed tenors {3…24} and `package.down_payment_options` and shows only combinations with instalment ≤ F2 and financed amount ≤ F6 limit, labelled e.g. "Pay {D} VND now and {I} VND/month for {T} months".

**AC 2:**
Given no combination of allowed tenor and down-payment brings the instalment ≤ F2,
when packages are built,
then no package is shown, and I see the affordability outcome from the policy layer (tier-policy branch "F2 < minimum instalment of cheapest package") with the path "a smaller basket or a larger down-payment may be approved".

**AC 3:**
Given the financed amount would fall below `policy.min_financed_vnd` (1,600,000 VND) after a down-payment option,
when alternatives are generated,
then that option is not offered.

**AC 4 (edge — basket above product maximum):**
Given the basket value minus the largest allowed down-payment still exceeds `policy.max_financed_vnd` (90,100,000 VND),
when I select "Pay over time",
then I see "This basket is above the maximum HLB can finance at checkout" and no package list is built.

**AC 5 (guardrail):**
Given any code path (alternatives, counter-offer, officer approval) produces a package,
when it is sent to any channel,
then a shared server-side validator rejects it if instalment > F2 at that moment and logs a `g4_block` event (feeds US-105).

**Dependencies**
Depends on: US-100. Blocks: US-124 (counter-offer reuses the same alternative generator).

**Open Questions:** OQ-17B-02 ("split basket" in Skill 13 Flow 2 is undefined); OQ-17B-04.
**Notes:** Skill 13 Flow 2 alternative path; Skill 15 step 2.3; G-4 hard rule (0%).

---

### US-102 — See indicative packages before I have a profile (Lane B)
**Feature:** 2.1 [CO-01] | **Epic:** EP-2 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As **Anh Hùng (new to HLB, Lane B)**,
I want to **see indicative instalment ranges before I apply, then my confirmed package after the decision**,
so that **I know roughly what I'd pay without giving my data first, and the final plan is one I can afford**.

**Acceptance Criteria**

**AC 1:**
Given I have no CCP (Lane B) and I select "Pay over time",
when the package screen loads,
then I see indicative, grade-agnostic ranges per tenor (lowest–highest monthly instalment across the published `pricing.rate_grid`) clearly labelled "Indicative — your final plan is confirmed after a quick check", and no personalised limit.

**AC 2:**
Given I select an indicative tenor and proceed,
when the in-session decision (US-112) returns an approve tier with my F2 and F6 limit,
then the confirmed package is re-computed with my grade's rate, and it is shown with its full all-in cost (US-106) before I can accept.

**AC 3 (edge — chosen tenor not affordable after decision):**
Given the decision approves me but the tenor I picked indicatively has an instalment > F2 or a financed amount > my limit,
when the confirmed package is computed,
then I am shown only the approved packages that fit (per US-101 rules) with the message "Your approved plan is different from the estimate", and the indicative choice is never booked.

**AC 4 (negative):**
Given I am on the indicative screen,
when the page renders,
then no CIC pull, CCP build or data read about me has occurred (the CCP is built only after SC consent in US-111).

**Dependencies**
Depends on: US-100 (builder), Part A E-07 (grid). Blocks: US-111, US-112.
**Open Questions:** OQ-17B-12 (basis of "from x VND/month" and indicative ranges).
**Notes:** Skill 13 Flow 2 alternative path "Lane B customer with no CCP yet".

---

### US-103 — Merchant 0% offers, with a safe fallback when promo or grid data is unavailable
**Feature:** 2.1 [CO-01] | **Epic:** EP-2 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As an **existing HLB customer or Anh Hùng**,
I want to **see a 0% package when the merchant subsidises it, and still get standard packages if the promotion data is down**,
so that **I get the best genuine price without the checkout breaking**.

**Acceptance Criteria**

**AC 1:**
Given the merchant has `merchant.zero_pct_eligibility` configured for this basket's category/tenor,
when packages are built,
then a 0% package is included only if its instalment ≤ F2 and financed amount ≤ F6 limit, and it is labelled "0% — interest paid by {merchant}", with any fees still disclosed per US-106.

**AC 2:**
Given the merchant subsidy data cannot be read (timeout or error),
when packages are built,
then packages are built from the standard `pricing.rate_grid` with no 0% offers and the message "Promotional offer unavailable" is shown.

**AC 3:**
Given the published rate grid itself cannot be read,
when packages are requested,
then no package with an unverified rate is shown; the customer sees "We can't show plans right now — please try again or choose another payment method", and a `grid_unavailable` alert is raised.

**AC 4 (negative):**
Given a merchant is not configured for 0%,
when packages are built,
then no package shows a 0% or "interest-free" label.

**Dependencies**
Depends on: US-100; Part A E-07 (grid) / OP-02 (config). Blocks: US-109.
**Open Questions:** OQ-16-02 (0% set-up by HLB staff in v1, merchant portal parked); OQ-17B-06.
**Notes:** Skill 13 Flow 2 error path; DD-10 (0% only where merchant-subsidised).

---

### US-104 — Keep my chosen package for 30 minutes
**Feature:** 2.1 [CO-01] | **Epic:** EP-2 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As **Anh Hùng**,
I want to **come back to the package I chose if my session drops**,
so that **I don't have to start again while I'm standing at the counter**.

**Acceptance Criteria**

**AC 1:**
Given I have selected a package,
when my checkout session times out or I leave the page,
then the selection (merchant, basket, package terms, down-payment) is saved for `package.hold_minutes` = 30 minutes, keyed to my checkout session.

**AC 2:**
Given I return within 30 minutes via the same checkout session / link,
when the package screen reopens,
then my saved package is pre-selected, and for Lane A its instalment is re-checked against my current F2 before I can continue.

**AC 3 (edge — expiry):**
Given 30 minutes have passed,
when I return,
then the saved package is discarded, I see "Your saved plan has expired", and packages are rebuilt from current data.

**AC 4 (edge — profile changed):**
Given my CCP changed within the 30 minutes so the saved package's instalment is now > F2,
when I return,
then the saved package is not offered and I see the rebuilt affordable set (US-101 rules).

**Dependencies**
Depends on: US-100, US-140. Blocks: none.
**Open Questions:** A-13-6 (30 min is a design default).
**Notes:** Skill 13 Flow 2 error path; Skill 15 step 2.4.

---

### US-105 — Measure package acceptance (KR 3.2) and prove zero affordability breaches (G-4)
**Feature:** 2.1 [CO-01] | **Epic:** EP-2 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As the **HLB PoP product owner**,
I want **every package shown, selected and booked to be logged with its affordability check**,
so that **I can report KR 3.2 weekly and demonstrate G-4 = 0%**.

**Acceptance Criteria**

**AC 1:**
Given a package set is rendered,
when it is displayed,
then a `packages_shown` event is logged with session ID, lane (A/B), segment, merchant ID, each package's tenor, instalment, down-payment, rate tier, 0% flag and the F2 value used.

**AC 2:**
Given the customer selects and later accepts (signs) a package,
when those actions occur,
then `package_selected` and `package_accepted` events are logged with the same session and package IDs, so KR 3.2 can be computed both per package shown and per package set shown (OQ-17B-13).

**AC 3 (G-4):**
Given a loan is booked,
when the booking event is written,
then it carries the instalment and the F2 value read at booking, and a daily automated check reports the count of booked loans with instalment > F2; any count > 0 raises a Sev-1 alert to the product owner and credit risk.

**AC 4:**
Given the weekly KR 3.2 report runs,
when it is produced,
then acceptance is split by lane, segment and 0%/non-0%, and excludes test merchants.

**Dependencies**
Depends on: US-100, US-114 (booking event). Blocks: none.
**Notes:** Skill 10 KR 3.2 (target `[DECISION REQUIRED]`), G-4; Measurement Plan: checkout/HLB Connect events, weekly.

---

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 2.2: All-in cost & schedule display [CO-02]
Epic: EP-2 | Label: [v1] | Complexity: S
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### US-106 — See the full cost and schedule of each package before I accept
**Feature:** 2.2 All-in cost & schedule display [CO-02] | **Epic:** EP-2 | **Size:** M
**Priority:** Must-have | **Label:** v1

**Story**
As **Anh Hùng**,
I want to **see exactly what I will pay each month, on which dates, and in total, including every fee and what happens if I pay late**,
so that **I am never surprised the way people are by hidden costs today**.

**Acceptance Criteria**

**AC 1:**
Given a package is displayed (indicative packages excepted, which show ranges only),
when I open it,
then I see: monthly instalment (VND), number of payments, each payment date, **total amount payable** (VND), all-in APR (%), every fee by name and amount from `pricing.fee_schedule`, and the late-payment consequences from `pricing.late_payment_terms`.

**AC 2:**
Given the package shown,
when the total amount payable is computed,
then it equals down-payment + sum of all scheduled instalments + all fees payable under the schedule, and the sum of the displayed instalments reconciles to the displayed total to the VND.

**AC 3:**
Given the late-payment consequences section,
when it renders,
then it states the late fee/charge per `pricing.late_payment_terms` and that repayment behaviour is reported to CIC (PR-04, part C).

**AC 4 (edge — 0% package):**
Given a merchant-subsidised 0% package,
when its cost is shown,
then interest is shown as 0 VND, any fee is still listed, and the APR reflects the fees (it is not labelled 0% APR if fees are payable).

**AC 5 (negative — no cost, no accept):**
Given the all-in cost block for the selected package failed to load,
when I try to proceed,
then the "Accept" / "Continue" control is disabled and I see "We can't show the full cost of this plan right now"; the application cannot be submitted.

**Dependencies**
Depends on: US-100 / US-102 (packages), Part A E-07 (pricing config). Blocks: US-107, US-108, US-109, US-114.
**Open Questions:** OQ-17B-06 (fee / late-fee values), OQ-17B-07 (first due date rule), OQ-17B-14 (APR calculation method for disclosure).
**Notes:** Skill 13 Flow 2 step 3; brief "fair and transparent cost" [Brief p.4]; FM-4 [R41].

---

### US-107 — No bundled add-ons, and proof that the cost was shown before acceptance
**Feature:** 2.2 [CO-02] | **Epic:** EP-2 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As **Chị Lan (credit-risk lead) accountable for responsible lending**,
I want **every acceptance to be provably preceded by the all-in cost screen, with no insurance or add-on bundled**,
so that **HLB meets the EP-2 success signal (100% of acceptances preceded by all-in cost display) and avoids forced-insurance harms**.

**Acceptance Criteria**

**AC 1:**
Given any package in any channel (widget, hosted page, HLB Connect),
when it is displayed,
then no insurance, membership or other add-on product is included, pre-selected or required to obtain the loan.

**AC 2:**
Given the all-in cost block is rendered to the customer,
when it is displayed,
then a `cost_disclosed` event is written containing a hash of the exact disclosed terms (instalment, dates, total, APR, fees, late terms) and a timestamp.

**AC 3:**
Given the customer accepts a package,
when the e-contract is generated (US-114),
then its terms must match the hash of the most recent `cost_disclosed` event for that package; on mismatch, signing is blocked and the customer is shown the updated cost screen again.

**AC 4 (audit):**
Given the daily compliance check runs,
when it compares `package_accepted` events to `cost_disclosed` events,
then 100% of acceptances have a prior matching disclosure; any exception is reported to compliance.

**Dependencies**
Depends on: US-106. Blocks: US-114.
**Notes:** Skill 13 §6 exclusion (no bundled insurance); DD-10; Skill 16 EP-2 success signal.

---

### US-108 — Know early-settlement terms up front
**Feature:** 2.2 [CO-02] | **Epic:** EP-2 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As an **existing HLB customer**,
I want to **see what happens if I pay off early before I accept**,
so that **the terms I see later when I settle early are the ones I agreed to**.

**Acceptance Criteria**

**AC 1:**
Given a package's all-in cost screen,
when I expand "Paying early",
then I see the early-settlement terms from `pricing.early_settlement_terms` for this package (any fee, and how remaining interest is treated).

**AC 2:**
Given I accept the package,
when the loan is booked,
then the early-settlement terms are stored with the loan record, so the servicing flow (Skill 13 Flow 4 "Customer repays early", part C) displays exactly these stored terms.

**AC 3 (negative):**
Given `pricing.early_settlement_terms` is not configured for a product,
when packages for that product are built,
then the package is not offered (a package without disclosed early-settlement terms cannot be shown).

**Dependencies**
Depends on: US-106. Blocks: none (consumed by part C servicing).
**Open Questions:** OQ-17B-06.
**Notes:** Skill 13 Flow 4 alternative path references "the early-settlement terms disclosed in CO-02".

---

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 2.3: Side-by-side package comparison [CO-03]
Epic: EP-2 | Label: [v1-Should] | Complexity: S | Condition: after 2.1 stable
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### US-109 — Compare packages side by side
**Feature:** 2.3 Side-by-side package comparison [CO-03] | **Epic:** EP-2 | **Size:** S
**Priority:** Should-have | **Label:** v1-Should

**Story**
As **Anh Hùng**,
I want to **compare my packages side by side on the key numbers**,
so that **I can pick the plan that best fits how my income comes in**.

**Acceptance Criteria**

**AC 1:**
Given 2–4 packages are shown,
when I open "Compare",
then I see one column per package with the same rows: tenor, down-payment, monthly instalment, total payable, all-in APR, total fees, and 0% label where applicable.

**AC 2:**
Given the comparison view,
when it renders,
then the values are identical to each package's all-in cost screen (US-106) — same source, no recomputation.

**AC 3 (edge — single package):**
Given only one package is available (US-100 AC 4),
when the package list renders,
then the "Compare" control is hidden.

**AC 4 (accessibility / mobile):**
Given a 360-px-wide phone screen,
when I compare 3–4 packages,
then all rows remain readable without horizontal page scroll (columns scroll within the table or stack), meeting WCAG 2.1 AA contrast.

**Dependencies**
Depends on: US-100, US-106, US-103. Blocks: none.
**Notes:** Skill 08 CO-03 (Should, after CO-01 stable); Skill 15 step 2.2.

---

## 2. EPIC 3 — In-Session Decision & Identity (Journey stage: Application & Instant Decision) — CRITICAL PATH

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 3.1: In-session decision [AP-01]
Epic: EP-3 | Label: [v1] | Complexity: L | RICE 18.75 (Value rank #1)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### US-110 — Confirm a package and get an instant decision (Lane A)
**Feature:** 3.1 In-session decision [AP-01] | **Epic:** EP-3 | **Size:** M
**Priority:** Must-have | **Label:** v1

**Story**
As an **existing HLB customer with a pre-approved limit**,
I want to **confirm my package with my face and get approved in the same checkout session**,
so that **I finish my purchase in minutes instead of waiting 1.8–4.6 business days**.

**Acceptance Criteria**

**AC 1:**
Given I am a Lane A customer whose CCP tier is PRE-APPROVED with limit L, and I have selected a package with financed amount ≤ L,
when I confirm the package and pass biometric face match (US-119),
then CreditPulse re-validates F5 and F2, calls the RT-02 CIC stacking check, and, if all pass, returns INSTANT-APPROVE for that package.

**AC 2:**
Given INSTANT-APPROVE is returned,
when I proceed,
then I am taken directly to e-contract signing (US-114) without entering any income, employment or document information.

**AC 3 (edge — F2 dropped since pre-approval):**
Given F2 re-validated at checkout is now lower than the selected package's instalment,
when the decision runs,
then the selected package is not approved; I am shown the re-built affordable packages (US-101 rules) with "Your plan has been updated to fit your current finances", and I must re-confirm.

**AC 4 (edge — stacking check fails):**
Given RT-02 reports a stacking signal,
when the decision runs,
then no INSTANT-APPROVE is issued; the policy layer returns the outcome with a stacking reason code, and I see that outcome (tier per OQ-17B-15).

**AC 5 (edge — integrity):**
Given re-validated F5 < `policy.integrity_floor`,
when the decision runs,
then the tier is REFER (identity review) and US-122 / US-124 apply.

**Dependencies**
Depends on: US-100, US-119; Part A E-05, E-07, E-09; Part C RT-02. Blocks: US-114, US-117.
**Open Questions:** OQ-17B-15.
**Notes:** Skill 13 Flow 3 Lane A happy path; Skill 15 steps 3.1(A)–3.2(A).

---

### US-111 — Apply with minimal fields and clear consent choices (Lane B)
**Feature:** 3.1 [AP-01] | **Epic:** EP-3 | **Size:** M
**Priority:** Must-have | **Label:** v1

**Story**
As **Anh Hùng, new to HLB**,
I want to **apply with just my phone number, my chip-ID card, one number for my usual monthly income, and my consent choices — no payslip, no documents, no reference contacts**,
so that **the way I earn doesn't stop me from applying**.

**Acceptance Criteria**

**AC 1:**
Given I have chosen a package (indicative) at a partner checkout,
when the application form loads,
then it asks only for: mobile phone number (OTP-verified), chip-CCCD NFC scan with liveness selfie (US-118), **one self-declared monthly income figure (number only, no proof requested; used for F2_thin per Skill 13 fix 2026-09-30, A-13-12)** and consent choices. It contains no document upload control, no employment-proof field and no reference-contact or guarantor field.

**AC 2:**
Given the consent step,
when it renders,
then SC (scoring) is presented as required to apply, and MK (marketing) and RF (referral) are presented as separate, optional, **not pre-selected** choices, each with a plain-language purpose statement; each choice is written to the consent ledger (E-01) with timestamp and purpose.

**AC 3 (negative — SC refused):**
Given I do not give SC consent,
when I try to continue,
then no CIC pull, CCP build or scoring occurs, and I see "We need your permission to assess your application — without it we can't offer pay-over-time"; I can return to the merchant's other payment methods.

**AC 4 (fairness of optional consent):**
Given two otherwise identical applications that differ only in MK/RF choices,
when they are decided,
then the tier, limit and packages are identical (MK and RF are never inputs to scoring).

**AC 5 (edge — invite link entry):**
Given I arrived through a friend's AD-01 invite link,
when I apply,
then the same Lane B form and my own SC consent apply, and no data about the inviter is read to score me.

**Dependencies**
Depends on: US-102; Part A E-01 (consent ledger). Blocks: US-112, US-118.
**Notes:** Skill 13 Flow 3 Lane B step 1; R-3 (no document friction); DD-08 (no reference contacts); Skill 15 step 3.1(B).

---

### US-112 — Get a decision built from my new profile while I'm still at the checkout (Lane B)
**Feature:** 3.1 [AP-01] | **Epic:** EP-3 | **Size:** L
**Priority:** Must-have | **Label:** v1

**Story**
As **Anh Hùng**,
I want **HLB to build my credit profile from what it can verify right now and tell me yes, no or "a little longer" before I leave the checkout**,
so that **I can buy what I came for today**.

**Acceptance Criteria**

**AC 1:**
Given I have given SC consent and passed eKYC (US-118),
when I submit the application,
then CreditPulse requests a new CCP from the part-A CCP API using CIC (S14), checkout footprint (S13) and application data (plus S16 Open API data only if I opted in and Phase 2 is live), and returns F1–F6 with F3 set per the source rule (typically THIN).

**AC 2:**
Given the CCP is built,
when the policy layer (E-07) evaluates the tier policy,
then exactly one tier is returned — INSTANT-APPROVE (standard or at starter limit), REFER or DECLINE — together with the approved limit, the affordable package set and reason codes, and it is shown to me in the same session.

**AC 3:**
Given the tier is INSTANT-APPROVE,
when the result is displayed,
then I see my confirmed package(s) with full all-in cost (US-106) and can proceed to CASA-first (US-129) and e-contract (US-114).

**AC 4 (edge — returning customer detected):**
Given the CCCD number read from my chip matches an existing HLB customer record,
when the application is submitted,
then the flow switches to Lane A handling (read existing CCP under SC purpose; face match against CCCD on file) rather than creating a duplicate customer.

**AC 5 (negative — no documents fallback):**
Given the evidence is insufficient for an automated approval,
when the policy layer runs,
then the system never asks me to upload documents; the outcome is REFER or DECLINE per policy.

**Dependencies**
Depends on: US-111, US-118, US-121; Part A E-02 (CIC client, S13 ingestion), E-05, E-06, E-07, E-09. Blocks: US-113, US-114, US-115, US-116, US-124, US-128, US-129, US-130.
**Open Questions:** OQ-17B-16 (duplicate-customer match rule).
**Notes:** Skill 13 Flow 3 Lane B steps 2–4; DF-2; Skill 15 step 3.2(B) (AHA moment).

---

### US-113 — Every decision returns a complete, logged, explainable result
**Feature:** 3.1 [AP-01] | **Epic:** EP-3 | **Size:** M
**Priority:** Must-have | **Label:** v1

**Story**
As **Chị Lan (credit-risk lead)**,
I want **every checkout decision to return and log the tier, packages, reason codes and the model and policy versions used**,
so that **any decision can be explained to the customer, reviewed by an officer and audited under the AI Law**.

**Acceptance Criteria**

**AC 1:**
Given any checkout decision (Lane A or B, any tier),
when the decision service responds,
then the response contains: decision ID, tier, approved limit (if any), package set (if any), reason codes from E-09, and CCP version ID.

**AC 2:**
Given the decision is made,
when it is persisted,
then the decision log entry holds an inputs-snapshot hash, champion/challenger model versions, policy-rule version, tier, reason codes, lane, segment, merchant ID and timestamps (Skill 13 §4 decision log).

**AC 3 (negative — missing reason code):**
Given the reason-code generator returns no code for a decision,
when the response is assembled,
then the decision is not released to the customer as approve/decline; it is routed to REFER with reason "explanation unavailable" and an alert is raised (KR 4.1 = 100% decisions with reason codes).

**AC 4 (data minimisation to merchant):**
Given a decision response is forwarded to the merchant (US-144),
when the merchant payload is built,
then it contains only decision status, decision ID and the accepted package summary — never F1–F5 values, reason codes or CIC data.

**Dependencies**
Depends on: US-112 / US-110; Part A E-09, GV-01. Blocks: US-115, US-117, US-125, US-144.
**Open Questions:** OQ-17B-17 (confirm merchant payload excludes reason codes).
**Notes:** KR 4.1; Skill 13 §4 Database/storage "Decision log"; staff/data minimisation lesson [R71].

---

### US-114 — Sign the e-contract with OTP and complete my order
**Feature:** 3.1 [AP-01] | **Epic:** EP-3 | **Size:** M
**Priority:** Must-have | **Label:** v1

**Story**
As **Anh Hùng or an existing HLB customer who has been approved**,
I want to **sign the contract with an OTP and have the merchant confirm my order immediately**,
so that **I leave with my purchase**.

**Acceptance Criteria**

**AC 1:**
Given I have an approved package whose terms match the latest `cost_disclosed` hash (US-107),
when I review the e-contract,
then it shows the same instalment, dates, total payable, APR, fees, late and early-settlement terms, and an OTP is sent to my verified phone number.

**AC 2:**
Given I enter the correct OTP,
when I submit,
then the loan is booked in HLB core banking, a payment guarantee is issued to the merchant, the merchant receives an "approved" confirmation (US-144), and I see "Your order is confirmed".

**AC 3 (final affordability gate):**
Given the booking step,
when the booking service re-reads F2,
then booking is blocked if instalment > F2 at that moment (G-4 hard rule), and I am returned to the affordable package set.

**AC 4 (edge — OTP):**
Given I enter a wrong or expired OTP,
when I submit,
then signing is refused with "The code is incorrect or expired — request a new code"; the loan is not booked; OTP attempt and expiry limits follow HLB's existing OTP policy.

**AC 5 (edge — duplicate submit):**
Given I tap "Sign" twice or the network retries the request,
when the booking service receives the duplicate,
then exactly one loan is booked (idempotent on decision ID + package ID).

**Dependencies**
Depends on: US-106, US-107, US-110 / US-112; Part A E-05; core banking APIs (A-13-10). Blocks: US-105 (booking event), US-117, US-144; Part C PR-04 (CIC reporting after booking).
**Open Questions:** OQ-17B-18 (merchant settlement mechanics — merchant portal parked).
**Notes:** Skill 13 Flow 3 Lane A step 4 / Lane B step 5; Skill 15 step 3.4; idempotency pattern [US-B1][US-B2].

---

### US-115 — Understand why I was declined
**Feature:** 3.1 [AP-01] (v1 minimum of parked AP-09) | **Epic:** EP-3 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As **Anh Hùng**,
I want to **see the main reasons for a decline and a general path forward**,
so that **I'm not left with an opaque "no"**.

**Acceptance Criteria**

**AC 1:**
Given the tier is DECLINE,
when the outcome screen renders,
then I see the top reason codes returned by E-09, in plain Vietnamese customer-facing wording from the reason-code catalogue.

**AC 2:**
Given the decline reason is affordability,
when the outcome screen renders,
then the generic path "A smaller basket or a larger down-payment may be approved" is shown, with a button back to the package screen.

**AC 3:**
Given any decline,
when I later open HLB Connect "Pay Later → Why this decision" (if I have HLB Connect),
then the same reason codes for my latest decision are shown.

**AC 4 (scope guard):**
Given AP-09 (rich path-to-yes) is parked,
when the decline screen renders,
then no personalised improvement plan, score simulator or third-party lender referral is shown.

**AC 5 (integrity decline wording):**
Given the decline is due to a confirmed identity mismatch (US-122),
when the outcome renders,
then the reason is shown as "We couldn't verify your identity" without revealing which fraud signal triggered.

**Dependencies**
Depends on: US-112, US-113; Part A E-09. Blocks: none.
**Notes:** Skill 13 Flow 3 error path "DECLINE"; Skill 15 step 3.D; PL-05 parked.

---

### US-116 — Meet the in-session latency target, and handle slow decisions gracefully
**Feature:** 3.1 [AP-01] | **Epic:** EP-3 | **Size:** M
**Priority:** Must-have | **Label:** v1

**Story**
As **Anh Hùng standing at the checkout**,
I want **the decision to come back within about three minutes and to know what's happening while I wait**,
so that **I don't abandon the purchase or leave not knowing the outcome**.

**Acceptance Criteria**

**AC 1 (performance target):**
Given production traffic over any rolling 7-day window,
when application-submit-to-decision time is measured,
then p50 ≤ 3 minutes and p90 ≤ 10 minutes (KR 1.2; A-10-3), measured separately for Lane A and Lane B.

**AC 2:**
Given a decision is in progress,
when I am waiting,
then I see a progress state with the step in plain words (e.g. "Verifying your ID", "Checking your credit record") and no dead-end spinner.

**AC 3:**
Given a CIC pull is still valid in cache for a Lane A customer,
when the decision runs,
then the cached pull is used asynchronously and no fresh synchronous CIC call blocks the decision (DF-2 "async where cached is valid").

**AC 4 (edge — budget exceeded):**
Given the decision has not completed within `decision.in_session_budget_seconds`,
when that budget elapses,
then the application is not silently dropped: it is handled per OQ-17B-08 (routed to the refer path with customer and merchant notified per US-124/US-144, or an agreed alternative), and a `decision_budget_exceeded` event is logged.

**AC 5 (alerting):**
Given p90 exceeds 10 minutes for 1 hour,
when the monitor evaluates,
then platform engineering is alerted (Skill 10 §4 alert threshold).

**Dependencies**
Depends on: US-112, US-117. Blocks: none.
**Open Questions:** OQ-17B-08, OQ-17B-19 (whether KR 1.2 "submit" for Lane B starts before or after eKYC).
**Notes:** Skill 13 DF-2 target p50 ≤ 3 min end-to-end; eKYC ~80 s [R67].

---

### US-117 — Instrument in-session rate (KR 1.1) and time to decision (KR 1.2)
**Feature:** 3.1 [AP-01] | **Epic:** EP-3 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As the **HLB PoP product owner**,
I want **every application to emit timestamped lifecycle events with an in-session flag**,
so that **KR 1.1 (daily) and KR 1.2 (real-time) can be reported and alerted on from the decision-engine event log**.

**Acceptance Criteria**

**AC 1:**
Given any application,
when it progresses,
then events `application_started`, `ekyc_started`, `ekyc_completed`, `application_submitted`, `decision_returned`, `contract_signed` and `session_ended` are emitted with application ID, lane, segment, merchant ID, channel (widget / hosted / QR / HLB Connect) and server timestamps, so KR 1.2 can be computed under either definition in OQ-17B-19.

**AC 2 (KR 1.1 classification):**
Given a decision,
when it is logged,
then `final_in_session` = true only if a final approve (including an accepted counter-offer) or decline was returned before `session_ended`; REFER cases (including eKYC-failure and CIC-fallback refers) are false.

**AC 3:**
Given the daily KR 1.1 job runs,
when it computes the rate,
then the denominator is submitted applications, abandoned-after-submit applications are counted as not in-session and reported separately, and results are split by lane and segment.

**AC 4 (alerts):**
Given the KR 1.1 rate is < 60% for 3 consecutive days,
when the monitor evaluates,
then the decision-engine product owner is alerted (Skill 10 §4).

**Dependencies**
Depends on: US-110, US-112, US-114. Blocks: US-116 (latency monitor), US-145.
**Open Questions:** OQ-17B-19, OQ-17B-20 (operational definition of "checkout session ended").
**Notes:** Skill 10 KR 1.1 (target ≥ 70%, A-10-2) and KR 1.2.

---

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 3.2: Chip-CCCD / VNeID biometric eKYC (reuse HLB capability) [AP-02]
Epic: EP-3 | Label: [v1] | Complexity: S
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### US-118 — Verify my identity with my chip-ID card and a selfie (Lane B)
**Feature:** 3.2 [AP-02] | **Epic:** EP-3 | **Size:** M
**Priority:** Must-have | **Label:** v1

**Story**
As **Anh Hùng**,
I want to **tap my chip-CCCD on my phone and take a liveness selfie instead of photographing documents**,
so that **HLB knows it's really me in about a minute and a half**.

**Acceptance Criteria**

**AC 1:**
Given I am in the Lane B application on an NFC-capable phone,
when I hold my chip-CCCD to the phone,
then HLB's existing eKYC capability reads the chip data, verifies it against the National ID / VNeID integration, runs liveness and face match against the chip photo, and returns pass/fail plus match scores to the application.

**AC 2:**
Given eKYC passes,
when the result is returned,
then name, date of birth and CCCD number are pre-filled from the chip (not typed), and the eKYC result is passed to the integrity check (US-121) as an F5 input.

**AC 3 (performance):**
Given production traffic,
when eKYC duration (ekyc_started → ekyc_completed) is measured,
then its median is reported against HLB's current ~80-second benchmark [R67] on the KR 1.2 dashboard.

**AC 4 (edge — no NFC):**
Given my phone has no NFC or NFC is switched off,
when I reach the chip-scan step,
then I see instructions to enable NFC; if the device has no NFC, the attempt counts as a failed attempt under US-120 (full assisted fallback is parked, PL-04).

**AC 5 (negative — no uploads):**
Given any eKYC outcome,
when the flow continues,
then I am never asked to upload or photograph a document.

**Dependencies**
Depends on: US-111; existing HLB eKYC service [R67]. Blocks: US-112, US-120, US-121.
**Open Questions:** OQ-17B-21 (confirm HLB's eKYC SDK can run inside a merchant web checkout / hosted page, or only in HLB Connect).
**Notes:** Skill 13 Flow 3 Lane B step 2; R-3; Decree 69/2024 [Skill02 RF-6].

---

### US-119 — Confirm with my face against the ID HLB already holds (Lane A)
**Feature:** 3.2 [AP-02] | **Epic:** EP-3 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As an **existing HLB customer**,
I want to **confirm my purchase with a face match against the chip-CCCD already on file**,
so that **I don't re-enter identity details HLB already has**.

**Acceptance Criteria**

**AC 1:**
Given my HLB customer record holds a chip-CCCD biometric reference,
when I confirm a package,
then a liveness + face match against that reference runs in HLB Connect (or the hosted page), and on pass the decision proceeds (US-110).

**AC 2:**
Given the face match passes,
when the decision request is sent,
then the biometric result and its timestamp are attached as an F5 input and to the decision log.

**AC 3 (edge — no chip reference on file):**
Given my record has no chip-CCCD biometric reference,
when I confirm,
then I am routed to the chip-CCCD NFC + liveness flow (US-118) before the decision.

**AC 4 (edge — failure):**
Given the face match fails,
when I retry,
then US-120 attempt counting applies.

**Dependencies**
Depends on: existing HLB biometric authentication [R67]. Blocks: US-110.
**Notes:** Skill 13 Flow 3 Lane A step 2; Circular 50/2024 biometric requirements [Skill02 RF-5].

---

### US-120 — Up to 3 eKYC attempts, then human identity review (v1 minimum of parked AP-08)
**Feature:** 3.2 [AP-02] | **Epic:** EP-3 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As **Anh Hùng whose phone struggles to read my card or recognise my face**,
I want to **retry a few times and then have a person check my identity rather than be declined**,
so that **a technical failure doesn't cost me the purchase or count against me**.

**Acceptance Criteria**

**AC 1:**
Given an eKYC attempt fails (NFC read failure, liveness fail or face not recognised),
when the result is returned,
then I see the specific failure type in plain words and a "Try again" option, and the attempt counter increments.

**AC 2:**
Given I have failed `ekyc.max_attempts` = 3 attempts in this application,
when the third attempt fails,
then no further attempts are offered, and the application goes to REFER with reason code "identity review" (US-124 / US-125 path).

**AC 3 (negative — not a decline):**
Given the application was referred for eKYC failure,
when the tier is recorded,
then it is REFER, not DECLINE, and no risk-grade (F1) penalty is applied for eKYC failures.

**AC 4 (instrumentation):**
Given any eKYC attempt,
when it completes,
then an `ekyc_attempt` event records attempt number, failure type, device OS/model class and channel, so HLB can size the parked assisted-fallback feature (PL-04 revisit condition: HLB eKYC failure data).

**AC 5 (scope guard):**
Given AP-08 is parked,
when attempts are exhausted,
then no assisted video call, branch-booking or document-upload fallback is offered in v1.

**Dependencies**
Depends on: US-118, US-119. Blocks: US-125.
**Open Questions:** A-13-8 (3 retries is a design default).
**Notes:** Skill 13 Flow 3 error path "eKYC fails"; face-not-recognised pattern [Skill03 S44].

---

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 3.3: Application-integrity checks → F5 integrity score [AP-03]
Epic: EP-3 | Label: [v1] | Complexity: M
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### US-121 — Score every application's integrity in real time
**Feature:** 3.3 [AP-03] | **Epic:** EP-3 | **Size:** M
**Priority:** Must-have | **Label:** v1

**Story**
As **Chị Lan (credit-risk lead)**,
I want **every application and every draw to be scored for identity and application integrity before a tier is set**,
so that **HLB can approve in seconds without letting impostors or fake applications through (R-3)**.

**Acceptance Criteria**

**AC 1:**
Given an application or a Lane A draw,
when integrity checks run,
then F5 (0–100) and flags are computed from: eKYC result, device signals, velocity rules (`integrity.velocity_rules`), identity-data consistency (chip data vs entered phone and application data), CIC identity match, and the integrity anomaly model; each triggered flag is named (e.g. ID-mismatch, device-velocity).

**AC 2:**
Given F5 is computed,
when the CCP is written,
then F5 and flags are stored with the CCP version and passed to the policy layer before any tier is returned.

**AC 3 (excluded data):**
Given the device-signal collector,
when it runs,
then it never reads phone contacts, photo gallery, SMS inbox or social-media data (X-01), and it never uses gender, ethnicity, religion or region as an input.

**AC 4 (edge — integrity service unavailable):**
Given the integrity model is unavailable,
when a decision is requested,
then no automated approval is issued without an F5 value; the case goes to REFER with reason "identity review" and an alert is raised.

**Dependencies**
Depends on: US-118 / US-119; Part A E-02 (signal ingestion), E-05, E-06 (anomaly model). Blocks: US-110, US-112, US-122.
**Open Questions:** OQ-17B-09.
**Notes:** Skill 13 §1.1 F5; S11, S13, S14 signals; §6 exclusions.

---

### US-122 — Route low-integrity applications to review, and confirmed mismatches to decline
**Feature:** 3.3 [AP-03] | **Epic:** EP-3 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As **Chị Lan**,
I want **low-integrity applications held for identity review and confirmed mismatches declined and reported to the fraud team**,
so that **genuine customers aren't auto-declined for a noisy signal while fraud is stopped**.

**Acceptance Criteria**

**AC 1:**
Given F5 < `policy.integrity_floor` and no confirmed mismatch,
when the policy layer runs,
then the tier is REFER with reason code "identity review", regardless of F1/F2, and no counter-offer is made until identity is resolved.

**AC 2:**
Given a confirmed identity mismatch (per the mismatch definition in OQ-17B-22),
when the policy layer runs,
then the tier is DECLINE and a `fraud_event` is logged to the fraud team queue with application ID, flags and evidence snapshot.

**AC 3:**
Given F5 ≥ `policy.integrity_floor`,
when the policy layer runs,
then the tier is determined by the remaining tier-policy rules (F2, F1, F3) unchanged by F5.

**AC 4 (negative — no disclosure of signals):**
Given a REFER or DECLINE for integrity,
when the customer and merchant are informed,
then neither sees which integrity signal fired (US-115 AC 5).

**Dependencies**
Depends on: US-121; Part A E-07. Blocks: US-123, US-125.
**Open Questions:** OQ-17B-22.
**Notes:** Skill 13 §1.1 first rule; Flow 3 error path "Integrity flag".

---

### US-123 — Measure confirmed fraud per 1,000 approved loans (G-2)
**Feature:** 3.3 [AP-03] | **Epic:** EP-3 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As an **HLB fraud / risk analyst**,
I want **confirmed fraud outcomes linked back to the original decision**,
so that **I can prove the G-2 guardrail — fraud not rising versus the manual baseline — and tune the integrity floor**.

**Acceptance Criteria**

**AC 1:**
Given the fraud team confirms a case as identity/application fraud,
when they record the outcome,
then it is linked to the decision ID and CCP version, with lane, channel, merchant ID, F5 value and flags at decision time.

**AC 2:**
Given the monthly G-2 report runs,
when it computes the rate,
then it reports confirmed fraud per 1,000 approved loans, split by lane, channel and segment, and compares it with the manual baseline parameter `g2.baseline_rate` (`[DECISION REQUIRED: baseline unknown]`, Skill 10).

**AC 3:**
Given the rate exceeds `g2.baseline_rate`,
when the report runs,
then an alert goes to credit risk and, per Skill 10 §3, the affected segment's or channel's auto-approval is flagged for pause pending review.

**AC 4 (edge — fraud on a referred case):**
Given a case approved by an officer later proves fraudulent,
when it is recorded,
then it is attributed to the officer-decision path (not the automated path) so both rates are visible separately.

**Dependencies**
Depends on: US-113, US-122; Part A GV-01. Blocks: none.
**Notes:** Skill 10 G-2; FM-11.

---

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 3.4: Refer-band handling with counter-offer and SLA [AP-04]
Epic: EP-3 | Label: [v1] | Complexity: M
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### US-124 — Get an immediate counter-offer when my application needs review
**Feature:** 3.4 [AP-04] | **Epic:** EP-3 | **Size:** M
**Priority:** Must-have | **Label:** v1

**Story**
As **Anh Hùng whose application lands in the refer band**,
I want to **be offered a smaller or shorter plan I can accept right now, or told clearly when I'll hear back**,
so that **I can still buy today or know exactly what happens next**.

**Acceptance Criteria**

**AC 1:**
Given the tier is REFER for a credit reason (not identity review),
when the outcome screen renders,
then I see "We need a little longer to review — we'll reply within 2 business hours" (`refer.sla_business_hours`), and, where the policy layer finds an affordable counter-offer (lower amount, shorter tenor or larger down-payment, generated by the US-101 rules), it is shown with full all-in cost (US-106).

**AC 2:**
Given I accept the counter-offer,
when I proceed,
then the flow continues on the happy path (e-contract, US-114), the application is recorded as final in-session (US-117 AC 2), and no officer case is created.

**AC 3:**
Given I decline the counter-offer, or none is affordable,
when I choose "Send for review",
then a refer case is created (US-125) and I see the SLA message and how I will be notified (in-app and SMS).

**AC 4 (negative — identity refers):**
Given the REFER reason is "identity review" (US-120, US-122),
when the outcome renders,
then no counter-offer is shown; the case goes straight to review.

**AC 5 (guardrail):**
Given any counter-offer,
when it is generated,
then its instalment ≤ F2 and financed amount ≤ approved limit (G-4 validator, US-101 AC 5).

**Dependencies**
Depends on: US-101, US-106, US-112; Part A E-07. Blocks: US-125, US-126.
**Notes:** Skill 13 Flow 3 alternative "REFER tier"; DD-06; Skill 15 step 3.R; lit. review M7.

---

### US-125 — Send my case to an officer with evidence pre-assembled and the merchant holding my goods
**Feature:** 3.4 [AP-04] | **Epic:** EP-3 | **Size:** M
**Priority:** Must-have | **Label:** v1

**Story**
As **Chị Lan (credit officer)**,
I want **each referred case to arrive in my queue with the CCP, sources, reason codes, model version and the counter-offer already tried, and an SLA timer running**,
so that **I review only the uncertain cases, fast, instead of rebuilding files by hand**.

**Acceptance Criteria**

**AC 1:**
Given a refer case is created (US-124 AC 3, US-120 AC 2, US-122 AC 1, US-130),
when it is written,
then the OP-01 queue (part A) receives: decision ID, evidence snapshot (CCP F1–F6 values and sources, reason codes, model and policy versions), eKYC and integrity results, requested package, and counter-offer(s) shown and the customer's response.

**AC 2:**
Given the case is created,
when the SLA timer starts,
then its deadline = creation time + `refer.sla_business_hours` (2) business hours computed on `ops.business_calendar`, and the deadline is shown on the case and sent to the merchant.

**AC 3 (merchant hold):**
Given the merchant has `merchant.supports_refer_hold` = true,
when the case is created,
then the merchant receives a "referred — hold requested until {deadline}" notification (US-144).

**AC 4 (edge — merchant does not hold):**
Given `merchant.supports_refer_hold` = false,
when the customer is about to send the case for review,
then the customer is told before confirming "This store can't hold your item while we review" (handling of the purchase after approval per OQ-13-03 / OQ-17B-23).

**AC 5 (negative — no extra customer burden):**
Given a case is in review,
when the officer needs more information,
then v1 provides no document-upload or reference-contact request channel to the customer (R-3, DD-08).

**Dependencies**
Depends on: US-124; Part A OP-01 (workbench), E-09. Blocks: US-126, US-127.
**Open Questions:** OQ-13-03 / A-13-7, OQ-17B-10, OQ-17B-23.
**Notes:** Skill 13 DF-4; IA §3 officer workbench.

---

### US-126 — Hear the outcome of my review and complete the purchase
**Feature:** 3.4 [AP-04] | **Epic:** EP-3 | **Size:** M
**Priority:** Must-have | **Label:** v1

**Story**
As **Anh Hùng waiting on a review**,
I want **to be notified the moment the officer decides and to be able to sign straight away if approved**,
so that **I still get my item from the merchant**.

**Acceptance Criteria**

**AC 1:**
Given the officer records approve, counter-offer or decline in OP-01,
when the decision is saved,
then an SMS and (if I have HLB Connect) an in-app notification are sent to me within `refer.notify_max_seconds`, and the merchant receives the outcome (US-144); the send timestamp is logged as `customer_notified` (US-127).

**AC 2:**
Given the outcome is approve or counter-offer,
when I open the notification link,
then I see the approved package with full all-in cost (US-106) and can sign with OTP (US-114) while `refer.approval_validity` has not elapsed.

**AC 3 (edge — approval expired):**
Given `refer.approval_validity` has elapsed without signature,
when I open the link,
then I see "This approval has expired" and must start a new application.

**AC 4 (decline):**
Given the officer declines,
when I open the notification,
then I see the reason codes per US-115.

**AC 5 (feedback loop):**
Given any officer outcome,
when it is saved,
then the outcome and the officer's justification (mandatory on override) are logged as a label for champion/challenger evaluation (DF-4).

**Dependencies**
Depends on: US-125; Part A OP-01, GV-01. Blocks: US-127.
**Open Questions:** OQ-17B-11, OQ-17B-24 (completion channel for Lane B customers without HLB Connect).
**Notes:** Skill 13 Flow 3 "customer is notified in-app and by SMS"; DF-4.

---

### US-127 — Measure the refer-band SLA (KR 1.4)
**Feature:** 3.4 [AP-04] | **Epic:** EP-3 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As **Chị Lan (credit-ops lead)**,
I want **daily SLA reporting on referred cases**,
so that **I know whether ≥ 90% are decided within 2 business hours and can staff the queue accordingly**.

**Acceptance Criteria**

**AC 1:**
Given each refer case,
when it is created and decided,
then `case_created`, `case_assigned`, `case_decided` and `customer_notified` timestamps are logged with case reason (credit / identity / CIC-fallback / eKYC).

**AC 2:**
Given the daily KR 1.4 job,
when it runs,
then it reports the share of cases decided within 2 business hours (business-hour arithmetic on `ops.business_calendar`), split by case reason and lane.

**AC 3:**
Given the share is < 90% for the day,
when the job completes,
then the credit-ops lead is alerted (Skill 10 §4).

**AC 4 (refer share):**
Given the same job,
when it runs,
then it also reports refer-band share of submitted applications (A-10-2 assumes ≤ 30%), as the input to KR 1.1 and KR 1.3.

**Dependencies**
Depends on: US-125, US-126. Blocks: none.
**Notes:** Skill 10 KR 1.4 (A-10-4).

---

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 3.5: Starter limit (low-and-grow) for new-to-bank thin files [AP-05]
Epic: EP-3 | Label: [v1] | Complexity: S
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### US-128 — Be approved at a starter limit even though I'm new to credit
**Feature:** 3.5 [AP-05] | **Epic:** EP-3 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As **Anh Hùng with a thin credit file**,
I want **to be approved for a smaller starter amount rather than declined for having no history**,
so that **I can start building a track record with a bank**.

**Acceptance Criteria**

**AC 1:**
Given F5 ≥ `policy.integrity_floor`, F2 ≥ the cheapest package's instalment, F3 = THIN and F1 ∈ {A, B, C},
when the policy layer runs,
then the tier is INSTANT-APPROVE at STARTER LIMIT, with limit = min(`policy.starter_limit_vnd`, the F2-based limit from the approval rule).

**AC 2:**
Given a starter-limit approval,
when packages are shown,
then every package has financed amount ≤ the starter limit and instalment ≤ F2; if the basket exceeds the starter limit, the US-101 alternatives (larger down-payment, smaller financed amount) are offered.

**AC 3 (negative):**
Given F3 = THIN and F1 ∈ {D, E},
when the policy layer runs,
then no starter limit is granted; the tier follows the remaining tier-policy rules (DECLINE with reason codes and path).

**AC 4:**
Given a starter-limit approval,
when the approval screen renders,
then I see "Starter limit — on-time payments can unlock more" and no promise of a specific future limit (graduation is offered later with explicit consent, PR-03 part C).

**AC 5 (instrumentation):**
Given a starter-limit loan is booked,
when it is logged,
then it is tagged `starter_limit = true` so KR 2.1 (thin-file approval at MOB3 ceiling) and KR 2.3 (THIN → BANK-OBSERVED upgrade) can be tracked.

**Dependencies**
Depends on: US-112; Part A E-07. Blocks: none; feeds Part C PR-03.
**Notes:** Skill 13 §1.1 rule 4; Nubank low-and-grow [R1]; Skill 15 AHA step 3.2(B).

---

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 3.6: CASA-first onboarding at approval [AP-06]
Epic: EP-3 | Label: [v1] | Complexity: S
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### US-129 — Open an HLB account at approval to repay from
**Feature:** 3.6 [AP-06] | **Epic:** EP-3 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As **Anh Hùng, newly approved**,
I want to **open an HLB account in the same flow and have it set as my repayment and refund account**,
so that **repaying is simple and HLB can see my income over time to offer me better terms**.

**Acceptance Criteria**

**AC 1:**
Given I am a Lane B applicant with an approve tier (including starter limit) and no HLB CASA,
when the approval screen renders,
then I am offered "Open an HLB account" using my already-verified eKYC data, with no re-entry of identity fields.

**AC 2:**
Given I open the account,
when the loan is booked,
then the new CASA is set as the default repayment and refund account for this loan.

**AC 3 (edge — I decline the account):**
Given I decline to open a CASA,
when I proceed,
then the loan is not blocked on that basis (the design makes CASA opening an offer) and the repayment method follows OQ-17B-25.

**AC 4 (instrumentation, KR 2.2):**
Given a Lane B approval,
when the account offer is shown and answered,
then `casa_offered` and `casa_opened` events are logged so KR 2.2 (CASA-first conversion) can be computed.

**Dependencies**
Depends on: US-112, US-118; HLB account-opening service. Blocks: none; feeds Part A E-02 (future CASA signals).
**Open Questions:** OQ-17B-25.
**Notes:** Skill 13 Flow 3 Lane B step 4; brief mitigation "onboard to CASA early" [Brief p.8].

---

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 3.7: CIC-unavailable fallback rules [AP-07]
Epic: EP-3 | Label: [v1] | Complexity: S
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### US-130 — Keep deciding safely when the credit bureau is down
**Feature:** 3.7 [AP-07] | **Epic:** EP-3 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As **Chị Lan (credit-risk lead)**,
I want **deterministic fallback rules when CIC is unavailable**,
so that **checkout keeps working for well-evidenced customers without taking blind risk on everyone else**.

**Acceptance Criteria**

**AC 1:**
Given CIC is unavailable and the applicant is Lane A with F3 = BUREAU+BANK and a cached CIC pull younger than `policy.cic_cache_max_age_days` = 30 days,
when the decision runs,
then it proceeds on the cached pull and the decision log marks `cic_source = cached` with the pull date.

**AC 2:**
Given CIC is unavailable and the applicant does not meet AC 1 (Lane B, or F3 ≠ BUREAU+BANK, or cache ≥ 30 days old),
when the decision runs,
then the outcome follows `policy.cic_fallback_mode`: REFER with reason "credit bureau unavailable", or INSTANT-APPROVE capped at `policy.cic_fallback_starter_cap_vnd` (values `[DECISION REQUIRED]`).

**AC 3 (negative):**
Given CIC is unavailable,
when any fallback path runs,
then no approval exceeds the fallback cap, and no RT-02 stacking check is reported as passed without CIC data (a cached pull counts as the data source only under AC 1).

**AC 4 (boundary):**
Given a cached pull exactly 30 days old,
when AC 1 is evaluated,
then it is treated as not valid ("under 30 days" per Skill 13).

**Dependencies**
Depends on: US-112, US-131; Part A E-02 (CIC client + cache), E-07. Blocks: none.
**Notes:** Skill 13 Flow 3 error path "CIC unavailable"; FM-12; CIC incident Sep 2025 [R71].

---

### US-131 — Detect CIC outages and flag every fallback decision
**Feature:** 3.7 [AP-07] | **Epic:** EP-3 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As **Chị Lan**,
I want **the system to detect CIC unavailability, switch to fallback automatically and flag every decision made in fallback**,
so that **those decisions can be re-checked when CIC is back**.

**Acceptance Criteria**

**AC 1:**
Given a CIC request times out or errors,
when the CIC client (part A) reports unavailability,
then the decision service switches to US-130 rules for that request and emits a `cic_unavailable` event.

**AC 2:**
Given CIC unavailability events exceed the alerting rule `cic.outage_alert_rule`,
when the monitor evaluates,
then credit risk and platform engineering are alerted.

**AC 3:**
Given CIC becomes available again,
when a loan booked under fallback exists,
then a fresh CIC pull is made for it and any new obligation or stacking signal found is written to the CCP and surfaced to credit risk (no automatic change to the booked loan).

**AC 4 (reporting):**
Given the daily report runs,
when it counts decisions,
then it shows the count and share of fallback decisions by mode (cached / refer / capped).

**Dependencies**
Depends on: Part A E-02 (CIC client). Blocks: US-130.
**Open Questions:** OQ-17B-26 (post-recovery re-check action, not in Skill 13).
**Notes:** Skill 13 §4 External APIs "fallback rules on unavailability".

---

### Parked in EPIC 3 (listed only — `[Later — pending user]`)
- **3.8 AP-08 eKYC failure fallback flows (PL-04).** Parked story stub: *"As a customer whose eKYC keeps failing, I want an assisted path (guided retry, assisted verification) so that I can still complete my application."* v1 minimum is **US-120** (3 attempts → REFER "identity review").
- **3.9 AP-09 Rich decline explanation / path-to-yes (PL-05).** Parked story stub: *"As a declined applicant, I want a personalised explanation of what would change the outcome, so that I know how to get approved next time."* v1 minimum is **US-115** (top E-09 reason codes + generic path).

---

## 3. EPIC 8 — Merchant & Partner Integration (cross-cutting channel)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 8.1: Checkout integration — API widget, hosted page, QR deep link [MX-01]
Epic: EP-8 | Label: [v1] | Complexity: L
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```
MX-01 is split, as instructed, into a shared **checkout-session API** (US-140), then one story per integration option (US-141 API widget, US-142 hosted page, US-143 QR deep link), plus merchant **decision notifications** (US-144) and **EP-8 instrumentation** (US-145).

### US-140 — Create and verify an HLB checkout session securely (shared API)
**Feature:** 8.1 [MX-01] | **Epic:** EP-8 | **Size:** L
**Priority:** Must-have | **Label:** v1

**Story**
As a **merchant integration developer**,
I want **one authenticated API to create a pay-over-time session with the basket, and to fetch its final status server-side**,
so that **all three integration options share one reliable contract and my order state is never set from a spoofable browser redirect**.

**Acceptance Criteria**

**AC 1:**
Given my merchant has onboarded API credentials,
when my server calls "create checkout session" with merchant ID, order reference, basket value (VND), item categories and return URL,
then the request is authenticated with API key + signed request over mutual TLS (A-13-11) and a session ID with an expiry is returned.

**AC 2 (validation):**
Given a basket value below `policy.min_financed_vnd` (1,600,000 VND) or one whose financed amount cannot be brought ≤ `policy.max_financed_vnd` (90,100,000 VND) by any allowed down-payment,
when I create a session,
then the API returns a validation error naming the allowed range, and no session is created.

**AC 3 (idempotency):**
Given I retry "create checkout session" with the same idempotency key and identical payload,
when HLB receives it,
then the original session is returned and no duplicate is created; the same key with a different payload returns an error [US-B1][US-B2].

**AC 4 (server-side status):**
Given a session has ended,
when my server calls "get session status",
then it returns one of: APPROVED_BOOKED, REFERRED_PENDING, DECLINED, CANCELLED, EXPIRED, with the decision ID and (if booked) the accepted package summary — never CCP fields or reason codes (US-113 AC 4).

**AC 5 (negative — auth):**
Given an invalid key, bad signature or missing client certificate,
when any call is made,
then it is rejected with an authentication error and logged; no session data is returned.

**Dependencies**
Depends on: platform API gateway and merchant onboarding (credentials, allow-listed return URL, webhook endpoint); no customer data is read at session creation. Blocks: US-100, US-102, US-104, US-141, US-142, US-143, US-144.
**Open Questions:** OQ-17B-05, OQ-17B-27 (session expiry value).
**Notes:** Skill 13 §4 Auth (merchants); "verify status server-side, never from URL parameters" pattern [US-B3][US-B4].

---

### US-141 — Offer "Pay over time with HLB" as an embedded widget (Option A)
**Feature:** 8.1 [MX-01] | **Epic:** EP-8 | **Size:** M
**Priority:** Must-have | **Label:** v1

**Story**
As a **partner merchant (online store / merchant app)**,
I want to **show "Pay over time with HLB — from {x} VND/month" next to my other payment methods and let the whole flow run inside my checkout**,
so that **my shoppers can finance a purchase without leaving my site**.

**Acceptance Criteria**

**AC 1:**
Given the widget is embedded with a valid session (US-140),
when the checkout page renders,
then the widget shows "Pay over time with HLB — from {x} VND/month" grouped with the merchant's other payment methods, where {x} is computed per OQ-17B-12 and labelled indicative.

**AC 2:**
Given the shopper selects the widget,
when the flow runs,
then the screens follow IA §3 order — package list (CO-01) → all-in cost (CO-02) → eKYC/biometric (AP-02) → decision (AP-01) → e-contract — inside the embedded frame/modal without a full-page redirect.

**AC 3:**
Given the flow ends,
when the widget closes,
then it emits a client event with the session ID only, and the merchant confirms the order by calling "get session status" server-side (US-140 AC 4).

**AC 4 (edge — HLB unavailable):**
Given the HLB widget script or API is unreachable,
when the checkout page renders,
then the widget hides itself (or shows "Pay over time is temporarily unavailable") and never blocks the merchant's other payment methods.

**AC 5 (edge — eKYC in embedded context):**
Given the device or embedded context cannot run NFC/liveness (OQ-17B-21),
when the shopper reaches eKYC,
then they are handed off to the hosted page (US-142) or HLB Connect with the same session ID and without losing the selected package.

**Dependencies**
Depends on: US-140, US-100/US-102, US-106, US-118. Blocks: US-145.
**Open Questions:** OQ-17B-12, OQ-17B-21.
**Notes:** DD-12; Baymard: group BNPL with similar payment methods, keep forms short [D6].

---

### US-142 — Redirect to an HLB-hosted page and back in the same session (Option B)
**Feature:** 8.1 [MX-01] | **Epic:** EP-8 | **Size:** M
**Priority:** Must-have | **Label:** v1

**Story**
As a **partner merchant with minimal engineering capacity**,
I want to **redirect my shopper to an HLB-hosted page and get them back to my order page with the result**,
so that **I can offer HLB pay-over-time without building the UI myself**.

**Acceptance Criteria**

**AC 1:**
Given a session created via US-140,
when the merchant redirects the shopper to the hosted-page URL for that session,
then the HLB page runs the full flow (packages → all-in cost → eKYC → decision → e-contract) and is branded HLB with the merchant name and basket shown.

**AC 2:**
Given the flow ends in any state (approved, referred, declined, cancelled),
when the shopper is returned,
then they land on the merchant's return URL registered for that merchant (allow-listed at onboarding) with only the session ID as a parameter.

**AC 3 (negative — open redirect):**
Given a return URL that is not on the merchant's allow-list,
when a session is created or a redirect is attempted,
then it is rejected.

**AC 4 (edge — shopper closes the tab):**
Given the shopper abandons the hosted page before a decision,
when the session expires,
then the session status becomes EXPIRED (or CANCELLED if they tapped "Back to store") and the merchant is notified (US-144).

**AC 5 (edge — decision already booked but return fails):**
Given the loan was booked but the browser never returned to the merchant,
when the merchant's server receives the webhook (US-144) or polls status,
then the order is confirmed from the server-side status, not from the redirect.

**Dependencies**
Depends on: US-140, US-144. Blocks: US-141 AC 5, US-143 AC 4, US-145.
**Notes:** IA §3 Option B "redirect-and-return, same session"; hosted-checkout redirect/webhook pattern [US-B3][US-B4].

---

### US-143 — Pay over time in a physical store by scanning a QR code (Option C)
**Feature:** 8.1 [MX-01] | **Epic:** EP-8 | **Size:** M
**Priority:** Must-have | **Label:** v1

**Story**
As a **partner merchant with a physical store** (and **Anh Hùng shopping in it**),
I want **a QR code at the till that opens the HLB flow on the shopper's phone and sends the confirmed decision back to my till**,
so that **in-store customers can pay over time in the same visit**.

**Acceptance Criteria**

**AC 1:**
Given the merchant's till creates a session (US-140) for the basket,
when the till displays the session's QR code,
then scanning it with a phone that has HLB Connect installed opens HLB Connect directly on that session's package screen (deep link carrying the session ID only).

**AC 2:**
Given the shopper completes the flow in HLB Connect,
when the loan is booked,
then the merchant's till/back-end receives the confirmation (US-144) and the till shows "HLB approved — order confirmed" with the decision ID.

**AC 3 (edge — QR expiry / reuse):**
Given the session has expired or has already produced a booked loan,
when the QR code is scanned,
then the shopper sees "This payment code is no longer valid — ask the cashier for a new one" and no new application starts.

**AC 4 (edge — HLB Connect not installed, e.g. Lane B):**
Given the scanning phone does not have HLB Connect,
when the QR is scanned,
then the link opens the HLB hosted page (US-142) for the same session in the phone browser, with an option to install HLB Connect (web fallback pattern [US-B5][US-B6]; confirm per OQ-17B-28).

**AC 5 (edge — basket changed at till):**
Given the cashier changes the basket after the QR was shown,
when the till updates the order,
then a new session and QR are created and the old session is cancelled.

**Dependencies**
Depends on: US-140, US-142, US-144. Blocks: US-145.
**Open Questions:** OQ-17B-28.
**Notes:** IA §3 Option C "scan → HLB Connect → decision → merchant confirmation"; DD-12 (in-store channel).

---

### US-144 — Receive the decision and payment guarantee in-session, including referred and expired states
**Feature:** 8.1 [MX-01] | **Epic:** EP-8 | **Size:** M
**Priority:** Must-have | **Label:** v1

**Story**
As a **partner merchant**,
I want **a signed, real-time notification of every state change of my shopper's HLB session**,
so that **I can release goods on approval, hold them during review, and cancel cleanly on decline or expiry**.

**Acceptance Criteria**

**AC 1:**
Given a session changes state (APPROVED_BOOKED, REFERRED_PENDING, DECLINED, CANCELLED, EXPIRED, and REFERRED → APPROVED_BOOKED / DECLINED),
when the change is committed,
then HLB sends a webhook to the merchant's registered HTTPS endpoint with session ID, order reference, new state, decision ID and timestamp, signed so the merchant can verify authenticity.

**AC 2 (payment guarantee):**
Given the state is APPROVED_BOOKED,
when the webhook is sent,
then it states the financed amount guaranteed by HLB and the down-payment the shopper must pay the merchant (if any).

**AC 3 (refer hold):**
Given the state is REFERRED_PENDING,
when the webhook is sent,
then it includes the SLA deadline (US-125 AC 2) and whether HLB has requested a hold (`merchant.supports_refer_hold`), and a follow-up webhook is sent with the officer outcome (US-126).

**AC 4 (delivery reliability):**
Given the merchant endpoint does not acknowledge,
when delivery fails,
then HLB retries with back-off, each retry carries the same event ID so the merchant can de-duplicate, and the latest state remains available via "get session status" (US-140 AC 4).

**AC 5 (privacy):**
Given any webhook,
when it is built,
then it contains no CCP field, reason code, CIC data, income estimate or consent detail about the shopper.

**Dependencies**
Depends on: US-140, US-113, US-114, US-125, US-126. Blocks: US-141, US-142, US-143, US-145.
**Open Questions:** OQ-17B-17, OQ-17B-18, OQ-17B-23.
**Notes:** Skill 13 Flow 3 "merchant receives a payment guarantee"; DF-2/DF-4 "merchant confirmed / notified"; signed-webhook pattern [US-B3][US-B4].

---

### US-145 — Measure merchant coverage and confirmation latency (EP-8 success signal)
**Feature:** 8.1 [MX-01] | **Epic:** EP-8 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As the **HLB PoP product owner**,
I want **integration-level metrics per merchant and channel**,
so that **I can track the share of partner checkouts offering HLB PoP and whether merchant confirmation stays within the KR 1.2 envelope**.

**Acceptance Criteria**

**AC 1:**
Given the widget, hosted page or QR is rendered to a shopper,
when it is displayed,
then an `hlb_option_shown` event is logged with merchant ID and channel (widget / hosted / QR).

**AC 2:**
Given a session is booked,
when the merchant acknowledges the APPROVED_BOOKED webhook,
then `merchant_confirmation_latency` = acknowledgement time − `application_submitted` time is logged, and reported at p50/p90 alongside KR 1.2.

**AC 3:**
Given the weekly EP-8 report,
when it runs,
then it shows, per merchant and channel: checkout sessions created, HLB option shown, sessions started, decisions returned, booked loans and webhook delivery success rate.

**AC 4 (edge):**
Given webhook delivery success for a merchant drops below `mx.webhook_success_alert` (`[DECISION REQUIRED]`),
when the monitor evaluates,
then the merchant-integration team is alerted.

**Dependencies**
Depends on: US-117, US-141, US-142, US-143, US-144. Blocks: none.
**Open Questions:** OQ-17B-29 (denominator for "share of partner checkouts offering HLB PoP": HLB sees only sessions created, not the merchant's total checkouts).
**Notes:** Skill 16 EP-8 success signal.

---

### Parked in EPIC 8 (listed only — `[Later — pending user]`)
- **8.2 MX-02 Merchant portal — 0% campaigns, settlement (PL-03).** Parked story stub: *"As a partner merchant, I want a self-service portal to set up 0% subsidised campaigns and see settlements, so that I don't depend on HLB staff."* v1 interim: 0% eligibility is configured per merchant by HLB staff (`merchant.zero_pct_eligibility`, OQ-16-02); settlement mechanics are an open item (OQ-17B-18).

---

## 4. Story Map Grid (part B)

| Epic | Feature | Story IDs | Label |
|---|---|---|---|
| EP-2 Packages & Transparent Cost | 2.1 Package builder [CO-01] | US-100, US-101, US-102, US-103, US-104, US-105 | v1 |
| | 2.2 All-in cost & schedule [CO-02] | US-106, US-107, US-108 | v1 |
| | 2.3 Side-by-side comparison [CO-03] | US-109 | v1-Should |
| EP-3 In-Session Decision & Identity ★ | 3.1 In-session decision [AP-01] | US-110, US-111, US-112, US-113, US-114, US-115, US-116, US-117 | v1 |
| | 3.2 Chip-CCCD/VNeID eKYC [AP-02] | US-118, US-119, US-120 | v1 |
| | 3.3 Integrity checks → F5 [AP-03] | US-121, US-122, US-123 | v1 |
| | 3.4 Refer band + counter-offer + SLA [AP-04] | US-124, US-125, US-126, US-127 | v1 |
| | 3.5 Starter limit [AP-05] | US-128 | v1 |
| | 3.6 CASA-first onboarding [AP-06] | US-129 | v1 |
| | 3.7 CIC-unavailable fallback [AP-07] | US-130, US-131 | v1 |
| | 3.8 eKYC fallback flows [AP-08] | — (parked; v1 minimum in US-120) | Later — pending user |
| | 3.9 Path-to-yes explanation [AP-09] | — (parked; v1 minimum in US-115) | Later — pending user |
| EP-8 Merchant & Partner Integration | 8.1 Checkout integration [MX-01] | US-140 (session API), US-141 (API widget), US-142 (hosted page), US-143 (QR deep link), US-144 (notifications), US-145 (metrics) | v1 |
| | 8.2 Merchant portal [MX-02] | — (parked) | Later — pending user |

### Walking skeleton (thinnest end-to-end slice through part B)
Lane B, hosted page: **US-140 → US-142 → US-102 → US-111 → US-118 → US-121 → US-112 → US-128 → US-106 → US-107 → US-114 → US-144**, with **US-117** instrumentation. Lane A adds US-100, US-119, US-110. Refer band (US-124–US-127) and CIC fallback (US-130–US-131) follow immediately, because KR 1.4 and FM-12 are v1 launch conditions.

---

## 5. Dependency Graph

### 5.1 Cross-part prerequisites (by feature code)
```
PART A (EP-0 / EP-7)                                  → PART B stories that cannot start without it
E-01 consent ledger (SC/MK/RF)                        → US-100, US-111
E-02 ingestion incl. CIC client/cache, S13 signals    → US-112, US-121, US-130, US-131
E-05 CCP generator & purpose-checked CCP API          → US-100, US-110, US-112, US-121
E-06 scoring incl. integrity anomaly model            → US-112, US-121
E-07 policy layer, limit rule, rate grid              → US-100, US-103, US-110, US-112, US-122, US-124, US-128, US-130
E-09 reason-code generator                            → US-113, US-115, US-125
OP-01 officer workbench                               → US-125, US-126
GV-01 decision log / audit                            → US-113, US-123, US-126
PART C (EP-1 / 4 / 5 / 6)
RT-02 anti-stacking gate                              → US-110 (AC 4)
AW-04 offer card (entry point)                        → US-100 (AC 1 entry only; not blocking)
AD-01 invite link (entry point)                       → US-111 (AC 5 entry only; not blocking)
Part B → Part C: US-114 booking event → PR-04 CIC reporting, "My plans"; US-128 starter flag → PR-03 graduation; US-108 stored terms → Flow 4 early settlement
```

### 5.2 Within part B
```
US-140 ─┬─> US-100 ─┬─> US-101 ──> US-124
        │           ├─> US-103 ──> US-109
        │           ├─> US-104
        │           └─> US-106 ─┬─> US-107 ──> US-114
        │                       ├─> US-108
        │                       └─> US-109
        ├─> US-102 ──> US-111 ──> US-118 ─┬─> US-120 ──> US-125
        │                                 └─> US-121 ──> US-122 ─┬─> US-123
        │                                                        └─> US-125
        │   US-119 ──> US-110 ──> US-114
        │   US-111 + US-118 + US-121 ──> US-112 ─┬─> US-113 ──> US-115, US-144
        │                                        ├─> US-114 ──> US-105, US-117, US-144
        │                                        ├─> US-116
        │                                        ├─> US-124 ──> US-125 ──> US-126 ──> US-127
        │                                        ├─> US-128
        │                                        ├─> US-129
        │                                        └─> US-130 <── US-131
        ├─> US-144 ──> US-141, US-142, US-143
        └─> US-142 ──> US-143 (AC 4 web fallback), US-141 (AC 5 eKYC hand-off)
US-117 + US-141/142/143/144 ──> US-145
```
**Critical path (part B):** US-140 → US-100/US-102 → US-106 → US-111 → US-118 → US-121 → US-112 → US-114 → US-144 (gated by part A E-01, E-05, E-07).

---

## 6. Definition of Done (part B additions)

The standard DoD in `.agents/skills/17-user-story-generation/templates/dod-checklist.md` applies to every story (code review, unit + integration tests, all ACs incl. edge ACs verified by QA/PM, WCAG 2.1 AA, error states, analytics events, no PII in logs/analytics, feature flag, rollback plan, monitoring, API docs). Part B adds:
- [ ] **G-4 validator** is called on every path that emits a package (US-101 AC 5) and has a test proving a 1-VND-over-F2 package is rejected.
- [ ] Every policy value is read from a named parameter (§0.3); **no hard-coded threshold** for any `[DECISION REQUIRED]` item.
- [ ] Every decision path writes a decision-log entry with reason codes (KR 4.1) and the lifecycle events of US-117.
- [ ] CCP reads pass the SC purpose check; tests cover the "no SC consent" negative path.
- [ ] Merchant-facing payloads (API, webhook, redirect) verified to contain **no** CCP fields, reason codes or CIC data.
- [ ] Merchant APIs: idempotency, signature/mTLS rejection and webhook retry/de-duplication covered by contract tests.
- [ ] Customer-facing copy available in Vietnamese; the all-in cost screen reviewed by HLB compliance.
- [ ] No document-upload, reference-contact, contacts/gallery permission appears anywhere in the flow.

---

## 7. Story Count Summary (part B)

```
Epics covered:                 3 (EP-2, EP-3, EP-8)
v1 features covered:           11  (CO-01, CO-02, AP-01..AP-07, MX-01) + 1 v1-Should (CO-03)
Stories:                       38  (EP-2: 10 · EP-3: 22 · EP-8: 6)
  Must-have / Should-have:     37 / 1 (US-109)
  By size:                     S 19 · M 17 · L 2 (US-112, US-140)
Acceptance criteria:           171 (EP-2: 42 · EP-3: 100 · EP-8: 29); every story has ≥ 3 ACs; every behavioural story has ≥ 1 negative/edge AC, and the instrumentation stories (US-105, US-117, US-127) carry alert-threshold ACs instead
Instrumentation stories/ACs:   KR 1.1 & 1.2 → US-117, US-116 AC 1/5 · KR 1.4 → US-127 · KR 3.2 → US-105 ·
                               G-2 → US-123 · G-4 → US-105 AC 3, US-101 AC 5, US-114 AC 3 ·
                               (also KR 2.2 → US-129 AC 4; KR 2.1/2.3 tag → US-128 AC 5; EP-8 signal → US-145)
Story points / sprints:        not assigned — feature effort bands await Skill 14 re-estimation (OQ-16-01);
                               the consolidated 17-user-stories.md should apply one point scale across parts A–C.
```

---

## 8. Later Stories Parked (not counted above)

| Parked feature | PL | Parked story (stub) | v1 minimum already in part B |
|---|---|---|---|
| AP-08 eKYC failure fallback flows | PL-04 | As a customer whose eKYC keeps failing, I want an assisted verification path, so that I can still complete my application. | US-120: 3 attempts → REFER "identity review"; attempt telemetry for sizing |
| AP-09 Rich decline explanation / path-to-yes | PL-05 | As a declined applicant, I want a personalised explanation of what would change the outcome, so that I know how to get approved next time. | US-115: top E-09 reason codes + generic path |
| MX-02 Merchant portal (0% campaigns, settlement) | PL-03 | As a partner merchant, I want a self-service portal to set up 0% campaigns and view settlements, so that I don't depend on HLB staff. | US-103: 0% configured per merchant by HLB staff (OQ-16-02) |

Rejected permanently and therefore absent from every story: CO-04 willingness-to-pay pricing, X-01 contact/gallery harvesting, bundled insurance.

---

## 9. Open Items Raised by This Part (carry to Skill 22 unless noted)

Design gaps found in Skill 13 are marked **[GAP-13]**.

| # | Item | Stories | Carry to |
|---|---|---|---|
| OQ-17B-01 | No Skill 05 persona card exists for the **partner merchant** or **merchant integration developer**; stories use role personas from Skill 08/13. Confirm acceptable or add evidence-backed merchant persona. | US-140–US-145 | Skill 22 |
| OQ-17B-02 | **[GAP-13]** "Split basket" (Flow 2 alternative) is undefined — financing part of the basket and paying the rest another way is already covered by down-payment. Define or drop. | US-101 | Skill 22 |
| OQ-17B-03 | **[GAP-13]** Design says "2–4 packages" but not what happens when fewer than 2 fit F2. Part B shows 1 rather than breach G-4; confirm. | US-100, US-109 | Skill 22 |
| OQ-17B-04 | Down-payment option set, and **who collects the down-payment** (merchant at till vs HLB) — not designed. | US-100, US-101, US-144 | Skill 22 `[DECISION REQUIRED]` |
| OQ-17B-05 | Is the pilot "requested financing range" 1.6M–90.1M VND [Brief p.9] the v1 product min/max, and does it apply to the financed amount (after down-payment) or the basket? | US-101, US-140 | Skill 22 |
| OQ-17B-06 | Fee schedule, late-payment terms and early-settlement terms (values and wording) — HLB policy. | US-103, US-106, US-108 | Skill 22 `[DECISION REQUIRED]` |
| OQ-17B-07 | **[GAP-13]** Rule for first due date / payment-date alignment not designed (cash-flow alignment itself is parked PL-06). | US-106 | Skill 22 |
| OQ-17B-08 | **[GAP-13]** No behaviour or budget defined when a decision does not complete within the session (latency > budget). Candidate: route to refer path. | US-116 | Skill 22 / Skill 18 NFR |
| OQ-17B-09 | Integrity velocity rules and device-signal set — HLB fraud policy. | US-121 | Skill 22 `[DECISION REQUIRED]` |
| OQ-17B-10 | HLB business-hours calendar for the 2-business-hour SLA (weekends, Tết, evening checkouts). | US-125, US-127 | Skill 22 `[DECISION REQUIRED]` |
| OQ-17B-11 | **[GAP-13]** How long an officer approval remains signable (`refer.approval_validity`) and the max notification delay — not designed. | US-126 | Skill 22 |
| OQ-17B-12 | Basis of the widget's "from {x} VND/month" and Lane B indicative ranges (longest tenor at best grid rate risks being misleading under FM-4). | US-102, US-141 | Skill 22 |
| OQ-17B-13 | KR 3.2 denominator: per package shown or per package set shown? Both are instrumented. | US-105 | Skill 22 |
| OQ-17B-14 | APR calculation/disclosure method required under Vietnamese consumer-credit rules — not researched in Skills 02/13. | US-106 | Skill 22 / legal |
| OQ-17B-15 | **[GAP-13]** Tier when the RT-02 stacking check fails at checkout (DECLINE vs REFER) — §1.1 only says approvals require it to pass. | US-110 | Skill 22 |
| OQ-17B-16 | **[GAP-13]** Lane B applicant who is actually an existing HLB customer: match rule (CCCD number) and switch to Lane A — not designed. | US-112 | Skill 22 |
| OQ-17B-17 | Confirm the merchant never receives reason codes or CCP data (part B assumes data minimisation per PDPL purpose limitation). | US-113, US-144 | Skill 22 |
| OQ-17B-18 | **[GAP-13]** Merchant settlement / funds flow for the payment guarantee — not designed (merchant portal parked). | US-114, US-144 | Skill 22 / Skill 14 |
| OQ-17B-19 | KR 1.2 start point for Lane B: before or after eKYC (~80 s)? Events allow both. | US-116, US-117 | Skill 22 |
| OQ-17B-20 | Operational definition of "checkout session ended" for KR 1.1 (merchant timeouts differ by channel). | US-117 | Skill 22 |
| OQ-17B-21 | **[GAP-13]** Can HLB's existing eKYC (NFC + liveness) run inside a merchant web checkout / HLB hosted page, or only in HLB Connect? Affects Lane B via widget and hosted page. | US-118, US-141 | Skill 14 |
| OQ-17B-22 | **[GAP-13]** Definition of "confirmed identity mismatch" (DECLINE) vs integrity-floor REFER. | US-122 | Skill 22 |
| OQ-17B-23 | **[GAP-13]** What happens to the purchase when the merchant will not hold goods during the refer SLA (A-13-7 / OQ-13-03). | US-125, US-144 | Skill 22 / merchant interviews |
| OQ-17B-24 | **[GAP-13]** Completion channel for a Lane B customer without HLB Connect after an officer approves (SMS link to hosted page?). | US-126 | Skill 22 |
| OQ-17B-25 | **[GAP-13]** Repayment method for a Lane B customer who declines CASA-first. | US-129 | Skill 22 |
| OQ-17B-26 | **[GAP-13]** Post-recovery re-check of loans booked under CIC fallback, and the outage alert rule — not designed. | US-131 | Skill 22 |
| OQ-17B-27 | Checkout-session expiry value (distinct from the 30-minute package hold). | US-140 | Skill 22 / Skill 18 |
| OQ-17B-28 | **[GAP-13]** In-store QR for shoppers without HLB Connect (most Lane B): web fallback to the hosted page proposed; confirm. | US-143 | Skill 22 |
| OQ-17B-29 | EP-8 coverage denominator (HLB cannot see merchants' total checkouts) and webhook-success alert floor. | US-145 | Skill 22 |
| OQ-17B-30 | **[GAP-13]** Order cancellation, returns and refunds after booking (Flow 3 mentions a "refund account" but no flow exists). No story written; needs design. | — | Skill 22 / Skill 20 |

Inherited and still open: OQ-13-02 (all policy thresholds), OQ-13-03 / A-13-7 (merchant hold), A-13-6 (30-min hold), A-13-8 (3 eKYC attempts), A-13-11 (API keys + mTLS), OQ-16-01 (effort), OQ-16-02 (0% set-up).

---

## Source Index

| # | Source | URL | Used for |
|---|---|---|---|
| US-B1 | Checkout.com Docs, "Idempotency" | https://www.checkout.com/docs/payments/manage-payments/idempotency | Idempotency-key replay behaviour (US-114 AC 5, US-140 AC 3) |
| US-B2 | Payabli API reference, "Prevent Duplicate Requests" | https://docs.payabli.com/api-reference/idempotency-key | Same key + different payload → error; webhooks to avoid duplicates |
| US-B3 | Koin API docs, "Embedded integration examples" | https://api-docs.koin.com.br/docs/embedded-integration-examples | BNPL embedded vs hosted checkout; asynchronous notification URLs |
| US-B4 | Peach Payments developer docs, "Hosted Checkout payment request" | https://developer.peachpayments.com/docs/checkout-payment | Redirect-and-return hosted checkout; verify status server-side |
| US-B5 | Nimble, "Mastering iOS Universal Links and Android App Links" | https://nimblehq.co/blog/guideline-ios-universal-links-android-app-links | Verified deep links with web fallback when the app is not installed (US-143 AC 4) |
| US-B6 | Branch Help, "Create Deep Links" | https://help.branch.io/docs/creating-a-deep-link | QR → deep link → app or web fallback |
| — | Upstream pipeline files | local | Behaviour: `13-solution-design.md` (§1.1, Flows 2–3, IA §3, DF-2/DF-4, DD-06/10/12, A-13-6..11); features: `16-epic-feature-decomposition.md`; KRs/guardrails: `10-success-metrics.md`; scope: `08-v1-product-scoping.md`; journey: `15-user-journey-map.md`; SD-1..SD-7: `_context.md`; tenors and ticket range [Brief p.9]: `_requirements.md`. Citations [R#], [D#], [Skill03 S#] resolve through those files. |
| — | Format reference (not cited as evidence) | `.agents/skills/00-case-studies/by-output-type/user-story-examples.md` | Card and AC format only |


---

# ═══ PART C ═══

# User Stories & Acceptance Criteria — Part C (Lifecycle: Awareness, Reassessment, Retention, Advocacy)
**Product:** HLB Point-of-Purchase Credit Decisioning — **HLB CreditPulse** | **Skill:** 17 (part C of 3) | **Status:** Done (2026-09-30)
**Depends on:** `16-epic-feature-decomposition.md` (Skill 16), `13-solution-design.md` (Skill 13), `10-success-metrics.md` (Skill 10), `07-problem-solution-fit.md` (Skill 07), `08-v1-product-scoping.md` (Skill 08), `15-user-journey-map.md` (Skill 15) | **Feeds into:** `17-user-stories.md` (merge of parts A, B and C), `18-nfr.md`, `product-prd.md` (§2 Functional Spec, §6 DoD)

> **Scope of this part.** EPIC 1 (Pre-approval Targeting), EPIC 4 (Continuous Reassessment & Graduation), EPIC 5 (Suitable Repeat Offers & Anti-Stacking), EPIC 6 (Responsible Referral & Merchant Flywheel).
> - **Part A** (US-001 to US-099) covers EP-0 and EP-7: CCP generator and API (E-05), ingestion (E-02), hybrid scoring (E-06), policy layer (E-07), refresh loop (E-08), consent ledger (E-01), reason codes (E-09), officer workbench (OP-01), governance (GV-01), policy console (OP-02).
> - **Part B** (US-100 to US-199) covers EP-2, EP-3 and EP-8: package builder (CO-01), all-in cost (CO-02), checkout decision (AP-01), eKYC (AP-02), integrity/F5 (AP-03), starter limit (AP-05), CASA-first (AP-06), checkout integration (MX-01).
> - Part C stories cite parts A and B **by feature code** (for example "Part A · E-01"). Story IDs will be resolved when the three parts are merged.
>
> **Traceability.** Every story names its Skill 08 feature code, the Skill 13 flow/decision it implements, and the KR or guardrail it serves. Story behaviour is taken from Skill 13. Where Skill 13 is silent, the AC is written against a **named configurable parameter** marked `[DECISION REQUIRED]` or `[GAP]`, and the gap is logged in §7 (OQ-17C-##). **No number in this file is new.** Every number is quoted from an upstream file, and the source is named next to it.
>
> **Labels.** `[v1]` = Skill 08 Must → Priority *Must-have*. `[v1-Should]` = Skill 08 Should (ships in v1 if time allows, subject to its condition) → Priority *Should-have*. `[Later — pending user]` = parked; listed only (§6).
>
> **Sizes.** S / M / L / XL are relative story sizes at the story level (≤ 1 sprint each; L = the largest that still fits in a sprint). They are **not** Skill 11 person-week bands. Skill 14 re-estimation is pending (OQ-16-01).
>
> **Consent purposes** (Part A · E-01; condition C1): **SC** = scoring/servicing, **MK** = marketing/targeting, **RF** = referral.
>
> **Research note.** No new external sources were needed. All behaviour and evidence trace to upstream files. Citations `[R#]`, `[Brief]` and `[Skill##]` resolve through those files (Source Index, §8).

---

## 0. Configurable Parameter Registry (used by Part C ACs)

Every AC that depends on an HLB policy value references one of these names. Each value is edited through the policy/rule console or version-controlled config (Part A · OP-02). Every change is audit-logged (Part A · GV-01).

| Parameter | Meaning | Value status | Source |
|---|---|---|---|
| `policy.graduation_on_time_instalments_N` | On-time instalments required before a graduation offer. Also used for invite eligibility; whether that should be a separate parameter is open (OQ-17C-10) | `[DECISION REQUIRED]` (Skill 13 example: 3) | Skill 13 Flow 4 step 3, Flow 6; OQ-13-02 |
| `policy.referral_reward_on_time_instalments_K` | The invitee's on-time instalments required before the inviter's reward is released | `[DECISION REQUIRED]` (Skill 13 example: 3) | Skill 13 Flow 6; DF-5; OQ-13-02 |
| `campaign.holdout_share` | Random share of each Awareness candidate list that is held out | `[DECISION REQUIRED]` (Skill 13 example: 10%) | Skill 13 Flow 1 step 2; OQ-13-02 |
| `campaign.attribution_window_days` | Window after offer delivery (treatment) or assignment (holdout) in which a booked PoP loan counts as take-up for KR 3.1 | `[GAP]`, not defined upstream | OQ-17C-04 |
| `campaign.mk_cooling_period_days` | Period with no MK contact after a complaint or opt-out | `[DECISION REQUIRED]` | Skill 13 Flow 1 error path |
| `policy.recent_hardship_lookback_days` | Look-back window that defines a "recent hardship episode" for the AW-02 exclusion | `[GAP]`, not defined upstream | OQ-17C-02 |
| `policy.stacking_concurrent_consumer_loans_threshold` | Count of concurrent consumer loans at CIC that trips the anti-stacking gate | Skill 13 value: **2**; configurable | Skill 13 Flow 5 step 2; G-3 definition |
| `policy.stacking_rising_obligations_rule` | Definition of "rising total obligations" (comparison window and threshold) | `[GAP]`, not defined upstream | OQ-17C-08 |
| `policy.internal_exposure_cap_vnd` | HLB internal exposure cap per customer, checked by the anti-stacking gate | `[DECISION REQUIRED]` | Skill 08 RT-02 |
| `policy.stacking_cic_snapshot_max_age_days` | Maximum age of the CIC snapshot the gate may rely on for re-offers | `[GAP]`. Skill 10 plans a monthly portfolio CIC refresh | Skill 10 §4 (G-3 row); OQ-17C-08 |
| `retention.max_recommendations_per_week` | Maximum recommendations per customer per weekly build | Skill 13/16 value: **3**; configurable | Skill 13 Flow 5 step 1; Skill 16 Feature 5.1 |
| `retention.dismissal_suppression_threshold` | Dismissals per category before that category is suppressed | Design default **3** (ASSUMPTION A-13-9) | Skill 13 Flow 5 error path |
| `retention.dismissal_suppression_period_days` | How long a dismissed category stays suppressed | `[GAP]`, not defined upstream | OQ-17C-09 |
| `retention.recent_completion_window_days` | How recently a PoP loan must have completed for RT-01 eligibility | `[GAP]` ("recently completed" is not defined) | OQ-17C-09 |
| `retention.reengagement_inactivity_days` | Inactivity after loan completion before the single re-engagement nudge | Skill 13 value: **90**; configurable | Skill 13 Flow 5 alternative path |
| `ew.trigger_rules` | Rule set that raises the early-warning flag (v1 is rule-based) | `[DECISION REQUIRED]` | Skill 16 Feature 4.2; Skill 11 suggestion |
| `ew.flag_clear_rule` | Condition that clears an early-warning flag | `[GAP]`, not defined upstream | OQ-17C-06 |
| `ew.max_supportive_messages_per_cycle` | Maximum supportive early-warning messages per repayment cycle | `[GAP]`, not defined upstream | OQ-17C-06 |
| `metrics.ew_cure_window_days` | Days after outreach at which cure is measured | Skill 10 value: **60** | KR 3.3 definition |
| `policy.graduation_step_ladder` | Size of each graduation step: limit increment, tenor unlock, rate-tier step (sized on risk-adjusted profit, DD-05) | `[DECISION REQUIRED]` | Skill 13 Flow 4; DD-05; OQ-13-02 |
| `policy.graduation_offer_validity_days` | How long a graduation offer stays open | `[GAP]`, not defined upstream | OQ-17C-07 |
| `auth.graduation_acceptance_level` | Authentication required to accept a graduation offer | `[GAP]`. Skill 13 §4 requires biometric authentication only for "disbursement-grade actions" | OQ-17C-07 |
| `model.psi_action_threshold` | PSI at which re-scoring falls back to the champion | Skill 10/13 value: **0.25** | G-8; DD-13; Skill 13 Flow 4 error path |
| `referral.reward_type` | Reward given to the inviter (for example, a fee waiver or a merchant voucher) | `[DECISION REQUIRED]` | Skill 13 Flow 6 step 3; OQ-13-02 |
| `referral.invite_link_validity_days` | How long an invite link stays valid | `[GAP]`, not defined upstream | OQ-17C-11 |
| `referral.max_active_invites_per_inviter` | Cap on open invites per inviter (abuse control) | `[GAP]`, not defined upstream | OQ-17C-11 |
| `merchant_lead.retention_days` | Retention period for a suggested shop's contact data when the shop is not onboarded | `[GAP]`; PDPL purpose limitation [R46] | OQ-17C-13 |
| `policy.passport_total_debt_affordability_rule` | Total-debt affordability test that gates the good-payer passport (FM-7) | `[DECISION REQUIRED]` | Skill 13 Flow 6 AD-03; FM-7 [R62] |

---

## 1. EPIC 1 — Pre-approval Targeting (Journey stage: Awareness)

Goal (Skill 16): existing HLB customers who have never used PoP credit, are low-risk and can afford it learn that they can pay over time with a pre-approved limit, and only when the offer is relevant. Success signal: significant incremental take-up vs a random holdout (KR 3.1), with the complaint rate not above baseline (G-6).

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 1.1 [AW-01]: Never-used / low-risk / high-potential target-list generator
Epic: EP-1 Pre-approval Targeting | Label: [v1] | Complexity: M
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Story ID: US-200
**Feature:** AW-01 Target-list generator | **Epic:** EP-1 | **Size:** M
**Priority:** Must-have | **Label:** v1

**Story**
As an **HLB marketing analyst**,
I want **a candidate list, built every night, of existing HLB customers who have never used PoP credit and whose Customer Credit Profile shows they are low-risk and can afford a package**,
so that **Awareness offers go only to customers the engine judges suitable, not to everyone (brief R-4)**.

**Acceptance Criteria**

**AC 1:**
Given the nightly CCP refresh (Part A · E-08) has completed for Lane A customers with SC consent,
when the AW-01 job runs,
then it selects only customers who meet **all** of these conditions:
- no HLB PoP loan ever booked;
- F1 ∈ {A, B};
- F2 ≥ the minimum instalment of the cheapest package (Part B · CO-01);
- F3 ≠ THIN;
- F6 = PRE-APPROVED with a limit L.

It writes the result as a versioned candidate list stamped with the CCP version used for each customer.

**AC 2:**
Given a customer has SC consent withdrawn, or has no SC consent,
when the job runs,
then that customer is not profiled for Awareness and does not appear on the list. The CCP read uses the MK purpose check (Part A · E-01/E-05, DF-1).

**AC 3:**
Given a customer has any booked HLB PoP loan (active, completed or written off),
when the job runs,
then the customer is excluded from the Awareness list. Their re-offers go through Retention (EP-5) instead.

**AC 4 (edge):**
Given a customer has F1 ∈ {A, B} and F3 = CONSENTED-PARTNER,
when the job runs,
then the customer **is** selected, subject to the same F2 and consent conditions. Skill 13 §1.1 was fixed on 2026-09-30 so that CONSENTED-PARTNER qualifies for PRE-APPROVED (OQ-17C-01 resolved). The job logs selections by F3 value, so MSME reach is visible.

**AC 5 (error):**
Given the nightly CCP refresh has not completed, or has failed, by the job's start time,
when the job runs,
then it does not build the list from stale or partial CCPs. It raises an alert to the campaign workspace, and the previous list is not re-sent.

**AC 6 (instrumentation):**
Given a list is produced,
when it is written,
then the job emits `awareness_candidates_built`. The event carries run ID, candidate count, exclusion counts per rule and CCP model versions, and feeds the KR 3.1 report (US-205).

**Dependencies**
Depends on: Part A · E-05 (CCP API), E-08 (refresh loop), E-07 (tier policy), E-01 (consent ledger); Part B · CO-01 (minimum package instalment)
Blocks: US-202, US-203, US-206, US-210

**Open Questions:** OQ-17C-01 (CONSENTED-PARTNER gap), OQ-17C-03 (definition of "never used": does it cover declined applicants and PoP at other lenders?).
**Notes:** Skill 13 Flow 1 step 1; DF-1; Skill 16 Feature 1.1. S12 digital engagement is not used here (DD-07).

---

### Story ID: US-201
**Feature:** AW-01 Target-list generator (campaign workspace) | **Epic:** EP-1 | **Size:** M
**Priority:** Must-have | **Label:** v1

**Story**
As an **HLB marketing analyst**,
I want to **set up an Awareness campaign in the campaign workspace from the nightly candidate list, choosing partner categories and the holdout share**,
so that **every campaign is targeted, measurable and auditable without my exporting customer data by hand**.

**Acceptance Criteria**

**AC 1:**
Given I have the campaign-analyst role,
when I create a campaign,
then I can pick:
- the candidate list version;
- the eligible partner merchant categories;
- the delivery channel (HLB Connect push and/or in-app card);
- `campaign.holdout_share`.

The workspace shows the counts after suitability exclusions and after the holdout split, before I launch.

**AC 2:**
Given I am viewing candidates,
when the workspace renders customer rows,
then it shows no raw F1 PD band, F2 income estimate or CIC data. It shows only the eligibility flags, the pre-approved limit band and the counts. This follows the data-minimisation principle in Skill 13 §4 (Auth).

**AC 3 (negative):**
Given I try to set `campaign.holdout_share` to 0, or try to launch without a holdout,
when I press Launch,
then the launch is blocked with the message "A random holdout is required for every Awareness campaign (C4)".

**AC 4 (negative):**
Given I try to add customers who are not on the AW-01 list (for example, by uploading a file),
when I save,
then the workspace rejects the upload. Broadcast or ad-hoc lists are not supported (AW-06 rejected; brief R-4).

**AC 5 (audit):**
Given a campaign is launched,
when launch completes,
then the audit log (Part A · GV-01) records the analyst, timestamp, list version, parameters and holdout share.

**Dependencies**
Depends on: US-200, US-202, US-203; Part A · E-01, GV-01
Blocks: US-204, US-205

**Notes:** Skill 13 §3 IA "Campaign workspace". The workspace's user-interface depth depends on Part A · OP-02 (it may start as config).

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 1.2 [AW-02]: Suitability filter + random holdout
Epic: EP-1 Pre-approval Targeting | Label: [v1] | Complexity: S
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Story ID: US-202
**Feature:** AW-02 Suitability filter | **Epic:** EP-1 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As **Chị Lan (credit-risk lead)**,
I want **every Awareness candidate who is currently unsuitable to be removed before any split or contact**,
so that **we never market credit to someone who is struggling, stacking debt or has not agreed to marketing (FM-5)**.

**Acceptance Criteria**

**AC 1:**
Given a candidate on the AW-01 list,
when the suitability filter runs,
then the candidate is excluded if **any** of these apply:
- an active early-warning flag (US-222);
- the anti-stacking gate returns SUPPRESS (US-244);
- a hardship episode within `policy.recent_hardship_lookback_days`;
- no active MK consent;
- inside the `campaign.mk_cooling_period_days` window after a complaint or opt-out (US-209).

**AC 2:**
Given a candidate is excluded,
when the filter writes its output,
then the exclusion reason code is stored per customer, and exclusion counts per reason are emitted. The excluded customer is in **neither** the treatment group nor the holdout.

**AC 3 (edge):**
Given a customer is excluded only because MK consent is absent,
when they later open the "Pay Later" hub themselves,
then they may still see their pre-approved limit as a service display (US-208). The exclusion affects only contact.

**AC 4 (error):**
Given the early-warning flags or the anti-stacking gate result cannot be read for a candidate,
when the filter runs,
then that candidate is excluded with reason `SUITABILITY_UNKNOWN` (fail closed). They are not contacted.

**Dependencies**
Depends on: US-200, US-222, US-244; Part A · E-01
Blocks: US-203, US-206

**Open Questions:** OQ-17C-02: which data source records a "hardship episode", and what is the look-back window?
**Notes:** Skill 13 Flow 1 step 2; C4. The fail-closed rule in AC 4 is derived from C4 and FM-5; it is listed in OQ-17C-02 for confirmation.

---

### Story ID: US-203
**Feature:** AW-02 Random holdout | **Epic:** EP-1 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As an **HLB marketing analyst**,
I want **a random, persistent holdout on every Awareness campaign, and holdout customers never contacted**,
so that **we can measure *incremental* take-up, not take-up that would have happened anyway (KR 3.1, C4)**.

**Acceptance Criteria**

**AC 1:**
Given a campaign's suitability-filtered list,
when the split runs,
then each customer is randomly assigned to treatment or holdout in the proportion `campaign.holdout_share`. The assignment uses a seeded, reproducible method, and the seed and the assignment are stored.

**AC 2:**
Given a customer is assigned to the holdout,
when any delivery job runs for that campaign (push, in-app card, "Offers for you" entry),
then no MK contact of any kind is sent or displayed to that customer for that campaign. The delivery log contains zero holdout sends. A daily automated check alerts on any holdout send.

**AC 3 (edge):**
Given a holdout customer opens the "Pay Later" hub on their own,
when "My limit" loads,
then the service display (US-208) behaves exactly as it does for treatment customers. The assignment itself is not visible to the customer.

**AC 4 (persistence):**
Given a customer was in the holdout for a campaign,
when the same campaign runs again in a later nightly cycle,
then the customer keeps their assignment (not re-randomised) for the life of the campaign.

**AC 5 (negative):**
Given an analyst or an operator tries to move a holdout customer into treatment,
when the change is submitted,
then it is rejected and the attempt is audit-logged.

**Dependencies**
Depends on: US-202
Blocks: US-204, US-205, US-210

**Open Questions:** OQ-17C-05: AC 3 treats the hub service display as outside the experiment. Please confirm that is intended.
**Notes:** Skill 13 Flow 1 step 2; DD-04; Skill 10 §4 ("persistent random holdout"); FM-5 ("permanent random holdout").

---

### Story ID: US-204
**Feature:** AW-02 Delivery-time consent and suitability re-check | **Epic:** EP-1 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As a **Lane A customer (existing HLB customer who has never used PoP)**,
I want **HLB to check again, at the moment of sending, that I still allow marketing and am still eligible**,
so that **I never receive an offer after I have withdrawn consent or my situation has changed**.

**Acceptance Criteria**

**AC 1:**
Given a treatment customer is queued for delivery,
when the delivery service is about to send,
then it re-checks, at send time:
- active MK consent in the consent ledger (Part A · E-01);
- no active early-warning flag;
- the anti-stacking gate still returns PASS;
- the CCP still returns F6 = PRE-APPROVED.

The offer is sent only if all four pass.

**AC 2:**
Given MK consent was withdrawn after the list was built,
when the re-check runs,
then the send is suppressed and logged as `offer_suppressed{reason=CONSENT_WITHDRAWN}` (KR 4.1). The customer stays in the treatment group for analysis (intention-to-treat).

**AC 3:**
Given the CCP changed adversely after the list was built (for example, F6 is no longer PRE-APPROVED, or a new CIC obligation tripped the gate),
when the re-check runs,
then the send is suppressed with the matching reason code.

**AC 4 (error):**
Given the consent ledger is unavailable at send time,
when the re-check runs,
then no send occurs (fail closed). The batch is retried within the campaign window, and an alert is raised.

**Dependencies**
Depends on: US-201, US-203, US-222, US-244; Part A · E-01, E-05
Blocks: US-206

**Notes:** Skill 13 Flow 1 error path ("Delivery-time consent re-check suppresses the offer"); C1; KR 4.1.

---

### Story ID: US-205
**Feature:** AW-02 Incrementality and guardrail measurement | **Epic:** EP-1 | **Size:** M
**Priority:** Must-have | **Label:** v1

**Story**
As an **HLB marketing analyst**,
I want **a per-campaign report of treated vs holdout take-up, with complaint and consent-withdrawal guardrails**,
so that **we keep only campaigns that add incremental, responsible take-up (KR 3.1, G-6, G-7)**.

**Acceptance Criteria**

**AC 1 (KR 3.1):**
Given a campaign has completed `campaign.attribution_window_days`,
when the report runs,
then it shows:
- take-up for treatment and for holdout, where take-up = booked HLB PoP loans within the window divided by group size (intention-to-treat);
- uplift = treated take-up − holdout take-up, with a confidence interval and a significance test result;
- group sizes.

**AC 2 (KR 3.1 stop rule):**
Given uplift is not statistically significant after 2 campaign cycles,
when the report runs,
then the campaign is flagged "stop" in the workspace, and the campaign owner is notified (Skill 10 §4 alert threshold).

**AC 3 (G-6):**
Given complaints are logged with the contact ID of the triggering campaign,
when the report runs,
then it shows complaints per 1,000 marketing contacts for the campaign. An alert is raised when the rate exceeds the configured HLB baseline (baseline `[DECISION REQUIRED]`, Skill 10 G-6).

**AC 4 (G-7):**
Given MK or data-consent withdrawal events are recorded with a timestamp,
when the monthly guardrail job runs,
then it reports the consent-withdrawal rate for Awareness-contacted customers, holdout customers and all customers. It raises an alert on a month-on-month spike, as defined in the G-7 alert rule.

**AC 5 (G-1 tagging):**
Given a loan is booked by a customer in an Awareness campaign within the attribution window,
when the loan is booked,
then the loan record carries `origin=AWARENESS` and the campaign ID and arm. This lets G-1 MOB3 30+ be reported for Awareness-originated loans.

**AC 6 (edge):**
Given a holdout customer books a PoP loan on their own,
when take-up is computed,
then it counts toward holdout take-up. It is not excluded.

**Dependencies**
Depends on: US-200, US-203, US-204, US-206, US-209; Part B · AP-01 (loan booking event)
Blocks: US-210

**Open Questions:** OQ-17C-04: the attribution window is not defined.
**Notes:** Skill 10 KR 3.1, G-6, G-7 and §4.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 1.3 [AW-04]: Pre-approved limit surfaced in HLB Connect
Epic: EP-1 Pre-approval Targeting | Label: [v1] | Complexity: M
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Story ID: US-206
**Feature:** AW-04 In-app offer card (MK consent) | **Epic:** EP-1 | **Size:** M
**Priority:** Must-have | **Label:** v1

**Story**
As a **Lane A customer who has never used PoP and has given marketing consent**,
I want to **see an HLB Connect offer that tells me how much I can pay over time, where, and the cost range**,
so that **I can decide whether paying over time suits me, without pressure or hidden costs**.

**Acceptance Criteria**

**AC 1:**
Given I am in the treatment group, have passed the delivery re-check (US-204) and have MK consent,
when the offer is delivered,
then I receive a push notification and/or an in-app card, according to the campaign channel, reading "You can pay over time up to {L} VND at {partner categories}". The card also appears under "Pay Later → Offers for you".

**AC 2:**
Given I open the offer card,
when it renders,
then it shows:
- my pre-approved limit L;
- the all-in cost **range**;
- the eligible partner merchants;
- a link to the full terms.

This follows the CO-02 principle and FM-4.

**AC 3:**
Given I tap "See my options",
when the tap is handled,
then I enter Consideration (Part B · CO-01) with my current CCP, and no package above F2 is shown.

**AC 4 (negative):**
Given I have no MK consent, or I am in the holdout,
when "Offers for you" loads,
then no Awareness offer card appears.

**AC 5 (instrumentation):**
Given an offer is delivered, opened, tapped through or dismissed,
when each action occurs,
then an event is emitted: `offer_delivered`, `offer_opened`, `offer_clicked` or `offer_dismissed`. Each carries customer pseudonymous ID, campaign ID, arm and CCP version, for KR 3.1 and for uplift training (DF-1).

**Dependencies**
Depends on: US-200, US-202, US-204; Part A · E-05; Part B · CO-01, CO-02
Blocks: US-205, US-207, US-209

**Notes:** Skill 13 Flow 1 steps 3–4; IA §3 "Offers for you".

---

### Story ID: US-207
**Feature:** AW-04 Offer re-validation on open | **Epic:** EP-1 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As a **Lane A customer**,
I want **my offer to reflect my current situation when I open it**,
so that **I am never shown a limit HLB would no longer approve**.

**Acceptance Criteria**

**AC 1:**
Given I open an offer card delivered earlier,
when the card loads,
then it re-reads my current CCP and anti-stacking status before it shows the limit.

**AC 2:**
Given my CCP changed adversely after delivery (for example, a new CIC obligation lowered F2 or the limit),
when the card loads,
then it shows "Your offer has been updated" with the new limit. If I no longer qualify, it shows "no offer" instead of the old limit.

**AC 3:**
Given an early-warning flag became active after delivery,
when I open the card,
then no limit is offered, and the card is withdrawn from "Offers for you".

**AC 4 (error):**
Given the CCP service is unavailable when I open the card,
when the card loads,
then it shows "We can't show your offer right now — please try again later". It does not show the cached limit.

**Dependencies**
Depends on: US-206, US-222, US-244; Part A · E-05
Blocks: None

**Notes:** Skill 13 Flow 1 error path ("offer card re-validates on open"). AC 4 (no stale limit) is derived from C2 and G-4.

---

### Story ID: US-208
**Feature:** AW-04 "My limit" service display without MK consent (DD-11) | **Epic:** EP-1 | **Size:** S
**Priority:** Must-have | **Label:** v1 — **pending legal confirmation (A-13-5, OQ-13-01)**

**Story**
As a **Lane A customer who has scoring consent but not marketing consent**,
I want to **see my own pre-approved limit when I open the "Pay Later" hub myself**,
so that **I know my status without HLB pushing marketing to me**.

**Acceptance Criteria**

**AC 1:**
Given I have SC consent and no MK consent, and my CCP has F6 = PRE-APPROVED,
when I open "Pay Later → My limit" on my own initiative,
then my pre-approved limit is shown as a service display. No push, card or banner is sent before or after my visit.

**AC 2 (negative):**
Given I have no MK consent,
when any HLB Connect screen outside the "Pay Later" hub loads,
then no pre-approval banner or prompt is shown.

**AC 3 (feature flag):**
Given legal has not confirmed A-13-5,
when the build ships,
then the service display for no-MK customers sits behind the flag `aw04_service_display_no_mk`, which is **off** by default. It is switched on only with the recorded legal sign-off reference (OQ-13-01). With the flag off, no-MK customers see "Pay Later" without a pre-approved limit.

**AC 4 (suppression):**
Given I have an active early-warning flag (US-224),
when I open "My limit",
then the pre-approved limit is shown as paused, with no amount available to use.

**AC 5 (instrumentation):**
Given I view "My limit",
when the screen renders,
then `hub_limit_viewed` is logged with `mk_consent=false|true` and `flag_state`. This measures the reach effect of DD-11.

**Dependencies**
Depends on: US-200; Part A · E-01, E-05
Blocks: US-247

**Open Questions:** OQ-13-01 / A-13-5 (legal).
**Notes:** Skill 13 Flow 1 alternative path; DD-11; IA §3 "My limit"; journey step 1.3 (A).

---

### Story ID: US-209
**Feature:** AW-04 Opt-out and complaint handling | **Epic:** EP-1 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As a **Lane A customer**,
I want to **stop PoP marketing in one tap, and to complain if an offer bothered me**,
so that **I stay in control and am not contacted again against my wishes**.

**Acceptance Criteria**

**AC 1:**
Given I am viewing an offer card,
when I tap "Stop these offers",
then my MK consent is withdrawn in the consent ledger (Part A · E-01) with a timestamp. It takes effect before the next delivery job, and I see confirmation.

**AC 2:**
Given I opt out or my complaint is logged,
when any MK delivery job runs within `campaign.mk_cooling_period_days`,
then I receive no MK contact.

**AC 3 (instrumentation):**
Given a complaint is received through any HLB channel about a PoP marketing contact,
when it is logged,
then it carries the campaign ID and contact ID, and it counts toward G-6 (US-205).

**AC 4 (edge):**
Given I opted out of MK,
when I open the "Pay Later" hub,
then my service display and my existing loans are unaffected (US-208).

**Dependencies**
Depends on: US-206; Part A · E-01
Blocks: US-202, US-205

**Notes:** Skill 13 Flow 1 error path. Frequency caps (RT-04) are parked (PL-09).

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 1.4 [AW-03]: Uplift model
Epic: EP-1 Pre-approval Targeting | Label: [v1-Should] | Complexity: L
Condition: ≥ 1 campaign cycle of holdout data (Skill 08)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Story ID: US-210
**Feature:** AW-03 Uplift model training and validation | **Epic:** EP-1 | **Size:** L
**Priority:** Should-have | **Label:** v1 (Should)

**Story**
As an **HLB marketing analyst**,
I want **an uplift model trained on our own treatment-vs-holdout results**,
so that **we target the customers whose take-up is *caused* by the offer, not those who would borrow anyway (DD-04, C4)**.

**Acceptance Criteria**

**AC 1 (condition):**
Given fewer than 1 completed campaign cycle of holdout outcomes exists,
when training is requested,
then training is blocked with the message "Uplift model requires ≥ 1 completed campaign cycle of holdout data".

**AC 2:**
Given ≥ 1 cycle of treatment and holdout outcomes (US-205),
when the model is trained,
then its inputs exclude protected attributes (gender, ethnicity, religion; Skill 13 §1.2). S12 digital engagement may be used, but only because this model outputs propensity, never risk (DD-07).

**AC 3 (per-component justification, FM-13):**
Given a trained uplift model,
when it is evaluated on a held-out randomised sample,
then it may be promoted only if its uplift ranking beats the v1 grade-based ranking on an uplift evaluation metric agreed with model risk (for example, a Qini curve). The comparison is recorded in the model registry (Part A · GV-01).

**AC 4:**
Given the model is promoted,
when it is registered,
then it carries a model version, and its F4 uplift score is written to the CCP (Part A · E-05). Its drift is monitored like other models (G-8).

**Dependencies**
Depends on: US-203, US-205; Part A · E-05, GV-01
Blocks: US-211

**Notes:** Skill 13 §1.1 F4; Flow 1 alternative path; DD-04.

---

### Story ID: US-211
**Feature:** AW-03 Uplift-ranked targeting | **Epic:** EP-1 | **Size:** S
**Priority:** Should-have | **Label:** v1 (Should)

**Story**
As an **HLB marketing analyst**,
I want to **rank Awareness candidates by uplift score instead of risk grade once the model is live**,
so that **limited contact slots go to customers for whom the offer makes a difference**.

**Acceptance Criteria**

**AC 1:**
Given a promoted uplift model exists,
when the AW-01 list is ranked,
then candidates are ordered by F4 uplift score. The eligibility conditions (US-200), the suitability filter (US-202) and the holdout (US-203) still apply unchanged.

**AC 2 (negative):**
Given a customer has a high uplift score but fails the suitability filter,
when the list is built,
then the customer is excluded. Uplift never overrides suitability.

**AC 3:**
Given the uplift model is paused (drift above `model.psi_action_threshold`, or failed validation),
when the list is built,
then ranking falls back to the v1 grade-based order, and the fallback is logged.

**Dependencies**
Depends on: US-210, US-200, US-202, US-203
Blocks: None

**Notes:** Skill 13 Flow 1 alternative path ("step 1 ranks candidates by uplift score rather than by grade").

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 1.5 [AW-05]: Platform-partner pre-selection
Epic: EP-1 Pre-approval Targeting | Label: [v1-Should] | Complexity: L
Condition: a Vietnam platform data-sharing agreement exists (D-08-2) — not assumed
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Story ID: US-212
**Feature:** AW-05 Partner pre-selection invitation | **Epic:** EP-1 | **Size:** L
**Priority:** Should-have | **Label:** v1 (Should, conditional)

**Story**
As **Anh Hùng (a gig driver who uses a platform partner's app)**,
I want to **see an "HLB pay-over-time invitation" inside the platform app I already use**,
so that **I learn about fair formal credit where I work, without having to find HLB first**.

**Acceptance Criteria**

**AC 1 (condition):**
Given no signed platform data-sharing agreement is registered in configuration,
when the AW-05 job is enabled,
then it refuses to run, and the feature stays off.

**AC 2:**
Given a signed agreement exists and Anh Hùng is eligible under the agreement's rules,
when the partner displays the invitation,
then the invitation opens the HLB Lane B flow (Part B · MX-01/AP-01). In that flow he gives his own SC consent (and optionally MK) before any HLB scoring.

**AC 3 (privacy):**
Given HLB exchanges data with the partner,
when an invitation is prepared,
then only the fields listed in the agreement's data-sharing schedule are exchanged. No CCP field values (F1, F2, F5) are sent to the partner.

**AC 4 (measurement):**
Given partner invitations are sent,
when the campaign runs,
then a random holdout per `campaign.holdout_share` is applied to partner-eligible customers, and KR 3.1 is reported separately for the partner channel.

**Dependencies**
Depends on: US-203, US-205; Part A · E-01; Part B · MX-01, AP-01
Blocks: None

**Open Questions:** OQ-17C-12: Skill 13 does not say which way data flows (partner → HLB eligibility signals, or HLB → partner selection). AC 3 is a proposed privacy constraint pending confirmation.
**Notes:** Skill 13 Flow 1 alternative path; HLB MY × Grab precedent [R8]; D-08-2.

---

## 2. EPIC 4 — Continuous Reassessment & Graduation (Journey stage: Purchase & Repayment)

Goal (Skill 16): every repayment refines the customer's profile. Good payers are offered better terms with explicit consent, and struggling payers are spotted early and supported. Success signals: G-1 MOB3 30+ within ceiling; KR 3.3 early-warning cure rate; G-3 graduation without a stacking increase.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 4.1 [PR-01]: Behavioural re-scoring on repayment events
Epic: EP-4 Continuous Reassessment & Graduation | Label: [v1] | Complexity: M
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Story ID: US-220
**Feature:** PR-01 Re-score on each repayment event | **Epic:** EP-4 | **Size:** M
**Priority:** Must-have | **Label:** v1

**Story**
As a **good payer**,
I want **every instalment I pay to update my credit profile**,
so that **my on-time behaviour counts toward better terms, even if I started with a thin file**.

**Acceptance Criteria**

**AC 1:**
Given a repayment event is posted to the PoP loan ledger,
when the event reaches the stream (DF-3),
then the S15 features are updated: days early or late, partial payment, payment-channel change. The behavioural model re-scores the customer, and a new CCP version n+1 is written with F1, F2 and F4 recomputed.

**AC 2:**
Given the new CCP version is written,
when it is stored,
then the history keeps version n unchanged, and version n+1 records:
- the triggering event ID;
- the model versions used;
- reason codes for any change in F1 (Part A · E-09).

**AC 3 (idempotency):**
Given the same repayment event is delivered twice,
when it is processed,
then only one CCP version is created. The duplicate is logged and dropped.

**AC 4 (edge: out-of-order):**
Given a repayment event arrives after a later event for the same loan has been processed,
when it is processed,
then the features are recomputed in event-time order (point-in-time correct, Skill 13 §4 feature store), and one corrected CCP version is written.

**AC 5 (early settlement):**
Given the customer settles the loan early,
when the settlement posts,
then the profile is refreshed. The early-settlement terms applied are the ones disclosed at acceptance (Part B · CO-02).

**AC 6 (downstream):**
Given CCP version n+1 changes F1, F2 or F6,
when it is written,
then the graduation evaluation (US-226), the early-warning evaluation (US-222) and the "My limit" display (US-247) read the new version on their next run or view.

**Dependencies**
Depends on: Part A · E-02, E-05, E-06, E-08, E-09
Blocks: US-221, US-222, US-226, US-247, US-260

**Notes:** Skill 13 Flow 4 step 2; S15; DF-3; user pain point "continuously reassess and refine".

---

### Story ID: US-221
**Feature:** PR-01 Missed-payment downgrade and drift fallback | **Epic:** EP-4 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As **Chị Lan (credit-risk lead)**,
I want **missed payments to be reflected in the next re-score, and re-scoring to fall back safely when a model drifts**,
so that **profiles stay accurate and no drifting model drives credit decisions (FM-9)**.

**Acceptance Criteria**

**AC 1:**
Given a scheduled instalment passes its due date unpaid,
when the next re-score runs,
then F1 is re-computed including the missed payment, and a reason code for the grade change is stored. The standard HLB collections process is triggered (outside this product).

**AC 2 (G-8):**
Given the monitoring job (Part A · GV-01) reports PSI > `model.psi_action_threshold` for the behavioural model or the challenger,
when re-scoring runs,
then it uses the champion scorecard. Graduation offers generated by the drifting model and not yet accepted are paused (US-228).

**AC 3:**
Given the fallback is active,
when I view the risk console,
then I see which model is serving, when the fallback began, and how many offers are paused.

**AC 4 (negative):**
Given a customer was referred through AD-01,
when any re-score runs,
then no referral attribute is present among the model inputs (Skill 13 §1.2 exclusion; C5). An automated schema check fails the build if one appears.

**Dependencies**
Depends on: US-220; Part A · E-06, GV-01
Blocks: US-228

**Notes:** Skill 13 Flow 4 error paths; DD-13; G-8. Collection-conduct standards are parked (PL-07).

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 4.2 [PR-02]: Early-warning → supportive outreach + suppression
Epic: EP-4 Continuous Reassessment & Graduation | Label: [v1] | Complexity: L
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Story ID: US-222
**Feature:** PR-02 Early-warning flag (rule-based v1) | **Epic:** EP-4 | **Size:** M
**Priority:** Must-have | **Label:** v1

**Story**
As **Chị Lan (credit-risk lead)**,
I want **customers likely to miss their next instalment to be flagged automatically, using rules I can configure**,
so that **HLB can support them before they fall behind, and stop offering them more credit**.

**Acceptance Criteria**

**AC 1:**
Given the rule set `ew.trigger_rules` is configured and version-controlled (Part A · OP-02),
when a CCP version is written (US-220) or the monthly batch runs,
then each customer with an active PoP loan is evaluated. The flag is set when any rule fires, and the rule ID and input values are stored.

**AC 2:**
Given a flag is set,
when it is stored,
then it carries a start timestamp, the triggering rule, and the next instalment's due date and amount.

**AC 3:**
Given a flagged customer meets `ew.flag_clear_rule`,
when the evaluation runs,
then the flag is cleared with an end timestamp. Suppressions lift only after that point (US-224).

**AC 4 (negative):**
Given the rules reference any excluded input (protected attributes, S12 digital engagement, referral status),
when the rule set is saved,
then validation rejects it.

**AC 5 (flag-rate indicator):**
Given flags are set and cleared,
when the daily job runs,
then the early-warning flag rate (a leading indicator, Skill 10 §5) is published to the risk console by segment and cohort.

**Dependencies**
Depends on: US-220; Part A · E-05, OP-02 (or version-controlled config)
Blocks: US-202, US-204, US-223, US-224, US-225, US-226, US-240, US-260

**Open Questions:** OQ-17C-06: `ew.flag_clear_rule` is not defined; a later move from rules to a predictive model needs the FM-13 justification.
**Notes:** Skill 13 Flow 4 alternative path; Skill 16 Feature 4.2 ("start with rule-based triggers").

---

### Story ID: US-223
**Feature:** PR-02 Supportive outreach | **Epic:** EP-4 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As a **struggling payer (for example, Anh Hùng in a slow week)**,
I want **a calm reminder of what is due and an easy way to tell HLB I'm having trouble**,
so that **I can act before I miss a payment, without feeling threatened**.

**Acceptance Criteria**

**AC 1:**
Given an early-warning flag is set (US-222),
when outreach is sent through HLB Connect and/or SMS (SC servicing purpose, Skill 15 §4),
then the message states the instalment due date and amount, and includes a "Talk to us" link to contact HLB about difficulty.

**AC 2 (tone):**
Given a message template is used,
when it is published,
then it has compliance sign-off recorded against the template version. It contains no threats, no mention of contacting third parties, and no penalty language beyond the late-payment consequences already disclosed in CO-02.

**AC 3:**
Given the customer taps "Talk to us",
when the request is submitted,
then a servicing case is created with the loan reference, and the customer sees a confirmation with the expected response channel.

**AC 4 (limit):**
Given a customer is flagged,
when outreach runs,
then no more than `ew.max_supportive_messages_per_cycle` messages are sent per repayment cycle.

**AC 5 (negative):**
Given the customer is flagged,
when any outreach is composed,
then it never contains a marketing offer, a graduation offer or a recommendation.

**Dependencies**
Depends on: US-222; Part A · E-01
Blocks: US-225

**Open Questions:** OQ-17C-06: which HLB team handles "Talk to us", and what can it offer while PR-05 flexible repayment is parked?
**Notes:** Skill 13 Flow 4 alternative path; journey step 4.3. Structured flexible repayment is PL-06 (parked; strong evidence [R64]).

---

### Story ID: US-224
**Feature:** PR-02 Suppression propagation | **Epic:** EP-4 | **Size:** M
**Priority:** Must-have | **Label:** v1

**Story**
As **Chị Lan (credit-risk lead)**,
I want **an early-warning flag to suppress marketing, graduation, invites and the pre-approved limit everywhere at once**,
so that **no part of the product offers more credit to someone who is struggling (DF-3)**.

**Acceptance Criteria**

**AC 1:**
Given a flag is set,
when it is written,
then within one processing cycle suppression flags reach the Campaign (AW), Recommendation (RT), Servicing & Graduation (PR) and Referral (AD) services. Each service records receipt.

**AC 2:**
Given a customer is flagged,
when any of these runs:
- Awareness list or delivery;
- recommendation build;
- graduation evaluation;
- invite eligibility check,

then the customer is excluded with reason `EARLY_WARNING`.

**AC 3 (pre-approval pause):**
Given a customer is flagged,
when "My limit" loads or the checkout decision (Part B · AP-01) reads the customer's F6,
then the pre-approved limit is treated as paused and is not usable for a new draw.

**AC 4 (pending graduation offer):**
Given a customer has an unaccepted graduation offer when the flag is set,
when they try to accept it,
then acceptance is blocked with "This offer is paused for now". The offer is not applied.

**AC 5 (clear):**
Given the flag is cleared (US-222 AC 3),
when the next evaluation runs,
then suppression lifts. Eligibility is then re-evaluated from the current CCP; a paused offer is not re-activated automatically.

**Dependencies**
Depends on: US-222; Part B · AP-01 (reads the pause)
Blocks: US-202, US-204, US-208, US-226, US-240, US-260

**Open Questions:** OQ-17C-06: AC 4 and the second half of AC 5 are derived from "no graduation offers while the flag is active" (Skill 13 Flow 4) and need confirmation.
**Notes:** Skill 13 Flow 4 alternative path; DF-3; journey step 4.3.

---

### Story ID: US-225
**Feature:** PR-02 Cure-rate and delinquency measurement | **Epic:** EP-4 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As **Chị Lan (credit-risk lead)**,
I want **the early-warning cure rate and MOB3 delinquency reported by segment and origin**,
so that **I can tell whether outreach works and whether risk stays within appetite (KR 3.3, G-1)**.

**Acceptance Criteria**

**AC 1 (KR 3.3):**
Given customers were flagged and received outreach,
when the monthly report runs,
then it shows the cure rate: the share of flagged-and-contacted customers who are current `metrics.ew_cure_window_days` after the outreach. It is shown by segment and cohort, with the prior quarter for comparison. An alert is raised if the cure rate falls below the prior quarter (Skill 10 §4).

**AC 2 (G-1):**
Given PoP loans reach month-on-book 3,
when the monthly job runs,
then MOB3 30+ is reported by segment (SAL-C, SAL-N, GIG, MSME, FTB), by cohort (A/B) and by loan origin (AWARENESS, CHECKOUT, GRADUATION, RECOMMENDATION, REFERRAL). A breach of the configured ceiling (`[DECISION REQUIRED]`, G-1) raises an alert to pause the relevant feature.

**AC 3 (data):**
Given an outreach message is sent,
when it is logged,
then the log carries the flag ID, the template version and the delivery status, so every cure can be tied to a contact.

**Dependencies**
Depends on: US-222, US-223, US-205 (origin tagging); Part A · GV-01
Blocks: None

**Notes:** Skill 10 KR 3.3, G-1, §4.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 4.3 [PR-03]: Graduation ladder (offered, explicitly consented step-ups)
Epic: EP-4 Continuous Reassessment & Graduation | Label: [v1] | Complexity: M
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Story ID: US-226
**Feature:** PR-03 Graduation eligibility and offer creation | **Epic:** EP-4 | **Size:** M
**Priority:** Must-have | **Label:** v1

**Story**
As a **good payer**,
I want **HLB to offer me better terms after I have paid on time**,
so that **my good behaviour opens a path to more flexible credit**.

**Acceptance Criteria**

**AC 1:**
Given a customer has at least `policy.graduation_on_time_instalments_N` consecutive on-time instalments on HLB PoP loans,
when the graduation evaluation runs after a CCP version update,
then a graduation **offer** is created only if all of these hold:
- F2 has been recomputed in this evaluation (refreshed);
- the anti-stacking gate (US-244) returns PASS on a CIC snapshot within `policy.stacking_cic_snapshot_max_age_days`;
- there is no active early-warning flag;
- no drift pause is active (US-221).

**AC 2:**
Given the offer is created,
when it is sized,
then each option (higher limit, access to longer tenors, better rate tier) follows `policy.graduation_step_ladder`. The resulting instalment capacity never exceeds the refreshed F2, and the limit follows the Skill 13 §1.1 rule min(policy limit for grade, F2 × tenor-adjusted factor).

**AC 3 (never auto-applied):**
Given the offer is created,
when it is stored,
then the customer's limit, tenors and rate tier are **unchanged**. The offer has status `OFFERED`. No service may write the new terms until an explicit acceptance event exists (US-227). An automated test asserts that an offer without an acceptance event leaves the terms unchanged.

**AC 4 (negative):**
Given the CIC snapshot shows concurrent consumer loans ≥ `policy.stacking_concurrent_consumer_loans_threshold`, or `policy.stacking_rising_obligations_rule` fires,
when the evaluation runs,
then no offer is created, and reason `STACKING` is logged.

**AC 5 (notification):**
Given an offer is created and the customer has SC consent,
when the offer is delivered in-app (the graduation offer touchpoint uses SC, Skill 15 §4),
then it appears in "Pay Later → My limit". A push is sent only if the customer has MK consent.

**Dependencies**
Depends on: US-220, US-222, US-224, US-244; Part A · E-05, E-07
Blocks: US-227, US-228, US-247, US-269

**Open Questions:** OQ-17C-07: should consecutive or cumulative on-time instalments count? Should AC 5's push-only-with-MK split be confirmed?
**Notes:** Skill 13 Flow 4 step 3; DD-05; C2; FM-2; RBI explicit-consent-per-increase benchmark [R30].

---

### Story ID: US-227
**Feature:** PR-03 Explicit acceptance or decline of a graduation offer | **Epic:** EP-4 | **Size:** M
**Priority:** Must-have | **Label:** v1

**Story**
As a **good payer**,
I want to **see exactly what changes, and choose whether to accept it**,
so that **my limit never grows without my say (C2)**.

**Acceptance Criteria**

**AC 1:**
Given I open a graduation offer,
when it renders,
then it shows my current and my new limit, tenor options and rate tier. For an example basket, it shows the effect on monthly instalment and total payable, with the all-in cost (Part B · CO-02). It offers two equal-weight buttons, "Accept" and "No thanks".

**AC 2:**
Given I tap "Accept" and pass `auth.graduation_acceptance_level`,
when acceptance is processed,
then F2 and the anti-stacking gate are re-checked at that moment (C2). If both pass, the new terms are applied. An acceptance record is written with the offer version, the terms shown, a timestamp and the authentication method (audit, Part A · GV-01).

**AC 3 (negative):**
Given I tap "No thanks" or ignore the offer,
when the offer is closed or expires after `policy.graduation_offer_validity_days`,
then my terms stay unchanged. No penalty applies and there is no effect on my CCP. I remain eligible for future evaluations.

**AC 4 (edge):**
Given my F2 dropped, or the gate failed, between offer and acceptance,
when I tap "Accept",
then the original offer is not applied. I see "Your offer has changed", with a re-sized offer I can accept separately, or no offer.

**AC 5 (instrumentation, G-3):**
Given I accept a graduation offer,
when acceptance completes,
then `graduation_accepted` is emitted, with the step type and my CIC concurrent-loan count at acceptance. This lets the stacking rate after re-offers (G-3) be tracked.

**Dependencies**
Depends on: US-226, US-244; Part A · E-05, GV-01; Part B · CO-02
Blocks: US-246, US-247

**Open Questions:** OQ-17C-07: authentication level, offer validity, and whether a better rate tier applies to existing loans or only to new ones.
**Notes:** Skill 13 Flow 4 step 3 ("must explicitly accept… nothing applies automatically"); DD-05; FM-2; journey step 4.4.

---

### Story ID: US-228
**Feature:** PR-03 Graduation pause controls | **Epic:** EP-4 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As **Chị Lan (credit-risk lead)**,
I want to **pause graduation offers when a guardrail or model breaks**,
so that **limit increases never go out on bad data or during a stacking rise**.

**Acceptance Criteria**

**AC 1:**
Given a drift fallback is active (US-221 AC 2),
when graduation offers generated by the drifting model are still unaccepted,
then they move to `PAUSED` and cannot be accepted. The customer sees "This offer is paused for now".

**AC 2:**
Given the G-3 stacking rate rises quarter on quarter, or G-1 breaches its ceiling for a segment (US-225, US-246),
when the guardrail alert fires,
then new graduation offer creation is paused for the affected segment until I resume it. Resume requires a recorded justification.

**AC 3:**
Given graduation is paused,
when I view the risk console,
then I see the pause reason, its scope (segment or model), its start time and the number of affected offers.

**Dependencies**
Depends on: US-221, US-226, US-246; Part A · GV-01
Blocks: None

**Notes:** Skill 13 Flow 4 error path; Skill 10 §3 ("breaching one pauses… graduation offers").

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 4.4 [PR-04]: CIC reporting of PoP loans
Epic: EP-4 Continuous Reassessment & Graduation | Label: [v1] | Complexity: S
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Story ID: US-229
**Feature:** PR-04 Report PoP loans and repayment status to CIC | **Epic:** EP-4 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As **Chị Lan (credit-risk lead)**,
I want **every booked PoP loan and its repayment status reported to CIC**,
so that **HLB meets its reporting obligation, customers build bureau history, and other lenders can see PoP exposure (FM-3)**.

**Acceptance Criteria**

**AC 1:**
Given a PoP loan is booked,
when the next CIC reporting cycle runs (schedule and format per the applicable CIC regulation and HLB's existing reporting process, `cic.reporting_schedule`),
then the loan is included with its amount, tenor and status.

**AC 2:**
Given repayments, missed payments or settlement occur,
when the next reporting cycle runs,
then the loan's status update is included.

**AC 3 (reconciliation):**
Given a reporting cycle completes,
when the reconciliation job runs,
then 100% of booked, reportable PoP loans in the ledger match a submitted record. Any mismatch raises an alert with the loan IDs.

**AC 4 (data minimisation):**
Given the CIC submission is prepared,
when the file or API payload is built,
then it contains only the fields CIC requires. No CCP-derived fields (F1–F6) are included.

**Dependencies**
Depends on: Part A · E-02; Part B · AP-01 (loan booking)
Blocks: US-230

**Open Questions:** OQ-17C-14: the CIC reporting schedule and format are not stated upstream; they must come from regulation / HLB's existing process (Skill02 RF-11).
**Notes:** Skill 13 Flow 4 step 1; Skill 08 PR-04 ("legal obligation").

---

### Story ID: US-230
**Feature:** PR-04 CIC reporting failure handling | **Epic:** EP-4 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As **Chị Lan (credit-risk lead)**,
I want **failed or rejected CIC submissions to be retried and escalated**,
so that **no PoP loan goes unreported (FM-12, CIC incident Sep 2025 [R71])**.

**Acceptance Criteria**

**AC 1:**
Given CIC is unavailable, or rejects a submission,
when the reporting job runs,
then the job retries per the reporting runbook and keeps the unsent records queued. It never drops them.

**AC 2:**
Given records remain unsubmitted at the regulatory deadline for the cycle,
when the deadline passes,
then an escalation alert goes to the credit-risk and compliance owners, with the count and loan IDs.

**AC 3:**
Given CIC rejects individual records for data errors,
when the rejection is received,
then each rejected record is listed with its CIC error code for correction and resubmission.

**Dependencies**
Depends on: US-229
Blocks: None

**Notes:** Skill 13 §4 External APIs (CIC); FM-12.

---

## 3. EPIC 5 — Suitable Repeat Offers & Anti-Stacking (Journey stage: Retention)

**Goal (Skill 16).** Good payers see a few relevant, affordable repeat-purchase suggestions at partner merchants. Customers who are building up debt elsewhere are protected from re-offers.

**Success signal.** KR 3.4 (good-payer repeat rate) rises while G-3 (stacking) does not.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 5.1 [RT-01]: Product / merchant-category recommendations
Epic: EP-5 Suitable Repeat Offers & Anti-Stacking | Label: [v1] | Complexity: L
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Story ID: US-240
**Feature:** RT-01 Weekly recommendation build | **Epic:** EP-5 | **Size:** L
**Priority:** Must-have | **Label:** v1

**Story**
As a **good payer**,
I want **HLB to suggest a few things I'm likely to buy and can actually afford at partner merchants**,
so that **repeat offers feel relevant rather than like spam**.

**Acceptance Criteria**

**AC 1 (eligibility):**
Given the weekly RT-01 job runs,
when it selects customers,
then it includes only customers who meet all of these conditions:
- active MK consent;
- an active PoP loan, or one completed within `retention.recent_completion_window_days`;
- no active early-warning flag;
- anti-stacking gate PASS (US-244).

**AC 1b (random holdout, C4/FM-5; Skill 13 Flow 5 fixed 2026-09-30, OQ-17C-15 resolved):**
Given the eligible set for this run,
when the job assigns treatment,
then a random share equal to `campaign.holdout_share` is placed in a Retention holdout and receives no recommendations. The assignment is logged, and incremental repeat take-up (KR 3.4) is reported as treatment minus holdout.

**AC 2 (logic, rules-first):**
Given an eligible customer,
when recommendations are built,
then the job does the following, in order:
1. It takes candidate categories from F4 top purchase categories (S9 spend categories and past PoP baskets).
2. It matches those categories to the partner merchant catalogue of PoP-eligible products, including merchant-subsidised 0% promotions.
3. It keeps only items for which at least one package from Part B · CO-01 has an instalment ≤ F2.

v1 uses rules-based category suggestion. No recommender model is used (Skill 11 mitigation).

**AC 3 (cap):**
Given more than `retention.max_recommendations_per_week` items qualify,
when the list is finalised,
then at most that many are kept per customer per week.

**AC 4 (negative: affordability):**
Given no catalogue item has a package with an instalment ≤ F2,
when the job runs,
then zero recommendations are produced for that customer. There is no fallback to unaffordable items (G-4).

**AC 5 (negative: fairness):**
Given the recommendation rules are evaluated,
when inputs are read,
then S12 digital engagement may affect only channel choice, never eligibility on risk grounds (DD-07). Protected attributes are never read.

**AC 6 (error):**
Given the partner catalogue feed is missing or stale,
when the job runs,
then category-level recommendations are produced without specific products, and the fallback is logged (Skill 13 Flow 5 error path).

**Dependencies**
Depends on: US-222, US-224, US-244; Part A · E-01, E-05; Part B · CO-01
Blocks: US-241, US-242, US-243, US-246

**Open Questions:** OQ-17C-09 (definition of "recent completion"); OQ-17C-15 (Retention has no holdout, although FM-5 lists Retention).
**Notes:** Skill 13 Flow 5 steps 1–2; DF-3 suppression; Skill 16 Feature 5.1.

---

### Story ID: US-241
**Feature:** RT-01 Recommendation card | **Epic:** EP-5 | **Size:** M
**Priority:** Must-have | **Label:** v1

**Story**
As a **good payer**,
I want **each suggestion to show the monthly amount and the total I would pay**,
so that **I can judge it honestly before I tap in**.

**Acceptance Criteria**

**AC 1:**
Given I have recommendations and MK consent at view time,
when I open "Pay Later → Offers for you",
then each card shows the partner name, category or product, "pay over {tenor} months from {instalment} VND/month", total {X} VND, and the all-in cost. These values come from the CO-01/CO-02 package, not from a separate calculation.

**AC 2:**
Given I tap a card,
when the tap is handled,
then I enter Consideration (Part B · CO-01) for that partner and item, with my current CCP.

**AC 3 (consent re-check):**
Given my MK consent was withdrawn after the weekly build,
when "Offers for you" loads, or a push is about to be sent,
then no recommendation is shown or sent.

**AC 4 (0% truth):**
Given a card shows 0%,
when it renders,
then the 0% applies only because a merchant subsidy is configured for that item, and the total payable equals the price. Otherwise the card shows the risk-tier rate (DD-10; FM-4).

**AC 5 (instrumentation):**
Given I view, tap or dismiss a card, or book a loan from it,
when the action occurs,
then `rec_viewed`, `rec_clicked`, `rec_dismissed` or `loan_booked{origin=RECOMMENDATION}` is emitted, for KR 3.4 and G-6.

**Dependencies**
Depends on: US-240; Part A · E-01; Part B · CO-01, CO-02
Blocks: US-242, US-246

**Notes:** Skill 13 Flow 5 step 3; IA §3 "Offers for you"; journey step 5.2.

---

### Story ID: US-242
**Feature:** RT-01 Dismissal suppression | **Epic:** EP-5 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As a **good payer**,
I want **HLB to stop suggesting a category after I've dismissed it a few times**,
so that **I'm not nagged about things I don't want**.

**Acceptance Criteria**

**AC 1:**
Given I have dismissed recommendations in the same category `retention.dismissal_suppression_threshold` times,
when the next weekly build runs,
then that category is suppressed for me for `retention.dismissal_suppression_period_days`.

**AC 2:**
Given one category is suppressed,
when the build runs,
then other eligible categories may still be recommended, within the weekly cap.

**AC 3 (edge):**
Given the suppression period ends,
when the build runs,
then the dismissal counter for that category resets to 0.

**AC 4:**
Given I tap "Stop these offers" on any recommendation card,
when the tap is handled,
then my MK consent is withdrawn (same behaviour as US-209). All recommendations stop, and the event counts toward G-7.

**Dependencies**
Depends on: US-240, US-241; Part A · E-01
Blocks: None

**Open Questions:** OQ-17C-09: the suppression period is not defined.
**Notes:** Skill 13 Flow 5 error path; ASSUMPTION A-13-9.

---

### Story ID: US-243
**Feature:** RT-01 Single re-engagement nudge after loan completion | **Epic:** EP-5 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As a **good payer who has finished a PoP loan**,
I want **at most one reminder that my limit is still available**,
so that **I hear about it once, not repeatedly**.

**Acceptance Criteria**

**AC 1:**
Given my PoP loan is completed, I have MK consent and no new PoP purchase within `retention.reengagement_inactivity_days`,
when the nudge job runs and the anti-stacking gate returns PASS,
then I receive one re-engagement nudge.

**AC 2 (negative):**
Given I have already received the nudge for this completed loan,
when the job runs again,
then no further nudge is sent for that loan.

**AC 3 (negative):**
Given I have no MK consent, or I have an early-warning flag or a stacking signal,
when the job runs,
then no nudge is sent.

**Dependencies**
Depends on: US-240, US-244; Part A · E-01
Blocks: None

**Notes:** Skill 13 Flow 5 alternative path ("at most one re-engagement nudge"). Frequency caps are parked (PL-09).

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 5.2 [RT-02]: Anti-stacking gate
Epic: EP-5 Suitable Repeat Offers & Anti-Stacking | Label: [v1] | Complexity: S
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Story ID: US-244
**Feature:** RT-02 Anti-stacking gate evaluation | **Epic:** EP-5 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As **Chị Lan (credit-risk lead)**,
I want **one gate that checks CIC and HLB exposure before any re-offer**,
so that **customers building up loans elsewhere are protected from more credit (FM-3, G-3)**.

**Acceptance Criteria**

**AC 1:**
Given a customer is being evaluated for any re-offer,
when the gate runs,
then it returns **SUPPRESS** if any of these is true:
- CIC shows concurrent consumer loans ≥ `policy.stacking_concurrent_consumer_loans_threshold`;
- `policy.stacking_rising_obligations_rule` fires;
- HLB total exposure would exceed `policy.internal_exposure_cap_vnd`.

Otherwise it returns **PASS**.

**AC 2 (data freshness):**
Given the gate runs for a re-offer (not a checkout draw),
when it reads CIC data,
then it uses a snapshot no older than `policy.stacking_cic_snapshot_max_age_days`. At a checkout draw, the gate uses the draw-time CIC pull from Part B · AP-01 (C2: "CIC checked at each new draw").

**AC 3 (fail closed):**
Given no CIC snapshot within the maximum age is available, and a fresh pull fails,
when the gate runs for a re-offer,
then it returns **SUPPRESS** with reason `CIC_UNAVAILABLE`.

**AC 4 (explainability):**
Given the gate returns SUPPRESS,
when the result is stored,
then it carries a reason code (Part A · E-09): `STACKING_CONCURRENT`, `STACKING_RISING`, `EXPOSURE_CAP` or `CIC_UNAVAILABLE`, plus the input values and snapshot date.

**AC 5 (silent to customer):**
Given the gate suppresses a marketing re-offer,
when the customer uses the app,
then no message about the suppression is shown, and existing loans and servicing are unaffected (journey step 5.3).

**Dependencies**
Depends on: Part A · E-02 (CIC ingestion), E-09; Part B · AP-01 (draw-time pull)
Blocks: US-202, US-204, US-226, US-227, US-240, US-243, US-245, US-269

**Open Questions:** OQ-17C-08 covers three points: the definition of "rising obligations"; whether HLB's own PoP loans count toward the concurrent count; and AC 3's fail-closed rule, which needs confirmation.
**Notes:** Skill 13 Flow 5 step 2; G-3; Skill 08 RT-02 ("CIC exposure check plus internal exposure cap before any re-offer").

---

### Story ID: US-245
**Feature:** RT-02 Gate enforcement across all offer paths | **Epic:** EP-5 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As **Chị Lan (credit-risk lead)**,
I want **every path that offers more credit to call the same anti-stacking gate**,
so that **there's no side door around stacking protection**.

**Acceptance Criteria**

**AC 1:**
Given any of these paths is about to offer credit:
- Awareness list or delivery (US-202, US-204);
- recommendation build or nudge (US-240, US-243);
- graduation creation or acceptance (US-226, US-227);
- passport issuance (US-269);
- checkout decision (Part B · AP-01),

when it runs,
then it calls the shared gate. It does not implement its own logic, and it records the gate result ID.

**AC 2 (test):**
Given the integration test suite runs,
when a test customer is set with CIC concurrent loans at the threshold,
then every path listed in AC 1 suppresses or declines that customer.

**AC 3 (audit):**
Given a sample of re-offers from the last month,
when the audit query runs,
then 100% carry a gate result ID with outcome PASS.

**Dependencies**
Depends on: US-244
Blocks: US-246

**Notes:** C2; FM-3.

---

### Story ID: US-246
**Feature:** RT-02 Repeat-rate and stacking guardrail measurement | **Epic:** EP-5 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As **Chị Lan (credit-risk lead)**,
I want **good-payer repeat use and stacking tracked together**,
so that **we grow repeat lending only while stacking does not rise (KR 3.4, G-3)**.

**Acceptance Criteria**

**AC 1 (KR 3.4):**
Given the monthly job runs,
when it computes the good-payer repeat rate,
then it reports the share of customers with ≥ 3 on-time instalments who took a second PoP loan within 6 months. This is the Skill 10 KR 3.4 definition. It is split by origin (RECOMMENDATION, GRADUATION, organic).

**AC 2 (G-3):**
Given the monthly portfolio CIC refresh completes,
when the job runs,
then it reports the stacking rate: the share of PoP customers with ≥ `policy.stacking_concurrent_consumer_loans_threshold` concurrent consumer loans at CIC after an HLB re-offer. It is shown by quarter.

**AC 3 (guardrail action):**
Given the G-3 stacking rate rises quarter on quarter, or KR 3.4 rises together with G-3,
when the alert fires,
then new recommendations and graduation offers are paused for the affected segment (US-228), and I am notified.

**AC 4:**
Given re-offers (recommendations, nudges, graduation offers) are logged,
when the job runs,
then each is joined to the customer's later CIC concurrent-loan count, so G-3 can be attributed by re-offer type.

**Dependencies**
Depends on: US-227, US-241, US-245; Part A · GV-01
Blocks: US-228

**Notes:** Skill 10 KR 3.4, G-3 and §4 ("Rises together with G-3 → pause re-offers").

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 5.3 [RT-03]: "Ready limit" dashboard
Epic: EP-5 Suitable Repeat Offers & Anti-Stacking | Label: [v1-Should] | Complexity: S
Condition: with Feature 4.3 (PR-03)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Story ID: US-247
**Feature:** RT-03 Ready limit and graduation progress | **Epic:** EP-5 | **Size:** S
**Priority:** Should-have | **Label:** v1 (Should)

**Story**
As a **good payer**,
I want to **see my available limit and how close I am to my next step-up**,
so that **I know what I can afford and how on-time payments help me**.

**Acceptance Criteria**

**AC 1:**
Given I have SC consent and a current CCP,
when I open "Pay Later → My limit",
then I see my ready limit and the maximum monthly instalment I can take on (F2). This is a service display, so no MK consent is needed.

**AC 2:**
Given I am working toward graduation,
when "My limit" renders,
then I see "{x} of `policy.graduation_on_time_instalments_N` on-time payments toward your next step". If an offer is open, a link to it (US-227) is shown.

**AC 3 (suppression):**
Given I have an active early-warning flag,
when "My limit" renders,
then my limit is shown as paused and graduation progress is hidden. A link to "Talk to us" is shown instead.

**AC 4 (negative):**
Given the dashboard renders,
when it shows my data,
then it never shows my F1 grade letter, my PD band or other lenders' CIC details.

**AC 5 (error):**
Given the CCP service is unavailable,
when "My limit" loads,
then it shows "Limit temporarily unavailable" rather than a cached figure.

**Dependencies**
Depends on: US-208, US-220, US-226; Part A · E-05
Blocks: None

**Open Questions:** OQ-17C-16: how "ready limit" is computed (the F6 limit minus outstanding PoP principal, or otherwise) is not defined. AC 4 needs confirmation.
**Notes:** Skill 13 Flow 4 step 4; IA §3 "My limit"; journey step 5.1.

---

## 4. EPIC 6 — Responsible Referral & Merchant Flywheel (Journey stage: Advocacy)

**Goal (Skill 16).** Loyal good payers can invite other people and suggest shops. Nobody is exposed to debt pressure or harassment of reference contacts. Rewards follow repayment, not borrowing.

**Success signal.**
- KR 3.5: the referred cohort's MOB3 30+ is at or below the non-referred cohort's.
- 100% of rewards are released only after K on-time instalments.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 6.1 [AD-01]: Repayment-conditioned invite
Epic: EP-6 Responsible Referral & Merchant Flywheel | Label: [v1] | Complexity: M
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Story ID: US-260
**Feature:** AD-01 Invite eligibility and personal link | **Epic:** EP-6 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As a **good payer**,
I want to **invite someone I know to HLB pay-over-time with a personal link**,
so that **I can share something that worked for me without taking on responsibility for their debt**.

**Acceptance Criteria**

**AC 1 (eligibility):**
Given I open "Pay Later → Invite & earn",
when eligibility is checked,
then "Invite someone" is enabled only if all of these are true:
- I have ≥ `policy.graduation_on_time_instalments_N` on-time instalments;
- my F1 ∈ {A, B};
- I have no active early-warning flag;
- I have active RF consent.

**AC 2 (negative):**
Given I fail any eligibility condition,
when the screen loads,
then "Invite someone" is disabled with a neutral reason: "Available after {N} on-time payments" or "Turn on referral sharing in Privacy & consent". My grade and my flags are never shown.

**AC 3:**
Given I am eligible and tap "Invite someone",
when the link is generated,
then it is unique to me. It expires after `referral.invite_link_validity_days`, and I can share it through my phone's share sheet.

**AC 4 (no reference contacts):**
Given the invite flow runs,
when any screen or permission request is shown,
then HLB never asks me to give contacts, never asks for access to my phone contacts, and never records me as a reference or guarantor for the invitee (DD-08; X-01).

**AC 5 (RF withdrawal):**
Given I withdraw RF consent,
when the withdrawal is recorded,
then my unused links are deactivated, and no new link can be generated.

**AC 6 (cap):**
Given I already have `referral.max_active_invites_per_inviter` open links,
when I tap "Invite someone",
then no new link is created until one expires or is used.

**Dependencies**
Depends on: US-220, US-222, US-224; Part A · E-01, E-05
Blocks: US-261, US-262, US-263

**Open Questions:**
- OQ-17C-10: should invite eligibility reuse the graduation N?
- OQ-17C-11: link validity and the invite cap.
- OQ-17C-11: what happens to pending rewards when RF consent is withdrawn?

**Notes:** Skill 13 Flow 6 AD-01 step 1; SD-6a; C5; journey step 6.1.

---

### Story ID: US-261
**Feature:** AD-01 Invitee instant eligibility check | **Epic:** EP-6 | **Size:** M
**Priority:** Must-have | **Label:** v1

**Story**
As an **invitee**,
I want to **check whether I'm eligible straight from my friend's link, on my own consent**,
so that **I find out quickly, and my friend never learns my finances**.

**Acceptance Criteria**

**AC 1:**
Given I open a valid invite link,
when the landing page loads,
then I enter the Lane B flow (Part B · MX-01/AP-01/AP-02). I must give my **own** SC consent before any scoring. MK and RF are optional and off by default.

**AC 2 (independent scoring):**
Given I complete eKYC and consent,
when CreditPulse builds my CCP,
then it uses only my own data. The inviter's data is never read. No attribute indicating "referred" or the inviter's identity is passed to the scoring or policy layers (Skill 13 §1.2; C5). A schema check enforces this.

**AC 3 (attribution only):**
Given I arrived through a link,
when my application is stored,
then the referral link ID is stored **only** in the Referral ledger (DF-5), for reward and KR 3.5 tracking, and is separated from the feature store.

**AC 4 (expired or invalid link):**
Given the link is expired, deactivated or malformed,
when I open it,
then I can still apply through the normal Lane B flow. The application is not attributed to any inviter.

**AC 5 (no reference contacts):**
Given I apply,
when the application runs,
then I am never asked for reference contacts, guarantors or phone-contacts access.

**AC 6 (existing customer):**
Given I am already an HLB customer or an existing PoP borrower,
when I open the link,
then (per OQ-17C-11) I am routed to my existing HLB Connect "Pay Later" hub. Attribution then follows the rule confirmed in Skill 22.

**Dependencies**
Depends on: US-260; Part A · E-01, E-05; Part B · MX-01, AP-01, AP-02, AP-03
Blocks: US-262, US-264, US-265

**Open Questions:** OQ-17C-11: Skill 13 designs only the Lane B invitee, so the handling of existing-customer invitees and their attribution is open.
**Notes:** Skill 13 Flow 6 AD-01 step 2; DD-08; [R61]; journey step 6.2.

---

### Story ID: US-262
**Feature:** AD-01 Repayment-conditioned reward release | **Epic:** EP-6 | **Size:** M
**Priority:** Must-have | **Label:** v1

**Story**
As a **good payer who invited a friend**,
I want **my reward to unlock when my friend repays on time**,
so that **the scheme rewards responsible use, not borrowing**.

**Acceptance Criteria**

**AC 1:**
Given my invitee booked a PoP loan through my link,
when their `policy.referral_reward_on_time_instalments_K`-th consecutive on-time instalment posts,
then the Referral ledger releases my reward of type `referral.reward_type` and records the release with the triggering instalment IDs.

**AC 2 (negative: timing):**
Given my invitee has signed up, been approved or booked a loan, but has not yet made K on-time instalments,
when the ledger is evaluated,
then no reward, partial reward or advance is released (C5; DD-08; Skill 13 §6).

**AC 3 (negative: delinquency):**
Given my invitee misses an instalment before reaching K,
when the ledger is evaluated,
then no reward is released for that referral (Skill 13 Flow 6 error path).

**AC 4 (negative: inviter status):**
Given I have an active early-warning flag or have withdrawn RF consent at the time of release,
when the release would occur,
then the ledger follows the rule confirmed in OQ-17C-11. Until that rule is confirmed, the reward is held, not paid.

**AC 5 (100% compliance, KR 3.5):**
Given the monthly audit runs,
when it compares every released reward with the invitee's instalment history,
then 100% of releases have ≥ K on-time instalments before the release timestamp. Any exception raises a compliance alert.

**Dependencies**
Depends on: US-261; Part B · AP-01 (loan booking), Part A · E-02 (repayment events)
Blocks: US-263, US-265

**Open Questions:** OQ-13-02 (K and the reward type); OQ-17C-11 (consecutive vs cumulative K; a delinquent-then-cured invitee).
**Notes:** Skill 13 Flow 6 AD-01 step 3; DF-5; FM-6.

---

### Story ID: US-263
**Feature:** AD-01 Inviter status view (privacy by design) | **Epic:** EP-6 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As a **good payer who invited a friend**,
I want to **see whether my reward is on its way without seeing my friend's private details**,
so that **inviting someone never turns me into their debt monitor**.

**Acceptance Criteria**

**AC 1:**
Given I open "Invite & earn",
when my invites are listed,
then each invite shows only one of these statuses:
- "Link not used yet";
- "Your friend's reward unlocks after K on-time payments";
- "Reward unlocked";
- "Your invite wasn't used".

**AC 2 (negative: privacy):**
Given my invitee applied,
when I view the status,
then I never see their decision tier, limit, loan amount, instalment count, payment dates, delinquency or reason codes (Skill 13 Flow 6 step 4).

**AC 3 (declined invitee):**
Given my invitee was declined, or abandoned the application,
when I view the status,
then I see only "Your invite wasn't used", and no decline details (Skill 13 Flow 6 error path).

**AC 4 (no inference):**
Given my invitee's loan became delinquent before K,
when I view the status,
then the status does not change in a way that reveals the delinquency. It moves to a terminal status only per the rule confirmed in OQ-17C-11. Until then it stays "Your friend's reward unlocks after K on-time payments", and no instalment count is shown.

**Dependencies**
Depends on: US-260, US-262
Blocks: None

**Open Questions:** OQ-17C-11: Skill 13 does not define the terminal status shown to the inviter for a delinquent invitee. Any change of status leaks repayment conduct.
**Notes:** Skill 13 Flow 6 step 4 and error paths; privacy by design.

---

### Story ID: US-264
**Feature:** AD-01 Referral abuse controls | **Epic:** EP-6 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As **Chị Lan (credit-risk lead)**,
I want **self-referrals and duplicate identities blocked from earning rewards**,
so that **the referral scheme cannot be gamed (G-2)**.

**Acceptance Criteria**

**AC 1:**
Given an application arrives through an invite link,
when integrity checks run (Part B · AP-03, F5),
then the ledger records the result of three checks:
- same identity as the inviter (self-referral);
- a device shared with the inviter;
- a duplicate CCCD across referrals.

**AC 2:**
Given any of those checks is positive,
when the referral is evaluated,
then no reward is ever released for it, and the case is flagged for the fraud team.

**AC 3 (separation):**
Given an abuse flag is raised on a referral,
when the invitee's credit decision is made,
then the referral flag itself does not change the invitee's F1. Only the standard F5 integrity rules apply to their application, so that "referred" is not a risk feature (C5).

**Dependencies**
Depends on: US-261; Part B · AP-03
Blocks: None

**Notes:** Skill 13 Flow 6 error path ("F5 integrity flags apply, and no reward is released").

---

### Story ID: US-265
**Feature:** AD-01 Referral quality monitoring and invite pause | **Epic:** EP-6 | **Size:** S
**Priority:** Must-have | **Label:** v1

**Story**
As **Chị Lan (credit-risk lead)**,
I want **the referred cohort's delinquency compared with the non-referred cohort, with invites paused automatically if referrals perform worse**,
so that **the referral scheme never erodes risk quality (KR 3.5, FM-6)**.

**Acceptance Criteria**

**AC 1 (KR 3.5):**
Given referred loans reach MOB3,
when the monthly job runs,
then MOB3 30+ is reported for referred and non-referred customers **in the same segment**.

**AC 2 (pause):**
Given the referred cohort's MOB3 30+ exceeds the non-referred cohort's in a segment,
when the job runs,
then new invite-link generation is paused (US-260 AC 1 is disabled) for inviters in that segment, and I am alerted. Existing released rewards are unaffected.

**AC 3 (resume):**
Given invites are paused,
when I resume them in the risk console,
then a recorded justification is required, and the action is audit-logged.

**AC 4 (instrumentation):**
Given invite activity occurs,
when events are emitted,
then `invite_link_created`, `invite_link_opened`, `invitee_applied`, `invitee_booked`, `reward_released` and `reward_withheld{reason}` are logged. They carry pseudonymous IDs and no invitee credit data.

**Dependencies**
Depends on: US-261, US-262; Part A · GV-01
Blocks: None

**Open Questions:** OQ-17C-17: the minimum cohort size before the AC 2 comparison is statistically meaningful is not defined.
**Notes:** Skill 10 KR 3.5 and §4; Skill 13 Flow 6 error path ("invites are paused"); ASSUMPTION A-10-6.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 6.2 [AD-02]: Refer-a-merchant via Sổ Bán Hàng
Epic: EP-6 Responsible Referral & Merchant Flywheel | Label: [v1-Should] | Complexity: M
Condition: Sổ Bán Hàng partnership terms allow it (not public)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Story ID: US-266
**Feature:** AD-02 Suggest a shop | **Epic:** EP-6 | **Size:** S
**Priority:** Should-have | **Label:** v1 (Should, conditional)

**Story**
As a **good payer**,
I want to **suggest my favourite local shop to HLB**,
so that **I can pay over time there too, and the shop gains a new way to sell**.

**Acceptance Criteria**

**AC 1 (condition):**
Given the configuration does not record that the Sổ Bán Hàng partnership terms permit merchant referrals,
when the app loads "Invite & earn",
then "Suggest a shop" is hidden.

**AC 2:**
Given the feature is enabled,
when I tap "Suggest a shop",
then I enter the shop's name and phone number. Before I can submit, I must confirm that the shop owner has agreed to be contacted by HLB.

**AC 3:**
Given I submit,
when the lead is created,
then it is sent to the HLB merchant team queue with the shop details and my pseudonymous ID. I see "Thanks — HLB will contact the shop".

**AC 4 (data):**
Given a lead is not onboarded within `merchant_lead.retention_days`,
when the retention job runs,
then the shop's contact data is deleted (PDPL purpose limitation [R46]).

**AC 5 (negative):**
Given a lead is submitted,
when it is processed,
then no reward is given for the submission itself (see US-268).

**Dependencies**
Depends on: Part A · E-01
Blocks: US-267, US-268

**Open Questions:** OQ-17C-13: how the shop owner's consent is evidenced (customer attestation only vs a confirmation by HLB at first contact), and the retention period.
**Notes:** Skill 13 Flow 6 AD-02 step 1; SD-6b; journey step 6.4.

---

### Story ID: US-267
**Feature:** AD-02 Shop onboarding via Sổ Bán Hàng | **Epic:** EP-6 | **Size:** M
**Priority:** Should-have | **Label:** v1 (Should, conditional)

**Story**
As a **shop owner**,
I want to **be contacted once, choose whether to join, and if I join, get set up to accept HLB pay-over-time through Sổ Bán Hàng**,
so that **customers can buy from me on instalments without my running a credit business**.

**Acceptance Criteria**

**AC 1:**
Given HLB contacts me from a lead,
when I am first contacted,
then HLB confirms that I agree to further contact. If I decline, the lead is closed, my data is deleted, and I am not contacted again from that lead.

**AC 2:**
Given I agree to join,
when onboarding completes through Sổ Bán Hàng (HLB account opening, bookkeeping, QR, e-invoice [R66]),
then my shop is registered as a PoP acceptance point and appears in the partner catalogue used by RT-01.

**AC 3 (separate consent):**
Given I am onboarded as a merchant,
when HLB wants to use my Sổ Bán Hàng sales data (S5) to assess me as an MSME borrower,
then that happens only if I give separate merchant consent for it. Joining as an acceptance point does not imply that consent (C6; Skill 13 Flow 6 AD-02 step 2).

**AC 4 (negative):**
Given I have joined,
when the referring customer's identity is stored,
then my shop is not shown the referring customer's credit details.

**Dependencies**
Depends on: US-266; Part A · E-01; Part B · MX-01 (acceptance-point integration)
Blocks: US-268

**Notes:** Skill 13 Flow 6 AD-02 step 2; [R66].

---

### Story ID: US-268
**Feature:** AD-02 Merchant-funded perk after first PoP sale | **Epic:** EP-6 | **Size:** S
**Priority:** Should-have | **Label:** v1 (Should, conditional)

**Story**
As a **good payer who suggested a shop**,
I want **a perk at that shop once it has made its first pay-over-time sale**,
so that **I'm thanked for growing the network, not for borrowing**.

**Acceptance Criteria**

**AC 1:**
Given a shop I suggested is onboarded,
when it books its first PoP sale (with any customer),
then I receive the merchant-funded perk the shop has configured, redeemable at that shop.

**AC 2 (negative):**
Given the shop was onboarded but has not yet made a PoP sale,
when I view "Invite & earn",
then no perk has been issued. The status shows "Your shop is joining".

**AC 3 (negative):**
Given the shop's first PoP sale was made to me,
when the perk is evaluated,
then the perk is still issued. The perk never depends on my own borrowing and is not conditional on it.

**Dependencies**
Depends on: US-266, US-267
Blocks: None

**Open Questions:** OQ-17C-13: the perk's type and value are set by each merchant, and no HLB policy governs it. Should HLB set a floor or ceiling?
**Notes:** Skill 13 Flow 6 AD-02 step 3; BNPL +~20% merchant sales [R57].

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature 6.3 [AD-03]: Good-payer credit passport (debt-gated)
Epic: EP-6 Responsible Referral & Merchant Flywheel | Label: [v1-Should] | Complexity: L
Condition: total-debt affordability gate (FM-7)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Story ID: US-269
**Feature:** AD-03 Passport issuance gated on total-debt affordability | **Epic:** EP-6 | **Size:** M
**Priority:** Should-have | **Label:** v1 (Should, gated)

**Story**
As a **good payer who has finished a PoP loan on time**,
I want to **earn a "good-payer passport" that records my on-time history**,
so that **my record is recognised, but only when more credit would not hurt me (FM-7)**.

**Acceptance Criteria**

**AC 1:**
Given I have completed a PoP loan with every instalment paid on time,
when the passport evaluation runs,
then a passport is issued only if **all** of these hold:
- `policy.passport_total_debt_affordability_rule` passes on my total CIC obligations;
- the anti-stacking gate (US-244) returns PASS;
- I have no active early-warning flag.

**AC 2 (negative: FM-7):**
Given my total CIC obligations fail the affordability rule,
when the evaluation runs,
then no passport is issued, and I see no invitation to use one. Reason `TOTAL_DEBT` is logged internally.

**AC 3 (re-check):**
Given I hold a passport,
when my CCP is refreshed and I later fail the affordability rule or the gate, or get an early-warning flag,
then the passport is suspended until I pass again.

**AC 4:**
Given a passport is issued,
when I view it in "Pay Later → Good-payer passport",
then it shows my on-time PoP record, and that the record is also reported to CIC (PR-04). It does not show my F1 grade or PD band.

**Dependencies**
Depends on: US-220, US-222, US-226, US-229, US-244; Part A · E-05
Blocks: US-270

**Open Questions:** OQ-17C-18: the affordability rule is `[DECISION REQUIRED]`. FM-7 also says the passport should be "targeted to no-existing-debt thin files", and Skill 13 does not carry that into Flow 6. Should the passport be restricted to that group?
**Notes:** Skill 13 Flow 6 AD-03 step 1; FM-7 [R62]; SD-6c.

---

### Story ID: US-270
**Feature:** AD-03 Present passport at a partner merchant | **Epic:** EP-6 | **Size:** M
**Priority:** Should-have | **Label:** v1 (Should, gated)

**Story**
As a **good payer holding a passport**,
I want to **show it at a partner merchant to unlock my graduation tier there**,
so that **my good record works for me where I shop**.

**Acceptance Criteria**

**AC 1 (customer-controlled):**
Given I hold an active passport,
when I tap "Show passport" at a partner merchant,
then presentation happens only on my explicit action. The passport is never shared with merchants automatically.

**AC 2:**
Given I present the passport at checkout,
when the checkout decision runs (Part B · AP-01),
then the decision applies my currently accepted graduation tier. It re-checks F2 and the anti-stacking gate at that draw (C2). The passport never bypasses those checks.

**AC 3 (privacy):**
Given the merchant receives the presentation,
when it is displayed to the merchant,
then the merchant sees only that the passport is valid, and never my limit, grade, income estimate or CIC data.

**AC 4 (suspended):**
Given my passport is suspended (US-269 AC 3),
when I try to present it,
then it is shown as unavailable, and checkout proceeds on my normal CCP.

**Dependencies**
Depends on: US-269, US-227; Part B · AP-01, MX-01
Blocks: None

**Open Questions:** OQ-17C-18: Skill 13 does not say what the passport unlocks that the CCP-driven checkout decision does not already apply. It also does not say what, if anything, the merchant should see. AC 3 is a proposed minimum.
**Notes:** Skill 13 Flow 6 AD-03 step 2; Skill 08 AD-03 ("customer-controlled record").

---

## 5. Cross-cutting views

### 5.1 Story Map Grid (Epic × Feature × Stories)

| Epic | Feature | Label | Stories |
|---|---|---|---|
| **EP-1 Pre-approval Targeting** | 1.1 AW-01 Target-list generator | v1 | US-200, US-201 |
| | 1.2 AW-02 Suitability filter + holdout | v1 | US-202, US-203, US-204, US-205 |
| | 1.3 AW-04 Pre-approved limit in HLB Connect | v1 | US-206, US-207, US-208, US-209 |
| | 1.4 AW-03 Uplift model | v1-Should | US-210, US-211 |
| | 1.5 AW-05 Platform-partner pre-selection | v1-Should | US-212 |
| **EP-4 Continuous Reassessment & Graduation** | 4.1 PR-01 Behavioural re-scoring | v1 | US-220, US-221 |
| | 4.2 PR-02 Early warning + outreach + suppression | v1 | US-222, US-223, US-224, US-225 |
| | 4.3 PR-03 Graduation ladder | v1 | US-226, US-227, US-228 |
| | 4.4 PR-04 CIC reporting | v1 | US-229, US-230 |
| **EP-5 Suitable Repeat Offers & Anti-Stacking** | 5.1 RT-01 Recommendations | v1 | US-240, US-241, US-242, US-243 |
| | 5.2 RT-02 Anti-stacking gate | v1 | US-244, US-245, US-246 |
| | 5.3 RT-03 Ready-limit dashboard | v1-Should | US-247 |
| **EP-6 Responsible Referral & Merchant Flywheel** | 6.1 AD-01 Repayment-conditioned invite | v1 | US-260, US-261, US-262, US-263, US-264, US-265 |
| | 6.2 AD-02 Refer-a-merchant | v1-Should | US-266, US-267, US-268 |
| | 6.3 AD-03 Good-payer passport | v1-Should | US-269, US-270 |

### 5.2 Dependency Graph

**Cross-part foundations (must exist before Part C can be integration-tested):**
- Part A: E-01 consent ledger (purpose check and send-time re-check); E-05 CCP API (versioned reads); E-08 refresh loop; E-07 tier policy; E-09 reason codes; E-02 ingestion (repayment and CIC events); GV-01 audit, model registry and monitoring; OP-02 policy console or config for the §0 parameters.
- Part B: CO-01/CO-02 (package pricing reused by US-206, US-227 and US-241); AP-01 (loan booking events, draw-time CIC pull, reading the early-warning pause and the shared gate); AP-02/AP-03 (invitee eKYC and F5); MX-01 (invitee and acceptance-point integration).

**Within Part C (→ means "must precede"):**
```
US-220 (re-score) ─┬─→ US-221 ─→ US-228
                   ├─→ US-222 (EW flag) ─┬─→ US-223 ─→ US-225
                   │                     └─→ US-224 (suppression) ──┐
                   ├─→ US-226 (graduation offer) ─→ US-227 ─→ US-247 │
                   └─→ US-260 (invite) ─→ US-261 ─→ US-262 ─→ US-263 │
                                               ├─→ US-264          │
                                               └─→ US-265          │
US-244 (anti-stacking gate) ─→ US-245 ─→ US-246 ─→ US-228           │
   │                                                               │
   └──────────────┬────────────────────────────────────────────────┘
                  ▼
US-200 (candidates) ─→ US-202 (suitability) ─→ US-203 (holdout) ─→ US-201 (campaign) ─→ US-204 (send re-check)
                  ─→ US-206 (offer card) ─→ US-207, US-209 ─→ US-205 (KR 3.1 report) ─→ US-210 ─→ US-211
US-208 (DD-11 service display) — needs US-200 only; flag off until legal sign-off
US-240 (recs) ─→ US-241 ─→ US-242; US-240 ─→ US-243
US-229 (CIC reporting) ─→ US-230; US-229 ─→ US-269 ─→ US-270
US-266 ─→ US-267 ─→ US-268
US-212 (AW-05) — needs US-203, US-205 and a signed partner agreement
```

**Critical ordering inside Part C.** First US-220, US-222/US-224 and US-244. They supply the suppression and gate signals that every offer path in EP-1, EP-4, EP-5 and EP-6 consumes, so no offer path may go live before them. This matches the Skill 16 build order (EP-4 → EP-1, EP-5 → EP-6).

### 5.3 Instrumentation Coverage (required KRs and guardrails)

| Metric | Definition source | Stories / ACs that instrument it |
|---|---|---|
| **KR 3.1** Incremental take-up vs holdout | Skill 10 §2 | US-205 AC 1–2, AC 6; US-203 AC 1–2; US-206 AC 5; US-200 AC 6; US-212 AC 4 |
| **KR 3.3** Early-warning cure rate | Skill 10 §2 | US-225 AC 1, AC 3; US-222 AC 5 (flag-rate leading indicator) |
| **KR 3.4** Good-payer repeat rate | Skill 10 §2 | US-246 AC 1; US-241 AC 5 |
| **KR 3.5** Referral quality + 100% reward compliance | Skill 10 §2 | US-265 AC 1–2, AC 4; US-262 AC 5 |
| **G-1** MOB3 30+ by segment/cohort | Skill 10 §3 | US-225 AC 2; US-205 AC 5 (origin tagging); US-265 AC 1 |
| **G-3** Stacking rate after re-offer | Skill 10 §3 | US-246 AC 2–4; US-227 AC 5; US-244 AC 4 |
| **G-6** Marketing complaint rate | Skill 10 §3 | US-205 AC 3; US-209 AC 3; US-241 AC 5 |
| **G-7** Consent-withdrawal rate | Skill 10 §3 | US-205 AC 4; US-209 AC 1; US-242 AC 4 |
| KR 4.1 (supporting) consent-matched use | Skill 10 §2 | US-204 AC 2; US-200 AC 2 |

### 5.4 Definition of Done — Part C additions

Every Part C story must pass the standard DoD (Skill 17 template: ACs pass; unit and integration tests; peer review; WCAG 2.1 AA; error states; analytics events; feature flag; docs). It must also pass these checks:
- [ ] Every policy value is read from a §0 named parameter. No literal thresholds appear in code (automated lint or test).
- [ ] Every read of the CCP by the story's service passes the correct consent-purpose check (SC / MK / RF). This is verified by a test with consent withdrawn.
- [ ] Every path that offers credit has tests for: an active early-warning flag, an anti-stacking SUPPRESS, and MK consent withdrawn between build and send.
- [ ] No limit, tenor or rate change is persisted without an acceptance event (applies to US-226, US-227 and US-270).
- [ ] No referral attribute reaches the feature store or model inputs (schema test; US-221 AC 4, US-261 AC 2).
- [ ] Privacy review: no screen reveals another person's credit data (inviter/invitee, merchant/customer).
- [ ] Events named in the ACs are emitted with pseudonymous IDs and appear in the §5.3 metric pipeline.

---

## 6. Story Count Summary and Later Parking Lot

### 6.1 Story Count Summary (Part C)

```
Epics covered:          4   (EP-1, EP-4, EP-5, EP-6)
Features covered:      15   (10 [v1] + 5 [v1-Should])
Total stories:         43   (34 Must-have [v1] + 9 Should-have [v1-Should])
Total ACs:            190   (154 on Must-have stories + 36 on Should-have stories)
  by epic:   EP-1 13 stories / 59 ACs · EP-4 11 / 48 · EP-5 8 / 35 · EP-6 11 / 48
Story sizes:           S 25 · M 15 · L 3 · XL 0
Story points / sprints: not computed. Team velocity and a points scale are not given upstream,
                        and Skill 14 re-estimation is pending (OQ-16-01, OQ-17C-19). Sizes are
                        converted when the three parts are merged.
```

### 6.2 Later Stories Parked (listed, not written)

| Parking ID | Feature | Epic | Status | Stories that would be written if promoted |
|---|---|---|---|---|
| PL-06 | **PR-05** Cash-flow-aligned / flexible repayment (daily or weekly deduction, skip-a-payment) | EP-4 | Later — pending user assessment. Recommended for promotion; strongest-evidence parked item [R64] | Choose a repayment rhythm aligned to inflows; request skip-a-payment through "Talk to us"; affordability re-check on schedule change. This would also give US-223 "Talk to us" a concrete remedy |
| PL-07 | **PR-06** Collection-conduct standards ("never contact relatives/references") | EP-4 | Later — pending user assessment. Skill 11 recommends promotion (RICE rank 7, Kano Basic) | Enforce the no-third-party-contact rule in collections tooling; audit collections contacts. v1 already requires no reference contacts at application (US-260 AC 4, US-261 AC 5) |
| PL-08 | **PR-07** Financial-literacy nudges (late-payment consequences) | EP-4 | Later — pending user assessment | Explain the CIC impact at booking and before a due date |
| PL-09 | **RT-04** Marketing contact-frequency caps and quiet periods | EP-5 | Later — pending user assessment. Partly covered by E-01, AW-02, US-209 and US-242 | Cross-channel weekly cap across Awareness and Retention; quiet hours |
| PL-10 | AW-06 Broadcast campaigns | EP-1 | Rejected permanently (brief R-4) | None. US-201 AC 4 enforces the rejection |
| PL-12 | AD-04 Referral leaderboards / cash-for-sign-up | EP-6 | Rejected permanently [R61] | None. US-262 AC 2 enforces the rejection |

---

## 7. Open Items Raised by Part C (carry to Skill 22)

Each item is a **design gap in Skill 13** or an unresolved policy value. The ACs above are written against named parameters, or against the proposed behaviour marked in the story's Open Questions, and nothing has been silently decided.

| # | Item | Stories affected | Type |
|---|---|---|---|
| OQ-17C-01 | **RESOLVED 2026-09-30** (Skill 13 §1.1 fixed). **Original: Tier-policy vs AW-01 mismatch.** AW-01 selects F3 ≠ THIN, which includes CONSENTED-PARTNER. The Skill 13 §1.1 tier policy returns PRE-APPROVED only for BUREAU+BANK or BANK-OBSERVED, so a CONSENTED-PARTNER customer with F1 ∈ {A,B} falls through to the final ELSE → DECLINE. This affects MSME (Sổ Bán Hàng) customers. Should CONSENTED-PARTNER be eligible for pre-approval or instant approval? | US-200 (and Part A · E-07) | Design gap |
| OQ-17C-02 | "Recent hardship episode" has no source system and no look-back window. The fail-closed suitability rule (US-202 AC 4) needs confirmation | US-202 | Design gap + `[DECISION REQUIRED]` |
| OQ-17C-03 | Definition of "never used PoP credit": is it HLB PoP only? Are previously declined applicants included? What about BNPL at other lenders seen in CIC? | US-200 | Design gap |
| OQ-17C-04 | The KR 3.1 take-up attribution window (`campaign.attribution_window_days`) is not defined | US-205 | Design gap |
| OQ-17C-05 | The holdout and the DD-11 service display: holdout customers can still see their limit in the hub. Please confirm that the experiment measures the effect of *contact* only | US-203, US-208 | Design gap |
| OQ-17C-06 | Early warning. Undefined items: the flag-clear rule; the message cap per cycle; which team owns "Talk to us" and what it can offer while PR-05 is parked; the handling of pending graduation offers when a flag is raised; whether a paused offer is re-activated after the flag clears | US-222–US-224 | Design gap |
| OQ-17C-07 | Graduation. Undefined items: consecutive vs cumulative N; offer validity; the authentication level for acceptance; whether a better rate tier applies to existing loans or only new ones; step sizes (`policy.graduation_step_ladder`); whether a push notification needs MK consent (the graduation touchpoint is SC per Skill 15) | US-226, US-227 | Design gap + `[DECISION REQUIRED]` |
| OQ-17C-08 | Anti-stacking. Undefined items: the "rising total obligations" rule; whether HLB's own PoP loans count toward the concurrent-loan threshold; the maximum CIC snapshot age for re-offers; the proposed fail-closed behaviour when CIC is unavailable (US-244 AC 3) | US-244, US-226 | Design gap |
| OQ-17C-09 | Retention. Undefined items: "recently completed" loan window; the duration of dismissal suppression | US-240, US-242 | Design gap |
| OQ-17C-10 | Is invite eligibility the same N as graduation (Skill 13 reuses the symbol N), or a separate parameter? | US-260 | Design gap |
| OQ-17C-11 | Referral. Undefined items: link validity; invite cap; consecutive vs cumulative K; whether an invitee who is late then cured still counts; the effect of inviter RF withdrawal or an inviter early-warning flag on a pending reward; existing-customer invitees (Skill 13 designs only Lane B). Also, **the status the inviter sees when the invitee becomes delinquent**: any status change leaks repayment conduct | US-260–US-263 | Design gap (privacy) |
| OQ-17C-12 | AW-05: which way does data flow between the partner and HLB, and what is shared? US-212 AC 3 proposes that no CCP field values go to the partner | US-212 | Design gap |
| OQ-17C-13 | Refer-a-merchant. Undefined items: how the shop owner's consent to contact is evidenced; the lead-data retention period; whether HLB governs merchant perks | US-266–US-268 | Design gap |
| OQ-17C-14 | The CIC reporting schedule and format are not stated upstream. They must come from the CIC regulation and HLB's existing process (Skill02 RF-11) | US-229, US-230 | External fact needed |
| OQ-17C-15 | **RESOLVED 2026-09-30:** Flow 5 now has a holdout (US-240 AC 1b). Original: FM-5 and C4 list **Retention** as needing uplift/suitability targeting with a random holdout, but Skill 13 Flow 5 has no holdout. Should RT-01 recommendations carry a holdout? | US-240, US-246 | Design gap (binding-condition coverage) |
| OQ-17C-16 | The "ready limit" computation (F6 limit minus outstanding PoP principal, or otherwise) is not defined. Please also confirm that the F1 letter grade is never shown to customers | US-247 | Design gap |
| OQ-17C-17 | The minimum cohort size before the referred vs non-referred MOB3 comparison can trigger an invite pause is not defined | US-265 | Design gap |
| OQ-17C-18 | The passport. The total-debt affordability rule is `[DECISION REQUIRED]`. FM-7's "targeted to no-existing-debt thin files" is not carried into Skill 13 Flow 6. It is also unclear what the passport unlocks beyond the CCP-driven decision, and what the merchant sees | US-269, US-270 | Design gap |
| OQ-17C-19 | Story points and sprint count cannot be computed: there is no velocity or points scale, and Skill 14 re-estimation is pending (OQ-16-01) | All | Planning input |
| — | Inherited, unchanged: OQ-13-01 / A-13-5 (legal status of DD-11; US-208 ships behind a default-off flag); OQ-13-02 (N, K, holdout %, reward type, other policy values); A-13-9 (3 dismissals); G-1, G-2 and G-6 baselines and ceilings `[DECISION REQUIRED]` | Various | Carried |

**Interface requirements Part C places on Parts A and B.** Confirm these at merge:
1. Part B · AP-01 must read the early-warning pause (US-224 AC 3) and call the shared anti-stacking gate (US-245 AC 1).
2. Part A · E-01 must expose a send-time consent check with sub-second freshness for delivery jobs (US-204 AC 1).
3. Part A · E-05/E-06 must enforce the "no referral attribute" schema rule (US-221 AC 4, US-261 AC 2).
4. Part A · OP-02 must hold every §0 parameter with audit.

---

## 8. Source Index

No new external sources were used. Every behaviour, number and constraint traces to the upstream files below. `[R#]` citations resolve to `_research-notes-07-13.md`.

| Source | Used for |
|---|---|
| `05-specify/16-epic-feature-decomposition.md` (Skill 16) | Feature list, codes, labels, conditions, parking lot, build order |
| `04-design/13-solution-design.md` (Skill 13) | §1.1 CCP fields and tier policy; §1.2 exclusions (S12, referral, protected attributes); Flows 1, 4, 5, 6 (happy, alternative and error paths); §3 IA ("Pay Later" hub, campaign workspace, risk console); §4 DF-1, DF-3, DF-5; DD-04, DD-05, DD-07, DD-08, DD-10, DD-11, DD-13; §6 exclusions; A-13-5, A-13-9; OQ-13-01/02 |
| `03-define/10-success-metrics.md` (Skill 10) | KR 3.1, 3.3, 3.4, 3.5, 4.1 definitions; G-1, G-3, G-6, G-7, G-8; §4 measurement plan and alert rules |
| `03-define/07-problem-solution-fit.md` (Skill 07) | Binding conditions C1–C6; failure modes FM-2, FM-3, FM-5, FM-6, FM-7, FM-9, FM-12, FM-13 |
| `03-define/08-v1-product-scoping.md` (Skill 08) | MoSCoW labels; RT-02 internal exposure cap; AD-03 "customer-controlled"; D-08-1, D-08-2; rejected AW-06, AD-04 |
| `05-specify/15-user-journey-map.md` (Skill 15) | Stages 1, 4, 5, 6 steps; touchpoint consent purposes (§4); personas per lane |
| `_context.md` | SD-1 to SD-7; consent purposes SC / MK / RF |
| `02-discover/05-persona-research.md` | Persona names: Anh Hùng (GIG), Chị Lan (credit-risk lead) |
| `[R8]` HLB MY × Grab; `[R30]` RBI explicit consent per limit increase; `[R46]` PDPL purpose consent; `[R50]` Aydin limit-increase RCT; `[R57]` BNPL merchant sales; `[R61]` Bryan–Karlan–Zinman referral enforcement; `[R62]` Burke et al. credit-builder RCT; `[R64]` repayment flexibility RCT; `[R66]` Sổ Bán Hàng; `[R71]` CIC incident | Evidence behind the design decisions implemented here (cited in upstream files) |
| `.agents/skills/00-case-studies/by-output-type/user-story-examples.md` | Card format and AC testability bar only (not cited as evidence) |

---

# ═══ ADDENDUM — Stories for features promoted in Skill 22 (User decision, Skill 22, 2026-09-30) ═══

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature: PR-05 Payout-aligned weekly repayment option (GIG, MSME)
Epic: EP-4 | Label: [v1] | Complexity: M
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Story US-231: As **Anh Hùng, a gig driver paid weekly by the platform**, I want **to repay weekly on the day after my usual payout** so that **my instalments fit when money actually arrives.**
Size: M | Priority: Must-have | Dependencies: US-100 (packages), US-106 (cost disclosure), Part A E-02 (S4/S5 signals)

  AC 1: Given the applicant's segment evidence shows platform payouts (S4) or merchant settlements (S5) with a stable weekday pattern over `repayment.weekly_pattern_min_weeks` weeks,
        when packages are built,
        then each tenor is also offered with a weekly schedule, where the due weekday = the detected payout weekday + 1.
  AC 2: Given a weekly schedule is shown,
        when the cost disclosure renders,
        then it shows the weekly instalment, the number of payments, the **total payable** and the all-in APR computed on the weekly schedule. The total payable is never higher than the monthly equivalent for the same tenor and rate.
  AC 3 (negative): Given no stable payout weekday is detected, or the customer is not GIG/MSME,
        when packages are built,
        then no weekly option is shown. Monthly schedules only.
  AC 4 (affordability): Given a weekly option,
        when affordability is checked,
        then F2 is compared against the weekly instalment × `repayment.weeks_per_month_factor`. G-4 (0 breaches) applies.

Story US-232: As a **gig worker on a weekly schedule**, I want **to change back to monthly** so that **I'm not locked in if my income pattern changes.**
Size: S | Priority: Must-have | Dependencies: US-231

  AC 1: Given an active weekly-schedule loan,
        when the customer requests a switch in "My plans",
        then a monthly schedule for the remaining balance is quoted with its total payable and applied only after explicit confirmation.
  AC 2 (negative): Given the loan has a missed instalment outstanding,
        when a switch is requested,
        then the switch is offered through the servicing case, not self-service.

Story US-233: As **HLB credit risk**, I want **weekly-schedule loans tracked separately** so that **we learn whether flexibility lowers default here as it did in the RCT evidence [R64].**
Size: S | Priority: Must-have | Dependencies: US-231; Skill 19 events

  AC 1: Given a loan is booked,
        when `loan_booked` fires,
        then it carries `schedule_type` ∈ {MONTHLY, WEEKLY}.
  AC 2: Given MOB3 is computed,
        when the monthly report runs,
        then MOB3 30+ is reported by schedule type and segment (G-1).

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Feature: PR-06 Collection-conduct standards
Epic: EP-4 | Label: [v1] | Complexity: S
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Story US-234: As **a borrower who is behind on a payment**, I want **HLB to contact only me, at reasonable times, and respectfully** so that **I'm not shamed in front of family or colleagues.**
Size: S | Priority: Must-have | Dependencies: US-223 (early-warning outreach)

  AC 1: Given any reminder, outreach or collection contact,
        when it is scheduled,
        then it goes only to the borrower's own verified contacts. No reference, relative, employer or third party is ever contacted (none are collected, DD-08).
  AC 2: Given a contact is scheduled,
        when the local time is outside `ew.contact_hours`,
        then it is deferred to the next allowed window.
  AC 3: Given contacts in the current cycle have reached `ew.max_supportive_messages_per_cycle` (or `collections.max_contacts_per_week`),
        when another is requested,
        then it is suppressed and logged.
  AC 4 (negative): Given a message template,
        when it is not in the Compliance-approved template library (VNBA Code of Conduct, Decision 15/QĐ-HHNH [Skill20 E2]),
        then it cannot be sent.

Story US-235: As **HLB Compliance**, I want **third-party collection agents bound to the same code** so that **delegating collection does not bypass it.**
Size: S | Priority: Must-have | Dependencies: US-234

  AC 1: Given a case is assigned to an external agent,
        when the assignment is created,
        then the agent's contract includes the code and the assignment is refused if the agent's code attestation is missing or expired.
  AC 2: Given an agent contact,
        when it is logged,
        then time, channel and template ID are recorded for audit.

Story US-236: As **a borrower**, I want **a simple complaint channel about collection conduct** so that **problems are fixed.**
Size: S | Priority: Must-have | Dependencies: US-234

  AC 1: Given the "My plans" screen,
        when the borrower opens "Góp ý về cách liên hệ" (feedback on how we contact you),
        then a complaint can be filed and acknowledged with a reference number.
  AC 2: Given a conduct complaint,
        when it is filed,
        then contacts on that loan pause for `collections.complaint_pause_days` pending review, and the complaint counts toward G-6.

**Addendum totals:** 6 stories, 17 ACs. **Updated Skill 17 totals:** 123 stories, ≈ 545 ACs.
