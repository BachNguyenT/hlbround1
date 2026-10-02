# Product Strategy & Vision
**Product:** HLB Point-of-Purchase Credit Decisioning (working name) | **Skill:** 01 | **Status:** Done (2026-09-28)
**Depends on:** _context.md, _requirements.md | **Feeds into:** All downstream skills

> Rubric tags: `[R1]` Problem Diagnosis · `[R2]` Intervention Justification · `[R3]` Solution Concept · `[R4]` Data & Assumptions. Case facts are cited as **[Brief p.N]**; everything else carries a Source Index number **[S#]**.

---

## Pre-filled Context (from elicitation)
- Product name: HLB Point-of-Purchase Credit Decisioning (working name)
- Product type: Other — a credit decisioning capability that HLB Vietnam owns and runs, offered to customers through merchant and partner checkouts (B2B2C)
- Primary persona hypothesis: thin-file point-of-purchase borrowers (gig/platform worker, online MSME merchant, first-time digital borrower), with an HLB credit-risk/underwriting lead as the internal operator
- Problem hypothesis: a branch-style manual review (380,000 VND/application, 1.8–4.6 business days) cannot assess thin-file applicants, cannot decide within the purchase moment, and costs too much relative to small-ticket loans (1.6M–90.1M VND) **[Brief pp.4–6, 9]**
- Constraints: risk quality, responsible lending, regulatory compliance, fraud/identity controls **[Brief p.6]**. AI counts only if it shows incremental value over the baseline **[Brief p.10]**.

---

## 1. Product Vision Statement

> **For** Vietnamese shoppers and online sellers who earn and transact digitally but are invisible to bureau-based underwriting, **who** today lose the purchase, delay it, or turn to informal "black credit" when financing isn't available at checkout, **HLB Point-of-Purchase Credit Decisioning** is **an in-journey credit decision capability** that **returns a fair, transparent, responsible credit decision inside the purchase moment, at a cost per decision that fits a small-ticket loan**. **Unlike** HLB's current branch-designed, document-and-bureau manual review — and unlike informal app lenders that approve fast with no affordability check — **it** decides using consented data the bank and its partners already hold, sends only the genuinely uncertain cases to human review, and verifies identity in real time without documents.

Why this framing: the brief states that customers who can't get financing at checkout "delay the purchase, abandon it, or seek credit outside the formal banking system" **[Brief p.4]**. Vietnamese authorities describe informal apps that approve in "20–30 minutes without requiring income verification" as the alternative people turn to **[S5]**. The vision therefore competes on **speed with safeguards**, not speed alone. `[R1][R3]`

---

## 2. North Star Metric

```
North Star Metric: Good In-Moment Loans (GIML) per month
Definition: Count of point-of-purchase loans that were (a) decided within the checkout
            session (target window set in Skill 10; working hypothesis ≤ 5 minutes from
            application submit to decision), (b) disbursed/booked, and (c) not 30+ days
            past due at month-on-book 3 (MOB3 30+ = 0).
Why this metric: It moves only when all three of the case's requirements improve together:
            speed-to-decision (R-2), reach into thin-file segments (R-1, since a loan never
            decided counts zero) and risk quality/responsible lending (R-2 guardrail — a
            loan that goes delinquent counts zero). Approval rate alone is vanity (it can
            be bought with loose credit), and cost per decision alone can be gamed by
            declining everyone.
Current benchmark: HLB pilot baseline — effectively 0 in-moment decisions (1.8–4.6
            business days) [Brief p.9]. External reference: Home Credit Vietnam has
            promoted automated approval in about 3 minutes for point-of-sale instalment
            loans [S6]; POS lending designs aim for approve/decline/refer "within seconds"
            [S8].
Target at 12 months: [DECISION REQUIRED — set in Skill 10. No sourced HLB pilot volume,
            approval-rate or delinquency baseline exists in the brief; any number here
            would be invented. Logged as OQ-03.]
```

**Supporting input metrics (seed for Skill 10):** `[R3]`
- **In-session decision rate:** % of applications decided within the checkout window. Baseline 0% **[Brief p.9]**.
- **Cost per decision (VND):** baseline 380,000 **[Brief p.9]**, which equals 23.75% of the smallest ticket (1.6M VND) and 0.42% of the largest (90.1M VND) (derived arithmetic).
- **Thin-file approval rate at controlled risk:** approval rate for the gig, MSME and first-time segments, which make up 44% of inbound applications **[Brief p.9]**, with MOB3 30+ held at or below a risk appetite that HLB sets.
- **Straight-through-processing (STP) share:** % of decisions with no human touch. Manual review is kept only for the "refer" band.
- **Application completion rate:** % of started applications submitted. This is the proxy for document-friction abandonment **[Brief p.6]**.

This follows the lending-operations practice of tracking approval rate, time-to-decision, automation % and cost per funded deal together **[S7]**, and treating risk-adjusted return (not volume) as the real value signal in lending **[S9]**.

---

## 3. Strategic Pillars (3)

### Pillar 1 — Decide on data, not documents
**Thesis:** Most thin-file applicants are not unknown. Their income and behaviour already sit in HLB accounts (salary and CASA inflows), partner platforms (gig income, e-commerce sales) and third parties (e-wallet, telco, utilities) **[Brief p.5, Exhibit 1]**. Scoring on consented versions of these signals can extend reliable assessment beyond the bureau file. `[R1][R2]`
**Evidence:**
- Only about 51% of Vietnamese adults were covered by the credit registry (reported figure, c. 2018), and CIC data comes mainly from credit institutions, so people who have never borrowed formally have no CIC rating **[S3]**.
- The brief records officers seeing cash flows yet still declining MSMEs, and gig workers being declined "despite suspected repayment ability" **[Brief p.8]**.
- McKinsey reports that banks using next-generation decisioning models increased revenue, reduced credit losses and gained efficiency **[S10]**. This is general evidence, not Vietnam-specific; the Vietnam-specific evidence is gathered in the literature review.
**Risk:** Legal access to partner and third-party data under Vietnam's personal-data regime may be narrower or slower than assumed (OQ-02). Alternative-data models may also carry bias or fail to predict default for these segments (tested in the literature review).

### Pillar 2 — Unit cost proportional to ticket size
**Thesis:** The bottleneck is not only that review is slow. The same fixed 380,000 VND manual process is applied to every application regardless of risk or ticket size **[Brief p.5]**. A tiered decision flow could cut blended cost per decision to fit small tickets while keeping human judgment where it adds value. In that flow, automated approve/decline covers the clear cases, and humans review only the uncertain middle band. `[R1][R2]`
**Evidence:**
- The brief states the review "may cost more than the loan earns" for checkout-size loans **[Brief p.5]**.
- Home Credit Vietnam's automated POS flow checks CIC plus external databases and returns a score in minutes, calling references or employers only when needed **[S6]**.
- McKinsey notes gen-AI and automation shift credit-officer effort toward review rather than assembly **[S10]**.
**Risk:** If the uncertain middle band is large (for example, if most thin-file cases land there), savings shrink. The size of that band is unknown until back-testing on HLB pilot data. This is logged for Skill 22.

### Pillar 3 — Responsible, safe and targeted inclusion
**Thesis:** Fast credit without safeguards reproduces the harms of informal lending. Each decision must include three controls: real-time identity and application-genuineness checks without document friction (R-3), an affordability and responsible-lending check (R-2), and targeted rather than broadcast offers (R-4). This is also what makes the line sustainable and inclusive in the sense the brief highlights **[Brief p.2]**. `[R2][R3]`
**Evidence:**
- Vietnam recorded over 125,000 fraudulent websites and more than 22,200 online-scam reports in the first nine months of 2024 **[S5]**.
- The State Bank of Vietnam is urging lenders to simplify procedures and digitise specifically to push back black credit **[S5]**.
- The BNPL sector is growing fast (reported about US$2.61B in 2025, +36.5% YoY) and regulators are discussing a sandbox framework **[S2]**.
**Risk:** Friction added for fraud and affordability checks may cancel out the speed gains. Tuning targeted offers for acceptance could slide into pushing credit to vulnerable users. Skill 05 personas and the literature review must test this trade-off.

---

## 4. Product Hypothesis

```
We believe that thin-file point-of-purchase applicants in Vietnam (gig/platform
workers, online MSME merchants, first-time digitally active borrowers — 44% of pilot
inbound [Brief p.9])
experience declines, multi-day waits and document friction at checkout
because HLB's point-of-purchase lending runs on a branch-designed, document-and-bureau
manual review that ignores the digital income/transaction records the bank and its
partners already hold, and applies one fixed-cost human review to every ticket size
[Brief pp.4–5].
If we build an in-journey decision capability that (1) verifies identity and
application genuineness in real time, (2) scores applicants on consented bank,
partner and alternative data, (3) auto-decides clear cases and routes only the
uncertain middle band to credit officers, and (4) pre-qualifies likely, suitable
customers inside partner ecosystems,
they will complete applications and receive decisions inside the purchase moment,
and more creditworthy thin-file applicants will be approved at controlled risk,
which we will measure by Good In-Moment Loans per month (North Star), with leading
indicators in-session decision rate, cost per decision and thin-file approval rate
at MOB3 30+ within appetite.
```

This hypothesis still has to beat an alternative that is not AI. The brief allows a "lean process or creative rule-based redesign" **[Brief p.11]**. Skill 04 and the preliminary Skill 12 must test whether a rules-and-process redesign captures most of the value before assuming machine learning is required. `[R2]`

---

## 5. Strategic Context Brief

- **Market category and stage:** Embedded / point-of-purchase consumer and micro-merchant credit (BNPL and instalment loans) in Vietnam. This is a **growing** market: reported CAGR of 58.3% for 2021–2024 and a projected US$2.61B in 2025 **[S2]**. Growth is driven by e-commerce, low credit-card penetration, and super-app integration (MoMo, ZaloPay) **[S2]**. Enterprise AI adoption in finance is also still maturing: 58% of finance functions used AI in 2024, up 21 points from 2023 (Gartner survey of 121 finance leaders — the source of the brief's p.2 statistic) **[S1]**.
- **Primary competitors (names only; full analysis in Skill 02):**
  - Consumer-finance companies: Home Credit, FE Credit
  - BNPL and fintech: Fundiin, Kredivo, Atome
  - E-wallet pay-later: MoMo, ZaloPay
  - Other banks' digital instant-loan and BNPL programmes
- **HLB-specific context:** HLB Vietnam already has the HLB Connect digital app and an SME Sales Ledger ecosystem, including a partnership with Sổ Bán Hàng (a merchant sales app). It won Best SME Bank for CX (SEA and Vietnam) and Best Digital Bank for CX (Vietnam) at the 2026 Digital CX Awards **[S4][Brief p.3]**. The Sales Ledger partnership is a potential source of consented MSME sales data, to be verified in Skill 02.
- **Key insight:** The data needed to lend to thin-file segments already exists in digital form. It is the decision process that is designed for paper. The leverage point is therefore the decision workflow (how an application is routed and on what evidence), not model accuracy alone. The rubric rewards exactly this: "the tightly coupled decision point(s) with the highest operational leverage" **[Brief p.11]**. `[R1]`
- **Success at 12 months:**
  - Most point-of-purchase applications are decided in-session.
  - Blended cost per decision falls well below 380,000 VND.
  - Thin-file approval rate rises at MOB3 delinquency within HLB's risk appetite.
  - Numeric targets: [DECISION REQUIRED — Skill 10].
- **Success at 3 years:** Point-of-purchase lending is a profitable HLB business line across merchant and partner ecosystems. Thin-file customers graduate into bureau-visible, CASA-holding HLB customers (the CASA-first mitigation in Exhibit 1 becomes a growth loop). The scorecards are monitored for drift and fairness.
- **Known constraints:**
  - Regulatory: SBV consumer-lending rules, eKYC rules, personal-data protection, CIC reporting (all detailed in Skill 02).
  - Responsible lending and affordability.
  - Real-time fraud and identity checks.
  - AI allowed only with incremental value.
  - Competition deliverable: a one-page 1920×1080 PDF.

---

## Open Items Raised by This Skill

| # | Item | Carry to |
|---|---|---|
| OQ-03 | No HLB pilot volumes, per-segment approval rates or delinquency rates are given, so North Star targets cannot be sourced. | Skill 10 / 22 |
| OQ-07 | The size of the "refer to human" band under a tiered flow is unknown without back-testing, and it drives Pillar 2's savings. | Skill 04 / 22 |
| OQ-08 | Vietnam credit-registry coverage figure (51%) is dated (c. 2018). A current CIC/PCB coverage figure is needed. | Skill 02 |

---

## Source Index

| # | Source | Used for |
|---|---|---|
| S1 | Gartner press release, "Gartner Survey Shows 58% of Finance Functions Using AI in 2024" (11 Sep 2024) — https://www.gartner.com/en/newsroom/press-releases/2024-09-11-gartner-survey-shows-58-percent-of-finance-functions-use-ai-in-2024 ; corroborated by Journal of Accountancy https://www.journalofaccountancy.com/news/2024/sep/organizations-turning-to-ai-in-finance-function/ | Origin of brief's 58% statistic; AI maturity context |
| S2 | ResearchAndMarkets "Vietnam Buy Now Pay Later Business Report 2025", via GlobeNewswire https://www.globenewswire.com/news-release/2025/02/17/3027131/28124/en/ and BusinessWire https://www.businesswire.com/news/home/20250221652599/en/ | BNPL market size/growth, players, SBV sandbox signal |
| S3 | Viet Nam News, "51% of Vietnamese adults have credit registry coverage" — https://vietnamnews.vn/economy/419819/51-of-vietnamese-adults-have-credit-registry-coverage.html | Bureau coverage gap (dated) |
| S4 | HLB press release, "Hong Leong Bank Vietnam Named Best SME Bank for Customer Experience in Southeast Asia and Vietnam" — https://www.hlb.com.my/en/personal-banking/news-updates/hlb-vietnam-named-best-sme-bank-for-customer-experience-in-southeast-asia-and-vietnam.html ; Vietnam.vn coverage of Sales Ledger / Sổ Bán Hàng — https://www.vietnam.vn/en/hong-leong-viet-nam-ngan-hang-sme-co-trai-nghiem-khach-hang-tot-nhat ; HLB Connect — https://www.hlbank.com.vn/en/about-us/news-and-updates/hong-leong-bank-vietnam-launches-new-digital-banking-platform.html | HLB digital assets |
| S5 | VietNamNet, "'Black credit' loan scams spread across Vietnam's online networks" — https://vietnamnet.vn/en/black-credit-loan-scams-spread-across-vietnam-s-online-networks-2332080.html ; SBV credit review 2024/2025 — https://www.sbv.gov.vn/en/web/sbv_portal/w/sbv622860 ; Tạp chí Ngân hàng on microfinance vs black credit — https://tapchinganhang.gov.vn/phat-trien-tai-chinh-vi-mo-de-day-lui-tin-dung-den-16387.html | Informal-credit substitute, fraud scale, SBV stance |
| S6 | Vietnam Investment Review, "Home Credit advances with digital technology" — https://vir.com.vn/home-credit-advances-with-digital-technology-45277.html | Competitor automated POS decision (~3 min) |
| S7 | Lendflow, "Calculating the True ROI of Your Embedded Finance Strategy" — https://www.lendflow.com/post/calculating-the-true-roi-of-your-embedded-finance-strategy | Lending ops metric set |
| S8 | LendFoundry, "POS Lending Explained" — https://lendfoundry.com/blog/pos-lending-explained-how-point-of-sale-financing-fits-into-embedded-lending/ | POS decision-in-seconds design norm |
| S9 | A. Zulkifli, "True North Star Metric of Online Lenders" (Medium) — https://medium.com/@aidil_93441/true-north-star-metric-of-online-lenders-95930579dc22 | Risk-adjusted return as lending North Star (practitioner view; low authority) |
| S10 | McKinsey, "Designing next-generation credit-decisioning models" — https://www.mckinsey.com/capabilities/risk-and-resilience/our-insights/designing-next-generation-credit-decisioning-models ; McKinsey, "Banking on gen AI in the credit business" — https://www.mckinsey.com/capabilities/risk-and-resilience/our-insights/banking-on-gen-ai-in-the-credit-business-the-route-to-value-creation | Decisioning-model value; officer-effort shift |
