# Technical Feasibility
**Product:** HLB Point-of-Purchase Credit Decisioning — **HLB CreditPulse** | **Skill:** 14 | **Status:** Done (2026-09-30)
**Depends on:** solution-design.md | **Feeds into:** product-prd.md (Section 3 — System Design), risk-register.md

> **Run-order note (SD-5).** The user-confirmed pipeline order ran Skill 13 (Solution Design) and Skill 15 (User Journey Map) **before** this skill. This file therefore **does not edit** Skills 13 or 15. Where feasibility findings require a change in either file, the change is listed in §9 "Required upstream changes" for the user and Skill 22 to action.
>
> **Evidence rules applied.**
> - No HLB-internal fact is invented. HLB's cloud provider, core-banking APIs, vendor contracts and unit costs are **not public**. Wherever they matter, this file writes `[DECISION REQUIRED]` rather than a guess.
> - Every cost figure is a **published** unit price with its source, or it is `[DECISION REQUIRED — HLB infra/vendor pricing not public]`.
> - Every effort figure is an **engineering estimate by this skill** (person-weeks, pw). No HLB IT input or vendor quote exists. Estimates are flagged `(est.)`.
> - Citations: `[R#]` = `_research-notes-07-13.md`; `[T#]` = new sources for this skill (Source Index at end); `[L#]` = `literature-review.md`; `[D#]` = Skill 13 sources; `[Skill0N …]` = upstream files. Rubric tags are written `[Rubric R#]`.

---

## 0. Summary for decision-makers `[Rubric R2]`

| Question | Answer |
|---|---|
| Can CreditPulse be built as designed in Skill 13? | **Yes, with conditions.** No component is fundamentally infeasible (**no ❌**). **13 of 29 v1 components are ⚠️**. They reduce to **9 spikes** (§1.3), each needing an HLB decision, a measurement or a legal confirmation before build. |
| Can the checkout path meet p50 ≤ 3 min / p90 ≤ 10 min (KR 1.2)? | **Feasible on the system side** (§2). The machine steps take about 1.5 minutes at p50 for a new-to-bank applicant. eKYC (~80 s [R67]) dominates, and CIC and scoring add seconds [T6][T9][T10]. **The binding risk is the channel, not the engine.** A browser cannot read the chip-CCCD over NFC [T24], so a new-to-bank shopper on a web checkout must be handed off to a phone app. That hand-off is unmeasured. |
| Biggest blockers | (1) **AI Law timing**: the 1 Sep 2027 grace period appears to cover only systems **already in operation** before 15 Aug 2026 [T17]. A new CreditPulse may need its conformity assessment **before launch**. (2) **Hosting decision** under data-localisation rules [T1][T2][T20]. (3) **CASA metadata** behind signals S2, S4, S6, S7 and S9 is unverified (A-13-1 to A-13-4). (4) **Cold start**: there is no labelled thin-file or PoP repayment history to train the ML models on [L20][L36]. (5) **Web-checkout eKYC hand-off** [T24]. |
| Build vs buy | **Build** the models, the consent ledger, the CCP and the stage services on open-source components that can run in Vietnam. **Reuse** HLB's eKYC [R67]. **Decide** between a small in-house rules service and a commercial decisioning platform (FICO, Experian PowerCurve, Provenir), based on what HLB already licenses [T11][T12][T13]. |
| Effort | v1 Must scope: **~260–410 pw (est.)**, against Skill 11's implied 276 pw. Add ~22–34 pw of platform work that no feature code owns. **7 features change band** versus Skill 11 (§7). |
| Monthly run cost | **Cannot be stated honestly.** No HLB volume, hosting choice or vendor price is public. §8 gives the cost **structure** and the only public unit prices found (CIC, historical; C06 authentication). |

---

## 1. Feasibility Matrix `[Rubric R2][Rubric R3]`

> Verdict key: ✅ Confirmed (straightforward, well understood) · ⚠️ Risk (possible, but needs a spike, decision or legal confirmation) · ❌ Blocker (infeasible as designed; needs redesign). **Complexity** follows Skill 11's bands (S ≈ ≤5 pw, M ≈ 6–12, L ≈ 13–24, XL ≈ >24). **Cost/mo**: `DR` = `[DECISION REQUIRED — HLB infra/vendor pricing not public]`.

### 1.1 Engine components (Skill 13 §1, §4)

| # | Component (Skill 13 ref) | Approach | Verdict | Complexity | Cost/mo | Risk | Notes |
|---|---|---|---|---|---|---|---|
| C-01 | **Consent Service / ledger** (E-01; §4 svc 1) | Build: append-only per-customer, per-purpose (SC/MK/RF) ledger. Every CCP read passes a purpose check. Revocation propagates to Campaign and Recommendation. | ✅ | M | DR | M | Standard pattern. PDPL requires tamper-evident consent records that can be retrieved on request [T23][R46]. Commercial consent platforms in Vietnam are mostly web-cookie tools [T23], so the ledger is core bank logic → build. |
| C-02 | **Data ingestion, batch** (E-02: core banking, cards, loans, Sổ Bán Hàng daily) | Build: CDC or batch extracts into an in-country data platform | ⚠️ | XL | DR | H | Depends on **A-13-10** (core-banking extract/API access) and on the **CASA field richness in A-13-1 to A-13-4**. A spike is needed (B-03). Sổ Bán Hàng data-access terms and API are not public [R66][T22]. |
| C-03 | **Data ingestion, event stream** (E-02/E-08: CASA txns, repayments, application events) | Build on a Kafka-class open-source event bus, self-managed or hosted by an in-country provider | ⚠️ | L | DR | M | The pattern is proven at Nubank [D3]. AWS's Hanoi Local Zone lists no managed streaming service [T4], so this layer is **self-operated** unless HLB's provider offers one. Needs core-banking events (A-13-10). |
| C-04 | **Feature store** (online + offline, S1–S17) | Open-source Feast (Apache-2.0) [T14] on in-country compute. Online store = low-latency KV; offline store = the analytical store. | ✅ | M | DR (licence $0 [T14]; infra DR) | M | Feast provides point-in-time-correct offline retrieval and low-latency online serving [T14]. That is the parity Skill 13 requires [D4][D5]. Managed feature stores are tied to cloud regions that are not in Vietnam (§4). |
| C-05 | **Risk champion: scorecard** (E-06a) | Build: logistic scorecard per segment and evidence tier; reason codes from attributes | ⚠️ | L | DR | M | The technique is standard. **Cold start**: HLB's historical approvals exclude the thin-file declines, and reject inference barely helps [L20]. The launch scorecard for THIN-tier applicants must therefore be judgment-weighted and paired with starter limits plus a randomised approval corridor [L36] (B-04). |
| C-06 | **Risk challenger: GBM** (E-06b) | Build in-house with open-source gradient boosting | ⚠️ | L | DR | H | It only earns deployment on a randomised split once it has enough outcome labels (DD-02). **It cannot be trained for THIN-tier segments at launch**: no PoP performance data exists. Generic off-the-shelf scores underperformed on ecosystem data at Mercado Pago [R12]. |
| C-07 | **Stable-income estimator** (F2) | Build: statistical estimate over CASA inflows (S1–S4, S8) with a confidence interval | ⚠️ | M | DR | M | Feasible on inflow amounts and dates alone. The **payer-identification** that makes it robust (S2, S4) depends on A-13-1/A-13-2 (B-03). |
| C-08 | **Behavioural / survival re-score** (PR-01, E-08) | Build. Launch as a rules-plus-scorecard behavioural refresh; move to survival modelling once PoP repayment history exists [D1] | ⚠️ | M | DR | M | **Cold start**: there are no PoP repayment events before launch. The existing HLB card and loan conduct (S10) can seed a behavioural scorecard for Cohort A. |
| C-09 | **Early-warning model** (PR-02) | **Rule triggers at launch** (missed or partial instalment, new CIC obligation, inflow drop). ML predictor later. | ⚠️ | M | DR | M | Skill 13 specifies a *predicted* missed payment. It cannot be trained without PoP delinquency labels. Skill 11 already suggested rules first. Change requested (§9 U-04). |
| C-10 | **Uplift model** (AW-03, Should) | Build with open-source uplift libraries: CausalML (Apache-2.0, ~6k GitHub stars) [T15] or scikit-uplift [T16] | ✅ | L | DR (licence $0) | L | Blocked by design until ≥1 holdout cycle exists (AW-02). The libraries implement meta-learners and uplift trees and give Qini/uplift metrics [T15][T16]. |
| C-11 | **Integrity anomaly model** (F5, AP-03) | Rules at launch (ID mismatch, device and velocity, CIC identity match), plus an unsupervised anomaly score; supervised model later | ⚠️ | M | DR | M | Fraud labels are scarce at launch. Rules plus biometric eKYC carry R-3 [R67][L52]. Device-intelligence vendors were not researched → `[DECISION REQUIRED]` build vs buy (OQ-14-06). |
| C-12 | **Policy & Decision Service** (E-07, tier policy §1.1) | Build a rules-as-code service with versioned parameters, **or** buy a commercial decisioning platform (§5) | ✅ | M | DR | L | The tier policy is six ordered rules (Skill 13 §1.1). Deterministic evaluation is trivial in latency terms [T9][T10]. The thresholds are HLB policy (OQ-13-02). |
| C-13 | **Reason-code generator** (E-09) | Scorecard attributes → codes. The GBM challenger needs attribution-to-reason mapping and Vietnamese customer copy. | ✅ | M | DR | M | Band rises S → M because the challenger's reasons must be as explainable as the champion's (AI Law transparency [Skill02 RF-8]). |
| C-14 | **CCP store & API** (E-05; §4 svc 6) | Build: key-value store for the current CCP plus a versioned history in the analytical store; purpose-scoped read API | ✅ | L | DR | L | Standard. Every read is checked against C-01. |
| C-15 | **Decision log / audit store** (§4 storage; GV-01) | Build: immutable log of inputs hash, model versions, tier and reason codes | ✅ | S | DR | L | Needed for AI Law technical documentation and logs [T17] and for disputes. Retention period `[DECISION REQUIRED — legal]`. |
| C-16 | **Governance & monitoring** (GV-01: PSI drift, fairness, registry, champion/challenger split) | Build scheduled jobs and dashboards over C-15 plus a model registry | ⚠️ | XL | DR | H | The engineering is routine. The **AI Law conformity assessment** (third-party certification or self-assessment [T17]), and its **timing**, are not (B-01). |

