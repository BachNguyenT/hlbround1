# Requirements Document — Business Challenge 2026 Ideation Round Case: AI in Finance and Banking
**Client:** Hong Leong Bank Vietnam (HLB Vietnam) | **Source:** `Business Challenge 2026 _ Ideation Round Case _ AI in Finance and Banking.pdf` (13 pages) | **Compiled:** 2026-09-28

> This document distils the raw case brief into a structured requirements reference. It is the evidence base for the Foundation and Discover stages. It carries no Source Index because it *is* the source. Everything below is taken from the brief; nothing is inferred.

---

## 1. Industry Overview (brief p.2)
- Financial institutions face pressure from sophisticated fraud, stricter regulation, and rising expectations for personalised digital services. AI has moved from efficiency tool to strategic capability across the financial value chain.
- Enterprise-wide AI adoption is uneven: "Only 58% of finance organizations have actively deployed AI, indicating that many institutions are still struggling to move beyond experimentation." (Brief does not cite the source of the 58% figure — Skill 02 should locate it.)
- Stated next challenge: not simply deploying AI, but doing so while supporting a **more sustainable and inclusive financial system**.

## 2. Client Overview (brief p.3)
- Hong Leong Bank: 120-year Malaysian heritage; presence in Malaysia, Vietnam, Singapore, Hong Kong, Cambodia, China.
- HLB Vietnam: first Malaysian bank to establish operations in Vietnam (2009); offices in Ho Chi Minh City and Hanoi; serves individuals and businesses.
- Recognition: three Digital CX Awards 2026 (The Digital Banker) — Best Digital Bank for Customer Experience in Vietnam; Best SME Bank for Customer Experience in Vietnam and Southeast Asia.

## 3. Current Journey — "Credit as Part of the Purchase Experience" (brief pp.4–5)
**Today:** Credit is extended through an application journey designed for **branch lending**. A customer seeking financing via a merchant or partner platform is redirected into a separate process:
1. Physical or scanned document submission
2. Verification
3. Credit officer review against credit bureau records
4. Decision after several days

**Customer needs:** Vietnamese consumers buy across physical stores, e-commerce platforms, and merchant apps. For larger purchases some want to pay over time. When financing isn't available at that moment, the customer **delays, abandons, or seeks credit outside the formal banking system**. Customers expect credit that is:
- available within the shopping journey,
- simple to apply for,
- approved within **minutes rather than days**,
- offered at a **fair and transparent cost**.

**Assessment basis:** formal credit bureau history and collateral.
- Workable for salaried customers with existing loans/cards.
- For young professionals, gig/platform workers, and small online merchants, records may be **short or absent**, even though they earn and transact actively through digital channels.
- Platform income, e-wallet activity, and online sales histories **exist as records within the bank, its partners, or third parties**, but a document-based review does not draw on them.

**Unit economics:** Each review is manual, takes several days, and carries a **fixed cost per application regardless of loan size**. Small for large branch loans; for small-ticket checkout loans the review **may cost more than the loan earns**, and the decision arrives after the purchase moment has passed.

## 4. Business Problem (brief p.6) — verbatim requirements
HLB Vietnam aims to build point-of-purchase lending into a **commercially viable business line**. This requires:
- **R-1 Beyond-bureau assessment:** assess creditworthiness beyond the bureau file, extending reliable risk assessment to young professionals, gig/platform workers, and online merchants.
- **R-2 In-moment, proportional-cost decisions:** approval decisions delivered within the purchase moment, at a cost per decision proportional to small-ticket loan sizes, while maintaining risk quality, responsible lending standards, and regulatory compliance.
- **R-3 Real-time identity & genuineness:** verify each applicant is who they claim to be and each application is genuine, in real time during the digital journey, without the document friction that causes abandonment today.
- **R-4 Targeted offers:** within merchant/partner ecosystems, identify which customers are most likely to need, accept, and responsibly use embedded credit, so offers are targeted and relevant rather than broadcast.

## 5. Exhibit 1 — Customer Segmentation and Credit Assessment (brief pp.7–8)

