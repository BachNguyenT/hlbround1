# Data Requirements
**Product:** HLB Point-of-Purchase Credit Decisioning (working name) — **HLB CreditPulse** | **Skill:** 19 | **Status:** Done (2026-09-30)
**Depends on:** nfr.md | **Feeds into:** product-prd.md (Section 3 — System Design, data model)

> **How to read this file**
> - **Scope.** This file lists every data entity that CreditPulse creates, reads, updates or deletes. It says which service owns each one, which fields are personal data, how long each is kept under Vietnamese law, and which analytics events feed each Skill 10 KPI and guardrail. It also shows how training data is built without leakage or prohibited inputs.
> - **Regime.** Vietnam only: the PDPL (Law 91/2025) with Decree 356/2025, the AI Law (134/2025), SBV circulars, the AML Law and the Accounting Law. GDPR does not apply [Skill18 §1].
> - **Upstream inputs.**
>   - Skill 13: CCP fields F1–F6 including the 2026-09-30 fixes, signals S1–S17, IA §3, services and data flows DF-1 to DF-6 (§4), consent purposes SC/MK/RF.
>   - Skill 17: 117 stories, their named parameters and their event names.
>   - Skill 18: regulations RG-01 to RG-17 and retention rules NFR-DATA-01 to 12.
>   - Skill 14: hosting (§4), component list C-01 to C-30, and the decision-log retention question (OQ-14-10).
>   - Skill 10: KPIs and guardrails.
> - **No invented periods or values.** A retention period appears only where a law is cited, or where an upstream skill proposed it and labelled it as a design value. Everything else is marked `[DECISION REQUIRED]` and appears in §10.
> - **Citations.**
>   - `[P#]`: new sources for this skill (Source Index).
>   - `[N#]`: Skill 18 sources.
>   - `[R#]`: `_research-notes-07-13.md`.
>   - `[T#]`: Skill 14 sources.
>   - `[D#]`: Skill 13 sources.
>   - `US-###`: Skill 17 stories.
>   - `[Rubric R3]`: the data-to-decision architecture criterion ("inputs, logic, outputs, business actions" [Brief p.11]).
> - **Types used in attribute tables.**
>   - `uuid`, `string`, `int`, `decimal`, `bool`, `date`, `json`.
>   - `ts`: timestamp with time zone, stored in UTC and shown in ICT.
>   - `vnd`: integer VND amount.
>   - `enum{…}`: a fixed set of values.
>   - `hash`: a content hash; the algorithm and keys follow NFR-SEC-06.
>   - `→X`: a foreign key to entity X.
>   - `ext:X`: a reference to external system X (not a copy of its data).

---

## 0. Summary (engineer view) `[Rubric R3]`

| Area | Headline |
|---|---|
| Entities | **37 entities** (DM-01 to DM-37) across 15 CreditPulse services and 8 external systems. Core-banking data is **referenced by CIF and never duplicated**. The only copies CreditPulse keeps are derived features and point-in-time snapshots needed to reproduce a decision |
| Systems of record | **HLB core banking (CBS)** owns customer master data (CIF), CASA, loan booking and the repayment ledger. **CreditPulse** owns consent, features, CCPs, decisions, cases, offers, campaigns, referrals and governance data. **CIC**, **C06/VNeID**, **Sổ Bán Hàng** and Phase-2 **Open API** banks are external sources. CreditPulse keeps only the result or derived features from them (plus raw CIC data for ≤ 30 days, A-18-1) |
| Sensitive personal data (PDPL/Decree 356) | Nine categories are flagged sensitive (§4.1):<br>- CASA transaction history<br>- account and card details<br>- financial information held by the bank (declared income, CCP F1/F2, loan data)<br>- CIC credit information<br>- biometric data<br>- citizen-ID card images<br>- behavioural-tracking data from online services (S12, S13 device and time-of-day)<br>- e-ID credentials (never stored)<br>- protected attributes used only for fairness monitoring (gender; ethnicity and religion are **never collected**) [P1][P2] |
| Consent model | Append-only ledger per purpose (**SC**, **MK**, **RF**) plus source-level scopes (`source:SBH`, `source:OPENAPI:<bank>`). **Correction 2026-09-30:** Circular 15/2023 Art. 12(6) requires borrower consent only when *third-party* organisations use CIC services, not for a credit institution's own enquiry [P7]. A CIC enquiry is therefore covered by the SC purpose notice, not a separate `source:CIC` scope. Every CCP read is logged with its purpose (DM-36). Decree 356 requires finance, banking and credit organisations to **keep end-to-end processing logs** and run **annual compliance assessments**. Its **consent notices must disclose scoring, profiling and credit evaluation, with retention periods** [P2]. The article number is not verified. |
| Retention | **SBV Circular 04/2025/TT-NHNN** (effective 1 Jul 2025) **applies to credit institutions** [P5]. Its appendix sets banking-document retention periods, including Group IV (credit) and Group XII (credit information). **The specific credit-dossier periods could not be read from a primary source in this pass.** An earlier draft cited 5/10/15 years and an amendment by "Circular 37/2026"; both are **unverified** → `[DECISION REQUIRED — legal check of the Circular 04/2025 appendix]` (OQ-19-01b). Verified anchors: KYC and transaction information **5 years** (AML Law 2022 [N21]); accounting documents **≥ 10 years** [N20]; CIC keeps credit information **5 years from the date the information arose** (Circular 15/2023 Art. 11(2)) [P7]. This narrows Skill 18 OQ-18-12. Still open: raw biometrics, declined applications, training data and raw analytics events (§6) |
| Analytics | **Events** in `object_action` snake_case (a design convention). 50 are reused verbatim from Skill 17 ACs; 8 are new. **Every Skill 10 metric maps to ≥ 1 event (0 gaps, §5.4).** Analytics events carry only pseudonymous IDs; audit events carry identifiers and live in the restricted audit store |
| Model lineage | Point-in-time training sets built with the feature store's point-in-time historical join [T14], filtered by SC consent **as of the observation time**. Tags that decide what can be a model input: `model_input`, `propensity_only` (S12) and `fairness_monitoring_only` (gender, age band, region, `referred_by_customer`). Labels come from the loan ledger only (§7) |

---

## 1. Entity Relationship Overview `[Rubric R3]`

### 1.1 Relationship map

```
                         ┌──────────────── ext:CBS (core banking: CIF, CASA, loan ledger) ────────────────┐
                         │                                                                                  │
 DM-02 Applicant 0..1 ──converts to──> 1 DM-01 Customer <── 0..1 DM-03 FairnessAttributeRecord (separate store)
     │  (Lane B)                          │ (Lane A; cif_id → ext:CBS)
     │                                    │
     ├────────── 1..N DM-04 ConsentRecord (append-only; purpose SC/MK/RF or source scope) ──────────┤
     ├────────── 0..N DM-05 DataSourceLink ─── gated by ConsentRecord(scope=source:*) ─────────────┤
     │                                    │
     │               DM-06 FeatureDefinition 1 ── N DM-07 FeatureSnapshot N ── 1 subject
     │                                    │
     └──> 0..1 DM-08 CustomerCreditProfile (current) 1 ── N DM-09 CCPVersion ── N:1 DM-10 ModelVersion
                                          │                     │        └─ N:1 DM-12 PolicyParameterSet
                                          │                     │
 DM-31 MerchantPartner 1 ── N DM-13 CheckoutSession 1 ── 0..1 DM-14 Application ── 0..N DM-15 eKYCResult
                                                            │  ├─ 0..N DM-16 CICInquiry
                                                            │  ├─ 0..N DM-20 IntegrityFlag / FraudCase
                                                            │  ├─ 1..4 DM-21 Package (+ CostDisclosure)
                                                            │  └─ 1..N DM-17 Decision (append-only log)
                                                            │            ├─ N:M DM-18 ReasonCode
                                                            │            ├─ 0..1 DM-19 ReferCase / OfficerReview
                                                            │            └─ 0..1 DM-22 LoanContract ── ext:CBS booking ref
                                                            │                        ├─ 1..N DM-23 RepaymentEvent
                                                            │                        ├─ 0..N DM-24 EarlyWarningFlag
                                                            │                        └─ 0..N DM-25 GraduationOffer
 DM-26 Campaign 1 ── N DM-27 CampaignAssignment (TREATMENT | HOLDOUT) N ── 1 Customer
 DM-28 MarketingSuppression N ── 1 Customer             DM-29 Recommendation N ── 1 Customer (N ── 0..1 Campaign)
 DM-30 Referral: inviter Customer 1 ── N Referral N ── 0..1 invitee Applicant/Customer (privacy wall)
 DM-32 MerchantLead N ── 1 suggesting Customer; 0..1 → MerchantPartner (after onboarding)
 DM-33 Passport 0..1 ── 1 Customer
 DM-10 ModelVersion 1 ── 1 DM-11 TrainingDataset (per training run); 1 ── N DM-35 DriftMetric
 DM-34 FairnessMonitoringSnapshot (aggregates only; reads DM-03 × DM-17 × DM-23)
 DM-36 CCPReadLog N ── 1 DM-04 ConsentRecord matched     DM-37 AnalyticsEvent (pseudonymous; no FK joins in analytics)
```

### 1.2 Service keys used in this file (from Skill 13 §4 and Skill 14 §1)

| Key | Service | Skill 13 §4 / Skill 14 component |
|---|---|---|
| CON | Consent Service and ledger | svc 1 / C-01 |
| ING | Data Ingestion (batch + stream) | svc 2 / C-02, C-03 |
| FST | Feature Store (online + offline; Feast-class [T14]) | svc 3 / C-04 |
| MDL | Model Layer and scoring API | svc 4 / C-05 to C-11 |
| POL | Policy & Decision Service, including reason codes | svc 5 / C-12, C-13 |
| CCP | CCP Store & API | svc 6 / C-14 |
| CMP | Campaign service and workspace | svc 7 / C-17 |
| OFR | Offer / Package service | svc 7 / C-18 |
| CHK | Checkout Decision orchestrator, including eKYC orchestration and e-contract | svc 7 / C-19, C-20 |
| SRV | Servicing & Graduation, including early warning and CIC reporting | svc 7 / C-21, C-09 |
| REC | Recommendation service and anti-stacking gate | svc 7 / C-22, C-23 |
| REF | Referral ledger, including merchant leads and passport | svc 7 / C-24 |
| CASE | Case Management / officer workbench | svc 8 / C-25 |
| GOV | Governance & Monitoring, model registry, policy console (OP-02) and audit store | svc 9 / C-15, C-16 |
| MXG | Merchant API gateway (MX-01) | C-27 |
| ANL | Analytics event pipeline and KPI store | **Not a named Skill 13 service.** It is implied by the Skill 10 §4 measurement plan and the Skill 17 DoD ("analytics events firing (Skill 19 event schema)"). Added here as a platform component (A-19-3) |
| HUB | HLB Connect "Pay Later" hub (client only; owns no data) | C-26 |
| ext:CBS, ext:CARDS, ext:CIC, ext:C06, ext:SBH, ext:OPENAPI, ext:SMS, ext:MERCHANT | External systems | Skill 14 §3 |

### 1.3 Entity details

#### DM-01 Customer
**Description:** an HLB customer who already has a core-banking CIF (Lane A, or a Lane B applicant after CASA-first opening). CreditPulse keeps **only a reference and product-specific state**. Name, address, phone and account data stay in core banking and are read on demand under the right purpose. This avoids a second customer master and a second breach surface (the CIC breach lesson [R71]; NFR-SEC-09).

