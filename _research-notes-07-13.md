# Research Notes — Skills 07 & 13 (Central AI Credit-Profile Engine for HLB Vietnam PoP Lending)
**Compiled:** 2026-09-30 | **Status:** scratch evidence file (research subagent) | **Rule:** figures recorded only where the source (or a search-engine extract of it) states them; otherwise "figure unverified".

Legend: [Confidence | Source type]. Source types: regulator / company (filing, IR, official site) / peer-reviewed / NGO-research (CGAP etc.) / press / vendor-blog.
"Via search extract" = figure quoted from a search-engine summary of the named page, not a full-page read; treat one notch lower than stated.

---

## Topic 1 — Analogous successes: central credit/risk profile reused across the lifecycle

### Nubank / Nu Holdings (Brazil, Mexico, Colombia)
- Nu acquired ~80–90% of customers organically (word of mouth / unpaid referral); credit cards and loans offered with limits that "grow over time" as users build history ("low and grow"). [High | company — SEC F-1, 2021] https://www.sec.gov/Archives/edgar/data/1691493/000119312521314359/d213207df1.htm (via search extract)
- FY2024: >114M customers, 20.4M net adds in year; revenue $11.5B (+58% FXN); net income ~ $2.0B; credit card portfolio $14.6B (+28% FXN); 90+ NPL ~7%. [High | company — Q4 2024 earnings release, 20 Feb 2025] https://international.nubank.com.br/company/nu-holdings-ltd-reports-fourth-quarter-and-full-year-2024-financial-results/ (via search extract; 90+ NPL figure from call summary — Med)
- Proprietary lending models strengthened via 2024 Hyperplane acquisition; claims ~29M customers got their first-ever credit card through Nubank. [Low | vendor-blog (Whitesight)] https://whitesight.net/nubanks-ai-model-rewrites-credit-underwriting/ — figure unverified against Nu primary source.
- Blended CAC "< US$5", NPS ~90, marketing ~2% of revenue. [Low | vendor-blog] https://whitesight.net/inside-nubanks-playbook-for-cost-efficient-hypergrowth/2/ — figures unverified in primary source.
- Mechanism relevant to HLB: one customer data platform + proprietary models reused for (a) first-limit decision, (b) automated limit increases (announced limit-increase waves to tens of millions of customers — Brazilian press, figure unverified), (c) cross-sell (personal loans, secured lending), (d) referral-driven acquisition.

### Grab / GrabFin (Southeast Asia — gig drivers & merchants; most relevant analogue)
- Loan portfolio outstanding $536M at 31 Dec 2024, up 64% from $326M; Q4 2024 disbursals $639M (+44% YoY). [High | company — SEC 20-F FY2024 / 6-K] https://www.sec.gov/Archives/edgar/data/1855612/000185561225000013/ck0001855612-20241231.htm (via search extract)
- Grab impact page (2025 figures): "68% of driver-partner borrowers accessed formal credit for the first time through Grab"; "1 in 2 driver-partners who took a loan through Grab did so to avoid taking loans from unlicensed payday lenders"; "$3.2B in loans disbursed, +47% YoY"; "1 in 3 active driver-partners has a loan with Grab"; ">90% of merchant borrowers are MSMEs". [Med | company self-reported impact page, no survey method disclosed] https://www.grab.com/sg/sustainability/partners/financial-inclusion (full-page fetch)
- Repayment mechanic: driver Cash Loan repaid by automated **daily** deductions from Grab Driver Cash Wallet (fallback: Credit Wallet), sized to platform earnings. [High | company product page] https://grab.com/ph/driver/finance/cash-loan
- **Hong Leong Bank Malaysia precedent:** HLB x GFin Services (Grab affiliate) "Personal Loan: GRAB It Now" campaign, 1–31 Jul 2021 — pre-selected Grab drivers/delivery partners (age 21–60, min annual Grab earnings RM24,000, no HLB PL arrears) received GrabFinance in-app messages and applied via HLB online portal/branches. [High | company T&C/FAQ] https://www.hlb.com.my/content/dam/hlb/my/images/campaigns/2021/grab-pl-03/hlb-grab-it-now-tnc-en.pdf (via search extract) — proves HLB Group has already run partner-data pre-selection targeting of gig workers.

### Sea Limited / Monee (SeaMoney) — SPayLater (Shopee)
- Consumer & SME loans principal outstanding US$5.1B at end-2024 (+64% YoY; $4.2B on-book, $0.9B off-book); 90+ day NPL ratio 1.2–1.4% across 2024 quarters. [High | company — earnings releases/calls 2024] https://www.businesswire.com/news/home/20241111850616/en ; https://www.fool.com/earnings/call-transcripts/2025/12/23/sea-limited-se-q4-2024-earnings-call-transcript (via search extract)
- Took SPayLater **offline**: partnered with >1,000 electronics stores in Indonesia for customised SPayLater loans on mobile-phone purchases — a direct PoP analogue. [Med | company earnings call via search extract] same URLs.

### Mercado Pago (MercadoLibre, LatAm)
- Q4 2024 credit portfolio $6.0B (+77% YoY); 15–90 day NPL 7.8%; NIMAL 24% (−13pp YoY, attributed to card mix and "move upmarket"); >$1B merchant loans outstanding. [Med | company results via analyst summary] https://sergeycyw.substack.com/p/mercadolibre-q4-2024-earnings-analysis ; primary: https://www.sec.gov/Archives/edgar/data/1099590/000109959024000039/meli-20241106xex991.htm
- Expert interview: 2022–23 underwriting used an off-the-shelf bureau-style score and "was ineffective"; from late 2023 shifted to in-house data-science models using Mercado Libre marketplace data, enabling more aggressive card issuance. [Low | expert-network interview (In Practise)] https://inpractise.com/articles/mercado-libre-mercado-pago-buildout-and-nubank-competition — lesson: generic scorecards underperform on ecosystem data.

### Kredivo (Indonesia)
- ML with traditional + alternative data; scores >500,000 users/month; >60% of users received first credit access via Kredivo; NPF kept <5% in 2024 (OJK limit); also pulls SLIK (national bureau) — i.e. **bureau + alt-data blend, not alt-data alone**. [Med | press (Kontan, Indonesian)] https://keuangan.kontan.co.id/news/jaga-kualitas-pinjaman-credit-scoring-kredivo-analisa-500000-pengguna-per-bulan

### Upstart (US AI underwriting) — success then stress
- **CFPB No-Action Letter results (Aug 2019):** vs a traditional model, Upstart's alt-data/ML model approved **27% more applicants** with **16% lower average APR**; acceptance +23–29% and APR −15–17% across race/ethnicity/sex groups; near-prime (FICO 620–660) approved ~2× as often; **applicants under 25 were 32% more likely to be approved**; no disparities requiring further fair-lending analysis. [High | regulator (CFPB blog) via press/law-firm summaries] https://www.crowdfundinsider.com/2019/08/150380-cfpb-comments-on-first-no-action-letter-use-of-alternative-data-for-credit-approval-access-to-credit-increased-with-lower-aprs-for-consumers/ ; https://www.consumerfinancemonitor.com/2019/08/08/cfpb-gives-boost-to-use-of-alternative-data-and-machine-learning/ — note: simulation on Upstart's own data, not an independent RCT.
- 2023: 437,659 loans, $4.6B originated (−59% YoY); conversion 9.7% vs 14.1% in 2022; 2022: $11.2B, 1,129,672 loans. [High | company — Q4/FY2023 results, 13 Feb 2024] https://ir.upstart.com/news-releases/news-release-details/upstart-announces-fourth-quarter-and-full-year-2023-results (via search extract)
- 10-K: capital from institutional loan buyers fell as rates rose and defaults increased; 2023 bank failures further cut funding. [High | company SEC 10-K FY2022/2023] https://www.sec.gov/Archives/edgar/data/1647639/000164763923000021/upst-20221231.htm
- Lesson: a strong model does not protect a **funding-dependent** marketplace; HLB as a deposit-funded bank has the structural advantage Upstart lacked.

