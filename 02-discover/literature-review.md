# Literature Review — AI & Alternative Data for Point-of-Purchase Credit
**Product:** HLB Point-of-Purchase Credit Decisioning (working name) | **Deliverable:** Discover-phase literature review (user-requested extra) | **Status:** Done (2026-09-28)
**Depends on:** 01-product-strategy.md, _requirements.md | **Feeds into:** 04-problem-research.md, 05-persona-research.md, 12-solution-proposal.md (preliminary)

> **Two tagging systems are used.** Rubric tags `[R1]` Problem Diagnosis · `[R2]` Intervention Justification · `[R3]` Solution Concept · `[R4]` Data & Assumptions refer to the competition marking rubric **[Brief p.11]**. Requirement tags **R-1** beyond-bureau assessment · **R-2** in-moment, proportional-cost decisions · **R-3** real-time identity & genuineness · **R-4** targeted offers refer to the case requirements **[Brief p.6]**.
> **Segment codes** (Exhibit 1–2, **[Brief pp.7–9]**): **SAL-C** salaried with credit history (35.5% of inbound) · **SAL-N** salaried, no credit history (20.5%) · **GIG** gig/platform workers (15.0%) · **MSME** online merchants (14.5%) · **FTB** first-time digitally active borrowers (14.5%).
> Citations are **[L#]** entries in the Source Index at the end. Case facts are **[Brief p.N]**. All quantified findings were checked against the paper's abstract, full text, or the regulator's own document, except where a row says *"secondary"* (verified in reporting of the source, not in the source itself) or *"vendor claim"*.

---

## 1. Scope, method & search strategy

### 1.1 Questions the review answers
1. Does non-bureau data predict repayment for people with thin or no credit files, and by how much? (R-1)
2. Do machine-learning (ML) models add value over logistic scorecards, and what governance do they need? (R-1, R-2)
3. What does the evidence say about automating decisions: speed, cost, and effects on defaults? (R-2)
4. What happens to consumers and merchants when credit is embedded at checkout (BNPL / point-of-sale instalments)? (R-2 responsible lending, R-4)
5. What cuts identity-verification cost and friction, and what fraud patterns does it face? (R-3)
6. How should offers be targeted so that they reach people who need credit and will use it responsibly? (R-4)
7. What in this evidence holds in Vietnam's data, regulatory and consent environment? (all)

### 1.2 Sources and queries
- **Databases and portals searched:** NBER, SSRN, BIS working papers, IMF eLibrary, Oxford Academic (RFS, WBER, EJ, QJE), Wiley (Journal of Finance), ScienceDirect (JFE, EJOR, JBF, JFS, Applied Soft Computing), INFORMS (Management Science), arXiv, CFPB / Federal Reserve / FCA / RBI / FATF / FSB publications, CGAP and World Bank Open Knowledge, Center for Global Development, ISEAS, Cambridge Core, State Bank of Vietnam and *Tạp chí Ngân hàng* (SBV's banking journal), Vietnamese legal databases (LuatVietnam, Thư viện Pháp luật), and law-firm briefings for Vietnamese regulation.
- **Representative queries:** "digital footprint credit scoring"; "mobile phone data predicts repayment"; "fintech alternative data invisible primes"; "machine learning credit scoring benchmark"; "reject inference"; "machine learning credit disparity"; "fintech mortgage processing speed"; "digital credit Kenya / Malawi"; "buy now pay later consumer outcomes / merchant sales"; "open banking credit access"; "UPI / Account Aggregator credit"; "eKYC Aadhaar cost"; "synthetic identity fraud"; "biometric authentication Decision 2345"; "uplift modelling targeting"; "algorithmic credit scoring Vietnam"; "chấm điểm tín dụng dữ liệu thay thế"; "Luật Bảo vệ dữ liệu cá nhân chấm điểm tín dụng".
- **Search window:** mainly 2010–2026, plus foundational pre-2010 work where it is still the reference (reject inference, 2004).

### 1.3 Inclusion criteria
- **Included:** empirical studies that report predictive or causal effects on credit access, defaults, pricing, spending, fraud or onboarding; regulator and standard-setter documents that set binding or de facto requirements; major-institution reports (BIS, IMF, World Bank / CGAP, FSB, FinRegLab); Vietnamese legal and academic sources.
- **Included with a flag (grade C):** consultancy and vendor figures, used only as corroboration and never as the sole basis for a claim.
- **Excluded:** opinion pieces without data; vendor marketing without a named methodology; sources whose claimed figures could not be traced. A number of search-snippet figures were **dropped** because they could not be verified in the source (for example, specific percentages attributed to deHaan et al. 2024 and to McKinsey 2021; see §7).

### 1.4 Evidence grading
| Grade | Meaning |
|---|---|
| **A** | Peer-reviewed journal article, or an empirical publication / binding rule from a regulator or standard-setter |
| **B** | Working paper (NBER, BIS, IMF, SSRN, SAFE), or a report from a major policy/research institution (World Bank / CGAP, FSB, FinRegLab, CGD, ISEAS, PERC) |
| **C** | Industry, consultancy, vendor or news source (used for corroboration or Vietnam context only) |

---

## 2. Executive synthesis (key takeaways)

| # | Takeaway | Grade | Evidence | Implication for HLB | Rubric |
|---|---|---|---|---|---|
| T1 | **Cheap, checkout-native data predicts default about as well as a bureau score, and best of all when combined with it.** In a German e-commerce setting, digital-footprint variables alone reached AUC 69.6% against 68.3% for the bureau score, and 73.6% combined. Discriminatory power for customers with no bureau score was similar (72.2%). | A | Berg et al. 2020 **[L1]** | Device, channel, time-of-day and email signals are free at checkout and cover FTB and SAL-N. They should **complement** CIC, not replace it. | `[R1][R2][R3]` |
| T2 | **Much of the inclusion gain comes from the data, not the algorithm.** A fintech's model cut rejection probability sharply compared with a traditional model (the traditional model implied a 70% higher probability of rejection). About two-thirds of the effect came from the extra data and the rest from the more sophisticated method. | A | Di Maggio, Ratnadiwakara & Carmichael **[L9]** | HLB's "AI vs baseline" case should separate **data value** (consented inflows, platform and sales data) from **model value**. A rules-based scorecard on the new data may capture most of the gain. | `[R2][R4]` |
| T3 | **The advantage of ML and alternative data shrinks as credit history lengthens.** It is largest for thin files. | A/B | Gambacorta et al. 2024 **[L4]**; Björkegren & Grissen 2020 **[L2]** | Put model investment into GIG, MSME and FTB (44% of inbound) and SAL-N. For SAL-C, the bureau baseline plus straight-through processing is likely enough. | `[R1][R2]` |
| T4 | **Ensembles (e.g. XGBoost) beat logistic scorecards in benchmarks, but deep learning does not add further value.** | A | Lessmann et al. 2015 **[L18]**; Gunnarsson et al. 2021 **[L19]** | Gradient boosting with explainability is the evidence-backed ceiling. Logistic scorecards remain a credible, lower-governance alternative that is not AI. | `[R2][R3]` |
| T5 | **Automated lending is faster without higher defaults.** US fintech lenders processed mortgage applications about 20% faster, and faster processing did not come at the cost of higher defaults. | A | Fuster et al. 2019 **[L30]** | This supports the claim that in-moment decisions need not degrade risk quality (R-2). The evidence comes from mortgages, so the effect size will not transfer directly. | `[R2]` |
| T6 | **Embedded credit lifts merchant sales but draws in riskier and stacked borrowers.** Offering BNPL raised merchant sales by 20%, driven by customers with lower creditworthiness. 63% of US BNPL borrowers held multiple simultaneous loans in 2022, and most of these loans did not appear in credit records. | B / A | Berg, Burg, Keil & Puri **[L44]**; CFPB 2025 **[L38]** | Merchants will push for approvals. HLB needs affordability checks and in-house exposure aggregation, and should report to CIC so that stacking does not stay invisible. | `[R1][R2][R3]` |
| T7 | **Unchecked digital credit harms thin-file borrowers.** In Kenya, roughly 50% of digital borrowers reported repaying late and about 12% reported defaulting. By end-2022, 14 million accounts had defaulted on digital loan apps. | B | Izaguirre et al. 2018 **[L33]**; CGAP 2025 **[L34]** | Responsible-lending controls must be built into the decision flow: affordability, clear terms, sensible limits, and collections standards. HLB's differentiation from "black credit" depends on it **[Brief p.4]**. | `[R2][R3]` |
| T8 | **Digital ID with biometrics cuts both onboarding cost and fraud.** One Indian Ministry of Finance estimate put the fall in verification cost at about $15 to $0.50 per customer, and verification time fell from more than five days to seconds. In Vietnam, SBV reported fraud incidents down 50% and fraud-receiving accounts down 72% after biometric authentication became mandatory. | B / C | Pisa & Woodsome 2019 **[L46]**; SBV via VietNamNet 2024 **[L52]** | Chip-CCCD/VNeID biometric matching is already mandatory infrastructure. HLB can reuse it rather than collecting documents (R-3). | `[R2][R3]` |
| T9 | **Consent-based data sharing improves both approval and pricing.** Borrowers who share data get higher approval and lower rates, with lower defaults. Riskier applicants are more likely to share. | B / A | Nam 2022 **[L15]**; Babina et al. **[L16]**; Alok et al. **[L14]** | Offer a voluntary "share my HLB / e-wallet / platform data for a better offer" step, which Vietnam's PDPL consent rule requires anyway **[L56]**. | `[R3][R4]` |
| T10 | **Target on predicted uplift and suitability, not on predicted response or risk alone.** The customers with the highest predicted risk are often not the ones an intervention helps. Framing and advertising content can move credit take-up as much as large price cuts. | A | Ascarza 2018 **[L62]**; Bertrand et al. 2010 **[L64]** | R-4 targeting must optimise for "benefits and repays", with guardrails against nudging vulnerable users. | `[R3][R4]` |
| T11 | **ML can widen disparities, and thin files add noise that algorithms may either amplify or reduce.** ML increased rate disparity between groups in US mortgage data. Equalising the precision of credit scores could roughly halve disparities in approval. Algorithmic lenders discriminated 40% less than face-to-face lenders. | A / B | Fuster et al. 2022 **[L21]**; Blattner & Nelson **[L23]**; Bartlett et al. 2022 **[L22]** | Fairness monitoring by segment, gender and region is a requirement, not an extra. Removing officer discretion may itself reduce bias. | `[R3][R4]` |
| T12 | **Vietnam's rules now make consented, sandboxed alternative scoring legally possible, and make scoring without consent illegal.** | A | PDPL Law 91/2025/QH15 **[L56]**; Decree 94/2025 **[L55]**; Circular 64/2024 **[L54]** | Consent capture is a hard design requirement. Third-party scoring may need the SBV sandbox. Bank Open APIs reach full compliance on 1 Mar 2027. | `[R3][R4]` |

---

## 3. Findings by theme

### 3.1 Alternative-data credit scoring (R-1)

**What is known**
- **Digital footprints.** Berg, Burg, Gombović & Puri **[L1]** analysed 270,399 purchases at a German furniture e-commerce firm (Oct 2015–Dec 2016). The bureau score had AUC 68.3%. Footprint variables alone (device type, operating system, email host, channel, time of purchase, typing errors) reached 69.6%, and the combined model 73.6%. For unscorable customers, the footprint AUC was 72.2% against 69.6% for scorable customers. Unscorable customers defaulted more (2.49% vs 0.94%). Univariate gaps were large: orders from mobile phones defaulted at 2.14% against 0.74% from desktops, and Android at 1.79% against iOS at 1.07%. The authors warn about the Lucas critique: once lenders use footprint variables, applicants may change the ones that are cheap to change. They also caution against extrapolating to emerging markets. **Grade A.**
- **Mobile-phone (telco) data.** Björkegren & Grissen **[L2]** matched call records to repayment for credit extended by a South American telecom. On thin-file individuals their method **outperformed** bureau-based models, both within the same period and on a later period. It performed similarly on people with no financial history, who cannot be scored traditionally. Applicants in the riskiest quintile were **2.8 times** as likely to default as those in the safest. Óskarsdóttir et al. **[L3]** found that adding call-detail records and call-network features to traditional data **significantly increased AUC**. The **most profitable** model used calling-behaviour features only. **Grade A.**
- **BigTech / e-commerce transaction data.** Gambacorta, Huang, Qiu & Wang **[L4]**, using data from a Chinese fintech (May–Sep 2017), found that ML with non-traditional data predicted losses and defaults better than traditional models **under a negative credit-supply shock**, and that the advantage **declines for borrowers with longer credit histories**. Frost et al. **[L5]** found evidence from Argentina (Mercado Libre) that BigTech lenders have an information advantage over a traditional credit bureau. Gambacorta et al. **[L6]** (over 2 million Chinese firms borrowing from both Ant Group and banks) found BigTech credit tracks firms' transaction volumes and network scores rather than local house prices, whereas bank credit does react to house prices: "data versus collateral". Huang et al. **[L7]** (IMF; 1.8 million loans from a Chinese online bank) found the fintech approach predicted SME defaults better in normal times and under shocks, and that proprietary platform data can **substitute for credit history** so that unbanked firms can borrow. Hau et al. **[L8]** used a discontinuity in Ant's credit algorithm for Taobao vendors and showed that access to automated FinTech credit **raised sales and transaction growth and customer ratings**, with larger effects for vendors with more information asymmetry and less collateral. **Grades A/B.**
- **Platform / delivery transaction data for merchants without credit history.** Chioda, Gertler, Higgins & Medina **[L10]** (a Mexican fintech lending to merchants on a delivery platform) show that alternative data from platform transactions predicts creditworthiness for borrowers with **no formal credit history**. They also note that most fintechs still lend mainly to people with bureau histories. **Grade B.**
- **Cash-flow data.** FinRegLab **[L11]** analysed data from six lenders (Accion, Brigit, Kabbage, LendUp, Oportun, Petal) and found cash-flow variables and scores **predictive of credit risk** across firms, populations and products. The study also examined fair-lending effects. Ghosh, Vallee & Zeng **[L13]** (an Indian fintech lending to small businesses) found that greater use of verifiable **cashless payments** predicts higher approval, lower rates and **lower default conditional on the rate**, with stronger effects for more precise payment technologies and for outflows. **Grades A/B.**
- **Utility and telecom bill payments.** PERC **[L12]** found that in the US thin-file population, adding fully reported utility and telecom data moved 25% of consumers up a risk tier and 6% down. "A little over eighty percent" of thin-file consumers were unscoreable without the data, compared with "around ninety percent" scored with it. **Grade B.**
- **Alternative data combined with ML in production.** The CFPB's review of Upstart's No-Action Letter data **[L17]** reported that the tested model approved **27% more applicants** with **16% lower average APRs**. Applicants under 25 were 32% more likely to be approved. The Bureau states the results "were not separately replicated". Di Maggio et al. **[L9]** found that a traditional model would give a **70% higher probability of rejection**, that the biggest winners were "invisible primes" (low score, short history, low default), and that **about two-thirds** of the effect came from the extra data. **Grades A/B.**

**Contested or uncertain points**
- **Manipulability and drift.** Footprint signals are cheap to game once they are known **[L1]**. Telco signals depend on the ownership and pricing structure of mobile plans **[L2]**.
- **Fintechs still rely on bureaus.** Chioda et al. **[L10]** document that fintechs mostly lend to borrowers with bureau histories, which dampens the claims of inclusion.
- **Alternative data can lower some scores.** In PERC, 6% of thin-file consumers moved **down** a tier **[L12]**. Inclusion is net positive but not universal.
- **Most studies come from China, the US or Europe,** with smartphone-rich populations. Vietnam-specific peer-reviewed evidence on predictive lift is scarce (§3.7, OQ-LR-01).

**Relevance by segment**
| Segment | Most-evidenced data source | Evidence |
|---|---|---|
| SAL-C | Bureau (CIC) plus own-account history. The ML advantage is smallest here | **[L4]** |
| SAL-N | Salary inflows and cash flow in HLB CASA, or shared via consent | **[L11][L15]** |
| GIG | Platform income and transaction data, telco and e-wallet usage | **[L10][L2][L13]** |
| MSME | E-commerce / sales-ledger transactions, cashless inflows | **[L6][L7][L8][L13]** |
| FTB | Digital footprint, telco, utility / top-up behaviour | **[L1][L2][L3][L12]** |

### 3.2 ML underwriting performance & governance (R-1, R-2)

**What is known**
- **Benchmarks.** Lessmann et al. **[L18]** benchmarked 41 classifiers on eight credit datasets and recommended ensembles over single classifiers. Gunnarsson et al. **[L19]** conclude that "XGBoost is the best performing method for credit scoring of all the methods considered". Deep neural networks did not outperform shallower ones and cost far more to compute. **Grade A.**
- **Reject inference (training only on approved applicants).** Banasik and Crook found only "distinct but modest scope" for reject inference. Their 2004 augmentation experiments **[L20]** "suggested that augmentation actually undermined predictive performance", and their 2007 follow-up confirmed it. **Implication:** a model trained on HLB's historical approvals will be biased against the declined thin-file population. The fix in the literature is controlled exploration (approving randomly selected marginal applicants, as in Karlan & Zinman **[L36]**), not statistical imputation. **Grade A.**
- **Explainability.** Bussmann et al. **[L24]** grouped SHAP explanations of 15,000 SMEs on a P2P platform to explain credit scores. FinRegLab **[L25]** tested diagnostic tools from seven vendors plus open-source ones. It found that "some explainability techniques and tools reliably identified features" important for consumer disclosures and fair-lending analysis, even for complex models. **Grades A/B.**
- **Fairness.** Fuster et al. **[L21]** found that in US mortgage data, ML **increases rate disparity** between and within groups, mainly because it is more flexible, and that Black and Hispanic borrowers benefit less. Blattner & Nelson **[L23]** show that credit scores are **noisier for under-served groups** because their files are thin, and that equalising score precision could cut approval disparities "by approximately half". Bartlett et al. **[L22]** found that fintech algorithms still discriminate on pricing, but **40% less** than face-to-face lenders. **Grades A/B.**
- **Regulatory expectations.**
  - Adverse-action reasons. CFPB Circular 2022-03 **[L26]** stated that creditors using complex algorithms must still give specific principal reasons for denials. The CFPB **withdrew** the circular on 12 May 2025 as part of a withdrawal of 39 guidance documents; the specific-reasons duty itself sits in Regulation B.
  - Model risk management. **SR 11-7 was superseded by SR 26-2 on 17 April 2026 [L27].** The new guidance takes a more risk-based approach, tailored to each organisation's model risk profile.
  - International bodies. The FSB **[L28]** saw benefits for thin-file access "in countries with shallow credit markets" but flagged opacity and proxy-discrimination risks. BIS FSI Insights No. 35 **[L29]** found that regulators' AI principles converge on reliability, accountability, transparency, fairness and ethics, with more weight on human responsibility than for traditional models.
  - **Grade A/B.**

**Contested or uncertain points**
- The accuracy gain from ML over logistic regression is real in benchmarks, but its **economic** size varies by dataset **[L18][L19]**. Whether the gain is worth the extra governance cost at HLB's volumes has not been established (OQ-LR-04).
- The fairness evidence is almost all from the US, organised around race and ethnicity. In Vietnam, the comparable dimensions are likely gender, region (urban/rural), age and informality (OQ-LR-06).
- **Monotonic constraints:** no verified peer-reviewed effect sizes were found in this review. They are standard practice for keeping models explainable, but that is not evidenced here (OQ-LR-04).

### 3.3 Automated / tiered decisioning & process economics (R-2)

**What is known**
- **Speed without extra risk.** US fintech lenders grew from 2% to 8% of mortgage lending (2010–2016) and processed applications about **20% faster**, with **no higher defaults** and more elastic supply when demand spiked **[L30]**. **Grade A.**
- **Time-to-yes and commercial outcomes (grade C).** McKinsey reports one European bank that cut SME time to yes from "20 days to less than ten minutes", raising win rates by a third and margins by over 50% **[L37]**. It also reports "20 to 40 percent" efficiency gains from next-generation decisioning models (secondary). This is consultancy evidence and should be treated as illustrative only.
- **Digital credit in emerging markets.** For M-Shwari in Kenya, Suri, Bharadwaj & Jack **[L31]** used the eligibility-score cutoff as a regression discontinuity: 34% of eligible customers took a loan, and borrowers were **6.3 percentage points less likely to forgo expenses** after negative shocks. Brailovskaya, Dupas & Robinson **[L32]** found **no evidence** that small digital loans harmed perceived well-being in Malawi. However, most borrowers did not know the loan terms, repaid late and paid large late fees. A phone-based financial-literacy intervention **modestly increased** loan demand and the likelihood of ever defaulting. **Grade A.**
- **Payments rails and open banking as credit infrastructure (India).** Alok, Ghosh, Kulkarni & Puri **[L14]** show that India's UPI increased formal credit to new borrowers. "Only fintechs extended credit to new-to-credit customers", and there was "no evidence that this expansion increased default rates". On Account Aggregators (AA), CGAP **[L65]** reports 120 million linked accounts by Dec 2024. 38% of loan applicants were offered AA sharing and 21% used it, and only 30% had heard of AAs. **Grade B.**
- **Human-in-the-loop "refer" bands.** No peer-reviewed study quantifying the optimal refer-band size was found. The closest causal evidence is Karlan & Zinman **[L36]**, discussed next.
- **Expanding approvals at the margin.** In a South African field experiment, loan officers were encouraged to approve **randomly selected marginal rejected** applicants. The marginal loans brought significant benefits to borrowers and were profitable for the lender, though less so than loans to more creditworthy applicants **[L36]**. This is the experimental template for testing a refer band or a thin-file approval corridor. **Grade A.**

**Contested or uncertain points**
- Cost-to-serve figures for automated decisions are almost all consultancy or vendor claims (grade C). No peer-reviewed per-decision cost benchmark for emerging-market point-of-sale lending was found (OQ-LR-03).
- Speed alone does not ensure good outcomes. The Malawi and Kenya evidence **[L32][L33]** shows that fast digital credit with opaque terms leads to late fees and defaults.

### 3.4 BNPL / point-of-sale credit outcomes (R-2 responsible lending, R-4)

**What is known**
- **Market behaviour (CFPB, US).**
  - 2022 report **[L39]**: 73% of applicants were approved in 2021 (up from 69% in 2020). Loans from the five largest lenders grew from 16.8 million (2019) to 180 million (2021).
  - January 2025 report **[L38]** (matched credit records; Affirm, Afterpay, Klarna, PayPal, Sezzle, Zip):
    - 21% of consumers with a credit record used BNPL in 2022.
    - About 63% originated multiple simultaneous loans, and 33% did so across multiple firms. "The majority of BNPL loans ... did not appear in credit records."
    - Approval rates were 67% in 2020, 78% in 2021 and 79% in 2022, partly because of **counteroffers** (a higher down-payment or a lower amount).
    - Deep-subprime borrowers made up 45% of originations and subprime borrowers 16%.
    - BNPL loans defaulted at **2%** against **10%** on the same borrowers' credit cards, which the CFPB attributes partly to automatic repayment.
    - BNPL users carried more other unsecured debt: $453 more in personal loans and $871 more on credit cards.
  - **Grade A.**
- **Spending effects.** Di Maggio, Katz & Williams **[L41]** find that BNPL access increases total spending and the retail share of spending by more than standard models can explain. They call this a "liquidity flypaper effect". deHaan, Kim, Lourie & Zhu **[L42]** (10.6 million US consumers' bank data) find that new BNPL users experience **rapid increases in overdraft charges and credit-card interest and fees** compared with non-users, which is consistent with overborrowing. Guttman-Kenney, Firth & Gathergood **[L43]**: 19.5% of active UK credit cards had a BNPL transaction in 2021, most commonly among younger and more deprived consumers. That is, interest-free BNPL was refinanced onto roughly 20%-APR cards. **Grades A/B.**
- **E-wallet-embedded BNPL (China).** Bian, Cong & Ji **[L45]** find that BNPL dominates e-wallet payments, promotes inclusion and "allows greater spending without indebtedness or delinquencies, thanks to ecosystem incentives in super-apps". This is the closest analogue to MoMo/ZaloPay-style pay-later. **Grade B.**
- **Merchant economics.** Berg, Burg, Keil & Puri **[L44]**: offering BNPL **raises sales by 20%**, driven by low-creditworthiness customers and products where the merchant has more market power. For the merchant, benefits "significantly outweigh the costs". **Grade B.**
- **Regulatory response (UK).** The Woolard Review **[L40]** found that BNPL use nearly quadrupled in 2020 to £2.7bn. It listed harms including poor consumer understanding, weak affordability assessment and **invisibility of BNPL debt to other lenders**, and recommended urgent regulation. The FCA's deferred-payment-credit regime **came into force on 15 July 2026** and requires proportionate creditworthiness and affordability assessments **[L40]**. **Grade A.**

**Contested or uncertain points**
- Whether BNPL harms or helps is unresolved. Default rates are low **[L38]** and ecosystem incentives can discipline borrowers **[L45]**, but spending, overdraft and stacking effects point the other way **[L41][L42]**. HLB's instalment tenors (3–24 months) and tickets (1.6M–90.1M VND) **[Brief p.9]** make its product closer to point-of-sale instalment loans than to US "pay-in-4". Loss dynamics and the case for affordability checks are therefore **stronger** for HLB than for pay-in-4 products (OQ-LR-05).

### 3.5 eKYC, identity & application fraud (R-3)

**What is known**
- **Cost and time of digital KYC.** According to Pisa & Woodsome (CGD) **[L46]**, a Ministry of Finance official estimated that moving from paper KYC to Aadhaar e-KYC cut average verification cost "from roughly $15 to $0.50". Banks could cut verification time "from more than five days to seconds". Gelb & Metz **[L47]** document the spread of digital ID for development and conclude that it can do "tremendous good" but also "inflict serious harm". **Grade B.**
- **Standards.** FATF's 2020 Digital Identity Guidance **[L48]** states that non-face-to-face onboarding relying on reliable, independent digital ID with appropriate risk mitigation "may present a standard level of risk, and may even be lower-risk". It supports **tiered customer due diligence** by assurance level. **Grade A.** Liveness / presentation-attack detection is tested under ISO/IEC 30107-3. Independent labs (e.g. iBeta) test at Level 1 (basic spoofs) and Level 2 (more sophisticated attacks) **[L51]**. **Grade C.**
- **Fraud patterns.** The Federal Reserve's 2019 white paper **[L49]** describes synthetic identity fraud (identities assembled from real and fabricated elements) as serious and growing, and "by some accounts" the fastest-growing financial crime in the US. **Grade B.** Berg et al. **[L1]** note that footprint variables such as email errors also flag impersonation risk.
- **Vietnam's biometric mandate.** SBV Decision 2345/QĐ-NHNN (effective 1 Jul 2024) requires facial biometric authentication matched to the chip-CCCD/VNeID for transfers above VND 10 million per transaction or VND 20 million per day **[L52]**. Circular 17/2024/TT-NHNN (effective 1 Jan 2025) requires biometric matching against chip ID or national population data before accounts can transact electronically **[L53]**. On 26 Sep 2024, SBV's Payment Department director reported fraud incidents down 50% (to 700) and fraud-receiving accounts down 72% (to 682). About 38 million accounts had biometrics collected by mid-September 2024 **[L52]**. **Grade A (rules) / C (outcome figures reported in news).**
- **Friction and abandonment.** Signicat's European survey **[L50]** found that 68% of consumers had abandoned a financial onboarding in the prior year. The main reasons were time to apply, the amount of personal information required, and changing their mind; 38% lacked the right identity credentials. **Grade C.** It is European and self-reported, and no Vietnam-specific abandonment evidence was found (OQ-LR-07).

**Contested or uncertain points**
- No peer-reviewed evidence was found on **first-party (application) fraud** rates in emerging-market point-of-sale credit, or on the incremental value of behavioural biometrics over chip-ID matching (OQ-LR-08).
- The SBV fraud figures are before-and-after comparisons reported by the regulator in the press, not causal estimates.

### 3.6 Propensity / targeting & responsible offer design (R-4)

**What is known**
- **Uplift over risk or response.** Ascarza **[L62]** combined field experiments with ML and showed that the highest-risk customers "are not necessarily the best targets". Firms should target on **sensitivity to the intervention**. Devriendt, Moldovan & Verbeke **[L63]** survey uplift-modelling methods and evaluate them on four real datasets. **Grade A.**
- **Framing moves credit take-up.** In a South African direct-mail experiment, Bertrand et al. **[L64]** found that advertising content affected loan demand by about as much as large price changes. The most-cited example is that a photo of a woman increased demand by about as much as a 25% interest-rate cut (secondary summary; the direction and size are reported in the abstract). **Grade A.** Implication: offer design at checkout is powerful and must be governed.
- **Consent-based data sharing.** Nam **[L15]** (auxmoney, Germany): riskier applicants share data more readily. Sharing raises approval, lowers rates and is associated with lower ex-post defaults. Babina et al. **[L16]**: 49 countries have adopted open banking. In the UK it helps consumers get credit and SMEs form new lending relationships. However, **privacy-conscious or costlier consumers may face higher prices**. **Grades A/B.**
- **Consumer protections for digital lending (India).** The RBI Working Group **[L35]** found about 600 of about 1,100 lending apps operating illegally (secondary reporting of the RBI report). The 2022 RBI Digital Lending Guidelines **[L35]** require:
  - a **Key Fact Statement** with the all-in APR;
  - a **cooling-off period** (at least 3 days for tenors of 7 days or more);
  - **explicit consent** for any credit-limit increase;
  - direct disbursal between the borrower's and the lender's accounts.
  - **Grade A.**
- **Harms of indiscriminate digital credit.** CGAP 2025 **[L34]**: 2.7 million Kenyans defaulted between 2014 and 2016, including 400,000 with digital loans of KES 200 (about USD 2) or less. In 2024 phone surveys, 86% of Kenyan digital borrowers faced repayment challenges, and about 60% cut basic expenses to repay. **Grade B.**

**Contested or uncertain points**
- No peer-reviewed study was found that applies uplift modelling specifically to **pre-approved point-of-sale credit offers** with repayment as the outcome (OQ-LR-09). Transferring churn and marketing uplift results to credit is an inference.

### 3.7 Vietnam-specific evidence

**What is known**
- **Bureau infrastructure.** Around 2020, CIC held credit data on 670,000 companies and 30.8 million individuals with credit history, and worked with over 1,200 credit institutions. A smaller private bureau (PCB) was founded by 11 banks in 2007 **[L57a]**. Lainez notes that CIC's individual reports took "several days" and that its linear data does not reflect "unstable and multidirectional career paths". **Grade B.** A current (2025–26) coverage figure was not verified here (OQ-LR-02; see also OQ-08 in 01-product-strategy).
- **Account penetration.** World Bank Global Findex account ownership (age 15+) for Vietnam: **56.3%** (2022 data point) → **70.6%** (2024 data point) **[L60]**. **Grade A.**
- **The algorithmic-scoring market.** Lainez (ISEAS 2021) **[L57a]** documents lenders in Vietnam using algorithmic credit scoring "at scale but in legal limbo". Trusting Social uses call/SMS metadata, top-up and value-added-service data, and KT's K-Telco Score uses subscription and billing data. His 2023 article in the *Asian Journal of Law and Society* **[L57b]** proposes a legal framework to regulate it. Vendor claims he reports (CredoLab: Gini 0.40–0.50 against 0.25–0.30 for traditional scorecards; +20% approvals, −15% defaults, −22% fraud) are **vendor claims, grade C**, and are not independently verified.
- **Local ML studies.** Tran et al. (IEA/AIE 2021) **[L59]** describe their work as an early ML credit-scoring study on Vietnamese bank data. Its abstract (as indexed) reports that ensembles outperform SVM and logistic regression; the figures could not be independently verified. Vũ Trọng Sinh (*Tạp chí Ngân hàng*, 28 Mar 2026) **[L58]** proposes **vertical federated learning** so that banks can score thin-file customers together with telco and e-commerce data without moving raw data under the PDPL. It cites 10–15% metric improvements from the wider literature (grade B/C; not an original empirical result).
- **Regulation now in force.**
  - **PDPL (Law 91/2025/QH15, effective 1 Jan 2026) [L56]**: banks may not use customers' financial or credit information to score or rate creditworthiness **without consent**. Consent must be specific and informed; silence is not consent. The law's section for finance, banking and credit information also requires the consent to name the purpose (including credit scoring), the retention period and the data sources.
  - **Decree 94/2025/ND-CP (sandbox, effective 1 Jul 2025) [L55]**: covers **credit scoring, Open API and P2P lending** solutions for up to two years under SBV supervision.
  - **Circular 64/2024/TT-NHNN [L54]**: consent-based bank Open APIs, effective 1 Mar 2025, with full compliance by **1 Mar 2027**.
  - **Decision 2345 and Circular 17/2024 [L52][L53]**: biometric and chip-ID requirements (§3.5).
- **Gig workers.** A World Bank / Vietnam Social Security survey of nearly 4,900 platform workers (reported 22 Sep 2026) found that 94% said platform work was their main job, average net monthly earnings were VND 7.4–9.7 million, and 78% had not paid social insurance in the previous six months **[L61]** (grade C: news report of the survey). This is consistent with Exhibit 1: GIG income is real and recurring, but workers lack salary slips and social-insurance records that could serve as income proof.

**Gaps.** No peer-reviewed Vietnamese study was found that measures the **out-of-sample predictive lift** of telco, e-wallet or platform data over CIC for consumer instalment loans, or the **default outcomes** of Vietnamese BNPL (OQ-LR-01, OQ-LR-05).

---

## 4. Evidence map

| # | Study | Year | Market | Data type | Method | Key finding (quantified) | Grade | Relevance |
|---|---|---|---|---|---|---|---|---|
| L1 | Berg, Burg, Gombović & Puri | 2020 | Germany | Digital footprint (device, OS, email, time) | Logit, AUC, out-of-sample CV | AUC: footprint 69.6% vs bureau 68.3%; combined 73.6%. Unscorable-customer AUC 72.2%. Mobile vs desktop default 2.14% vs 0.74% | A | R-1, R-3 |
| L2 | Björkegren & Grissen | 2020 | South America | Telco call records | ML on behavioural features | Top risk quintile 2.8× more likely to default than bottom. Beats bureau models on thin files | A | R-1 |
| L3 | Óskarsdóttir et al. | 2019 | Undisclosed country (credit-card applicants) | Call-detail records, call networks | Social network analytics, profit-based selection | Adding call records significantly raises AUC. Calling-behaviour-only model is most profitable | A | R-1 |
| L4 | Gambacorta, Huang, Qiu & Wang | 2024 | China | Fintech transaction data | ML vs traditional models, shock test | ML with non-traditional data predicts better under a credit-supply shock. Advantage declines with longer credit history | A | R-1 |
| L5 | Frost et al. (BIS WP 779) | 2019 | Argentina, China | Marketplace (Mercado Libre) data | Comparison with bureau | BigTech lenders have an information advantage over the bureau | B | R-1 (MSME) |
| L6 | Gambacorta et al. "Data versus Collateral" | 2023 | China (>2m firms) | Ant Group transaction and network scores | Panel regressions | BigTech credit tracks transactions, not house prices; bank credit tracks house prices | A | R-1 (MSME) |
| L7 | Huang et al. (IMF) | 2020 | China (1.8m loans) | Online-bank big data | ML vs scorecard | Fintech approach predicts SME default better in normal and shock periods | B | R-1 (MSME) |
| L8 | Hau et al. | 2024 | China (Taobao vendors) | Automated credit lines | Regression discontinuity | Credit access raises vendor sales, transactions and ratings, more so for opaque vendors | A | R-1, R-4 (MSME) |
| L9 | Di Maggio, Ratnadiwakara & Carmichael | 2022 | US | Fintech platform alternative data | Counterfactual model comparison | Traditional model → 70% higher probability of rejection. About two-thirds of the effect is from data | A | R-1, R-2 |
| L10 | Chioda, Gertler, Higgins & Medina | 2024 | Mexico | Delivery-platform transactions | ML (default vs profit targets) | Platform data predicts creditworthiness with no bureau history. Profit-target ML performs comparably | B | R-1 (MSME, GIG) |
| L11 | FinRegLab | 2019 | US (6 lenders) | Bank-account cash flow | Independent validation | Cash-flow variables predictive across lenders and products | B | R-1 (SAL-N, GIG) |
| L12 | PERC (Turner et al.) | 2012 | US | Utility and telecom payments | Score-tier migration | Thin-file: 25% up a tier vs 6% down; scored share rises from under 20% to about 90% | B | R-1 (FTB) |
| L13 | Ghosh, Vallee & Zeng | 2026 | India | Cashless payment flows | Loan-level regressions | More cashless payments → higher approval, lower rate, lower default | A | R-1 (MSME) |
| L14 | Alok, Ghosh, Kulkarni & Puri | 2024 | India | UPI rollout, universe of loans | Difference-in-differences | Credit to new borrowers rose. Only fintechs lent to new-to-credit customers. No default increase | B | R-1, R-4 |
| L15 | Nam | 2022 | Germany | Consent-based account data | Loan-application analysis | Sharing → higher approval, lower rate, lower defaults. Riskier applicants share more | B | R-1, R-4 |
| L16 | Babina et al. | 2024 | 49 countries / UK | Open-banking policy | Cross-country and micro data, model | Open banking spurs entry and credit access. Privacy-conscious consumers may pay more | A | R-4 |
| L17 | CFPB (Upstart NAL) | 2019 | US | Alternative data + ML | Lender-run simulation | +27% approvals, −16% APR. Not replicated by the CFPB | B | R-1 |
| L18 | Lessmann et al. | 2015 | 8 datasets | Standard credit data | 41-classifier benchmark | Ensembles recommended over single classifiers | A | R-1, R-2 |
| L19 | Gunnarsson et al. | 2021 | Multiple datasets | Standard credit data | DL vs ensembles, Bayesian tests | XGBoost best. Deep nets no better and costlier | A | R-2 |
| L20 | Crook & Banasik / Banasik & Crook | 2004/2007 | UK | Sample with near-all accepts | Reject-inference tests | Only modest scope for reject inference; augmentation undermined performance | A | R-1 |
| L21 | Fuster et al. | 2022 | US mortgages | Loan data | Logit vs ML plus equilibrium model | ML raises rate disparity; Black and Hispanic borrowers gain less | A | R-1, R-4 |
| L22 | Bartlett et al. | 2022 | US mortgages | Mortgage pricing data | Pricing regressions | Minorities pay 7.9 bp more on purchase loans. Algorithms discriminate 40% less | A | R-2 |
| L23 | Blattner & Nelson | 2021 | US | Credit files | Structural model | Scores noisier for under-served groups. Equalising precision cuts disparities by about half | B | R-1 |
| L24 | Bussmann et al. | 2021 | Europe (15,000 SMEs) | P2P platform data | SHAP plus correlation networks | Explanations cluster borrowers into interpretable groups | A | R-2 |
| L25 | FinRegLab | 2023 | US | Model diagnostics | Tool evaluation | Some explainability tools reliably identify key features for disclosures and fair-lending analysis | B | R-2 |
| L26 | CFPB Circular 2022-03 (withdrawn 2025) | 2022/2025 | US | — | Regulatory interpretation | Specific adverse-action reasons required even for complex models. Circular withdrawn 12 May 2025 | A | R-2 |
| L27 | Federal Reserve SR 11-7 → SR 26-2 | 2011/2026 | US | — | Supervisory guidance | SR 26-2 (17 Apr 2026) supersedes SR 11-7 with risk-based model risk management | A | R-2 |
| L28 | FSB | 2017 | Global | — | Policy report | Access gains in shallow credit markets; opacity and bias risks | B | R-1, R-2 |
| L29 | BIS FSI Insights 35 | 2021 | Global | — | Regulatory survey | Converging AI principles; emphasis on human responsibility | B | R-2 |
| L30 | Fuster, Plosser, Schnabl & Vickery | 2019 | US mortgages | Application/origination data | Regressions with controls | Fintechs about 20% faster, with no higher defaults | A | R-2 |
| L31 | Suri, Bharadwaj & Jack | 2021 | Kenya | M-Shwari digital loans | Regression discontinuity | 34% take-up; 6.3 pp less likely to forgo expenses after shocks | A | R-2, R-4 |
| L32 | Brailovskaya, Dupas & Robinson | 2024 | Malawi | Digital loans | Regression discontinuity plus RCT | No well-being harm. Low term awareness and late fees; literacy nudge raised default | A | R-2, R-4 |
| L33 | Izaguirre, Kaffenberger & Mazer (CGAP) | 2018 | Kenya, Tanzania | Phone surveys; 20m loans | Descriptive | Kenya: ~50% repaid late, ~12% defaulted | B | R-2 |
| L34 | CGAP Responsible Digital Credit | 2025 | Multi-country | Survey syntheses | Technical guide | 14m defaulted digital-loan accounts in Kenya by 2022; 86% faced repayment challenges | B | R-2, R-4 |
| L35 | RBI Working Group; RBI Digital Lending Guidelines | 2021/2022 | India | App-store scan; rules | Regulatory | About 600 of 1,100 apps illegal (secondary). Key Fact Statement, cooling-off, consent rules | A | R-2, R-4 |
| L36 | Karlan & Zinman | 2010 | South Africa | Randomised marginal approvals | Field experiment | Marginal loans benefit borrowers and are profitable (less than core loans) | A | R-1, R-2 |
| L37 | McKinsey (two articles) | 2018/2021 | Europe / global | Case examples | Consultancy | SME time to yes cut from 20 days to under 10 minutes; win rates up by a third | C | R-2 |
| L38 | CFPB BNPL report | 2025 | US | Matched BNPL and credit records | Descriptive | 63% stack loans; default 2% vs 10% on cards; 79% approval (2022) with counteroffers | A | R-2, R-3 |
| L39 | CFPB BNPL market trends | 2022 | US | Lender data | Descriptive | 73% approval (2021); 180m loans (2021) | A | R-2 |
| L40 | FCA Woolard Review; FCA PS26/1 | 2021/2026 | UK | — | Regulatory | BNPL nearly quadrupled in 2020 (£2.7bn). Affordability rules in force 15 Jul 2026 | A | R-2 |
| L41 | Di Maggio, Katz & Williams | 2022 | US | Transaction panel | Rollout-based estimation | BNPL raises total and retail spending ("liquidity flypaper") | B | R-2, R-4 |
| L42 | deHaan, Kim, Lourie & Zhu | 2024 | US (10.6m consumers) | Bank account data | Instrumental variables | New users see rising overdraft charges and card interest/fees | A | R-2 |
| L43 | Guttman-Kenney, Firth & Gathergood | 2023 | UK | Credit-card transactions | Descriptive | 19.5% of active cards had a BNPL transaction in 2021 | A | R-2 |
| L44 | Berg, Burg, Keil & Puri | 2024 | Online retail | Merchant sales data | Empirical (NBER WP) | BNPL raises sales by 20%, driven by low-creditworthiness customers | B | R-4 |
| L45 | Bian, Cong & Ji | 2023/2024 | China | E-wallet transactions | Panel analysis | BNPL dominates e-wallet payments; more spending without more delinquency | B | R-4 |
| L46 | Pisa & Woodsome (CGD) | 2019 | India | Aadhaar e-KYC | Policy analysis | KYC cost about $15 → $0.50; time from more than 5 days to seconds | B | R-2, R-3 |
| L47 | Gelb & Metz | 2018 | Global | Digital ID | Book | Digital ID can do great good or serious harm | B | R-3 |
| L48 | FATF | 2020 | Global | Digital ID | Guidance | Reliable digital ID for remote onboarding can be standard or lower risk; tiered due diligence | A | R-3 |
| L49 | Federal Reserve | 2019 | US | Synthetic ID | White paper | Synthetic ID fraud serious and growing | B | R-3 |
| L50 | Signicat | 2022 | Europe (7,600 respondents) | Survey | Descriptive | 68% abandoned an onboarding; 38% lacked the right ID credentials | C | R-3 |
| L51 | iBeta / ISO 30107-3 | — | Global | Liveness | Test standard | Level 1 and Level 2 presentation-attack tests | C | R-3 |
| L52 | SBV Decision 2345 + SBV results | 2023/2024 | Vietnam | Biometric authentication | Rule; before/after figures | Fraud incidents −50%; fraud-receiving accounts −72%; 38m accounts enrolled | A / C | R-3 |
| L53 | SBV Circular 17/2024 | 2024 | Vietnam | Account opening | Regulation | Biometric match to chip ID required before electronic transactions (from 1 Jan 2025) | A | R-3 |
| L54 | SBV Circular 64/2024 | 2024 | Vietnam | Open API | Regulation | Consent-based Open API; full compliance by 1 Mar 2027 | A | R-1, R-4 |
| L55 | Decree 94/2025/ND-CP | 2025 | Vietnam | Sandbox | Regulation | Credit scoring, Open API and P2P can be piloted for up to 2 years | A | R-1 |
| L56 | PDPL Law 91/2025/QH15 | 2025 | Vietnam | Personal data | Law | No credit scoring without consent; purpose-specific consent | A | R-1, R-4 |
| L57 | Lainez (ISEAS WP; AJLS) | 2021/2023 | Vietnam | Telco and smartphone scoring | Qualitative legal / ethnographic | Algorithmic scoring used at scale in legal limbo; CIC 30.8m individuals (c.2020) | B / A | R-1 |
| L58 | Vũ Trọng Sinh (*Tạp chí Ngân hàng*) | 2026 | Vietnam | Federated learning proposal | Policy article | Vertical federated learning for thin-file scoring under the PDPL | B | R-1 |
| L59 | Tran et al. (IEA/AIE) | 2021 | Vietnam | Bank credit data | ML comparison | Ensembles outperform SVM and logistic regression (per indexed abstract; unverified figures) | B | R-1 |
| L60 | World Bank WDI / Findex | 2022/2024 | Vietnam | Survey | Descriptive | Account ownership 56.3% → 70.6% | A | R-1, R-4 |
| L61 | World Bank / VSS survey via Tuoi Tre | 2026 | Vietnam | Platform workers | Survey (news report) | 94% main job; VND 7.4–9.7m monthly net; 78% no social insurance | C | R-1 (GIG) |
| L62 | Ascarza | 2018 | Telecom / retention | Field experiments | Uplift vs risk targeting | Highest-risk customers are not the best targets | A | R-4 |
| L63 | Devriendt, Moldovan & Verbeke | 2018 | 4 datasets | Marketing | Uplift survey and benchmark | Taxonomy and evaluation of uplift methods | A | R-4 |
| L64 | Bertrand et al. | 2010 | South Africa | Direct-mail credit offers | Field experiment | Ad content moves demand as much as large price cuts | A | R-4 |
| L65 | CGAP (Salman, Fernandez Vidal & Paikine) | 2025 | India | Account Aggregator users | Survey | 38% of applicants offered AA, 21% used it; 30% aware | B | R-1, R-4 |

---

## 5. Transferability to Vietnam

### 5.1 What carries over
- **Mechanism-level findings.**
  - Non-bureau data adds predictive power for thin files **[L1][L2][L9][L10]**.
  - The advantage shrinks as bureau history grows **[L4]**.
  - Ensembles beat single models, but deep learning is unnecessary **[L18][L19]**.
  - Reject inference only partly fixes selection bias **[L20]**.
  - Automation speeds decisions without raising defaults **[L30]**.
  - BNPL raises merchant sales, mostly through riskier customers **[L44]**.
  - These are structural relationships, not market-specific parameters.
- **Digital-ID economics.** India's paper-to-e-KYC cost collapse **[L46]** has a direct Vietnamese counterpart: chip-CCCD/VNeID biometric matching is **already mandatory** for bank accounts **[L52][L53]**. HLB does not need to build identity infrastructure. It needs to reuse it inside the checkout journey.
- **Consumer-harm patterns.** Opaque terms, stacking, late fees and aggressive collections **[L32][L33][L34][L38]** match what Vietnamese sources say about "black credit" apps **[L57a]** and the SBV's push against them (see 01-product-strategy, S5).

### 5.2 What does not carry over (or only partly)
- **Data availability.** US and European studies assume dense bureau files and bank-account data aggregators. In Vietnam, CIC covered about 30.8 million individuals around 2020 **[L57a]**. Open-API data sharing between banks and third parties becomes fully mandatory only on **1 Mar 2027 [L54]**, so until then consented data flows from other banks or e-wallets depend on bilateral partnerships.
- **Consent regime.** Vietnam's PDPL is **stricter than US practice**: no credit scoring without consent **[L56]**, with purpose-specific consent that names sources and retention. Passive footprint scoring of the Berg et al. **[L1]** kind needs explicit consent in Vietnam. Scoring "every shopper" in a partner ecosystem for R-4 targeting cannot rely on silence or pre-ticked boxes **[L56]**.
- **Fairness dimensions.** The US literature is about race **[L21][L22]**. In Vietnam, the relevant protected or vulnerable dimensions are more likely gender, age, region (urban/rural) and informality. Existing results give **no evidence** on these for Vietnam (OQ-LR-06).
- **Product structure.** US "pay-in-4" BNPL default evidence (2% **[L38]**) does not transfer to HLB's 3–24-month, up-to-90.1M VND instalment loans **[Brief p.9]**, which carry longer exposure.
- **Adverse-action / explanation rules.** The US specific-reasons obligation **[L26]** has no verified Vietnamese equivalent in this review. The PDPL's rules on credit-information assessment outputs **[L56]** need legal reading (OQ-LR-10).

### 5.3 What must be validated locally before relying on it
1. **Predictive lift per data source and per segment** on HLB pilot or partner data: CIC only, compared with CIC plus CASA inflows (SAL-N, MSME), plus platform income (GIG), plus telco/e-wallet (FTB). Measure out-of-sample AUC/Gini and approval at a fixed bad rate. **[L1][L4][L9]** show this must be tested, not assumed.
2. **Size of the uncertain middle band** (the "refer" band) under a tiered policy. Use a small randomised approval corridor in the style of Karlan & Zinman **[L36]** to learn about the declined population **[L20]**.
3. **Manipulability and drift** of footprint and telco features after launch **[L1]**.
4. **Consent take-up rates** at checkout. India shows only 21% of offered applicants used AA sharing **[L65]**, so HLB's data-driven lift will apply only to the share of applicants who consent.
5. **Whether HLB's point-of-purchase loans, and competitors' BNPL loans, appear in CIC in time** to detect stacking **[L38][L40]**.
6. **Sandbox applicability:** whether third-party (telco or e-wallet) scoring used by HLB falls under Decree 94's credit-scoring sandbox **[L55]**.

---

## 6. Implications for solution design (neutral, not a recommendation)

The list below separates mechanisms the literature **supports** from those it **cautions against**, across AI and non-AI options. It does not pick a solution. That is Skill 12's job.

### 6.1 Mechanisms supported by the evidence
| # | Mechanism | AI / non-AI | Evidence | Serves |
|---|---|---|---|---|
| M1 | **Segment-differentiated decision paths.** Bureau-led straight-through processing for SAL-C; alternative-data scoring for thin files | Either | **[L4][L2][L9]** | R-1, R-2 |
| M2 | **Consented cash-flow / inflow scoring** from HLB CASA, e-wallet or platform data (salary regularity, sales inflows) | Rules or ML | **[L11][L13][L15][L10]** | R-1 |
| M3 | **Checkout-native digital-footprint signals** (device, channel, time, email validity) as a complementary score and fraud flag, with consent | Rules or ML | **[L1]** | R-1, R-3 |
| M4 | **Gradient-boosted model with explainability** (SHAP-based reason codes), benchmarked against a logistic scorecard as challenger | AI | **[L18][L19][L24][L25]** | R-1, R-2 |
| M5 | **Logistic scorecard or rules on the new data.** A non-AI route, since most of the lift comes from data **[L9]** | Non-AI | **[L9][L18]** | R-1, R-2 |
| M6 | **Tiered approve / refer / decline**, with humans only in the refer band, plus a small randomised approval corridor to learn about rejected applicants | Either | **[L30][L36][L20]** | R-2 |
| M7 | **Counteroffers** (lower amount, shorter tenor, higher down-payment) instead of outright decline | Rules | **[L38]** | R-2, R-4 |
| M8 | **Reuse of chip-CCCD/VNeID biometric matching** with liveness detection tested to ISO 30107-3, for document-free remote onboarding under tiered due diligence | Non-AI infrastructure + AI liveness | **[L48][L52][L53][L51][L46]** | R-3 |
| M9 | **Affordability check plus in-house exposure aggregation**, and CIC reporting of point-of-purchase loans | Rules | **[L38][L40][L34]** | R-2 |
| M10 | **Key-Fact-Statement-style disclosure, cooling-off period, explicit consent for limit increases** | Non-AI | **[L35][L32]** | R-2, R-4 |
| M11 | **Consent-gated "share data for a better offer" step** | Non-AI | **[L15][L16][L56][L65]** | R-1, R-4 |
| M12 | **Uplift-based targeting within partner ecosystems** (target people whose outcome improves), with suitability filters | AI | **[L62][L63]** | R-4 |
| M13 | **Fairness and drift monitoring** by gender, age and region; model governance aligned with risk-based model risk management | Governance | **[L21][L23][L27][L29]** | R-2 |
| M14 | **CASA-first onboarding to build observable history** (Exhibit 1 mitigation for MSME) | Non-AI | **[L13][L8]** | R-1 |

### 6.2 Mechanisms the evidence cautions against
| # | Pattern | Why | Evidence |
|---|---|---|---|
| X1 | Deep learning as the default model class | No accuracy gain over XGBoost, at higher cost and lower explainability | **[L19]** |
| X2 | Training only on past approvals and trusting reject-inference imputation | Augmentation undermined performance | **[L20]** |
| X3 | Passive scoring of shoppers without explicit consent | Illegal under the PDPL; also a trust risk | **[L56]** |
| X4 | Maximising approval or merchant conversion alone | BNPL gains come mainly from riskier customers; stacking and overdraft effects follow | **[L44][L38][L42]** |
| X5 | Targeting on predicted response or risk rather than uplift and suitability | Wastes offers and can push credit to people it harms | **[L62][L34]** |
| X6 | Persuasive framing or nudges at checkout without guardrails | Content moves demand as much as price; financial-literacy nudges even raised default | **[L64][L32]** |
| X7 | Relying on features that are easy to game (e.g. a single device flag) | Lucas critique | **[L1]** |
| X8 | Speed without disclosure | Fast digital credit with opaque terms leads to late fees and defaults | **[L32][L33]** |

---

## 7. Gaps in the literature & open items

| # | Open item | Why it matters | How to close | Carry to |
|---|---|---|---|---|
| OQ-LR-01 | No peer-reviewed Vietnamese study measures the out-of-sample lift of telco, e-wallet or platform data over CIC for consumer instalment loans | R-1's core claim rests on foreign evidence | Back-test on HLB pilot or partner data by segment; otherwise state as an assumption | Skill 04 / 22 |
| OQ-LR-02 | Current CIC (and PCB) coverage of Vietnamese adults was not verified; the latest verified figure is 30.8m individuals around 2020 **[L57a]** | Sizes the no-bureau problem (links to OQ-08) | CIC annual report / SBV statistics | Skill 02 |
| OQ-LR-03 | No peer-reviewed cost-per-decision benchmark exists for automated emerging-market point-of-sale lending; only consultancy figures (grade C) | Needed to quantify R-2 savings against 380,000 VND | Build a bottom-up cost model (API calls, eKYC, refer-band staff time); state assumptions | Skill 04 / 09 |
| OQ-LR-04 | The economic value of ML over a logistic scorecard at HLB's volume is unknown; no verified evidence on monotonic-constraint trade-offs | Decides whether AI is "incremental value" per the brief | Champion–challenger back-test | Skill 12 / 14 |
| OQ-LR-05 | No verified default or harm outcomes for Vietnamese BNPL or point-of-sale instalment loans | Responsible-lending guardrail calibration | Skill 02 market research (consumer-finance NPL data); state as an assumption | Skill 02 / 21 |
| OQ-LR-06 | No fairness evidence on Vietnamese dimensions (gender, region, age, informality) | Risk of excluding or mispricing groups | Disparity testing on pilot data | Skill 18 / 21 |
| OQ-LR-07 | No Vietnam-specific evidence on onboarding abandonment by step | Sizes R-3 friction benefit | Funnel analytics from HLB Connect or partners | Skill 03 / 04 |
| OQ-LR-08 | No peer-reviewed evidence on first-party / synthetic application fraud rates in emerging-market point-of-sale credit, or on the added value of behavioural biometrics beyond chip-ID matching | Fraud-layer design | HLB fraud data; vendor pilots (grade C) | Skill 14 / 21 |
| OQ-LR-09 | No study applies uplift modelling to pre-approved point-of-sale credit offers with repayment as the outcome | R-4 method is inferred from marketing studies | Randomised offer test in one partner ecosystem | Skill 12 |
| OQ-LR-10 | The PDPL's exact rules for credit-information assessment outputs and any Vietnamese adverse-action (explanation) duty need legal reading | Shapes decision output design | Legal review of Law 91/2025 and Decree 356/2025 | Skill 02 / 22 |
| OQ-LR-11 | Whether telco or e-wallet scoring that HLB consumes must go through the Decree 94 sandbox | Timeline and feasibility | SBV / legal confirmation | Skill 14 / 22 |
| OQ-LR-12 | Several figures seen in search snippets could not be verified and were **excluded**: deHaan et al. percentage increases; McKinsey 2021 revenue and loss figures; an Indonesian MDPI study's Gini figures | Keeps the evidence base clean | Obtain full texts if needed later | — |

---

## Source Index

| # | Full citation | URL / DOI |
|---|---|---|
| L1 | Berg, T., Burg, V., Gombović, A., & Puri, M. (2020). On the Rise of FinTechs: Credit Scoring Using Digital Footprints. *Review of Financial Studies*, 33(7), 2845–2897. (Working paper: FDIC CFR WP 2018-04; NBER w24551) | https://academic.oup.com/rfs/article-abstract/33/7/2845/5568311 ; https://www.fdic.gov/analysis/cfr/working-papers/2018/cfr-wp2018-04.pdf |
| L2 | Björkegren, D., & Grissen, D. (2020). Behavior Revealed in Mobile Phone Usage Predicts Credit Repayment. *World Bank Economic Review*, 34(3), 618–634. | https://academic.oup.com/wber/article-abstract/34/3/618/5622690 ; https://arxiv.org/abs/1712.05840 |
| L3 | Óskarsdóttir, M., Bravo, C., Sarraute, C., Vanthienen, J., & Baesens, B. (2019). The value of big data for credit scoring: Enhancing financial inclusion using mobile phone data and social network analytics. *Applied Soft Computing*, 74, 26–39. | https://doi.org/10.1016/j.asoc.2018.10.004 ; https://arxiv.org/abs/2002.09931 |
| L4 | Gambacorta, L., Huang, Y., Qiu, H., & Wang, J. (2024). How do machine learning and non-traditional data affect credit scoring? New evidence from a Chinese fintech firm. *Journal of Financial Stability*, 73. (BIS WP No. 834, 2019) | https://www.bis.org/publ/work834.htm ; https://www.sciencedirect.com/science/article/abs/pii/S157230892400069X |
| L5 | Frost, J., Gambacorta, L., Huang, Y., Shin, H. S., & Zbinden, P. (2019). BigTech and the changing structure of financial intermediation. BIS Working Paper No. 779 (also *Economic Policy*). | https://www.bis.org/publ/work779.htm |
| L6 | Gambacorta, L., Huang, Y., Li, Z., Qiu, H., & Chen, S. (2023). Data versus Collateral. *Review of Finance*, 27(2), 369–398. | https://academic.oup.com/rof/article-abstract/27/2/369/6569096 |
| L7 | Huang, Y., Zhang, L., Li, Z., Qiu, H., Sun, T., & Wang, X. (2020). Fintech Credit Risk Assessment for SMEs: Evidence from China. IMF Working Paper No. 20/193. | https://www.elibrary.imf.org/view/journals/001/2020/193/article-A001-en.xml |
| L8 | Hau, H., Huang, Y., Lin, C., Shan, H., Sheng, Z., & Wei, L. (2024). FinTech Credit and Entrepreneurial Growth. *Journal of Finance*. | https://doi.org/10.1111/jofi.13384 |
| L9 | Di Maggio, M., Ratnadiwakara, D., & Carmichael, D. (2022). Invisible Primes: Fintech Lending with Alternative Data. NBER WP 29840; *Management Science* (forthcoming). | https://www.nber.org/papers/w29840 ; https://doi.org/10.1287/mnsc.2024.07854 |
| L10 | Chioda, L., Gertler, P., Higgins, S., & Medina, P. C. (2024, rev. 2026). FinTech Lending to Borrowers with No Credit History. NBER WP 33208. | https://www.nber.org/papers/w33208 |
| L11 | FinRegLab (2019). The Use of Cash-Flow Data in Underwriting Credit: Empirical Research Findings. | https://finreglab.org/research/the-use-of-cash-flow-data-in-underwriting-credit-empirical-research-findings/ |
| L12 | Turner, M. A., Walker, P. D., Chaudhuri, S., & Varghese, R. (2012). A New Pathway to Financial Inclusion: Alternative Data, Credit Building, and Responsible Lending in the Wake of the Great Recession. PERC. | https://www.perc.net/wp-content/uploads/2013/09/WEB-file-ADI5-layout1.pdf |
| L13 | Ghosh, P., Vallee, B., & Zeng, Y. (2026). FinTech Lending and Cashless Payments. *Journal of Finance*, 81(2), 1053–1101. | https://doi.org/10.1111/jofi.70003 ; https://www.nber.org/papers/w34148 |
| L14 | Alok, S., Ghosh, P., Kulkarni, N., & Puri, M. (2024, rev. 2025). Breaking Barriers to Financial Access: Cross-Platform Digital Payments and Credit Markets (earlier title: Open Banking and Digital Payments: Implications for Credit Access). NBER WP 33259. | https://www.nber.org/papers/w33259 |
| L15 | Nam, R. J. (2022). Open Banking and Customer Data Sharing: Implications for FinTech Borrowers. SAFE Working Paper No. 364. | https://ideas.repec.org/p/zbw/safewp/364.html ; https://papers.ssrn.com/sol3/papers.cfm?abstract_id=4278803 |
| L16 | Babina, T., Bahaj, S., Buchak, G., De Marco, F., Foulis, A., Gornall, W., Mazzola, F., & Yu, T. (2024). Customer Data Access and Fintech Entry: Early Evidence from Open Banking. NBER WP 32089; *Journal of Financial Economics* (2025). | https://www.nber.org/papers/w32089 ; https://www.sciencedirect.com/science/article/pii/S0304405X24001739 |
| L17 | Ficklin, P. A., & Watkins, P. (2019, Aug 6). An update on credit access and the Bureau's first No-Action Letter. CFPB blog. | https://www.consumerfinance.gov/about-us/blog/update-credit-access-and-no-action-letter/ |
| L18 | Lessmann, S., Baesens, B., Seow, H.-V., & Thomas, L. C. (2015). Benchmarking state-of-the-art classification algorithms for credit scoring: An update of research. *European Journal of Operational Research*, 247(1), 124–136. | https://www.sciencedirect.com/science/article/abs/pii/S0377221715004208 |
| L19 | Gunnarsson, B. R., vanden Broucke, S., Baesens, B., Óskarsdóttir, M., & Lemahieu, W. (2021). Deep learning for credit scoring: Do or don't? *European Journal of Operational Research*, 295(1), 292–305. | https://doi.org/10.1016/j.ejor.2021.03.006 ; https://lirias.kuleuven.be/retrieve/c170bd0f-a5fa-4437-bd58-d74d001f36a2 |
| L20 | Crook, J., & Banasik, J. (2004). Does reject inference really improve the performance of application scoring models? *Journal of Banking & Finance*, 28(4), 857–874; Banasik, J., & Crook, J. (2007). Reject inference, augmentation, and sample selection. *European Journal of Operational Research*, 183, 1582–1594. | https://doi.org/10.1016/j.jbankfin.2003.10.010 ; https://sci2s.ugr.es/keel/pdf/specific/articulo/banasik_crook_07.pdf |
| L21 | Fuster, A., Goldsmith-Pinkham, P., Ramadorai, T., & Walther, A. (2022). Predictably Unequal? The Effects of Machine Learning on Credit Markets. *Journal of Finance*, 77(1), 5–47. | https://doi.org/10.1111/jofi.13090 |
| L22 | Bartlett, R., Morse, A., Stanton, R., & Wallace, N. (2022). Consumer-lending discrimination in the FinTech Era. *Journal of Financial Economics*. (NBER WP 25943) | https://www.sciencedirect.com/science/article/abs/pii/S0304405X21002403 ; https://www.nber.org/papers/w25943 |
| L23 | Blattner, L., & Nelson, S. (2021). How Costly is Noise? Data and Disparities in Consumer Credit. arXiv:2105.07554. | https://arxiv.org/abs/2105.07554 |
| L24 | Bussmann, N., Giudici, P., Marinelli, D., & Papenbrock, J. (2021). Explainable Machine Learning in Credit Risk Management. *Computational Economics*, 57, 203–216. | https://doi.org/10.1007/s10614-020-10042-0 |
| L25 | FinRegLab (2023). Explainability & Fairness in Machine Learning for Credit Underwriting: Policy Analysis (and empirical findings). | https://finreglab.org/research/explainability-fairness-in-machine-learning-for-credit-underwriting-policy-analysis/ |
| L26 | CFPB (2022). Consumer Financial Protection Circular 2022-03: Adverse action notification requirements in connection with credit decisions based on complex algorithms; CFPB (2025). Interpretive Rules, Policy Statements, and Advisory Opinions; Withdrawal (90 FR, 12 May 2025). | https://www.consumerfinance.gov/compliance/circulars/circular-2022-03-adverse-action-notification-requirements-in-connection-with-credit-decisions-based-on-complex-algorithms/ ; https://www.federalregister.gov/documents/2025/05/12/2025-08286/interpretive-rules-policy-statements-and-advisory-opinions-withdrawal |
| L27 | Board of Governors of the Federal Reserve System & OCC (2011). SR 11-7: Guidance on Model Risk Management; Federal Reserve, OCC & FDIC (2026, Apr 17). SR 26-2: Revised Guidance on Model Risk Management (supersedes SR 11-7). | https://www.federalreserve.gov/boarddocs/srletters/2011/sr1107.pdf ; https://www.federalreserve.gov/supervisionreg/srletters/SR2602.pdf |
| L28 | Financial Stability Board (2017, Nov 1). Artificial intelligence and machine learning in financial services. | https://www.fsb.org/uploads/P011117.pdf |
| L29 | Prenio, J., & Yong, J. (2021). Humans keeping AI in check – emerging regulatory expectations in the financial sector. BIS FSI Insights No. 35. | https://www.bis.org/fsi/publ/insights35.htm |
| L30 | Fuster, A., Plosser, M., Schnabl, P., & Vickery, J. (2019). The Role of Technology in Mortgage Lending. *Review of Financial Studies*, 32(5), 1854–1899. (NBER WP 24500) | https://academic.oup.com/rfs/article-abstract/32/5/1854/5427780 ; https://www.nber.org/papers/w24500 |
| L31 | Suri, T., Bharadwaj, P., & Jack, W. (2021). Fintech and household resilience to shocks: Evidence from digital loans in Kenya. *Journal of Development Economics*, 153. (NBER WP 25604) | https://www.sciencedirect.com/science/article/abs/pii/S0304387821000742 ; https://www.nber.org/papers/w25604 |
| L32 | Brailovskaya, V., Dupas, P., & Robinson, J. (2024). Is Digital Credit Filling a Hole or Digging a Hole? Evidence from Malawi. *Economic Journal*, 134(658), 457–. (NBER WP 29573) | https://academic.oup.com/ej/article/134/658/457/7296122 ; https://www.nber.org/papers/w29573 |
| L33 | Izaguirre, J. C., Kaffenberger, M., & Mazer, R. (2018, Sep 25). It's Time to Slow Digital Credit's Growth in East Africa. CGAP blog (based on CGAP demand-side surveys in Kenya and Tanzania). | https://www.cgap.org/blog/its-time-to-slow-digital-credits-growth-in-east-africa |
| L34 | Izaguirre, J. C., Arenaza, S., Meagher, P., & Valenzuela, M. (2025, July). Responsible Digital Credit: Frontier Solutions for Authorities and Providers. Technical Guide. CGAP/World Bank. | https://documents1.worldbank.org/curated/en/099072425135514500/pdf/P181316-72eed651-b7c2-4137-a1f0-a00c3e1efede.pdf |
| L35 | Reserve Bank of India (2021, Nov 18). Report of the Working Group on Digital Lending including Lending through Online Platforms and Mobile Apps (app counts verified via secondary reporting: Inc42; SCC Online); RBI (2022, Sep 2). Guidelines on Digital Lending. | https://rbidocs.rbi.org.in/rdocs/PublicationReport/Pdfs/DIGITALLENDINGF6A90CA76A9B4B3E84AA0EBD24B307F1.PDF ; https://inc42.com/buzz/rbi-spots-600-illegal-lending-apps-out-of-1100-available/ ; https://fidcindia.org.in/wp-content/uploads/2022/09/RBI-GUIDELINES-ON-DIGITAL-LENDING-02-09-22.pdf |
| L36 | Karlan, D., & Zinman, J. (2010). Expanding Credit Access: Using Randomized Supply Decisions to Estimate the Impacts. *Review of Financial Studies*, 23(1), 433–464. | https://papers.ssrn.com/sol3/papers.cfm?abstract_id=982921 |
| L37 | McKinsey & Company. The lending revolution: How digital credit is changing banks from the inside (2018); Designing next-generation credit-decisioning models (2021, Dec 2). | https://www.mckinsey.com/capabilities/risk-and-resilience/our-insights/the-lending-revolution-how-digital-credit-is-changing-banks-from-the-inside ; https://www.mckinsey.com/capabilities/risk-and-resilience/our-insights/designing-next-generation-credit-decisioning-models |
| L38 | Consumer Financial Protection Bureau (2025, January). Consumer Use of Buy Now, Pay Later and Other Unsecured Debt (Shupe & DeLuca). | https://files.consumerfinance.gov/f/documents/cfpb_BNPL_Report_2025_01.pdf |
| L39 | Consumer Financial Protection Bureau (2022, September). Buy Now, Pay Later: Market trends and consumer impacts. | https://files.consumerfinance.gov/f/documents/cfpb_buy-now-pay-later-market-trends-consumer-impacts_report_2022-09.pdf |
| L40 | Financial Conduct Authority (2021, Feb 2). The Woolard Review – A review of change and innovation in the unsecured credit market; FCA (2026). PS26/1: Regulation of Deferred Payment Credit (in force 15 July 2026). | https://www.fca.org.uk/publication/corporate/woolard-review-report.pdf ; https://www.fca.org.uk/publications/policy-statements/ps26-1-regulation-deferred-payment-credit ; https://www.fca.org.uk/firms/regulating-buy-now-pay-later |
| L41 | Di Maggio, M., Katz, J., & Williams, E. (2022). Buy Now, Pay Later Credit: User Characteristics and Effects on Spending Patterns. NBER WP 30508. | https://www.nber.org/papers/w30508 |
| L42 | deHaan, E., Kim, J., Lourie, B., & Zhu, C. (2024). Buy Now Pay (Pain?) Later. *Management Science*, 70(8), 5586–5598. | https://doi.org/10.1287/mnsc.2022.03266 |
| L43 | Guttman-Kenney, B., Firth, C., & Gathergood, J. (2023). Buy now, pay later (BNPL)…on your credit card. *Journal of Behavioral and Experimental Finance*. | https://www.sciencedirect.com/science/article/pii/S2214635023000023 ; https://arxiv.org/abs/2201.01758 |
| L44 | Berg, T., Burg, V., Keil, J., & Puri, M. (2024). The Economics of "Buy Now, Pay Later": A Merchant's Perspective. NBER WP 33152. | https://ideas.repec.org/p/nbr/nberwo/33152.html ; https://www.nber.org/system/files/working_papers/w33152/w33152.pdf |
| L45 | Bian, W., Cong, L. W., & Ji, Y. (2023/2024). The Rise of E-Wallets and Buy-Now-Pay-Later: Payment Competition, Credit Expansion, and Consumer Behavior (NBER WP 31202); The Rise of E-Wallet Super-Apps and Buy-Now-Pay-Later (NBER WP 33178). | https://www.nber.org/papers/w31202 ; https://www.nber.org/papers/w33178 |
| L46 | Pisa, M., & Woodsome, J. (2019, Feb 7). Overcoming the "Know Your Customer" Hurdle with E-KYC. Center for Global Development. | https://www.cgdev.org/blog/overcoming-know-your-customer-hurdle-e-kyc |
| L47 | Gelb, A., & Diofasi Metz, A. (2018). *Identification Revolution: Can Digital ID Be Harnessed for Development?* Center for Global Development. | https://www.cgdev.org/publication/identification-revolution-can-digital-id-be-harnessed-development |
| L48 | FATF (2020, March). Guidance on Digital Identity. | https://www.fatf-gafi.org/content/dam/fatf-gafi/guidance/Guidance-on-Digital-Identity.pdf |
| L49 | Federal Reserve (2019, July). Synthetic Identity Fraud in the U.S. Payment System: A Review of Causes and Contributing Factors. Payments Fraud Insights. | https://fedpaymentsimprovement.org/strategic-initiatives/payments-security/synthetic-identity-payments-fraud/ |
| L50 | Signicat (2022). The Battle to Onboard 2022 (press release: "Record number of European consumers abandon financial applications during onboarding"). | https://www.signicat.com/press-releases/the-battle-to-onboard-2022 ; https://thefintechtimes.com/68-of-european-consumers-abandon-financial-applications-during-onboarding-finds-signicat/ |
| L51 | iBeta Quality Assurance. ISO 30107-3 Presentation Attack Detection Test Methodology and Confirmation Letters. | https://www.ibeta.com/iso-30107-3-presentation-attack-detection-confirmation-letters/ |
| L52 | State Bank of Vietnam, Decision 2345/QĐ-NHNN (Dec 2023; effective 1 Jul 2024) on safe online payment and bank-card solutions (summaries: Keesing Technologies; VietinBank); VietNamNet (2024, Sep 26). Fraud-related accounts reduced by 72% following biometric authentication (quoting Pham Anh Tuan, SBV Payment Department). | https://www.keesingtechnologies.com/blog/id-documents/vietnams-new-regulation-on-biometric-verification-for-banks/ ; https://www.vietinbank.vn/en/xac-thuc-sinh-trac-hoc-khi-giao-dich-truc-tuyen-1-51-html ; https://vietnamnet.vn/en/fraud-related-accounts-reduced-by-72-following-biometric-authentication-2326095.html |
| L53 | State Bank of Vietnam, Circular 17/2024/TT-NHNN (28 Jun 2024) on opening and use of payment accounts. | https://english.luatvietnam.vn/tai-chinh/circular-17-2024-tt-nhnn-opening-and-use-of-payment-accounts-at-payment-service-providers-358811-d1.html ; https://en.baochinhphu.vn/cashless-payment-requires-biometric-data-updating-from-january-1-2025-111241213163253439.htm |
| L54 | State Bank of Vietnam, Circular 64/2024/TT-NHNN on Open API in the banking sector (effective 1 Mar 2025; full compliance 1 Mar 2027). | https://sbv.gov.vn/en/w/press-release-on-issuance-of-circular-no.64/2024/tt-nhnn ; https://english.luatvietnam.vn/tai-chinh/circular-64-2024-tt-nhnn-deployment-of-open-application-programming-interfaces-in-the-banking-sector-387124-d1.html |
| L55 | Government of Vietnam, Decree 94/2025/ND-CP (29 Apr 2025; effective 1 Jul 2025) on the regulatory sandbox for fintech in banking. | https://www.tilleke.com/insights/vietnam-issues-fintech-sandbox-decree/ ; https://www.dfdl.com/insights/legal-and-tax-updates/vietnam-new-decree-no-94-2025-nd-cp-on-regulatory-sandbox-in-the-banking-sector/ |
| L56 | National Assembly of Vietnam, Law on Personal Data Protection No. 91/2025/QH15 (effective 1 Jan 2026); Bùi Hữu Toàn (2026, Apr 9). Luật Bảo vệ dữ liệu cá nhân năm 2025 và những tác động đối với lĩnh vực ngân hàng tại Việt Nam. *Tạp chí Ngân hàng*; Tilleke & Gibbins, Vietnam's New Personal Data Protection Law: A Closer Look. | https://english.luatvietnam.vn/dan-su/law-on-personal-data-protection-law-no-91-2025-qh15-405135-d1.html ; https://tapchinganhang.gov.vn/luat-bao-ve-du-lieu-ca-nhan-nam-2025-va-nhung-tac-dong-doi-voi-linh-vuc-ngan-hang-tai-viet-nam-17211.html ; https://www.tilleke.com/insights/vietnams-new-personal-data-protection-law-a-closer-look/ |
| L57a | Lainez, N. (2021, January). The Prospects and Dangers of Algorithmic Credit Scoring in Vietnam: Regulating a Legal Blindspot. ISEAS Economics Working Paper No. 2021-1. | https://www.iseas.edu.sg/wp-content/uploads/2021/01/ISEASEWP2021-1Lainez.pdf |
| L57b | Lainez, N. (2023). Algorithmic Credit Scoring in Vietnam: A Legal Proposal for Maximizing Benefits and Minimizing Risks. *Asian Journal of Law and Society*. | https://www.cambridge.org/core/journals/asian-journal-of-law-and-society/article/algorithmic-credit-scoring-in-vietnam-a-legal-proposal-for-maximizing-benefits-and-minimizing-risks/332A14670E7C75746E1A3F9B6AD09E4F |
| L58 | Vũ Trọng Sinh (2026, Mar 28). Ứng dụng học tập liên kết trong chấm điểm tín dụng và an ninh tài chính tại Việt Nam [Federated learning in credit scoring and financial security in Vietnam]. *Tạp chí Ngân hàng*. | https://tapchinganhang.gov.vn/ung-dung-hoc-tap-lien-ket-trong-cham-diem-tin-dung-va-an-ninh-tai-chinh-tai-viet-nam-17103.html |
| L59 | Tran, K., Duong, B., Tran, L., Tran, A. L., Nguyen, A. T., & Nguyen, K. V. (2021). Machine Learning-Based Empirical Investigation for Credit Scoring in Vietnam's Banking. In *Advances and Trends in Artificial Intelligence (IEA/AIE 2021)*, Springer LNCS. | https://doi.org/10.1007/978-3-030-79463-7_48 |
| L60 | World Bank, World Development Indicators / Global Findex: Account ownership at a financial institution or with a mobile-money-service provider (% of population ages 15+), Viet Nam (FX.OWN.TOTL.ZS). | https://data.worldbank.org/indicator/FX.OWN.TOTL.ZS?locations=VN ; https://api.worldbank.org/v2/country/VN/indicator/FX.OWN.TOTL.ZS?format=json |
| L61 | Tuoi Tre News (2026, Sep 22). Vietnam's platform workers earn decent incomes but face long hours, social safety net gaps: World Bank (reporting the World Bank / Vietnam Social Security survey of ~4,900 platform workers). | https://news.tuoitre.vn/vietnams-platform-workers-earn-decent-incomes-but-face-long-hours-social-safety-net-gaps-world-bank-103260922215127026.htm |
| L62 | Ascarza, E. (2018). Retention Futility: Targeting High-Risk Customers Might Be Ineffective. *Journal of Marketing Research*, 55(1). | https://journals.sagepub.com/doi/abs/10.1509/jmr.16.0163 |
| L63 | Devriendt, F., Moldovan, D., & Verbeke, W. (2018). A Literature Survey and Experimental Evaluation of the State-of-the-Art in Uplift Modeling. *Big Data*, 6(1), 13–41. | https://doi.org/10.1089/big.2017.0104 |
| L64 | Bertrand, M., Karlan, D., Mullainathan, S., Shafir, E., & Zinman, J. (2010). What's Advertising Content Worth? Evidence from a Consumer Credit Marketing Field Experiment. *Quarterly Journal of Economics*, 125(1), 263–306. | https://academic.oup.com/qje/article/125/1/263/1880334 |
| L65 | Salman, A., Fernandez Vidal, M., & Paikine, R. (2025, Apr 3). Convenience Drives Rapid Adoption of Account Aggregators in India. CGAP blog. | https://www.cgap.org/blog/convenience-drives-rapid-adoption-of-account-aggregators-in-india |

*Context sources also consulted but not cited for figures: Payments Dive coverage of the CFPB 2025 BNPL report (https://www.paymentsdive.com/news/cfpb-buy-now-pay-later-bnpl-use-debt-research-report/737285/), used only to cross-check L38; Business Standard and Sahamati Account Aggregator statistics (grade C), superseded by L65.*