| Attribute | Type | Req | Notes |
|---|---|---|---|
| customer_key | uuid | Y | Internal surrogate key. It is never exposed to merchants |
| cif_id | ext:CBS | Y | Core-banking CIF. Unique |
| subject_pid | string | Y | Pseudonymous analytics ID: a keyed hash of customer_key, with the key held in the HSM (A-19-2) |
| cohort | enum{A,B} | Y | A = profiled in advance; B = came in through the new-to-bank lane |
| segment | enum{SAL-C,SAL-N,GIG,MSME,FTB,UNKNOWN} | Y | Derived [Brief p.9]; used for reporting and scorecard selection |
| origin_applicant_id | →DM-02 | N | Set when a Lane B applicant becomes a customer (AP-06) |
| pop_first_loan_at | ts | N | Null = "never used PoP" (the AW-01 criterion) |
| created_at / updated_at | ts | Y | |

**Relationships:** has one DM-08 (0..1); has many DM-04, DM-05, DM-09, DM-14, DM-22, DM-27, DM-29, DM-30 (as inviter); has one DM-03 (separate store), DM-33 (0..1) and DM-28 (0..N). Links 0..1 to DM-02.

#### DM-02 Applicant (Lane B, before a CIF exists)
**Description:** a new-to-bank shopper at a partner checkout (US-111). They are created from the chip-CCCD read and the minimal form. A CIF is created only if the customer accepts CASA-first opening (AP-06, US-129) or a loan is booked. **Identity resolution:** if the CCCD number matches an existing CIF, the applicant is linked to that Customer rather than duplicated (ASSUMPTION A-19-1; see OQ-19-11).

| Attribute | Type | Req | Notes |
|---|---|---|---|
| applicant_id | uuid | Y | |
| cccd_number | string (field-encrypted) | Y | Citizen-ID number from the chip (NFR-SEC-04) |
| full_name, date_of_birth, gender, permanent_address | string / date / enum / string | Y | From the chip-CCCD read (C06). **gender is copied to DM-03 and removed from the model-input path** |
| phone | string (field-encrypted) | Y | For OTP and notifications (US-111) |
| declared_monthly_income_vnd | vnd | Y (Lane B) | The single self-stated income figure used for F2_thin (A-13-12). Personal financial data |
| device_ref | string | N | Device fingerprint reference used for F5 velocity checks (NFR-SEC-13); not the raw fingerprint |
| referral_id | →DM-30 | N | Stored **outside** the feature store (US-221 AC 4, US-261 AC 2) |
| status | enum{STARTED,SUBMITTED,DECIDED,CONVERTED,ABANDONED} | Y | |
| converted_customer_key | →DM-01 | N | |
| created_at | ts | Y | |

**Relationships:** has many DM-04, DM-14, DM-15; 0..1 converts to DM-01.

#### DM-03 FairnessAttributeRecord (separate store)
**Description:** the protected attributes that are allowed **only for fairness monitoring** (G-5, KR 4.3, NFR-AI-03). They are held in a **physically separate store** and never joined into feature vectors (NFR-SEC-9; Skill 13 §6). Ethnicity and religion are **not collected at all**.

| Attribute | Type | Req | Notes |
|---|---|---|---|
| subject_key | uuid | Y | customer_key or applicant_id |
| gender | enum | Y | From CIF or CCCD |
| age_band | enum | Y | Derived from date of birth at decision time; the bands follow `governance.fairness_reference_group` |
| region | enum | Y | Province or region from the permanent address |
| source, captured_at | string, ts | Y | |

**Relationships:** 1–1 with DM-01 or DM-02. It is read **only** by the GOV fairness job (DM-34).

#### DM-04 ConsentRecord (append-only ledger)
**Description:** one immutable entry per grant, withdrawal or re-grant, for each purpose or source scope (E-01, US-021 to US-023). It is queryable as of any timestamp (US-023 AC 2). The PDPL requires consent to be verifiable as to **when it was given, what it covers and who gave it** [P2][N3].

| Attribute | Type | Req | Notes |
|---|---|---|---|
| consent_entry_id | uuid | Y | |
| subject_key | uuid | Y | Customer or applicant |
| scope | enum{SC,MK,RF,source:SBH,source:OPENAPI:<bank>} | Y | No separate `source:CIC` scope: Art. 12(6) consent applies to third-party organisations, not to HLB's own enquiry [P7] (corrected 2026-09-30) |
| action | enum{GRANT,WITHDRAW} | Y | |
| notice_version / text_hash | string / hash | Y | The exact wording shown |
| channel | enum{HUB,WIDGET,HOSTED,QR,BRANCH,PARTNER_APP} | Y | |
| captured_at | ts | Y | Server time |
| auth_evidence_ref | string | Y | Session or OTP reference that proves who acted |
| supersedes_entry_id | →DM-04 | N | |
| prev_hash | hash | Y | Hash chain for tamper evidence (NFR-SEC-08) |

**Relationships:** N–1 to subject; 1–N from DM-36 (reads matched against it).

#### DM-05 DataSourceLink
**Description:** which data sources are connected for a subject, and whether each is currently usable. It drives F3's `sources_used` list and ingestion gating (US-001 AC 3, US-023 AC 3).

| Attribute | Type | Req | Notes |
|---|---|---|---|
| link_id | uuid | Y | |
| subject_key | uuid | Y | |
| source | enum{CASA,CARDS,HLB_LOANS,HLB_CONNECT,SBH,CIC,CHECKOUT,POP_LEDGER,OPENAPI:<bank>} | Y | Phase-3 sources (S17) are not in the enum; they stay parked (PL-01) |
| consent_entry_id | →DM-04 | Cond. | Required for SBH, OPENAPI and CIC |
| status | enum{ACTIVE,STALE,REVOKED,UNAVAILABLE} | Y | STALE follows `ingest.max_staleness[source]`, for example SBH > 48 h (Skill 18 §5.2) |
| last_ingested_at | ts | N | |
| access_window_end | date | N | Open API default 180-day window [R73] |

#### DM-06 FeatureDefinition (feature registry)
**Description:** the registered definition of each engineered signal S1–S16 and its sub-features, with **usage tags that the feature store enforces** (US-005).

| Attribute | Type | Req | Notes |
|---|---|---|---|
| feature_name, version | string, int | Y | For example `s1_inflow_cv` v2 |
| signal_id | enum{S1..S16} | Y | |
| source | enum (as in DM-05) | Y | |
| consent_scope | enum | Y | For example SC, or SC + source:SBH |
| usage_tag | enum{model_input,propensity_only,fairness_monitoring_only,excluded} | Y | S12 = `propensity_only`; `referred_by_customer` = `fairness_monitoring_only` (it is analysis-only and never an input); contacts, gallery and social data do not exist as features (X-01) |
| transformation_ref, owner | string | Y | Code version (NFR-AI-10 lineage) |
| ttl | interval | Y | Point-in-time join window [T14] |

#### DM-07 FeatureSnapshot (point-in-time)
**Description:** a timestamped value of the features for a subject. Online rows serve checkout scoring; offline rows build training sets. Each decision stores a pointer to the exact snapshot it used (DM-17 `feature_snapshot_ref`).

| Attribute | Type | Req | Notes |
|---|---|---|---|
| subject_key | uuid | Y | |
| feature_name + version | →DM-06 | Y | |
| value | json | Y | |
| event_ts | ts | Y | When the underlying fact happened; the point-in-time join key [T14] |
| created_ts | ts | Y | When it was computed |
| source_batch_ref | string | Y | |

#### DM-08 CustomerCreditProfile (current)
**Description:** the latest CCP for a subject, held in the low-latency store and served by the purpose-checked read API (US-006, US-008).

| Attribute | Type | Req | Notes |
|---|---|---|---|
| subject_key | uuid | Y | Primary key |
| current_version_id | →DM-09 | Y | |
| f1_grade / f1_pd_band | enum{A..E} / string | Y | |
| f2_headroom_vnd | vnd | Y | ≥ 0 |
| f2_method | enum{BANK_OBSERVED,F2_THIN_DECLARED,OPENAPI} | Y | Records whether F2_thin was used (A-13-12) |
| income_estimate_vnd, income_ci_low, income_ci_high | vnd | N | US-012 |
| f3_evidence | enum{BUREAU+BANK,BANK-OBSERVED,CONSENTED-PARTNER,THIN} | Y | |
| f3_sources | array<enum> | Y | |
| f4_suitability / f4_uplift_score / f4_top_categories | bool / decimal / array | N | The uplift score (AW-03) is Should |
| f5_integrity_score / f5_flags | int 0–100 / array | Y | |
| f6_tier | enum{PRE_APPROVED,INSTANT_APPROVE,REFER,DECLINE} | Y | |
| f6_limit_vnd | vnd | N | |
| suppression_flags | array{EW,STACKING,HARDSHIP,MK_ABSENT,PAUSED} | Y | Propagated to CMP and REC (DF-3; NFR-PERF-12) |
| refreshed_at | ts | Y | |

#### DM-09 CCPVersion (history)
**Description:** an immutable version of the CCP, written on every refresh (E-08: application, repayment, monthly batch, consent change, new source, nightly Lane A run). It is the basis for KR 2.3 (F3 upgrade) and for decision replay.

| Attribute | Type | Req | Notes |
|---|---|---|---|
| ccp_version_id | uuid | Y | |
| subject_key | uuid | Y | |
| version_no | int | Y | Monotonic per subject |
| fields | json | Y | F1–F6 as in DM-08 |
| trigger | enum{APPLICATION,REPAYMENT,MONTHLY,NIGHTLY,CONSENT_CHANGE,SOURCE_ADDED,CIC_OBLIGATION} | Y | |
| model_version_ids | array<→DM-10> | Y | Champion and, if used, challenger, income, integrity, EW |
| policy_set_id | →DM-12 | Y | |
| feature_snapshot_ref | string | Y | |
| sources_used | array<enum> | Y | |
| created_at | ts | Y | |

#### DM-10 ModelVersion / ModelCard
**Description:** the registry entry for every served model: risk champion scorecard, GBM challenger, income estimator, behavioural re-score, EW rules/model, uplift and integrity (US-030). The model card fields follow NFR-AI-09; the template itself is `governance.model_card_template` (OQ-17A-11).

| Attribute | Type | Req | Notes |
|---|---|---|---|
| model_version_id | uuid | Y | |
| model_family | enum{RISK_CHAMPION,RISK_CHALLENGER,INCOME,BEHAVIOURAL,EARLY_WARNING,UPLIFT,INTEGRITY} | Y | |
| segment_scope | array<enum> | Y | |
| status | enum{CANDIDATE,VALIDATED,CHAMPION,CHALLENGER,RETIRED,FALLBACK_ACTIVE} | Y | |
| training_dataset_id | →DM-11 | Y (ML) | Rule sets reference their rule version instead |
| feature_list | array<→DM-06> | Y | Only `model_input` features, plus `propensity_only` features for the UPLIFT family. **Enforced at registration** |
| validation_report_ref, fairness_test_ref | string | Y | NFR-AI-03 pre-deployment tests |
| approved_by / approved_at | string / ts | Y | Model Risk sign-off (NFR-AI-08) |
| card | json | Y | Purpose, intended use, limits, data sources and consent basis, method, known limitations, human-oversight procedure |

#### DM-11 TrainingDataset
**Description:** a versioned, reproducible training or validation set (NFR-AI-10; NFR-SEC-15c). See §7.