### Ant Group — Huabei / Jiebei / Sesame Credit (China)
- $37B IPO halted by regulators ~2 days before 5 Nov 2020 listing; April 2021 rectification: become financial holding company, sever "inappropriate links" between Alipay and Huabei/Jiebei; lending moved into Chongqing Ant Consumer Finance Co. (approved 2021). [High | press (Caixin, Al Jazeera, CNBC)] https://www.caixinglobal.com/2021-04-12/ant-group-to-remake-itself-to-fall-in-line-with-regulators-101690166.html ; https://www.cnbc.com/2023/01/04/ant-gets-approval-to-expand-its-consumer-finance-business.html
- Lesson: a single ecosystem-wide credit profile that is monopolistically tied to payments and funded off-balance-sheet drew regulatory reset — data must flow through licensed, consented channels.

### Klarna (BNPL)
- FY2022 net loss SEK 10.4B (~$1B), up from SEK 7.1B; net credit losses ~SEK 5.7B (~$545M); credit loss rate cut 37% vs 2021 after tightening underwriting. [Med | press (Sifted, The Stack) citing annual report] https://sifted.eu/articles/klarna-annual-losses-2022-1bn

### MoMo (Vietnam — local competitor/benchmark)
- "Ví Trả Sau" launched 2021; MoMo Credit Scoring (AI on in-app behaviour/transactions) supplements bureau data for partner banks; limits up to 20M VND; partner lenders include TPBank (early, 5M VND cap), MBV, VCBNeo. [Med | company blog + press] https://www.momo.vn/blog/cac-vi-tra-sau-de-duyet-nhat-hien-nay-c116dt2286 ; https://thitruongtaichinhtiente.vn/tpbank-va-momo-phoi-hop-ra-mat-vi-tra-sau-han-muc-toi-da-5-trieu-dong-36634.html ; https://www.momo.vn/tin-tuc/thong-cao-bao-chi/momo-va-vcbneo-thiet-lap-quan-he-doi-tac-chien-8836
- ">80% activate on first attempt" is from MoMo's own user survey — [Low | company marketing]. MoMo claims ~30M users (press, 15-year anniversary). [Med | press] https://daibieunhandan.vn/momo-ky-niem-15-nam-nguoi-khong-lo-fintech-viet-nam-dat-30-trieu-nguoi-dung-cong-nghe-ai-lam-chu-loi-van-hanh-10397472.html
- Implication: Vietnamese customers already experience "wallet-scored instant BNPL" from MoMo + bank partners; HLB's differentiation must be higher tickets (up to 90.1M VND), longer tenors, and bank-grade pricing.

### Tala (Kenya, Philippines, Mexico, India)
- Smartphone-data ML scoring; >$3.4B disbursed to ~8M customers (older figure; later "over $6B" claimed — figure unverified); avg loan $50–$500. [Low | aggregator/press] https://www.causeartist.com/companies/tala
- Lesson: micro-ticket, short-tenor; not directly comparable to 3–24 month instalments.

### KBank / Kasikornbank (Thailand)
- Data-driven unsecured digital lending initially offered **to existing customers on a selective basis**, combining automation + human review; K PLUS had 2M new users in 2022; US$2.7B programme targeting unbanked/underbanked, self-employed. [Med | press/company] https://www.bangkokpost.com/business/finance/1475489/kbank-digital-lending-busts-out-of-the-gate ; https://www.aap.com.au/aapreleases/cision20220712ae14379
- Lesson: incumbent-bank pattern = start with known CASA customers (pre-approval), then extend to new-to-bank.

### Paytm Postpaid (India)
- After RBI raised risk weights on unsecured consumer credit (100%→125%; cards 125%→150% for banks) on 16 Nov 2023, Paytm cut sub-₹50,000 postpaid (BNPL) disbursals ~50% and partners paused. [High | regulator action reported by press (Business Standard, Inc42)] https://www.business-standard.com/companies/news/paytm-to-cut-down-on-small-ticket-postpaid-loans-as-rbi-tightens-norms-123120601026_1.html ; https://businesstoday.in/personal-finance/news/story/rbi-increases-risk-weight-on-consumer-credit-exposure-of-banks-nbfcs-405984-2023-11-16
- Lesson: small-ticket BNPL volumes originated by a platform on behalf of a lender are highly sensitive to capital rules — HLB owning the balance sheet and the model avoids the partner-withdrawal risk.

### Not verified in this pass
- Tinkoff (Russia), PhonePe credit: not researched (time budget; sanctions context makes Tinkoff a poor exemplar).

### Implications for design (Topic 1)
1. Every scaled success blends **bureau + proprietary behavioural data** (Kredivo uses SLIK; Nubank/Mercado moved from generic scores to in-house) — the engine should ingest CIC as one input, not replace it.
2. The strongest analogues (Grab, KBank, HLB Malaysia x Grab) start with **pre-selected, already-observable customers** — supports the Awareness stage targeting existing low-risk CASA customers first.
3. Low-and-grow (Nubank) is the canonical mechanism for thin-file customers: small first limit, automatic graduation on repayment.
4. Balance-sheet ownership matters (Upstart, Paytm): a bank-owned engine on deposit funding is more resilient than a marketplace.
5. HLB Group has an existing playbook (Grab It Now 2021) for partner-data pre-selection of gig workers — cite as internal precedent.

---

## Topic 2 — Failure modes & counter-evidence

### Regulatory resets of ecosystem credit
- **Ant Group:** IPO (~$37B) suspended days before 5 Nov 2020; April 2021 forced rectification into a financial holding company; Huabei/Jiebei decoupled from Alipay and moved into a licensed consumer-finance company. [High | press] https://www.caixinglobal.com/2021-04-12/ant-group-to-remake-itself-to-fall-in-line-with-regulators-101690166.html ; https://www.aljazeera.com/economy/2021/4/13/bb-jack-mas-ant-groups-business-to-be-drastically-revamped
- **China P2P collapse:** ~5,000 platforms at 2017 peak (CNY 2.8T volume) → zero by mid-Nov 2020 (CBIRC announcement 27 Nov 2020). [High | academic brief (HKUST IEMS) + press (Caixin)] https://iems.ust.hk/assets/publications/thought-leadership-briefs/2022/hkustiems-huang-why-did-the-peer-to-peer-lending-market-fail-in-china-tlb61.pdf ; https://www.caixinglobal.com/2020-11-07/chinas-3-year-crackdown-leaves-just-3-p2p-lenders-standing-101624086.html
- **India RBI Digital Lending Guidelines (2 Sep 2022):** disbursal/repayment only between borrower's account and the regulated entity (no pass-through/pool accounts); **no automatic increase in credit limit without explicit borrower consent recorded for each increase**; applies to new loans immediately and existing by 30 Nov 2022. [High | regulator] https://www.rbi.org.in/scripts/FS_Notification.aspx?Id=12382 (content via law-firm summaries: https://khaitanco.com/thought-leaderships/RBI-notifies-Digital-Lending-Guidelines)
- **RBI risk-weight hike (16 Nov 2023):** unsecured consumer credit 100%→125%; bank card receivables 125%→150%; Paytm cut sub-₹50k postpaid ~50%. [High | regulator via press] https://businesstoday.in/personal-finance/news/story/rbi-increases-risk-weight-on-consumer-credit-exposure-of-banks-nbfcs-405984-2023-11-16 ; https://www.business-standard.com/companies/news/paytm-to-cut-down-on-small-ticket-postpaid-loans-as-rbi-tightens-norms-123120601026_1.html
- **UK BNPL:** brought under FCA regulation from **15 July 2026** — mandatory proportionate affordability checks, upfront disclosure of payment dates/amounts/consequences of missed payments. [High | regulator] https://www.fca.org.uk/news/press-releases/new-protections-confirmed-buy-now-pay-later-borrowers
- **Indonesia OJK:** licensed P2P lenders may access only camera, microphone and location on the phone — contacts/gallery harvesting banned after debt-shaming abuses. [Med | legal press (Hukumonline) + KrASIA] https://pro.hukumonline.com/a/lt5c11fecbb8c2b/ojk-urges-public-to-be-cautious-when-utilizing-p2p-fintech-services/

