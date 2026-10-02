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

Story US-035: As an **HLB compliance officer / DPO**, I want **the AI Law technical documentation pack and human-oversight procedure generated from the live system of record**, so that **the engine's conformity assessment is completed before 1 Sep 2027 (KR 4.2).**
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
        then it shows KR 4.2 status (`NOT STARTED` / `INCOMPLETE` / `COMPLETE`) and the days remaining to 1 Sep 2027.
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
| KR 4.2 AI Law documentation before 1 Sep 2027 | US-035 |
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