| Attribute | Type | Req | Notes |
|---|---|---|---|
| dataset_id | uuid | Y | |
| label_definition | string | Y | For example MOB3 30+ from the loan ledger |
| observation_window / performance_window | daterange | Y | |
| entity_df_hash / content_hash | hash | Y | |
| feature_versions | array | Y | |
| consent_filter_version | string | Y | SC as of the observation time |
| includes_corridor_rows / corridor_weighting | bool / string | Y | Randomised approval corridor (OQ-14-07) |
| excluded_fields_check | json | Y | Automated proof that no `fairness_monitoring_only` or `excluded` feature is present |

#### DM-12 PolicyParameterSet (versioned)
**Description:** one immutable, validated version of every HLB policy value (US-017, US-036). **A deployment with a required parameter unset fails its configuration validation** (Skill 17 rule). Named parameters (values `[DECISION REQUIRED]` unless noted; Skill 17 registers):

| Group | Parameters |
|---|---|
| Tier and limit | `policy.integrity_floor`, `policy.dsr_cap`, `policy.living_cost_buffer`, `policy.starter_limit` (= `policy.starter_limit_vnd`), `policy.limit_by_grade[A..E]` (= `policy.grade_limit_vnd`), `policy.tenor_adjusted_factor[tenor]`, `policy.min_package_instalment`, `policy.tier_rule.consented_partner_ab`, `policy.thin_file_no_income_rule`, `policy.declared_income_haircut`, `policy.thin_headroom_cap_vnd`, `policy.living_cost_buffer_vnd`, `policy.corridor_share`, `policy.stacking_fail_outcome` (default REFER if F1 ∈ {A,B}, else DECLINE `EXISTING_OBLIGATIONS`, to confirm) |
| CIC | `policy.cic_cache_max_age_days` = 30 (given), `policy.cic_fallback_mode`, `policy.cic_fallback_starter_cap_vnd` (= `policy.cic_unavailable_starter_cap`), `policy.cic_timeout_s`, `cic.outage_alert_rule` |
| Evidence | `ccp.bank_observed_min_months`, `ccp.f3_precedence`, `signals.s1_cv_window_months`, `signals.s11_kyc_change_window_days` |
| Packages and pricing | `package.tenors_months` = {3,6,9,12,18,24}, `package.min_count` / `max_count` = 2/4, `package.hold_minutes` = 30 (A-13-6), `package.down_payment_options`, `policy.min_financed_vnd` / `max_financed_vnd`, `pricing.rate_grid`, `pricing.fee_schedule`, `pricing.late_payment_terms`, `pricing.early_settlement_terms`, `schedule.first_due_rule`, `merchant.zero_pct_eligibility` |
| Checkout ops | `ekyc.max_attempts` = 3 (A-13-8), `refer.sla_business_hours` = 2, `ops.business_calendar`, `refer.approval_validity`, `refer.notify_max_seconds`, `decision.in_session_budget_seconds`, `integrity.velocity_rules`, `checkout.session_expiry` |
| Lifecycle | `policy.graduation_on_time_instalments_N`, `policy.graduation_step_ladder`, `policy.graduation_offer_validity_days`, `auth.graduation_acceptance_level`, `ew.trigger_rules`, `ew.flag_clear_rule`, `ew.max_supportive_messages_per_cycle`, `metrics.ew_cure_window_days` = 60, `policy.recent_hardship_lookback_days`, `policy.stacking_concurrent_consumer_loans_threshold` = 2, `policy.stacking_rising_obligations_rule`, `policy.internal_exposure_cap_vnd`, `policy.stacking_cic_snapshot_max_age_days` |
| Marketing and retention | `campaign.holdout_share`, `campaign.attribution_window_days`, `campaign.mk_cooling_period_days`, `retention.max_recommendations_per_week` = 3, `retention.dismissal_suppression_threshold` = 3 (A-13-9), `retention.dismissal_suppression_period_days`, `retention.recent_completion_window_days`, `retention.reengagement_inactivity_days` = 90 |
| Referral | `policy.referral_reward_on_time_instalments_K`, `referral.reward_type`, `referral.invite_link_validity_days`, `referral.max_active_invites_per_inviter`, `merchant_lead.retention_days`, `policy.passport_total_debt_affordability_rule` |
| Governance | `governance.challenger_split_pct`, `governance.challenger_mob3_tolerance`, `governance.fairness_reference_group`, `governance.fairness_mob3_ratio_alert`, `governance.champion_drift_action`, `model.psi_action_threshold` = 0.25, `guardrail.mob3_30_ceiling[segment]`, `guardrail.consent_withdrawal_spike`, `g2.baseline_rate`, `reason_codes.display_count`, `privacy.sc_withdrawal_policy`, **`audit.retention_period`** (anchored in §6), `consent.self_view_purpose`, `pause.*` switches, `feature.open_api_enabled` |

| Attribute | Type | Req | Notes |
|---|---|---|---|
| policy_set_id / version | uuid / int | Y | |
| parameters | json (schema-validated) | Y | |
| rate_grid_version | string | Y | DD-10 |
| author, approver, effective_from | string, string, ts | Y | Four-eyes approval (Skill 17 DoD) |
| supersedes | →DM-12 | N | Rollback ≤ 5 min (NFR-REL-07) |

#### DM-13 CheckoutSession
**Description:** a merchant-initiated session through MX-01 (widget, hosted page or QR), bound to the merchant order ID (US-140).

| Attribute | Type | Req | Notes |
|---|---|---|---|
| session_id | uuid | Y | |
| merchant_id | →DM-31 | Y | |
| merchant_order_ref | string | Y | |
| channel | enum{WIDGET,HOSTED,QR,HUB} | Y | |
| basket_value_vnd, basket_category | vnd, string | Y | Untrusted input (NFR-SEC-16) |
| state | enum{CREATED,PACKAGES_SHOWN,APPLYING,HANDOFF_APP,DECIDED,REFERRED,CONFIRMED,EXPIRED,CANCELLED} | Y | |
| handoff_ref | string | N | Web-to-app hand-off for NFC eKYC (U-01) |
| expires_at | ts | Y | `checkout.session_expiry` |

#### DM-14 Application
**Description:** one credit application, from a checkout (Lane A or B) or started from an offer or recommendation.

| Attribute | Type | Req | Notes |
|---|---|---|---|
| application_id | uuid | Y | |
| subject_key | uuid | Y | Customer or applicant |
| lane | enum{A,B} | Y | |
| session_id | →DM-13 | N | |
| origin | enum{CHECKOUT,AWARENESS_OFFER,RECOMMENDATION,REFERRAL,GRADUATION,PASSPORT} | Y | For G-1 and KR 3.x origin tagging (US-205 AC 5) |
| selected_package_id | →DM-21 | N | |
| financed_amount_vnd | vnd | N | Basket value minus down-payment |
| submitted_at, decided_at | ts | N | KR 1.2 |
| final_in_session | bool | N | KR 1.1 |
| status | enum{STARTED,SUBMITTED,DECIDED,REFERRED,BOOKED,WITHDRAWN,EXPIRED} | Y | |

#### DM-15 eKYCResult
**Description:** the **result and references** of an eKYC attempt (AP-02, US-118 to US-120): NFC chip read, liveness and face match against the chip or C06/VNeID, or a Lane A face match against the CCCD on file. **Raw face images and liveness frames are not kept in CreditPulse.** Any retention of raw images belongs to HLB's account-opening dossier (see §6 R-05 and OQ-19-01).

| Attribute | Type | Req | Notes |
|---|---|---|---|
| ekyc_id | uuid | Y | |
| application_id / subject_key | →DM-14 / uuid | Y | |
| attempt_no | int | Y | ≤ `ekyc.max_attempts` |
| method | enum{NFC_CHIP_LIVENESS,VNEID,FACE_MATCH_ON_FILE} | Y | |
| chip_authenticity_result, liveness_result, face_match_result | enum{PASS,FAIL,ERROR} | Y | |
| match_score | decimal | N | |
| provider_txn_ref | ext:C06 | Y | Reference to C06/VNeID or the vendor transaction |
| biometric_template_ref | string | N | A pointer to HLB's biometric record, if HLB's eKYC platform stores one. CreditPulse never stores the template |
| failure_type, device_class | enum, string | N | `ekyc_attempt` (US-120) |
| completed_at | ts | Y | |

#### DM-16 CICInquiry
**Description:** one CIC enquiry. The raw payload is kept encrypted for ≤ 30 days (A-18-1 / NFR-DATA-07). After that only the derived features remain (S14 counts, delinquency flags, total obligations; NFR-SEC-09).

| Attribute | Type | Req | Notes |
|---|---|---|---|
| cic_inquiry_id | uuid | Y | |
| subject_key | uuid | Y | |
| purpose | enum{APPLICATION,STACKING_CHECK,GRADUATION,PORTFOLIO_REFRESH,RECHECK_AFTER_FALLBACK} | Y | |
| consent_entry_id | →DM-04 | Y | `source:CIC` or SC per OQ-19-06 (Circular 15/2023 Art. 12(6)) |
| status | enum{OK,TIMEOUT,ERROR,CACHED} | Y | `cic_status`; a fallback sets `cic_fallback=true` on DM-17 |
| raw_payload | bytes (field-encrypted) | N | Purged at the 30-day mark (A-18-1) |
| derived_features_ref | →DM-07 | Y | |
| requested_at, responded_at, purge_due_at | ts | Y | |

#### DM-17 Decision (append-only decision log)
**Description:** one record for every automated tier, officer action, counter-offer, graduation offer and pre-approval (US-033, NFR-AI-06). It must be reproducible by replay. **No decision is issued if this write fails** (NFR-REL-08).

| Attribute | Type | Req | Notes |
|---|---|---|---|
| decision_id | uuid | Y | |
| application_id / subject_key | →DM-14 / uuid | Y | |
| decision_type | enum{PRE_APPROVAL,CHECKOUT,COUNTER_OFFER,OFFICER,GRADUATION,REOFFER} | Y | |
| cohort, segment, lane | enum | Y | |
| input_snapshot_hash / feature_snapshot_ref | hash / string | Y | |
| ccp_version_id | →DM-09 | Y | |
| model_version_ids, experiment_arm | array<→DM-10>, enum{CHAMPION,CHALLENGER} | Y | KR 2.1 |
| policy_set_id, rate_grid_version, fired_rule | →DM-12, string, string | Y | |
| tier | enum{PRE_APPROVED,INSTANT_APPROVE,REFER,DECLINE} | Y | |
| limit_vnd, starter_limit_flag | vnd, bool | N | |
| corridor_flag | bool | Y | Randomised approval corridor (U-05) |
| cic_fallback | bool | Y | AP-07 |
| stacking_result | enum{PASS,FAIL,NOT_RUN} | Y | |
| f2_method | enum | Y | Shows when F2_thin was used |
| consent_entry_ids | array<→DM-04> | Y | |
| officer_id / justification_code / justification_text | string | Cond. | For officer actions (US-028) |
| latencies | json | Y | NFR-PERF |
| prev_hash | hash | Y | Hash chain |
| decided_at | ts | Y | |

**Relationships:** N–M with DM-18 (through DecisionReasonCode: decision_id, code, rank 1–4); 0..1 DM-19; 0..1 DM-22.

#### DM-18 ReasonCode (catalogue)
**Description:** a controlled catalogue of category-level codes (E-09; NFR-AI-01; NFR-SEC-15b), with Vietnamese customer copy. Codes named upstream: `AFFORDABILITY`, `EXISTING_OBLIGATIONS`, `LIMITED_HISTORY`, `IDENTITY_REVIEW`. **The full catalogue and its copy are HLB content** (OQ-19-12).

