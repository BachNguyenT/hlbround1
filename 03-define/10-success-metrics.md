# Success Metrics & OKRs
**Product:** HLB Point-of-Purchase Credit Decisioning (working name) | **Skill:** 10 | **Status:** Done (2026-09-30)
**Depends on:** lean-canvas.md (run in parallel, per orchestration Wave 4), v1-product-scoping.md | **Feeds into:** product-prd.md (Section 8 — Business Context), solution-design.md

> **Rubric anchor.** The brief scores "operational KPIs that directly measure bottleneck relief, avoiding disconnected macro statistics" inside Solution Concept (35%) [Brief p.11]. Every metric below is tied to (a) the Skill 04 core bottleneck, (b) a brief requirement R-1 to R-4, or (c) a user-specified journey stage. Market-size and adoption statistics are deliberately excluded.
>
> **Targets.** The brief gives no HLB volumes, per-segment approval rates or loss rates (OQ-02-01 to 03, OQ-03-01). So every target is either:
> - **Benchmark-anchored proposed target**, labelled `ASSUMPTION A-10-#`. It is stated explicitly, as the brief permits ("Teams may make reasonable assumptions… but all assumptions must be stated clearly" [Brief p.10]), and carried to Skill 22 for confirmation.
> - **`[DECISION REQUIRED]`**, where the number is HLB's own policy choice (for example, risk appetite).
>
> Citations: `[R#]` = `_research-notes-07-13.md`; `[M#]` = new sources for this skill; others as elsewhere. Rubric tags are written as `[Rubric R#]`.

---

## 1. North Star Metric

```
Metric: Good In-Moment Loans (GIML) per month  — carried forward from Skill 01, extended to the
        user's lifecycle by counting repeat loans.
Definition: Count of PoP loans in the month that were
        (a) decided within the checkout session (auto-decision; see §2 KR 1.1 definition),
        (b) booked/disbursed, and
        (c) NOT 30+ days past due at month-on-book 3 (MOB3 30+ = 0),
        counting first and repeat loans alike; reported split by cohort (A existing / B new-to-bank)
        and by segment (SAL-C, SAL-N, GIG, MSME, FTB).
Why this represents value delivery: it rises only when speed (R-2), reach into thin files (R-1)
        and responsible outcomes (R-2 risk quality) improve together; repeat-loan inclusion makes
        it sensitive to the Retention/graduation stages the user added. Approval rate alone can be
        bought with loose credit; cost per decision alone can be gamed by declining everyone.
Benchmark (comparable products): HLB baseline = 0 in-moment decisions (1.8–4.6 business days)
        [Brief p.9]. In-market decision speeds: Home Credit ~3 min, Techcombank "approval and
        disbursement within 3 minutes", MoMo ~3 min, Fundiin ~5 s [Skill02 F1][R77]. No public
        competitor reports a GIML-equivalent count.
v1 target (3 months post-launch): [DECISION REQUIRED — absolute count depends on HLB pilot
        volume, which the brief does not give]. Proposed *relative* target (ASSUMPTION A-10-1):
        GIML ≥ 60% of all booked PoP loans by month 3, i.e. the majority of booked loans are both
        in-moment and performing — anchored on ACB auto-approving ~60% of applications in 5–10
        minutes [Skill03 S36].
```

---

## 2. OKR Cascade

Journey stages map as follows:
- **O1**: the core bottleneck, Application & Instant Decision.
- **O2**: R-1 thin-file inclusion.
- **O3**: the user's lifecycle stages (Awareness, Consideration, Purchase & Repayment, Retention, Advocacy).
- **O4**: responsible and compliant operation.

