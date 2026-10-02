# Persona Research
**Product:** HLB Point-of-Purchase Credit Decisioning (working name) | **Skill:** 05 | **Status:** Done (2026-09-29)
**Depends on:** 01-product-strategy.md, 02-market-research.md, 03-user-research.md, 04-problem-research.md, literature-review.md | **Feeds into:** problem-statement.md, user-journey-map.md

> This skill is synthesis-first for the three borrower personas: `03-user-research.md` already gathered sourced, segment-specific pain points and verbatim quotes at full depth, and this skill's job is to assemble them into persona-card format and add what was missing (primarily the bank-side operator persona). Citations `[Skill0N S#]` point to that skill's Source Index; `[N#]` are new sources found specifically for this skill (listed at the end). No quote below is invented — where no verbatim quote exists, this is stated explicitly rather than filled. Rubric tags: `[R1]` Diagnosis · `[R2]` Justification · `[R3]` Concept · `[R4]` Assumptions.

---

## Pre-filled Context
- Primary persona hypothesis (elicitation): thin-file point-of-purchase borrower (gig/platform worker, online MSME merchant, or first-time digitally active borrower), plus an HLB Vietnam credit-risk/underwriting lead as internal operator.
- Problem hypothesis (Skill 01, tested and upheld by Skill 04): a branch-style, document- and bureau-based manual review (380,000 VND/application, 1.8–4.6 business days) cannot assess thin-file applicants, cannot decide within the purchase moment, and costs too much per decision for small tickets.

> The hypothesis is **upheld, not revised**: online research confirms all three borrower archetypes and adds a fourth, necessary persona (the bank operator) that elicitation only gestured at.

---

## How to read this document
Segment codes and the Confirmed/Partial/Hypothesis confidence rule are inherited unchanged from `03-user-research.md` §"How to read this document". Each persona card below states, per attribute, the Skill 03 pain point(s) or new source it rests on — nothing here is a freestanding claim.

---

## Persona 1 — GIG: "Anh Hùng", motorbike/delivery ride-hailing driver

