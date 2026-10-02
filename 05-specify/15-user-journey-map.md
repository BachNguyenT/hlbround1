# User Journey Map
**Product:** HLB Point-of-Purchase Credit Decisioning — **HLB CreditPulse** | **Skill:** 15 | **Status:** Done (2026-09-30)
**Depends on:** solution-design.md (Skill 13), v1-product-scoping.md; Skill 14 (Technical Feasibility) **not run in this pass** (user-confirmed pipeline order SD-5) | **Feeds into:** epic-feature-decomposition.md

> This is the BACKBONE of the backlog. Every Epic in epic-feature-decomposition.md
> traces back to a journey stage defined here.

> **How to read this map**
> - **Future-state journey** with CreditPulse, six stages (SD-2).
> - **Two lanes** (D-08-1, provisional): **Lane A**, an existing HLB account-holder with no PoP history. **Lane B**, Anh Hùng (GIG), new to HLB, at a partner checkout. The lanes diverge only in Awareness and in Application & Instant Decision.
> - **Emotion column:** "today" emotions are **sourced** from Skill 03 quotes and evidence. "Target" emotions are **hypotheses**, tagged `(target)`, to be validated in a pilot.
> - **Pain column:** the evidenced pain the step addresses today. Pains that belong to Claude's **proposed** list (user assessment pending) are tagged `[Proposed — pending user]`. Their fixes are parked (PL-03 to PL-09), not designed.
> - Feature codes refer to Skill 08 / Skill 13. Citations: `[Skill03 S#]`, `[R#]` (`_research-notes-07-13.md`), `[J#]` (new sources for this skill), others as elsewhere.

---

## Pre-filled Context
- **Primary persona:**
  - Lane B: **Anh Hùng**, ride-hailing/delivery driver (GIG, 15.0% of inbound) [Skill05 Persona 1].
  - Lane A: **existing HLB account-holder with no PoP history**, typically salaried without credit history (SAL-N, 20.5%) [Brief p.7]. This is the anchor for Awareness and Retention.
  - Both assignments are provisional per D-08-1 and carried to Skill 22.
- **Secondary persona:** Chị Phụng (MSME) follows Lane A if she uses Sổ Bán Hàng with an HLB account, otherwise Lane B.
- **Internal operator:** Chị Lan (credit-risk lead) appears at the refer-band steps.
- **Product type:** Other. A bank-internal credit decisioning capability, distributed B2B2C through merchant/partner checkouts.

---

## Journey Overview

An existing HLB customer (Lane A) is invited, and only because CreditPulse judges the offer suitable and incremental, to pay over time at partner merchants, with a pre-approved limit visible in HLB Connect. A new-to-bank gig worker (Lane B) meets the offer at a partner checkout and is verified by chip-ID in about 80 seconds [R67]. He gets a starter limit decided within the session, without a payslip. Both then repay, with each instalment feeding back into their Customer Credit Profile. On-time payers are offered graduation and relevant repeat purchases; struggling payers are spotted early. Loyal payers can invite a friend or a favourite shop, and the referrer's reward unlocks only when the friend repays on time.

---

## 1. Full Journey Map

### Stage 1: Awareness
**Goal:** find out, from a trusted source, that I can pay over time for something I actually want, without being spammed.