| Attribute | Type | Req | Notes |
|---|---|---|---|
| code, version | string, int | Y | |
| category | enum{AFFORDABILITY,CREDIT_HISTORY,OBLIGATIONS,IDENTITY,POLICY,DATA_UNAVAILABLE} | Y | |
| customer_text_vi / customer_text_en | string | Y | Plain language (NFR-A11Y-09) |
| path_text_vi | string | N | Generic path to yes (AP-09 detail is parked) |
| active | bool | Y | |

#### DM-19 ReferCase / OfficerReview
**Description:** a referred case in the OP-01 queue, including identity-review and CIC-fallback refers (US-026 to US-029).

| Attribute | Type | Req | Notes |
|---|---|---|---|
| case_id | uuid | Y | |
| decision_id | →DM-17 | Y | The originating REFER |
| case_reason | enum{CREDIT,IDENTITY,CIC_FALLBACK,EKYC,HUMAN_REVIEW_REQUEST} | Y | |
| evidence_snapshot_ref | string | Y | |
| counter_offer_tried | bool | Y | |
| merchant_hold | bool | Y | `merchant.supports_refer_hold` |
| assigned_officer_id | string | N | |
| created_at, assigned_at, decided_at, customer_notified_at | ts | N | KR 1.4 |
| outcome | enum{APPROVE,COUNTER_OFFER,DECLINE,FRAUD_ESCALATED} | N | |
| override_flag, justification_code, justification_text | bool, enum, string | Cond. | 100% justification (NFR-AI-07). The free text must not contain health or other sensitive details beyond the case need (§4 note) |

#### DM-20 IntegrityFlag / FraudCase
**Description:** F5 flags raised per application or draw, and the fraud-team case for confirmed mismatches (US-121 to US-123, US-029; G-2).

| Attribute | Type | Req | Notes |
|---|---|---|---|
| flag_id | uuid | Y | |
| application_id | →DM-14 | Y | |
| flag_type | enum{ID_MISMATCH,DEVICE_VELOCITY,IDENTITY_VELOCITY,CIC_ID_MISMATCH,KYC_CHANGE_RECENT,REFERRAL_ABUSE} | Y | |
| severity / status | enum | Y | status ∈ {OPEN,CLEARED,CONFIRMED_FRAUD,NOT_FRAUD} |
| confirmed_at, confirmed_by | ts, string | N | `fraud_confirmed` label |

#### DM-21 Package (quote) and CostDisclosure
**Description:** each of the 2–4 packages built by CO-01, and the hash of the exact terms shown (CO-02; US-106, US-107). **Signing is blocked if the terms don't match the latest disclosure hash.**

| Attribute | Type | Req | Notes |
|---|---|---|---|
| package_id | uuid | Y | |
| application_id / session_id | →DM-14 / →DM-13 | Y | |
| tenor_months | enum{3,6,9,12,18,24} | Y | |
| instalment_vnd, down_payment_vnd, total_payable_vnd | vnd | Y | Instalment ≤ F2 (G-4) |
| apr_all_in, fees, rate_tier, zero_pct_flag | decimal, json, enum, bool | Y | |
| indicative | bool | Y | Lane B before a CCP exists |
| disclosure_hash, disclosed_at | hash, ts | Y | `cost_disclosed` |
| status | enum{SHOWN,SELECTED,ACCEPTED,EXPIRED,SUPERSEDED} | Y | 30-minute hold |

#### DM-22 LoanContract
**Description:** the signed PoP e-contract and its booking reference. **Core banking is the system of record** for the loan account, balance and schedule. CreditPulse keeps the evidence needed for the credit dossier (NFR-SEC-12) and the booking reference.

| Attribute | Type | Req | Notes |
|---|---|---|---|
| contract_id | uuid | Y | |
| decision_id / package_id | →DM-17 / →DM-21 | Y | |
| cbs_loan_ref | ext:CBS | Y | Booking reference |
| principal_vnd, tenor_months, apr_all_in, schedule_ref | vnd, int, decimal, ext:CBS | Y | The schedule lives in CBS; the reference is kept here |
| contract_version_hash, signature_evidence (OTP event and biometric match ID), trusted_timestamp | hash, json, ts | Y | Law on E-Transactions [N23] |
| disbursement_account_type | enum{HLB_CASA_NEW,HLB_CASA_EXISTING,MERCHANT_DIRECT} | Y | KR 2.2 |
| origin | enum (as in DM-14) | Y | |
| status | enum{BOOKED,ACTIVE,SETTLED_EARLY,CLOSED,DEFAULTED,WRITTEN_OFF,CANCELLED} | Y | |
| booked_at, closed_at | ts | N | closed_at starts the retention clock (§6) |

#### DM-23 RepaymentEvent
**Description:** an instalment payment, partial payment or missed-due event, received from the CBS ledger through the stream, or a daily batch fallback (C-28). It becomes S15 and triggers PR-01 re-scoring.

| Attribute | Type | Req | Notes |
|---|---|---|---|
| repayment_event_id | uuid | Y | Idempotency key (US-220 AC on duplicates) |
| contract_id | →DM-22 | Y | |
| cbs_txn_ref | ext:CBS | Y | |
| due_date, paid_at | date, ts | N | |
| amount_due_vnd, amount_paid_vnd | vnd | Y | |
| days_past_due | int | Y | |
| payment_channel | enum | N | S15 "payment-channel changes" |
| event_ts, received_ts | ts | Y | Out-of-order handling (US-220) |

#### DM-24 EarlyWarningFlag (with outreach)
**Description:** a rule-based v1 early-warning flag (PR-02, U-04) and the supportive contacts it triggers (US-222 to US-225).

| Attribute | Type | Req | Notes |
|---|---|---|---|
| ew_flag_id | uuid | Y | |
| contract_id / subject_key | →DM-22 / uuid | Y | |
| rule_fired | string | Y | From `ew.trigger_rules` |
| raised_at, cleared_at | ts | Y / N | `ew.flag_clear_rule` |
| outreach | array{template_version, channel, sent_at, delivery_status} | N | ≤ `ew.max_supportive_messages_per_cycle` |
| cure_evaluated_at, cured | ts, bool | N | Current `metrics.ew_cure_window_days` after the outreach (KR 3.3) |

#### DM-25 GraduationOffer
**Description:** an **offered, never auto-applied** graduation step (PR-03, DD-05, C2). Nothing changes until the customer explicitly accepts, and the acceptance is recorded with authentication evidence.

| Attribute | Type | Req | Notes |
|---|---|---|---|
| graduation_offer_id | uuid | Y | |
| subject_key / contract_id | uuid / →DM-22 | Y | |
| decision_id | →DM-17 | Y | |
| step_type | enum{LIMIT_INCREASE,TENOR_UNLOCK,RATE_TIER} | Y | `policy.graduation_step_ladder` |
| new_limit_vnd / new_rate_tier | vnd / enum | N | |
| status | enum{OFFERED,ACCEPTED,DECLINED,EXPIRED,WITHDRAWN_BY_HLB} | Y | |
| response_at, acceptance_auth_ref | ts, string | Cond. | `auth.graduation_acceptance_level` |
| cic_concurrent_loans_at_acceptance | int | Cond. | For G-3 (`graduation_accepted`) |
| valid_until | ts | Y | `policy.graduation_offer_validity_days` |

#### DM-26 Campaign
**Description:** an Awareness or Retention campaign run (AW-01/02, RT-01), with its holdout configuration.

| Attribute | Type | Req | Notes |
|---|---|---|---|
| campaign_id | uuid | Y | |
| type | enum{AWARENESS,RETENTION,REENGAGEMENT,PARTNER} | Y | PARTNER = AW-05 (Should) |
| list_version, run_id | string | Y | `awareness_candidates_built` |
| holdout_share | decimal | Y | `campaign.holdout_share` |
| attribution_window_days | int | Y | `campaign.attribution_window_days` |
| targeting_method | enum{SUITABILITY_RULES,UPLIFT_RANKED} | Y | AW-03 is Should |
| created_by, created_at | string, ts | Y | Audit (US-201) |

#### DM-27 CampaignAssignment
**Description:** one customer's arm in one campaign. It **persists** so that uplift (KR 3.1) and repeat-rate (KR 3.4) contrasts are valid. Contact outcomes are recorded as events.

| Attribute | Type | Req | Notes |
|---|---|---|---|
| assignment_id | uuid | Y | |
| campaign_id / subject_key | →DM-26 / uuid | Y | |
| arm | enum{TREATMENT,HOLDOUT} | Y | Random |
| exclusion_reason | enum{EW,STACKING,HARDSHIP,MK_ABSENT,COOLING,NONE} | Y | Candidate-level exclusions are counted in `awareness_candidates_built` |
| ccp_version_id | →DM-09 | Y | |
| delivery_status | enum{PENDING,DELIVERED,SUPPRESSED,FAILED} | Y | Send-time consent re-check (US-204) |
| assigned_at | ts | Y | |

#### DM-28 MarketingSuppression (opt-out, complaint and cooling period)
**Description:** the **minimal record kept after MK withdrawal or a complaint** so the opt-out keeps being honoured (NFR-DATA-05), plus complaint logging for G-6 (US-209).

| Attribute | Type | Req | Notes |
|---|---|---|---|
| suppression_id | uuid | Y | |
| subject_key | uuid | Y | |
| reason | enum{MK_WITHDRAWN,COMPLAINT,OPT_OUT} | Y | |
| triggering_contact_ref | string | N | Campaign or recommendation that prompted a complaint |
| cooling_until | ts | Cond. | `campaign.mk_cooling_period_days` |
| created_at | ts | Y | |

#### DM-29 Recommendation
**Description:** up to three weekly product recommendations (RT-01), with retention holdout assignment (Flow 5 step 3) and dismissal suppression.

| Attribute | Type | Req | Notes |
|---|---|---|---|
| recommendation_id | uuid | Y | |
| subject_key, campaign_id | uuid, →DM-26 | Y | |
| merchant_id / category / partner_product_ref | →DM-31 / string / string | Y | |
| instalment_from_vnd, total_vnd | vnd | Y | Must be ≤ F2 |
| stacking_gate_result | enum{PASS,SUPPRESS} | Y | RT-02 |
| status | enum{GENERATED,VIEWED,CLICKED,DISMISSED,CONVERTED,EXPIRED} | Y | |
| dismissal_count_category | int | Y | Suppression after `retention.dismissal_suppression_threshold` |
| week_of | date | Y | |

#### DM-30 Referral (and reward ledger)
**Description:** a repayment-conditioned invite (AD-01). **There is a privacy wall:** the inviter sees only the invite status and reward state, never the invitee's credit data (Skill 13 Flow 6; US-263). `referred` is analysis-only and never a feature (DD-08).

| Attribute | Type | Req | Notes |
|---|---|---|---|
| referral_id | uuid | Y | |
| inviter_key | →DM-01 | Y | Needs RF consent and the eligibility checks |
| invite_link_token | string | Y | Expires per `referral.invite_link_validity_days` |
| invitee_subject_key | uuid | N | Set when the invitee applies (their own SC consent). **Not readable by the inviter's services** |
| status | enum{CREATED,OPENED,APPLIED,BOOKED,NOT_USED,EXPIRED} | Y | The inviter sees only CREATED/OPENED/"not used"/reward state |
| on_time_instalments_count | int | Y | Compared with K |
| reward_status | enum{PENDING,RELEASED,WITHHELD} | Y | |
| reward_withheld_reason | enum{ABUSE,DELINQUENT,INVITER_INELIGIBLE,RF_WITHDRAWN} | N | |
| reward_type / reward_value_ref | enum / string | N | `referral.reward_type`; a payout is an accounting entry in CBS |