```
PERSONA: Anh Hùng (archetype — composite of sourced driver voices, not one real individual)
Role: Ride-hailing / delivery driver (Grab-, Be-, Xanh SM-style platform), full-time
Segment: GIG — 15.0% of inbound applications [Brief p.9]
Seniority/tenure: Platform work as main income; >90% of ~5,000 surveyed platform workers report
  it as their main income source, but only ~20% carry social insurance [Skill03 S3]
Team size: Solo operator; income flows through one or more ride-hailing/delivery platform apps

GOALS (sourced):
- Cover urgent, lumpy costs (a phone repair, a family medical bill, a motorbike part) without
  losing riding time — evidence: an 11.6M VND phone loan repaid via 32,000 VND/day auto-deduction
  from the driver's platform wallet over 360 days [Skill03 S1]
- Be judged on what he actually earns, not on paperwork he doesn't have [Skill03 Pain #1]

FRUSTRATIONS (sourced):
- Income swings hard with demand: "Giờ ế lắm. Nhiều hôm tôi phải chạy xe liên tục 12 tiếng đồng hồ
  mới kiếm được 300.000 đồng" ("It's very slow now. Many days I ride 12 hours straight to earn
  300,000 VND") — Bảo Châu, 33, motorbike driver [Skill03 S1]
- Fixed instalments don't bend with income: "Chậm ngày nào, tôi vẫn là con nợ, phải trả thêm lãi.
  Nhưng không chạy xe, lấy tiền đâu mà trả" ("Every day late I'm still a debtor paying more
  interest. But if I don't drive, where does the money come from?") — Huy, driver [Skill03 S1]
- Lacks a labour contract or payslip: 31% of surveyed Vietnamese adults are uncomfortable or
  unable to provide a labour contract for a loan application, 33% for a payslip
  [Skill03 S18, n=1,200]
- No formal credit history to point to: CIC coverage reaches roughly 60% of adults on a 2022
  figure [Skill03 S35] — drivers without prior bank borrowing fall outside it

TOOLS USED (sourced):
- Ride-hailing/delivery platform app (income source and, per B2, an accepted repayment rail via
  wallet auto-deduction) [Skill03 S1]
- E-wallet(s) linked to the platform for payouts and bill payment [Brief p.7 "digital footprints
  including e-wallet top-ups"]

WORKFLOW:
Rides or delivers for long, variable hours; income is paid out through the platform, often daily
or weekly. When a lump-sum need arises, the fastest available options today are a
platform-embedded loan (wallet-deducted, ~20-30%/yr implied by cited terms) or informal/"black
credit" — going to a bank branch with documents he may not have is a weak fourth option
[Skill03 B1, B2].

SUCCESS CRITERIA:
Gets an amount he needs, sized to what he can actually repay day-to-day, without having to
produce a labour contract or payslip, and without losing riding income to a branch visit.

REAL QUOTES:
"Giờ ế lắm. Nhiều hôm tôi phải chạy xe liên tục 12 tiếng đồng hồ mới kiếm được 300.000 đồng."
  — Bảo Châu, motorbike driver — Tuổi Trẻ, 4 Aug 2023 [Skill03 S1]
"Chậm ngày nào, tôi vẫn là con nợ, phải trả thêm lãi. Nhưng không chạy xe, lấy tiền đâu mà trả."
  — Huy, driver — Tuổi Trẻ, 4 Aug 2023 [Skill03 S1]

DECISION MAKING:
Authority: Sole decision-maker on whether to borrow; no organisational buyer above him.
Budget/ticket size: Small-to-mid tickets within HLB's range — the sourced comparable is an
  11.6M VND phone loan [Skill03 S1]; likely concentrated at the lower end of 1.6M–90.1M VND.

TECH PROFICIENCY: Medium-High — lives inside platform and e-wallet apps daily, but is not
  necessarily comfortable with document-heavy, form-based banking apps (no direct evidence either
  way; inferred from platform-app fluency, flagged as inference).

DATA FOOTPRINT AVAILABLE FOR ALTERNATIVE SCORING:
- Platform payout history and frequency (held by the ride-hailing/delivery platform — access
  requires a data-sharing partnership and consumer consent; not yet sourced whether HLB has one)
- E-wallet transaction regularity (held by the wallet provider, e.g. MoMo/ZaloPay/ShopeePay)
- Telco usage/tenure (held by the telco) — the brief lists this as a signal type for the
  first-time-borrower segment generally [Brief p.8]; not confirmed specific to GIG
- CCCD/VNeID biometric identity (mandatory infrastructure per Skill 02 RF-4/RF-5)
- Literature support: alternative-data lift for thin-file, platform-income populations is the
  literature review's strongest and most consistent theme [L1, L2, L4, L9]

TYPICAL TICKET / PURCHASE USE-CASES WITHIN 1.6M–90.1M VND:
Phone or vehicle-parts purchase/repair (documented case: 11.6M VND, [Skill03 S1]), short-tenure
(likely 3–6 months given repayment-per-ride cadence), possibly financing a motorbike accessory
or a family emergency purchase at a partner merchant.

RESPONSIBLE-LENDING VULNERABILITIES:
Volatile daily/weekly income against fixed monthly instalments is a Confirmed pain [Skill03 Pain
#10, Opp. 6] — an affordability model built on a monthly-salary assumption would misprice or
over-extend this persona. He is already accepting wallet-deducted loans in the 20–30%/yr range
[Skill03 S1] — an HLB offer only helps him if it beats that price and fits his actual cash-flow
rhythm, not just his stated income.
```

---

## Persona 2 — MSME: "Chị Phụng", online/household-business seller

