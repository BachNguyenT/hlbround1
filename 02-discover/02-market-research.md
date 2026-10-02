# Market Research
**Product:** HLB Point-of-Purchase Credit Decisioning (working name) | **Skill:** 02 | **Status:** Done (2026-09-28)
**Depends on:** product-strategy.md | **Feeds into:** problem-statement.md, lean-canvas.md

> Rubric tags: `[R1 Diagnosis]` · `[R2 Justification]` · `[R3 Concept]` · `[R4 Assumptions]`. Case facts are cited as **[Brief p.N]**. Everything else carries a Source Index number **[S#]**.
> **Confidence labels:** **High** means the finding is confirmed by 3 or more independent source types (e.g. regulator or official portal, company disclosure, press, law firm). **Medium** means 2 source types. **Low** means a single source; treat it as a hypothesis.
> **Number labels:** **(sourced)** means the figure is taken directly from the source. **(derived)** means it is arithmetic on sourced figures, with the formula shown. **[DECISION REQUIRED]** means research cannot answer it and HLB or the team must decide.

---

## Pre-filled Context (from elicitation)
- Product type: Other — bank-internal credit decisioning capability, distributed B2B2C through merchant/partner checkouts
- Target geography: Vietnam (HLB offices in Ho Chi Minh City and Hanoi), with India, Indonesia, the Philippines, Kenya and similar developing markets as analogues **[Brief p.10]**
- Known competitors: none named in the brief. This skill maps the landscape: finance companies (Home Credit, FE Credit, HD Saison, Mcredit), BNPL fintechs (Fundiin, Kredivo, Atome), e-wallet and marketplace pay-later products (MoMo, ZaloPay, SPayLater) with their lending partners, and bank-led PayLater (Cake by VPBank / MWG, TPBank, CIMB)
- Segment: all five inbound segments from Exhibit 2, weighted toward the three thin-file segments (gig 15%, online MSME 14.5%, first-time 14.5% = 44% of inbound) **[Brief p.9]**

---

## Executive Summary: Top Findings

| # | Finding | Confidence | Rubric |
|---|---|---|---|
| F1 | **Every sourced competitor decides in 5 seconds to about 30 minutes. HLB takes 1.8–4.6 business days.** Home Credit takes 3 minutes (CCCD only, no income proof), Home PayLater 60 seconds, MoMo Ví Trả Sau 3 minutes, MWG PayLater (Cake) 2 minutes, Fundiin about 5 seconds **[S9][S10][S18][S21][S15]**. Customers now expect in-moment decisions; that is table stakes, not a differentiator. | High | `[R1 Diagnosis]` |
| F2 | **Market prices cannot absorb a 380,000 VND manual review on small tickets.** At a market-level ~30% effective APR (FE Credit's own example), with bank cost of funds of 4.8% and finance-company credit cost of 11.5%, a 3-month loan needs a ticket of about **16.9M VND** just to cover the review cost. A 6-month loan needs about 9.6M VND (derived, §2A). In other words, much of HLB's 1.6M–90.1M VND range is loss-making by construction under manual review. | Medium (derived from sourced inputs) | `[R1 Diagnosis]` `[R2 Justification]` |
| F3 | **Pay-later in Vietnam is shifting from fintechs to regulated banks.** Atome exited in 2023 and about 10 BNPL start-ups left for lack of capital **[S17]**. The leading pay-later products are now funded by banks or finance companies: SPayLater by TPBank/VPBank, MoMo by TPBank/MBV/VCBNeo, ZaloPay by CIMB/Lotte Finance, MWG PayLater by Cake **[S20][S21]**. A bank entering is on-trend, not contrarian. | High | `[R2 Justification]` `[R3 Concept]` |
| F4 | **Structural cost-of-funds advantage for banks.** The banking-sector average cost of funds was about 4.8% in 2025. Home Credit's was about 7.1%, and its May–June 2026 bonds priced at 9.5–9.8% **[S24]**. Combined with Circular 29/2026 raising the "small-value loan" threshold to 400M VND (effective 15 Aug 2026) **[S27]**, a bank can price fairly and still earn a margin on POS tickets. It can only do so if cost per decision falls. | Medium–High | `[R2 Justification]` |
| F5 | **The data rails and the constraints both tightened in 2025–26.** Enablers: Open API (Circular 64/2024, full compliance by 1 Mar 2027), the credit-scoring sandbox (Decree 94/2025), and household-business e-invoicing after lump-sum tax ended on 1 Jan 2026. Constraints: the PDPL (Law 91/2025, effective 1 Jan 2026), under which financial data is sensitive, consent must be purpose-specific and buying or selling personal data is banned. The AI Law (134/2025, effective 1 Mar 2026) classes automated credit-decision systems as **high-risk**, with a finance-sector grace period to 1 Sep 2027 **[S35][S37][S38][S39][S50]**. | High | `[R3 Concept]` `[R4 Assumptions]` |

---

## 1. Competitive Matrix

### 1.1 Landscape overview
Vietnam's point-of-purchase credit market has three layers.

1. **Licensed finance companies (fincos)** run in-store instalment lending for durables. Examples: Home Credit, FE Credit, HD Saison, Mcredit. There are 16 licensed fincos, 15 of them active, and they held about 5% of consumer lending at end-Feb 2024 **[S6]**.
2. **Pay-later wrappers on e-wallets and marketplaces.** MoMo, ZaloPay and Shopee provide the user interface and data, while banks or fincos book the credit **[S18][S19][S20]**.
3. **Bank-led PayLater.** Cake by VPBank runs MWG PayLater; CIMB partners with Fundiin **[S15][S21]**.

The market grew from 4 BNPL players in 2020 to 15 in 2024, then shook out: Atome exited in 2023, and roughly 10 start-ups left for lack of capital **[S17][S21]**. Consumer finance recovered in 2025. Finco NPLs fell from 11.2% to 7.2% and credit cost from 14.5% to 11.5%, and the product mix moved toward durables, point-of-sale lending and BNPL **[S3][S4]**. `[R1 Diagnosis]`

### 1.2 Competitor profiles (only players that could be sourced)

**Home Credit Vietnam (finco).** Operating in Vietnam for 18+ years, with about 17M customers **[S7]**.
- **POS durables loans:** 1–40M VND over 4–24 months. 0% interest on selected items, down payment from 0 VND, "CCCD only", no income verification, "3-minute approval". Partners include Thế Giới Di Động, Điện Máy Xanh, FPT Shop and CellphoneS **[S9]**.
- **Home PayLater:** limit up to 25M VND, terms of 1/3/6/12 months, automatic decision in 60 seconds, 500+ partners **[S10]**.
- **Financials:** 2025 profit after tax of VND 2,076bn (+60.7%), loan book +37.4%, NPL 1.66%, CAR 23.3%. The SCBX acquisition lapsed **[S7]**. Cost of funds was about 7.1% in 2025 **[S24]**.
- Confidence: High.

**FE Credit (finco, VPBank subsidiary).**
- **Phone/electronics loans:** 1–100M VND over 3–24 months, at 0–2.69% per month flat on the initial balance. CCCD required, age 19–60, income of at least 3M VND/month, real-time approval at partner stores **[S11]**.
- **Pricing example from FE Credit itself:** 10M VND over 12 months at 1.41%/month flat, about 974k VND/month. That equals an effective **~29.8% nominal APR (~34.3% EAR)** (derived by solving for the internal rate of return).
- **Financials:** 2025 profit before tax above VND 600bn, its second consecutive profitable year **[S12]**. It recorded large losses in 2023, with NPL reported above 20% in Q2 2023 **[S12]** (Low confidence on the NPL figure).
- Also runs PayNow on ViettelPay for small bills **[S17]**.

**HD Saison (finco, HDBank/Credit Saison).**
- 15M+ customers and 26,500+ service points covering motorbikes, electronics, furniture, education, dental and more **[S13]**.
- Rates reported at **20.41–34.78% per year on the initial balance**. Loans of 2.5–30M VND, approval in about 10–30 minutes. Some channels ask for extra ID documents and a reference contact **[S13]**.
- Confidence: Low–Medium. Pricing and documents come from aggregator sites, not HD Saison's own tariff page.

**Mcredit (finco, MB Bank / SBI Shinsei).** Offers POS instalment and cash loans. Under the SBV's rate-disclosure directives it published an average lending rate of **25.44% p.a. (Jan 2025)** and **29.03% p.a. (period to Nov 2025)** **[S14]**. Confidence: High (primary disclosure).

**Fundiin (BNPL fintech, founded 2019).**
- 0% cost to the consumer; the merchant pays. Decision "within 5 seconds", no annual or hidden fees **[S15]**.
- 800+ retail partners, including Thế Giới Di Động, Pharmacity and Lotte Cinema. Total funding about US$6.8M, with a US$5M Series A in Oct 2022 **[S15]**.
- In Feb 2025 it launched "Trả góp Tối ưu" with CIMB Vietnam. The product uses credit records and transaction data to match lenders, explicitly for thin-file customers **[S15]**.
- Its merchant fee rate is not public (see OQ-02-12).

**Kredivo (BNPL, operating through VietCredit finco).** Launched in 2021. Offers 30-day 0% or 3/6/12-month instalments; VietCredit is the lender of record **[S16]**. **Whether it is still operating in 2026 could not be verified** (OQ-02-10).

**Atome.** Exited Vietnam in July 2023 **[S17]**. It is not treated as an active competitor, although some 2025 market reports still list it **[S1]**. This is a data-quality flag on those reports. **Muadee** could not be sourced, so it is excluded.

**MoMo Ví Trả Sau (e-wallet; lenders TPBank, MBV, VCBNeo).**
- Limit of 1–20M VND, 0% interest, "3-minute" approval, about 2M active users **[S18][S20]**.
- Fees: 33,000 VND/month service fee when used (the first 5 transactions are free). Instalment conversion fee of 7.5–30% for 3–12 months (about 2.5% of principal per month; the older blog rate was 3%/month). Late fees of 5.25–21% of the overdue balance.
- Requirements: CCCD-verified identity, a linked bank account and a clean CIC history **[S18]**.
- Confidence: High.

**ZaloPay Tài khoản Trả Sau (lenders CIMB Vietnam, Lotte Finance).** Limit up to 8M VND, 0% for up to 37 days. Lotte's version charges 20,000 VND/month from Feb 2025 **[S19][S20]**.

**Shopee SPayLater (lenders TPBank, VPBank).** Limit up to 50M VND, 0% for 1–3-month terms **[S20]**. Shopee charges sellers **0% transaction fee on SPayLater orders**, against 4.91% (from Jul 2025) rising to 6% (from 1 May 2026) for card or instalment orders **[S22]**. Shopee is therefore steering checkout volume toward its own pay-later rails.

**MWG PayLater (lender and technology: Cake by VPBank).** Launched 30 Jun 2025 across Thế Giới Di Động, Điện Máy Xanh, TopZone, AVAKids and An Khang.
- Limit up to 40M VND, approval in about 2 minutes, tenures up to 24 months, interest-free for up to 90 days, no down payment **[S21]**.
- Cake runs eKYC, face match, AI credit assessment and risk management end to end **[S21]**.
- Confidence: High.

**Other banks' digital instant loans.** VPBank, TPBank, MB and Techcombank offer online consumer loans under the electronic-lending rules **[S28]**. Product-level terms for VIB, Timo and others could not be sourced, so they are left out of the matrix.

### 1.3 Feature comparison matrix

Legend: ✅ = offered and sourced · ⚠️ = partial or conditional · ❌ = not offered · ? = not found in sources

| Capability | Home Credit (POS + Home PayLater) | FE Credit (POS) | HD Saison | MoMo Ví Trả Sau | SPayLater | MWG PayLater (Cake) | Fundiin | **HLB today** [Brief p.9] | **HLB target concept** |
|---|---|---|---|---|---|---|---|---|---|
| Lender of record | Finco | Finco | Finco | TPBank / MBV / VCBNeo | TPBank / VPBank | Cake (VPBank) | Merchant-funded + CIMB partner | Bank | Bank |
| Decision speed | 3 min (POS), 60 s (PayLater) | Real-time at store | ~10–30 min (Low) | 3 min | 2–5 min (market-wide) [S20] | ~2 min | ~5 s | **1.8–4.6 business days** | In-session; target set in Skill 10 [DECISION REQUIRED] |
| Ticket / limit (VND) | 1–40M; PayLater ≤25M | 1–100M | 2.5–30M | 1–20M | ≤50M | ≤40M | ? | 1.6–90.1M | 1.6–90.1M (brief range) |
| Tenure | 4–24 m; PayLater 1–12 m | 3–24 m | 6–24 m (Low) | ≤12 m | 1–3 m at 0% | ≤24 m | Short, pay-in-parts | 3–24 m | 3–24 m |
| Consumer price | 0% on select items; otherwise ? | 0–2.69%/month flat (~30% APR example) | 20.4–34.8% p.a. flat (Low) | 0% + fees (33k/month; 7.5–30% conversion) | 0% for 1–3 m | 0% up to 90 days | 0% | ? [DECISION REQUIRED] | Transparent APR [DECISION REQUIRED] |
| Documents | CCCD only | CCCD + income ≥3M | CCCD + extra ID/reference (Low) | CCCD eKYC + linked bank | Shopee account + eKYC | eKYC + face + OTP | ? | Physical/scanned docs | Chip-CCCD/VNeID biometric eKYC, no documents |
| Data used | CIC + external databases [Skill 01 S6] | CIC; income | ? | CIC credit history | Platform data (not disclosed) | AI assessment (inputs not disclosed) | Credit records + transaction data (CIMB) | CIC + collateral + manual review | CIC + CASA + consented partner/alt data |
| Thin-file acceptance | ⚠️ (no income proof, but high-price risk pricing) | ⚠️ | ⚠️ | ❌ (needs clean CIC history) | ? | ? | ⚠️ (explicit thin-file aim with CIMB) | ❌ (lowest approval) | ✅ goal |
| Merchant model | Retail partners, 0% promos | Retail partners | 26,500 service points | In-wallet, thousands of POS | Own marketplace, 0% seller fee | Single retailer group (captive) | Merchant pays | Merchant/partner redirect | Partner-embedded API [R3] |
| In-store + online | ✅ | ✅ (store) | ✅ | ✅ | ❌ (online only) | ✅ | ✅ | ⚠️ (redirect to branch process) | ✅ |
| Cost of funds | ~7.1% (2025); new bonds 9.5–9.8% | ? | ? | Bank | Bank | Bank | Partner-dependent | Bank (sector avg ~4.8%) | Bank |

`[R1 Diagnosis]` The gap is not features or brand but **process**: HLB is the only player on the grid whose decision leaves the purchase moment. `[R3 Concept]` Every in-moment competitor combines chip-ID eKYC, automated CIC and data checks, and e-contract.

### 1.4 Our top 3 differentiators (hypotheses for Skill 07/12)
1. **Bank cost of funds and CASA.** This allows fair pricing and lower merchant subsidy on 6–24-month tickets that pay-later wallets (≤8–20M VND limits) do not serve **[S19][S18][S24]**. `[R2 Justification]`
2. **Cash-flow underwriting on consented bank and partner data.** This means salary and CASA inflows, merchant sales ledgers and e-invoices, rather than "CCCD-only, no income proof" risk pricing. That matters most for the 44% of HLB inbound that is thin-file **[Brief p.9][S9][S50][S52]**. `[R1][R2]`
3. **Regulatory trust.** A bank licence, PDPL and AI-Law compliance by design, and transparent APR against wallet fee stacks (late fees up to 21%, conversion up to 30%) **[S18][S35][S37]**. `[R3 Concept]`

---

## 2. Market Sizing (TAM / SAM / SOM)

```
TAM (flow, embedded/BNPL point-of-purchase credit, Vietnam): US$2.14B–2.61B GMV in 2025 (sourced; two editions
     of the same publisher disagree — see note) [S1][S2]; forecast US$2.70B in 2026 (sourced) [S2]
     Context ceiling (stock): total consumer credit (banks + fincos) ≈ VND 3 million billion at end-Q3 2024,
     +15.7% YoY (sourced) [S5]; consumer lending grew ≈26% in 2025 (sourced) [S4]
SAM: US$1.41B–1.72B (derived) = TAM × (1 − 34% pay-in-4 share) — i.e., instalment BNPL/POS volume
     compatible with HLB's 3–24-month tenures [S1]
SOM: [DECISION REQUIRED] — HLB volume, geography and capture targets are not in the brief; sensitivity only (below)
Growth rate (CAGR): BNPL 21.4% (2026–2031, sourced [S2]) / 26.7% (2025–2030, sourced [S1]);
     historical 28.6% (2022–2025) [S2] and 58.3% (2021–2024) [S1]
```

**Methodology and notes** `[R4 Assumptions]`
- **TAM approach (top-down, sourced).** BNPL/POS gross merchandise value is the closest published proxy for "credit at the purchase moment". ResearchAndMarkets' 2025 edition gives US$2.61B for 2025 **[S1]**. Its 2026 edition gives US$2.14B for 2025 (actual) and US$2.70B for 2026 **[S2]**. Both are shown as a range. The gap is logged as OQ-02-06. Finco in-store durables lending that is not branded "BNPL" may sit partly outside these figures; its size is not public (OQ-02-05).
- **Online cross-check (derived).** The online e-commerce checkout segment is about 41% of BNPL revenue **[S1]**, giving about US$0.88–1.07B of online BNPL. Vietnam B2C e-commerce was US$31B in 2025 (Ministry of Industry and Trade, via press) **[S47]**. Online BNPL is therefore about **2.8–3.5% of B2C e-commerce**, which is low penetration and consistent with the growth outlook. Credit-card ownership is reported at only about 10% of the population **[S17]** (Low confidence).
- **SAM narrowing (derived).** Pay-in-4 (about 34% share **[S1]**) is excluded because its short tenure falls outside HLB's 3/6/9/12/18/24-month menu **[Brief p.9]**. No further geographic narrowing is applied, because HLB can lend digitally nationwide under the electronic-lending rules **[S28]**. Whether HLB targets the whole country or only HCMC and Hanoi is a **[DECISION REQUIRED]** (OQ-02-04). If only HCMC and Hanoi, SAM shrinks by their unsourced share of volume.
- **Micro-merchant (MSME) credit.** No sourced size exists for online-merchant purchase or working-capital credit (OQ-02-15). Evidence that demand exists: sales-software platforms such as KiotViet already embed bank loans. For example, KBank and VPBank lend unsecured up to 400M VND on shop sales data **[S51]**, and 315,260 household businesses had registered cash-register e-invoicing by mid-2026 (+56% vs end-2025) **[S50]**.

**SOM sensitivity. This is an arithmetic illustration, not a forecast.** The capture rates below are hypothetical parameters for HLB to choose, not research findings. `[R4 Assumptions]`

| Scenario | SAM (derived) | Hypothetical capture rate | Implied annual GMV |
|---|---|---|---|
| Conservative | US$1.41B | 0.5% | ≈US$7.1M |
| Base | US$1.41–1.72B | 1.0% | ≈US$14.1–17.2M |
| Optimistic | US$1.72B | 2.0% | ≈US$34.5M |

Reference points for choosing a rate (not HLB estimates):
- Home Credit was reported at about 14% of Vietnam consumer finance in 2023. That is a mature leader after 17 years (secondary source; Low confidence).
- MWG PayLater expected "hundreds of thousands of users" in its first year **[S21]**.

**Key growth drivers (sourced).**
- E-commerce: B2C revenue went from US$25B (2024) to US$31B (2025) **[S47]**.
- Low card penetration **[S17]**.
- Consumer-finance recovery and the shift to durables and POS lending **[S3]**.
- Bank-funded pay-later partnerships **[S20][S21]**.
- 400M VND small-value threshold for electronic loans from 15 Aug 2026 **[S27]**.

### 2A. Unit-economics context: is a 380,000 VND review viable? `[R1 Diagnosis]` `[R2 Justification]`

**Sourced market price and cost points**

| Parameter | Value | Source | Confidence |
|---|---|---|---|
| Finco POS pricing (FE Credit) | 0–2.69%/month flat. Example: 1.41%/month flat, which is ~29.8% nominal APR / ~34.3% EAR (derived) | [S11] | High (primary) |
| HD Saison pricing | 20.41–34.78% p.a. on initial balance | [S13] | Low |
| Mcredit average lending rate | 25.44% (Jan 2025); 29.03% (to Nov 2025) | [S14] | High (primary) |
| E-wallet pay-later fees | 0% interest; 33k VND/month; conversion 7.5–30% (3–12 months); late fee 5.25–21% | [S18] | High |
| Merchant-borne cost of instalment (proxy) | Shopee card/instalment processing fee 4.91% (Jul 2025), then 6% (from 1 May 2026); SPayLater orders 0% to seller | [S22] | Medium |
| Global BNPL merchant discount rate (benchmark only) | ~3.29–6% + US$0.30 (Klarna, Afterpay) | [S23] | Medium (not Vietnam) |
| Vietnam BNPL merchant discount rate | **Not public** | — | OQ-02-12 |
| Cost of funds | Banking sector avg ~4.8% (2025); Home Credit ~7.1% (2025); Home Credit bonds 9.5–9.8% (May–Jun 2026); deposit rates up 3–4 pp at many banks since Q3 2025 | [S24] | Medium |
| Credit quality | Finco sector NPL 11.2% (2023 peak) → 7.2% (2025); credit cost 14.5% → 11.5%; Home Credit NPL 1.66% (2025) | [S3][S4][S7] | High |
| Bank consumer-lending NPL | ~4.1% (Feb 2024) | [S6] | Medium |

**Break-even ticket to recover the 380,000 VND review cost alone (derived).** This excludes acquisition, servicing, capital and collection costs, so real break-evens are higher.

| Pricing assumption | 3-month | 6-month | 12-month | Formula |
|---|---|---|---|---|
| Gross flat interest 1.41%/month (FE Credit example) | 8.98M VND | 4.49M | 2.25M | 380,000 ÷ (r × n) |
| Gross flat interest 2.69%/month (FE Credit max) | 4.71M | 2.35M | 1.18M | same |
| 0% consumer, merchant pays 4.91% | 7.74M (tenure-independent) | — | — | 380,000 ÷ MDR |
| Net spread: 29.8% APR − 4.8% bank cost of funds − 11.5% finco-level credit cost | **16.9M** | **9.6M** | **5.2M** | 380,000 ÷ (spread × average outstanding in years ≈ (n+1)/24) |
| Net spread with 3% credit cost (hypothetical, near bank consumer NPL level) | 10.4M | 5.9M | 3.2M | same |

**Reading.** The smallest HLB ticket (1.6M VND) cannot recover the review cost under any sourced pricing. The review alone is 23.75% of that ticket **[Brief p.9]**. On a risk-adjusted basis, short-tenure tickets below roughly 10–17M VND lose money on the review alone. This is quantitative evidence for the brief's statement that "the review may cost more than the loan earns" **[Brief p.5]**. HLB's actual pricing, cost of funds and loss rates are not public, so these figures use market proxies (OQ-02-01 to 02-03). `[R4 Assumptions]`

---

## 3. Trend Radar

| # | Trend | Signal | Horizon | Implication for product | Rubric |
|---|---|---|---|---|---|
| 1 | Bank-led PayLater replaces standalone BNPL fintechs | High | Now–2028 | Build as a bank-grade, partner-embedded decision API; compete with Cake/TPBank models, not fintech apps | R2, R3 |
| 2 | Consumer finance recovery, with mix shifting to durables, POS lending and embedded finance | High | Now–2027 | Demand is there; the winners will be those with low credit cost and low cost per decision | R1, R2 |
| 3 | Mandatory biometric, chip-ID eKYC across banking | High | In force 2024–26 | Build identity checks on chip-CCCD NFC or VNeID plus face match; documents are no longer needed and are no longer enough | R3 |
| 4 | Data-protection and AI governance tightening (PDPL, AI Law high-risk list, CIC breach) | High | 2026–Sep 2027 | Purpose-specific consent, explainability logs, human oversight of the "refer" band, conformity assessment | R3, R4 |
| 5 | Open data rails: Open API, credit-scoring sandbox, e-invoicing for household businesses | Medium–High | 2025–Mar 2027 | Design cash-flow features from consented bank and partner data; use the sandbox for alternative-data scoring | R2, R3 |
| 6 | Alternative-data scoring vendors mature but under financial and regulatory pressure | Medium | Now–2027 | Treat vendors as optional features behind a champion–challenger design; run PDPL due diligence | R3, R4 |
| 7 | AI adoption in Vietnamese banks is mainstream | High | Now | AI is expected; value must be shown against the baseline, as the brief requires | R2 |
| 8 | E-commerce and live-commerce growth; TikTok Shop's rise | High | Now–2028 | Checkout partners are concentrated (Shopee + TikTok ≈ 97% of marketplace GMV); plan both direct-merchant and platform integrations | R3 |
| 9 | Gig and platform workforce of about 2M, mostly without social insurance | Medium | Now–2028 | A sizeable thin-file segment with platform income records; responsible-lending guardrails needed | R1, R3 |
| 10 | Rising funding costs in 2025–26 | Medium | 2026–27 | Bank cost-of-funds advantage over fincos widens; price transparently | R2 |
| 11 | Loan rules liberalised for small electronic loans (Circular 29/2026) | High | From 15 Aug 2026 | Whole HLB ticket range (≤90.1M) is a "small-value loan" under the 400M threshold; lender sets per-customer electronic-lending limits | R2, R3 |

### Trend detail

**Trend 1: Bank-led PayLater replaces standalone fintech BNPL**
- **Signal strength:** High
- **Evidence:** Atome exited in 2023 and about 10 start-ups exited **[S17]**. SPayLater (TPBank/VPBank), MoMo (TPBank/MBV/VCBNeo) and ZaloPay (CIMB/Lotte) are all bank- or finco-funded **[S20]**. Cake runs MWG PayLater end to end **[S21]**. Fundiin partnered with CIMB **[S15]**. Analysts describe the shift toward regulated-institution PayLater with longer terms **[S21]**. Indonesia's POJK 32/2025 (effective 15 Dec 2025) now allows only banks and fincos to offer BNPL **[S53]**.
- **Implication:** HLB's natural shape is a lender plus decision engine behind partners' checkouts, not a consumer app.

**Trend 2: Consumer finance recovery and product-mix shift**
- **Signal strength:** High
- **Evidence:** Consumer lending grew about 26% in 2025, with banks growing about twice as fast as fincos. Finco NPL fell from 11.2% to 7.2% and credit cost from 14.5% to 11.5% **[S3][S4]**. Fincos are pivoting to "white-label lending, embedded finance, BNPL" **[S3]**. Home Credit's loan book grew 37.4% in 2025 **[S7]**.
- **Implication:** Fincos will chase the same embedded volume, so speed and pricing will compete. HLB wins only if its cost per decision and credit cost are both lower.

**Trend 3: Mandatory biometric, chip-ID eKYC**
- **Signal strength:** High
- **Evidence:**
  - Circular 17/2024 (effective 1 Jul 2024; individual biometrics from 1 Jan 2025) replaced Circular 16/2020 and requires matching against chip-CCCD or VNeID biometrics **[S30]**.
  - Decision 2345/QĐ-NHNN requires biometrics for transfers above 10M VND or 20M VND/day (from 1 Jul 2024) **[S32]**.
  - Circular 50/2024 (effective 1 Jan 2025, staged to 1 Jul 2026) and Circular 77/2025 (from 1 Mar 2026) cover online banking security **[S33]**.
  - Circular 45/2025 (effective 5 Jan 2026) requires biometric authentication before cards can transact electronically **[S31]**.
  - Decree 69/2024 covers VNeID e-identification **[S34]**.
- **Implication:** Real-time identity (R-3) should be built on NFC chip reading, face liveness and VNeID. This removes the "document friction" the brief cites **[Brief p.6]**.

**Trend 4: Data-protection and AI governance tightening**
- **Signal strength:** High
- **Evidence:**
  - PDPL Law 91/2025 and Decree 356/2025 took effect 1 Jan 2026, replacing Decree 13/2023 **[S35]**.
  - AI Law 134/2025 took effect 1 Mar 2026. Decision 33/2026/QĐ-TTg (30 Jun 2026) lists "AI-powered credit scoring and lending systems that make automated credit decisions" as high-risk. The finance-sector grace period to 1 Sep 2027 applies only to systems already operating before 15 Aug 2026; a new system must comply before deployment **[S37]** [Skill18 N6].
  - A CIC breach was confirmed on 11–12 Sep 2025. Attackers claimed about 160M records **[S42]**.
- **Implication:** HLB needs purpose-specific consent capture, reason codes, decision logs, human oversight of the refer band, and a conformity-assessment plan. The CIC breach also raises trust and fraud risk (leaked PII used for synthetic or impersonation applications). This strengthens the case for liveness and chip checks.

**Trend 5: Open data rails**
- **Signal strength:** Medium–High
- **Evidence:**
  - Circular 64/2024 on Open API took effect 1 Mar 2025, with full compliance due by 1 Mar 2027 and consent-based third-party access **[S39]**.
  - Decree 94/2025 sandbox (effective 1 Jul 2025) covers credit scoring, Open API and P2P, for up to 2 years plus extensions **[S38]**.
  - Household-business lump-sum tax was abolished from 1 Jan 2026. Revenue is now evidenced by e-invoices and bank flows, and 315,260 households had registered cash-register e-invoicing by H1 2026 **[S50]**.
  - HLB's Sales Ledger (Sổ Bán Hàng) partnership links accounts to accounting and e-invoicing and cut account opening from about 300 to about 80 seconds **[S52]**.
- **Implication:** For MSME borrowers, consented sales-ledger and e-invoice data is a lawful, observable income signal, consistent with the brief's CASA-first mitigation **[Brief p.8]**.

**Trend 6: Alternative-data scoring vendors**
- **Signal strength:** Medium
- **Evidence:**
  - **Trusting Social:** 2025 revenue US$23.1M, with Vietnam its largest market. It has scored more than 1B consumers and works with 130+ financial institutions, but posted a 2025 net loss of US$87.5M, mostly a fair-value loss **[S43]**.
  - **CredoLab:** smartphone-metadata scorecards, lists Vietnam among its markets **[S44]**.
  - **MobiFone mobiCredit:** a telco trust-scoring service **[S45]**.
  - **PCB:** a private credit bureau owned by 11 banks under Decree 58/2021 **[S40]**.
  - Specific Viettel scoring partnerships and FiinGroup scoring products could not be confirmed. They are not asserted here.
- **Implication:** Vendor data can add lift for first-time borrowers (14.5% of inbound). The PDPL ban on buying or selling personal data and its purpose-specific consent rule require legal due diligence on each vendor's data provenance (OQ-02-14).

**Trend 7: AI adoption in banks is mainstream**
- **Signal strength:** High
- **Evidence:** More than 70% of credit institutions use AI or machine learning, including for credit scoring and fraud (SBV IT Department, Jun 2026) **[S46]**. The SBV Governor has called for "practical and responsible AI … transparency, accountability and data protection" (Aug 2026) **[S46]**. Globally, 58% of finance functions used AI in 2024 (Gartner; the brief's p.2 statistic) **[S55]**.
- **Implication:** AI alone is no differentiator. The rubric rewards incremental value over the baseline and operational viability **[Brief p.11]**.

**Trend 8: E-commerce growth**
- **Signal strength:** High
- **Evidence:** B2C e-commerce reached US$31B in 2025, about 10% of retail **[S47]**. Four-marketplace GMV was about US$16.4B (+35%). TikTok Shop's share rose to about 41% and Shopee's fell to about 56% **[S47]** (Medium; Metric data via secondary site).
- **Implication:** Partner concentration is high, so a direct-merchant and offline strategy matters too.

**Trend 9: Gig and platform workforce**
- **Signal strength:** Medium
- **Evidence:** About 2M digital-platform workers (World Bank, 22 Sep 2026). In a survey of about 5,000, over 90% relied on gig work as primary income and only 20% had social insurance **[S48]**. A separate estimate counts about 600,000 ride-hailing and delivery drivers **[S48]**.
- **Implication:** Platform income records exist but income is volatile. Affordability rules must use smoothed income and stress buffers.

**Trend 10: Rising funding costs**
- **Signal strength:** Medium
- **Evidence:** Deposit rates have risen 3–4 pp at many banks since Q3 2025. Finco bond yields reached 9.5–9.8% **[S24]**.
- **Implication:** Fincos' cost disadvantage is structural. Banks with CASA can undercut them on price.

**Trend 11: Small-value electronic loans liberalised**
- **Signal strength:** High
- **Evidence:** Circular 29/2026/TT-NHNN was signed 30 Jun 2026 and takes effect 15 Aug 2026. It amends Circular 39/2016 and raises the small-value loan threshold from 100M to 400M VND (200M for people's credit funds). It removes the prior 100M VND cap on electronic loans and requires each lender to set its own per-customer electronic-lending limit. Lenders may choose verification technology for returning customers **[S27]**.
- **Implication:** The whole HLB ticket range fits. HLB must set its own per-customer exposure limits as policy [DECISION REQUIRED, OQ-02-16].

### Developing-market analogues (lessons, not competitors)
- **India.** Bajaj Finance offers "Easy EMI" approvals in about 3 minutes, pre-approved limits, and a network of 150,000+ partner stores in 4,000+ cities **[S53]**. The lesson: pre-approval and targeting (R-4) make POS decisions instant. The RBI Digital Lending Directions 2025 mandate a Key Fact Statement with APR, a cooling-off period, and a 5% cap on default-loss guarantees **[S53]**. This is a responsible-lending design pattern HLB can adopt voluntarily.
- **Indonesia.** POJK 32/2025 restricts BNPL to banks and finance companies, with debtor eligibility rules mandatory from 1 Jul 2026 **[S53]**. This signals the regional regulatory direction Vietnam may follow.
- **Kenya and the Philippines.** Not researched in this skill; covered by the literature review.

### Trends we are deliberately not acting on

| Trend | Why not |
|---|---|
| P2P lending sandbox (Decree 94) | HLB is a licensed bank. P2P adds regulatory complexity with no gain for POS decisioning. |
| Pay-in-4 short BNPL | Outside HLB's 3–24-month tenure menu **[Brief p.9]**, and dominated by 0% wallet products. |
| Cash-loan cross-sell | Fincos are cutting cash loans for risk reasons **[S3]**, and it is off-brief. |

---

## 4. Whitespace Map

**Whitespace 1: Mid-to-large-ticket, 6–24-month instalments at fair bank pricing, in-moment** `[R2 Justification]`
- **Evidence:** Wallet pay-later caps out at 8–20M VND (ZaloPay ≤8M, MoMo ≤20M) **[S18][S19]**. Home PayLater caps at 25M **[S10]** and Home Credit POS at 40M **[S9]**. Only FE Credit (≤100M) and SPayLater (≤50M, 0% only for 1–3 months) go higher **[S11][S20]**. Finco pricing sits around 25–35% p.a. **[S11][S13][S14]**, with cost of funds of 7.1–9.8% versus about 4.8% for banks **[S24]**.
- **Size of opportunity:** High. HLB's range already reaches 90.1M VND **[Brief p.9]**, and the 400M small-value rule applies **[S27]**.
- **Our angle:** An in-session decision for 10–90M VND tickets over 6–24 months at a lower, transparent APR, funded by CASA.

**Whitespace 2: Thin-file customers with evidence of cash flow** `[R1 Diagnosis]` `[R2 Justification]`
- **Evidence:** MoMo requires a clean CIC history **[S18]**. Home Credit and FE Credit approve on "CCCD only" or minimal income checks and price for risk: sector finco NPL is 7.2% and credit cost 11.5% **[S3][S4][S9][S11]**. The CIC covered 48.6M subjects, just over 60% of adults, in 2022 **[S41]**. That leaves roughly 40% invisible. The figure is dated (OQ-02-13). The brief says gig workers are declined "despite suspected repayment ability" **[Brief p.8]**. Fundiin and CIMB launched a product explicitly for thin-file customers in 2025 **[S15]**, which validates demand.
- **Size of opportunity:** High. This is 44% of HLB inbound **[Brief p.9]**.
- **Our angle:** Score on consented CASA and salary inflows, platform income, and sales-ledger and e-invoice data. Onboard CASA first as the brief suggests **[Brief p.8]**. The result is lower credit cost than finco-style risk pricing and higher approval than CIC-only wallets.

**Whitespace 3: Purchase and inventory credit for online MSMEs and household businesses, tied to their digital ledgers** `[R2 Justification]` `[R3 Concept]`
- **Evidence:** Tax reform from 1 Jan 2026 makes household-business revenue observable through e-invoices **[S50]**. KiotViet-embedded bank loans (up to 400M VND unsecured) prove the model **[S51]**. HLB already has the Sales Ledger integration **[S52]**. None of the consumer pay-later competitors mapped here serve merchants as borrowers.
- **Size of opportunity:** Medium. This is 14.5% of inbound, and the market size is unsourced (OQ-02-15).
- **Our angle:** Embedded purchase finance for merchants inside HLB's Sales Ledger, with sales data as the primary underwriting signal.

**Whitespace 4: Transparent, responsible credit as a brand position** `[R3 Concept]`
- **Evidence:** Wallet fee stacks run to late fees of 5.25–21% and conversion fees of 7.5–30% **[S18]**. Consumer protection is tightening: the draft Circular 43 amendment restricts collection hours to 7am–9pm **[S29]**, and FiinGroup reports a new debt-collection ethics code **[S3]**. Trust has been damaged by the CIC breach **[S42]** and by "black credit" apps **[Skill 01 S5]**.
- **Size of opportunity:** Medium. It is a differentiator, not a market in itself.
- **Our angle:** One all-in APR shown before acceptance (following India's Key Fact Statement pattern **[S53]**), affordability checks, explainable declines, and no stacked fees.

**Whitespace 5: Merchant economics** `[R2 Justification]`
- **Evidence:** Marketplaces charge merchants 4.91–6% on card or instalment orders, but 0% on their own pay-later **[S22]**. Global BNPL merchant fees run about 3.3–6% **[S23]**. Vietnam BNPL merchant fees are not public (OQ-02-12).
- **Size of opportunity:** Medium.
- **Our angle:** Lower merchant subsidy for 0% promotions, funded by bank cost of funds. This needs HLB's pricing decision (OQ-02-01).

---

## 5. Competitive Minimum Bar

| Capability | Must-have or differentiator | Market evidence | HLB today |
|---|---|---|---|
| Decision within the checkout session (≤ ~3 min; best in class ≤60 s) | **Must-have** | Home Credit 3 min; Home PayLater 60 s; MoMo 3 min; MWG 2 min; Fundiin ~5 s [S9][S10][S18][S21][S15] | ❌ 1.8–4.6 days |
| No documents: chip-CCCD/VNeID biometric eKYC, e-contract, OTP | **Must-have** (and legally required biometrics) | CCCD-only offers [S9][S11]; Circulars 17/2024, 45/2025, 50/2024 [S30][S31][S33] | ❌ physical/scanned docs |
| Automated CIC check | **Must-have** | MoMo uses CIC [S18] | ⚠️ manual officer review |
| Omnichannel embedding (in-store + online + app) via partner API | **Must-have** | Home Credit, MWG PayLater omnichannel [S9][S21] | ⚠️ redirect |
| 0% promotional option (merchant-subsidised) | **Must-have** for durables and electronics | FE Credit, Home Credit, SPayLater, MWG [S9][S11][S20][S21] | ? |
| Tenures up to 24 months; limits of at least 20–40M | **Must-have** for durables | [S9][S11][S21] | ✅ (3–24 m, ≤90.1M) |
| Transparent APR / Key Fact Statement | Differentiator (a must-have in India [S53]) | Wallet fee stacks [S18] | ? |
| Cash-flow and alternative-data underwriting for thin-file customers | **Differentiator** | Only Fundiin/CIMB claims it explicitly [S15] | ❌ |
| Pre-approved limits and targeted offers (R-4) | Differentiator | Bajaj pre-approved EMI [S53]; wallet in-app limits [S18] | ❌ |
| Explainability, human oversight, AI-Law conformity | **Must-have before deployment** (≤ 1 Mar 2027 for a new system; see RF-8) | [S37] | n/a |
| Merchant-borrower (MSME) purchase finance | Differentiator | KiotViet bank loans [S51] | ⚠️ Sales Ledger exists [S52] |

---

## 6. Regulatory Flags (Vietnam; checked as of 28 Sep 2026)

| # | Instrument | Effective | What it means for the design | Verification | Rubric |
|---|---|---|---|---|---|
| RF-1 | **Law on Credit Institutions 2024 (32/2024/QH15)** | 1 Jul 2024 | Replaces Law 47/2010. Banks and fincos agree interest rates and fees with customers under the law. The Civil Code's 20% p.a. cap (Art. 468) applies to civil loans, **not to credit institutions** [S25][S26]. There is no statutory APR cap for POS credit, but the SBV applies moral suasion: fincos must publish average lending rates (Notice 527/TB-VPCP, Directive 01/CT-NHNN 2024) [S14]. | High (law firms + legal press + primary disclosure) | R4 |
| RF-2 | **Circular 39/2016 on lending, as amended by Circular 06/2023** (electronic lending: eKYC, automated appraisal, e-contracts), **Circular 12/2024**, and **Circular 29/2026** | 06/2023: 2023; 29/2026: **15 Aug 2026** | Automated electronic credit appraisal is expressly permitted [S28]. Circular 29/2026 raises the small-value loan threshold to **400M VND**, removes the 100M electronic-lending cap, requires each lender to set **its own per-customer limit**, and lets lenders choose verification technology for returning customers [S27]. | High (chinhphu.vn listing + CafeF + DIV + VNBA) | R2, R3 |
| RF-3 | **Circular 43/2016 on finco consumer lending (+ Circular 18/2019)** | 2017 / 2019 | Applies to **finance companies, not to HLB as a bank**. Relevant if HLB partners with or benchmarks against fincos. A draft amendment was under consultation from 23 Oct 2025 (Công văn 9294/NHNN-CSTT): collection only 7am–9pm, limits on cash-disbursement share, risk warnings [S29]. **Whether it has been issued could not be verified.** FiinGroup also reports a reduction of the minimum consumer-lending share for fincos from 70% to 65% [S3] (not verified). **Decision 15/QĐ-HHNH** (the VNBA debt-collection Code of Conduct) is now **verified**: it was issued and took effect on 31 Mar 2026 [Skill20 E2]. | Medium (draft); Low (70%→65%, Decision 15) | R4 |
| RF-4 | **Circular 17/2024 (account opening; replaces Circular 16/2020)** and **Circular 18/2024 (cards)**, amended by **Circular 45/2025** | 17/2024: 1 Jul 2024 (individual biometrics 1 Jan 2025); 45/2025: 5 Jan 2026 | eKYC must match against chip-CCCD biometrics or VNeID. Cards cannot transact electronically before biometric authentication. If HLB opens a CASA account at checkout (the CASA-first mitigation) or issues a card-based limit, these rules apply [S30][S31]. | High | R3 |
| RF-5 | **Decision 2345/QĐ-NHNN** (biometrics for online transfers >10M VND/transaction or >20M/day), now within **Circular 50/2024** (online-banking security; staged to 1 Jul 2026) and **Circular 77/2025** (from 1 Mar 2026, extends scope to Mobile Money) | 1 Jul 2024; 1 Jan 2025 to 1 Jul 2026; 1 Mar 2026 | Disbursements and repayments through HLB Connect must meet the authentication tiers. Whether Decision 2345 remains a standalone instrument or has been fully superseded **is not verified** (OQ-02-09). | High (rules); Low (supersession) | R3 |
| RF-6 | **Decree 69/2024 on e-identification (VNeID)** | 1 Jul 2024 | VNeID level-2 accounts serve as an identity and biometric source. MobiFone offers VNeID-based checking (VNIDCheck) [S34][S45]. | Medium | R3 |
| RF-7 | **Personal Data Protection Law 91/2025/QH15 + Decree 356/2025** (replace Decree 13/2023) | **1 Jan 2026** | Consent must be specific to each purpose; silence is not consent. **Financial data (account and transaction history) and telco/social behavioural data are sensitive.** **Buying or selling personal data is banned** (fines up to 10× the proceeds). DPIA must be filed with A05 within 60 days. Breach notice within 72 hours for financial-service incidents. Fines up to VND 3bn, or 5% of revenue for cross-border violations. Small-enterprise grace periods do not apply to sensitive data [S35]. The Sep 2025 draft decree required disclosure when data is used for "credit scoring, rating, credit assessment" [S36]; **whether the final Decree 356 text keeps this wording needs verification** (OQ-02-07). | High (law); Medium (credit-scoring detail) | R3, R4 |
| RF-8 | **Law on Artificial Intelligence 134/2025/QH15 + Decision 33/2026/QĐ-TTg** (list of 46 high-risk systems, 30 Jun 2026) | Law 1 Mar 2026; **finance-sector grace period to 1 Sep 2027 applies only to systems already operating before 15 Aug 2026**. A new system must comply before deployment (corrected 2026-09-30 per Skill 14 U-07 / Skill 18 UC-18-01) | "AI-powered credit scoring and lending systems that make automated credit decisions" are **high-risk**. Required: conformity assessment before deployment, technical documentation and logs, risk management, **human oversight and intervention**, transparency, incident reporting [S37]. Whether a human-in-the-loop "refer" band changes the classification is unknown (OQ-02-11). | High (3 law firms + consultancy) | R3, R4 |
| RF-9 | **Decree 94/2025 on the fintech sandbox** (credit scoring, Open API, P2P) | 1 Jul 2025 | Credit institutions can pilot credit-scoring solutions under SBV supervision for up to 2 years (+ up to 2 × 1-year extensions), within a certified scope. P2P limits: 100M VND per borrower per platform, 400M aggregate [S28][S38]. **Whether HLB needs the sandbox** or can deploy alternative-data scoring under its general licence **is unresolved** (OQ-02-17). | High (sandbox); Low (applicability) | R3, R4 |
| RF-10 | **Circular 64/2024 on Open API** | 1 Mar 2025; full compliance **by 1 Mar 2027** | A consent-based rail for customers to share bank data from other banks with HLB, which feeds cash-flow underwriting for salaried no-credit applicants [S39]. | High | R3 |
| RF-11 | **Credit information: CIC and Decree 58/2021** (private bureaus such as PCB) | Decree 58: 15 Aug 2021 | Credit institutions must report to CIC. PCB is a licensed private bureau [S40]. CIC recalibrated its individual score bands from **1 Jun 2026** [S41]. The **CIC breach** (confirmed 11–12 Sep 2025) exposed identity and debt data; card numbers and OTPs were not in scope [S42]. | High | R1, R3 |
| RF-12 | **BNPL-specific regulation** | None found | Vietnam has **no dedicated BNPL rulebook**. BNPL is lent under general lending, finco and e-wallet rules [S17][S1]. For contrast, Indonesia's POJK 32/2025 and India's RBI Directions 2025 exist [S53]. Monitor for an SBV BNPL framework. | Medium | R4 |
| RF-13 | **Household-business tax reform** (end of lump-sum tax) | 1 Jan 2026 | Revenue must be evidenced by e-invoices, cash-register invoices (mandatory above 1bn VND revenue) and bank flows. This creates lawful, verifiable MSME income data [S50]. | Medium–High | R2, R3 |

**The rules that most constrain solution design:** RF-7 (PDPL: purpose-specific consent, sensitive financial data, data-trading ban), RF-8 (AI Law: human oversight and conformity for automated credit decisions — **before deployment** for a new system; see correction in RF-8), and RF-4/5 (biometric chip-ID eKYC). **The rules that most enable it:** RF-2 (Circular 29/2026: 400M small-value threshold, lender-set electronic limits) and RF-10 (Open API).

---

## Open Items Raised by This Skill

| # | Open item | Why it matters | Validation method | Carry to |
|---|---|---|---|---|
| OQ-02-01 | HLB's POS pricing: APR, fees, and willingness to offer merchant-subsidised 0% | Drives the break-even ticket (§2A) and whitespace 1 and 5 | HLB product or treasury input; or state as an explicit submission assumption | Skill 09 / 22 |
| OQ-02-02 | HLB's cost of funds and CASA ratio (proxy used: sector average ~4.8%) | Break-even and pricing advantage | HLB financial statements or treasury | Skill 09 / 22 |
| OQ-02-03 | Expected credit loss by segment (proxies: finco credit cost 11.5%, Home Credit NPL 1.66%, bank consumer NPL ~4.1%) | Risk-adjusted break-even; approval cut-offs | HLB pilot back-book; otherwise a stated assumption | Skill 04 / 10 / 22 |
| OQ-02-04 | SOM: HLB's volume target, capture rate, and geographic scope (national digital vs HCMC/Hanoi) | SOM is [DECISION REQUIRED] | Team or HLB decision | Skill 08 / 10 / 22 |
| OQ-02-05 | Size of finco in-store durables lending not counted as "BNPL" | TAM completeness | Buy FiinGroup Consumer Finance 2026 or the ResearchAndMarkets databook | Skill 22 (accept as limitation) |
| OQ-02-06 | ResearchAndMarkets gives 2025 BNPL GMV as US$2.61B (2025 edition) vs US$2.14B (2026 edition) | TAM range width | Use the 2026 edition as the "actual"; disclose the range | Skill 22 |
| OQ-02-07 | Final Decree 356/2025 wording on credit-scoring consent; whether telco and e-wallet data are "sensitive" | Legal basis for alternative data (R-1) | Legal review of the Decree 356 text | Lit review / Skill 19 / 22 |
| OQ-02-08 | Status of the Circular 43/2016 amendment; the 70%→65% finco rule; Decision 15/QĐ-HHNH collection code | Competitor constraints; partner model | thuvienphapluat / SBV portal check | Skill 21 |
| OQ-02-09 | Whether Decision 2345 is still standalone or superseded by Circulars 50/2024 and 77/2025 | Disbursement authentication design | Legal check | Skill 18 / 19 |
| OQ-02-10 | Kredivo Vietnam's current status (2026); Muadee unverifiable | Completeness of the competitor set | Check app store and VietCredit disclosures | Skill 22 (low priority) |
| OQ-02-11 | Does a human-reviewed "refer" band or hybrid design change high-risk AI classification or obligations? What conformity-assessment path applies to HLB? | Architecture and timeline (deadline: before deployment for a new system) | Legal review of Law 134/2025 and Decision 33/2026 | Skill 13 / 18 / 21 |
| OQ-02-12 | Vietnam BNPL merchant discount rates (Fundiin, Home Credit and others are not public) | Merchant-subsidy economics | Merchant interviews or partner quotes; otherwise a stated assumption | Skill 09 / 22 |
| OQ-02-13 | Current CIC coverage (latest found: 48.6M subjects, >60% of adults, 2022). Updates Skill 01 OQ-08. | Size of the thin-file population | CIC annual report / SBV | Skill 04 / 22 |
| OQ-02-14 | PDPL compliance of alternative-data vendors (Trusting Social, CredoLab, telco scores): data provenance and consent chain | The data-trading ban could make vendor data unusable | Vendor due diligence; legal review | Lit review / Skill 19 |
| OQ-02-15 | Size of online-MSME and household-business purchase and working-capital credit demand | MSME whitespace sizing | GSO/MoF household-business counts; KiotViet or Sapo data | Skill 04 / 22 |
| OQ-02-16 | HLB's per-customer electronic-lending limit policy (now lender-set under Circular 29/2026) | Credit policy and exposure | HLB credit-risk decision | Skill 13 / 22 |
| OQ-02-17 | Whether HLB needs the Decree 94 sandbox to use alternative-data or third-party credit scoring, or can proceed under its licence plus PDPL consent | Go-live path and timeline | Legal review; SBV consultation | Skill 14 / 21 / 22 |

---

## Source Index

| # | Source | Used for |
|---|---|---|
| S1 | ResearchAndMarkets, "Vietnam Buy Now Pay Later Business Report 2025" — GlobeNewswire, 17 Feb 2025: https://www.globenewswire.com/news-release/2025/02/17/3027131/28124/en/Vietnam-Buy-Now-Pay-Later-Business-Report-2025-BNPL-Market-to-Grow-by-36-5-this-Year-to-Reach-2-61-Billion-Investment-Opportunity-Forecasts-to-2030.html ; BusinessWire, 21 Feb 2025: https://www.businesswire.com/news/home/20250221652599/en/ | BNPL GMV 2025 (US$2.61B), CAGR, pay-in-4 34%, online checkout 41%, player list |
| S2 | ResearchAndMarkets, "Vietnam BNPL Business and Investment Report 2026" — GlobeNewswire, 29 Jan 2026: https://www.globenewswire.com/news-release/2026/01/29/3228868/0/en/Vietnam-Buy-Now-Pay-Later-Business-and-Investment-Report-2026-A-7-12-Billion-Market-by-2031-Featuring-MoMo-Home-Credit-FE-Credit-Kredivo-and-Fundiin.html | GMV 2025 US$2.14B, 2026 US$2.70B, 2031 US$7.12B, CAGRs |
| S3 | FiinGroup, "Vietnam Consumer Finance 2026 — Winning through the industry reset" (28 May 2026): https://fiingroup.vn/en/news-fg/vietnam-consumer-finance-2025-review-winning-through-the-industry-reset-id2813754.html | 2025 recovery, product mix, embedded-finance pivot, Decision 15/QĐ-HHNH |
| S4 | VnEconomy, "Cho vay tiêu dùng bứt tốc, nợ xấu giảm mạnh từ mức đỉnh" (FiinGroup data, Jun 2026): https://vneconomy.vn/cho-vay-tieu-dung-but-toc-no-xau-giam-manh-tu-muc-dinh.htm | +26% consumer lending 2025; finco NPL 11.2%→7.2%; credit cost 14.5%→11.5%; 70%→65% |
| S5 | VnEconomy, "Thị trường tài chính tiêu dùng phục hồi" (2 Jan 2025): https://vneconomy.vn/thi-truong-tai-chinh-tieu-dung-phuc-hoi.htm | Consumer credit ≈VND 3 million billion (Q3 2024), +15.7%; finco NPL 9.6% |
| S6 | Nhân Dân, "Nâng cao tính lành mạnh của cho vay tiêu dùng" (22 Apr 2024): https://nhandan.vn/nang-cao-tinh-lanh-manh-cua-cho-vay-tieu-dung-post805825.html | 15 of 16 fincos active; finco share ~5% (138.8 trillion VND); bank consumer NPL ~4.1% |
| S7 | Tuổi Trẻ, Home Credit 2025 results (27 Mar 2026): https://tuoitre.vn/tai-san-tang-gan-37-home-credit-viet-nam-bao-lai-hon-2-000-ti-20260327143458699.htm ; VietnamBiz: https://vietnambiz.vn/home-credit-viet-nam-cong-bo-ket-qua-kinh-doanh-nam-2025-voi-loi-nhuan-tang-607-2026327141740167.htm ; The Leader (SCBX deal): https://theleader.vn/home-credit-viet-nam-bao-lai-ky-luc-ngung-sap-nhap-voi-scb-thai-lan-d45031.html | Home Credit profit, NPL 1.66%, loan growth, customers, SCBX |
| S8 | Vietnam.vn, Home Credit 2024 results: https://www.vietnam.vn/en/home-credit-viet-nam-cong-bo-ket-qua-kinh-doanh-nam-2024 | 2024 NPL 1.76%, profit |
| S9 | Home Credit Vietnam product pages: https://www.homecredit.vn/vay-tra-gop-thiet-bi-dien-tu ; https://www.homecredit.vn/blog/vay-20-trieu-clx | POS durables terms, 3-min, CCCD only |
| S10 | Home PayLater partner page: https://www.homepaylater.vn/doi-tac/the-gioi-di-dong/ | 25M limit, 60-s decision, 500+ partners |
| S11 | FE Credit, phone/electronics loans: https://www.fecredit.com.vn/vay-mua-dien-thoai-dien-may/ | Terms, 0–2.69%/month, pricing example |
| S12 | VPBank press release, 2025 results: https://www.vpbank.com.vn/en/tin-tuc/thong-cao-bao-chi/2026/vpbank-lap-ky-luc-loi-nhuan-30600-ty-dong-nam-2025-tang-truong-53 ; The Investor: https://theinvestor.vn/vpbank-ceo-defends-higher-bad-debt-as-trade-off-for-growth-d18914.html | FE Credit PBT > VND 600bn; 2023 NPL (Low) |
| S13 | HD Saison: https://www.hdsaison.com.vn/ ; TaiChinhVui: https://taichinhvui.vn/vay-tra-gop-hd-saison-12-2025-huong-dan-va-thong-tin-chi-tiet/ ; Tima: https://tima.vn/tin-tuc/vay-tien-tra-gop-hd-saison-2629.html | Reach, pricing range, approval time (Low) |
| S14 | Mcredit, "Thông báo về lãi suất cho vay bình quân": https://mcredit.com.vn/vi/tin-hoat-dong/thong-bao-ve-lai-suat-cho-vay-binh-quan-020125 | Average lending rate 25.44% / 29.03%; SBV disclosure directives |
| S15 | Fundiin–CIMB (PR Newswire, Feb 2025): https://www.prnewswire.com/apac/news-releases/fundiin-partners-with-cimb-bank-vietnam-empowering-the-next-wave-of-financial-inclusion-302387581.html ; VIR: https://vir.com.vn/fundiin-signs-deal-with-cimb-bank-vietnam-123693.html ; ThinkZone: https://thinkzone.vn/blog/why-we-invest-fundiin-relieve-financial-burdens-for-consumers ; Genesia: https://www.genesiaventures.com/en/investment-fundiin-3/ | Fundiin model, 5-s decision, partners, funding, thin-file product |
| S16 | VietCredit–Kredivo: https://www.vietcredit.com.vn/en/news/vietcredit-cooperates-with-kredivo-in-launching-new-product-buy-now-pay-later/ ; The Paypers: https://thepaypers.com/payments/news/kredivo-launches-buy-now-pay-later-in-vietnam | Kredivo model (2021–22) |
| S17 | DealStreetAsia, "Atome exits Vietnam" (6 Jul 2023): https://www.dealstreetasia.com/stories/atome-ree-pay-bnpl-ops-vietnam-350420 ; CafeF (20 Jul 2025): https://cafef.vn/cuoc-dua-trong-linh-vuc-tai-chinh-ty-usd-fe-credit-shopee-lotte-deu-da-nhap-cuoc-the-gioi-di-dong-hop-tac-voi-cake-tham-vong-ve-lai-san-choi-188250720085517057.chn | Atome exit; ~10 start-ups exited; ~10% credit-card holders; FE PayNow |
| S18 | MoMo Ví Trả Sau: https://www.momo.vn/vi-tra-sau ; MoMo instalment blog: https://www.momo.vn/blog/ban-can-luu-y-gi-khi-tra-gop-vi-tra-sau-tren-momo-c116dt2387 ; TPBank: https://tpb.vn/cn-thanh-toan-cho-ca-nhan-vay-momo | Limits, fees, lenders, eligibility, ~2M users |
| S19 | ZaloPay: https://zalopay.vn/cap-nhat-bieu-phi-tai-khoan-tra-sau-6689 ; https://zalopay.vn/dich-vu/tai-khoan-tra-sau | 8M limit, 37 days, Lotte fee |
| S20 | CafeF, "Đứng sau SPayLater của Shopee, Ví Trả sau của Momo… là những ngân hàng nào?" (22 May 2026): https://cafef.vn/dung-sau-spaylater-cua-shopee-vi-tra-sau-cua-momola-nhung-ngan-hang-nao-188260522091837027.chn | Lender behind each pay-later product; SPayLater 50M; 2–5 min approvals |
| S21 | MWG PayLater — PR Newswire (2 Jul 2025): https://www.prnewswire.com/apac/news-releases/vietnams-leading-retailer-mwg-partners-with-cake-digital-bank-to-launch-paylater-service-302496608.html ; Tuổi Trẻ: https://tuoitre.vn/ra-mat-dich-vu-tra-sau-mwg-paylater-20250703165204075.htm ; Vietstock (Jul 2025): https://en.vietstock.vn/2025/07/vietnam8217s-bnpl-market-set-for-changes-amid-intensifying-competition-37-615310.htm | MWG PayLater terms; Cake end-to-end; 4→15 players; shift to PayLater |
| S22 | MISA eShop, Shopee payment fees: https://www.misaeshop.vn/25519/phi-thanh-toan-shopee/ ; Tuổi Trẻ/NLĐ (15 May 2026): https://tuoitre.vn/nld/shopee-va-tiktok-shop-dong-loat-ra-thong-bao-quan-trong-196260515094918377.htm | 4.91% → 6% card/instalment fee; 0% SPayLater |
| S23 | Chargeblast, "Affirm vs Afterpay vs Klarna merchant fees (2026)": https://www.chargeblast.com/blog/affirm-vs-afterpay-klarna-which-has-lower-merchant-fees ; Primer: https://primer.io/blog/klarna-vs-afterpay-for-merchants | Global BNPL merchant fee benchmark |
| S24 | Thời báo Ngân hàng, "Công ty tài chính xoay bài toán giá vốn" (23 Sep 2026): https://thoibaonganhang.vn/cong-ty-tai-chinh-xoay-bai-toan-gia-von-187548.html | Cost of funds: banks ~4.8%, Home Credit ~7.1%, bonds 9.5–9.8% |
| S25 | CafeF: https://cafef.vn/tai-sao-cac-cong-ty-tai-chinh-hay-ngan-hang-co-the-cho-vay-voi-lai-suat-vuot-qua-20-nam-ma-khong-vi-pham-luat-20221208154142411.chn ; LSVN: https://lsvn.vn/tai-sao-cong-ty-tai-chinh-cho-vay-lai-cao-nhung-khong-bi-phat1658819448-a121717.html ; Vietnam Law Magazine: https://vietnamlawmagazine.vn/laws-on-interest-rates-penalties-and-fees-in-lending-activities-of-creditinstitutions-in-vietnam-5162.html | Civil Code 20% cap not applicable to credit institutions; agreed-rate principle |
| S26 | Freshfields, "Vietnam's new law on credit institutions 2024": https://www.freshfields.com/en/our-thinking/briefings/2024/07/vietnams-new-law-on-credit-institutions-2024 ; LuatVietnam (Law 32/2024): https://english.luatvietnam.vn/tai-chinh/law-on-credit-institution-no-32-2024-qh15-296639-d1.html | LCI 2024, effective 1 Jul 2024 |
| S27 | Circular 29/2026/TT-NHNN — Government legal portal: https://vanban.chinhphu.vn/?pageid=27160&docid=218709&classid=1&orggroupid=4 ; DIV: https://div.gov.vn/tu-ngay-15-8-2026-ngan-hang-nha-nuoc-nang-han-muc-khoan-vay-gia-tri-nho-len-400-trieu-dong ; CafeF: https://cafef.vn/tin-vui-lon-cho-nguoi-vay-ngan-hang-cong-ty-tai-chinh-188260701114345854.chn ; VNBA: https://vnba.org.vn/vi/ngan-hang-nha-nuoc-ban-hanh-thong-tu-29-2026-tt-nhnn-quy-dinh-ve-hoat-dong-cho-vay-22478.htm | 400M small-value threshold; electronic-lending changes; effective 15 Aug 2026 |
| S28 | Tạp chí Ngân hàng, "Đảm bảo an toàn và hiệu quả giao dịch cho vay trên nền tảng số" (7 Oct 2025): https://tapchinganhang.gov.vn/dam-bao-an-toan-va-hieu-qua-giao-dich-cho-vay-tren-nen-tang-so-16569.html ; Thị trường Tài chính Tiền tệ: https://thitruongtaichinhtiente.vn/ngan-hang-nha-nuoc-sua-doi-thong-tu-39-2016-tt-nhnn-ve-hoat-dong-cho-vay-cua-tctd-41226.html | Circular 06/2023 electronic lending; banks offering online loans; P2P limits |
| S29 | Circular 43/2016 (Government portal): https://vanban.chinhphu.vn/default.aspx?pageid=27160&docid=188917 ; VIB Online draft amendment: https://vibonline.com.vn/du_thao/du-thao-thong-tu-sua-doi-thong-tu-432016tt-nhnn-ve-cho-vay-tieu-dung-cua-cong-ty-tai-chinh ; LuatVietnam draft: https://luatvietnam.vn/tai-chinh/du-thao-thong-tu-sua-doi-bo-sung-mot-so-dieu-cua-thong-tu-43-2016-tt-nhnn-172862-d10.html ; DIV (2017): https://div.gov.vn/tai-chinh-tieu-dung-nen-tang-moi-thiet-lap | Finco consumer-lending rules and draft amendment |
| S30 | LuatVietnam, Circular 17/2024/TT-NHNN: https://english.luatvietnam.vn/tai-chinh/circular-17-2024-tt-nhnn-opening-and-use-of-payment-accounts-at-payment-service-providers-358811-d1.html ; Circular 18/2024: https://english.luatvietnam.vn/tai-chinh/circular-18-2024-tt-nhnn-bank-card-operations-358546-d1.html | eKYC and biometric account opening; replaces Circular 16/2020 |
| S31 | Việt Nam News, "Việt Nam adopts mandatory biometric authentication for new bank cards" (5 Dec 2025): https://vietnamnews.vn/economy/1731179/viet-nam-adopts-mandatory-biometric-authentication-for-new-bank-cards.html | Circular 45/2025, effective 5 Jan 2026 |
| S32 | VietNamNet: https://vietnamnet.vn/en/biometric-authentication-required-for-transfers-over-vnd10-million-from-july-2264616.html ; HLB Vietnam notice: https://www.hlbank.com.vn/en/about-us/news-and-updates/register-biometric-data-according-to-decision-2345.html | Decision 2345 thresholds |
| S33 | LuatVietnam on Circular 77/2025: https://english.luatvietnam.vn/legal-news/circular-no-77-2025-tt-nhnn-tightens-online-banking-becurity-from-march-1-2026-4729-106500-article.html ; Tilleke & Gibbins on Circular 50: https://www.tilleke.com/insights/circular-50-strengthens-security-for-online-banking-in-vietnam/ | Online-banking security timelines |
| S34 | LuatVietnam, Decree 69/2024: https://english.luatvietnam.vn/hanh-chinh/decree-69-2024-nd-cp-on-electronic-identification-and-authentication-357444-d1.html ; Vietnam Law Magazine: https://vietnamlawmagazine.vn/new-decree-on-electronic-identification-and-authentication-noteworthy-points-72178.html | VNeID e-ID |
| S35 | DFDL, "Vietnam Personal Data Protection 2026": https://www.dfdl.com/insights/legal-and-tax-updates/vietnam-personal-data-protection-2026-what-foreign-organizations-need-to-know/ ; Tilleke & Gibbins: https://www.tilleke.com/insights/vietnams-new-personal-data-protection-law-a-closer-look/ ; FPF brief (Jan 2026): https://fpf.org/wp-content/uploads/2026/01/January-2026-FPF-Issue-Brief-Making-Sense-of-Vietnams-Latest-Data-Protection-and-Governance-Regime-1.pdf | PDPL 91/2025 and Decree 356/2025 provisions |
| S36 | Ministry of Public Security, "Bảo vệ dữ liệu cá nhân trong hoạt động tài chính, ngân hàng, hoạt động thông tin tín dụng" (draft decree, Sep 2025): https://bocongan.gov.vn/chinh-sach-phap-luat/bai-viet/bao-ve-du-lieu-ca-nhan-trong-hoat-dong-tai-chinh-ngan-hang-hoat-dong-thong-tin-tin-dung-1758105919 ; Xây dựng chính sách: https://xaydungchinhsach.chinhphu.vn/quy-dinh-bao-ve-du-lieu-ca-nhan-trong-hoat-dong-tai-chinh-ngan-hang-119250725172823942.htm | Credit-scoring consent and disclosure detail (draft) |
| S37 | Allen & Gledhill on Law 134/2025: https://www.allenandgledhill.com/vn/publication/articles/32667/s-new-law-on-artificial-intelligence-risk-based-regulatory-framework-in-force-1-march-2026 ; Vietnam Briefing on Decision 33/2026: https://www.vietnam-briefing.com/news/46-high-risk-ai-systems-to-face-enhanced-regulatory-oversight-in-vietnam.html/ ; LuatVietnam: https://english.luatvietnam.vn/law-no-134-2025-qh15-dated-december-10-2025-of-the-national-assembly-on-artificial-intelligence-422299-doc1.html ; Baker McKenzie: https://www.bakermckenzie.com/en/insight/publications/2026/02/vietnam-artificial-intelligence-law-foundation-and-outlook | AI Law; high-risk credit scoring; finance grace period to 1 Sep 2027 |
| S38 | Tilleke & Gibbins on Decree 94: https://www.tilleke.com/insights/vietnam-issues-fintech-sandbox-decree/20/ ; VILAF: https://www.vilaf.com.vn/blog/unlocking-fintech-growth-decree-94-opens-sandbox-for-p2p-lending-and-open-api/ ; DFDL: https://www.dfdl.com/insights/legal-and-tax-updates/vietnam-new-decree-no-94-2025-nd-cp-on-regulatory-sandbox-in-the-banking-sector/ | Sandbox scope, dates, duration |
| S39 | SBV press release on Circular 64/2024: https://sbv.gov.vn/en/w/press-release-on-issuance-of-circular-no.64/2024/tt-nhnn ; LuatVietnam: https://english.luatvietnam.vn/tai-chinh/circular-64-2024-tt-nhnn-deployment-of-open-application-programming-interfaces-in-the-banking-sector-387124-d1.html ; Brankas: https://blog.brankas.com/How-Open-Banking-is-Evolving-in-Vietnam-with-The-New-Circular-64 | Open API timelines and consent |
| S40 | LuatVietnam, Decree 58/2021: https://english.luatvietnam.vn/decree-no-58-2021-nd-cp-dated-june-10-2021-of-the-government-promulgating-regulations-on-the-provision-of-credit-information-services-203481-doc1.html ; PCB: https://pcb.vn/en/about-pcb/ | Private credit bureau regime |
| S41 | VnEconomy, "Độ phủ thông tin tín dụng Việt Nam đạt trên 48,6 triệu chủ thể" (7 Apr 2022): https://vneconomy.vn/do-phu-thong-tin-tin-dung-viet-nam-dat-tren-48-6-trieu-chu-the-chiem-hon-60-nguoi-truong-thanh.htm ; Người Quan Sát (CIC score change, 1 Jun 2026): https://nguoiquansat.vn/tu-1-6-cic-se-thay-doi-cach-danh-gia-hang-trieu-nguoi-tung-vay-ngan-hang-295593.html | CIC coverage; score recalibration |
| S42 | Việt Nam News (12 Sep 2025): https://vietnamnews.vn/economy/1725079/national-credit-info-centre-hacked-personal-data-at-risk.html ; Bloomberg: https://www.bloomberg.com/news/articles/2025-09-12/vietnam-says-national-credit-center-hacked-warns-of-data-theft ; The Vietnamese Magazine: https://thevietnamese.org/2025/09/massive-data-breach-160-million-vietnamese-credit-records-stolen-in-cic-hack/ | CIC breach |
| S43 | Fintech News Singapore, "Trusting Social parent firm posts US$87.5M loss in 2025" (1 Jul 2026): https://fintechnews.sg/133903/vietnam/trusting-social-revenue/ ; Trusting Social: https://trustingsocial.com/about-us/ | Alternative-data vendor scale and finances |
| S44 | CredoLab: https://www.credolab.com/ ; https://credolab.com/news-press/how-credolab-reduces-banks-credit-risks-through-smartphone-metadata | Smartphone-metadata scoring; Vietnam presence |
| S45 | Ministry of Science and Technology portal (MobiFone mobiCredit, VNIDCheck): https://mst.gov.vn/mobifone-tham-gia-trien-lam-tai-hoi-nghi-so-ket-trien-khai-cac-chuong-trinh-trong-diem-ve-cchc-va-cds-quoc-gia-197251225103517314.htm | Telco scoring and VNeID checking |
| S46 | VIR, "Banks accelerate AI adoption" (3 Jun 2026): https://vir.com.vn/banks-accelerate-ai-adoption-154052.html ; VOV (18 Aug 2026): https://english.vov.vn/en/economy/vietnamese-central-bank-pushes-ai-adoption-while-tightening-risk-management-post1325181.vov ; VnEconomy: https://en.vneconomy.vn/banks-urged-to-use-data-to-improve-access-to-credit.htm | >70% of credit institutions use AI; SBV guidance |
| S47 | Tạp chí Công Thương (MoIT data): https://tapchicongthuong.vn/buc-tranh-thuong-mai-dien-tu-da-sac-103157.htm ; VnEconomy: https://vneconomy.vn/thuong-mai-dien-tu-viet-nam-tiep-tuc-but-pha-huong-den-muc-tieu-25-ty-usd-vao-2025.htm ; Vietnam Market Insights (Metric data): https://vnmarketinsights.com/ecommerce/ ; Vietnam Briefing: https://www.vietnam-briefing.com/news/vietnams-e-commerce-sector-outlook-in-2026.html/ | E-commerce size and platform shares |
| S48 | VnEconomy (World Bank data, 22 Sep 2026): https://en.vneconomy.vn/closing-the-social-safety-gap-for-vietnams-two-million-gig-economy-workers.htm ; VnExpress International: https://e.vnexpress.net/news/news/over-a-decade-later-vietnam-s-600-000-gig-drivers-remain-uninsured-workers-5084753.html | Gig workforce size and profile |
| S49 | Vietnam.vn (SBV financial inclusion): https://www.vietnam.vn/en/hon-87-nguoi-viet-nam-truong-thanh-co-tai-khoan-ngan-hang | ~87% of adults have a bank account (context) |
| S50 | VietNamNet: https://vietnamnet.vn/ho-kinh-doanh-su-dung-hoa-don-dien-tu-ke-khai-thue-ra-sao-tu-nam-2026-2470000.html ; einvoice.vn: https://einvoice.vn/tin-tuc/hkd-chuyen-sang-ke-khai-thue-tu-nam-2026 ; Trúc Lâm commune portal: https://truclam.thanhhoa.gov.vn/chuyen-doi-so/tu-ngay-1-1-2026-xoa-bo-hinh-thuc-thue-khoan-doi-voi-ho-kinh-doanh-634738 | End of lump-sum tax; e-invoice adoption |
| S51 | KiotViet Finance: https://finance.kiotviet.vn/ ; KiotViet–KBank: https://www.kiotviet.vn/vay-von-kinh-doanh-khong-tai-san-dam-bao-tu-kbank-nhu-the-nao/ ; VietinBank–KiotViet: https://www.vietinbank.vn/tin-tuc/dang-bo/vietinbank-va-kiotviet-dua-giai-phap-ngan-hang-tich-hop-toi-gan-hon-voi-khach-hang-kinh-doanh-nha-ban-hang-20250108064209-00-html | Embedded MSME lending on sales data |
| S52 | Vietnam.vn, "Hong Leong Việt Nam – ngân hàng SME có trải nghiệm khách hàng tốt nhất": https://www.vietnam.vn/en/hong-leong-viet-nam-ngan-hang-sme-co-trai-nghiem-khach-hang-tot-nhat | HLB Sales Ledger integration; 300→80 s account opening |
| S53 | Indonesia POJK 32/2025 — SSEK: https://ssek.com/blog/indonesia-introduces-a-dedicated-bnpl-regulatory-framework/ ; Fintech News Indonesia: https://fintechnews.id/110330/lending/ojk-indonesia-bnpl-paylater-regulations-limits/ · India RBI Digital Lending Directions 2025 — Legal500: https://www.legal500.com/developments/thought-leadership/the-rbis-digital-lending-directions-2025-a-unified-code-for-a-fragmented-sector/ ; Argus Partners: https://www.argus-p.com/updates/updates/rbi-digital-lending-directions-2025-an-overview/ · Bajaj Finance: https://www.bajajfinserv.in/easy-emi-consumer-durables-loan | Developing-market analogues |
| S55 | Gartner, "58% of Finance Functions Using AI in 2024" (11 Sep 2024): https://www.gartner.com/en/newsroom/press-releases/2024-09-11-gartner-survey-shows-58-percent-of-finance-functions-use-ai-in-2024 | Origin of the brief's p.2 statistic |
| Skill 01 S5 / S6 | Cited from `01-foundation/01-product-strategy.md`: VietNamNet "black credit" and VIR Home Credit digital (see that file's Source Index) | Informal-credit context; Home Credit CIC plus external databases |

*S54 is intentionally unused: a candidate source on total system credit was dropped because its figure was not needed.*