### Credit-loss / business-model failures
- **Klarna FY2022:** net loss SEK 10.4B (~$1B), net credit losses ~SEK 5.7B; tightened underwriting cut credit loss rate 37% vs 2021. [Med | press citing annual report] https://sifted.eu/articles/klarna-annual-losses-2022-1bn
- **Upstart 2022–23:** originations fell 59% to $4.6B in 2023 as loan buyers withdrew amid rising rates/defaults. [High | company] https://ir.upstart.com/news-releases/news-release-details/upstart-announces-fourth-quarter-and-full-year-2023-results
- **Affirm credit losses:** not researched in this pass (time budget).

### Over-indebtedness / consumer harm
- **CGAP Kenya digital credit survey (2018):** 47% of Kenyan digital borrowers repaid late, 12% defaulted; 16% borrowed to repay another loan; 20% cut food purchases to repay; bank digital-loan NPL 11.4% vs 9.69% whole bank book. [High | NGO-research (CGAP/World Bank-hosted)] https://www.microfinancegateway.org/sites/default/files/publications/files/digital_credit_survey_-_kenya_presentation_cgap_v3.pdf ; https://cgap.org/node/3430
- **CFPB BNPL report (2 Mar 2023):** BNPL borrowers much more likely to be highly indebted, revolve on cards, have delinquencies and use payday/pawn/overdraft; 88% of BNPL users also hold a credit card; concern over "loan stacking" invisible to bureaus. [High | regulator] https://www.nortonrosefulbright.com/en/inside-fintech/blog/2023/03/cfpb-publishes-new-findings-on-financial-profiles-of-buy-now-pay-later-borrowers (regulator report summarised by law firm)
- **Vietnam "tín dụng đen" apps:** MPS reported >200 online lending apps linked to black credit; HCMC police removed 27 apps and handled 133 cases/206 suspects Jan–Aug 2023; PM Official Telegram 766/CĐ-TTg (24 Aug 2023) on black credit; Quang Nam police busted "Great Vay" ring (VND 20 trillion ≈ $846M, APR ~2,346%, >1M borrowers, late 2020–Jul 2023). [High | press citing police/government] https://vneconomy.vn/hon-200-ung-dung-cho-vay-truc-tuyen-lien-quan-den-tin-dung-den.htm ; https://vneconomy.vn/cong-an-tp-hcm-triet-xoa-27-app-tin-dung-den-nup-bong-cong-ty-tai-chinh-thu-hoi-no.htm ; https://tuoitrenews.vn/news/society/20230724/police-in-vietnam-bust-846mn-loan-shark-ring-led-by-chinese/74580.html
  - Relevance: validates the brief's "seek credit outside formal banking" risk AND means Vietnamese consumers/regulators are primed to distrust app-based alt-data lenders — HLB's bank brand and consented data are a differentiator.

### Fairness / transparency failures
- **Apple Card (NYDFS, 23 Mar 2021):** analysis of ~400,000 NY applicants found **no unlawful gender discrimination**, but "deficiencies in customer service and a perceived lack of transparency undermined consumer trust". [High | regulator] https://www.dfs.ny.gov/reports_and_publications/press_releases/pr202103231
- **Apple Card (CFPB, 23 Oct 2024):** $89M total ($45M Goldman penalty, $25M Apple penalty, ~$19.8M redress); failures in dispute handling and **misleading consumers about interest-free Apple Card Monthly Installments** for Apple devices; Goldman temporarily barred from new card launches unless it can comply. [High | regulator] https://www.consumerfinance.gov/enforcement/actions/goldman-sachs-bank-usa/ ; https://www.consumerfinance.gov/enforcement/actions/apple-inc/
  - Directly relevant: PoP instalment "0%" marketing is an enforcement target — disclosure design matters as much as the model.

### Model drift
- COVID-19: credit decisioning recalibrated/tightened; models "significantly affected, at times overridden, or even turned off"; stimulus inflated scores, reducing their specificity. [Low–Med | vendor-blog + academic abstract] https://dotdata.com/blog/new-credit-risk-modeling-for-lending/ ; https://crc.business-school.ed.ac.uk/sites/crc/files/2025-11/Normalizing-Pandemic-Data-for-Credit-Scoring-abstract.pdf ; https://thesis.eur.nl/pub/62134/Thesis_stephan_vegter.pdf (Master's thesis, Netherlands — Low)
- Fed SR 11-7 (model risk management) requires ongoing monitoring/outcomes analysis. [High | regulator — well established; URL: https://www.federalreserve.gov/supervisionreg/srletters/sr1107.htm]

### Consent / data privacy (Vietnam)
- **Law on Personal Data Protection (passed 26 Jun 2025, effective 1 Jan 2026)**, guided by Decree 356/2025/ND-CP (replaces Decree 13/2023): consent must be voluntary, clear, and in text/verifiable electronic form — silence ≠ consent; fines up to 5% of prior-year revenue for cross-border transfer violations; up to 10× illegal gains for illegal personal-data trading; otherwise up to VND 3B. [High | law-firm analysis of statute] https://www.tilleke.com/insights/vietnams-new-personal-data-protection-law-a-closer-look/ ; https://conventuslaw.com/report/vietnams-new-personal-data-protection-law-imposes-administrative-fines-up-to-5-of-annual-revenue-for-non-compliance/ ; https://vietanlaw.com/decree-356-2025-vs-decree-13-2023-updates-on-personal-data-protection-in-vietnam/

### Implications for design (Topic 2)
1. **Consent per data source and per use** (scoring vs marketing vs referral) is a hard requirement under Vietnam PDPL 2026; marketing use of the credit profile (Awareness/Retention stages) needs its own consent basis.
2. **Limit increases should be offered, not silently applied** (RBI DLG explicit-consent rule is the strictest benchmark; adopt it voluntarily).
3. Build **anti-stacking checks** (CIC pull at each draw + internal exposure cap) — CFPB/CGAP evidence shows BNPL-style credit concentrates among already-indebted users.
4. Keep the engine on-balance-sheet and licensed (Ant/P2P/Paytm lessons); no pooled/pass-through accounts.
5. Don't harvest phone contacts/gallery (OJK precedent; Vietnam black-credit app stigma).
6. Explainability + adverse-action reasons and transparent 0%-instalment disclosure (Apple Card NYDFS + CFPB).
7. Champion–challenger + drift monitoring with human override (COVID lesson, SR 11-7).

---

## Topic 3 — Lifecycle mechanics evidence (for Skill 13 design)

### (a) Behavioural scoring & continuous limit management
- **Agarwal, Chomsisengphet, Mahoney & Stroebel, "Do Banks Pass Through Credit Expansions to Consumers Who Want to Borrow?" (QJE 2018; NBER w21567):** 8.5M US credit card accounts, 743 credit-limit regression discontinuities; marginal propensity to borrow (MPB) out of a limit increase falls from **59% for FICO < 660 to ~0 for FICO > 740**; banks' marginal propensity to lend is lowest exactly where MPB is highest (because low-score borrowers' marginal profitability is lower). [High | peer-reviewed] https://www.nber.org/system/files/working_papers/w21567/w21567.pdf ; https://pages.stern.nyu.edu/~jstroebe/PDF/ACMS_Passthrough.pdf
  - Design use: limit increases for thin-file/lower-score customers get used (drive volume) but raise risk; increases for prime customers are mostly unused. The engine should size graduation steps on **risk-adjusted marginal profit**, not usage propensity alone.