```
PERSONA: Chị Phụng (archetype — composite; named after the sourced case, not the same individual)
Role: Owner-operator of a small online shop (e.g. swimwear, apparel, or general goods sold via
  Shopee/TikTok Shop/Facebook and possibly a small physical storefront)
Segment: MSME (online merchant) — 14.5% of inbound applications [Brief p.9]
Seniority/tenure: Established household business; over 315,260 household businesses had
  registered cash-register e-invoicing by mid-2026, +56% vs end-2025 [Skill02 S50] — a sign this
  segment is moving from informal to semi-formal record-keeping right now
Team size: Solo or family-run

GOALS (sourced):
- Buy stock at the moment a good deal appears, before it disappears or a competitor buys first
  [Skill03 Pain #4]
- Be assessed on real, observable sales and cash flow rather than on financial statements or
  collateral she doesn't have [Skill03 Pain #3]

FRUSTRATIONS (sourced):
- Timing, not price, is the binding constraint: "Nhiều lúc nhìn thấy nguồn hàng đẹp, giá tốt
  nhưng vốn chưa xoay kịp. Chỉ chậm vài ngày là hàng hết hoặc đối thủ đã nhập trước rồi." ("Many
  times I see good stock at a good price but my capital hasn't turned around in time. A few days
  late and it's sold out or a competitor got there first.") — Ms Mỹ Phụng, swimwear shop owner,
  HCMC — Công Thương, 27 Jul 2026 [Skill03 S40]
- Banks can see her cash flow but still decline: "over 75% of businesses cannot borrow without
  collateral" and only 20.5% of SMEs access loans vs 61.4% of large firms — BVBank quote and
  statistic, VTV, 25 Sep 2026 [Skill03 S38]
- Cash flow is fragmented across channels: "When cash comes from multiple sources like transfers,
  QR codes, and e-commerce platforms, it becomes difficult to manage" without integrated systems,
  and many household businesses "still mix business cash flow with personal spending" —
  Techcombank director, Tuổi Trẻ, 30 Mar 2026 [Skill03 S39]

TOOLS USED (sourced):
- E-commerce seller dashboards (Shopee, TikTok Shop, Facebook-based selling implied by press
  coverage) [Skill03 S40, S41]
- Bank sales-ledger / e-invoicing tools — e.g. Techcombank's T-Shop [Skill03 S39]; HLB itself
  already has a Sales Ledger partnership with Sổ Bán Hàng [Skill01 §Strategic Context Brief]
- Platform seller-loan products as a workaround — Shopee SEasy, reviewed "trong vòng 24 giờ"
  (within 24 hours), from 1.17%/month, 3–12 months, eligible sellers only [Skill03 S41]

WORKFLOW:
Runs day-to-day sales through one or more online channels; restocks opportunistically when
suppliers offer good pricing. A financing decision that arrives a few days late is functionally
a "no" — the stock window has closed by the time an answer comes [Skill03 B10].

SUCCESS CRITERIA:
Gets working capital fast enough to act on a stock opportunity, using her actual sales history as
proof rather than needing to produce financial statements, tax filings, or collateral she may not
have [Skill03 S38 — "no management system, no financial statements, or no tax filings" are
commonly cited refusal reasons].

REAL QUOTES:
"Nhiều lúc nhìn thấy nguồn hàng đẹp, giá tốt nhưng vốn chưa xoay kịp. Chỉ chậm vài ngày là hàng
hết hoặc đối thủ đã nhập trước rồi." — Ms Mỹ Phụng, swimwear shop owner — Công Thương, 27 Jul 2026
[Skill03 S40]

DECISION MAKING:
Authority: Sole owner-operator decision.
Budget/ticket size: Likely spans a wider range than the other two personas, from small
  restocking purchases to larger inventory/equipment buys — not independently sourced by ticket
  size for Vietnam (see open items).

TECH PROFICIENCY: High for e-commerce/seller-platform tools (she runs her business through them
  daily); proficiency with formal bank lending processes is not evidenced either way.

DATA FOOTPRINT AVAILABLE FOR ALTERNATIVE SCORING:
- E-commerce platform sales history (held by Shopee/TikTok Shop/Lazada — requires a data
  partnership and seller consent)
- Sales-ledger / e-invoice data — HLB's own Sổ Bán Hàng partnership is a differentiator no
  sourced competitor has for merchant lending [Skill02 Whitespace 3]; post-2026 household-business
  tax reform makes e-invoiced revenue a lawful, verifiable signal [Skill02 RF-13]
- CASA transaction history if onboarded early — this is the brief's own stated mitigation:
  "onboarding to a CASA account early to build observable transaction records prior to credit
  decisions" [Brief p.8]
- Literature support: cash-flow/inflow scoring using CASA, platform or e-invoice data [L11, L13]
  and case data showing HLB's existing Sales Ledger asset [Skill01]

TYPICAL TICKET / PURCHASE USE-CASES WITHIN 1.6M–90.1M VND:
Inventory/stock restocking, packaging and fulfilment supplies, small equipment — timed to a
sales or sourcing opportunity; likely mid-range tickets with tenure matched to inventory turnover
(3–12 months plausible, not independently sourced for Vietnam MSME point-of-purchase credit).

RESPONSIBLE-LENDING VULNERABILITIES:
Because household-business cash flow is frequently mixed with personal spending [Skill03 S39],
a purchase-finance product must be clear about whether it is financing business inventory or a
personal purchase — the case brief does not resolve this, and it changes both the product fit and
the data used (see open item, inherited from Skill 03's OQ-03-10).
```