| Step | User Action | System Response | Touchpoint | Emotion (today → target) | Pain (evidence today) | Opportunity / feature |
|---|---|---|---|---|---|---|
| 1.1 (A) | Uses HLB Connect normally, having given scoring (SC) and marketing (MK) consent | Nightly CCP refresh. AW-01 selects never-used, low-risk (F1 A/B), affordable (F2) customers. AW-02 applies the suitability filter and holds out a random group | — (back-end) | n/a | The brief requires offers "targeted and relevant rather than broadcast to all" [Brief p.6] | **AW-01, AW-02**: user-suggested pain point (Awareness) |
| 1.2 (A) | Receives an in-app card: "Pay over time up to {L} VND at {partner categories}" | Delivery-time consent re-check. The card shows the limit, the all-in cost range and eligible merchants | HLB Connect push/in-app (MK consent) | Today: irritated by blanket cross-sell. "…then they call constantly asking if I want to borrow money" [Skill03 S9] → (target) informed, not pressured | Unwanted cross-sell (Pain #9) `[Proposed — pending user]`; data-misuse worry, 49.5% [Skill03 S42] | **AW-04**; frequency caps PL-09 parked |
| 1.3 (A) | Opens the "Pay Later" hub on their own, with no MK consent | Pre-approved limit shown as a service display (DD-11) | HLB Connect "Pay Later" hub | (target) in control | Legal interpretation pending (OQ-13-01) | DD-11 |
| 1.1 (B) | Shops at a partner store or site; sees "Pay over time with HLB — from {x} VND/month" | Widget or product-page message (BNPL messaging on product pages primes shoppers [J1]) | Merchant widget / hosted page / in-store QR (MX-01) | Today: resigned. The formal option means documents and days. People turn to app credit because they "do not want to sign in loan procedures at banks" [Skill03 S47] | The decision arrives after the purchase moment (Pain #4) | **MX-01** |
| 1.2 (B) | Hears about HLB through a friend's invite link | Link opens the Lane B flow; invitee is scored independently | Invite link (RF consent of inviter) | (target) trusts a friend's recommendation | Reference-contact harassment fear (Pain #8) `[Proposed — pending user]` | **AD-01**: no reference contacts required |

**Stage emotion arc (target):** indifferent → curious → in control.

### Stage 2: Consideration
**Goal:** understand exactly what I'd pay and pick a plan that fits my income.

| Step | User Action | System Response | Touchpoint | Emotion (today → target) | Pain (evidence today) | Opportunity / feature |
|---|---|---|---|---|---|---|
| 2.1 | Taps "Pay over time" at checkout or from the offer card | CO-01 builds 2–4 packages from F1 (rate tier), F2 (max instalment), F6 (limit) and merchant 0% eligibility; tenors 3–24 months [Brief p.9] | Checkout / HLB Connect | Today: unsure what he can afford | No evidence yet that packages fit income (volatile income, Pain #10) | **CO-01**: user-suggested pain point (Consideration) |
| 2.2 | Compares packages | CO-02 shows monthly amount, payment dates, **total payable**, all-in APR, fees and late-payment consequences. No bundled insurance | Package screen | Today: angry about hidden costs: "Tôi cảm thấy… cực kỳ tức giận" (forced insurance) [Skill03 S45]; borrowed 9M, repaid ~14M [Skill03 S10] → (target) clear, trusting | Hidden or all-in cost opacity (Pain #7); brief: "fair and transparent cost" [Brief p.4] | **CO-02** (brief-mandated); CO-03 comparison (Should) |
| 2.3 | Basket exceeds what fits | Shows a larger down-payment, longer tenor or split basket. Never a package above F2 (G-4) | Package screen | (target) respected, not rejected | Over-borrowing risk (Pain #11) | CO-01 + G-4 |
| 2.4 | Selects a package | Package saved (30 min, A-13-6) → Stage 3 | Checkout | (target) committed | — | — |

**Stage emotion arc (target):** uncertain → clear → committed.

### Stage 3: Application & Instant Decision (the core bottleneck) `[Rubric R1]`
**Goal:** get a yes or no now, without documents, while I'm still at the checkout.

| Step | User Action | System Response | Touchpoint | Emotion (today → target) | Pain (evidence today) | Opportunity / feature |
|---|---|---|---|---|---|---|
| 3.1 (A) | Confirms with face biometric | Face match against the chip-CCCD on file (AP-02); F5 and F2 re-validated; CIC stacking check (RT-02) | HLB Connect | (target) effortless | Biometric failures on the day of rollout: "Không nhận ra khuôn mặt" ("face not recognised") [Skill03 S44] `[Proposed — pending user]` | **AP-02**; fallback PL-04 parked |
| 3.2 (A) | — | **INSTANT-APPROVE** in ≤ 3 min (p50 target, KR 1.2) | HLB Connect | Today: waits 1.8–4.6 business days [Brief p.9] → (target) relieved | Decision after the purchase moment (Pain #4) | **AP-01** |
| 3.1 (B) | Enters phone, taps chip-CCCD (NFC), takes a liveness selfie; chooses consents (SC required; MK/RF optional) | eKYC ~80 s (HLB's existing National ID Registry integration [R67]); F5 integrity score | Checkout → **hand-off to HLB Connect (QR / deep link) for chip NFC**, because browsers can't read the CCCD chip [Skill14 T24, U-01] | Today: "Bắt chụp chứng minh thư xong kh đc duyệt hồ sơ" ("made me photograph my ID, then the application wasn't approved") [Skill03 S22]; 31% can't or won't provide a labour contract, 33% a payslip [Skill03 S18] → (target) "no paperwork needed" | Income proof doesn't fit how he earns (Pain #1) | **AP-02, AP-03**: no document uploads (R-3) |
| 3.2 (B) | Waits in-session | CreditPulse builds the CCP from CIC (S14), checkout footprint (S13), application data, plus Open API data if he opts in (Phase 2) → F3 THIN → **INSTANT-APPROVE at STARTER LIMIT** | Checkout | Today: invisible to the bureau (Pain #2); informal credit approves in "20–30 minutes without requiring income verification" [Skill01 S5] → (target) **"a bank said yes to me"** ★ AHA | Invisible to the bureau (Pain #2) | **AP-01, AP-05** |
| 3.3 (B) | Accepts; opens an HLB account for disbursement and repayment | CASA-first onboarding (AP-06) → he becomes bank-observable (moves to Lane A over time) | HLB Connect | (target) belonging | Brief: onboard to CASA early to build observable records [Brief p.8] | **AP-06** |
| 3.4 | Signs the e-contract with OTP | Loan booked; merchant confirmed; order completes | Checkout | (target) done | — | MX-01 |
| 3.R | *Refer path:* sees "We need a little longer — reply within {SLA}" plus an affordable counter-offer | If the counter-offer is accepted, the flow continues. Otherwise Chị Lan (officer) reviews a pre-assembled case in OP-01; SLA ≥ 90% within 2 business hours (KR 1.4); merchant holds goods (A-13-7) | Checkout, SMS / in-app; officer workbench | (target) informed. Operator today: rebuilds every file by hand (Pain #13) → (target) reviews only uncertain cases | Manual file assembly (Pain #13); no standard for thin-file cases (Pain #12) | **AP-04, OP-01** |
| 3.D | *Decline path:* sees top reasons plus a generic path ("smaller basket / bigger down-payment may be approved") | Reason codes (E-09) | Checkout | Today: opaque rejection (Fundiin reviews [Skill03 S22]) → (target) understands why | Opaque rejection (Pain #6) `[Proposed — pending user]` | **E-09**; rich path-to-yes PL-05 parked |

**Stage emotion arc (target):** hopeful → effortless → relieved (AHA).

### Stage 4: Purchase & Repayment: continuous reassessment
**Goal:** repay without stress, and have my good behaviour count.

| Step | User Action | System Response | Touchpoint | Emotion (today → target) | Pain (evidence today) | Opportunity / feature |
|---|---|---|---|---|---|---|
| 4.1 | Sees his schedule and total payable | Loan reported to CIC (PR-04); schedule in "My plans" | HLB Connect | (target) oriented | 46% don't know late payment affects their credit rating [Skill03 S18] | PR-04; literacy nudges PL-08 parked |
| 4.2 | Pays an instalment | PR-01 re-scores on the repayment event (S15) → CCP version n+1 | HLB Connect / auto-debit | (target) progressing | — | **PR-01**: user-suggested pain point (continuous reassessment) |
| 4.3 | Has a slow week (GIG) | PR-02 early-warning flag → supportive reminder with date and amount plus a "talk to us" link; marketing and graduation suppressed | HLB Connect / SMS | Today: "Chậm ngày nào, tôi vẫn là con nợ… Nhưng không chạy xe, lấy tiền đâu mà trả" ("every day late I'm still a debtor… but if I don't drive, where does the money come from?") [Skill03 S1] → (target) supported, not threatened | Volatile income vs fixed instalments (Pain #10) `[Proposed — pending user]`; collection harassment (Pain #8) `[Proposed — pending user]` | **PR-02**; flexible schedules PL-06 parked (strong RCT evidence [R64]); conduct standards PL-07 parked |
| 4.4 | Completes N on-time instalments | PR-03 **offers** a graduation step (higher limit, longer tenor or better rate tier); nothing applies until he explicitly accepts (C2, [R30]) | HLB Connect "My limit" | (target) **"my limit grows because I paid on time"** ★ second aha | Thin-file customers lack a path to better credit | **PR-03**; Nubank low-and-grow [R1][D1 in Skill13] |

**Stage emotion arc (target):** oriented → supported → rewarded.

### Stage 5: Retention
**Goal:** use it again for something I need and can afford, and not get overloaded with offers.

| Step | User Action | System Response | Touchpoint | Emotion (today → target) | Pain (evidence today) | Opportunity / feature |
|---|---|---|---|---|---|---|
| 5.1 | Opens the Pay Later hub | "Ready limit" shows current headroom and next graduation step | HLB Connect | (target) confident | — | RT-03 (Should) |
| 5.2 | Sees up to 3 recommendations (MK consent) | RT-01: F4 purchase categories × F2 headroom × partner catalogue, all-in cost shown | HLB Connect "Offers for you" | Today: unwanted cross-sell (Pain #9) → (target) relevant | Cross-sell resentment (Pain #9) `[Proposed — pending user]` | **RT-01**: user-suggested pain point (Retention) |
| 5.3 | (Customer has other loans elsewhere) | RT-02 anti-stacking gate suppresses offers if CIC shows ≥ 2 concurrent consumer loans or rising obligations | — (back-end) | (target) protected, without knowing it | Debt stacking: a student with >100M VND across lenders [Skill03 S16]; CFPB stacking evidence [R36] | **RT-02** |
| 5.4 | Buys again via Stage 2 | Package pre-filled from the current CCP | Checkout | (target) habitual | — | CO-01 |

**Stage emotion arc (target):** confident → valued → habitual.

### Stage 6: Advocacy
**Goal:** share something that worked for me, without putting a friend at risk.

| Step | User Action | System Response | Touchpoint | Emotion (today → target) | Pain (evidence today) | Opportunity / feature |
|---|---|---|---|---|---|---|
| 6.1 | Taps "Invite someone" (eligible: N on-time payments, F1 A/B, no early-warning, RF consent) | Personal link generated | HLB Connect "Invite & earn" | (target) proud | Being a "reference" today means harassment: "Bị khủng bố đòi nợ vì làm 'tham chiếu'" [Skill03 vocabulary log, search-level] | **AD-01**: user-suggested pain point (Advocacy) |
| 6.2 | Friend applies | Friend scored independently (own CCP, own SC consent); the inviter's data is never used | Lane B flow | (target) safe | Referrals don't screen risk [R61] | DD-08 |
| 6.3 | Friend makes K on-time payments | Referral ledger releases the inviter's reward; the inviter sees only the status, never the friend's credit details | HLB Connect | (target) rewarded fairly | — | **AD-01**, enforcement effect [R61] |
| 6.4 | Suggests a favourite shop | Lead → shop onboarded via Sổ Bán Hàng as a PoP acceptance point (and potential MSME borrower); customer gets a merchant-funded perk after the shop's first PoP sale | HLB Connect → Sổ Bán Hàng | (target) community-minded | Merchants lose sales when financing isn't instant (Pain #15) `[Proposed — pending user]` | **AD-02** (Should); BNPL +~20% merchant sales [R57] |
| 6.5 | Shows a "good-payer passport" at a partner merchant | Passport unlocks graduation tier (gated on total-debt affordability) | HLB Connect | (target) recognised | Credit builders harm already-indebted users [R62] | **AD-03** (Should, gated) |

**Stage emotion arc (target):** proud → trusted → recognised.

---

## 2. Aha Moment

```
Aha Moment: Step 3.2 (Lane B) — a new-to-bank thin-file shopper (Anh Hùng) is approved in-session,
  at the checkout, for a starter limit that fits his income, with the total cost shown — without a
  payslip, labour contract or document upload. (Lane A equivalent: Step 3.2 (A), instant approval of
  a pre-approved package in ≤ 3 minutes.)
How we know it matters: it directly reverses the evidenced pains with the highest opportunity scores
  (Pain #1 "prove your income", Pain #2 "invisible to the bureau", Pain #4 "decision after the
  purchase moment" — all Opportunity 9 [Skill03 §3]) and the core bottleneck (single manual
  document-and-bureau workflow [Skill04 §4]); Grab reports 68% of its driver borrowers accessed
  formal credit for the first time through it (company self-reported) [R5], and Kredivo >60% first
  credit access [R13] — first formal "yes" is the value moment in analogous products.
Time-to-aha target: p50 ≤ 3 minutes from application submit to decision (KR 1.2; ASSUMPTION A-10-3),
  anchored on Home Credit ~3 min [Skill02 S9], Techcombank "approval and disbursement within 3
  minutes" [R77]; eKYC step ~80 s [R67]. Baseline today: 1.8–4.6 business days [Brief p.9].
What blocks users from reaching it:
  - eKYC failure on the customer's phone (Pain #5 [Skill03 S44]) — minimal v1 handling (3 retries →
    refer); full fallback parked (PL-04, pending user)
  - Refusing scoring consent (A2; 49.5% cite data-misuse as top worry [Skill03 S42])
  - Landing in the refer band (target ≤ 30%, A-10-2) or CIC unavailability (AP-07)
  - Basket larger than F2 headroom (handled by counter-offers, Stage 2.3)

Secondary aha (lifecycle): Step 4.4 — first graduation offer ("my limit grows because I paid on
  time"); evidence: Nubank low-and-grow [R1]; randomised limit expansion sustains instalment use [R50].
```

---

## 3. Critical Path

Minimum steps to the aha moment. This defines the highest-priority Epic (EP-3, with EP-0 as its dependency).

**Lane B (new-to-bank, primary):**
1. Select "Pay over time" at the partner checkout (1.1 B, MX-01)
2. Pick a package with the all-in cost shown (2.1–2.4, CO-01/CO-02)
3. Give scoring consent (E-01)
4. Chip-CCCD NFC + liveness (3.1 B, AP-02/AP-03)
5. In-session decision: INSTANT-APPROVE at starter limit (3.2 B, AP-01/AP-05) ← **AHA**

**Lane A (existing customer):**
1. Open the offer card or Pay Later hub (1.2 A / 1.3 A)
2. Pick a package (Stage 2)
3. Face biometric confirm (3.1 A)
4. INSTANT-APPROVE (3.2 A) ← **AHA**

---

## 4. Touchpoint Map

| Touchpoint | Type | Stages | Consent purpose | Feature |
|---|---|---|---|---|
| HLB Connect push / in-app offer card | Push notification / in-app message | Awareness, Retention | MK | AW-04, RT-01 |
| HLB Connect "Pay Later" hub (My limit, Offers, My plans, Invite, Passport, Why this decision, Privacy & consent) | App screens | All | SC (service); MK for Offers | IA §3 of Skill 13 |
| Merchant embedded widget / product-page message | Web / app UI | Awareness, Consideration | — | MX-01 |
| HLB hosted checkout page | Web UI | Consideration, Application | SC | MX-01, CO-01/02, AP-01/02 |
| In-store QR deep link | QR → app | Awareness → Application | SC | MX-01 |
| Chip-CCCD NFC + liveness screens | App UI | Application | SC | AP-02 |
| e-contract + OTP | App UI / SMS | Application | — | AP-01 |
| Refer-band status (SMS + in-app) | Notification | Application | SC | AP-04 |
| Repayment reminders / early-warning messages | Push / SMS | Purchase & Repayment | SC (servicing) | PR-02 |
| Graduation offer | In-app | Purchase & Repayment, Retention | SC | PR-03 |
| Invite link (shareable) | Deep link | Advocacy → Awareness (invitee) | RF | AD-01 |
| Sổ Bán Hàng merchant app | Partner app | Advocacy (AD-02), MSME data (S5) | Merchant consent | AD-02 |
| Officer workbench | Internal web app | Application (refer) | — | OP-01 |
| Campaign workspace / risk console | Internal web apps | Awareness, governance | — | AW-01/02, GV-01 |

---

## 5. Stage-to-Epic Seed List

| Journey Stage | Epic Name | Epic Goal | v1? |
|---|---|---|---|
| Cross-cutting (all stages) | **EP-0 CreditPulse Engine & Customer Credit Profile** | Consent-gated data ingestion (Phase 1), feature store, hybrid models, policy layer and CCP API that every stage consumes | v1 |
| Awareness | **EP-1 Pre-approval Targeting** | Invite never-used, low-risk, high-potential existing customers with suitable, incremental offers (holdout-measured) | v1 (uplift model = Should) |
| Consideration | **EP-2 Personalised Packages & Transparent Cost** | Show 2–4 affordable packages with the all-in cost before acceptance | v1 |
| Application & Instant Decision | **EP-3 In-Session Decision & Identity** | Decide within the checkout session, with chip-ID eKYC, integrity checks, starter limits, CASA-first and a human refer band | v1 (**critical path**) |
| Purchase & Repayment | **EP-4 Continuous Reassessment & Graduation** | Re-score on every repayment, warn early, offer consented graduation, report to CIC | v1 |
| Retention | **EP-5 Suitable Repeat Offers & Anti-Stacking** | Recommend affordable, relevant repeat purchases while suppressing stacking risk | v1 (ready-limit dashboard = Should) |
| Advocacy | **EP-6 Responsible Referral & Merchant Flywheel** | Repayment-conditioned invites (v1); refer-a-merchant and good-payer passport (Should) | v1 (partial) |
| Cross-cutting | **EP-7 Consent, Governance & Human Oversight** | Per-purpose consent ledger, reason codes, officer workbench, drift and fairness monitoring, AI Law documentation | v1 |
| Cross-cutting (channel) | **EP-8 Merchant & Partner Integration** | API widget, hosted page, QR deep link; merchant confirmation | v1 |
| (Parked) | **EP-P Proposed Pain-Point Enhancements** | PL-03 to PL-09: eKYC fallback, rich decline guidance, flexible repayment, collection conduct, literacy nudges, frequency caps, merchant portal | Later, **pending user assessment** |

---

## Open Items Raised by This Skill

| # | Item | Carry to |
|---|---|---|
| OQ-15-01 | All "target" emotions are hypotheses. They should be validated with pilot usability sessions and a CSAT survey after the decision. | Skill 22 (state as assumption) |
| OQ-15-02 | The lane-to-persona mapping follows the provisional D-08-1. If the user picks a different primary persona, the Lane B narrative changes, but the stages and epics do not. | Skill 22 |
| OQ-15-03 | Skill 14 (Technical Feasibility) did not run (SD-5). Latency (p50 ≤ 3 min) and integration assumptions (A-13-1 to A-13-4, A-13-10) have not been feasibility-checked. | Skill 14 / 22 |

---

## Source Index

| # | Source | URL | Used for |
|---|---|---|---|
| J1 | Baymard Institute, "Payment UX: Research-Backed Standards for Ecommerce Checkouts" (and current-state checkout UX) | https://baymard.com/blog/payment-ux ; https://baymard.com/blog/current-state-of-checkout-ux | BNPL messaging and payment-method grouping at checkout |
| — | `03-user-research.md` [Skill03 S#] | local | All sourced "today" emotions and pains (quotes S1, S9, S10, S16, S18, S22, S42, S44, S45, S47) |
| — | `_research-notes-07-13.md` [R#] | local | R1, R5, R13, R30, R36, R50, R57, R61, R62, R64, R67, R77 |
| — | Upstream Skills 01–13, `_requirements.md` [Brief] | local | Stage definitions, features, KPIs |

## Skill 22 Resolutions (User decision, Skill 22, 2026-09-30)
- **OQ-15-02:** lane-to-persona mapping confirmed (D-02).
- **OQ-15-03:** Skill 14 has now run; latency and integration assumptions have been checked (see Skill 14 §2, spikes SP-1 to SP-9).
- **Stage 4:** now includes the weekly payout-aligned repayment option (PR-05) and collection-conduct standards (PR-06) (D-01).
