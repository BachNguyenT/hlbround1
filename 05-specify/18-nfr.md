# Non-Functional Requirements
**Product:** HLB Point-of-Purchase Credit Decisioning — **HLB CreditPulse** | **Skill:** 18 | **Status:** Done (2026-09-30)
**Depends on:** user-stories.md, epic-feature-decomposition.md (Skill 16), solution-design.md (Skill 13), success-metrics.md (Skill 10) | **Feeds into:** product-prd.md (Section 5 — NFR), data-requirements.md, error-states.md, risk-register.md

> **How to read this file**
> - **Regime.** HLB Vietnam is a Vietnamese-licensed bank serving Vietnamese residents. GDPR and CCPA **do not apply** and are replaced throughout by the Vietnamese regime (§1). Each instrument has an effective date and a source. Where a numeric obligation could not be verified, this file says so and does not invent a number.
> - **Every requirement has three parts:** a standard (what it is anchored to), a target, and a verification method. IDs are `NFR-<area>-##`, so Skill 17 stories and the Skill 23 PRD can reference them.
> - **Target labels.**
>   - **[Reg]**: a legal or regulatory obligation.
>   - **[Std]**: an industry standard (OWASP, W3C, NIST).
>   - **[Bench]**: a benchmark from a comparable product. Vendor sources are marked *(vendor, Low)*.
>   - **[Design]**: a target this skill proposes, justified in the text. Each one is carried to Skill 22 as `A-18-#`.
>   - **`[DECISION REQUIRED]`**: an HLB choice that research cannot answer.
> - **Skill 14 (Technical Feasibility)** was still in progress when this file was written (all sections `[PENDING]`). The hosting and latency numbers here are therefore not yet cross-checked against it (OQ-18-01).
> - **Citations.**
>   - `[N#]`: new sources for this skill (Source Index at the end).
>   - `[R#]`: `_research-notes-07-13.md`.
>   - `[Skill02 RF-#]` / `[Skill02 S#]`: `02-market-research.md`.
>   - `[L#]` / `X#`: `literature-review.md`.
>   - `[Brief p.#]`: the case brief.
> - **Rubric tags** are written `[Rubric R#]`: R2 Intervention Justification, R3 Solution Concept, R4 Data & Assumptions.
> - **Feature codes** (AP-, CO-, E-, OP-, GV-, MX- …) are the Skill 08/16 codes.

---

## Pre-filled Context
- **Product type:** a bank-internal credit decisioning capability (the CreditPulse engine plus its six-field Customer Credit Profile, CCP). It is distributed B2B2C through the HLB Connect app, merchant checkouts (API widget, hosted page, QR deep-link) and internal staff tools [Skill13 §3–4].
- **Geography and compliance scope:** Vietnam. Regulator: the State Bank of Vietnam (SBV). Data protection authority: the Ministry of Public Security's A05 department. AI regulator: Law 134/2025 authorities [`_context.md`; Skill02 §6].
- **Technical constraints:** none mandated by the brief. HLB-held data only in Phase 1, Open API in Phase 2, third-party data in Phase 3 (DD-09). The existing HLB Connect eKYC takes ~80 s [R67]. The CIC (national credit bureau) must be treated as a fallible, sensitive dependency [R71].
- **Criticality:** a credit decision that disburses money inside a live merchant checkout, and an AI system that the law classifies as high-risk (§1, RG-02). This file therefore goes **above the pipeline's "startup-grade" default**: 99.5% uptime, ASVS L1 and 1-hour MTTR are not adequate for a bank's 24/7 credit channel. Each place this happens is justified.

---

## 0. NFR Summary (engineer view)

| Area | Headline requirement | Anchor |
|---|---|---|
| Compliance | 17 instruments apply (§1). Hard launch gates:<br>- AI Law conformity assessment before deployment<br>- PDPL DPIA (the data-protection impact assessment) filed within 60 days of processing<br>- SBV notified 10 days before launch<br>- purpose-specific, verifiable consent for every CCP read | [N3][N6][N9] |
| Security | OWASP ASVS 5.0 **L3** for the decision, identity, consent and CCP paths; **L2** for everything else. TLS ≥ 1.2 (1.3 preferred). AES-256 at rest with HSM-held keys. Staff MFA. Annual penetration test plus a test before go-live | [N13][N10][N8] |
| Performance | Checkout **p50 ≤ 3 min / p90 ≤ 10 min** end-to-end (KR 1.2). System-side decision orchestration (excluding eKYC and CIC) **p95 ≤ 2 s**. Scoring API **p95 ≤ 200 ms / p99 ≤ 500 ms**. Widget LCP **≤ 2.5 s** at p75 | KR 1.2; [R67]; [N15][N16][N17] |
| Reliability | Decision path availability **99.9%/month** [Design]. Per-incident outage well inside the SBV Level-3 4-hour tolerance: **RTO ≤ 1 h, RPO ≤ 5 min**, and **RPO = 0** for the decision log and consent ledger. Degraded modes for CIC, model, feature-store and eKYC outages | [N8] Circular 09 Level 3 |
| Scalability | Volumes are **not in the brief**. Three parameterised tiers (launch, 6 months, 12 months) with a load-test gate at **3× forecast peak** `[DECISION REQUIRED]` | — |
| Accessibility | WCAG 2.1 AA on the HLB Connect hub, hosted checkout and officer workbench. Vietnamese first. Plain-language all-in cost disclosure | [N18]; [N19] |
| AI / model | 100% reason codes; fairness monitoring (G-5); PSI drift 0.10 / 0.25; champion fallback **≤ 5 min**; model documentation fit for AI-Law conformity; 100% override logging; data lineage | KR 4.1–4.3; G-5; G-8 |
| Retention | Accounting records ≥ 10 years; AML customer-ID records 5 years after closure; breach records ≥ 5 years. The credit-dossier-specific period is **not verified** `[DECISION REQUIRED]` | [N20][N21][N4] |

---

## 1. Compliance Requirements `[Rubric R2][Rubric R4]`

### 1.1 Applicable regulations (Vietnam, checked 30 Sep 2026)