#### DM-31 MerchantPartner
**Description:** a partner merchant or platform offering "Pay over time with HLB" (MX-01). This is business data. The owner's and contact persons' names and phones are personal data (§4).

| Attribute | Type | Req | Notes |
|---|---|---|---|
| merchant_id | uuid | Y | |
| legal_name, tax_code, category | string | Y | |
| channels_enabled | array{WIDGET,HOSTED,QR} | Y | |
| api_client_id / mtls_cert_ref | string | Y | NFR-SEC-03 |
| zero_pct_eligibility, supports_refer_hold | json, bool | Y | |
| contact_name, contact_phone | string | Y | Personal data |
| sbh_merchant_ref | ext:SBH | N | If onboarded via Sổ Bán Hàng (AD-02) |
| status | enum{ONBOARDING,LIVE,SUSPENDED} | Y | |

#### DM-32 MerchantLead (refer-a-merchant, Should)
**Description:** "Suggest a shop" (AD-02, US-266), entered by a customer with the **shop owner's consent to be contacted**.

| Attribute | Type | Req | Notes |
|---|---|---|---|
| lead_id | uuid | Y | |
| suggested_by | →DM-01 | Y | |
| shop_name, owner_phone | string | Y | owner_phone is personal data of a third party |
| owner_contact_consent_ref | string | Y | Evidence of the owner's consent |
| status | enum{NEW,CONTACTED,ONBOARDED,REJECTED,EXPIRED} | Y | |
| merchant_id | →DM-31 | N | After onboarding |
| perk_status | enum{PENDING,GRANTED,NOT_ELIGIBLE} | N | After the first PoP sale |
| purge_due_at | ts | Cond. | `merchant_lead.retention_days` if not onboarded (OQ-17C-13) |

#### DM-33 Passport (good-payer credit passport, Should, gated)
**Description:** issued after a fully on-time PoP loan **and** a passing total-debt affordability check (AD-03; FM-7 [R62]; US-269, US-270).

| Attribute | Type | Req | Notes |
|---|---|---|---|
| passport_id | uuid | Y | |
| subject_key | →DM-01 | Y | |
| eligibility_decision_id | →DM-17 | Y | `policy.passport_total_debt_affordability_rule` |
| status | enum{ISSUED,SUSPENDED,REVOKED,EXPIRED} | Y | |
| on_time_record_summary | json | Y | Only what the customer chooses to present |
| presentations | array{merchant_id, presented_at, outcome} | N | |

#### DM-34 FairnessMonitoringSnapshot
**Description:** the monthly **aggregate** approval rate and MOB3 30+ by gender, age band and region, with ratios against the reference group (US-032; G-5; KR 4.3). **It contains no row-level personal data.**

| Attribute | Type | Req | Notes |
|---|---|---|---|
| snapshot_id, period | uuid, month | Y | |
| model_version_id / segment | →DM-10 / enum | Y | |
| dimension, group, reference_group | enum, string, string | Y | |
| n_applications, approval_rate, mob3_30_rate | int, decimal, decimal | Y | Small cells are suppressed below a minimum count (OQ-19-13) |
| approval_ratio, mob3_ratio, alert | decimal, decimal, bool | Y | Alert if approval_ratio < 0.8 (A-10-7) |

#### DM-35 DriftMetric
**Description:** weekly PSI on scores and the top-20 features, plus monthly performance (US-031; G-8; NFR-AI-04).

| Attribute | Type | Req | Notes |
|---|---|---|---|
| metric_id, computed_at | uuid, ts | Y | |
| model_version_id | →DM-10 | Y | |
| target | enum{SCORE,FEATURE} + feature_name | Y | |
| psi, gini, ks | decimal | N | |
| band | enum{OK,INVESTIGATE,ACT} | Y | 0.10 / 0.25 |
| action_taken | enum{NONE,FALLBACK_TO_CHAMPION,GRADUATION_PAUSED,CHAMPION_DRIFT_ACTION} | Y | |

#### DM-36 CCPReadLog (processing-activity and audit log)
**Description:** one entry per CCP read and per staff view of a case (US-008 AC 5; NFR-SEC-08). It serves the KR 4.1 reconciliation and **Decree 356 Art. 8's duty to log all processing activities** for financial, banking and credit businesses [P2].

| Attribute | Type | Req | Notes |
|---|---|---|---|
| read_id | uuid | Y | |
| caller_service / caller_user | enum / string | Y | |
| subject_key | uuid | Y | |
| purpose | enum{SC,MK,RF} | Y | |
| consent_entry_id | →DM-04 | N | Null means the read was denied |
| ccp_version_id, fields_returned | →DM-09, array | Y | |
| result | enum{ALLOWED,DENIED} | Y | |
| read_at, prev_hash | ts, hash | Y | |

#### DM-37 AnalyticsEvent (envelope)
**Description:** the common envelope for every product-analytics event in §5. **No direct identifiers are allowed.** It uses `subject_pid`, `application_pid` and `session_id` only. De-identified data is excluded from the PDPL's definition of personal data [P3]. Whether **pseudonymised** data counts as de-identified is **not verified**, so this design conservatively treats it as personal data. So this store is in the PDPIA scope and is access-controlled, but it is kept separate from identifiable stores.

| Attribute | Type | Req | Notes |
|---|---|---|---|
| event_id, event_name, schema_version | uuid, string, int | Y | |
| occurred_at (server), received_at | ts | Y | Server timestamps only, for KR 1.2 |
| subject_pid, application_pid, session_id | string | N | Keyed hashes |
| lane, cohort, segment, channel, merchant_id | enum / uuid | N | merchant_id is business data |
| properties | json (schema per event) | Y | Validated against the tracking plan; unknown properties are rejected |

---

### 1.4 Entity Summary Table

| # | Entity | Description | Related entities | Owner |
|---|---|---|---|---|
| DM-01 | Customer | CIF-linked customer reference | DM-02, 03, 04, 08, 14, 22, 27, 30 | CCP (ref to ext:CBS) |
| DM-02 | Applicant | Lane B pre-CIF applicant | DM-01, 04, 14, 15 | CHK |
| DM-03 | FairnessAttributeRecord | Protected attributes, separate store | DM-01/02, 34 | GOV |
| DM-04 | ConsentRecord | Append-only purpose and source consent | DM-01/02, 05, 16, 36 | CON |
| DM-05 | DataSourceLink | Connected sources and status | DM-04, 08 | ING |
| DM-06 | FeatureDefinition | Feature registry and usage tags | DM-07, 10 | FST |
| DM-07 | FeatureSnapshot | Point-in-time feature values | DM-06, 09, 11, 17 | FST |
| DM-08 | CustomerCreditProfile | Current CCP F1–F6 | DM-09 | CCP |
| DM-09 | CCPVersion | Immutable CCP history | DM-08, 10, 12, 17 | CCP |
| DM-10 | ModelVersion / ModelCard | Model registry | DM-11, 35, 09, 17 | GOV |
| DM-11 | TrainingDataset | Versioned training set | DM-10, 07 | MDL |
| DM-12 | PolicyParameterSet | Versioned policy and rate grid | DM-09, 17 | POL (edited via GOV console) |
| DM-13 | CheckoutSession | Merchant session | DM-31, 14, 21 | MXG / CHK |
| DM-14 | Application | Credit application | DM-13, 15, 16, 17, 21 | CHK |
| DM-15 | eKYCResult | eKYC result and references | DM-14 | CHK |
| DM-16 | CICInquiry | CIC enquiry, raw ≤ 30 days | DM-14, 04, 07 | ING (CIC client) |
| DM-17 | Decision | Append-only decision log | DM-14, 18, 19, 22, 09, 10, 12 | POL (stored in the C-15 audit store) |
| DM-18 | ReasonCode | Code catalogue | DM-17 | POL |
| DM-19 | ReferCase / OfficerReview | Refer queue and overrides | DM-17 | CASE |
| DM-20 | IntegrityFlag / FraudCase | F5 flags, fraud label | DM-14 | CHK (flags) / CASE (fraud case) |
| DM-21 | Package / CostDisclosure | Quotes and disclosure hash | DM-14, 22 | OFR |
| DM-22 | LoanContract | e-contract and booking reference | DM-17, 21, 23, 24, 25 | CHK (evidence); ext:CBS (loan) |
| DM-23 | RepaymentEvent | Repayment facts | DM-22 | ext:CBS → SRV |
| DM-24 | EarlyWarningFlag | EW flag and outreach | DM-22 | SRV |
| DM-25 | GraduationOffer | Offered step and acceptance | DM-22, 17 | SRV |
| DM-26 | Campaign | Campaign and holdout config | DM-27, 29 | CMP |
| DM-27 | CampaignAssignment | Treatment or holdout arm | DM-26, 01 | CMP |
| DM-28 | MarketingSuppression | Opt-out, complaint, cooling | DM-01 | CMP |
| DM-29 | Recommendation | Weekly recommendations | DM-01, 26, 31 | REC |
| DM-30 | Referral | Invite and reward ledger | DM-01, 02 | REF |
| DM-31 | MerchantPartner | Partner merchant | DM-13, 29, 32 | MXG |
| DM-32 | MerchantLead | Suggested shop (Should) | DM-01, 31 | REF |
| DM-33 | Passport | Good-payer passport (Should) | DM-01, 17 | REF |
| DM-34 | FairnessMonitoringSnapshot | Aggregate fairness | DM-03, 10 | GOV |
| DM-35 | DriftMetric | PSI and performance | DM-10 | GOV |
| DM-36 | CCPReadLog | Processing-activity log | DM-04, 09 | CCP (stored in the GOV audit store) |
| DM-37 | AnalyticsEvent | Pseudonymous event envelope | — | ANL |

---

## 2. CRUD Matrix (by owner service) `[Rubric R3]`

Legend:
- Each cell names the service or actor allowed to perform the operation.
- **"—"** means not permitted.
- **Append-only** means updates are made only by appending a superseding record, never in place.
- **Purge** means automated deletion when the retention period expires (§6). **Erase** means deletion on a data-subject request, subject to legal holds (§6.2).
- **CUST** is the customer acting through the HUB or checkout UI; **OFF** is a credit officer (OP-01); **MR** is Model Risk; **DPO** is the Data Protection Officer.
- Every read of DM-08 or DM-09 by any service goes through the **CCP API purpose check** and writes DM-36 (US-008).