```
Objective 1 — Decide inside the purchase moment, at a cost that fits the ticket  [Rubric R1][Rubric R3]
  KR 1.1: In-session decision rate (share of applications receiving a final approve/decline
          before the checkout session ends; refer-band cases are NOT in-session)
          — Baseline: 0% [Brief p.9]  Target: ≥ 70% by v1+3 months (ASSUMPTION A-10-2: refer band
          ≤ 30%, anchored on ACB ~60% auto-approved [Skill03 S36] plus auto-declines)
  KR 1.2: Median (p50) time from application submit to auto-decision. **Definition (U-08, Skill 14):** "submit" = the customer confirms a package, before eKYC. So the measure includes eKYC, the web-to-app hand-off, CIC, scoring and policy, but excludes e-contract signing. Companion measure: "checkout tap → order complete" (end-to-end, including e-contract/OTP). Both are reported; only the first is targeted.
          — Baseline: 1.8–4.6 business days (range, not median) [Brief p.9]
            Target: p50 ≤ 3 minutes; p90 ≤ 10 minutes (ASSUMPTION A-10-3, anchored on Home Credit
            3 min [Skill02 S9], Techcombank 3 min [R77], ACB 5–10 min [Skill03 S36])
  KR 1.3: Blended cost per decision (VND) = (refer-band share × cost per referred case) +
          (auto share × cost per automated decision)
          — Baseline: 380,000 VND flat [Brief p.9]
            Target: ≤ ~114,000 VND + automated cost per decision, by v1+3 months
            (DERIVED: 30% refer share × 380,000 VND, conservatively assuming a referred case still
            costs today's full review; the automated per-decision cost is unknown — OQ-04-03 —
            so the full target is [DECISION REQUIRED once HLB's automated cost is known])
  KR 1.4: Refer-band SLA — share of referred cases decided within 2 business hours
          — Baseline: n/a (100% manual today)  Target: ≥ 90% (ASSUMPTION A-10-4; a design
          target for Skill 13, not benchmarked)

Objective 2 — Extend reliable, responsible credit beyond the bureau file  [Rubric R1][Rubric R3]
  KR 2.1: Thin-file approval rate (GIG + MSME + FTB = 44.0% of inbound [Brief p.9]) at MOB3 30+
          within the ceiling set in G-1
          — Baseline: not numerically stated ("lowest approval rates" [Brief pp.7–8])
            Target: [DECISION REQUIRED — needs HLB's current per-segment approval rates];
            measured as uplift vs the rules-only champion on a randomised challenger split, so the
            AI's incremental value is observable [Brief p.10]
  KR 2.2: Cohort-B CASA-first conversion — share of approved new-to-bank applicants who open and
          receive disbursement into an HLB account
          — Baseline: n/a  Target: [DECISION REQUIRED — no benchmark found]; tracked because the
          brief names CASA-first as the mitigation for observability [Brief p.8]
  KR 2.3: Evidence-confidence upgrade rate — share of Cohort-B customers whose profile moves from
          "thin" to "bank-observed" within 6 months
          — Baseline: n/a  Target: [DECISION REQUIRED]; leading indicator that the engine is making
          invisible customers visible (Exhibit 1 [Brief p.8])

Objective 3 — Reach and grow the right customers across the lifecycle  [Rubric R3]
  KR 3.1 (Awareness): Incremental take-up of pre-approved offers vs a random holdout
          (treated take-up − holdout take-up)
          — Baseline: n/a  Target: statistically significant positive uplift by first campaign
          cycle (ASSUMPTION A-10-5); rationale: propensity ≠ incrementality [R49]; uplift targeting
          cut mail volume ~60% while raising sales ~10% in one retail-bank case (vendor case, Low)
          [R53]
  KR 3.2 (Consideration): Package acceptance rate — share of shown personalised packages accepted
          — Baseline: n/a  Target: [DECISION REQUIRED]; guardrailed by G-4 (affordability)
  KR 3.3 (Purchase & Repayment): Early-warning cure rate — share of customers flagged by the
          early-warning model who, after proactive outreach, are current 60 days later
          — Baseline: n/a  Target: [DECISION REQUIRED]; evidence that proactive flexibility lowers
          default exists [R64] (feature PR-05 itself parked pending user assessment)
  KR 3.4 (Retention): Good-payer repeat rate — share of customers with ≥ 3 on-time instalments who
          take a second PoP loan within 6 months, with no increase in G-3 stacking
          — Baseline: n/a  Target: [DECISION REQUIRED]; randomised evidence that post-repayment
          limit expansion drives instalment use (11¢ per extra dollar of limit in Q1, 28¢ by year 3)
          [R50]
  KR 3.5 (Advocacy): Referral quality — MOB3 30+ of referred customers ≤ MOB3 30+ of non-referred
          customers in the same segment; referrer rewards paid only after the referee's on-time
          instalments (100% compliance)
          — Baseline: n/a  Target: parity or better (ASSUMPTION A-10-6; repayment-conditioned
          rewards produce an enforcement effect [R61]; referred customers showed ≥16% higher value
          and ~18% lower churn at a German bank [R60])

Objective 4 — Operate safely, fairly and compliantly  [Rubric R2][Rubric R4]
  KR 4.1: 100% of decisions carry reason codes; 100% of profile uses have a matching consent
          purpose in the consent ledger (C1, C3) — Baseline: n/a  Target: 100% (hard requirement)
  KR 4.2: AI Law technical documentation, human-oversight procedure and conformity assessment for
          the engine completed **before deployment**, and by 1 Mar 2027 at the latest. The 1 Sep 2027 grace period applies only
          to systems already operating before 15 Aug 2026 (corrected per Skill 14 U-07 / Skill 18 UC-18-01 [Skill18 N6]) — Target: complete
  KR 4.3: Fairness monitoring live: approval-rate and MOB3 30+ reported monthly by gender, age band
          and region, with a disparity alert (see G-5) — Target: live at launch
```