### 1.2 Stage services, channels and internal tools

| # | Component | Approach | Verdict | Complexity | Cost/mo | Risk | Notes |
|---|---|---|---|---|---|---|---|
| C-17 | **Campaign service + workspace** (AW-01/02/04, RT-01 delivery) | Build: batch target list over the CCP, random holdout, consent re-check at send | ✅ | M | DR | L | Batch job and UI. Holdout assignment must be logged for KR 3.1. |
| C-18 | **Offer / Package service** (CO-01/02) | Build: package generator over the tenor set {3…24} [Brief p.9], F2 and the rate grid; all-in cost and APR | ✅ | M | DR | L | Deterministic arithmetic. The all-in APR method must match HLB's disclosure practice `[DECISION REQUIRED — HLB legal]`. |
| C-19 | **Checkout Decision orchestrator** (AP-01, DF-2) | Build: state machine calling eKYC → CIC (parallel) → features → scoring → policy, with timeouts and fallback (AP-07) | ✅ | L | DR | M | The latency budget is feasible (§2). The engineering is timeout handling, idempotency and a merchant callback. |
| C-20 | **eKYC reuse** (AP-02) | Reuse HLB's National-ID-Registry/C06 integration (~80 s onboarding [R67]) | ⚠️ | M | C06 fee, see §8 | **H** | **Channel gap:** browsers support only NDEF through Web NFC (Chrome for Android only). They cannot read ISO-DEP chips such as the CCCD [T24]. MX-01 Options A and B (web widget, hosted page) therefore **cannot perform chip-CCCD NFC in the browser** and must hand off to a native app (B-06). |
| C-21 | **Servicing & Graduation service** (PR-03, PR-04) | Build: graduation offer (never auto-applied) plus mapping of the new product into existing CIC reporting | ✅ | M | DR | L | CIC reporting is an existing obligation [Skill02 RF-11]; only the product mapping is new. |
| C-22 | **Recommendation service** (RT-01) | **Rules at launch**: F2 headroom × merchant category × partner catalogue. Propensity model later. | ✅ | M | DR | M | Category precision depends on A-13-4 (§6 B-03). VietQR follows EMVCo, which carries a Merchant Category Code field [T19]. Whether HLB stores it for QR receipts is unverified. |
| C-23 | **Anti-stacking gate** (RT-02) | Rule over a fresh or cached CIC pull plus the internal exposure cap | ✅ | S | CIC per-query fee (DR) | L | **Cost driver:** a CIC query per draw and per graduation offer (§8). |
| C-24 | **Referral ledger** (AD-01) | Build: invite link, invitee Lane B flow, reward released after K on-time instalments | ✅ | M | DR | L | No external dependency. Abuse checks reuse F5. |
| C-25 | **Case management / officer workbench** (OP-01) | Build, or extend HLB's existing loan-origination or case tool `[DECISION REQUIRED — unknown whether one exists]` | ✅ | M | DR | M | M if an existing case tool is extended; L if built new. |
| C-26 | **HLB Connect "Pay Later" hub** (IA §3) | Build new app modules in the existing app [R67] | ✅ | M | DR | L | **No feature code owns the hub shell** (navigation, My plans, Why this decision, Privacy & consent). It is added as a platform line in §7. |
| C-27 | **Merchant checkout integration** (MX-01: API widget, hosted page, QR deep link) | Build three integration types, merchant onboarding, keys + mTLS (A-13-11) | ⚠️ | XL | DR | **H** | Three channel types plus per-merchant integration and security review. Lane B eKYC hand-off (C-20, B-06) changes the web flow. |
| C-28 | **Loan booking & repayment events from core banking** (A-13-10) | Integrate with HLB core banking | ⚠️ | L | DR | **H** | Not verifiable externally. If core banking cannot emit repayment events in near real time, PR-01/E-08 falls back to a daily batch. That is acceptable for re-scoring, but not for in-session booking (B-07). |
| C-29 | **Open API ingestion** (E-03, Phase 2, Should) | Build a Circular 64 client: REST/JSON, OAuth 2.0, TLS 1.2+, JWS (RFC 7515), security level 3 [T18] | ⚠️ | L | DR | M | Depends on **other** banks' readiness (full compliance due 1 Mar 2027 [R73]) and on customer consent. The data-access window is 180 days unless otherwise agreed [R73]. |
| C-30 | **Phase-3 third-party feeds** (E-04, Later) | Not designed (PL-01) | — | — | — | — | Out of v1. Decree 94 applicability is open (OQ-02-17) [R74]. |

**Result:** 0 ❌, 13 ⚠️ (C-02, C-03, C-05 to C-09, C-11, C-16, C-20, C-27 to C-29), 16 ✅, plus C-30 out of scope. The ⚠️ items reduce to **9 spikes**, listed below.

### 1.3 Components requiring a spike (⚠️)

| Spike | Components | What needs investigation | Owner | Timebox (est.) |
|---|---|---|---|---|
| SP-1 CASA metadata audit | C-02, C-07, C-22 | Sample 3–6 months of anonymised CASA and card records. For each A-13-1 to A-13-4 field, measure (a) whether it is present, (b) its fill-rate, (c) whether the counterparty is identifiable: salary payer, platform payout, e-wallet top-up, bill-pay, MCC. Output: a go/no-go for each of S2, S4, S6, S7 and S9. | HLB data engineering + credit-risk analytics | 2–3 weeks |
| SP-2 CIC H2H latency & contract | C-19, C-23 | Measure HLB's actual CIC H2H response distribution (p50/p90/p99) and error rate. The only public figure is "<10 s" from 2019 [T6]. Obtain the current per-query fee schedule. | HLB IT + procurement | 1–2 weeks |
| SP-3 Lane B eKYC hand-off | C-20, C-27 | Compare three hand-off paths for web-checkout shoppers: (a) QR to HLB Connect (app install needed for new-to-bank users), (b) a native HLB eKYC SDK inside merchant apps, (c) the MPS/VNeID electronic-authentication service that Vietcombank already uses [T25]. Measure time and drop-off for each. | HLB digital + product | 3–4 weeks |
| SP-4 Core-banking integration | C-28, C-03 | Confirm APIs or events for loan booking, disbursement to merchant, and repayment events (A-13-10). | HLB core-banking team | 2 weeks |
| SP-5 Cold-start data inventory | C-05, C-06, C-08, C-09, C-11 | Count HLB's historical labelled outcomes by segment × evidence tier. Decide which segments get a statistical scorecard and which a judgmental starter policy. Size the randomised approval corridor. | HLB credit-risk modelling | 3 weeks |
| SP-6 Hosting & localisation legal check | all | Confirm the hosting model against Decree 53, Circular 09/2020 and PDPL/Decree 356 (§4). Confirm whether any offshore processing (for example group data science) triggers a transfer impact assessment. | HLB legal + IT risk | 2–3 weeks |
| SP-7 AI Law conformity path | C-16 | Legal opinion: is a new CreditPulse subject to conformity assessment **before** deployment (no transition period)? Third-party or self-assessment? Does the refer band change the classification (OQ-02-11)? | HLB legal / compliance | 2–4 weeks |
| SP-8 Sổ Bán Hàng data access | C-02 (S5) | Partnership terms, data fields, API or file feed, merchant-consent mechanics [R66][T22] | HLB SME banking + partner | 2 weeks |
| SP-9 Decisioning platform inventory | C-12 | Does HLB VN or HLB Group already license a decision engine or LOS? This drives the build-vs-buy choice in §5. | HLB IT architecture | 1 week |