| Entity | Create | Read | Update | Delete | Owner Service |
|---|---|---|---|---|---|
| DM-01 Customer | CCP (on first profiling or CASA conversion) | All services by customer_key; merchants **never** | CCP (cohort, segment, pop_first_loan_at) | Erase (DPO workflow) subject to legal hold | **CCP**; master data stays in ext:CBS |
| DM-02 Applicant | CHK (chip read + form) | CHK, CCP, CASE (assigned case only), REF (status only) | CHK (status, conversion link) | Purge (§6 R-08) / Erase | **CHK** |
| DM-03 FairnessAttributeRecord | GOV (from CIF/CCCD at decision time) | **GOV fairness job only** | GOV (address or ID correction) | With the parent subject | **GOV** |
| DM-04 ConsentRecord | CON (CUST action via HUB/checkout; staff-assisted with evidence) | CON; all services through the purpose check; DPO | **— (append-only)** | — until the dependent retention ends (NFR-DATA-06) | **CON** |
| DM-05 DataSourceLink | ING (on consent grant or source detection) | ING, FST, CCP (F3 sources), HUB (Privacy screen) | ING (status, staleness) | ING (on revocation, after dependent features are purged) | **ING** |
| DM-06 FeatureDefinition | FST (data engineering, MR approval for `model_input`) | FST, MDL, GOV | New version only | Retire (never hard-delete while a model references it) | **FST** |
| DM-07 FeatureSnapshot | FST (batch and stream from ING) | MDL (scoring), POL (via CCP), MDL training job (offline) | — (immutable; new rows) | Purge by TTL and retention (§6 R-12); erase on SC withdrawal per `privacy.sc_withdrawal_policy` | **FST** |
| DM-08 CustomerCreditProfile | CCP (on build) | OFR, CHK, CMP, REC, SRV, REF, CASE, HUB through the purpose check | CCP (on each refresh) | CCP (on SC withdrawal; OQ-17A-04) | **CCP** |
| DM-09 CCPVersion | CCP | CCP, CASE, GOV (replay), MDL (training via snapshot) | — (append-only) | Purge (§6 R-10) | **CCP** |
| DM-10 ModelVersion / ModelCard | GOV (registration by MDL, approval by MR) | MDL, POL, GOV, auditors | GOV (status transitions only) | — (retire) | **GOV** |
| DM-11 TrainingDataset | MDL (training pipeline) | MDL, MR, GOV | — | Purge (§6 R-13) | **MDL** |
| DM-12 PolicyParameterSet | POL, through the OP-02 console with four-eyes approval | POL, OFR, CHK, SRV, CMP, REC, REF, GOV | — (new version) | — | **POL** |
| DM-13 CheckoutSession | MXG (merchant API) | MXG, CHK, OFR | CHK/MXG (state) | Purge (§6 R-08) | **MXG** |
| DM-14 Application | CHK | CHK, POL, CASE, GOV, ANL (pseudonymised) | CHK (status) | Purge (§6 R-06/R-08) | **CHK** |
| DM-15 eKYCResult | CHK (from ext:C06 or the eKYC provider) | CHK, MDL (F5), CASE (identity review) | — | Purge (§6 R-05) | **CHK** |
| DM-16 CICInquiry | ING CIC client (on request by CHK, REC, SRV) | ING → FST (derived); CASE (summary only) | ING (status); **raw payload nulled at 30 days** | Purge raw (§6 R-04); metadata with the dossier | **ING** |
| DM-17 Decision | POL (automated), CASE (officer), SRV (graduation), CMP (pre-approval via POL) | POL, CASE, GOV, HUB ("Why this decision", customer's own), auditors | **— (append-only)** | Purge only at the end of retention (§6 R-06/R-07) | **POL** |
| DM-18 ReasonCode | POL (catalogue admin with Compliance) | POL, HUB, CASE, MXG (code only, no detail) | New version | Deactivate only | **POL** |
| DM-19 ReferCase / OfficerReview | CHK (on REFER), CUST (human-review request, NFR-AI-02) | CASE (assigned OFF), GOV | CASE (assignment, outcome, justification) | With the dossier (§6 R-06/R-07) | **CASE** |
| DM-20 IntegrityFlag / FraudCase | CHK/MDL (flags); CASE (fraud confirmation) | CHK, CASE, fraud team, GOV (G-2) | CASE (status) | With the dossier | **CHK** (flags), **CASE** (case) |
| DM-21 Package / CostDisclosure | OFR | OFR, CHK, HUB, MXG (package terms only) | OFR (status) | Purge with the application (§6) | **OFR** |
| DM-22 LoanContract | CHK (on signature + CBS booking ack) | CHK, SRV, CASE, HUB (own contract), GOV | SRV (status, from CBS) | Purge after R-06 | **CHK** (evidence); loan account in **ext:CBS** |
| DM-23 RepaymentEvent | ext:CBS → ING | SRV, FST (S15), REF (K count), GOV (MOB3) | — | Purge per R-06 (copy); CBS keeps the ledger (R-01) | **ext:CBS** (system of record); copy in **SRV** |
| DM-24 EarlyWarningFlag | SRV | SRV, CMP, REC, REF (suppression only), CASE | SRV (clear, outreach, cure) | With the dossier | **SRV** |
| DM-25 GraduationOffer | SRV (via POL decision) | SRV, HUB, GOV | CUST (accept/decline with auth), SRV (expire, withdraw) | With the dossier | **SRV** |
| DM-26 Campaign | CMP (marketing analyst) | CMP, GOV, ANL | CMP (status) | Purge (§6 R-15) | **CMP** |
| DM-27 CampaignAssignment | CMP | CMP, ANL (pseudonymised), MDL (uplift training, MK scope) | CMP (delivery status) | Erase within 20 days of MK withdrawal (NFR-DATA-05) | **CMP** |
| DM-28 MarketingSuppression | CMP (from CON withdrawal or complaint intake) | CMP, REC, HUB | CMP (cooling end) | Kept while it is needed to honour the opt-out (NFR-DATA-05) | **CMP** |
| DM-29 Recommendation | REC | REC, HUB, ANL | REC (status), CUST (dismiss) | Erase on MK withdrawal; purge (§6 R-15) | **REC** |
| DM-30 Referral | REF (inviter action) | REF; inviter sees status only (HUB); invitee sees nothing about the inviter beyond the link | REF (status, count, reward) | Erase on RF withdrawal, except reward accounting entries (OQ-19-10) | **REF** |
| DM-31 MerchantPartner | MXG (merchant onboarding) | MXG, CHK, OFR, REC, REF | MXG | Deactivate; purge contacts (§6 R-17) | **MXG** |
| DM-32 MerchantLead | REF (CUST suggestion) | REF, HLB merchant team | REF (status) | Purge at `merchant_lead.retention_days` if not onboarded | **REF** |
| DM-33 Passport | REF (via POL eligibility decision) | REF, HUB, MXG (presented attributes only, with CUST action) | REF (suspend, revoke) | With the dossier | **REF** |
| DM-34 FairnessMonitoringSnapshot | GOV job | GOV, MR, Compliance, auditors | — | Kept with the model technical file (§6 R-13) | **GOV** |
| DM-35 DriftMetric | GOV job | GOV, MR, MDL | GOV (action_taken) | Kept with the technical file | **GOV** |
| DM-36 CCPReadLog | CCP API (automatic) | GOV, DPO, Compliance | **— (append-only)** | Purge (§6 R-14) | **CCP** (stored in the GOV audit store) |
| DM-37 AnalyticsEvent | All services via the ANL SDK | ANL, KPI owners (Skill 10 §4) | — | Purge (§6 R-16) | **ANL** |

---

## 3. Data Ownership Map (including external sources) `[Rubric R3]`

| Data type | Owned by (system of record) | Accessed by | Sync mechanism | Notes / evidence |
|---|---|---|---|---|
| Customer master (CIF, name, contacts, KYC status) | **ext:CBS** | CCP (reference), CHK (conversion), HUB, SMS dispatch | Read on demand through a CBS API; CIF-created event to CCP | Not duplicated (§1.3 DM-01). A-13-10 (CBS APIs) is unverified; SP-4 |
| CASA transactions and balances (S1–S8) | **ext:CBS** | ING → FST | **Event stream** (CDC or CBS events) plus nightly batch reconciliation (C-02/C-03) | S2/S4/S7 counterparty metadata = A-13-1/A-13-2 (spike SP-1) |
| Card and loan conduct (S9, S10) | **ext:CARDS**, HLB loan systems | ING → FST | Monthly batch (Skill 14 §3) | MCC coding = A-13-4 |
| HLB Connect engagement (S12) | HLB Connect platform | ING → FST (`propensity_only`) | Daily batch | Never used for risk (DD-07) |
| PoP loan booking, schedule, repayment ledger (S15) | **ext:CBS** | SRV, FST, REF, GOV | Stream (repayment events), with a **daily batch fallback** (C-28) | Booking has no fallback in-session (B-07; OQ-14-11) |
| CIC credit report (S14) | **ext:CIC** (keeps credit information 5 years from the date it arose, Art. 11(2) [P7]) | ING CIC client → FST (derived); POL (stacking) | Synchronous **host-to-host** enquiry per application, draw or graduation; monthly portfolio refresh (G-3) [T6] | Covered by the SC notice. Art. 12(6) consent applies to third-party users, not HLB [P7]. Raw payload ≤ 30 days (A-18-1) |
| CIC reporting of PoP loans (PR-04) | HLB (reporting institution) → ext:CIC | SRV | Periodic file or H2H submission at the frequency set in the **SBV indicator system** referenced by Circular 15/2023 Art. 10 [P7] | Frequency not yet pinned down (OQ-19-07; narrows OQ-18-06) |
| Chip-CCCD data, face match, VNeID (F5, AP-02) | **ext:C06** (national population DB) / VNeID; HLB eKYC platform | CHK → DM-15 (result only) | Synchronous API through HLB's existing integration [R67]; web-to-app hand-off for web checkout (U-01) | C06 fee is per authentication [T8] |
| Sổ Bán Hàng merchant ledger (S5) | **ext:SBH** (partner); the merchant is the data subject or controller | ING → FST, with `source:SBH` consent | Daily batch per the data-sharing agreement; stale > 48 h → F3 downgrade (Skill 18 §5.2) | API and terms not public (C-02; [R66]) |
| Cross-bank transactions (S16, Phase 2) | **ext:OPENAPI** (other banks) | ING → FST | Circular 64 REST/OAuth pull; **asynchronous**, never on the checkout path; 180-day window [R73][T18] | Behind `feature.open_api_enabled` |
| Merchant basket and order | **ext:MERCHANT** | MXG → CHK, OFR | Signed API calls (mTLS + OAuth 2.0 + JWS; NFR-SEC-03) | Merchants receive **only** tier, packages and status |
| Consent (SC/MK/RF and source scopes) | **CON** | All services (purpose check); HUB | Synchronous check on every read; revocation events pushed to CMP, REC, ING and REF | Fail closed (NFR-REL-02) |
| Features and CCP | **FST**, **CCP** | MDL, POL and the stage services | Online store (low latency); offline store for training; CCP versioned on each refresh | NFR-PERF-06/08 |
| Decisions, reason codes, cases | **POL**, **CASE** (C-15 audit store) | GOV, HUB (customer's own), auditors | Synchronous write before a decision is released (NFR-REL-08); RPO = 0 | AI-Law logs [N5] |
| Models, parameters, monitoring | **GOV**, **POL** | MDL, POL, MR | Registry pointer switch; parameter-set versioning | Rollback ≤ 5 min |
| Campaigns, recommendations, referrals | **CMP**, **REC**, **REF** | HUB, ANL | Batch builds (nightly / weekly) plus events | MK / RF scope |
| Protected attributes | **GOV** (separate store) | GOV fairness job only | Derived at decision time from CIF/CCCD | Never in the feature store |
| Analytics events | **ANL** | KPI owners (Skill 10 §4) | Asynchronous event bus → warehouse; server-side timestamps | Pseudonymous only |
| SMS / OTP delivery | **ext:SMS** (processor) | CHK, CASE, SRV | API; DPA required (Skill 18 §1.3) | Phone numbers pass through; not stored by ANL |
| Telco / e-wallet / platform feeds (S17) | — | — | **Not in v1** (PL-01) | Decree 94 sandbox open (OQ-02-17) |

**Hosting:** every CreditPulse store in this map is hosted in Vietnam on portable components (NFR-SEC-14; Skill 14 §4 [T1][T2]). Any HLB Group access uses **de-identified** extracts under a TIA (OQ-18-14).

---

## 4. PII Register `[Rubric R3]`

### 4.1 Sensitive-data categories (Decree 356/2025)
Decree 356 classes the following as **sensitive** personal data [P1][P2]:
- bank-account information, financial transaction history, and credit, securities and insurance records;
- biometric data;
- images of ID and citizen-ID cards;
- login credentials for electronic-identification accounts;
- data on behaviour in using telecommunications services and social networks;
- geolocation;
- racial or ethnic origin, and religious beliefs.

CreditPulse processes the first five. It **never collects** ethnicity, religion or location (Skill 13 §6).

### 4.2 Register

| Data field | Entity | PII type | Legal basis | Retention (§6 rule) | Access control |
|---|---|---|---|---|---|
| CIF / customer_key | DM-01 | Basic (identifier) | SC consent; contract | With the customer relationship (ext:CBS) | All CreditPulse services, by key only |
| Name, phone, email | DM-01 (ref), DM-02 | Basic | SC consent (Lane B); contract | R-08 (applicant), CBS (customer) | CHK, CASE (assigned), SMS processor (phone only) |
| CCCD number, chip data fields | DM-02, DM-15 | **Sensitive** (ID-card data) | SC consent; legal obligation (eKYC rules, Circular 17/2024 [Skill02 RF-4]) | R-05 | CHK; CASE (identity review) |
| Face / liveness result (score, match flag); **no raw images in CreditPulse** | DM-15 | **Sensitive** (biometric) | SC consent; legal obligation (biometric authentication [Skill02 RF-4/RF-5]) | R-05; raw images belong to HLB's account-opening dossier (OQ-19-01) | CHK only |
| Declared monthly income (Lane B) | DM-14 | **Sensitive** (financial) | SC consent | R-06/R-07 | CHK, POL, CASE |
| CASA transactions, balances, inflow features S1–S8 | DM-07 | **Sensitive** (transaction history) | SC consent | R-12 | FST, MDL (scoring and training), CASE (summary) |
| Card and loan conduct S9, S10 | DM-07 | **Sensitive** | SC consent | R-12 | FST, MDL |
| CIC report (raw) | DM-16 | **Sensitive** (credit records) | SC notice + credit-appraisal purpose; Circular 15/2023 [P7] | R-04 (raw ≤ 30 days) | ING only; derived features to FST |
| Sổ Bán Hàng sales data S5 | DM-07 | **Sensitive** (financial) | `source:SBH` consent + SC | R-12 | FST, MDL |
| Open API cross-bank data S16 (Phase 2) | DM-07 | **Sensitive** | `source:OPENAPI:<bank>` consent (Circular 64 [R73]) + SC | R-12; 180-day access window | FST, MDL |
| HLB Connect engagement S12 | DM-07 | **Sensitive** (behavioural tracking) | MK consent (propensity only) | R-12 | FST, CMP, REC — **never MDL risk models** |
| Checkout footprint S13 (device type, time of day, email/phone validity) | DM-07, DM-14 | **Sensitive** (behavioural) | SC consent | R-12 | FST, MDL, CHK (integrity) |
| CCP F1–F6 and history | DM-08, DM-09 | **Sensitive** (credit evaluation) | SC consent; notice must disclose scoring and profiling [P2] | R-10 | Through the purpose-checked CCP API only |
| Decision + reason codes | DM-17, DM-18 | **Sensitive** | SC consent; AI Law logging [Skill18 N5] | R-06/R-07 | POL, CASE, GOV, customer (own) |
| Officer notes / override justification | DM-19 | **Sensitive** | Legitimate credit appraisal (contract) | R-06/R-07 | CASE (assigned), GOV |
| Loan contract, APR, schedule, e-signature evidence | DM-22 | **Sensitive** (credit record) | Contract; Law on E-Transactions 2023 [Skill18 N23] | R-06 | CHK, SRV, customer (own) |
| Repayment events | DM-23 | **Sensitive** | Contract | R-06 (copy); CBS ledger R-01 | SRV, FST, REF (count only), GOV |
| Early-warning flag, outreach log | DM-24 | **Sensitive** | Contract / responsible lending | R-06 | SRV, CASE |
| Campaign assignment, recommendations | DM-27, DM-29 | Basic + behavioural | MK consent | R-15; erase on MK withdrawal (NFR-DATA-05) | CMP, REC |
| Referral (inviter ↔ invitee link, reward) | DM-30 | Basic | RF consent (inviter); SC (invitee) | R-15; reward accounting per R-02 (OQ-19-10) | REF; inviter sees status only |
| Gender, age band, region | DM-03 | Basic (region is derived from ID data; not geolocation) | Legal obligation / legitimate purpose: fairness monitoring under AI Law transparency [Skill18 §7] | With the technical file (R-13), aggregated only | **GOV fairness job only**; never a model input |
| Analytics events (pseudonymous IDs) | DM-37 | Treated as personal data (conservative; §1.3 DM-37) | Per originating purpose | R-16 | ANL, KPI owners |
| Merchant contact details | DM-31, DM-32 | Basic | Contract / merchant consent | R-17 | MXG, merchant team |

---

## 5. Analytics Event Schema `[Rubric R3]`

**Envelope** (DM-37): `event_name`, `event_ts` (server), `subject_pid` (pseudonymous), `application_pid`, `session_id`, `cohort` (A/B), `segment`, `channel`, `schema_version`. No direct identifiers.

**Status column:** "S17" = the event name appears in Skill 17 ACs and is reused verbatim. "new" = added by this skill.

### 5.1 Acquisition and decision events

| Event | Trigger | Key properties | Feeds | Status |
|---|---|---|---|---|
| `awareness_candidates_built` | Nightly AW-01 job completes | count by segment and F3, holdout_count | KR 3.1 denominator | S17 |
| `holdout_assigned` | Customer placed in holdout (AW-02, RT-01) | campaign_id, arm | KR 3.1, KR 3.4 | new |
| `offer_delivered` / `offer_opened` / `offer_clicked` / `offer_dismissed` | Awareness offer lifecycle | campaign_id, arm, limit_band | KR 3.1; G-6 | S17 |
| `hub_limit_viewed` | Customer opens "My limit" | tier, self_initiated=true | Awareness reach (DD-11) | S17 |
| `hlb_option_shown` | Merchant checkout renders the HLB option | merchant_id, integration_type | Merchant coverage (EP-8) | S17 |
| `application_started` | Customer taps "Pay over time" | lane, merchant_id | KR 1.1 denominator; funnel | S17 |
| `packages_shown` | CO-01 returns packages | n_packages, min_instalment_band, zero_percent_flag | KR 3.2 | S17 |
| `affordability_check` | F2 evaluated against a package | pass/fail, f2_status | G-4 | S17 |
| `cost_disclosed` | CO-02 all-in cost screen rendered | package_id, disclosure_hash | KR 4.1 audit; FM-4 | S17 |
| `package_selected` / `package_accepted` | Customer selects / confirms | tenor, zero_percent_flag | KR 3.2 | S17 |
| `application_submitted` | Package confirmed (KR 1.2 start, U-08) | lane | KR 1.2 start | S17 |
| `ekyc_started` / `ekyc_completed` | eKYC lifecycle | method (NFC / face-on-file / hand-off), attempts, result | KR 1.2; eKYC pass rate | S17 |
| `web_to_app_handoff` | Web checkout hands off to HLB Connect | handoff_ms, completed | KR 1.2 (U-01) | new |
| `cic_unavailable` | CIC timeout or error | fallback_rule_applied | AP-07 monitoring | S17 |
| `ccp_build_started` / `ccp_build_completed` / `ccp_refreshed` | CCP lifecycle | trigger, duration_ms, f3 | KR 1.2; E-08 | S17 |
| `decision_returned` | Policy returns a tier | tier, reason_codes[], model_versions, corridor_flag, in_session | **KR 1.1, KR 1.2**, KR 2.1 | S17 |
| `reason_code_missing` | Decision without a reason code (should never fire) | decision_id | KR 4.1 alarm | S17 |
| `case_created` / `case_assigned` / `case_decided` / `refer_decided` | Refer band lifecycle | sla_deadline, outcome, override_flag | **KR 1.4** | S17 |
| `refer_sla_breached` | Case open beyond SLA | minutes_over | KR 1.4 alert | new |
| `contract_signed` | e-contract + OTP complete | tenor, amount_band | GIML input | S17 |
| `loan_booked` | CBS booking acknowledged | loan_pid, amount_band, tenor, lane, segment | **North Star GIML**; KR 1.3 denominator | new |
| `casa_offered` / `casa_opened` | CASA-first step | — | **KR 2.2** | S17 |
| `fraud_confirmed` | Fraud case confirmed | loan_pid or application_pid | **G-2** | new (Skill 17 uses `fraud_event`) |

### 5.2 Lifecycle events

| Event | Trigger | Key properties | Feeds | Status |
|---|---|---|---|---|
| `repayment_recorded` | Repayment or missed-due event from CBS | days_late, partial | PR-01; G-1 | new |
| `mob3_status_computed` | Monthly job at MOB3 | dpd30_flag | **G-1; North Star (c)** | new |
| `early_warning_flagged` / `early_warning_cured` | PR-02 rule fires / clears | trigger_rule | **KR 3.3** | new |
| `graduation_offered` / `graduation_accepted` / `graduation_declined` | PR-03 | step_type | KR 3.4; C2 audit | `graduation_accepted` S17; others new |
| `stacking_check_failed` | RT-02 gate fails | cic_concurrent_loans | **G-3** | new |
| `recommendation_built` | RT-01 weekly job | n_recs, arm | KR 3.4 | new |
| `rec_viewed` / `rec_clicked` / `rec_dismissed` | Recommendation lifecycle | category | KR 3.4 | S17 |
| `invite_link_created` / `invite_link_opened` / `invitee_booked` / `reward_released` | AD-01 lifecycle | k_count | **KR 3.5** | S17 |
| `consent_granted` / `consent_withdrawn` | Consent ledger change | purpose/scope, channel | **G-7**, KR 4.1 | `consent_withdrawn` S17; `consent_granted` new |
| `marketing_complaint_logged` | Complaint intake tagged to a marketing contact | campaign_id | **G-6** | new |
| `evidence_confidence_upgraded` | F3 moves from THIN to a higher tier | from, to, months_since_first_loan | **KR 2.3** | new |

### 5.3 Governance events (audit store, not the analytics store)

| Event | Trigger | Feeds |
|---|---|---|
| `psi_computed` | Weekly drift job | **G-8** |
| `fairness_snapshot_computed` | Monthly fairness job | **G-5**, KR 4.3 |
| `ccp_read` (DM-36) | Every CCP read | **KR 4.1** |
| `model_fallback_to_champion` | Drift or failure fallback | G-8 action |
| `policy_parameters_changed` | New PolicyParameterSet version | Audit |

### 5.4 KPI coverage check (Skill 10 → events)

| Skill 10 metric | Event(s) |
|---|---|
| North Star GIML | `decision_returned` (in_session) + `loan_booked` + `mob3_status_computed` |
| KR 1.1 in-session decision rate | `application_started`, `decision_returned` |
| KR 1.2 p50/p90 time | `application_submitted` → `decision_returned`; `ekyc_*`, `web_to_app_handoff` |
| KR 1.3 blended cost per decision | `decision_returned` (tier mix) + `case_decided` counts × finance cost allocation |
| KR 1.4 refer SLA | `case_created`, `case_decided`, `refer_sla_breached` |
| KR 2.1 thin-file approval (champion/challenger) | `decision_returned` (segment, model_versions, arm) + `mob3_status_computed` |
| KR 2.2 CASA-first | `casa_offered`, `casa_opened` |
| KR 2.3 evidence upgrade | `evidence_confidence_upgraded` |
| KR 3.1 uplift | `holdout_assigned`, `offer_*`, `loan_booked` |
| KR 3.2 package acceptance | `packages_shown`, `package_accepted` |
| KR 3.3 early-warning cure | `early_warning_flagged`, `early_warning_cured` |
| KR 3.4 good-payer repeat | `repayment_recorded`, `loan_booked`, `recommendation_built`, `holdout_assigned` |
| KR 3.5 referral quality | `invitee_booked`, `reward_released`, `mob3_status_computed` |
| KR 4.1 reason codes & consent | `decision_returned`, `reason_code_missing`, `ccp_read` |
| KR 4.2 AI Law docs | Governance milestone (not an event) |
| KR 4.3 / G-5 fairness | `fairness_snapshot_computed` |
| G-1 MOB3 30+ | `mob3_status_computed` |
| G-2 fraud | `fraud_confirmed` |
| G-3 stacking | `stacking_check_failed` + monthly CIC refresh |
| G-4 affordability breach | `affordability_check` + `loan_booked` |
| G-6 complaints | `marketing_complaint_logged` |
| G-7 consent withdrawal | `consent_withdrawn` |
| G-8 drift | `psi_computed` |

**Result:** every Skill 10 metric maps to at least one event. KR 4.2 is a milestone, not an event.

---

## 6. Data Retention Policy `[Rubric R4]`

| Rule | Data type | Retention period | Deletion method | Regulatory basis |
|---|---|---|---|---|
| R-01 | Loan ledger and repayment records (system of record) | Per HLB CBS policy; accounting documents ≥ 10 years | CBS archival | Accounting Law / Decree 174/2016 [Skill18 N20] |
| R-02 | Accounting entries for referral rewards and fees | ≥ 10 years | Archival | [Skill18 N20] |
| R-03 | KYC information and customer transaction information | 5 years from the end of the transaction/relationship | Scheduled purge | AML Law 2022, Art. 38 [Skill18 N21] |
| R-04 | Raw CIC payload | ≤ 30 days, then derived features only | Null the raw field | Data minimisation (PDPL); A-18-1 |
| R-05 | eKYC result and references | With the credit dossier (R-06/R-07); raw biometric images are not stored in CreditPulse | Purge with the dossier | eKYC rules [Skill02 RF-4]; OQ-19-01 |
| R-06 | Approved-loan credit dossier (application, decision, contract, cost disclosure, case notes) | **`[DECISION REQUIRED — legal check of the Circular 04/2025 appendix, Group IV]`**, minimum ≥ 10 years for accounting-relevant records | Scheduled purge after the period | Circular 04/2025 applies to credit institutions [P5]; [Skill18 N20] |
| R-07 | Declined / withdrawn application dossier | `[DECISION REQUIRED — legal]`; must still cover AI Law logging and complaint-handling windows | Scheduled purge | OQ-19-11 |
| R-08 | Lane B applicant records never converted; checkout sessions | Same as R-07 | Scheduled purge | OQ-19-11 |
| R-09 | Consent ledger entries | As long as any dependent processing record is retained (NFR-DATA-06) | Purge after the last dependent record | PDPL verifiability [P2] |
| R-10 | CCP versions | With the dossier they supported (R-06/R-07) | Purge | AI Law logs [Skill18 N5] |
| R-11 | Decision log (audit) | `[DECISION REQUIRED]` (OQ-14-10), not shorter than R-06 for approved loans | Purge | AI Law logs; disputes |
| R-12 | Feature snapshots | Online store TTL per feature; offline snapshots kept only as long as the training sets that use them (R-13) | TTL + purge; erase on SC withdrawal per `privacy.sc_withdrawal_policy` | PDPL minimisation |
| R-13 | Training datasets, model cards, fairness and drift records (technical file) | `[DECISION REQUIRED — AI Law technical-documentation period]` (OQ-19-12) | Purge | AI Law 134/2025 [Skill18 N5] |
| R-14 | CCP read log (processing-activity log) | `[DECISION REQUIRED]`; at least the annual compliance-assessment cycle [P2] | Purge | Decree 356 finance-sector logging [P2] |
| R-15 | Campaign assignments, recommendations, referral records | While the purpose is active; erase ≤ 20 days after MK/RF withdrawal (NFR-DATA-05) | Erase | PDPL [Skill18 §8] |
| R-16 | Raw analytics events | `[DECISION REQUIRED]` (OQ-19-13) | Purge / aggregate | PDPL minimisation |
| R-17 | Merchant contacts (partners and leads) | While the relationship is active; unconverted leads per `merchant_lead.retention_days` | Purge | PDPL |

---

## 7. Data Lineage for Model Training `[Rubric R3]`

1. **Sources → feature store.** Every feature is registered in DM-06 with one of three usage tags:
   - `model_input` (allowed in risk models);
   - `propensity_only` (S12; allowed only in propensity models);
   - `fairness_monitoring_only` (gender, age band, region, `referred_by_customer`).

   The training pipeline **rejects** any risk-model feature set containing a tag other than `model_input` (US-004-type check).
2. **Point-in-time correctness.** Training rows are assembled with the feature store's point-in-time join as of the observation timestamp [T14]. No feature can leak from after the decision.
3. **Consent as of observation time.** A row is included only if SC consent was active at the observation timestamp. Withdrawals after that time follow `privacy.sc_withdrawal_policy` (OQ-17A-04).
4. **Labels** come only from the CBS loan ledger (`mob3_status_computed`, DPD). Declined applications have no label. Challenger training for THIN tiers uses **randomised-corridor** approvals only (Skill 13 §1.1, U-05) to limit reject-inference bias [L20].
5. **Excluded by construction:** protected attributes, `referred_by_customer`, S12 in risk models, and phone contacts or gallery data (never collected).
6. **Reproducibility.** Every Decision (DM-17) stores the feature-snapshot hash, model version and policy-parameter version, so any decision can be replayed (AI Law technical documentation).

---

## 8. Open Items Raised by This Skill

| # | Item | Carry to |
|---|---|---|
| OQ-19-01 | Raw biometric images: does HLB's account-opening dossier keep them, and for how long? CreditPulse stores results only | Skill 22 (legal) |
| OQ-19-01b | Exact retention periods in the Circular 04/2025 appendix (Group IV credit, Group XII credit information). This pass could not read the appendix from a primary source; earlier draft figures and the "Circular 37/2026" amendment are unverified | Skill 22 (legal) |
| OQ-19-06 | ~~`source:CIC` consent scope~~. **Resolved 2026-09-30:** not required. Circular 15 Art. 12(6) consent applies to third-party users [P7]. Circular 46/2026 (from 1 Nov 2026) tightens explicit consent for those third parties, not for HLB's own enquiries [P6] | Closed |
| OQ-19-07 | CIC reporting frequency for PoP loans (set in the SBV credit-information indicator system under Circular 15/2023 Art. 10 [P7]) | Skill 22 (HLB compliance) |
| OQ-19-10 | On RF withdrawal: which referral data is kept for reward accounting (R-02) vs erased (R-15)? | Skill 22 |
| OQ-19-11 | Retention of declined applications and unconverted Lane B applicants (R-07/R-08) | Skill 22 (legal) |
| OQ-19-12 | AI Law technical-documentation retention period (R-13) | Skill 22 (legal) |
| OQ-19-13 | Raw analytics event retention (R-16), and whether pseudonymised data counts as de-identified under the PDPL (not verified) | Skill 22 (legal) |

---

## Source Index

| # | Source | URL | Used for |
|---|---|---|---|
| P1 | Decree 356/2025/ND-CP (full text via LuatVietnam; QTSC PDF) | https://english.luatvietnam.vn/decree-no-356-2025-nd-cp-dated-december-31-2025-of-the-government-detailing-a-number-of-articles-and-measures-for-the-implementation-of-the-law-on-p-422896-doc1.html ; https://www.qtsc.com.vn/uploads/files/2026/02/02/356_2025_ND-CP_689146.pdf | Sensitive-data categories (list as summarised in search results; article numbers not verified) |
| P2 | Vietnam Briefing, "Vietnam's Personal Data Protection Regulation: Decree 356" | https://www.vietnam-briefing.com/news/vietnam-personal-data-protection-regulation-decree-356.html/ | Sensitive financial data; finance-sector end-to-end logs and annual compliance assessment; consent notices must disclose scoring, profiling and credit evaluation; 72-hour breach notification (regulators; data subjects for finance/banking) |
| P3 | Tilleke & Gibbins, "Vietnam's New Personal Data Protection Law: A Closer Look" | https://www.tilleke.com/insights/vietnams-new-personal-data-protection-law-a-closer-look/ | De-identified data excluded from personal data |
| P5 | LuatVietnam, comparison of Circular 04/2025/TT-NHNN and Circular 22/2021 | https://luatvietnam.vn/linh-vuc-khac/ban-so-sanh-thong-tu-04-2025-tt-nhnn-va-thong-tu-22-2021-tt-nhnn-ve-thoi-han-luu-tru-ho-so-tai-lieu-nganh-ngan-hang-883-103484-article.html | Circular 04/2025 applies to credit institutions; effective 1 Jul 2025 |
| P6 | LuatVietnam, Circular 46/2026/TT-NHNN amending Circular 15/2023; VietNamNet, "Explicit consent required for sharing customers' credit information from Nov. 1" | https://english.luatvietnam.vn/circular-no-46-2026-tt-nhnn-dated-september-09-2026-of-the-state-bank-of-vietnam-amending-and-supplementing-a-number-of-articles-of-circular-no-15-447145-doc1.html ; https://vietnamnet.vn/en/explicit-consent-required-for-sharing-customers-credit-information-from-nov-1-2555225.html | Explicit consent for third-party credit-information use from 1 Nov 2026 |
| P7 | LuatVietnam (English), Circular 15/2023/TT-NHNN on credit information operations | https://english.luatvietnam.vn/tai-chinh/circular-15-2023-tt-nhnn-credit-information-operation-of-the-state-bank-281736-d1.html | Art. 12(6) consent scope (third parties); Art. 10 reporting frequency; Art. 11(2) CIC keeps information 5 years |
| — | Skill 18 sources N3, N5, N20, N21, N23; Skill 14 T14 (Feast point-in-time joins); `_research-notes-07-13.md` [R#]; `literature-review.md` [L20] | see those files | Retention anchors, AI Law, e-transactions, feature store |

*P4, P8, P9 and P10 from the interrupted draft had no recorded URL. Their claims were removed or re-sourced (P9 → T14; P10 → design convention) rather than cited without a source.*

## Skill 22 Resolutions (User decision, Skill 22, 2026-09-30)
- OQ-19-01, 01b, 07, 11, 12 and 13 (retention and legal details) are **accepted as logged, non-blocking legal-review items** (D-05 → RK-23). The retention rules marked `[DECISION REQUIRED — legal]` stay as named retention parameters, set after legal review.
- OQ-19-10 (referral data on RF withdrawal): resolved by D-07 / BD-20-16. The accounting entry is kept (R-02); other referral data is erased (R-15).