---

## Persona 3 — FTB: "Hữu Tùng", first-time digitally active borrower

```
PERSONA: Hữu Tùng (real first name from the sourced case; treated as an archetype for a wider
  population of similar young, first-time borrowers, not a single real individual to be profiled)
Role: University student / early-career young worker, HCMC
Segment: FTB (first-time digitally active borrower) — 14.5% of inbound applications [Brief p.9]
Seniority/tenure: New to formal credit; "invisible to the bank until they submit a credit
  application" [Brief p.8]

GOALS (sourced):
- Solve an immediate money need (the sourced case: 10M VND) without a bureau history to draw on
  [Skill03 S14]
- Build toward being able to borrow more in future

FRUSTRATIONS (sourced):
- Initial relief, then regret: "Lúc đó tôi cảm thấy nhẹ nhõm vì đã giải quyết được vấn đề trước
  mắt" ("At the time I felt relieved to have solved the immediate problem"), later, "Tôi cảm thấy
  hối hận khi lựa chọn vay kiểu này. Nó không hề an toàn hay đơn giản như tôi tưởng" ("I regret
  borrowing this way. It wasn't safe or simple at all as I thought") — Hữu Tùng, student, 10M VND
  at 4–5%/month — Tiền Phong, 23 Apr 2025 [Skill03 S14, S15]
- Threats over late payment: "Chỉ cần trả nợ chậm một vài hôm, bên cho vay đã gọi điện đe dọa sẽ
  khóa iCloud" ("Just a few days late and the lender called threatening to lock my iCloud") —
  same source [Skill03 S14]
- Low awareness of consequences: 46% of borrowers don't know missed payments affect their credit
  rating; 57% don't know it affects future credit access [Skill03 S18]
- Debt-stacking risk: a student accumulated >100M VND of debt across banks, fincos and illegal
  apps [Skill03 S16]; FoMO and positive attitudes toward BNPL are documented predictors of
  impulsive use and elevated debt risk among Vietnamese university students [Skill03 S21]
- Identity-theft exposure: 13 students/new workers had their biometrics harvested via a fake
  e-wallet app and loans taken out in their names (~300M VND total) [Skill03 S17]

TOOLS USED (sourced):
- E-wallets, telco accounts, and utility bill payment — the brief's own list of signals for this
  segment: "e-wallet top-ups, telecom usage patterns, and on-time utility bill payments"
  [Brief p.8]
- BNPL/instalment apps as the accessible entry point to credit [Skill03 S20 — BNPL adoption among
  surveyed online shoppers rose from 17% (2022) to 49% (2025)]

WORKFLOW:
Faces a specific need (device, course fee, emergency expense), has no bureau history to offer,
and turns to whichever channel asks for the least paperwork and answers fastest — which today is
rarely a bank.

SUCCESS CRITERIA:
Gets a fair first "yes", sized conservatively to what he can actually repay, with clear
disclosure of total cost and consequences, and without being pushed toward stacking multiple
loans.

REAL QUOTES:
"Lúc đó tôi cảm thấy nhẹ nhõm vì đã giải quyết được vấn đề trước mắt." / "Tôi cảm thấy hối hận khi
lựa chọn vay kiểu này. Nó không hề an toàn hay đơn giản như tôi tưởng." — Hữu Tùng, student —
Tiền Phong, 23 Apr 2025 [Skill03 S14, S15]

DECISION MAKING:
Authority: Sole decision-maker; no organisational buyer.
Budget/ticket size: Sourced comparable is a 10M VND personal loan [Skill03 S14] — likely the
  smallest, shortest-tenure end of HLB's range.

TECH PROFICIENCY: High — digitally native, comfortable with apps; this is precisely why he is
  "digitally active" despite having no formal credit footprint.

DATA FOOTPRINT AVAILABLE FOR ALTERNATIVE SCORING:
- E-wallet top-up regularity, telecom usage patterns, on-time utility bill payment — explicitly
  named in the brief as this segment's available signals [Brief p.8]
- VNeID/chip-CCCD biometric identity (mandatory infrastructure, Skill02 RF-4-RF-6)
- Literature support: digital-footprint scoring reaches similar discriminatory power to a bureau
  score and holds up for no-bureau-score applicants specifically [L1]; ML/alt-data advantage is
  largest for exactly this thin-file population [L4]

TYPICAL TICKET / PURCHASE USE-CASES WITHIN 1.6M–90.1M VND:
Small personal/electronics purchases or urgent personal expenses; sourced comparable 10M VND
[Skill03 S14]; likely short tenure and the segment most in need of a conservative starting limit.

RESPONSIBLE-LENDING VULNERABILITIES:
This is the segment with the clearest, most severe documented harm pattern: debt stacking,
FoMO-driven impulsive borrowing, low awareness of consequences, and identity-theft exposure
[Skill03 Pain #11, Opp. 6; S16, S17, S21]. Any solution targeting FTB carries the highest
guardrail requirement of the three borrower personas — affordability limits, anti-stacking checks
(ideally via CIC reporting of the new product), and consumer-friendly disclosure are not optional
extras here.
```