### 1.4 Blockers (❌)

None. No component is fundamentally infeasible as designed. The design changes required are listed in §9. The launch-gating risks are in §6.

---

## 2. Latency Feasibility — checkout path (KR 1.2) `[Rubric R2]`

**Target (Skill 10 KR 1.2):** time "from application submit to auto-decision": p50 ≤ 3 min, p90 ≤ 10 min. Refer-band cases are outside KR 1.2 by definition (KR 1.1).

**Ambiguity to close (OQ-14-03).** Two things are undefined. "Submit" could mean before or after eKYC. The e-contract and OTP steps sit after the decision, so KR 1.2 does not cover them. Both readings are shown below.

### 2.1 Step-by-step budget: Lane B (new-to-bank, the worst case)

| # | Step | Can run in parallel? | p50 evidence | p90 driver | Source |
|---|---|---|---|---|---|
| 1 | Package list (CO-01 indicative) | — | Sub-second (deterministic arithmetic) | — | Decision APIs run in ms [T9][T10] |
| 2 | Customer enters phone number and consents | — | **User time, not sourced.** Measure in the pilot. | Consent-screen comprehension | — |
| 3 | eKYC: NFC chip read, liveness, face match, National-ID-Registry check | Starts step 4 once the chip read returns the ID number | **~80 s**: HLB's own onboarding time after the National ID Registry integration (from ~300 s) [R67]. This figure already includes customer actions. | NFC read failures and retries. Failure patterns are documented in Vietnam [Skill03 S44]. Cap: 3 retries, then REFER (A-13-8). | [R67]; [Skill03 S44] |
| 4 | CIC pull (S14) | **Yes**: in parallel with liveness and face match | **< 10 s**: CIC's host-to-host dedicated-line model, as reported in 2019 [T6] | Stale figure (2019). No public SLA. Set a timeout, then apply the AP-07 fallback. | [T6]; SP-2 |
| 5 | Online features + scorecard + GBM + policy + reason codes | After 3 and 4 | **Sub-second.** Capital One's auto-loan decisioning returns in 180–200 ms [T9]. Experian reports a lender averaging 15–20 ms per decision, and about 3 s for new customers [T10]. | Cold online-store reads for new customers | [T9][T10] (vendor-published, Medium) |
| 6 | Decision shown, with packages and reason codes | — | Sub-second | — | — |
| | **System time, submit → decision (eKYC inside "submit")** | | **≈ 80 s + ~0–10 s CIC overlap + ≤3 s ≈ 1.4–1.6 min** | | Arithmetic on the rows above |
| | **Submit → decision (eKYC before "submit")** | | **≈ 5–15 s** | | Same |
| 7 | e-contract + OTP (after the decision; outside KR 1.2) | — | Not sourced for HLB. **Existence proof:** Techcombank advertises "approval and disbursement within 3 minutes" in-app [R77]. | SMS OTP delivery. In-app Smart OTP or biometrics avoid SMS. | [R77] |

### 2.2 Lane A (pre-approved existing customer)

The steps are biometric face match against the chip-CCCD on file, re-validation of F2 and F5 from the online store, and a CIC stacking check (fresh pull or a cache under 30 days, AP-07). The machine time is **seconds**; no step is minute-scale. Techcombank's "3 minutes including disbursement" [R77] and the Home Credit ~3 min figure [Skill02 S9] show the path is achieved in Vietnam today.

### 2.3 Verdict

| Target | Verdict | Condition |
|---|---|---|
| **p50 ≤ 3 min** | ✅ **Feasible.** About 1.5 min of system time leaves ~1.5 min for customer input. | eKYC p50 stays near HLB's published ~80 s [R67]. The CIC pull runs in parallel with liveness. |
| **p90 ≤ 10 min** | ⚠️ **Feasible with controls** | (a) Cap eKYC retries at 3 (A-13-8). (b) Set a **CIC timeout**, then the AP-07 fallback; the timeout value is `[DECISION REQUIRED]` and SP-2 informs it. (c) **Resolve the web-checkout hand-off (B-06).** A new-to-bank shopper on a desktop web checkout who must install HLB Connect to scan the chip adds app-store download and registration time. That time is unmeasured and could alone breach p90. |

**Counter-evidence to keep in view.** MSB's FICO-based decisioning in Vietnam (announced June 2026) cut loan turnaround from **30 to 15 minutes**, not to 3 [T11]. The engine is rarely the bottleneck. Integration hops and manual residue are. `[Rubric R2]`

**Design implications (carried to §9):** run the CIC pull in parallel with liveness; add an explicit CIC timeout parameter; state which KR 1.2 reading applies; instrument every step with timestamps (the decision log, C-15).

---

## 3. API Dependency Map