- **Aydin, "Consumption Response to Credit Expansions: Evidence from Experimental Assignment of 45,307 Credit Lines" (AER 2022, 112(1):1–40):** randomized limit increases at a Turkish bank; recipients borrow to spend **11 cents per dollar of extra limit in the first quarter and 28 cents by year 3**; long-run extra borrowing is **predominantly via instalments**. [High | peer-reviewed, RCT] https://www.aeaweb.org/doi/10.1257/aer.20191178
  - Design use: strongest causal evidence that post-repayment limit expansion drives instalment usage — supports the Retention stage "re-offer" mechanic. Also a caution: effects persist for years, so affordability must be re-checked.
- **RBI Digital Lending Guidelines (2022):** no automatic limit increase without explicit consent per increase. [High | regulator] https://www.rbi.org.in/scripts/FS_Notification.aspx?Id=12382

### (b) Pre-approval / propensity targeting of existing customers
- **McKinsey, "How advanced analytics are redefining banking" (2013):** one bank's next-product-to-buy (NPTB) model "increased the likelihood to buy three times over"; NPTB engines have boosted new sales "upwards of 30 percent"; several banks achieved a ten-fold increase in recommendation success rate. [Med | consultancy (not peer-reviewed); via search extract, full page timed out] https://www.mckinsey.com/capabilities/tech-and-ai/our-insights/how-advanced-analytics-are-redefining-banking
- **McKinsey Periscope brochure:** claims 5–10% revenue improvement and 25% sales-productivity boost in FS. [Low | vendor marketing] https://www.mckinsey.com/~/media/McKinsey/Business%20Functions/Marketing%20and%20Sales/Periscope/Resources/Brochures/Periscope-for-Financial-Institutions.pdf
- **KBank:** data-driven unsecured digital loans first offered **selectively to current customers**. [Med | press] https://www.bangkokpost.com/business/finance/1475489/kbank-digital-lending-busts-out-of-the-gate
- **HLB Malaysia x Grab (2021):** pre-selected drivers messaged in-app to apply. [High | company] https://www.hlb.com.my/content/dam/hlb/my/images/campaigns/2021/grab-pl-03/hlb-grab-it-now-tnc-en.pdf
- **Vietnam in-market benchmarks (existing-customer instant loans):** see Topic 4 (Techcombank 3-minute approval/disbursal; VPBank NEO; MB card-linked loans).

### (c) Uplift modelling for credit offers
- Retail-bank case: uplift modelling increased total sales ~10% while cutting mailing volume ~60%, more than doubling campaign profitability. [Low | vendor case study (Portrait Software via TDWI, 2007)] https://tdwi.org/articles/2007/10/18/case-study-top-retail-bank-more-than-doubles-campaign-profitability-using-latest-umt.aspx
- U.S. Bank Consumer Direct: incremental (uplift) models raised incremental revenue while reducing mail volume — figure unverified. [Low | vendor case study] https://www.pitneybowes.ca/docs/US/Products-Services/Software/Analytics/Marketing-Analytics/Portrait-Uplift/PDFs/US-Bank-case-study.pdf
- Academic: uplift modelling applied to credit-line-increase strategy (poster). [Low | academic poster] https://openprairie.sdstate.edu/datascience_symposium/2019/posters/12 ; response-dependent-cost targeting (arXiv 2003.06271) [Med | preprint] https://arxiv.org/pdf/2003.06271
  - Design use: target on **incremental** take-up (customers who would not have bought/borrowed otherwise) with a random holdout; the Agarwal MPB finding means propensity ≠ incrementality.

### (d) Personalised loan pricing / package offers & risk-based pricing constraints
- Legal scholarship distinguishes **risk-based pricing** (price varies with expected loss — generally lawful) from **personalised pricing** (equal-risk borrowers charged differently by willingness-to-pay — fairness/consumer-protection concern). [Med | academic legal (Oxford Business Law Blog 2023; Gillis, Harvard Business Law Review 2025)] https://blogs.law.ox.ac.uk/oblb/blog-post/2023/04/unfairness-personalized-credit-pricing ; https://journals.law.harvard.edu/hblr/wp-content/uploads/sites/87/2025/03/03_HLB_15_1_Gillis99-170.pdf
- **Keil, Berg, Burg & Puri, "The Economics of 'Buy Now, Pay Later': A Merchant's Perspective" (JFE 2025, vol. 171, 104093; NBER w33152):** offering BNPL raises merchant sales by **~20%**, driven by low-creditworthiness customers and products where the merchant has more market power; BNPL functions as merchant price discrimination via a subsidised loan; benefits outweigh costs for the merchant. [High | peer-reviewed] https://nber.org/system/files/working_papers/w33152/w33152.pdf
- **Di Maggio, Katz & Williams, NBER w30508 (2022):** BNPL access raises total spending and retail share beyond what substitution explains — "liquidity flypaper effect"; BNPL use rises when weekly salary drops. [High | working paper, NBER] https://www.nber.org/papers/w30508 — exact $/week figure unverified.
- Vietnam constraint: SBV Circular 43/2016 (consumer lending by **finance companies**) — rate-range rules and VND 100M outstanding cap per consumer apply to finance companies, not banks; banks fall under general lending circulars (39/2016 as amended). [Med | press/legal] https://vietnamnews.vn/economy/350994/outstanding-consumer-loans-capped-at-vnd100m-per-person.html
- CFPB Apple Card order: misleading marketing of interest-free instalments was sanctioned — package personalisation must keep APR/fees transparent. [High | regulator] https://www.consumerfinance.gov/enforcement/actions/apple-inc/
  - Design use: personalise **tenor, limit, down-payment and merchant-subsidised 0% eligibility** by risk grade (risk-based), but avoid WTP-based rate personalisation; publish a rate grid per risk tier.