---

## Persona 4 — OFFICER: "Chị Lan", HLB credit-risk / underwriting lead

```
PERSONA: Chị Lan (archetype — built from Vietnamese banking job-description/requirements content
  plus adjacent bank-spokesperson evidence in Skill 03; NOT built from a first-person quote from
  an actual credit officer, because none was found — see evidence-gap note below)
Role: Chuyên viên/Trưởng nhóm thẩm định tín dụng (credit appraisal specialist / credit-risk lead)
  at a Vietnamese bank; internal operator and decision-owner for the point-of-purchase product
Seniority: Mid-to-senior — typical postings require a Bachelor's degree in economics, finance,
  banking or business administration plus 1–2 years' relevant experience [N1]
Industry context: Commercial bank credit/risk function
Team size: Works within a centralised credit-appraisal team (matches the brief's "Centralised
  Manual Review" workflow [Brief p.9])

GOALS (sourced/inferred from role definition):
- Evaluate an application's legal standing, financial capacity, loan purpose, repayment plan and
  collateral, then score and recommend approve/decline [N1 — this is the standard, generic job
  description for this role in Vietnamese banking, not HLB-specific]
- Keep the loan book's risk quality within the bank's risk appetite while processing volume
  efficiently — general framing consistent with regional credit-risk-manager role descriptions
  (portfolio monitoring, underwriting-strategy refinement) [N2]

FRUSTRATIONS (sourced, largely via Skill 03's OFFICER-cluster evidence — bank-spokesperson
  statements, not a working-level officer's own voice):
- No standardised way to say "yes" on thin-file evidence she can see: SMEs struggle "because
  evaluation data lacks standardized systems for assessment" — BVBank quote [Skill03 S38]; a
  finco CEO frames the gap as national trustworthiness scoring helping "score even customers with
  no credit history" [Skill03 S34] — implying today's officers cannot
- Manual, repetitive file assembly: the brief's own baseline — "centralised manual review",
  380,000 VND/application, 1.8–4.6 business days [Brief pp.5,9]; consumer-loan appraisal teaching
  material describes officers verifying information with employers and interviewing customers at
  their residences [Skill03 S49]
- Document/identity fraud that paper review can't reliably catch: a finco agent forged 455 ID
  cards across 607 applications [Skill03 S31]; a bank's own credit appraiser fabricated 20 files
  worth ~48.8bn VND [Skill03 S32]

TOOLS USED (sourced/inferred):
- CIC bureau lookup, internal credit-scoring/scorecard tools, loan-origination workflow systems
  (generic to the role) [N1, N2]
- What she does NOT yet have, per the case baseline: any standardised alternative-data input, or
  an automated triage/STP layer [Brief p.5]

WORKFLOW:
Receives a file passed from relationship/sales staff, checks legal standing, financial capacity,
loan purpose and collateral, scores and rates the applicant, and gives an approve/decline
recommendation [N1] — for every application, regardless of size, under the case's baseline
[Brief p.5].

SUCCESS CRITERIA:
Approves creditworthy applicants defensibly (i.e. against a policy she can point to, not personal
judgment alone), keeps portfolio risk within appetite, and processes volume without every file
taking days of manual work.

REAL QUOTES:
No first-hand quote from a Vietnamese credit officer describing their own day-to-day decisions
was found in this research (this gap was already flagged by Skill 03 as OQ-03-03 and persists
here). The closest sourced material is bank-spokesperson/executive commentary on the SME
evaluation gap [Skill03 S38, S39] and generic role-description content from Vietnamese job boards
[N1] — both are used above and clearly labelled as such, not presented as a personal quote.

DECISION MAKING:
Authority: Recommends approve/decline within a scoring/policy framework; final authority likely
  sits with a credit committee for larger or borderline cases (standard practice; not confirmed
  specific to HLB) [N1, N2].
Budget/compensation context: reported average base salary for this role nationally is about
  13–21M VND/month (median ~15M VND/month); at large banks (e.g. Techcombank, VietinBank) total
  compensation including bonus can reach 45–47M VND/month [N3]. This is general market data, not
  HLB-specific, included only to calibrate seniority/context.

TECH PROFICIENCY: Medium — comfortable with internal banking systems and CIC lookups by
  definition of the role; comfort with a new alternative-data/ML-assisted scoring tool is not
  evidenced and should not be assumed (a change-management consideration for Skill 13 onward).

DATA FOOTPRINT / WHAT SHE NEEDS TO DO HER JOB DIFFERENTLY:
A standardised, policy-approved, explainable set of alternative-data inputs and a defensible
approve/decline (or refer) recommendation — not raw data access. This matches the literature
review's finding that explainable, governed scoring (SHAP-based reason codes, monotonic
constraints) is the credible way to bring a human reviewer into an automated flow rather than
around it [L18, L19, L24, L25], and Vietnam's AI Law explicitly requires human oversight for
automated credit-decision systems [Skill02 RF-8].

RESPONSIBLE-LENDING / OPERATIONAL VULNERABILITIES:
If a new decisioning tool is introduced without her being able to see and act on the "refer" band
in a defensible way, she either becomes a bottleneck (undermining the whole point of automation)
or a rubber stamp (undermining risk quality and the AI Law's human-oversight requirement). Her
role design is therefore a first-class product constraint, not an implementation detail.
```