| API / Service | Purpose (Skill 13 ref) | Rate limits | Pricing | Reliability (public evidence) | Fallback |
|---|---|---|---|---|---|
| **CIC** (National Credit Information Centre): bureau enquiry + reporting | S14; anti-stacking RT-02; PR-04 reporting | Not public. Access is by **host-to-host dedicated line** per credit institution [T6]. | **Current schedule not public.** Historical: Decision 1669/2005 set 40,000 VND per individual report (the decision is expired) [T7]. CIC has cut fees repeatedly since 2015, including a 50% monthly reduction that saved 123 bn VND in one half-year [T7]. → `[DECISION REQUIRED — HLB's CIC contract]` | Retrieval "< 10 s" (2019) [T6]. **Sep 2025 incident = data breach, not outage.** SBV and CIC stated that CIC services kept running normally [T21][R71]. No public uptime SLA. | **AP-07**: Lane A uses a cached pull under 30 days old when F3 = BUREAU+BANK; otherwise REFER or a lower starter cap. Treat CIC data as sensitive and minimise retention (breach lesson [R71]). Skill 20 should define the timeout behaviour. |
| **National population DB (C06) / chip-CCCD / VNeID** | AP-02 eKYC; F5 | Not public | Authentication via the national population database: **500 VND per authentication** at the 50%-reduced rate for 1 Dec 2023 – 31 Dec 2025 (search extract of a legal database) [T8]. **The 2026 rate is not verified.** → `[DECISION REQUIRED — confirm current fee]` | No public SLA. eKYC failure patterns are reported by users [Skill03 S44]. HLB's integration is live (~80 s onboarding) [R67]. | Retry ×3, then REFER "identity review" (Skill 13 Flow 3; the full assisted fallback is PL-04). **Web-checkout hand-off** to a native app or the VNeID authentication service (SP-3) [T24][T25]. |
| **Sổ Bán Hàng** merchant ledger | S5 (MSME); AD-02 | Not public | Partnership terms not public [R66] | Unknown | Without the feed, MSME assessment uses CASA QR/transfer receipts only (S5 partial), so F3 = BANK-OBSERVED or THIN and the applicant gets a starter limit. |
| **Merchant / partner checkout** (HLB-provided APIs) | MX-01; DF-2 | Defined by HLB per merchant (HLB is the provider) | Commercial terms `[DECISION REQUIRED]` | HLB-controlled | QR deep link (Option C) works whenever the web widget or hosted page fails. Package reservation for 30 min (A-13-6). |
| **Open API (Circular 64)**, Phase 2 | S16; E-03 | Not set in public summaries. Standards: REST/JSON, OAuth 2.0, TLS 1.2+, JWS, security level 3 [T18] | Not public | Depends on counterparty banks. Full compliance due 1 Mar 2027 [R73]. | Not used, so F3 stays THIN or BANK-OBSERVED; no hard dependency in v1. |
| **VietQR / NAPAS** (QR receipts; in-store QR) | S5, S9; MX-01 Option C | Not public | Not public | EMVCo-compliant [T19] | Category coding falls back to counterparty-name heuristics (SP-1). |
| **SMS / OTP gateway** | e-contract OTP; refer-band notices | HLB's existing provider | `[DECISION REQUIRED — HLB's existing SMS contract]` | Not public | In-app Smart OTP or biometric signing through HLB Connect. Circular 50/2024 authentication tiers apply [Skill02 RF-5]. |
| **HLB core banking** (loan booking, disbursement to merchant, repayment events) | DF-2 booking; DF-3 re-score; S1–S11 | Internal | Internal | Internal; A-13-10 unverified | Repayment events: daily batch fallback for PR-01. **Booking has no acceptable fallback** for an in-session decision (B-07). |
| **HLB cards/loans systems** | S9, S10 | Internal | Internal | Internal | Monthly batch |
| **HLB Connect** (app platform) | All customer surfaces | Internal | Internal | Award-winning live channel [R67] | Hosted HLB web page for everything **except** NFC eKYC |

---

## 4. Hosting & Data-Localisation Constraints `[Rubric R4]`

HLB's hosting model and cloud provider are **not public**, and this file does **not** assume one (`[DECISION REQUIRED]`, OQ-14-01). What the rules say:

| Rule | What it requires | Effect on CreditPulse | Source |
|---|---|---|---|
| **Decree 53/2022** (Cybersecurity Law) | **Domestic** enterprises providing regulated online services "must store the [regulated data] in Vietnam", with no triggering condition. Whether cloud storage hosted abroad but accessed from Vietnam complies is **unanswered**. Counsel recommend physically in-country storage. | Whether HLB Connect counts as a regulated online service is a legal question (SP-6). Conservative design: **store all CreditPulse personal data in Vietnam.** | [T1] |
| **SBV Circular 09/2020/TT-NHNN** (information-system security in banking; in force 1 Jan 2021) | Cloud and third-party use is **permitted with conditions**. For level-3+ systems and systems processing customer personal data, the bank must: assess IT and operational risk beforehand; classify workloads intended for cloud; hold backup plans for level-3+ components; sign a contract covering security commitments, maximum interruption time, backup and DR, and data segregation from other clients; and assess the provider's compliance annually. The provider must own infrastructure that complies with Vietnamese law and hold a valid international information-security certificate. | CreditPulse processes customer personal and financial data, so these conditions apply to any external hosting. Budget for the risk assessment and the contract clauses. | [T2][T3] |
| **PDPL 91/2025 + Decree 356/2025** | Financial data is **sensitive** [Skill02 RF-7]. Cross-border transfer requires a **transfer impact assessment** dossier filed with MPS (A05), generally within 60 days. | Any offshore processing needs a TIA: offshore model training, group shared services, or a foreign SaaS (for example a consent or decisioning SaaS hosted abroad). Keep training and serving in-country by default. | [T20][R46][R48] |
| **Circular 64/2024** (Open API) | Security level 3; ISO 27001 or TCVN 11930 | Applies to the Phase-2 Open API client (C-29) | [T18] |

**In-country hosting options that exist (not a recommendation of any one):**
- HLB's own data centres (unknown).
- Vietnamese cloud and DC providers such as **Viettel IDC** and **FPT Cloud**, which serve banking [T4].
- **AWS Hanoi Local Zone** (launched June 2026). It offers EC2, EBS, S3 One Zone-IA, ECS, EKS, VPC, Direct Connect and ALB, and does **not** list managed databases, SageMaker, managed Kafka or ElastiCache [T4]. VIB and VPBank were reported as early financial adopters (search extract, lower confidence) [T4].

A Vietnamese bank running mainly on public cloud is precedented: Techcombank migrated the majority of its applications to AWS [T5].

**Architecture consequence (feasibility finding).** Build CreditPulse on **portable open-source components that run on Kubernetes or VMs**: an event bus, Feast [T14], an open-source key-value store and relational/analytical stores, and open-source ML libraries [T15][T16]. Do **not** build on region-bound managed ML or feature-store services. Then any in-country option above, or HLB's own DC, can host it. The cost of this choice is more self-operation effort, carried in §7 "Platform foundation". `[Rubric R2]`

---

## 5. Build vs. Buy Decisions `[Rubric R2]`

Vendor names below are cited for **existence and in-market use only**. No vendor price is public, and none is invented.

| Component | Build | Buy / Use | Recommendation | Rationale |
|---|---|---|---|---|
| **Policy / decision engine** (E-07, C-12) | Rules-as-code service. The six-rule tier policy (Skill 13 §1.1) plus versioned parameters. OP-02 console later. | Commercial decisioning platforms: **FICO**, where MSB went live in 10 months with a partner and cut turnaround from 30 to 15 min [T11]; **Experian PowerCurve**, which Techcombank selected in 2011 [T12]; **Provenir** loan origination/decisioning [T13]. | **`[DECISION REQUIRED]`, pending SP-9.** Default if HLB licenses nothing: **Build.** | The rule set is small and deterministic. A platform's advantages are built-in champion/challenger, audit and business-user rule editing (≈ OP-02). Against it: licence cost (not public), a 10-month precedent [T11], and a possible offshore SaaS TIA (§4). If HLB VN or HLB Group already licenses one, **reuse it**. |
| **Feature store** (C-04) | Custom tables plus a serving cache | **Feast**, open-source under Apache-2.0 [T14]. Managed cloud feature stores are not available in-country (§4) [T4]. | **Use Feast (open-source), self-hosted in Vietnam** | Point-in-time correctness and online/offline parity come out of the box [T14]. Licence cost is zero; infrastructure cost is DR. |
| **Event streaming** (C-03) | — | Kafka-class open-source bus, self-operated or offered by an in-country provider | **Use open source** | Nubank precedent [D3]. No managed streaming service is listed in the Hanoi Local Zone [T4]. |
| **Risk models** (scorecard, GBM, income estimator, behavioural) (C-05 to C-08) | In-house, with open-source libraries | Third-party alt-data scores, for example Trusting Social [R79] (Phase 3 only, PL-01) | **Build in-house** | CASA-derived signals are HLB-only data [R71]. Generic off-the-shelf scores underperformed on ecosystem data at Mercado Pago [R12]. Scaled analogues moved to in-house models [R12][R13]. The AI Law requires the bank to document and oversee the system [T17], which is easier with models it owns. |
| **Uplift model** (AW-03) | In-house | **CausalML** (Apache-2.0, ~6k stars) [T15]; **scikit-uplift** [T16] | **Build on the open-source libraries** | Mature libraries with Qini/uplift metrics. The need is holdout data, not tooling. |
| **eKYC** (AP-02) | — | **Reuse HLB's existing** National-ID-Registry eKYC (~80 s) [R67] | **Reuse** | Already live and compliant with biometric rules [Skill02 RF-4]. **Add** a web-to-app hand-off path (SP-3). |
| **Consent ledger** (E-01) | Append-only ledger plus purpose-check API | Consent platforms: OneTrust, CookieYes and Complianz lead web-consent share in Vietnam; AesirX and UniConsent market PDPL products [T23] | **Build the ledger.** Optionally buy a privacy-governance tool for DPIA/TIA workflow. | The ledger sits on the critical path of every CCP read and must be in-country (§4). The tools found are mainly web-cookie consent products [T23]. |
| **Integrity / fraud anomaly** (F5) | Rules plus an unsupervised anomaly score | Device-intelligence and fraud vendors (**not researched in this pass**) | **Build rules now; `[DECISION REQUIRED]` on a device-intelligence vendor** (OQ-14-06) | Biometric eKYC carries most of R-3 [L52]. |
| **Officer workbench** (OP-01) | New case UI | HLB's existing LOS or case tool, if any (unknown) | **Extend the existing tool if one exists (SP-9); otherwise build** | Workbench value comes from the pre-assembled evidence, not from a new UI. |
| **Model governance & monitoring** (GV-01) | PSI and fairness jobs, dashboards, registry | Decisioning-platform built-ins (if bought); conformity-assessment body (external, required if third-party certification applies [T17]) | **Build the monitoring. Buy the conformity-assessment service if required.** | Monitoring is simple arithmetic over the decision log. Certification is external by nature. |
| **Merchant checkout** (MX-01) | Widget, hosted page, QR deep link | — | **Build** | HLB-branded channel. It depends on HLB's API gateway. |

---

## 6. Blocker Register `[Rubric R4]`

Priority: **P0** = gates launch · **P1** = gates a Must feature's full design · **P2** = gates a Should/Phase-2 item · **P3** = Later.

| # | Blocker | Impact | Mitigation option | Priority |
|---|---|---|---|---|
| **B-01** | **AI Law conformity timing.** Decision 33/2026 lists "AI-powered credit scoring and lending systems that make automated credit decisions" as high-risk [T17]. It took effect 15 Aug 2026. The **1 Sep 2027** finance-sector deadline is reported as applying to systems **already in operation** before that date [T17]. The public summaries do not say what applies to a system first deployed after 15 Aug 2026. High-risk systems need a conformity assessment **before deployment**, technical documentation, logs, risk management and human oversight [T17][Skill02 RF-8]. | Upstream files (Skill 08 constraints, Skill 13 §4, GV-01) assume a 2027 deadline. If a new CreditPulse must pass assessment **before launch**, GV-01 is on the **critical path**, and ML components deployed without it are non-compliant. | (a) SP-7 legal opinion. (b) Plan GV-01 documentation from sprint 1. (c) **Staged launch:** a rules and scorecard champion with human refer band first, if counsel confirms that such a configuration falls outside, or more easily inside, the regime (OQ-02-11). ML challengers follow after assessment. (d) Budget for a registered conformity-assessment body `[DECISION REQUIRED]`. | **P0** |
| **B-02** | **Hosting and data localisation.** HLB's hosting model is unknown. Decree 53 requires in-country storage for domestic enterprises, and cloud hosted abroad is legally unclear [T1]. Circular 09/2020 sets cloud and outsourcing conditions [T2]. PDPL requires a TIA for any cross-border transfer [T20]. | Choosing region-bound managed services, or offshore model training, could force re-architecture or filings. | Portable open-source stack (§4). Default is in-country training and serving. SP-6. Record the decision as OQ-14-01. | **P0** |
| **B-03** | **CASA metadata (A-13-1 to A-13-4)** cannot be verified from outside HLB. S2 (salary payer), S4 (platform payouts), S6 (bill-pay), S7 (wallet top-ups) and S9 (MCC) need counterparty or category fields. VietQR carries an EMVCo MCC field [T19], but storage at HLB is unverified. | Without them, the "innovative CASA signals" (Skill 13 §1.2, rubric R3) shrink to amount- and date-based signals: S1, S3, S8, S10, S11. GIG and FTB lose most of their bank-observed evidence. | **SP-1** (2–3 weeks) before model design. The launch scorecard uses only signals that SP-1 confirms. Counterparty-name heuristics are a fallback, with an accuracy check. | **P0** (for E-02/E-06 design) |
| **B-04** | **Cold start / reject inference.** HLB's history contains approvals under today's manual policy. Thin-file applicants were mostly declined or never applied. Reject inference has only "modest scope", and augmentation "undermined predictive performance" [L20]. There is no PoP repayment history for PR-01, PR-02, the F5 ML model or AW-03. | Thin-file ML (the challenger's main value case, DD-02) cannot be trained at launch. ML claims in the one-pager must be framed as "measured after N months", not "at launch". | **Cold-start plan:** (1) THIN tier launches on a **judgmental starter policy + starter limits** (AP-05, low-and-grow [R1]). (2) A **randomised approval corridor**: approve a small random share of marginal applicants to collect unbiased outcome labels. This is the Karlan & Zinman experimental template [L36][Skill LR M6]. (3) Rules first for PR-02, F5 and RT-01. (4) Challenger training starts once the corridor has matured MOB3 outcomes. The corridor size, loss budget and minimum label count are `[DECISION REQUIRED — HLB risk appetite / model-validation policy]`. | **P0** (for E-06 challenger); P1 overall |
| **B-05** | **CIC dependency.** There is no public SLA, and the only latency figure is from 2019 (<10 s) [T6]. The **Sep 2025 incident was a confidentiality breach; SBV and CIC said services continued normally** [T21][R71]. Skill 13 FM-12 and AP-07 treat it as an availability precedent, which the evidence does not show. | Outage frequency and latency are unknown. The breach shows CIC data is a sensitive asset to minimise. | AP-07 fallback stays (a prudent design). Re-word its rationale (§9 U-02). SP-2 to measure. Minimise CIC data retention. Set an explicit timeout. | **P1** |
| **B-06** | **Web-checkout eKYC.** Web NFC works only in Chrome for Android and only with NDEF tags. It cannot read ISO-DEP chips such as the CCCD [T24]. Skill 13 IA sends MX-01 Options A and B (web widget, hosted page) straight to "eKYC/biometric". | New-to-bank shoppers on web checkouts **cannot complete chip-CCCD eKYC in the browser**. They must switch to a native app, which may need installing. Unmeasured latency and drop-off, and a risk to p90 and to R-3 "without friction". | SP-3. Options: (a) QR or deep link to HLB Connect (install for Lane B); (b) native HLB eKYC SDK inside partner merchant apps; (c) the MPS/VNeID electronic-authentication service, which Vietcombank already uses [T25]. Update Skill 13 IA (§9 U-01). | **P0** (for MX-01 Lane B) |
| **B-07** | **Core-banking integration (A-13-10).** Loan booking, disbursement to the merchant, and repayment events. | Without real-time booking, the "order completes" step of Flow 3 cannot happen in-session. Without events, PR-01 runs daily, which is acceptable. | SP-4. If booking is batch-only, add an approve-and-guarantee step: the merchant receives a payment guarantee and booking completes asynchronously (`[DECISION REQUIRED — HLB ops/legal]`). | **P1** |
| **B-08** | **Sổ Bán Hàng data access.** The partnership exists [R66][T22], but data fields, API and consent mechanics are not public. | MSME S5 falls back to CASA receipts. | SP-8. | **P2** |
| **B-09** | **Open API counterparties.** Other banks must comply by 1 Mar 2027 [R73]. | E-03 value depends on external readiness. | Keep E-03 as Should. Build the client when counterparties are live. | **P2** |
| **B-10** | **Decree 94 sandbox applicability** for Phase-3 data (OQ-02-17) [R74] | Phase 3 only | Unchanged (PL-01/PL-02) | **P3** |

---

## 7. Effort Re-estimate (v1 features) `[Rubric R4]`

> ⚠️ **These are engineering ESTIMATES by this skill (est.), not HLB IT figures.** They cover build, integration and test engineering plus data-science time. They **exclude** HLB policy-setting, legal work, independent model validation, and the fees and time of an external conformity-assessment body. Band mapping follows Skill 11: S ≈ ≤5 pw, M ≈ 6–12, L ≈ 13–24, XL ≈ >24. The midpoint of each range decides the band.

### 7.1 Must-have features

| Code | Feature | Skill 11 band (pw) | **Skill 14 estimate (pw, est.)** | Skill 14 band | Change? | Basis |
|---|---|---|---|---|---|---|
| E-01 | Consent ledger | M (8) | 8–12 | M | = | Ledger + purpose-check API + HLB Connect consent UI + revocation propagation |
| **E-02** | Phase-1 ingestion | L (16) | **24–40** | **XL** | **▲** | 6+ sources (CASA, cards, loans, HLB Connect events, Sổ Bán Hàng, CIC, application). Batch and stream. Historical backfill for training. In-country platform. SP-1/SP-4 dependencies. |
| E-05 | CCP generator | L (16) | 12–16 | L | = | CCP store, versioned history, purpose-scoped API |
| E-06 | Hybrid scoring | XL (32) | 32–48 | XL | = | Champion scorecards per segment and tier, GBM challenger, income estimator, serving. **Sequencing change:** challenger training waits for corridor labels (B-04). |
| E-07 | Decision-tier policy | M (8) | 8–12 | M | = | Rules-as-code. Less if a platform is reused (SP-9). |
| E-08 | Profile refresh loop | M (8) | 8–16 | M (upper) | = (risk ▲) | Rises to L if core banking cannot emit events (B-07) |
| **E-09** | Reason codes | S (4) | **6–10** | **M** | **▲** | Challenger attribution → customer reasons; Vietnamese copy; legal review |
| AW-01 | Target list | M (8) | 6–8 | M | = | Batch query + campaign workspace |
| AW-02 | Suitability + holdout | S (4) | 3–5 | S | = | Random assignment + logging |
| AW-04 | Pre-approved limit in app | M (8) | 8–12 | M | = | Offer card and re-validation on open. The hub shell is costed separately below. |
| CO-01 | Package builder | M (8) | 6–10 | M | = | Deterministic generator |
| CO-02 | All-in cost & schedule | S (4) | 3–5 | S | = | APR method per HLB disclosure practice |
| AP-01 | In-session decision | L (16) | 16–24 | L | = | **Skill 11 asked whether AP-01 could be M. It cannot.** Orchestration across eKYC, CIC, features, scoring, policy, booking and merchant callback, with timeouts and idempotency. |
| **AP-02** | eKYC (reuse) | S (4) | **8–12** | **M** | **▲** | The reuse itself is S. The **web-to-app hand-off** (B-06/SP-3) and the merchant-app SDK path add M. |
| AP-03 | Integrity checks | M (8) | 8–12 | M | = | Rules plus an unsupervised score at launch |
| AP-04 | Refer-band handling | M (8) | 6–8 | M | = | Refer state, counter-offer, notifications, merchant hold message |
| AP-05 | Starter limit | S (4) | 2–4 | S | = | Policy parameter |
| AP-06 | CASA-first onboarding | S (4) | 4–6 | S | = | Reuses in-app account opening [R66][R67] |
| AP-07 | CIC fallback | S (4) | 3–5 | S | = | Cache with retention policy + timeout |
| PR-01 | Behavioural re-score | M (8) | 8–12 | M | = | Rules/scorecard refresh first (B-04) |
| **PR-02** | Early warning | L (16) | **6–10** | **M** | **▼** | **Only if** v1 uses rule triggers (§9 U-04). The ML predictor is deferred until labels exist. |
| PR-03 | Graduation ladder | M (8) | 6–10 | M | = | Offer and consent flow; CIC check at offer |
| PR-04 | CIC reporting | S (4) | 3–6 | S | = | New product mapping in the existing channel |
| **RT-01** | Recommendations | L (16) | **6–10** | **M** | **▼** | **Only if** v1 uses a rules-based category suggestion (Skill 11 dissent; §9 U-04) |
| RT-02 | Anti-stacking gate | S (4) | 3–5 | S | = | Rule over a CIC pull |
| AD-01 | Repayment-conditioned invite | M (8) | 6–10 | M | = | Referral ledger + link + reward release |
| OP-01 | Officer workbench | M (8) | 8–16 | M | = | L if no existing case tool (SP-9) |
| **MX-01** | Merchant checkout | L (16) | **24–32** | **XL** | **▲** | Three integration types, merchant onboarding kit, mTLS/keys, security review, B-06 redesign |
| **GV-01** | Model governance | L (16) | **20–32** | **XL** | **▲** | Drift and fairness jobs, registry, decision log, **plus AI Law technical documentation and conformity-assessment preparation** that may now be pre-launch (B-01) |
| | **Sum, 29 Must features** | **276 pw** (Skill 11 bands) | **261–408 pw** | | | |
| — | **Platform foundation** (no feature code) | — | **16–24** | L | **new** | In-country environments, Kubernetes, CI/CD, observability, Circular 09 security-level classification and risk assessment (§4) |
| — | **HLB Connect "Pay Later" hub shell** (no feature code) | — | **6–10** | M | **new** | Navigation, My plans, Why this decision, Privacy & consent screens (Skill 13 IA §3) |
| | **Total v1 Must incl. platform** | | **≈ 283–442 pw** | | | |

### 7.2 Should-have features

| Code | Skill 11 band | Skill 14 estimate (pw, est.) | Band | Change? | Note |
|---|---|---|---|---|---|
| E-03 Open API | L (16) | 16–24 | L | = | Circular 64 client [T18]; depends on counterparties |
| AW-03 Uplift | L (16) | 12–16 | L | = | Open-source libraries [T15][T16]; needs holdout data |
| AW-05 Partner pre-selection | L (16) | 12–16 | L | = | Only if a partnership exists (D-08-2) |
| CO-03 Comparison | S (4) | 3–5 | S | = | UI |
| RT-03 Ready-limit dashboard | S (4) | 3–5 | S | = | UI over the CCP |
| AD-02 Refer-a-merchant | M (8) | 6–10 | M | = | Depends on SP-8 |
| AD-03 Good-payer passport | L (16) | 10–16 | L | = | Gated on affordability (FM-7) |
| OP-02 Policy console | M (8) | 6–10 | M | = | Near zero if a decisioning platform is bought |
| **Sum, Should** | | **68–102 pw** | | | |

### 7.3 Material differences from Skill 11 (for re-scoring, OQ-11-02)

A one-band move halves or doubles RICE, because the bands double. Re-computed RICE uses Skill 11's own Value column ÷ the new band's pw:

| Code | Skill 11 band → Skill 14 band | Skill 11 RICE → re-computed RICE | Consequence |
|---|---|---|---|
| E-02 | L → **XL** | 15.00 → **7.50** | Still Must; drops from the top group |
| MX-01 | L → **XL** | 15.00 → **7.50** | Still Must |
| GV-01 | L → **XL** | 6.25 → **3.13** | Legal gate; placement unaffected (Must) |
| AP-02 | S → **M** | 50.00 → **25.00** | Still rank 1–2 |
| E-09 | S → **M** | 20.00 → **10.00** | Still Must |
| PR-02 | L → **M** (if rules-first) | 2.75 → **5.50** | Removes most of the Skill 11 RICE dissent |
| RT-01 | L → **M** (if rules-first) | 3.75 → **7.50** | Removes most of the Skill 11 RICE dissent |
| AP-01 | L → **L (confirmed)** | 18.75 unchanged | Skill 11's sensitivity case (M → rank 2) **does not apply** |

---

## 8. Infrastructure Cost Estimate `[Rubric R4]`

**No monthly total is given, and none can be given honestly.** Three inputs are missing: (1) monthly application and profile volumes, which the brief does not give ([Brief p.9] gives mix and ticket range only); (2) HLB's hosting model (OQ-14-01); (3) HLB's contracted prices for CIC, SMS and cloud. Below is the **cost structure** with the only sourced unit prices.

### 8.1 Cost structure (components × drivers)

| Cost line | Driver | Unit price (sourced) | Formula | Launch / 12-month |
|---|---|---|---|---|
| **CIC enquiries** | Lane B applications + Lane A stacking checks per draw (RT-02) + graduation offers (PR-03) + monthly batch refresh, if contracted | **Current: not public.** Historical 40,000 VND per individual report (Decision 1669/2005, expired) [T7]. Fees cut repeatedly since 2015 [T7]. | (apps + draws + graduation offers + batch pulls) × CIC unit fee | `[DECISION REQUIRED — HLB's CIC contract + volumes]` |
| **National population DB authentication** | eKYC attempts (incl. retries) | **500 VND per authentication** at the reduced rate for 1 Dec 2023 – 31 Dec 2025 [T8]; the 2026 rate is not verified | attempts × fee | `[DECISION REQUIRED — confirm 2026 fee + volumes]` |
| **SMS / OTP** | OTPs + refer notices + reminders | Not sourced | messages × rate | `[DECISION REQUIRED — HLB's SMS contract]` |
| **Compute (serving)**: orchestrator, scoring, policy, CCP API, consent | Peak decisions per second, not total profiles | Not sourced for in-country providers | nodes × node-hour rate | `[DECISION REQUIRED — hosting model]` |
| **Online feature store / KV** | Profiles × features × bytes | Feast licence $0 (Apache-2.0) [T14]; infrastructure not sourced | memory-GB × rate | `[DECISION REQUIRED]` |
| **Event bus** | Events per second (CASA txns, repayments) | Open-source licence; infrastructure not sourced | brokers × rate | `[DECISION REQUIRED]` |
| **Offline / analytical store + training compute** | History months × customers; retraining cadence | Not sourced | storage-TB + compute-hours | `[DECISION REQUIRED]` |
| **Decision log / audit retention** | Decisions × retention years | Not sourced | storage-TB × rate | `[DECISION REQUIRED — retention period (legal)]` |
| **Decisioning-platform licence** (if bought) | Vendor model | Not public | — | `[DECISION REQUIRED — SP-9 / vendor quote]` |
| **Conformity assessment** (AI Law) | Per assessment / re-assessment on significant change [T17] | Not public | — | `[DECISION REQUIRED — registered body quote]` |
| **ML libraries** (CausalML, scikit-uplift, Feast) | — | $0 licence (open source) [T14][T15][T16] | — | $0 licence |

### 8.2 What can be said about scale

- **Scoring is not the cost driver.** Decision compute is milliseconds per decision [T9][T10]. External per-query fees (CIC, C06) and fixed platform and people costs dominate.
- **CIC volume grows with re-offers.** RT-02 and PR-03 add a CIC query per draw and per graduation offer on top of each new application. KR 1.3 (blended cost per decision) should include CIC and C06 fees per decision, so the "automated cost per decision" (OQ-04-03) is not understated.
- **The comparison that matters for rubric R2** is this. The 380,000 VND manual review [Brief p.9] is replaced by (CIC fee + C06 fee + amortised platform cost) per automated decision. The historical CIC fee (40,000 VND, 2005) [T7] and the C06 fee (500 VND, 2023–2025) [T8] suggest the external per-decision fees are an order of magnitude below 380,000 VND. **This is indicative only**, because the current CIC fee is unverified. `[Rubric R2]`

---

## 9. Required Upstream Changes (Skill 13 / 15 — not edited here)

| # | File / section | Current text (summary) | Required change | Reason |
|---|---|---|---|---|
| **U-01** | Skill 13 §3 IA (merchant checkout: "…→ eKYC/biometric (AP-02) → …"); §2 Flow 3 Lane B step 2; Skill 15 step 3.1 (B) ("Checkout → HLB hosted page / HLB Connect") | NFC eKYC implied in the hosted page or widget | Add an explicit **web-to-app hand-off** for chip-CCCD NFC (QR/deep link to HLB Connect, a native SDK in merchant apps, or VNeID authentication, per SP-3). Show it as a Lane B step, with its time counted in KR 1.2. | Browsers cannot read the CCCD chip [T24] (B-06) |
| **U-02** | Skill 13 Flow 3 error path "CIC unavailable (FM-12; CIC incident Sep 2025 [R71])"; AP-07 rationale; Skill 08/11 "one confirmed incident" | The Sep 2025 event is cited as an availability precedent | Re-word: the Sep 2025 incident was a **data breach; CIC services continued** [T21]. Keep AP-07 as prudent design (no public SLA). Add **data minimisation of CIC data** as the lesson. | Accuracy (B-05) |
| **U-03** | Skill 13 §4 DF-2 ("CIC pull (async where cached is valid)") | Sequential eKYC → CIC | Make the **CIC pull run in parallel with liveness and face match** once the chip read returns the ID number. Add a **CIC timeout** parameter `[DECISION REQUIRED]`. | Latency budget §2 |
| **U-04** | Skill 13 §1 F5, §2 Flow 4 (PR-02 "predicted missed payment"), Flow 5 (RT-01 F4); Skill 12 method table | ML from launch | Launch **PR-02, RT-01 and the F5 anomaly layer as rules**, and move to ML once labelled outcomes exist. State that the E-06 challenger for THIN tiers trains on the randomised approval corridor. | Cold start (B-04); Skill 11 dissents |
| **U-05** | Skill 13 §1.1 tier policy / AP-05 | Starter limit only | Add the **randomised approval corridor** as an explicit, logged policy branch for marginal THIN applicants. Its size and loss budget are `[DECISION REQUIRED]`. | Reject inference [L20][L36] (B-04) |
| **U-06** | Skill 13 §4 "Technology choices are capability-level… illustrative" | Kafka-class and feature-store examples | State the constraint: **portable, in-country-hostable components**, and no region-bound managed services unless an in-country offering exists. | §4 (B-02) |
| **U-07** | Skill 08 constraints ("conformity assessment… by 1 Sep 2027"); Skill 13 §4 decision log / GV-01; Skill 02 RF-8 | 1 Sep 2027 deadline assumed | Qualify: the 2027 grace period appears to cover systems **already operating** before 15 Aug 2026 [T17]. For a new CreditPulse, conformity assessment may be **pre-launch**. Pending SP-7. | B-01 |
| **U-08** | Skill 10 KR 1.2 definition | "application submit to auto-decision" | Define whether "submit" is before or after eKYC. Add a companion measure, "checkout tap → order complete". | §2 ambiguity (OQ-14-03) |
| **U-09** | Skill 11 effort bands; Skill 16 epics | Judgment bands | Re-score E-02, MX-01, GV-01, AP-02 and E-09 up, and PR-02 and RT-01 down (if U-04 is adopted). Add "Platform foundation" and "Pay Later hub shell" backlog items. | §7.3 |
| **U-10** | Skill 13 §9 A-13-10 | "Integration effort (Skill 14)" | Upgrade the A-13-10 risk: in-session **booking** has no acceptable fallback (B-07). Add the approve-and-guarantee option to the decision list. | B-07 |

---

## 10. Open Items Raised by This Skill

| # | Item | Why it matters | Carry to |
|---|---|---|---|
| OQ-14-01 | **Hosting model**: HLB DC, an in-country provider (Viettel IDC, FPT Cloud), the AWS Hanoi Local Zone, or a mix. Also, is any offshore processing (for example HLB Group data science) intended? | Drives architecture, cost, Circular 09 obligations and PDPL TIA filings (B-02) | Skill 22 `[DECISION REQUIRED]` |
| OQ-14-02 | **AI Law timing and pathway** for a newly deployed CreditPulse: pre-launch conformity assessment? Third-party or self-assessment? Does the refer band change the classification? | May put GV-01 and an external assessment on the launch critical path (B-01) | Skill 21 (risk) / 22 |
| OQ-14-03 | **KR 1.2 definition**: is eKYC inside "submit"? Is e-contract/OTP measured? | Changes what the p50 ≤ 3 min target tests (§2) | Skill 18 (NFR) / 22 |
| OQ-14-04 | **CIC**: current per-query fee, H2H latency distribution, and a timeout value for AP-07 | Cost model (§8) and p90 (§2) | SP-2 → Skill 20 / 22 |
| OQ-14-05 | **Web-checkout eKYC path** for new-to-bank shoppers: app hand-off, merchant-app SDK, or VNeID authentication | p90, abandonment, R-3 "without friction" (B-06) | SP-3 → Skill 20 / 22 |
| OQ-14-06 | Buy a **device-intelligence / fraud vendor** for F5, or build? (Vendors not researched.) | Integrity-score quality (G-2) | Skill 22 |
| OQ-14-07 | **Randomised approval corridor**: size, loss budget, minimum label count before challenger training | The cold-start plan cannot be sized without HLB risk appetite (B-04) | Skill 22 `[DECISION REQUIRED]` |
| OQ-14-08 | **Decisioning platform / LOS inventory**: does HLB already license FICO, Experian, Provenir or similar? | Build-vs-buy for E-07, OP-01 and OP-02 (§5) | SP-9 → Skill 22 |
| OQ-14-09 | **Monthly volumes**: applications, draws, profiles | Needed for any infrastructure cost number (§8) and capacity NFRs | Skill 18 / 22 |
| OQ-14-10 | **Decision-log retention period** | Storage cost; AI Law logs; disputes | Skill 19 / 22 |
| OQ-14-11 | If core banking cannot book in real time, is an **approve-and-guarantee** step (merchant paid, booking asynchronous) acceptable? | Flow 3 "order completes" (B-07) | SP-4 → Skill 22 |

Resolves (partly) upstream items: **OQ-13-04** becomes SP-1 (not closable from outside HLB). **OQ-11-02** is answered by §7. **OQ-15-03**: latency is checked (§2), and integration assumptions are converted into spikes (§1.3).

---

## Source Index

| # | Source | URL | Used for |
|---|---|---|---|
| T1 | Tilleke & Gibbins, "What Do Vietnam's New Data Localization Requirements Mean for Domestic Enterprises?" (Decree 53/2022) | https://www.tilleke.com/insights/what-do-vietnams-new-data-localization-requirements-mean-for-domestic-enterprises/ | Domestic enterprises must store regulated data in Vietnam; offshore cloud unclear |
| T2 | Mondaq, "Vietnam Steps Up Information Security For Banks" (Circular 09/2020/TT-NHNN) | https://www.mondaq.com/financial-services/1249184/vietnam-steps-up-information-security-for-banks | Cloud/third-party conditions: classification, backups, contract terms, annual assessment |
| T3 | LuatVietnam, Circular 09/2020/TT-NHNN text; Google Cloud SBV Circular 09 compliance mapping (existence) | https://english.luatvietnam.vn/circular-no-09-2020-tt-nhnn-dated-october-21-2020-of-the-state-bank-of-vietnam-promulgating-the-security-of-information-system-in-banking-operations-192825-doc1.html ; https://services.google.com/fh/files/misc/sbv_circular_no_9_2020_tt_nhnn_gcp_compliance_mapping.pdf | Circular 09 scope (effective 1 Jan 2021); provider criteria (enterprise, infrastructure compliant with Vietnamese law, international infosec certificate) |
| T4 | W.Media, "AWS launches Local Zone in Hanoi…" (22 Jun 2026); Vietnam News, "AWS Local Zone in Hà Nội…" (search extract: VIB, VPBank early adopters); Datacenter Map, Viettel IDC portfolio; FPT Telecom, VPBank Direct Connect | https://w.media/aws-launches-local-zone-in-hanoi-to-expand-cloud-infrastructure-in-vietnam/ ; https://vietnamnews.vn/economy/1784612/aws-local-zone-in-ha-noi-unlocking-ai-for-vietnamese-businesses.html ; https://www.datacentermap.com/cloud/viettel-idc-product-service-portfolio-2025/ ; https://fpt.vn/tin-tuc/fpt-telecom-deploys-direct-connect-channel-solution-for-vpbank-6680.html | In-country hosting options; Local Zone service list |
| T5 | AWS, Techcombank case study; The Asian Banker | https://aws.amazon.com/solutions/case-studies/techcombank-case-study/ ; https://www.theasianbanker.com/updates-and-articles/techcombank-partners-with-aws-to-become-a-cloud-first-organisation | Vietnamese bank precedent on public cloud |
| T6 | Tạp chí Ngân hàng, "CIC áp dụng phương thức cấp tin mới nhanh chóng, chính xác" (28 Mar 2019) | https://tapchinganhang.gov.vn/cic-ap-dung-phuong-thuc-cap-tin-moi-nhanh-chong-chinh-xac-8483.html | CIC H2H dedicated lines; retrieval < 10 s |
| T7 | Caselaw.vn, Decision 1669/2005/QĐ-NHNN on CIC service fees (expired); VnEconomy, "CIC giảm 123 tỷ đồng phí dịch vụ"; VietnamFinance, "CIC giảm 12% giá dịch vụ"; DIV | https://caselaw.vn/van-ban-phap-luat/225594-quyet-dinh-so-1669-2005-qd-nhnn-ngay-18-11-2005-cua-thong-doc-ngan-hang-nha-nuoc-viet-nam-ve-muc-thu-dich-vu-thong-tin-tin-dung-van-ban-het-hieu-luc ; https://vneconomy.vn/trung-tam-thong-tin-tin-dung-quoc-gia-giam-123-ty-dong-phi-dich-vu-ho-tro-khach-hang.htm ; https://vietnamfinance.vn/cic-giam-12-gia-dich-vu-ho-tro-cac-ngan-hang-ha-lai-suat-d14829.html ; https://div.gov.vn/cic-giam-gia-dich-vu-thong-tin-tin-dung-ho-tro-cac-tctd-giam-chi-phi-hoat-dong-gop-phan-giam-lai-suat-cho-vay-thuc-day-phat-trien-ktxh | Historical CIC fee (40,000 VND/report, expired); fee reductions; current schedule not public |
| T8 | Thư Viện Pháp Luật, "Mức thu phí xác thực thông tin công dân từ ngày 01/12/2023" (search extract; page returned 403 on fetch, Medium–Low) | https://thuvienphapluat.vn/phap-luat-doanh-nghiep/bai-viet/muc-thu-phi-xac-thuc-thong-tin-cong-dan-tu-ngay-01-12-2023-6420.html | 500 VND per authentication, reduced rate 1 Dec 2023–31 Dec 2025 |
| T9 | Akka, Capital One customer story (vendor-published) | https://akka.io/customer-stories/capital-one | Auto-loan decisions in 180–200 ms |
| T10 | Experian, "Saving Time with APIs" (Nov 2019, vendor blog) | https://www.experian.com/blogs/insights/2019/11/saving-time-apis | 15–20 ms per decision; ~3 s for new customers |
| T11 | FICO press release, "Vietnam Maritime Bank Achieves 200% Faster Loan Approvals with Intelligent Decisioning from FICO" (Jun 2026) | https://investors.fico.com/news-releases/news-release-details/vietnam-maritime-bank-achieves-200-faster-loan-approvals | Vendor decisioning in Vietnam; 10-month implementation; 30 → 15 min |
| T12 | Experian plc, "Techcombank selects Experian…" (2011) | https://www.experianplc.com/newsroom/press-releases/2011/techcombank-selects-experian-to-enhance-banking-operations-across-credit-lifecycle-in-vietnam | Experian decisioning in Vietnam (existence) |
| T13 | Provenir, Loan Origination datasheet | https://provenir.com/wp-content/uploads/2024/05/Loan-Origination-datasheet.pdf | Vendor existence |
| T14 | Feast (feast-dev) GitHub; Google Cloud blog, "Introducing Feast" | https://github.com/feast-dev ; https://cloud.google.com/blog/products/ai-machine-learning/introducing-feast-an-open-source-feature-store-for-machine-learning | Apache-2.0; offline/online stores; point-in-time correctness |
| T15 | Uber CausalML GitHub | https://github.com/uber/causalml | Apache-2.0; uplift meta-learners and trees; ~6k stars |
| T16 | scikit-uplift documentation / GitHub | https://www.uplift-modeling.com/en/stable/user_guide/introduction/index.html ; https://github.com/maks-sh/scikit-uplift | Uplift library; Qini/uplift metrics |
| T17 | Vietnam Briefing, "46 High-Risk AI Systems to Face Enhanced Regulatory Oversight in Vietnam" (9 Jul 2026); Allen & Gledhill, "Vietnam identifies high-risk AI systems across six sectors" (Aug 2026, search extract) | https://www.vietnam-briefing.com/news/46-high-risk-ai-systems-to-face-enhanced-regulatory-oversight-in-vietnam.html ; https://www.allenandgledhill.com/sg/publication/articles/33374/identifies-high-risk-ai-systems-across-six-sectors | Decision 33 banking high-risk systems; effective 15 Aug 2026; transition deadlines for systems already in operation; conformity assessment pathways |
| T18 | Thị trường Tài chính Tiền tệ, "Open API và sức mạnh của dữ liệu…" | https://thitruongtaichinhtiente.vn/open-api-va-suc-manh-cua-du-lieu-don-bay-kep-cho-tai-chinh-toan-dien-va-an-toan-he-thong-73859.html | Circular 64 technical standards (REST/JSON, OAuth 2.0, TLS 1.2+, JWS, level 3) |
| T19 | Bizhub/Vietnam News, "VietQR launched…"; EMVCo MPM payload (MCC field 52), via search extract | https://bizhub.vietnamnews.vn/vietqr-launched-to-promote-digital-transformation-in-banking-sector-post325152.html ; https://developers.ecommpay.com/en/pm_vietqr.html | VietQR complies with EMVCo; MCC field |
| T20 | EY Vietnam Legal Alert (Mar 2026), Decree 356/2025; Vietnam Briefing PDPL compliance guide 2026 | https://www.ey.com/content/dam/ey-unified-site/ey-com/en-vn/technical/tax/documents/ey-vietnam-legal-alert-march-2026-decree-no356-2025-nd-cp-providing-detailed-guidance-for-implementation-of-personal-data-protection-law-eng.pdf ; https://www.vietnam-briefing.com/news/vietnam-personal-data-protection-compliance-guide-2026.html/ | Cross-border transfer impact assessment; A05 filing within 60 days |
| T21 | VietnamFinance, "Ngân hàng Nhà nước lên tiếng về sự cố thông tin tín dụng tại CIC"; Thời báo Tài chính Việt Nam | https://vietnamfinance.vn/ngan-hang-nha-nuoc-len-tieng-ve-su-co-thong-tin-tin-dung-tai-cic-d132391.html ; https://thoibaotaichinhvietnam.vn/ngan-hang-nha-nuoc-chi-dao-xu-ly-su-co-thong-tin-tin-dung-tai-cic-183155.html | Sep 2025 CIC incident was a breach; CIC services kept running normally |
| T22 | The Leader, "Số hóa lấy con người làm trung tâm…" (HLB × Sổ Bán Hàng) | https://theleader.vn/so-hoa-lay-con-nguoi-lam-trung-tam-chia-khoa-de-trao-quyen-cho-tieu-thuong-d43821.html | HLB lending based on standardised Sổ Bán Hàng business data (intent; no API detail) |
| T23 | Wmtips, consent-management share in Vietnam; AesirX ComplianceOne; UniConsent Vietnam PDPL | https://www.wmtips.com/technologies/consent-management/country/vn ; https://www.aesirx.io/compliance-one ; https://www.uniconsent.com/vietnam-pdpl | Consent vendors (mainly web CMPs); PDPL tamper-evident consent records |
| T24 | Chrome for Developers, "Interact with NFC devices on Chrome for Android"; W3C Web NFC mailing list | https://developer.chrome.com/docs/capabilities/nfc ; https://lists.w3.org/Archives/Public/public-web-nfc/2019Nov/0161.html | Web NFC: Chrome for Android only, NDEF only; no ISO-DEP |
| T25 | VNBA, "Vietcombank tiên phong khai thác trực tiếp dịch vụ xác thực điện tử từ VNeID"; VietnamFinance | https://vnba.org.vn/vi/vietcombank-tien-phong-khai-thac-truc-tiep--dich-vu-xac-thuc-dien-tu--tu-vneid-14689.htm ; https://vietnamfinance.vn/vietcombank-la-ngan-hang-dau-tien-khai-thac-dich-vu-xac-thuc-dien-tu-cua-bo-cong-an-d112793.html | MPS/VNeID electronic authentication service available to banks |
| — | `_research-notes-07-13.md` [R#] (esp. R12, R13, R46, R48, R66, R67, R71, R73, R74, R77, R79) | local | eKYC ~80 s; Sổ Bán Hàng; CIC; Open API; Decree 94; Techcombank 3 min |
| — | `literature-review.md` [L20] Crook & Banasik 2004 / Banasik & Crook 2007; [L36] Karlan & Zinman; [L52] | local | Reject inference limits; randomised marginal approvals; biometrics and fraud |
| — | Skill 13 [D1], [D3], [D4], [D5]; Skills 02, 03, 08, 10, 11, 12; `_requirements.md` [Brief] | local | Components, flows, targets, effort bands, regulatory flags |