| # | Instrument | Effective | Applies? Trigger | Key obligations for CreditPulse | Implementation (feature / NFR) | Verification | Confidence |
|---|---|---|---|---|---|---|---|
| RG-01 | **Personal Data Protection Law 91/2025/QH15** + **Decree 356/2025/ND-CP** (replace Decree 13/2023) | **1 Jan 2026** [N3][R46] | **Y.** The engine processes CASA transaction history, card data, CIC data and eKYC biometrics, all of which are personal data, and much of it sensitive | (a) Consent must be specific to each purpose, clear and verifiable. The consent record must be retained so its **timing and content can be proven**. Pre-ticked defaults and confusing wording are banned [N3].<br>(b) **Sensitive data** explicitly includes bank account information, card information and **financial transaction history**, as well as behaviour-tracking data [N3]. Biometrics are also sensitive.<br>(c) A **PDPIA** (personal-data impact assessment), plus a **TIA** (transfer impact assessment) if any data leaves Vietnam, must be filed with A05 **within 60 days** of starting processing. A05 reviews within 15 days; gaps must be fixed within 30 days. Updates are due every 6 months when purposes or processors change, and within 10 days after a reorganisation [N3][N4].<br>(d) **Breach notification.** A written or online notification to the competent authority. For location and **biometric** data, affected individuals must be told **within 72 hours** of discovery. Breach records must be kept **≥ 5 years** [N3][N4]. Skill 02 recorded a 72-hour notice for financial-service incidents [Skill02 RF-7, S35]; the exact authority-notification clock for non-biometric financial data **was not re-verified** here (OQ-18-02).<br>(e) Deletion requests must be completed within **20 days** (30 with third parties; one 20-day extension) [N3].<br>(f) **Buying or selling personal data is banned** [R46].<br>(g) A qualified **DPO** (data protection officer) is required [N3].<br>(h) Decree 356 has a dedicated section on *financial, banking and credit-information* activities; its article-level content **was not retrieved** [N4] (OQ-18-03) | E-01 consent ledger (per purpose SC/MK/RF, append-only, timestamped content hash); NFR-SEC-04/05; NFR-DATA-05 deletion workflow; NFR-OPS-03 breach runbook (72-hour clock); DPIA filed before go-live | DPIA acceptance by A05; consent-ledger audit (KR 4.1 = 100%); breach tabletop drill ≥ 1 per year | High (law and decree); Medium (sector article) |
| RG-02 | **Law on Artificial Intelligence 134/2025/QH15** + **Decree 142/2026** + **Decision 33/2026/QĐ-TTg** (list of 46 high-risk systems) | Law **1 Mar 2026**; Decision 33 **15 Aug 2026** [N5][N6] | **Y.** Decision 33 lists as high-risk "AI systems for **credit scoring and automated decisions on credit granting or loan disbursement without independent approval by credit officers**" [N6]. CreditPulse's INSTANT-APPROVE and PRE-APPROVED tiers are exactly that. Decision 33 also lists as high-risk systems that **automatically execute high-value electronic transactions without human control** [N6]; whether PoP disbursement at checkout falls under that second entry is unverified (OQ-18-04) | Before deployment: conformity assessment and registration in the national AI database. Continuous risk management, data quality and documentation, **operation logs**, **human oversight and intervention**, transparency, incident cooperation [N5][N6].<br>**Deadline correction.** The finance-sector grace period to **1 Sep 2027** covers only systems **already in operation before 15 Aug 2026**. Systems deployed in the six months after 15 Aug 2026 must comply by **1 Mar 2027** [N6]. CreditPulse is a new system, so the 1 Sep 2027 date in KR 4.2 and Skill02 RF-8 **does not apply** (OQ-18-05, upstream correction).<br>Whether banking systems need third-party certification or can self-assess sits in the Decision 33 appendix, which was not retrieved [N6] (OQ-18-04) | §7 AI/model NFRs; GV-01; OP-01 human override; decision log (NFR-AI-06); technical file (NFR-AI-05) | Conformity-assessment certificate or self-assessment record before go-live; AI-database registration receipt | High (law, list, dates); Low (pathway) |
| RG-03 | **Law on Cybersecurity 116/2025/QH15** (consolidates the 2018 Cybersecurity Law and the 2015 Network Information Security Law) | **1 Jul 2026** [N1] | **Y** (information-system operator) | Keeps the **data-localisation** provisions [N1]. Systems classified as critical to national security face mandatory cybersecurity assessments; CreditPulse is not expected to qualify, which is unverified [N1] | NFR-SEC-14 hosting in Vietnam | Legal sign-off on system classification | High (law); Medium (applicability) |
| RG-04 | **Decree 53/2022/ND-CP** (guiding the 2018 Cybersecurity Law; status under Law 116/2025 **not verified**) | 1 Oct 2022 [N2] | **Y** (as a domestic enterprise; HLB Vietnam is a Vietnamese legal entity) | Localisation of users' personal data, user-generated data and relationship data. One law-firm reading is that domestic enterprises must localise **on written request by A05**. Storage runs ≥ 24 months from the request, and system logs ≥ 12 months [N2]. A separate law-firm commentary is titled "Data localisation rules in need of further guidance" [N2b]; only its title was seen, not its text, so the correct reading of the domestic duty is treated as unsettled. The decree says nothing on copies held abroad | **Design choice:** host all CreditPulse personal data, CCPs, logs and models **in Vietnam** (NFR-SEC-14). This satisfies either reading and avoids a PDPL TIA. Any HLB Group (Malaysia) access for model risk review uses de-identified extracts under a TIA `[DECISION REQUIRED]` | Architecture review; data-flow map in the PDPIA | Medium |
| RG-05 | **SBV Circular 09/2020/TT-NHNN** (information-system security in banking) | 1 Jan 2021 [N7][N8] | **Y** | Five-level system classification. **Level 3 = a system serving 24/7 operations with a maximum 4-hour downtime tolerance.** Level 4 = data on ≥ 10 million customers, or critical payment systems [N8].<br>**MFA** is required for internal network access and for server or application access on Level ≥ 4 systems.<br>Admin accounts must be monitored and revoked when the task is done.<br>Outsourcing contracts must state the maximum service interruption and fix time, plus backup and disaster recovery.<br>An **annual incident-response drill** is required for Level ≥ 3 [N8].<br>No successor circular was found [N7] | Classify the decision path and CCP API as **≥ Level 3** (Level 4 if HLB's customer data base reaches ≥ 10M, `[DECISION REQUIRED]`); NFR-REL-01/02; NFR-SEC-07/08 | Level classification dossier; annual drill record | High |
| RG-06 | **SBV Circular 50/2024/TT-NHNN** (online-banking security) + **Circular 77/2025** (scope extension) + **Decision 2345/QĐ-NHNN** (biometric thresholds) | 50/2024: 1 Jan 2025. App and network provisions 1 Jul 2025; straight-through payment confirmation 1 Jan 2026; **authentication forms and reporting 1 Jul 2026** [N9]. 77/2025: 1 Mar 2026 [Skill02 S33]. Decision 2345: 1 Jul 2024 [Skill02 S32] | **Y.** The HLB Connect hub, the hosted checkout and disbursement are online-banking services | **Annual security assessment.** The **SBV must be notified 10 days before launching** a new online service, with the website/app, authentication solution and certificates [N9]. Authentication tiers apply. Biometrics are required for transfers > 10M VND per transaction or > 20M per day (Decision 2345) [Skill02 S32]. Whether Decision 2345 is superseded by 50/2024 is still **unverified** (OQ-02-09 carried forward). App-level technical controls (root/jailbreak detection, session rules) were **not retrieved** from the circular text [N9] | NFR-SEC-02/03 (biometric step-up for disbursement-grade actions, AP-02); launch-gate checklist | SBV pre-launch notice filed; annual assessment report | High (dates); Medium (specific controls) |
| RG-07 | **Circular 17/2024/TT-NHNN** (account opening and eKYC) + **Circular 18/2024** (cards), amended by **Circular 45/2025** | 17/2024: 1 Jul 2024 (individual biometrics 1 Jan 2025); 45/2025: **5 Jan 2026** [Skill02 S30][S31] | **Y.** AP-06 CASA-first opening at checkout; any card-based limit | eKYC must match against the chip-CCCD (citizen ID card) biometrics or VNeID (national e-ID). No electronic card transactions before biometric authentication [Skill02 RF-4] | AP-02 NFC chip-CCCD + liveness + face match; NFR-PERF-03 eKYC budget | eKYC vendor and regulatory test report | High |
| RG-08 | **Decree 69/2024/ND-CP** (electronic identification, VNeID) | 1 Jul 2024 [Skill02 RF-6] | **Y** (identity source) | VNeID level 2 as an identity and biometric source | AP-02 | Integration test | Medium |
| RG-09 | **SBV Circular 64/2024/TT-NHNN** (Open API) | 1 Mar 2025; existing connections compliant by **1 Mar 2027** [R73][N10] | **Y for Phase 2** (E-03, v1-Should) | Security (Annex 01/02): **HTTPS with TLS v1.2 or higher**; authentication and consent via **OAuth 2.0 (RFC 6749/6750/7009), OIDC, SAML 2.0**; encryption per TCVN 7816:2007, PKCS#1, ECC, JWE, TCVN 11367-2:2016 [N10]. Customers can view and withdraw consent. Default data-access window **180 days** [R73] | NFR-SEC-03/06; E-01 shows Open API consents alongside SC/MK/RF | Circular 64 conformance test with SBV/NAPAS profile | High |
| RG-10 | **Decree 58/2021/ND-CP** (credit information) + **SBV Circular 15/2023/TT-NHNN** (CIC operations) | Decree 58: 15 Aug 2021; Circular 15: **1 Jan 2025** [Skill02 S40][N22] | **Y.** PR-04 CIC reporting; S14 CIC pulls | Report the full CIC indicator set: customer identification, related parties, loans and credit, cards, collateral and more (9 groups) [N22]. The **reporting deadline or frequency was not verified** [N22] (OQ-18-06). CIC data is sensitive, and the Sep 2025 CIC breach [R71] argues for minimisation | PR-04; NFR-SEC-09 CIC minimisation; AP-07 fallback | CIC reporting reconciliation (daily automated check) | High (scope); Low (deadline) |
| RG-11 | **Law on Credit Institutions 32/2024/QH15** | 1 Jul 2024 [Skill02 RF-1] | **Y** | Rates and fees are agreed contractually, with no statutory APR cap for banks [Skill02 RF-1]. Customer-information confidentiality duties apply; the article-level text **was not re-verified** here | CO-02 disclosure; NFR-SEC-04 | Legal review | High (law); Medium (article detail) |
| RG-12 | **Circular 39/2016/TT-NHNN** (lending), as amended by 06/2023 and 12/2024, and **Circular 29/2026/TT-NHNN** | 29/2026: **15 Aug 2026** [Skill02 RF-2][S27] | **Y** | Electronic lending and automated appraisal are allowed. The small-value loan threshold is **400M VND**. Each lender sets its own per-customer electronic limit. For returning customers, the lender chooses the verification technology [Skill02 RF-2]. The **mandatory pre-contract disclosure fields** for electronic loans were **not verified** (OQ-18-07) | CO-02 all-in cost; OP-02 policy console holds the per-customer limit `[DECISION REQUIRED]` (OQ-02-16) | Legal review of CO-02 content against the circular | High (thresholds); Low (disclosure fields) |
| RG-13 | **Law on Electronic Transactions 20/2023/QH15** | **1 Jul 2024** [N23] | **Y.** e-contract with OTP signature at checkout | Covers electronic transactions and contracts in all sectors; their substance is governed by the sector laws [N23] | E-contract service: tamper-evident storage, time-stamping, signature evidence (NFR-SEC-12) | Legal review; integrity test | High |
| RG-14 | **Law on Anti-Money Laundering 14/2022/QH15** | 1 Mar 2023 [N21] | **Y.** Customer due diligence at CASA-first opening and loan booking | Keep customer-identification records **5 years** from the end of the transaction, the account closure or the report date (Art. 38) [N21] | NFR-DATA retention table | Retention-policy audit | High |
| RG-15 | **Law on Accounting 88/2015/QH13** + **Decree 174/2016** | 2017 [N20] | **Y** (loan-ledger accounting records) | Accounting documents used for the books and financial statements: **≥ 10 years** [N20] | NFR-DATA retention table | Retention-policy audit | High |
| RG-16 | **Decree 94/2025/ND-CP** (fintech sandbox) | 1 Jul 2025 [R74] | **Only if** Phase 3 alternative data or third-party scoring is pursued (PL-01/PL-02) | Up to 2-year supervised pilot within a certified scope | Not in v1 scope | — | High (decree); Low (applicability, OQ-02-17) |
| RG-17 | **Circular 26/2020/TT-BTTTT** (ICT accessibility for persons with disabilities) | 1 Jan 2021 [N19] | **Encouraged, not mandatory** for HLB. Mandatory only for state agencies, press agencies and public-service providers (Art. 5) [N19] | The referenced standards sit in an untranslated appendix [N19]. **No binding accessibility standard for bank apps was found** | HLB adopts **WCAG 2.1 AA** voluntarily (§3) | §3 audit | Medium |
| — | GDPR / CCPA | — | **N** | No EU or California users are targeted. Replaced by RG-01 | — | — | — |
| — | BNPL-specific rulebook | — | **None exists** [Skill02 RF-12] | Monitor | — | — | Medium |

### 1.2 Compliance checklist (launch gates)

| # | Requirement | Regulation | Gate | Status | Owner | Implementation notes |
|---|---|---|---|---|---|---|
| CG-01 | Privacy notice (Vietnamese) naming each purpose (SC scoring / MK marketing / RF referral / Open API), data sources, retention, rights | RG-01 | Before launch | Not Started | DPO | Shown in the "Privacy & consent" hub and at checkout step 1 |
| CG-02 | Per-purpose, verifiable, un-pre-ticked consent capture; withdrawal as easy as granting | RG-01, RG-09 | Before launch | Not Started | Product + DPO | E-01; KR 4.1 |
| CG-03 | Rights of access, correction and deletion; deletion within 20 days (30 with third parties) | RG-01 | Before launch | Not Started | DPO + Eng | Legal-hold override where AML or accounting retention applies |
| CG-04 | PDPIA filed with A05 (and a TIA if any cross-border flow) within 60 days of processing; re-filed on purpose or processor change | RG-01 | ≤ 60 days after processing starts; **recommended before go-live** | Not Started | DPO | Include CIC, eKYC biometrics and the Sổ Bán Hàng flows |
| CG-05 | Breach runbook: 72-hour clock, A05 portal notification, data-subject notice (biometric), 5-year record | RG-01 | Before launch | Not Started | CISO | NFR-OPS-03 |
| CG-06 | AI conformity assessment + national AI database registration + technical file + human-oversight procedure | RG-02 | **Before deployment; ≤ 1 Mar 2027 at the latest** | Not Started | Model Risk + Legal | Supersedes KR 4.2's 1 Sep 2027 (OQ-18-05) |
| CG-07 | System classification dossier (≥ Level 3) and annual incident drill | RG-05 | Before launch; annually | Not Started | CISO | — |
| CG-08 | SBV notification 10 days before launch of the online service | RG-06 | T-10 days | Not Started | Compliance | Include the authentication solution and certificates |
| CG-09 | Annual online-banking security assessment | RG-06 | Annually | Not Started | CISO | Combine with the NFR-SEC-10 penetration test |
| CG-10 | eKYC per Circular 17/2024 / 45/2025 (chip-CCCD / VNeID biometric match) | RG-07 | Before launch | In Progress (HLB has an existing integration [R67]) | Digital channels | Re-use the existing integration |
| CG-11 | CIC reporting of PoP loans in the Circular 15/2023 indicator set | RG-10 | From the first booking | Not Started | Credit ops | Frequency to confirm (OQ-18-06) |
| CG-12 | CO-02 disclosure reviewed against Circular 39 / 29/2026 | RG-12 | Before launch | Not Started | Legal | OQ-18-07 |
| CG-13 | E-contract evidence (signature, timestamp, integrity) retained | RG-13 | Before launch | Not Started | Eng | NFR-SEC-12 |
| CG-14 | Data residency: all CreditPulse personal data in Vietnam | RG-03/04 | Before launch | Not Started | Infra | NFR-SEC-14 |
| CG-15 | Qualified DPO appointed | RG-01 | Before launch | Unknown (HLB-level) | HR / Compliance | — |

### 1.3 Data processing agreements needed

| Third party | Data shared | Role | Agreement |
|---|---|---|---|
| CIC | Identity + credit data (pull and report) | Statutory credit-information relationship | Under Decree 58 / Circular 15 (no DPA needed; minimise retention) |
| National ID Registry / VNeID (Ministry of Public Security, C06 department) | CCCD chip data, face match | Statutory verification | Existing HLB integration [R67] |
| eKYC / liveness vendor (if any) | Biometric images | Processor | **DPA required**; Vietnam hosting; no retention by the vendor `[DECISION REQUIRED — vendor unknown]` |
| Sổ Bán Hàng | Merchant sales ledger | Joint or separate controller (merchant consent) | Data-sharing agreement + PDPL consent chain [R66] |
| Merchant/partner checkouts (MX-01) | Basket, order ID; **no CCP fields** beyond the decision tier and packages | Separate controller | Merchant agreement: purpose limitation, no resale (data-trading ban) |
| Other banks via Open API (Phase 2) | Consented transaction history | Circular 64 data provider | Circular 64 bilateral agreement |
| SMS/OTP gateway | Phone number, OTP | Processor | DPA; Vietnam hosting |
| Cloud / hosting provider (if any) | All | Processor / outsourced service | Circular 09 outsourcing contract terms (maximum interruption, backup and DR) [N8] |

### 1.4 Open compliance risks

| Risk | Impact | Mitigation | Owner |
|---|---|---|---|
| AI-Law deadline is 1 Mar 2027, not 1 Sep 2027, for a new system (RG-02) | The conformity work moves 6 months earlier, and deployment may be blocked without it | Start the technical file in build sprint 1; decide the pathway (OQ-18-04) | Model Risk |
| INSTANT-APPROVE without officer approval makes the engine high-risk. Routing everything through officers would defeat R-2 | Classification is unavoidable | Accept high-risk status and comply fully; the refer band provides human oversight for uncertain cases | Legal |
| Decree 356's financial-sector article is unread (OQ-18-03) | Unknown extra obligations on credit-scoring disclosure | Legal review of the decree text before the PDPIA | DPO |
| Data-localisation reading contested (RG-04) | Architecture rework if Group-level processing is planned | Vietnam-only hosting by default | Infra |

---

## 2. Security Requirements `[Rubric R3]`

### 2.1 Why ASVS L3/L2, not the template's L1
OWASP ASVS 5.0.0 (May 2025) defines three levels [N13]:
- **L1**: the minimum baseline for internet-facing apps.
- **L2**: for applications that handle sensitive personal or business data.
- **L3**: for critical applications that perform high-value transactions, such as banking.

CreditPulse **grants credit and triggers disbursement**, processes data the PDPL classes as sensitive (financial history, biometrics [N3]), and is an AI-Law high-risk system (RG-02). So:

| Tier | Components | ASVS level |
|---|---|---|
| **Critical** | Checkout Decision service (AP-01), Policy and Decision service (E-07), scoring API, CCP store and API (E-05), consent service (E-01), eKYC orchestration (AP-02), e-contract and loan booking, merchant API gateway (MX-01), officer-workbench **decision and override** functions (OP-01) | **L3** |
| **Standard** | Campaign workspace (AW-01/02), recommendations (RT-01), referral ledger (AD-01), risk console read-only views (GV-01), analytics | **L2** |
| **Mobile** | HLB Connect "Pay Later" hub modules | **OWASP MASVS** (Mobile Application Security Verification Standard) L2 + the RESILIENCE group [N14] |
| **APIs** | All externally exposed APIs | **OWASP API Security Top 10 (2023)** [N14b] |

A startup-grade L1 is rejected: it covers black-box testing only [N13], while SBV Circular 09 and 50 require assessed, documented controls (RG-05/06).

### 2.2 Security requirement table

| ID | Requirement | Standard | Target | Verification method |
|---|---|---|---|---|
| NFR-SEC-01 | **Application security baseline** | OWASP ASVS 5.0 [N13] | 100% of applicable L3 requirements (Critical tier) and L2 (Standard tier) pass; any exception has a risk acceptance signed by the CISO | Third-party ASVS assessment before go-live; ASVS checklist attached to each release |
| NFR-SEC-02 | **Customer authentication** | Circular 50/2024 authentication tiers [N9]; Decision 2345 biometric thresholds [Skill02 S32]; Circular 17/2024 eKYC (RG-07) | HLB Connect login plus a **biometric face match against the chip-CCCD/VNeID** for every disbursement-grade action: loan acceptance, limit increase acceptance (PR-03), change of disbursement account. Lane B uses NFC CCCD read + liveness + face match. Lane A e-contract signature uses OTP **plus** biometric. Liveness detection resists presentation attacks (photo, screen replay, mask) | Regulatory conformance test; pen test with presentation-attack cases; audit of the auth logs per action type |
| NFR-SEC-03 | **Merchant / partner API authentication** | Circular 64 profile (OAuth 2.0 RFC 6749/6750/7009, OIDC) [N10]; OWASP API Top 10 [N14b] | Mutual TLS + OAuth 2.0 client credentials with **signed requests** (JWS) and replay protection (nonce + timestamp ≤ 5 min skew). Per-merchant rate limits. Merchants receive **only** the decision tier, packages and order status, never CCP fields (purpose limitation) | API security test (BOLA/BFLA cases); contract test that the merchant payload excludes CCP fields |
| NFR-SEC-04 | **Data encryption at rest** | NIST SP 800-57 key management [N24]; FIPS 140-3 [N25] | AES-256 for all stores holding personal data (CCP store, feature store, decision log, consent ledger, case files, backups). **Field-level encryption** for sensitive fields: CCCD number, biometric templates, CIC payload, account numbers | Configuration audit; attempt to read a raw volume or backup without keys (must fail) |
| NFR-SEC-05 | **Data encryption in transit** | Circular 64 Annex: TLS ≥ 1.2 [N10] | **TLS 1.3 preferred, TLS 1.2 minimum** with forward-secret AEAD suites only, on all external **and internal** service-to-service traffic (mTLS in the service mesh). HSTS on web surfaces. Certificate pinning in HLB Connect | Automated TLS scan (0 findings below grade A); service-mesh policy check |
| NFR-SEC-06 | **Key management** | FIPS 140-3 Level 3 HSM [N25]; NIST SP 800-57 [N24] | Master keys and signing keys (e-contract, JWS, decision-log hash chain) live in an **HSM** located in Vietnam; envelope encryption for data keys; rotation ≤ 12 months (data keys) and on staff departure (admin credentials); dual control for key ceremonies | Key-ceremony record; HSM certificate; rotation log audit |
| NFR-SEC-07 | **Staff authentication and access** | Circular 09/2020: MFA for internal network access and for Level ≥ 4 server access; admin account monitoring [N8] | **MFA for 100% of staff** (officers, risk, campaign, engineers) on every internal CreditPulse tool. RBAC with least privilege: officers see **only cases assigned in the refer queue**; the campaign team sees **only** the MK-consented target list, never raw CASA transactions. Just-in-time admin access, revoked automatically when the task ends | Quarterly access recertification; IAM audit; pen test of privilege escalation |
| NFR-SEC-08 | **Audit logging of access** | Circular 09/2020 [N8]; AI Law logging (RG-02) | Every CCP read, record the caller, purpose (SC/MK/RF), consent-ledger reference, timestamp and fields returned. Every staff view of a customer case is logged. Logs are **append-only and tamper-evident** (hash chain), synchronised to a Vietnam SIEM (security monitoring platform) in ≤ 5 min | Daily automated reconciliation: CCP reads × consent ledger = 100% match (KR 4.1); tamper test |
| NFR-SEC-09 | **PII minimisation, especially CIC data** | PDPL purpose limitation [N3]; lesson of the Sep 2025 CIC breach [R71] | Store **derived CIC features** (counts, delinquency flags, total obligation), not the raw CIC report, beyond the decision window. The raw CIC payload is encrypted and purged after `[DECISION REQUIRED]` days (proposed 30 days, which matches the AP-07 cache window; A-18-1). Gender, age band and region are held in a **separate fairness-monitoring store**, never joined into model features (Skill 13 §6). Contacts, gallery and social data are never collected (X-01) | Data-inventory audit against the Skill 19 PII register; automated purge job test |
| NFR-SEC-10 | **Penetration testing** | Circular 50/2024 annual security assessment [N9]; ASVS [N13] | Independent pen test (web, mobile, API, and the **model endpoint**) **before go-live**, **annually**, and after any major change to the Critical tier. 0 open Critical/High findings at go-live; High findings fixed in ≤ 30 days, Medium in ≤ 90 days | Pen-test report + remediation tracker |
| NFR-SEC-11 | **Dependency, secrets and supply chain** | OWASP ASVS V15 / SSDF [N13] | SCA (software composition analysis) and SAST (static analysis) in CI; **0 known Critical CVEs** in deployed images; secrets only in a vault (0 secrets in code, which CI secret scanning blocks); signed container images; SBOM per release | CI gate reports; quarterly SBOM review |
| NFR-SEC-12 | **E-contract integrity** | Law on E-Transactions 2023 [N23] | Each signed contract is stored with the signature evidence (OTP event + biometric match ID), the exact contract version shown (hash), a trusted timestamp, and the disclosed all-in cost (CO-02 snapshot) | Integrity test: altering any byte breaks the hash; legal review |
| NFR-SEC-13 | **Fraud and abuse controls** | R-3 [Brief p.6]; G-2 | F5 integrity checks on every application and draw: device fingerprint, identity-velocity (the same CCCD, phone or device across applications), CIC identity match, KYC-change recency (S11). **Referral abuse:** self-referral and duplicate device or identity block rewards (Skill 13 Flow 6). **Eligibility-check probing:** ≤ `[DECISION REQUIRED]` instant eligibility checks per identity or device per 24 h (proposed 3; A-18-2), to stop limit-fishing and model extraction | Fraud red-team scenarios before go-live; G-2 monitoring |
| NFR-SEC-14 | **Data residency** | Cybersecurity Law 116/2025 localisation [N1]; Decree 53/2022 [N2]; PDPL TIA [N3] | 100% of CreditPulse personal data, CCPs, logs, models, backups and DR copies hosted **in Vietnam**. Any cross-border access (for example HLB Group model review) only on **de-identified** extracts under a filed TIA `[DECISION REQUIRED]` | Architecture review; cloud-region configuration audit; PDPIA data-flow map |
| NFR-SEC-15 | **Model security** (adversarial behaviour and gaming) | Lucas critique [L1]; lit. review X7; OWASP ML Security Top 10 [N14c] | (a) **Anti-gaming.** No single cheap-to-change feature (device type, OS, email host, time of day) may move a decision across a tier on its own. Checkout-footprint features (S13) are capped in contribution, and each is monitored for PSI shift after launch (a gaming signal).<br>(b) **Reason codes are category-level** ("affordability", "limited history"), not thresholds, so they are transparent without being a gaming recipe.<br>(c) **Training-data integrity:** lineage and hash of each training set, with outcome labels drawn only from the loan ledger (no customer-supplied labels).<br>(d) **Extraction resistance:** the scoring API is internal only (never exposed to merchants), plus the NFR-SEC-13 rate limits | Pre-release adversarial test (perturb S13 features; confirm tier stability); monthly feature-PSI report; ML-security review in the pen test |
| NFR-SEC-16 | **Input validation and output encoding** | OWASP Top 10 / ASVS V1–V2 [N13] | All inputs validated by schema at the API gateway; parameterised queries only; merchant-supplied basket data treated as untrusted | SAST + DAST (dynamic analysis) in CI; pen test |
| NFR-SEC-17 | **Session management** | ASVS V7 [N13] | Hosted checkout session ≤ **30 min** (matching the Skill 13 package save, A-13-6), bound to a merchant order ID. HLB Connect idle timeout follows HLB's existing policy `[DECISION REQUIRED — not public]`. Tokens rotate on privilege change | Security review; automated session tests |

---

## 3. Accessibility Requirements `[Rubric R3]`

**Legal position.** No binding accessibility standard for bank apps or websites was found in Vietnam. Circular 26/2020/TT-BTTTT *requires* accessible websites only from state agencies, press agencies and public-service providers, and *encourages* other organisations [N19]. HLB therefore adopts **WCAG 2.1 Level AA** [N18] voluntarily. Two reasons:
- The thin-file segments include first-time digital borrowers (FTB, 14.5% of inbound [Brief p.9]).
- Cost disclosure is only fair if people can actually read it [Brief p.4].

| ID | Requirement | Standard | Scope | Target | Verification method |
|---|---|---|---|---|---|
| NFR-A11Y-01 | WCAG conformance | WCAG 2.1 AA [N18] | HLB Connect "Pay Later" hub; hosted checkout and embedded widget (MX-01 options A/B); QR deep-link landing; officer workbench (OP-01); risk console and campaign workspace | 0 AA failures in automated scans; 0 Critical/Serious failures in the manual audit | axe-core in CI; manual audit by an accessibility specialist before go-live and annually |
| NFR-A11Y-02 | Keyboard and switch navigation | WCAG 2.1.1, 2.4.3, 2.4.7 | All interactive elements on web surfaces, including the officer workbench (officers work at speed with the keyboard) | 100% operable without a mouse; visible focus indicator on every element | Manual keyboard walkthrough of the core flows |
| NFR-A11Y-03 | Screen-reader compatibility | WCAG 4.1.2, 1.3.1 | Core flows: Consideration → eKYC → decision → e-contract; "Why this decision"; "Privacy & consent" | Flows completable with TalkBack (Android) and VoiceOver (iOS) in Vietnamese | Assistive-technology test script per release |
| NFR-A11Y-04 | Colour contrast and non-colour cues | WCAG 1.4.3, 1.4.11, 1.4.1 | All screens | Text ≥ 4.5:1 (large text ≥ 3:1); UI components ≥ 3:1; decision tiers and risk states never shown by colour alone | Automated contrast check; design review |
| NFR-A11Y-05 | Resize and reflow (mobile-first) | WCAG 1.4.4, 1.4.10 | Widget and hosted page | Usable at 200% text size and 320 px width with no horizontal scroll | Responsive test matrix |
| NFR-A11Y-06 | Timing adjustable | WCAG 2.2.1 | 30-minute package save; OTP entry; eKYC steps | Warning before timeout, with a one-tap extend; OTP resend without losing state | Functional test |
| NFR-A11Y-07 | eKYC accessibility | WCAG 1.1.1, 3.3.2 | NFC scan, liveness, face match (AP-02) | Voice and text guidance for card placement and liveness; retry count visible. Because the eKYC-fallback flows are parked (PL-04), after 3 failed attempts the customer is routed to REFER with an "identity review" reason, never a dead end (A-13-8) | Usability test that includes low-vision users |
| NFR-A11Y-08 | **Language** | WCAG 3.1.1 | All customer surfaces | **Vietnamese is the default** (`lang="vi"`), with English optional. All legal and cost text is available in Vietnamese | Content QA |
| NFR-A11Y-09 | **Plain-language cost disclosure** | WCAG 3.1.5 (AAA reading level, used as guidance); responsible lending [Brief p.4]; the CFPB enforcement lesson on 0% instalment messaging [R41] | CO-02 package screen; e-contract summary; reason codes (E-09) | For each package, one screen shows: monthly instalment, number of payments and dates, **total amount payable**, all-in APR, fees, and late-payment consequences, in short sentences with no unexplained jargon. "0%" is always shown next to the total payable. Reason codes are in plain Vietnamese | Comprehension test with ≥ 5 users per segment before launch: ≥ 80% correctly state the total payable (A-18-3); legal review (OQ-18-07) |

---

## 4. Performance Requirements `[Rubric R3]`

### 4.1 Budget derivation from the checkout target (KR 1.2)

KR 1.2 sets **p50 ≤ 3 min and p90 ≤ 10 min** (A-10-3, anchored on Home Credit and Techcombank at ~3 min and ACB at 5–10 min) [Skill10]. Skill 10 defines KR 1.2 as *application submit → auto-decision*, while Skill 13 applies it to the *whole checkout*. This file budgets **both**: the stricter end-to-end view, and the system-only submit → decision segment. OQ-18-08 asks which one is the KPI.

**Worst case = Lane B (new-to-bank, eKYC required).**

| # | Journey segment | Controlled by | p50 budget | p90 budget | Basis |
|---|---|---|---|---|---|
| J1 | Merchant page → widget or hosted page interactive | System | LCP ≤ 2.5 s | — (CWV is measured at p75: LCP ≤ 2.5 s) | Core Web Vitals "good" threshold [N15] |
| J2 | Indicative packages rendered (CO-01) | System | ≤ 0.5 s | ≤ 1 s | [Design] A-18-4 |
| J3 | Customer reads packages, enters phone, consents | **Customer** | remainder | remainder | Not controllable; measured |
| J4 | eKYC: NFC chip-CCCD read + liveness + face match (AP-02) | System + customer | **≤ 80 s** | ≤ 180 s | HLB's existing eKYC ~80 s [R67]; p90 is [Design] A-18-5 (allows 1 retry) |
| J5 | **Submit → decision** (CIC pull, online features, scoring, policy, packages) | System | ≤ 8 s | ≤ 15 s | §4.2 component budgets |
| J6 | e-contract display + OTP delivery + signature | System + customer | ≤ 20 s (system: OTP delivered ≤ 10 s) | ≤ 45 s (OTP ≤ 30 s) | [Design] A-18-6 |
| J7 | Loan booking + merchant confirmation | System | ≤ 3 s | ≤ 5 s | [Design] |
| | **System-controlled total** (J1+J2+J4+J5+J6+J7) | | **≈ 115 s** | **≈ 250 s** | |
| | **Left for customer time (J3) within KR 1.2** | | **≈ 65 s of 180 s** | **≈ 350 s of 600 s** | Feasible, but the p50 is tight. The eKYC step dominates (J4 ≈ 70% of the system budget) |

**Lane A (pre-approved)** skips J4. It replaces J4 with a biometric face match against the CCCD on file (≤ 10 s p50 / ≤ 20 s p90, [Design] A-18-7), plus F5/F2 re-validation and a CIC stacking check (inside J5). Its system-controlled total is ≈ 45 s at p50, which comfortably meets KR 1.2.

**Implication `[Rubric R2]`.** The engine itself (J5) takes **< 5%** of the end-to-end budget. The checkout-time risk sits in **eKYC and customer time**, not in the AI. This supports DD-14: pre-approving Cohort A removes J4 altogether.

### 4.2 Component performance requirements

| ID | Metric | Target | Measurement | Benchmark source |
|---|---|---|---|---|
| NFR-PERF-01 | End-to-end checkout time (J1 → J7), Lane B | p50 ≤ 3 min; p90 ≤ 10 min | Decision-engine event log: `checkout_start` → `merchant_confirmed` timestamps; KR 1.2 dashboard; alert if p90 > 10 min for 1 hour [Skill10 §4] | KR 1.2 / A-10-3 [Skill02 S9][R77] |
| NFR-PERF-02 | Submit → decision (J5) | p50 ≤ 8 s; p95 ≤ 15 s; p99 ≤ 20 s. The **hard timeout on the CIC call is 15 s**; after that, AP-07 fallback rules apply | Distributed tracing (APM) spans per stage; synthetic probe every 1 min | [Design] A-18-8. No public CIC response-time SLA was found (OQ-18-09) |
| NFR-PERF-03 | eKYC completion (J4) | p50 ≤ 80 s; p90 ≤ 180 s; C06/VNeID verification call p95 ≤ `[DECISION REQUIRED — the provider's SLA is not public]` | eKYC step events in the app | HLB ~80 s [R67] |
| NFR-PERF-04 | Decision orchestration **excluding** CIC and eKYC (feature fetch + scoring + policy + package finalise + decision-log write) | p95 ≤ 2 s; p99 ≤ 3 s | APM | [Design]; consistent with "underwriting in under a second" real-time patterns (vendor, Low) [D4] |
| NFR-PERF-05 | **Scoring API** (all models for one application, served behind the single scoring API) | **p95 ≤ 200 ms; p99 ≤ 500 ms** at the NFR-SCAL peak load | APM on the scoring service; load-test report | A production fintech risk-scoring engine reports sub-200 ms p95 (vendor case study, Low) [N16]; real-time payment fraud pipelines budget 50–200 ms end to end for features + inference + decision (vendor, Low) [N17] |
| NFR-PERF-06 | Online feature-store read (checkout features) | p95 ≤ 50 ms; p99 ≤ 100 ms | Feature-store metrics | [Design], inside the NFR-PERF-04 budget; online/offline parity pattern [D4][D5] (vendor, Low) |
| NFR-PERF-07 | Policy and decision service (E-07) | p95 ≤ 100 ms | APM | [Design]; deterministic rules |
| NFR-PERF-08 | **CCP read API** (E-05; stage services and HLB Connect) | **p95 ≤ 100 ms; p99 ≤ 300 ms**, including the consent-purpose check | APM | [Design], derived from the J5 budget and the HLB Connect hub load (NFR-PERF-10) |
| NFR-PERF-09 | Package build (CO-01) | Indicative p95 ≤ 1 s; final (post-decision) p95 ≤ 300 ms | APM | [Design] A-18-4 |
| NFR-PERF-10 | Merchant widget / hosted page / HLB Connect hub web views | **LCP ≤ 2.5 s, INP ≤ 200 ms, CLS ≤ 0.1** at the 75th percentile of real sessions on mobile; widget JS bundle ≤ 100 KB gzipped (A-18-9) so it doesn't slow merchant pages | RUM (real-user monitoring) with the web-vitals library; Lighthouse CI on each release | Core Web Vitals thresholds [N15] |
| NFR-PERF-11 | Nightly Cohort A pre-approval batch (DF-1) | All eligible CCPs refreshed within a **≤ 4 h** window, finishing before 06:00 ICT | Batch scheduler metrics | [Design] A-18-10 |
| NFR-PERF-12 | Repayment re-score (DF-3) and suppression propagation | Repayment event → CCP v(n+1) and early-warning suppression flags live **p95 ≤ 15 min** | Stream lag metrics | [Design]. A missed suppression means a marketing contact to a flagged customer (G-6) |
| NFR-PERF-13 | Officer workbench case view (OP-01) | p95 ≤ 2 s to load a pre-assembled case | RUM | [Design]; supports the KR 1.4 2-business-hour SLA |
| NFR-PERF-14 | Error rate on the decision path | 5xx responses < 0.1% of requests per month. Any internal failure returns a **graceful REFER, never an error screen** (§5.3) | APM error tracker | Template default [Skill 18 SKILL.md]; [Design] |

---

## 5. Reliability Requirements `[Rubric R3]`

### 5.1 Availability tiers and justification
- **Why 99.9% and not the template's 99.5%.** SBV Circular 09/2020 classes a system that serves **24/7 operations with a maximum tolerable downtime of 4 hours** as **Level 3** [N8]. The checkout decision path is 24/7: merchants sell at night and during Tết and sales events. At 99.5%, ~3.6 h of downtime per month is allowed, close to the Level-3 tolerance for a single incident, and every minute of downtime is a lost purchase moment (R-2).
- **The proposal.** **99.9% per month (≤ 43.8 min)** for the decision path [Design] A-18-11. HLB Connect's own availability target is not public. HLB must confirm this tier sits at or below HLB Connect's SLO, because the hub cannot be more available than the app it lives in `[DECISION REQUIRED]` (OQ-18-11).

| ID | Requirement | Target | Measurement |
|---|---|---|---|
| NFR-REL-01 | Availability: **checkout decision path** (MX-01 gateway → Checkout Decision → CCP API → scoring → policy → decision log) | **99.9% monthly**, measured by synthetic end-to-end transactions every 1 min from ≥ 2 Vietnamese networks. Planned maintenance (announced ≥ 7 days ahead, 01:00–04:00 ICT) is excluded, capped at 4 h per month | Synthetic monitoring + status page; monthly SLO report |
| NFR-REL-02 | Availability: **consent service (E-01)** | **99.95%** monthly. The consent service **fails closed** (no consent check → no CCP read, NFR-REL-08), so it must be more available than the path that depends on it | Synthetic monitoring |
| NFR-REL-03 | Availability: HLB Connect hub modules, officer workbench, risk console, campaign workspace | Hub: 99.9% (inherits the app). Workbench: **99.5% during refer-queue operating hours** `[DECISION REQUIRED — hours not set; KR 1.4 counts "business hours"]`. Console and campaign: 99.0% | Synthetic + RUM |
| NFR-REL-04 | **RTO** (recovery time objective) | Decision path and consent service **≤ 1 h**, a quarter of the Circular 09 Level-3 4-hour tolerance [N8]. Batch and analytics ≤ 24 h | Annual DR drill (Circular 09 requires annual incident drills for Level ≥ 3 [N8]); drill report |
| NFR-REL-05 | **RPO** (recovery point objective) | **0** (synchronous replication across two Vietnam sites or availability zones) for the decision log, consent ledger, e-contracts and loan-booking handoff. **≤ 5 min** for the CCP store and feature store (rebuildable from source events). ≤ 24 h for analytics | DR drill: measured data loss |
| NFR-REL-06 | **MTTD / MTTR** (mean time to detect / recover), Sev-1 = decision path down or wrong decisions | MTTD ≤ 5 min; **MTTR ≤ 1 h**; 24/7 on-call for the Critical tier | Incident log; monthly review |
| NFR-REL-07 | **Rollback** | Application release: ≤ 15 min (blue/green or canary with automated rollback on SLO breach). **Model:** ≤ 5 min to the previous champion (a model-registry pointer switch). **Policy parameters (tier thresholds, DSR cap, starter limit):** ≤ 5 min to the previous version. Every change is versioned and logged | Rollback drill per quarter; deployment runbook |
| NFR-REL-08 | **Fail-safe direction** | When in doubt the engine **refers, never auto-approves**. No decision is issued if the decision log cannot be written (AI-Law logging, RG-02). No CCP read happens without a consent check | Chaos tests (§5.2) |
| NFR-REL-09 | Backup | Point-in-time recovery for transactional stores; daily full backup; encrypted; **stored in Vietnam**; restore test **quarterly** | Restore test record |

### 5.2 Degraded modes (graceful degradation per dependency)

| Dependency failure | Detection | Engine behaviour | Customer / merchant sees | Exit |
|---|---|---|---|---|
| **CIC unavailable or > 15 s** (FM-12; Sep 2025 incident [R71]) | Circuit breaker on the CIC client | **AP-07.** Lane A with F3 = BUREAU+BANK and a cached CIC pull < 30 days old: decide on the cache. Otherwise REFER, or a lower-capped starter limit `[DECISION REQUIRED]` (Skill 13 Flow 3). Every such decision is flagged `cic_fallback=true`, and a CIC re-check runs within 24 h of recovery; any stacking breach is routed to credit ops | "We need a little longer" (REFER) or a normal decision | Circuit closes after 5 consecutive healthy probes |
| **Challenger model down or erroring** (> 1% errors over 5 min, or p99 > 500 ms for 10 min) | Scoring-service health | Automatic switch to the **champion scorecard** for 100% of traffic within **≤ 5 min**; experiment assignment paused (KR 2.1 split flagged) | No visible change | Manual re-enable after root cause |
| **Champion also down** | Health | No F1 → the policy layer returns **REFER** for all new applications; pre-approved limits paused | REFER message | Restore the model service |
| **Drift: PSI > 0.25 confirmed** (G-8) | Weekly monitoring job | Fallback to the champion; graduation offers from the drifting model paused (Skill 13 Flow 4) | No visible change | Retrain and revalidate (NFR-AI-08) |
| **Online feature store down** | Health / latency | Lane A: decide on the last CCP snapshot if ≤ 24 h old (policy rules re-applied to F2/F5 with live CIC). Lane B: REFER | Normal decision or REFER | Store recovery |
| **eKYC / C06 / VNeID down** | eKYC health | Lane B cannot proceed: offer "save and resume" (30 min) or REFER to identity review. Lane A: no disbursement-grade action without biometrics (RG-06) → "try again shortly" | Clear retry message; the merchant can switch to another payment method | Provider recovery |
| **Consent service down** | Health | **Fail closed**: no CCP reads, no campaigns sent. The checkout hides "Pay over time" | The option is temporarily unavailable at the merchant | Service recovery |
| **Decision-log write fails** | Write acknowledgement | **Fail closed**: the case moves to the REFER queue with an automatic retry | REFER message | Log store recovery |
| **Core banking booking API down** (A-13-10) | Health | Approval held, e-contract not issued; retries for ≤ 30 min, then the approval is cancelled and the customer notified | "Approved — finalising"; the merchant is not confirmed until booked | Recovery |
| **SMS OTP gateway down** | Delivery-rate monitor | Fail over to a secondary SMS gateway or an in-app push OTP | OTP arrives via the alternative channel | — |
| **Sổ Bán Hàng feed stale > 48 h** | Freshness monitor | S5 features marked stale; F3 downgraded one level for MSME applicants | Possibly a smaller limit | Feed recovery |

---

## 6. Scalability Requirements `[Rubric R4]`

**HLB volumes are not in the brief.** The brief gives the segment *mix* (SAL-C 35.5%, SAL-N 20.5%, GIG 15%, MSME 14.5%, FTB 14.5%) and the ticket and tenor ranges [Brief p.9], but **no application counts**. The same gap blocks the North Star absolute target (OQ-10-01) and SOM (OQ-02-04). No volume is invented here. Capacity is specified as **parameters** that HLB sets `[DECISION REQUIRED]` (OQ-18-10). The architecture must meet the rules below for whatever values are chosen.

**Parameters:**

| Symbol | Meaning |
|---|---|
| **V** | Checkout applications per day |
| **P** | Peak-hour share of daily volume (for example, evening e-commerce peaks) |
| **E** | Event multiplier for sales days and Tết (for example, 11.11 and 12.12) |
| **N_A** | Cohort A customers profiled nightly |
| **M** | Live merchant integrations |

**Derived peak load:** peak requests per second = V × P × E / 3600 × (API calls per application).

**Capacity rules:**
- **NFR-SCAL-01.** Load-test the Critical tier at **3× the forecast event peak** while holding NFR-PERF-02/05/08 [Design] A-18-12.
- **NFR-SCAL-02.** Scaling is horizontal and needs no code change: every Critical-tier service is stateless behind autoscaling, and state lives in partitioned stores.
- **NFR-SCAL-03.** The nightly batch (NFR-PERF-11) finishes within 4 h at **2× N_A**.

| Scenario | Load (parameterised) | Architecture approach |
|---|---|---|
| **Launch (pilot)** | V₀ applications/day `[DECISION REQUIRED]`; M₀ pilot merchants (Skill 08 pilot scope); N_A,0 Cohort A customers with SC consent `[DECISION REQUIRED]` | Single Vietnam region, **two sites/AZs** (NFR-REL-05). Stateless services with ≥ 2 replicas per site and autoscaling. Managed online feature store. CCP store as a key-value store partitioned by customer ID. The CIC client has a connection pool and a 30-day cache (AP-07). Nightly batch on shared compute |
| **6 months** | V₆ = V₀ × growth factor g₆ `[DECISION REQUIRED]`; first sales-event peak E | Autoscaling tuned from observed P and E. Read replicas for the CCP API. A dedicated scoring cluster, separate from batch, so the nightly refresh cannot starve checkout scoring. Merchant-level rate limits and quotas. Pre-warm capacity before known events (calendar-driven) |
| **12 months** | V₁₂ = V₀ × g₁₂ `[DECISION REQUIRED]`; Phase 2 Open API data (E-03) adds external calls | Partition the event stream by customer ID. Scale out the feature store. **Active-active** across the two Vietnam sites for the decision path. Asynchronous Open API ingestion so external bank latency never sits on the checkout path. Re-run the capacity test at each 2× volume step |

---

## 7. AI / Model Requirements `[Rubric R2][Rubric R3]`

These make the AI Law high-risk obligations (RG-02) and the Skill 10 guardrails **testable**.

| ID | Requirement | Standard | Target | Verification method |
|---|---|---|---|---|
| NFR-AI-01 | **Explainability: reason codes** | AI-Law transparency [N5]; KR 4.1; E-09; Banking Academy's "right to explanation" gap [R76] | **100%** of decisions (approve, refer, decline, limit, graduation) carry 1–4 ranked reason codes, generated from the policy layer and model attributions. They are stored in the decision log and shown in "Why this decision" in the same session. Codes are category-level (NFR-SEC-15) | Daily automated check: decisions without a reason code = 0 (KR 4.1 alert) |
| NFR-AI-02 | **Customer transparency and human review** | AI Law: human oversight and intervention [N5][N6] | Every customer surface states that an automated system contributed to the decision and offers a **"request a human review"** path. Requests are handled in the OP-01 queue within the refer SLA (KR 1.4) | Content QA; queue metrics |
| NFR-AI-03 | **Fairness monitoring** | G-5; KR 4.3; [L21–L23] | Approval rate and MOB3 30+ by gender, age band and region, **monthly**; **alert if the approval-rate ratio is < 0.8** (A-10-7, alert trigger only; Vietnam has no legal standard [R76]). **Pre-deployment:** the same test on the validation holdout, plus a proxy check (the correlation of each feature with the protected attributes). Protected attributes are never model inputs | Monthly fairness report signed by Model Risk; pre-release fairness test in the model technical file |
| NFR-AI-04 | **Drift** | G-8; PSI rule of thumb [M1][M2] | PSI **weekly** on the score and the top-20 features: **0.10–0.25 = investigate**, **> 0.25 = act** (retrain or fall back). Performance (Gini/KS on matured MOB3 labels) monthly, with a floor `[DECISION REQUIRED]` | Monitoring job output; alert log |
| NFR-AI-05 | **Champion fallback time** | DD-13 | Technical triggers (error, latency): automatic fallback **≤ 5 min**. Drift or performance triggers: fallback **≤ 1 business day** after Model Risk confirms. Graduation offers from the affected model paused at the same time | Quarterly fallback drill (NFR-REL-07) |
| NFR-AI-06 | **Decision logging** | AI-Law logging [N5]; FM-10 [R40] | Each decision record holds: input-snapshot hash plus a feature-vector pointer, model and policy versions, CCP fields F1–F6, tier, reason codes, consent references, experiment arm, latencies, and override (if any). Records are **immutable** (hash chain) and **reproducible**: replaying a logged input through the logged versions returns the same tier | Monthly replay test on a random sample of 100 decisions → 100% identical |
| NFR-AI-07 | **Human-override logging and oversight** | AI Law human oversight [N6]; OP-01 | **100%** of officer overrides carry a mandatory reason (controlled list + free text). Override rates are monitored per officer and per reason. Overrides feed back as labels (DF-4). A **segment-level kill switch** (credit-risk lead) stops auto-approval for a segment or channel within **≤ 5 min** | Audit of the override log; kill-switch drill each quarter |
| NFR-AI-08 | **Retraining and change control** | SR 11-7 practice [R45]; DD-02 | A scheduled model review **quarterly**. Retrain when PSI > 0.25 or performance falls below the floor. No model reaches production without independent validation, champion/challenger evidence and a Model Risk sign-off. The challenger traffic share is set in OP-02 `[DECISION REQUIRED]` | Model registry approvals; validation reports |
| NFR-AI-09 | **Model documentation (technical file)** | AI-Law conformity (RG-02); SR 11-7 [R45] | For each model: purpose, intended use and limits, data sources and consent basis, features, training method, validation (discrimination, calibration, stability, fairness), known limitations, human-oversight procedure, change log. It must be **complete before the conformity assessment** (CG-06) | Checklist review by Model Risk and Legal |
| NFR-AI-10 | **Data lineage** | PDPL purpose limitation [N3]; AI-Law data quality [N5] | Every feature traces to its source system, its **consent purpose** and its transformation version. Training sets are versioned and reproducible with point-in-time correctness (no label leakage) | Lineage query for 10 random features per release; retraining reproducibility test |
| NFR-AI-11 | **AI incident handling** | AI Law incident cooperation [N5] | Serious incidents (systematic wrongful declines, a fairness breach, a data-quality failure affecting decisions) are triaged within 24 h and reported as the AI Law requires. The **statutory reporting timeline was not verified** (OQ-18-04) | Incident runbook; tabletop exercise each year |
| NFR-AI-12 | **Affordability hard rule** | G-4; DD-03 | 0 booked loans whose instalment exceeds F2 headroom at booking. The policy layer enforces it and the booking service checks it again independently | Daily automated check (G-4 = 0%) |

---

## 8. Data Retention Requirements `[Rubric R4]`

**Principle.** Keep data as long as the **longest applicable legal duty**, then delete. Under the PDPL, data is deleted when its purpose ends, and deletion requests are fulfilled within 20 days unless a legal-hold duty applies [N3].

**Unverified.** A statutory retention period **specific to credit dossiers** under Circular 39/2016 was **not found**. The search surfaced **SBV Circular 04/2025/TT-NHNN** (effective 1 Jul 2025) on retention periods for banking-sector records. Its **application to commercial banks' loan files could not be verified**: the SBV portal was unreachable, and the extract referred to interbank and overnight loan files [N26]. The periods marked `[DECISION REQUIRED]` below therefore need HLB Legal confirmation (OQ-18-12).

| ID | Data | Retention target | Legal anchor | Verification |
|---|---|---|---|---|
| NFR-DATA-01 | Loan contracts, repayment ledger, accounting entries | **≥ 10 years** (start point per Decree 174/2016) | Law on Accounting 2015 + Decree 174/2016 [N20] | Retention-policy audit |
| NFR-DATA-02 | Customer identification (eKYC result, CCCD data, CDD records) | **5 years** from transaction end or account closure (whichever applies) | AML Law 2022, Art. 38 [N21] | Retention-policy audit |
| NFR-DATA-03 | Credit dossier: application, CCP snapshot, decision-log record, reason codes, officer notes, e-contract evidence | **Proposed: loan closure + 10 years**, aligned to NFR-DATA-01 `[DECISION REQUIRED]` | No credit-specific period verified; AI-Law logging [N5]; e-transaction evidence [N23] | Legal sign-off |
| NFR-DATA-04 | **Declined or abandoned applications** (no loan booked) | **Proposed: 5 years**, by analogy to AML and for dispute handling `[DECISION REQUIRED]` | No specific period found | Legal sign-off |
| NFR-DATA-05 | Marketing data and campaign responses (MK purpose) | Deleted within **20 days** of MK consent withdrawal. A minimal **suppression record** is kept so the opt-out keeps being honoured | PDPL [N3] | Deletion-workflow test |
| NFR-DATA-06 | Consent ledger | As long as the longest record that depends on that consent (it proves the lawful basis) | PDPL verifiable-consent retention [N3] | Audit |
| NFR-DATA-07 | Raw CIC report payload | **30 days** (then only derived features stay, under NFR-DATA-03) [Design] A-18-1 | PDPL minimisation [N3]; CIC breach lesson [R71] | Purge-job test |
| NFR-DATA-08 | Raw biometric images (face and liveness frames) | Keep the match result + reference ID. Raw-image retention `[DECISION REQUIRED]`: whether AML/eKYC rules require the image itself is unverified | PDPL (biometrics are sensitive) [N3]; Circular 17/2024 (not verified on retention) | Legal sign-off |
| NFR-DATA-09 | Security and access logs | **≥ 24 months** (proposed; Decree 53 requires ≥ 12 months of system logs when a storage request applies [N2]) | Decree 53/2022 [N2]; Circular 09 [N8] | SIEM retention config |
| NFR-DATA-10 | Personal-data breach records | **≥ 5 years** after remediation | Decree 356/2025 [N3] | Audit |
| NFR-DATA-11 | Model training datasets and validation evidence | Model in service + **the NFR-DATA-03 period** for decisions it made (reproducibility) `[DECISION REQUIRED]` | AI-Law documentation [N5] | Model registry audit |
| NFR-DATA-12 | Backups | 35-day rolling window; expiry propagates deletions | [Design] | Backup config audit |

---

## 9. Operability Requirements

| ID | Requirement | Target | Verification |
|---|---|---|---|
| NFR-OPS-01 | SLO dashboards | Real-time dashboards for NFR-PERF-01/02/05/08 and NFR-REL-01/02, alerting on burn rate, plus the Skill 10 KR 1.2 alert (p90 > 10 min for 1 h) | Dashboard review at launch readiness |
| NFR-OPS-02 | Incident management | Sev-1 to Sev-4 definitions; 24/7 on-call for the Critical tier; a post-incident review within 5 business days for Sev-1/2 | Incident log |
| NFR-OPS-03 | **Personal-data breach runbook** | Starts the 72-hour clock at discovery. Covers notification to A05 through the national portal, notice to data subjects for biometric data within 72 h, and a record kept ≥ 5 years [N3][N4] | Annual tabletop drill |
| NFR-OPS-04 | Regulatory change gates | The SBV pre-launch notice (T-10 days) [N9] and PDPIA updates (on purpose or processor change; 10 days after a reorganisation) [N3] are built into the release checklist | Release checklist audit |

---

## 10. Required Upstream Corrections (not edited here)

| # | File | Correction | Evidence |
|---|---|---|---|
| UC-18-01 | `03-define/10-success-metrics.md` KR 4.2; `02-discover/02-market-research.md` RF-8 | The AI-Law deadline for CreditPulse is **before deployment, and at the latest 1 Mar 2027**, not 1 Sep 2027. The Sep 2027 grace period applies only to finance systems **already operating before 15 Aug 2026** | [N6] |
| UC-18-02 | `02-discover/02-market-research.md` RF-7 | Decree 356 applies the **72-hour data-subject notice to location and biometric data**. The authority-notification clock for other financial data needs re-verification | [N3][N4] |
| UC-18-03 | `02-discover/02-market-research.md` §6 | Add Cybersecurity Law 116/2025/QH15 (effective 1 Jul 2026, replaces the 2018 law), AML Law 2022 (retention) and Law on E-Transactions 2023 | [N1][N21][N23] |
| UC-18-04 | `03-define/10-success-metrics.md` KR 1.2 | Fix the definition: *submit → decision* (system, NFR-PERF-02) vs *end-to-end checkout* (NFR-PERF-01) | §4.1 |

---

## 11. Assumptions Introduced by This Skill (carried to Skill 22)

| # | Assumption | Basis | Risk if wrong |
|---|---|---|---|
| A-18-1 | Raw CIC payload kept 30 days, then only derived features | Matches the AP-07 30-day cache; PDPL minimisation | Too short for dispute handling, or too long for minimisation |
| A-18-2 | ≤ 3 instant eligibility checks per identity or device per 24 h | Anti-probing design default | Genuine users blocked, or limit-fishing |
| A-18-3 | ≥ 80% comprehension of the total payable in pre-launch testing | Design target, not benchmarked | Disclosure not understood |
| A-18-4 | Indicative packages p95 ≤ 1 s; final ≤ 300 ms | Design allocation | Minor |
| A-18-5 | eKYC p90 ≤ 180 s (one retry) | HLB ~80 s average [R67]; no p90 is published | End-to-end p90 at risk |
| A-18-6 | OTP delivered ≤ 10 s p50 / 30 s p90 | Design allocation; no Vietnamese SMS gateway SLA found | p50 budget squeezed |
| A-18-7 | Lane A biometric re-authentication ≤ 10 s p50 | Design allocation | Minor |
| A-18-8 | CIC call p95 ≤ 15 s with a 15-s hard timeout | No public CIC SLA | More AP-07 fallbacks than planned |
| A-18-9 | Widget bundle ≤ 100 KB gzipped | Design target so merchant pages stay fast | Merchant pushback |
| A-18-10 | Nightly batch ≤ 4 h, done by 06:00 ICT | Design target | Stale pre-approvals |
| A-18-11 | Decision-path availability 99.9% per month | Circular 09 Level-3 reasoning (§5.1); not benchmarked against HLB | Over- or under-engineering |
| A-18-12 | Load test at 3× the forecast event peak | Common capacity-safety practice; not sourced | Event-day outage |

---

## 12. Open Items Raised by This Skill

| # | Item | Why it matters | Carry to |
|---|---|---|---|
| OQ-18-01 | Reconcile with Skill 14 (hosting, data localisation, latency feasibility, cost) once it is complete | Skill 14 was still in progress when this file was written | Skill 14 → this file |
| OQ-18-02 | PDPL/Decree 356 authority-notification deadline for non-biometric financial-data breaches | Breach runbook clock | Skill 22 (legal) |
| OQ-18-03 | Article-level content of Decree 356's section on financial, banking and credit-information activities, including any credit-scoring disclosure duty (inherits OQ-02-07) | Extra consent and disclosure obligations | Skill 19 / 22 |
| OQ-18-04 | AI-Law conformity pathway for the banking entries in the Decision 33 appendix (third-party vs self-assessment). Whether PoP disbursement also falls under the "automated high-value electronic transaction" entry. The Decree 142/2026 incident-reporting timeline (inherits OQ-02-11) | Launch timeline and cost | Skill 21 / 22 |
| OQ-18-05 | Accept the AI-Law deadline correction (UC-18-01): comply before deployment and by 1 Mar 2027 at the latest | Plan and KR 4.2 | Skill 22 |
| OQ-18-06 | CIC reporting frequency and deadline under Circular 15/2023 | PR-04 design | Skill 19 / 22 |
| OQ-18-07 | Mandatory pre-contract disclosure fields for electronic loans (Circular 39 as amended / 29/2026) | CO-02 compliance | Skill 22 (legal) |
| OQ-18-08 | Which definition KR 1.2 measures: submit → decision or end-to-end | Performance target | Skill 22 |
| OQ-18-09 | CIC and C06/VNeID response-time SLAs | J4/J5 budgets | Skill 14 / 22 |
| OQ-18-10 | Volume parameters V₀, g₆, g₁₂, P, E, N_A, M₀ | Capacity sizing and cost | Skill 22 `[DECISION REQUIRED]` |
| OQ-18-11 | Confirm 99.9% availability; HLB Connect's own SLO; refer-queue operating hours | Reliability tier; KR 1.4 | Skill 22 |
| OQ-18-12 | Retention periods for the credit dossier, declined applications, raw biometrics and training data. Whether Circular 04/2025 applies to commercial banks | Data retention and PDPL deletion | Skill 19 / 22 (legal) |
| OQ-18-13 | Circular 09 system level: 3, or 4 if HLB's customer data base is ≥ 10M | Control set | Skill 22 |
| OQ-18-14 | Any HLB Group (Malaysia) access to CreditPulse data (TIA needed) | Hosting and PDPL | Skill 22 |
| OQ-18-15 | Whether Decision 2345 is superseded by Circular 50/2024 authentication forms (effective 1 Jul 2026) (inherits OQ-02-09) | Authentication design | Skill 22 (legal) |
| OQ-18-16 | Status of Decree 53/2022 under Cybersecurity Law 116/2025, and whether domestic localisation is automatic or only on request | Hosting | Skill 14 / 22 |
| OQ-18-17 | eKYC/liveness vendor identity and DPA; HLB Connect session-timeout policy | Security controls | Skill 22 |

---

## Source Index

| # | Source | URL | Used for |
|---|---|---|---|
| N1 | Rajah & Tann Asia, "Law on Cybersecurity comes into operation on 1 July 2026" (Law 116/2025/QH15); Duane Morris Vietnam blog, "Law on Cybersecurity 2026" (8 Jan 2026) | https://www.rajahtannasia.com/viewpoints/law-on-cybersecurity-comes-into-operation-on-1-july-2026-establishing-a-unified-legal-framework-on-cybersecurity-and-network-information-security-in-vietnam/ ; https://blogs.duanemorris.com/vietnam/2026/01/08/vietnam-law-on-cybersecurity-2026-what-you-must-know/ | RG-03: effective date, consolidation, localisation retained (read via search extract) |
| N2 | Allen & Gledhill, "Data localisation requirement for specific data and entities in Vietnam effective 1 October 2022" | https://www.allenandgledhill.com/perspectives/publications/bulletins-vietnam/2022/data-localisation-requirement-for-specific-data-and-entities-in-vietnam-effective-1-october-2022/ | RG-04: data types, 24-month and 12-month durations, request trigger |
| N2b | Mondaq, "Data localisation rules in need of further guidance" (title only; not read) | https://www.mondaq.com/security/1273066/data-localisation-rules-in-need-of-further-guidance | RG-04: shows the interpretation is contested (Low) |
| N3 | EY Vietnam, "Legal alert March 2026: Decree No. 356/2025/ND-CP" | https://www.ey.com/en_vn/technical/tax/tax-and-law-updates/legal-alert-march-2026-decree-no-356-2025-nd-cp-providing-detailed-guidance-for-implementation-of-personal-data-protection-law | RG-01: sensitive financial data, verifiable consent, PDPIA/TIA timelines, 72-h biometric notice, 5-year breach records, 20-day deletion, DPO |
| N4 | Rajah & Tann Asia, "New Decree implementing the Law on Personal Data Protection passed" | https://www.rajahtannasia.com/viewpoints/new-decree-implementing-the-law-on-personal-data-protection-passed/ | RG-01: breach notification to the authority, 60-day filing, financial-sector section exists |
| N5 | Duane Morris, "Vietnam – the first Law on Artificial Intelligence" (3 Mar 2026); Rajah & Tann, "New Law on Artificial Intelligence takes effect on 1 March 2026" | https://blogs.duanemorris.com/vietnam/2026/03/03/vietnam-the-first-law-on-artificial-intelligence-what-you-must-know/ ; https://www.rajahtannasia.com/?p=184653 | RG-02: high-risk obligations (risk management, logs, human oversight, registration) — via search extract |
| N6 | Allen & Gledhill, "Vietnam identifies high-risk AI systems across six sectors" (19 Aug 2026); Vietnam Briefing, "46 high-risk AI systems…" | https://www.allenandgledhill.com/perspectives/articles/33373/vnkh-vietnam-identifies-high-risk-ai-systems-across-six-sectors ; https://www.vietnam-briefing.com/news/46-high-risk-ai-systems-to-face-enhanced-regulatory-oversight-in-vietnam.html | RG-02: the two banking entries, 15 Aug 2026 effective date, 1 Mar 2027 / 1 Sep 2027 deadlines |
| N7 | LuatVietnam, Circular 09/2020/TT-NHNN | https://english.luatvietnam.vn/circular-no-09-2020-tt-nhnn-dated-october-21-2020-of-the-state-bank-of-vietnam-promulgating-the-security-of-information-system-in-banking-operations-192825-doc1.html | RG-05: instrument and scope; no successor found |
| N8 | Mondaq, "Vietnam steps up information security for banks" | https://www.mondaq.com/financial-services/1249184/vietnam-steps-up-information-security-for-banks | RG-05: levels (Level 3 = 24/7, 4-h tolerance), MFA, admin control, outsourcing terms, annual drills |
| N9 | LuatVietnam, Circular 50/2024/TT-NHNN; Mondaq/Tilleke, "Circular 50 strengthens security for online banking in Vietnam" | https://english.luatvietnam.vn/tai-chinh/circular-50-2024-tt-nhnn-security-confidentiality-in-provision-of-online-banking-services-374068-d1.html ; https://www.mondaq.com/financial-services/1569470/circular-50-strengthens-security-for-online-banking-in-vietnam | RG-06: staged dates, annual assessment, 10-day pre-launch notice |
| N10 | WSO2, "Innovation, inclusion, interoperability: Vietnam's leap into open banking"; Circular 64/2024 text (LuatVietnam PDF) | https://wso2.com/library/blogs/innovation-inclusion-interoperability-vietnams-leap-into-open-banking ; https://static3.luatvietnam.vn/uploaded/vietlawfile/2025/2/64_2024_tt_nhnn_060225140745.pdf | RG-09: TLS ≥ 1.2, OAuth/OIDC/SAML, TCVN encryption standards |
| N13 | OWASP ASVS project; SoftwareMill, "What's new in ASVS 5.0" | https://owasp.org/www-project-application-security-verification-standard/ ; https://softwaremill.com/whats-new-in-asvs-5-0/ | §2.1: ASVS 5.0.0 (May 2025) levels; L3 for banking |
| N14 | OWASP MASVS | https://mas.owasp.org/MASVS/ | Mobile security standard |
| N14b | OWASP API Security Top 10 (2023) | https://owasp.org/API-Security/editions/2023/en/0x11-t10/ | Merchant API security |
| N14c | OWASP Machine Learning Security Top 10 | https://owasp.org/www-project-machine-learning-security-top-10/ | NFR-SEC-15 |
| N15 | web.dev, "Web Vitals" | https://web.dev/articles/vitals | LCP 2.5 s / INP 200 ms / CLS 0.1 at p75 |
| N16 | Uvik, "Fintech risk scoring engine (Python/FastAPI)" case study (vendor, Low) | https://uvik.net/case-studies/fintech-risk-scoring-engine-python-fastapi/ | Sub-200 ms p95 scoring |
| N17 | OneUptime, "Monitor fraud detection model inference latency in real-time payment"; Databricks, "What happens milliseconds after you tap pay" (vendor, Low; via search extract) | https://oneuptime.com/blog/post/2026-02-06-monitor-fraud-detection-inference-latency-opentelemetry/markdown ; https://databricks.com/blog/what-happens-milliseconds-after-you-tap-pay | 50–200 ms real-time decision budget |
| N18 | W3C, WCAG 2.1 | https://www.w3.org/TR/WCAG21/ | §3 success criteria |
| N19 | LuatVietnam, Circular 26/2020/TT-BTTTT | https://english.luatvietnam.vn/circular-no-26-2020-tt-btttt-dated-september-23-2020-of-the-ministry-of-information-and-communications-on-the-application-of-standards-and-technolog-191327-doc1.html | RG-17: accessibility mandatory for state bodies only |
| N20 | LawNet, "Duration of retaining accounting documents in Vietnam"; LuatVietnam, Decree 174/2016 | https://lawnet.vn/thong-tin-phap-luat/en/tai-chinh/duration-of-retaining-accounting-documents-in-vietnam-108243.html ; https://english.luatvietnam.vn/decree-no-174-2016-nd-cp-dated-december-30-2016-of-the-government-on-detailing-a-number-of-articles-of-the-law-on-accounting-111573-doc1.html | NFR-DATA-01: ≥ 10 years |
| N21 | Law on AML 2022, Art. 38 (Hệ thống pháp luật); Thư viện pháp luật full text | https://hethongphapluat.com/luat-phong-chong-rua-tien-2022/dieu-38 ; https://thuvienphapluat.vn/van-ban/Tien-te-Ngan-hang/Luat-Phong-chong-rua-tien-2022-519327.aspx | NFR-DATA-02: 5 years |
| N22 | LuatVietnam, "9 credit-information categories banks provide to CIC from 1 Jan 2025" (Circular 15/2023) | https://luatvietnam.vn/tin-van-ban-moi/9-thong-tin-tin-dung-ngan-hang-cung-cap-cho-cic-tu-ngay-01-01-2025-186-96366-article.html | RG-10 |
| N23 | Rajah & Tann, "New Law on Electronic Transactions"; LuatVietnam, Law 20/2023/QH15 | https://www.rajahtannasia.com/viewpoints/new-law-on-electronic-transactions/ ; https://english.luatvietnam.vn/thuong-mai/law-on-e-transactions-2023-no-20-2023-qh15-259738-d1.html | RG-13: effective 1 Jul 2024 |
| N24 | NIST SP 800-57 Part 1 Rev. 5 | https://csrc.nist.gov/pubs/sp/800/57/pt1/r5/final | Key management |
| N25 | NIST FIPS 140-3 | https://csrc.nist.gov/pubs/fips/140-3/final | HSM validation |
| N26 | Search extract: SBV Circular 04/2025/TT-NHNN on record retention periods (SBV press-release page unreachable; applicability unverified) | https://sbv.gov.vn/en/w/press-release-on-issuance-of-circular-no.04/2025/tt-nhnn | §8 caveat (Low) |
| M1, M2 | PSI literature (from Skill 10) | see `10-success-metrics.md` | NFR-AI-04 |
| D4, D5 | Real-time lending feature patterns (vendor, Low; from Skill 13) | see `13-solution-design.md` | NFR-PERF-04/06 |
| — | `_research-notes-07-13.md` [R#]: R40, R41, R45, R46–R48, R66, R67, R71, R73, R74, R76, R77 | local | PDPL, eKYC ~80 s, CIC incident, Open API, sandbox, legal gaps, benchmarks |
| — | Upstream: `02-market-research.md` (RF-1–RF-13; S27, S30–S33, S35, S40), `10-success-metrics.md`, `13-solution-design.md`, `16-epic-feature-decomposition.md`, `literature-review.md` (L1, X7, L21–L23), `_context.md`, `_requirements.md` [Brief] | local | Targets, features, guardrails, design |

## Skill 22 Resolutions (User decision, Skill 22, 2026-09-30)

| Item | Resolution |
|---|---|
| D-10 operational defaults | A-18-1 to A-18-12 **accepted as design defaults**, to be revised after pilot |
| D-11 | NFR-PERF-01 (end-to-end p50 ≤ 3 min) is the KR 1.2 target; NFR-PERF-02 is the system budget. OQ-18-08 and UC-18-04 are resolved |
| D-05 legal items | OQ-18-02/03/04/06/07/12/15/16 **accepted as logged, non-blocking risks** (RK-23), closed by a legal review sprint at gate G0. The PRD marks them "legal to confirm" |
| D-09 | OQ-18-14 (HLB Group access): de-identified extracts only (assumed). OQ-18-10 volumes: parameters (D-04) |