---

## Reference segments (not full persona cards, per user-confirmed scope)

**SAL-C — Salaried, with credit history (35.5% of inbound, [Brief p.9]):** Best served today —
"highest approval rate, largest ticket sizes, and lowest pricing" [Brief p.7]. Skill 03's evidence
for this segment is thin and mostly about friction *after* getting credit, not before: unwanted
cross-sell calls from multiple lenders after one purchase [Skill03 Pain #9, S9], and forced
bundled insurance [Skill03 Pain #7, S45]. Their relevance to this problem is P2, not P1: even
this segment waits 1.8–4.6 business days for a decision the bank could clearly automate, which is
part of the evidence (Skill 04 §3.3) that the bottleneck is the undifferentiated process itself.

**SAL-N — Salaried, no credit history (20.5% of inbound, [Brief p.9]):** "Moderate to low
approval rate; limited by the lack of prior credit bureau records" but has "stable monthly
inflows; regular salary transfers provide proof of income stability" [Brief p.7]. This segment
sits between SAL-C and the three thin-file personas: it needs alternative-style cash-flow
scoring (like GIG/MSME/FTB) but has a simpler, single, regular income source (unlike GIG's
volatility or MSME's mixed flows) — likely the easiest thin-file segment to serve first from a
pure modelling-difficulty standpoint, though this is an inference, not independently sourced.

---

## Persona Comparison Table

| Attribute | GIG (Anh Hùng) | MSME (Chị Phụng) | FTB (Hữu Tùng) | OFFICER (Chị Lan) |
|---|---|---|---|---|
| Share of inbound | 15.0% | 14.5% | 14.5% | n/a (internal) |
| Core problem | P1, P2, P4 | P1, P2 (time-critical) | P1, P2, P3, P4 | P1, P2 (operational) |
| Evidence strength (Skill 03 coverage) | Strong | Adequate (banker-heavy; direct seller voice thin — OQ-03-02) | Strong | Thin (no first-hand quote — OQ-03-03) |
| Repayment rail already proven | Yes — platform-wallet auto-deduction [Skill03 S1] | Partial — platform seller-loan repayment terms exist (SEasy) [Skill03 S41] | Weak — repeat/stacked borrowing is the observed pattern, not a proven good rail | n/a |
| Key data footprint | Platform payouts, e-wallet activity | E-commerce sales, sales-ledger/e-invoice data, CASA | E-wallet, telco, utility payments | n/a |
| Responsible-lending risk | Medium (volatility vs fixed instalments) | Medium (loan-purpose ambiguity) | **High** (debt stacking, FoMO, identity theft) | n/a |
| Strategic fit with HLB assets | Medium | **High** — HLB's own Sổ Bán Hàng partnership is unique among sourced competitors [Skill02 Whitespace 3] | Low-Medium | n/a |

---

## Primary Persona Selection

**Recommended primary borrower persona for a v1 pilot: GIG (Anh Hùng).** `[R1 Diagnosis]` `[R3 Concept]`

**Rationale:**
1. **Volume:** highest inbound share among the three thin-file personas (15.0% vs. 14.5%/14.5%, [Brief p.9]).
2. **Evidence strength:** the strongest, most-Confirmed Skill 03 evidence base of the three (multiple source types across income volatility, workaround behaviour, and a documented repayment mechanism), versus MSME's thinner direct-seller evidence (OQ-03-02) and FTB's higher-risk profile.
3. **A repayment rail is already proven in-market:** competitors already lend to this exact population using platform-wallet auto-deduction [Skill03 B2] — this de-risks the "will they repay" question relative to FTB, where the dominant observed pattern is debt stacking, not clean repayment.
4. **Lower responsible-lending guardrail burden than FTB:** GIG's core problem (income volatility vs. fixed instalments) is a pricing/affordability-design problem with known mitigations (repayment matched to income cadence); FTB's core problem (debt stacking, FoMO, identity theft) requires heavier guardrails to build responsibly, making it better suited as a second-wave segment once the core tiered-decision mechanism is proven.

**Strongest secondary/parallel candidate: MSME (Chị Phụng).** `[R2 Justification]` HLB already holds a unique data asset here — the Sổ Bán Hàng sales-ledger partnership [Skill02 Whitespace 3] — that no sourced competitor has for merchant lending, and the time-criticality of the "stock window" pain gives the clearest, most measurable before/after story for the "decision within the purchase moment" requirement (R-2). Its main weakness is evidentiary (thinner direct seller voice) and an unresolved product-design question (personal vs. business loan purpose — inherited OQ-03-10).

**FTB should not be the v1 primary** despite being the segment most aligned with the brief's inclusion language [Brief p.2] — it carries the highest documented harm pattern (debt stacking, identity-theft exposure, FoMO-driven impulsive borrowing) and is best served once the tiered decision flow and its guardrails are already validated on a lower-risk segment.

**This is a recommendation for Skill 08 (v1 Scoping) to ratify, not a locked decision** — Skill 08 is out of scope for this run. `[DECISION REQUIRED — Skill 08]`

---

## Open Items Raised by This Skill

| # | Item | Why it matters | Suggested validation / owner | Carry to |
|---|---|---|---|---|
| OQ-05-01 | Direct, first-person voices of Vietnamese online sellers (Shopee/TikTok/Facebook) declined for point-of-purchase credit remain thin (inherits Skill 03's OQ-03-02); the MSME persona rests more on banker/press commentary than seller testimony. | The MSME persona could be under- or over-weighting the "timing vs. price" framing without more direct seller input. | 5–10 seller interviews, or a short survey via HLB's Sổ Bán Hàng partner. | Skill 08 / 22 |
| OQ-05-02 | No first-hand quote from a working Vietnamese bank credit officer was found (inherits and confirms Skill 03's OQ-03-03); the OFFICER persona is built from generic role descriptions [N1, N2] plus adjacent bank-executive commentary, not a personal account. | The OFFICER persona's frustrations/workflow may not reflect HLB's specific team structure, tooling, or day-to-day reality. | Interview an HLB (or comparable bank) credit-risk/underwriting lead directly. | Skill 08 / 13 / 22 |
| OQ-05-03 | Whether HLB's existing credit-appraisal team would own this product, or whether a new/adapted team structure is needed, is not stated anywhere in the brief or sourced research. | Affects Skill 13 (solution design) and Skill 18 (NFRs) around who operates the refer-band and under what SLA. | Ask HLB/organisers, or state as an explicit open design assumption. `[R4 Assumptions]` | Skill 13 / 18 / 22 |
| OQ-05-04 | Willingness of GIG/MSME/FTB persons to consent to HLB reading platform, e-wallet, telco or sales-ledger data has not been measured (inherits Skill 03's OQ-03-04). | Every persona's "data footprint" section above depends on consent uptake being reasonably high; if it isn't, the whole alternative-data approach underperforms regardless of persona choice. | Literature review already covers general consent research [L14, L15, L16]; a Vietnam-specific, product-specific consent-rate test is still needed. | Skill 13 / 22 |
| OQ-05-05 | The Primary Persona Selection above is an evidence-based recommendation from Skill 05, not a ratified decision — Skill 08 (v1 Scoping), which would normally lock this, is out of scope for this run. | Skill 12 (preliminary, this run) needs to know which persona(s) to design around; downstream skills would need to revisit this if the user later runs Skill 08 and picks differently. | User/Skill 08 to confirm or override. `[DECISION REQUIRED]` | Skill 12 (preliminary) / Skill 08 |
| OQ-05-06 | Loan-purpose ambiguity for the MSME persona (personal vs. business use of point-of-purchase credit) is unresolved (inherited from Skill 03's OQ-03-10) and directly affects this persona's product fit and data footprint. | Changes what "success" and "responsible use" mean for this persona, and which data sources are relevant (sales data vs. personal inflows). | Ask the user / re-check the brief; state as an assumption if unresolved by submission time. `[DECISION REQUIRED]` | Skill 08 / 22 |

---

## Source Index

Persona evidence is drawn overwhelmingly from `03-user-research.md`'s Source Index (cited `[Skill03 S#]` throughout — see that file for full publisher/date/URL detail), plus `01-product-strategy.md` (`[Skill01]`), `02-market-research.md` (`[Skill02 S#/RF#]`) and `literature-review.md` (`[L#]`). New sources found specifically for this skill, primarily to build the OFFICER persona:

| # | Source | URL | Used for |
|---|---|---|---|
| N1 | JobOKO, "Nhân viên thẩm định tín dụng làm gì? Mô tả công việc mới nhất" (aggregating multiple Vietnamese job-posting sites incl. Indeed VN, CareerViet) | https://vn.joboko.com/blog/mo-ta-cong-viec-nhan-vien-tham-dinh-tin-dung-nwi3806 | Generic role description, responsibilities, qualifications for a Vietnamese bank credit-appraisal officer |
| N2 | Glassdoor / aggregated search results for "credit analyst" and "credit risk manager" roles in Vietnam (SME/consumer lending) | https://www.glassdoor.com/Job/vietnam-credit-analyst-jobs-SRCH_IL.0,7_IN251_KO8,22.htm | Generic credit-risk-manager responsibilities (portfolio monitoring, underwriting-strategy refinement) as context for seniority/scope |
| N3 | 1900.com.vn / JobsGO / VietnamWorks aggregated salary data for "Chuyên viên Thẩm định Tín dụng" | https://1900.com.vn/muc-luong/chuyen-vien-tham-dinh-tin-dung-1475 | Compensation/seniority context for the OFFICER persona (national average ~13–21M VND/month; large-bank total comp ~45–47M VND/month) |

*No quote in N1–N3 is a personal, first-person account — they are role-description and salary-aggregation content, used only for structural/contextual attributes of the OFFICER persona, and are clearly distinguished from the verbatim quotes used in Personas 1–3.*

## Skill 22 Resolutions (User decision, Skill 22, 2026-09-30)
- **OQ-05-05 / Primary persona:** confirmed. Anh Hùng (GIG) leads the new-to-bank lane, and the existing SAL-N account-holder leads the existing-customer lane (D-02).
- **OQ-05-06:** MSME loan purpose = purchase finance at partner merchants (D-03).