---

## 3. Guardrail Metrics

These must not degrade. Breaching one pauses the relevant feature (new targeting campaigns, graduation offers, or the specific segment's auto-approval) until it is fixed.

| # | Guardrail | Definition | Floor/ceiling | Anchor |
|---|---|---|---|---|
| G-1 | **MOB3 30+ delinquency** by segment and cohort | Share of loans 30+ days past due at month-on-book 3 | `[DECISION REQUIRED — HLB risk appetite]`. Reference points only: bank consumer-lending NPL ~4.1% (Feb 2024) [Skill02 S6]; Home Credit NPL 1.66% (2025) [Skill02 S7]; finco sector NPL 7.2% (2025) [Skill02 S3/S4]; SPayLater 90+ NPL 1.2–1.4% (2024) [R9] | Brief "maintaining risk quality" [Brief p.6] |
| G-2 | **Confirmed fraud rate** | Confirmed identity/application fraud per 1,000 approved loans | Must not rise vs the manual baseline `[DECISION REQUIRED: baseline unknown]` | R-3 [Brief p.6]; FM-11 |
| G-3 | **Stacking rate** | Share of PoP customers with ≥ 2 concurrent consumer loans at CIC after a HLB re-offer | Must not rise quarter on quarter | FM-3 [R35][R36] |
| G-4 | **Affordability breach rate** | Share of booked loans whose instalment exceeds the affordability-headroom field at booking | 0% (hard rule) | Responsible lending [Brief p.6]; C2 |
| G-5 | **Fairness disparity** | Ratio of approval rates (and of MOB3 30+) between any monitored group and the reference group | Alert if the approval-rate ratio falls below 0.8. This uses the four-fifths rule of thumb as an **alert trigger only**, not a legal standard in Vietnam. Vietnam has no algorithmic-audit standard [R76] | [L21][L22][L23]; ASSUMPTION A-10-7 |
| G-6 | **Marketing complaint rate** | Complaints per 1,000 marketing contacts (Awareness/Retention) | Must not exceed the pre-launch HLB baseline `[DECISION REQUIRED: baseline unknown]` | Skill 03 Pain #9 |
| G-7 | **Consent withdrawal rate** | Share of customers withdrawing marketing or data consent per month | Alert on a month-on-month spike | PDPL [R46] |
| G-8 | **Model drift (PSI)** on score and key inputs | Population Stability Index vs development sample | Alert at PSI 0.10–0.25 (investigate); action at PSI > 0.25 (retrain or override). Industry rule of thumb, acknowledged as lacking academic grounding [M1][M2] | FM-9 [R42][R45] |

---

## 4. Measurement Plan

| Metric | Tool (capability, not vendor) | Frequency | Owner | Alert threshold |
|---|---|---|---|---|
| GIML (North Star) | Loan-origination system + loan ledger joined to the decision log | Monthly (MOB3 lag) | Head of PoP Lending | Month-on-month decline for 2 consecutive months |
| KR 1.1 In-session decision rate | Decision-engine event log | Daily | Product owner, decision engine | < 60% for 3 days |
| KR 1.2 p50/p90 time to decision | Decision-engine event log (submit → decision timestamps) | Real-time dashboard | Platform engineering | p90 > 10 min for 1 hour |
| KR 1.3 Blended cost per decision | Finance cost allocation + refer-band volume | Monthly | Finance / Credit ops | > target for 2 months |
| KR 1.4 Refer-band SLA | Officer workbench (OP-01) queue metrics | Daily | Credit ops lead (OFFICER persona) | < 90% within 2 business hours |
| KR 2.1 Thin-file approval (champion vs challenger) | Decision log + experiment assignment | Weekly (approval); monthly (MOB3) | Credit risk | Challenger MOB3 30+ > champion + tolerance |
| KR 2.2 / 2.3 CASA-first, evidence upgrade | Core banking + CCP history | Monthly | Product owner | — (trend) |
| KR 3.1 Uplift vs holdout | Campaign system with persistent random holdout | Per campaign | Marketing analytics | Uplift not significant after 2 cycles → stop campaign |
| KR 3.2 Package acceptance | Checkout / HLB Connect events | Weekly | Product owner | — |
| KR 3.3 Early-warning cure rate | Collections system + model flags | Monthly | Collections lead | < prior quarter |
| KR 3.4 Good-payer repeat rate | Loan ledger | Monthly | Product owner | Rises together with G-3 → pause re-offers |
| KR 3.5 Referral quality | Referral ledger + loan ledger | Monthly | Growth lead | Referred MOB3 30+ > non-referred |
| KR 4.1 Reason codes / consent coverage | Decision log × consent ledger audit | Daily automated check | Compliance / DPO | Any decision without reason code or consent purpose |
| KR 4.3 / G-5 Fairness | Model-monitoring job | Monthly | Model risk | Ratio < 0.8 |
| G-8 Drift (PSI) | Model-monitoring job | Weekly | Model risk | PSI > 0.10 (investigate), > 0.25 (act) |
| G-3 Stacking | CIC pull at each draw + monthly portfolio CIC refresh | Monthly | Credit risk | Quarter-on-quarter increase |

---

## 5. Leading vs. Lagging Indicators

| Indicator | Type | What it predicts | How to act on it |
|---|---|---|---|
| In-session decision rate (KR 1.1) | Leading | GIML volume; merchant conversion | Tune refer-band cut-offs; add evidence sources |
| p50/p90 time to decision (KR 1.2) | Leading | Checkout abandonment | Performance fixes; bureau-fallback rules (AP-07) |
| Consent rate for data sources (per purpose) | Leading | Evidence confidence → thin-file approval | Consent-copy tests; explain the benefit ("share for a better offer") [L9][L15] |
| Evidence-confidence upgrade rate (KR 2.3) | Leading | Future pre-approval pool size | Push CASA-first onboarding |
| Uplift vs holdout (KR 3.1) | Leading | Incremental GIML from Awareness | Retarget or stop campaigns |
| Early-warning flag rate | Leading | MOB3 30+ | Proactive outreach; pause re-offers |
| PSI drift (G-8) | Leading | Future mis-grading | Recalibrate or roll back to the champion |
| MOB3 30+ (G-1) | Lagging | Loss rates, profitability | Tighten tier cut-offs by segment |
| Blended cost per decision (KR 1.3) | Lagging | Unit economics (Skill 09) | Rebalance refer share vs risk |
| Good-payer repeat rate (KR 3.4) | Lagging | Lifetime value | Adjust graduation ladder |
| GIML (North Star) | Lagging | Business-line viability | Portfolio-level review |

---

## Assumptions Introduced by This Skill (carried to Skill 22)

| # | Assumption | Anchor | Risk if wrong |
|---|---|---|---|
| A-10-1 | GIML ≥ 60% of booked PoP loans by v1+3 months | ACB ~60% auto-approved [Skill03 S36] | North Star target unrealistic |
| A-10-2 | Refer band ≤ 30% → in-session decision rate ≥ 70% | ACB ~60% auto-approval plus auto-declines | Cost and speed targets miss |
| A-10-3 | p50 ≤ 3 min; p90 ≤ 10 min | Home Credit 3 min, Techcombank 3 min, ACB 5–10 min | Checkout abandonment |
| A-10-4 | ≥ 90% of referred cases decided within 2 business hours | Design target, not benchmarked | Referred customers lost |
| A-10-5 | Positive, significant uplift within the first campaign cycle | Uplift literature [R49][R53] (vendor case, Low) | Awareness stage adds no incremental value |
| A-10-6 | Referred customers' MOB3 30+ ≤ non-referred | [R61][R60] | Referral harms risk quality |
| A-10-7 | 0.8 approval-rate ratio as the fairness alert trigger | Four-fifths rule of thumb; no Vietnam standard [R76] | Under- or over-alerting |

**`[DECISION REQUIRED]` items for Skill 22:**
- North Star absolute target
- KR 1.3 full target (needs HLB's automated cost per decision)
- KR 2.1, 2.2, 2.3, 3.2, 3.3 and 3.4 targets
- G-1 MOB3 30+ ceiling per segment (risk appetite)
- G-2 and G-6 baselines

---

## Open Items Raised by This Skill

| # | Item | Carry to |
|---|---|---|
| OQ-10-01 | HLB's current per-segment approval rates, MOB3 delinquency and funnel data are needed to turn the `[DECISION REQUIRED]` targets into numbers. For the submission they must be stated as assumptions. | Skill 22 |
| OQ-10-02 | The refer-band SLA (2 business hours) is a design target with no benchmark. Skill 13 must show how a referred customer is handled at the checkout (for example, a provisional hold). | Skill 13 / 22 |

---

## Source Index

| # | Source | URL | Used for |
|---|---|---|---|
| M1 | Yurdakul & Naranjo, "Statistical properties of the population stability index", *Journal of Risk Model Validation* 14(4) | https://files.wmich.edu/s3fs-public/attachments/u730/2022/PSIfinal.pdf | PSI 0.10/0.25 rule of thumb and its lack of academic grounding |
| M2 | "A critical review of existing and new population stability testing procedures in credit risk scoring" (ResearchGate) | https://www.researchgate.net/publication/368935591_A_critical_review_of_existing_and_new_population_stability_testing_procedures_in_credit_risk_scoring | PSI usage in credit scoring |
| — | `_research-notes-07-13.md` [R#]: R9 SPayLater NPL; R35/R36 over-indebtedness; R42/R45 drift; R46 PDPL; R49 Agarwal MPB; R50 Aydin; R53 uplift case; R60 Schmitt et al.; R61 Bryan–Karlan–Zinman; R64 repayment flexibility RCT; R76 Banking Academy gaps; R77 Techcombank | local | Benchmarks and design anchors |
| — | Upstream: `_requirements.md`, `02-market-research.md` (S3, S4, S6, S7, S9, F1), `03-user-research.md` (S36), `04-problem-research.md` §7, `literature-review.md` (L9, L15, L21–L23) | local | Baselines, KPI candidates, fairness evidence |

## Skill 22 Resolutions (User decision, Skill 22, 2026-09-30)

| Item | Resolution |
|---|---|
| D-11 KR 1.2 definition | **The p50 ≤ 3 min / p90 ≤ 10 min target applies end-to-end:** from the customer tapping "Pay over time" to the merchant order being confirmed. This matches how competitors advertise ("approval and disbursement within 3 minutes" [R77]). The system decision (submit → decision, excluding eKYC) has its own budget, NFR-PERF-02: p50 ≤ 8 s, p99 ≤ 20 s. The earlier KR 1.2 text and U-08 note are superseded. |
| D-04 `[DECISION REQUIRED]` targets and ceilings | Kept as **named parameters set by HLB at launch** (for example the G-1 MOB3 ceiling per segment and KR 2.x/3.x targets). No invented values. Illustrative values for the competition one-pager may be prepared separately, labelled as assumptions. |
| Assumptions A-10-1 to A-10-7 | Accepted as stated design assumptions |
