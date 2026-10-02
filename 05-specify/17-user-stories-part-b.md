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