| Segment | Current approval rate & terms | Internal visibility | Risk assessment basis & behavioural patterns | Underwriting challenge / mitigation under STP |
|---|---|---|---|---|
| **Salaried, with credit history** | Highest approval rate, largest tickets, lowest pricing | Most visible — bank sees transaction history via existing account data | Formal bureau records (CIC) and existing credit/loan history provide an established baseline | Strong risk profile regardless of method; STP maintains terms while automating execution |
| **Salaried, no credit history** | Moderate to low approval; limited by lack of prior bureau records | Visible if existing account holders; otherwise observable primarily upon application | Stable monthly inflows; regular salary transfers prove income stability | Requires assessing regular income inflows rather than relying strictly on bureau files |
| **Gig / platform workers** | Lowest approval rates (alongside first-time borrowers); frequently declined despite suspected repayment ability | Low direct visibility until applying, unless linked via platform partners or digital channels | Income received through platforms; consistent purchasing patterns and regular platform activity | Lacks traditional documentation |
| **Online merchants (MSME)** | Frequently declined despite officers observing operational cash flows | Moderate to low visibility prior to application | High transaction activity, digital sales histories, recurring business cash inflows | Mitigated by onboarding to a CASA account early to build observable transaction records before credit decisions |
| **First-time digitally active borrowers** | Lowest approval rates due to lack of scorable traditional credit data | Almost invisible until they submit an application | Digital footprints: e-wallet top-ups, telecom usage patterns, on-time utility bill payments | Cannot be assessed by standard scorecards |

*STP = straight-through processing. CIC = Credit Information Centre (State Bank of Vietnam's national credit bureau). CASA = current and savings account.*

## 6. Exhibit 2 — Point-of-Purchase Pilot Operational Baseline & Inbound Application Mix (brief p.9)

| Operational parameter | Baseline value |
|---|---|
| Requested financing range | 1,600,000 – 90,100,000 VND |
| Available loan tenures | 3, 6, 9, 12, 18, 24 months |
| Underwriting workflow | Centralised manual review |
| Internal processing cost benchmark | 380,000 VND / application |
| Decision turnaround time range | 1.8 – 4.6 business days |

| Inbound application mix | Share |
|---|---|
| Salaried (with credit) | 35.5% |
| Salaried (no credit) | 20.5% |
| Gig / platform workers | 15.0% |
| Online merchants (MSME) | 14.5% |
| First-time borrowers | 14.5% |

Derived (arithmetic only): thin-file segments (gig + MSME + first-time) = 44.0%; all no-bureau segments (incl. salaried no-credit) = 64.5%. Review cost as share of the minimum ticket = 380,000 / 1,600,000 = 23.75%; of the maximum ticket = 0.42%.

## 7. Jobs To Be Done (brief p.10)
Using the information provided and considering operating conditions in Vietnam and other developing markets:
1. Identify the bottleneck and analyse the root of the problem.
2. Propose a business solution with key metrics to demonstrate effectiveness. AI solutions are acceptable, **provided they demonstrate clear, incremental value compared to the current operational baseline.**
3. Propose the operational mechanism of the AI / non-AI solution.

"Teams may make reasonable assumptions where information is unavailable, but **all assumptions must be stated clearly.**"

## 8. Marking Rubric (brief p.11)

| # | Criterion | Weight | Assessment details |
|---|---|---|---|
| R1 | Problem Diagnosis | 20% | Precisely isolate the core bottleneck or tightly coupled decision point(s) with the highest operational leverage. Clearly demonstrate the root cause and mechanism driving it. |
| R2 | Intervention Justification | 30% | Prove how the core mechanism directly resolves the bottleneck; prove measurable business benefits (speed, operational efficiency, CX); prove practical feasibility with innovative, well-reasoned intervention logic. **If AI:** show how AI creates superior value and why it's operationally viable. **If non-AI:** show how a lean process or creative rule-based redesign solves it with high feasibility and resource efficiency. |
| R3 | Solution Concept | 35% | Clear, feasible **data-to-decision architecture** (data inputs, logical framework, outputs, subsequent business actions). Comprehensive community impact, or long-term financial and environmental sustainability. **Operational KPIs that directly measure bottleneck relief**, avoiding disconnected macro statistics. |
| R4 | Data and Assumptions | 10% | Explicitly state all assumptions; realistic and anchored in case data or credible operational benchmarks. |
| R5 | Visualization | 5% | 1920 × 1080 (16:9) layout in a **1-page PDF**, clear visual hierarchy between primary and secondary information. |

## 9. Brand Assets (brief p.12)
Horizontal and vertical HLB logos; typeface **Noto Sans**.
