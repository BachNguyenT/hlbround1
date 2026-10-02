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