### (e) Referral programmes in lending/fintech
- **Schmitt, Skiera & Van den Bulte, "Referral Programs and Customer Value" (Journal of Marketing 2011):** ~10,000 customers of a German bank over ~3 years; referred customers' value **≥16% higher**, contribution margin ~25% higher initially (gap narrows over time), churn ~18% lower (no erosion). [High | peer-reviewed] https://faculty.wharton.upenn.edu/wp-content/uploads/2012/04/Schmitt-Skiera-vandenBulte-2011-Referral-Programs-Customer-Value.pdf
- **Bryan, Karlan & Zinman, "Referrals: Peer Screening and Enforcement in a Consumer Credit Field Experiment" (AEJ: Micro 2015):** South African consumer lender, individual liability referral incentives; **no evidence of peer screening, but large enforcement effects** (referrer rewarded on referee's repayment → referee repays better). [High | peer-reviewed, field experiment] https://www.nber.org/papers/w17883
  - Design use: directly supports **repayment-conditioned referral rewards** (pay the referrer only after the referee's on-time instalments) — it's the enforcement channel, not screening, that works; don't treat "referred" as a risk-lowering score feature.
- **Nubank:** 80–90% of customers acquired organically/word of mouth (F-1). [High | company filing] https://www.sec.gov/Archives/edgar/data/1691493/000119312521314359/d213207df1.htm
- Responsible-lending caution: referral bonuses in credit can incentivise pushing debt onto friends/family (cf. CGAP over-indebtedness data) — no specific enforcement case found in this pass (qualitative).

### (f) Credit-builder / graduation evidence
- **Burke, Jamison, Karlan, Mihaly & Zinman, "Credit Building or Credit Crumbling?" (Review of Financial Studies 2022; CFPB/RAND/St. Louis Community CU RCT, n=1,531):** **null average effect** on credit scores; ML heterogeneity shows those **without** existing loans at baseline fare better, those **with** existing loans fare worse — driven by delinquency on pre-existing obligations (over-extension). [High | peer-reviewed RCT] https://www.nber.org/papers/w26110 ; https://www.povertyactionlab.org/sites/default/files/research-paper/working-paper_802_Credit-Building-Loans_UnitedStates_Oct2020.pdf
  - Design use: a "good-payer credit passport"/graduation track is most valuable for **no-debt thin-file** users (HLB's salaried-no-credit 20.5% and first-time 14.5%); for already-indebted users, graduation must be gated on total-debt affordability.
- **FICO x Affirm (Feb 2025):** 12-month study of >500,000 Affirm BNPL borrowers; including BNPL loans moved scores by roughly ±10 points for >85% of users, more often positive; most with ≥5 recent BNPL loans saw increases or no change; thin-file users affected more in either direction. [Med | company/vendor study] https://www.businesswire.com/news/home/20250204481887/en/FICO-and-Affirm-Unveil-Industry-Leading-Analysis-of-%E2%80%98Buy-Now-Pay-Later%E2%80%99-Loans
  - Vietnam: CIC collects all credit-institution loans, so HLB PoP instalments are automatically reported → on-time repayment builds a CIC file (mechanism; no Vietnamese outcome study found).
- **Nubank "low and grow"** limits grow as customers build history (F-1). [High | company]

### (g) Cash-flow-based / flexible repayment for gig & MSME borrowers
- **Grab driver Cash Loans:** repaid by automated **daily deductions** from driver earnings wallet. [High | company] https://grab.com/ph/driver/finance/cash-loan
- **Battaglia, Gulesci & Madestam, "Repayment Flexibility and Risk Taking" (Review of Economic Studies, 2024):** BRAC Bangladesh RCT, 2,717 borrowers, 2015–17; option to **delay up to 2 monthly repayments** in a 12-month loan → more business investment, higher revenues, and **lower default** for small microfinance clients (flexibility works as insurance). [High | peer-reviewed RCT] https://www.restud.com/repayment-flexibility-and-risk-taking-experimental-evidence-from-credit-contracts/ ; https://povertyactionlab.org/node/3713031
- **Shopify Capital:** repaid as a fixed % of daily sales (no sales → no remittance) with 60-day minimum "milestones"; >US$5.1B extended (press, date-dependent). [Med | company help/press] https://thelogic.co/news/shopifys-loan-business-is-booming/
  - Design use: for gig (15.0%) and MSME (14.5%) segments, offer **payday-aligned or sales-linked instalment schedules** and a limited "skip-a-payment" option; RCT evidence says this lowers, not raises, default.

### (h) Merchant/partner-side: embedded finance & merchant acquisition
- BNPL at the merchant raises sales ~20% (Keil et al. JFE 2025). [High | peer-reviewed]
- Sea/Shopee: SPayLater extended offline to >1,000 electronics stores in Indonesia for phone purchases. [Med | company call] https://www.fool.com/earnings/call-transcripts/2025/12/23/sea-limited-se-q4-2024-earnings-call-transcript
- Grab: >90% of merchant borrowers are MSMEs; Thailand merchant loans up to THB 10M. [Med | company] https://www.grab.com/sg/sustainability/partners/financial-inclusion ; https://grab.com/th/en/press/others/mexloanrevamp
- Mercado Pago: >$1B merchant loans outstanding (Q4 2024). [Med | analyst summary of company results]
- HLB Vietnam x Sổ Bán Hàng (13 Oct 2025): in-app HLB account opening, auto-bookkeeping, tax-compliant QR, e-invoice, and "financing" in one merchant app — the bank already has a merchant-ledger data channel. [High | company] https://www.hlbank.com.vn/en/about-us/news-and-updates/hlb-partners-with-so-ban-hang.html
  - Design use: "refer-a-merchant" loop — merchants onboarded via Sổ Bán Hàng become (i) PoP acceptance points and (ii) MSME borrowers scored on their own ledger.

### Implications for design (Topic 3)
1. **Graduation ladder** (low-and-grow): small first limit → step-ups triggered by N on-time instalments + refreshed affordability; each step-up **offered with explicit consent** (RBI benchmark), sized on risk-adjusted marginal profit (Agarwal) — expect ~11–28¢ per extra dong of limit to be drawn over time (Aydin).
2. **Target on uplift, not propensity**, with a permanent random holdout to measure incrementality (uplift literature; Agarwal MPB).
3. **Risk-tier rate grid + personalised tenor/limit/0%-subsidy eligibility**; no WTP pricing.
4. **Repayment-conditioned referral reward** (Bryan-Karlan-Zinman enforcement effect; Schmitt et al. +16% value, −18% churn).
5. **Credit passport** targeted at no-existing-debt thin files (Burke et al.); gate for indebted users.
6. **Cash-flow-aligned schedules** for gig/MSME (Grab daily deductions; BRAC flexibility RCT).
7. **Merchant flywheel** through Sổ Bán Hàng (Keil et al. +20% sales is the merchant value proposition).

---

## Topic 4 — Vietnam-specific enablers for the engine

### HLB Vietnam assets
- **HLB Connect / eKYC:** National ID Registry integration + eKYC cut onboarding **~80%, from ~300 s to ~80 s**; account opening embedded in the Sổ Bán Hàng app; 2026 Digital CX awards. [High | company press release, Apr 2026] https://aapnews.aap.com.au/news/cision20260428AE45287
- **HLB x Sổ Bán Hàng (13 Oct 2025):** in-app HLB account opening, auto-bookkeeping, tax-compliant QR, instant e-invoice, "financing"; incentives up to VND 3M/customer; no user counts disclosed. [High | company] https://www.hlbank.com.vn/en/about-us/news-and-updates/hlb-partners-with-so-ban-hang.html
- **HLB VN unsecured personal loan (existing product, benchmark for PoP):** VND 30M–500M; from 15%/yr fixed on declining balance; 12–60 months; salary ≥ VND 10M/month; ≥12 months employment; Hanoi/HCMC/Binh Duong only; max DSR 60%; 3% early-repayment fee in first 3 years; disbursed to HLB account. [High | company product page] https://hlbank.com.vn/vi/personal-banking/loan/unsecured-personal-loan.html
  - "Approval in 15 minutes" appears in an HLB VN blog per search extract only — [Low, figure unverified]; the 2020 "fully digital personal loan" article is about **HLB Malaysia**, not Vietnam. https://fintechnews.my/25189/various/hong-leong-bank-unveils-fully-digital-onboarding-experience-for-customers/
  - Implication: the existing product's 30M VND floor, salary-only eligibility and 3-city footprint exclude most of the PoP ticket range (1.6–90.1M) and all gig/MSME/first-time segments — this is the gap the engine fills.
- **HLB VN card instalment (0%) plan:** not found in this pass (HLB Malaysia has an Extended Payment Plan) — figure/product unverified for Vietnam.

### Credit bureau (CIC)
- CIC database reached **55.2 million records, covering >70% of the adult population** (2022–2025 review, reported Jan 2026). [Med | press citing CIC/SBV] https://baomoi.com/cic-phat-trien-co-so-du-lieu-thong-tin-tin-dung-dat-55-2-trieu-ho-so-c54646813.epi ; https://sbv.gov.vn/en/w/cic-tong-ket-hoat-dong-2025
- CIC does **not** collect deposit/payment-account data, balances or transaction histories (SBV statement) → CASA/salary-inflow signals are HLB's proprietary edge. [High | government] https://en.baochinhphu.vn/viet-nams-central-bank-confirms-credit-data-incident-at-credit-information-center-111250912192742851.htm
- **Counter-evidence: CIC cyberattack/data incident confirmed by SBV, 12 Sep 2025**; scope under investigation. [High | government] same URL — engine must not depend on a single bureau feed; design for bureau-unavailable fallback and treat bureau data as sensitive.
- Circular 15/2023/TT-NHNN governs credit information operations (new CIC data fields from 1 Jan 2025). [Med | legal database] https://english.luatvietnam.vn/tai-chinh/circular-15-2023-tt-nhnn-credit-information-operation-of-the-state-bank-281736-d1.html

### Open API (Circular 64/2024/TT-NHNN)
- Issued 31 Dec 2024, effective **1 Mar 2025**; banks with existing API connections must comply by **1 Mar 2027** (implementation plans due to SBV by 1 Jul 2025). Three basic Open API groups: (1) information queries (FX/interest rates); (2) **customer data access — account lists, transaction history, account details, with customer consent**; (3) payment services (payment initiation, e-wallet top-up/withdrawal). Customers must be able to view/withdraw consent; data access window 180 days unless otherwise agreed. [High | regulation via legal database] https://english.luatvietnam.vn/tai-chinh/circular-64-2024-tt-nhnn-deployment-of-open-application-programming-interfaces-in-the-banking-sector-387124-d1.html
  - Implication: consented transaction history from **other banks** (salary accounts elsewhere) can feed the affordability module for non-HLB applicants.

### Sandbox (Decree 94/2025/ND-CP)
- Promulgated 29 Apr 2025, effective **1 Jul 2025**; sandbox covers (a) **credit scoring**, (b) Open API data sharing, (c) P2P lending; **credit institutions may join credit scoring and Open API only (not P2P)**; testing up to 2 years; applicants must show inclusion, consumer benefit, risk management and consumer protection. [High | regulation via law firms] https://www.dfdl.com/insights/legal-and-tax-updates/vietnam-new-decree-no-94-2025-nd-cp-on-regulatory-sandbox-in-the-banking-sector/ ; https://english.luatvietnam.vn/tai-chinh/decree-94-2025-nd-cp-regulatory-sandbox-mechanism-in-banking-sector-399142-d1.html
- Credit scoring defined as IT solutions of credit institutions/foreign bank branches/fintechs scoring creditworthiness "to support credit-granting decisions". SBV Decision 2866/QĐ-NHNN (22 Jul 2025): P2P sandbox caps VND 100M per borrower per platform, VND 400M across platforms; Decision 2970/QĐ-NHNN (11 Aug 2025) on P2P–CIC connection. [Med | press] https://vneconomy.vn/khach-hang-duoc-vay-toi-da-100-trieu-dong-moi-nen-tang-p2p-lending.htm
- **Named sandbox participants for credit scoring: not found** (no public list located) — unverified.
- **Legal gaps (Banking Academy, Jul 2026):** no enumerated list of permissible alternative data, no tailored cross-sector consent templates, unclear purpose limitation when utility/e-wallet data is reused for credit, no right-to-explanation or algorithmic-audit standard, no interagency protocol; recommends tiered data classification, explanation rights with human review, bias audits. [Med | academic/policy (Quản lý Nhà nước journal)] https://www.quanlynhanuoc.vn/2026/07/28/du-lieu-thay-the-trong-cham-diem-tin-dung-tai-viet-nam-van-de-phap-ly-va-ham-y-chinh-sach/

### Data protection
- PDPL 2025 (effective 1 Jan 2026) + Decree 356/2025 — see Topic 2 (explicit, verifiable consent; fines up to 5% of revenue for cross-border breaches; up to 10× gains for illegal data trading).

### Competitor benchmarks — instant loans to existing customers in Vietnam
- **Techcombank** online instalment consumer loan: "approval and disbursement within 3 minutes", up to VND 100M, 20–36 months, from 1.2%/month (declining balance), income ≥ VND 10M/month, no bad debt. [High | company page] https://techcombank.com/thong-tin/blog/app-vay-tien-nhanh
- **VPBank NEO** (5-minute unsecured loan, VND 10–100M, from 15.9%/yr) and **MB** (card-linked loan up to 75% of card limit, VND 5–100M, ~3 minutes in-app): [Low | aggregated search extract, source page not confirmed] — figures unverified.
- **Standard Chartered Vietnam** runs a "pre-approved personal loan" programme for selected customers (indicative amounts, bank discretion). [Med | company T&C] https://www.sc.com/global/av/vn-08-personal-loan-pl-preapproved-vn.pdf
- **MoMo Ví Trả Sau** (up to VND 20M, via TPBank/MBV/VCBNeo) — see Topic 1.
- **Trusting Social** (telco/alt-data scoring vendor) claims >130 lenders across Indonesia, Vietnam, India, Philippines. [Low | vendor] https://partner.visa.com/site/partner-directory/trusting-social.html
- Viettel/MobiFone telco-scoring partnerships with banks: not verified in this pass.

### Black-credit context
- >200 black-credit lending apps (MPS report); PM Official Telegram 766/CĐ-TTg (Aug 2023). See Topic 2.

### Implications for design (Topic 4)
1. **Start where data is legal and owned:** HLB CASA/salary inflows (not in CIC) + CIC + Sổ Bán Hàng ledger (merchant consent) → Phase 1; Circular 64 consented cross-bank transaction data → Phase 2 (full compliance deadline 1 Mar 2027); telco/e-wallet via Decree 94 sandbox → Phase 3.
2. **Apply for the Decree 94 credit-scoring sandbox** to legitimise alt-data use for gig/first-time segments; use the Banking Academy gap list as the governance checklist (explanation rights, bias audit, tiered data classes).
3. **Speed benchmark is minutes, not days:** Techcombank 3 min, MoMo instant — the engine's "instant decision" KPI should target ≤ 3 minutes for pre-approved profiles.
4. **Price benchmark:** HLB UPL from 15%/yr; Techcombank from 1.2%/month; VPBank NEO from 15.9%/yr (unverified) — risk-tier grid should sit within this band.
5. **Bureau resilience:** CIC incident (Sep 2025) → cache/fallback rules and minimal bureau-data retention.

---

## Additional evidence (cross-topic)
- **Berg, Burg, Gombović & Puri, "On the Rise of FinTechs: Credit Scoring Using Digital Footprints" (Review of Financial Studies, 2020):** >250,000 purchases at a German e-commerce firm; a model using only 10 simple digital-footprint variables achieves **AUC 69.6% vs 68.3% for the credit-bureau score**; footprint **complements** bureau data. [High | peer-reviewed] https://www.fdic.gov/analysis/cfr/working-papers/2018/cfr-wp2018-04.pdf ; https://nber.org/papers/w24551 — the combined-model AUC figure was not verified in this pass.

---

## Source Index
1. Nu Holdings F-1 (SEC, 2021) — https://www.sec.gov/Archives/edgar/data/1691493/000119312521314359/d213207df1.htm
2. Nu Holdings Q4/FY2024 results (Feb 2025) — https://international.nubank.com.br/company/nu-holdings-ltd-reports-fourth-quarter-and-full-year-2024-financial-results/
3. Whitesight, Nubank AI underwriting / playbook (blog) — https://whitesight.net/nubanks-ai-model-rewrites-credit-underwriting/ ; https://whitesight.net/inside-nubanks-playbook-for-cost-efficient-hypergrowth/2/
4. Grab Holdings 20-F FY2024 (SEC) — https://www.sec.gov/Archives/edgar/data/1855612/000185561225000013/ck0001855612-20241231.htm
5. Grab financial inclusion impact page — https://www.grab.com/sg/sustainability/partners/financial-inclusion
6. Grab PH driver Cash Loan — https://grab.com/ph/driver/finance/cash-loan
7. Grab TH merchant loan revamp — https://grab.com/th/en/press/others/mexloanrevamp
8. HLB Malaysia "GRAB It Now" T&C (2021) — https://www.hlb.com.my/content/dam/hlb/my/images/campaigns/2021/grab-pl-03/hlb-grab-it-now-tnc-en.pdf
9. Sea Ltd Q3 2024 release — https://www.businesswire.com/news/home/20241111850616/en
10. Sea Ltd Q4 2024 call transcript — https://www.fool.com/earnings/call-transcripts/2025/12/23/sea-limited-se-q4-2024-earnings-call-transcript
11. MercadoLibre Q4 2024 analysis — https://sergeycyw.substack.com/p/mercadolibre-q4-2024-earnings-analysis ; MELI 6-K Q3 2024 — https://www.sec.gov/Archives/edgar/data/1099590/000109959024000039/meli-20241106xex991.htm
12. In Practise, Mercado Pago expert interview — https://inpractise.com/articles/mercado-libre-mercado-pago-buildout-and-nubank-competition
13. Kontan, Kredivo credit scoring — https://keuangan.kontan.co.id/news/jaga-kualitas-pinjaman-credit-scoring-kredivo-analisa-500000-pengguna-per-bulan
14. Upstart FY2023 results — https://ir.upstart.com/news-releases/news-release-details/upstart-announces-fourth-quarter-and-full-year-2023-results
15. Upstart 10-K FY2022 — https://www.sec.gov/Archives/edgar/data/1647639/000164763923000021/upst-20221231.htm
16. CFPB Upstart NAL results (press) — https://www.crowdfundinsider.com/2019/08/150380-cfpb-comments-on-first-no-action-letter-use-of-alternative-data-for-credit-approval-access-to-credit-increased-with-lower-aprs-for-consumers/ ; https://www.consumerfinancemonitor.com/2019/08/08/cfpb-gives-boost-to-use-of-alternative-data-and-machine-learning/
17. Caixin, Ant rectification (Apr 2021) — https://www.caixinglobal.com/2021-04-12/ant-group-to-remake-itself-to-fall-in-line-with-regulators-101690166.html
18. Al Jazeera, Ant revamp — https://www.aljazeera.com/economy/2021/4/13/bb-jack-mas-ant-groups-business-to-be-drastically-revamped
19. CNBC, Ant consumer finance approval (Jan 2023) — https://www.cnbc.com/2023/01/04/ant-gets-approval-to-expand-its-consumer-finance-business.html
20. Sifted, Klarna 2022 losses — https://sifted.eu/articles/klarna-annual-losses-2022-1bn
21. MoMo Ví Trả Sau blog — https://www.momo.vn/blog/cac-vi-tra-sau-de-duyet-nhat-hien-nay-c116dt2286
22. TPBank x MoMo Ví Trả Sau — https://thitruongtaichinhtiente.vn/tpbank-va-momo-phoi-hop-ra-mat-vi-tra-sau-han-muc-toi-da-5-trieu-dong-36634.html
23. MoMo x VCBNeo — https://www.momo.vn/tin-tuc/thong-cao-bao-chi/momo-va-vcbneo-thiet-lap-quan-he-doi-tac-chien-8836
24. MoMo 15-year anniversary (Đại biểu Nhân dân) — https://daibieunhandan.vn/momo-ky-niem-15-nam-nguoi-khong-lo-fintech-viet-nam-dat-30-trieu-nguoi-dung-cong-nghe-ai-lam-chu-loi-van-hanh-10397472.html
25. Causeartist, Tala profile — https://www.causeartist.com/companies/tala
26. Bangkok Post, KBank digital lending — https://www.bangkokpost.com/business/finance/1475489/kbank-digital-lending-busts-out-of-the-gate
27. KBank US$2.7B programme (AAP/Cision) — https://www.aap.com.au/aapreleases/cision20220712ae14379
28. Business Standard, Paytm postpaid cut — https://www.business-standard.com/companies/news/paytm-to-cut-down-on-small-ticket-postpaid-loans-as-rbi-tightens-norms-123120601026_1.html
29. Business Today, RBI risk weights — https://businesstoday.in/personal-finance/news/story/rbi-increases-risk-weight-on-consumer-credit-exposure-of-banks-nbfcs-405984-2023-11-16
30. RBI Digital Lending Guidelines (2022) — https://www.rbi.org.in/scripts/FS_Notification.aspx?Id=12382 ; Khaitan summary — https://khaitanco.com/thought-leaderships/RBI-notifies-Digital-Lending-Guidelines
31. HKUST IEMS, China P2P failure — https://iems.ust.hk/assets/publications/thought-leadership-briefs/2022/hkustiems-huang-why-did-the-peer-to-peer-lending-market-fail-in-china-tlb61.pdf
32. Caixin, P2P crackdown — https://www.caixinglobal.com/2020-11-07/chinas-3-year-crackdown-leaves-just-3-p2p-lenders-standing-101624086.html
33. FCA, BNPL protections — https://www.fca.org.uk/news/press-releases/new-protections-confirmed-buy-now-pay-later-borrowers
34. Hukumonline, OJK P2P data-access rule — https://pro.hukumonline.com/a/lt5c11fecbb8c2b/ojk-urges-public-to-be-cautious-when-utilizing-p2p-fintech-services/
35. CGAP Kenya digital credit survey — https://www.microfinancegateway.org/sites/default/files/publications/files/digital_credit_survey_-_kenya_presentation_cgap_v3.pdf ; https://cgap.org/node/3430
36. CFPB BNPL borrower profiles (Norton Rose summary) — https://www.nortonrosefulbright.com/en/inside-fintech/blog/2023/03/cfpb-publishes-new-findings-on-financial-profiles-of-buy-now-pay-later-borrowers
37. VnEconomy, >200 black-credit apps — https://vneconomy.vn/hon-200-ung-dung-cho-vay-truc-tuyen-lien-quan-den-tin-dung-den.htm
38. VnEconomy, HCMC police remove 27 apps — https://vneconomy.vn/cong-an-tp-hcm-triet-xoa-27-app-tin-dung-den-nup-bong-cong-ty-tai-chinh-thu-hoi-no.htm
39. Tuoi Tre News, "Great Vay" ring — https://tuoitrenews.vn/news/society/20230724/police-in-vietnam-bust-846mn-loan-shark-ring-led-by-chinese/74580.html
40. NYDFS Apple Card report press release (Mar 2021) — https://www.dfs.ny.gov/reports_and_publications/press_releases/pr202103231
41. CFPB enforcement, Goldman Sachs Bank USA — https://www.consumerfinance.gov/enforcement/actions/goldman-sachs-bank-usa/ ; Apple Inc. — https://www.consumerfinance.gov/enforcement/actions/apple-inc/
42. dotData, credit-model drift — https://dotdata.com/blog/new-credit-risk-modeling-for-lending/
43. Edinburgh CRC, pandemic data normalisation abstract — https://crc.business-school.ed.ac.uk/sites/crc/files/2025-11/Normalizing-Pandemic-Data-for-Credit-Scoring-abstract.pdf
44. Vegter, Erasmus thesis — https://thesis.eur.nl/pub/62134/Thesis_stephan_vegter.pdf
45. Fed SR 11-7 — https://www.federalreserve.gov/supervisionreg/srletters/sr1107.htm
46. Tilleke, Vietnam PDPL — https://www.tilleke.com/insights/vietnams-new-personal-data-protection-law-a-closer-look/
47. Conventus Law, PDPL fines — https://conventuslaw.com/report/vietnams-new-personal-data-protection-law-imposes-administrative-fines-up-to-5-of-annual-revenue-for-non-compliance/
48. Viet An Law, Decree 356 vs 13 — https://vietanlaw.com/decree-356-2025-vs-decree-13-2023-updates-on-personal-data-protection-in-vietnam/
49. Agarwal et al., NBER w21567 — https://www.nber.org/system/files/working_papers/w21567/w21567.pdf ; https://pages.stern.nyu.edu/~jstroebe/PDF/ACMS_Passthrough.pdf
50. Aydin, AER 2022 — https://www.aeaweb.org/doi/10.1257/aer.20191178
51. McKinsey, How advanced analytics are redefining banking — https://www.mckinsey.com/capabilities/tech-and-ai/our-insights/how-advanced-analytics-are-redefining-banking
52. McKinsey Periscope FS brochure — https://www.mckinsey.com/~/media/McKinsey/Business%20Functions/Marketing%20and%20Sales/Periscope/Resources/Brochures/Periscope-for-Financial-Institutions.pdf
53. TDWI uplift case study — https://tdwi.org/articles/2007/10/18/case-study-top-retail-bank-more-than-doubles-campaign-profitability-using-latest-umt.aspx
54. Pitney Bowes/Portrait, US Bank uplift case — https://www.pitneybowes.ca/docs/US/Products-Services/Software/Analytics/Marketing-Analytics/Portrait-Uplift/PDFs/US-Bank-case-study.pdf
55. SDSU uplift CLI poster — https://openprairie.sdstate.edu/datascience_symposium/2019/posters/12 ; arXiv 2003.06271 — https://arxiv.org/pdf/2003.06271
56. Oxford Business Law Blog, personalised credit pricing — https://blogs.law.ox.ac.uk/oblb/blog-post/2023/04/unfairness-personalized-credit-pricing ; Gillis, HBLR — https://journals.law.harvard.edu/hblr/wp-content/uploads/sites/87/2025/03/03_HLB_15_1_Gillis99-170.pdf
57. Keil, Berg, Burg & Puri, NBER w33152 / JFE 2025 — https://nber.org/system/files/working_papers/w33152/w33152.pdf
58. Di Maggio, Katz & Williams, NBER w30508 — https://www.nber.org/papers/w30508
59. Viet Nam News, Circular 43/2016 consumer loan cap — https://vietnamnews.vn/economy/350994/outstanding-consumer-loans-capped-at-vnd100m-per-person.html
60. Schmitt, Skiera & Van den Bulte (JM 2011) — https://faculty.wharton.upenn.edu/wp-content/uploads/2012/04/Schmitt-Skiera-vandenBulte-2011-Referral-Programs-Customer-Value.pdf
61. Bryan, Karlan & Zinman (AEJ Micro 2015), NBER w17883 — https://www.nber.org/papers/w17883
62. Burke et al., credit builder loan (RFS 2022) — https://www.nber.org/papers/w26110 ; J-PAL WP — https://www.povertyactionlab.org/sites/default/files/research-paper/working-paper_802_Credit-Building-Loans_UnitedStates_Oct2020.pdf
63. FICO x Affirm BNPL study (Feb 2025) — https://www.businesswire.com/news/home/20250204481887/en/FICO-and-Affirm-Unveil-Industry-Leading-Analysis-of-%E2%80%98Buy-Now-Pay-Later%E2%80%99-Loans
64. Battaglia, Gulesci & Madestam (REStud) — https://www.restud.com/repayment-flexibility-and-risk-taking-experimental-evidence-from-credit-contracts/ ; J-PAL — https://povertyactionlab.org/node/3713031
65. The Logic, Shopify Capital — https://thelogic.co/news/shopifys-loan-business-is-booming/
66. HLB VN x Sổ Bán Hàng — https://www.hlbank.com.vn/en/about-us/news-and-updates/hlb-partners-with-so-ban-hang.html
67. HLB VN Digital CX awards / eKYC release (Apr 2026) — https://aapnews.aap.com.au/news/cision20260428AE45287
68. HLB VN unsecured personal loan page — https://hlbank.com.vn/vi/personal-banking/loan/unsecured-personal-loan.html
69. Fintech News MY, HLB Malaysia digital onboarding (2020) — https://fintechnews.my/25189/various/hong-leong-bank-unveils-fully-digital-onboarding-experience-for-customers/
70. Baomoi, CIC 55.2M records — https://baomoi.com/cic-phat-trien-co-so-du-lieu-thong-tin-tin-dung-dat-55-2-trieu-ho-so-c54646813.epi ; SBV — https://sbv.gov.vn/en/w/cic-tong-ket-hoat-dong-2025
71. Báo Chính phủ, CIC data incident (Sep 2025) — https://en.baochinhphu.vn/viet-nams-central-bank-confirms-credit-data-incident-at-credit-information-center-111250912192742851.htm
72. LuatVietnam, Circular 15/2023 — https://english.luatvietnam.vn/tai-chinh/circular-15-2023-tt-nhnn-credit-information-operation-of-the-state-bank-281736-d1.html
73. LuatVietnam, Circular 64/2024 — https://english.luatvietnam.vn/tai-chinh/circular-64-2024-tt-nhnn-deployment-of-open-application-programming-interfaces-in-the-banking-sector-387124-d1.html
74. DFDL, Decree 94/2025 — https://www.dfdl.com/insights/legal-and-tax-updates/vietnam-new-decree-no-94-2025-nd-cp-on-regulatory-sandbox-in-the-banking-sector/ ; LuatVietnam — https://english.luatvietnam.vn/tai-chinh/decree-94-2025-nd-cp-regulatory-sandbox-mechanism-in-banking-sector-399142-d1.html
75. VnEconomy, P2P sandbox VND 100M cap — https://vneconomy.vn/khach-hang-duoc-vay-toi-da-100-trieu-dong-moi-nen-tang-p2p-lending.htm
76. Quản lý Nhà nước, alt-data legal issues (Banking Academy, Jul 2026) — https://www.quanlynhanuoc.vn/2026/07/28/du-lieu-thay-the-trong-cham-diem-tin-dung-tai-viet-nam-van-de-phap-ly-va-ham-y-chinh-sach/
77. Techcombank fast-loan page — https://techcombank.com/thong-tin/blog/app-vay-tien-nhanh
78. Standard Chartered VN pre-approved PL T&C — https://www.sc.com/global/av/vn-08-personal-loan-pl-preapproved-vn.pdf
79. Visa partner directory, Trusting Social — https://partner.visa.com/site/partner-directory/trusting-social.html
80. Berg et al., digital footprints (FDIC CFR WP / NBER w24551) — https://www.fdic.gov/analysis/cfr/working-papers/2018/cfr-wp2018-04.pdf ; https://nber.org/papers/w24551
