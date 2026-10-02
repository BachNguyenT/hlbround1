# User Research — Pain Points & Behaviours
**Product:** HLB Point-of-Purchase Credit Decisioning (working name) | **Skill:** 03 | **Status:** Done (2026-09-28)
**Depends on:** market-research.md | **Feeds into:** problem-research.md, persona-research.md

---

## Pre-filled Context
- Target user hypothesis: Thin-file point-of-purchase borrower in Vietnam (gig/platform worker, online MSME merchant, or first-time digitally active borrower), plus HLB Vietnam credit-risk/underwriting lead as internal operator
- Problem hypothesis: HLB Vietnam's point-of-purchase lending relies on a branch-style, document- and bureau-based manual review (380,000 VND/application, 1.8–4.6 business days) that cannot assess thin-file applicants, cannot decide within the purchase moment, and costs too much per decision relative to small-ticket loans (1.6M–90.1M VND)
- Product type: Other — bank-internal credit decisioning capability, distributed B2B2C through merchant/partner checkouts

---

### How to read this document
- **Segment codes:** `SAL-C` salaried with credit history · `SAL-N` salaried without credit history · `GIG` gig/platform worker · `MSME` online merchant · `FTB` first-time digitally active borrower · `OFFICER` bank credit officer / credit-risk lead · `MERCHANT` merchant or partner hosting financing at checkout.
- **Rubric tags:** `[R1 Diagnosis]` · `[R2 Justification]` · `[R3 Concept]` · `[R4 Assumptions]`.
- **Confidence rule (global rule §1):** **Confirmed** means the signal appears in at least 3 independent *source types* (for example press + forum + survey). **Partial** means 2 source types. **Hypothesis** means 1 source type. Case-brief facts are cited **[Brief p.N]** and count as one source type (the client's own account).
- **Sources:** **[S#]** refers to the Source Index at the end. Vietnamese quotes are given in the original, with our English translation. Quotes were pulled from the fetched pages; a few are close paraphrases, and those are labelled *(paraphrase)*.
- **Scoring:** Frequency (F) and Severity (Sev) are rated High = 3, Medium = 2, Low = 1. **Opportunity Score = F × Sev (1–9).** We did not use importance/satisfaction ratings because no user survey gives them, and making them up would break the zero-assumption rule.

---

## 1. Pain Point Registry

Sixteen pain points in five clusters: **A** access and assessment · **B** speed and friction · **C** cost, conduct and trust · **D** the bank operator · **E** the merchant. They are ranked inside each cluster by Opportunity Score.

### Segment × pain heat-map (summary)

| # | Pain point | SAL-C | SAL-N | GIG | MSME | FTB | OFFICER | MERCHANT | Confidence |
|---|---|---|---|---|---|---|---|---|---|
| 1 | Income proof doesn't fit how they earn | · | ● | ●● | ●● | ● | ● | · | Confirmed |
| 2 | Invisible to the bureau → decline or no offer | · | ●● | ● | ● | ●● | ● | · | Confirmed |
| 3 | Cash flow exists but isn't recognised (mixed accounts, collateral bias) | · | · | ● | ●● | · | ●● | · | Confirmed |
| 4 | Decision arrives after the purchase / stock moment | ● | ● | ● | ●● | ● | ● | ●● | Confirmed |
| 5 | eKYC / biometric steps fail on the customer's phone | ● | ● | ● | ● | ● | · | ● | Confirmed (foreign survey leg) |
| 6 | Opaque rejection — no reason, no path | · | ● | ● | · | ●● | · | · | Partial |
| 7 | Hidden or all-in cost opacity, bundled insurance | ● | ● | ●● | · | ●● | · | · | Confirmed |
| 8 | Fear of collection harassment, incl. of references and relatives | ● | ● | ● | · | ●● | · | · | Confirmed |
| 9 | Unwanted cross-sell calls / data passed to other lenders | ●● | ● | · | · | ● | · | · | Confirmed |
| 10 | Volatile income makes fixed instalments painful | · | · | ●● | ● | · | · | · | Confirmed |
| 11 | Over-borrowing / debt spiral among young borrowers | · | · | · | · | ●● | ● | · | Confirmed |
| 12 | No standard data to judge thin-file cases → default to collateral or decline | · | · | · | · | · | ●● | · | Confirmed |
| 13 | Manual, repetitive file assembly and verification | · | · | · | · | · | ●● | · | Confirmed |
| 14 | Identity and document fraud that document-based review can't catch | · | · | · | · | · | ●● | · | Partial (press/court reporting) |
| 15 | Lost sales when financing isn't instantly available | · | · | · | · | · | · | ●● | Partial (Vietnam leg is vendor claims) |
| 16 | In-store financing friction and loss of control over the offer | · | · | · | · | · | · | ● | Hypothesis |

●● = primary pain for the segment · ● = present · · = not evidenced

---

### Cluster A — Access & assessment

```
Pain Point #1: "Prove your income" doesn't fit how they actually earn
Segments: GIG, MSME, SAL-N, FTB (primary); OFFICER (receives unscoreable files)
Frequency: High | Severity: High | Opportunity: 9 | Confidence: Confirmed (survey + forum + press + research + brief)
Category: Workflow / Missing Feature
Rubric: [R1 Diagnosis] [R3 Concept]

Evidence:
  - Survey: "31% lack or feel uncomfortable providing labour contracts for loans"; "33% hesitant
    to share wage slips in loan applications" — Decision Lab × Home Credit Vietnam, n=1,200
    online adults, fieldwork 1–7 Dec 2023 [S18]
  - Forum: a freelancer asking for a 50M VND loan says he has no labour contract and no social
    insurance, only bank statements showing 15–30M VND/month income; replies say applying in
    person helps "for those without labour contracts" and first-time applicants "may face
    rejection due to lack of history" (paraphrase) — voz.vn, "Hỏi vay Homecredit cho freelancer" [S8]
  - Research: gig workers face credit barriers from "irregular income patterns and insufficient
    financial documentation" that traditional lending criteria struggle to handle — CGAP, Jun 2023 [S6]
  - Press (MSME): "Không có bảng lương, người làm nghề tự do chứng minh thu nhập thế nào để vay
    vốn?" ("No payslip — how do freelancers prove income to borrow?") appears as a recurring
    headline genre; banks tell sellers, drivers and freelancers to use regular inflows to their
    account as proof instead [S1 summary; search-level evidence only]
  - Brief: gig workers "lack traditional documentation"; first-time borrowers "cannot be assessed
    by standard scorecards" [Brief pp.7–8]

Workaround being used: Platform-embedded loans that skip income proof and deduct repayments
  from the driver's wallet (Pain #10) [S1]; Shopee's SEasy seller loans advertised as "không cần
  chứng minh thu nhập" (no income proof needed) [S41]; applying in person; informal credit [S47].
Unmet need: Be judged on the money they actually receive — platform payouts, e-commerce sales,
  e-wallet and salary inflows — without producing paper that their way of working never creates.
```

```
Pain Point #2: Invisible to the bureau → declined or never offered credit
Segments: FTB, SAL-N (primary); GIG, MSME
Frequency: High | Severity: High | Opportunity: 9 | Confidence: Confirmed (official data + expert press + app-store + forum + brief)
Category: Missing Feature
Rubric: [R1 Diagnosis]

Evidence:
  - Official: national credit-information coverage reached "over 48.6 million subjects"
    (individuals and legal entities), "more than 60% of Vietnam's adult population" — CIC CEO Cao
    Văn Bình, VnEconomy, 7 Apr 2022 [S35]. So roughly 4 in 10 adults were outside coverage (dated
    figure, see OQ-03-07).
  - Expert press: "hiện mới chỉ có hệ thống chấm điểm của CIC và hệ thống này chưa áp dụng một cách
    đại trà cho toàn dân" ("so far there is only CIC's scoring system, and it has not been applied
    across the whole population") — Dr Nguyễn Trí Hiếu, Báo Tin Tức, 12 May 2024 [S33]
  - Industry: "hiện nay có rất nhiều khách hàng dưới chuẩn ngân hàng … không có lịch sử tín dụng"
    ("there are very many sub-bank-standard customers … with no credit history") — Nguyễn Hải Nam,
    CEO Credit 360.AI, VnEconomy, 18 Jun 2024 [S34]
  - App store: "Bắt chụp chứng minh thư xong kh đc duyệt hồ sơ" ("Made me photograph my ID, then
    the application wasn't approved") — Fundiin iOS reviews [S22]; the developer replies that
    rejections come from an automated credit-scoring system
  - Brief: first-time borrowers have the "lowest approval rates due to lack of scorable traditional
    credit data"; salaried-no-credit approval is "moderate to low" [Brief pp.7–8]

Workaround being used: Fincos and BNPL apps that score on ID + phone number (Fundiin claims
  "xét duyệt hồ sơ trong 5s … chỉ với CMND/CCCD và số điện thoại chính chủ" — approval in 5 seconds
  with just an ID card and a registered phone number) [S30]; illegal loan apps [S14][S47].
Unmet need: A fair first "yes" (possibly small) that uses signals they do have, and a way to build
  a record from it.
```

```
Pain Point #3: Business cash flow is real but not recognised (mixed accounts, collateral bias)
Segments: MSME (primary); GIG; OFFICER
Frequency: High | Severity: High | Opportunity: 9 | Confidence: Confirmed (banker + expert press + official stats + brief)
Category: Workflow / Missing Feature
Rubric: [R1 Diagnosis] [R3 Concept]

Evidence:
  - Banker: "SMEs face difficulty because evaluation data lacks standardized systems for
    assessment" — Trần Việt Phương, BVBank; the same article reports "over 75% of businesses cannot
    borrow without collateral" and that only 20.5% of SMEs access loans vs 61.4% of large firms —
    VTV, 25 Sep 2026 [S38]
  - Banker: "When cash comes from multiple sources like transfers, QR codes, and e-commerce
    platforms, it becomes difficult to manage" without integrated systems; many household
    businesses "still mix business cash flow with personal spending" — Techcombank director,
    Tuổi Trẻ, 30 Mar 2026 [S39]
  - Seller-platform content: banks commonly give "không có hệ thống quản lý, không có báo cáo tài
    chính hoặc không kê khai thuế" (no management system, no financial statements, or no tax
    filings) as reasons for refusing small businesses [search-level evidence, Sapo/VnExpress results]
  - Brief: MSMEs are "frequently declined despite officers observing operational cash flows";
    mitigation is "onboarding to a CASA account early" [Brief p.8]

Workaround being used: Platform-embedded seller loans — Shopee SEasy (review "trong vòng 24 giờ",
  from 1.17%/month, 3–12 months, only for eligible sellers) [S41]; bank sales-ledger apps that
  capture cash flow (Techcombank T-Shop [S39]; HLB's own Sổ Bán Hàng partnership, per Skill 01);
  borrowing against a house.
Unmet need: Let observed sales and settlement flows stand in for financial statements and
  collateral — with the seller's consent.
```

### Cluster B — Speed & friction

```
Pain Point #4: The decision arrives after the purchase (or stock-buying) moment has passed
Segments: MSME, MERCHANT (primary); all borrower segments; OFFICER
Frequency: High | Severity: High | Opportunity: 9 | Confidence: Confirmed (seller quote + trader press + onboarding survey + competitor benchmarks + brief)
Category: Performance / Workflow
Rubric: [R1 Diagnosis] [R2 Justification]

Evidence:
  - Seller: "Nhiều lúc nhìn thấy nguồn hàng đẹp, giá tốt nhưng vốn chưa xoay kịp. Chỉ chậm vài ngày
    là hàng hết hoặc đối thủ đã nhập trước rồi" ("Many times I see good stock at a good price but my
    capital hasn't turned around in time. A few days late and it's sold out or a competitor got
    there first") — Ms Mỹ Phụng, swimwear shop owner, HCMC; Công Thương, 27 Jul 2026 [S40]
  - Trader press: people turn to black credit because they "cần tiền gấp, thiếu tài sản thế chấp
    và ngại thủ tục phiền phức" (need money urgently, lack collateral and dread cumbersome
    procedures) — Kinh tế Chứng khoán, 30 Jan 2016 [S48]; most app borrowers "need to borrow a small
    sum of money in a short time but do not want to sign in loan procedures at banks" — Vietnam
    News, 8 Dec 2022 [S47]
  - Survey (Europe, used as a benchmark): the average applicant abandons a financial application
    after 18 min 53 s; 68% have abandoned one — Signicat / Sapio, n=7,600, Mar 2022 [S24]
  - Competitor benchmark: "Ninety five per cent of Home Credit Vietnam's customers receive results
    within 15 minutes, at an average of five minutes per person" at the POS — VIR, Oct 2016 [S46];
    ACB cut approval "from 10 days to 5–10 minutes" with ~60% auto-approved — VietnamFinance via
    DNSE, 10 Sep 2025 [S36]
  - Brief: 1.8–4.6 business days; "the decision arrives after the purchase moment has passed"
    [Brief pp.5, 9]

Workaround being used: Use a finco at the store counter (minutes); delay or abandon the purchase;
  borrow informally [Brief p.4][S47].
Unmet need: An answer while the customer is still at the checkout — minutes, not days.
```

```
Pain Point #5: Identity/eKYC steps fail on the customer's own phone
Segments: all borrower segments; MERCHANT (drop-off at checkout)
Frequency: Medium | Severity: Medium | Opportunity: 4 | Confidence: Confirmed — press + app-store + survey (survey leg is European)
Category: UX / Performance
Rubric: [R2 Justification] [R3 Concept]

Evidence:
  - Press: on the first day of mandatory biometric checks, users saw "Không nhận ra khuôn mặt. Bạn
    hãy xác thực lại nhé" ("Face not recognised. Please verify again") and "Thông tin giấy tờ không
    hợp lệ" ("Document information invalid" at the NFC step); one user split transfers to stay under
    the 10M VND threshold; others waited 4+ hours at Vietcombank branches — Tuổi Trẻ, 3 Jul 2024 [S44]
  - App store: "Chụp ảnh cmnd rồi gửi chục lần không được" ("Photographed my ID and sent it a dozen
    times, still no good") — Fundiin iOS reviews [S22]
  - Survey (Europe): 38% abandoned a financial application because they lacked the required ID
    credential; 21% because it took too long; 21% because of the amount of personal information
    requested — Signicat 2022 [S24]

Workaround being used: Split transactions below the threshold; go to a branch; retry many times [S44][S22].
Unmet need: Identity proof that works first time with the chip ID and phone they already have, and
  a fallback that doesn't mean "go to a branch".
Note: No Vietnam-specific eKYC failure or drop-off *rate* was found → OQ-03-09.
```

```
Pain Point #6: Opaque rejection — no reason given, no path to eligibility
Segments: FTB (primary); SAL-N, GIG
Frequency: Medium | Severity: Medium | Opportunity: 4 | Confidence: Partial (app-store + forum)
Category: UX
Rubric: [R3 Concept] [R4 Assumptions]

Evidence:
  - App store: repeated Fundiin reviews of ID submitted, then declined; the developer's standard
    reply cites an automated multi-data-point scoring system without giving individual reasons [S22]
  - App store: "Had one month remaining on loan. Wanted to borrow more but was denied" — Home Credit
    iOS review, 20 Dec 2022 [S23]
  - Forum: first-time applicants "may face rejection due to lack of history" (paraphrase) [S8]
  - Lender content: lenders publish long generic lists of rejection reasons (bad CIC history,
    insufficient income, inaccurate information, existing debts, location, age/occupation) rather
    than per-applicant reasons — Home Credit blog, 3 Jun 2024 [S50]

Workaround being used: Try another app or lender; reapply repeatedly [S22].
Unmet need: Know why they were declined and what would change the answer (a graduation path).
Why it matters for the case: responsible-lending and explainability expectations (R-2) — to be
  checked against Vietnamese regulation in Skill 02 / the literature review.
```

### Cluster C — Cost, conduct & trust

```
Pain Point #7: True cost is opaque — service fees, penalties, bundled insurance
Segments: FTB, GIG (primary); SAL-C, SAL-N
Frequency: High | Severity: Medium | Opportunity: 6 | Confidence: Confirmed (press + forum + app-store + merchant positioning)
Category: Pricing
Rubric: [R3 Concept] [R4 Assumptions]

Evidence:
  - Forum/press repost: a 29-year-old factory worker (8M VND/month) borrowed 9M VND online for his
    daughter's hospitalisation and repaid "nearly 14 million" after service fees (~2.3M), a
    collection fee (180k) and a late penalty (250k) — voz.vn, "Thòng lọng vay nhanh trả góp trực
    tuyến" [S10]
  - Forum: "borrowing 20 million costs about 24 million to repay" (paraphrase); reported rates of
    30–40%/year — voz.vn [S8]
  - Press: contract rate "20% per year" but with added fees, actual rates reach "tens of percent per
    month" — Tiền Phong, 23 Apr 2025 [S15]
  - Press (forced insurance): "Tôi cảm thấy không hài lòng và cực kỳ tức giận" ("I felt dissatisfied
    and extremely angry") — Ms Linh, told to buy 40M VND of insurance to get a bank loan; another
    borrower was told cancelling the policy meant "Tất toán ạ, tất toán khoản vay" (settle the loan
    in full) — Tuổi Trẻ, 13 Feb 2023 [S45]
  - App store: early settlement after 3 of 6 months triggered an unexpected ~10% charge, and staff
    gave inconsistent information — Home Credit iOS review, 16 Dec 2022 [S23]
  - Merchant positioning: Thế Giới Di Động's 2024 pay-later pitches 0% interest with no hidden fees
    "like insurance or processing charges" — Thanh Niên, 11 Nov 2024 [S28]

Workaround being used: Choose 0% offers, including through credit cards [S19][S28]; complain to
  regulators [S13].
Unmet need: One all-in monthly figure and total cost, shown up front, with no forced add-ons.
```

```
Pain Point #8: Fear of harassing collection — including calls to references and relatives
Segments: FTB (primary); all consumer segments
Frequency: Medium | Severity: High | Opportunity: 6 | Confidence: Confirmed (forum + regulator + ministry statistics + press)
Category: Trust / Conduct
Rubric: [R2 Justification] [R3 Concept]

Evidence:
  - Forum: collectors "có mười mấy nghìn số điện thoại" ("have tens of thousands of phone numbers");
    "nó gọi liên tục" ("they call non-stop") — tinhte.vn, 1 Feb 2020 [S11]
  - Regulator: the SBV inspection agency added FE Credit to its 2018 inspection plan after
    complaints of threats, calls to reference contacts who never borrowed, and months of harassment.
    "I told them clearly I didn't borrow money…" — Ms Thủy Anh, HCMC — Tuổi Trẻ/PLO, 14 May 2018 [S12]
  - Ministry statistics: consumer credit was the most-complained-about service, "chiếm tới 21,8%" of
    complaints, led by non-borrowers "liên tục bị gọi điện thoại, nhắn tin để quấy rối, đe doạ"
    (continuously called and texted to harass and threaten) — MOIT Competition & Consumer Protection
    Dept., via CafeF, 1 May 2020 [S13]
  - Press (young borrowers): "Chỉ cần trả nợ chậm một vài hôm, bên cho vay đã gọi điện đe dọa sẽ
    khóa iCloud" ("Just a few days late and the lender called threatening to lock my iCloud") —
    Hữu Tùng, 3rd-year student, HCMC; Tiền Phong, 23 Apr 2025 [S14]

Workaround being used: Truecaller and community-reported numbers [S11]; hiding debts from family
  [S14]; going to the police [S15].
Unmet need: A lender that collects lawfully and never contacts third parties — a trust advantage
  that a *bank* brand could plausibly claim (hypothesis, see OQ-03-08).
```

```
Pain Point #9: Unwanted cross-sell calls and data passed to other lenders after one loan
Segments: SAL-C (primary); SAL-N, FTB
Frequency: Medium | Severity: Medium | Opportunity: 4 | Confidence: Confirmed (forum + consumer survey + academic survey; the Signicat leg is European)
Category: Trust / Privacy
Rubric: [R3 Concept] [R4 Assumptions]

Evidence:
  - Forum: "Used them for installments, paid on time without missing a cent..then they call
    constantly asking if I want to borrow money" — user "Vô Tâm", voz.vn, 3 Jul 2022; another user
    reports calls from several lenders (ACS, FE, MB, TPBank) after one purchase [S9]
  - Consumer survey: "49,5% người dùng Việt xem việc lạm dụng dữ liệu cá nhân là mối lo lớn nhất"
    (49.5% of Vietnamese users see misuse of personal data as their biggest worry); 79% see online
    fraud as the top risk — Vero × Kadence, SEA Consumer Tech Trust Index 2026, via Tuổi Trẻ,
    4 Aug 2026 [S42]
  - Academic survey: among 417 Hanoi students, privacy concerns strongly raised perceived risk
    (β = 0.629), and trust was the strongest driver of willingness to share data (β = 0.424) —
    Tạp chí Kinh tế Tài chính, 9 Jun 2026 [S43]
  - Survey (Europe): 92% worry how financial providers use their data — Signicat 2022 [S24]

Workaround being used: Blocking numbers; reluctance to share documents (31–33%, Pain #1) [S18].
Unmet need: Clear, narrow consent for exactly what data the decision uses, and no marketing reuse.
Implication for alternative data: consent UX is a make-or-break design element (→ Skill 13).
```

```
Pain Point #10: Volatile, long-hours income makes fixed instalments painful
Segments: GIG (primary); MSME
Frequency: Medium | Severity: High | Opportunity: 6 | Confidence: Confirmed (press ×2 + World Bank survey + academic/NGO rating)
Category: Pricing / Workflow
Rubric: [R3 Concept] [R4 Assumptions]

Evidence:
  - Press: "Giờ ế lắm. Nhiều hôm tôi phải chạy xe liên tục 12 tiếng đồng hồ mới kiếm được 300.000
    đồng" ("It's very slow now. Many days I ride 12 hours straight to earn 300,000 VND") — Bảo Châu,
    33, motorbike driver. And "Chậm ngày nào, tôi vẫn là con nợ, phải trả thêm lãi. Nhưng không chạy
    xe, lấy tiền đâu mà trả" ("Every day late I'm still a debtor paying more interest. But if I don't
    drive, where does the money come from?") — Huy, driver. The same article describes a phone loan
    of 11.6M VND repaid at 32,000 VND/day auto-deducted from the driver wallet over 360 days, at
    1.67–2.5%/month — Tuổi Trẻ, 4 Aug 2023 [S1]
  - Press: "Nhiều anh em đọc không kỹ, tưởng dễ ăn nên đăng ký vay. Có thời điểm ế khách, chạy sấp
    mặt cũng không đủ tiền trả nợ" ("Many guys don't read carefully, think it's easy and sign up. In
    slow periods you can ride till you drop and still not cover the debt") — Mr K., Bình Thạnh [S1];
    income fell "hơn 60%" during COVID — Nguyễn Văn Hinh, Grab driver, Tuổi Trẻ, 14 Apr 2020 [S2]
  - Survey: a World Bank survey of nearly 5,000 platform workers found "hơn 90% … coi công việc này
    là nguồn thu nhập chính, nhưng chỉ khoảng 20% tham gia bảo hiểm xã hội" (>90% see it as their
    main income but only ~20% have social insurance) — SGGP, 22 Sep 2026 [S3]; in a separate survey
    of 141 drivers, 35.5% work more than 10 hours a day — Thanh Niên, 16 Sep 2026 [S4]
  - Rating: no Vietnamese platform could evidence that workers earn at least the local minimum wage
    after costs — Fairwork Vietnam Ratings 2023 [S5]

Workaround being used: Auto-deduction from platform wallets (accepted, but at high cost) [S1];
  working longer hours [S4].
Unmet need: Affordability checks and repayment timing that follow daily/weekly income, not a
  monthly salary calendar. Also a signal for underwriting: platform-wallet auto-deduction is a
  repayment rail these workers already accept.
```

```
Pain Point #11: Over-borrowing and debt spirals among young first-time borrowers
Segments: FTB (primary); OFFICER (risk quality)
Frequency: Medium | Severity: High | Opportunity: 6 | Confidence: Confirmed (press + national survey + academic study)
Category: Responsible lending
Rubric: [R2 Justification] [R3 Concept]

Evidence:
  - Press: "Lúc đó tôi cảm thấy nhẹ nhõm vì đã giải quyết được vấn đề trước mắt" ("At the time I felt
    relieved to have solved the immediate problem"), then later "Tôi cảm thấy hối hận khi lựa chọn vay
    kiểu này. Nó không hề an toàn hay đơn giản như tôi tưởng" ("I regret borrowing this way. It wasn't
    safe or simple at all as I thought") — Hữu Tùng, student, 10M VND at 4–5%/month — Tiền Phong,
    23 Apr 2025 [S14][S15]; a student accumulated >100M VND of debt across banks, fincos and illegal
    apps — Tuổi Trẻ, May 2026 [S16]
  - Survey: ~40% of people with loans or credit debt struggle with monthly repayments; 46% don't know
    missed payments affect their credit rating; 57% don't know it affects future credit access —
    Decision Lab × Home Credit, Dec 2023 [S18]
  - Academic: among Vietnamese university students, FoMO and positive attitudes "emerge as strong
    predictors of impulsive BNPL purchases, which … significantly elevate debt risk"; spending
    control and financial literacy are protective — BJBS, 2025 [S21]
  - Fraud variant: students and newly arrived workers were tricked into installing an e-wallet app,
    and their biometrics were used to take loans in their names (13 victims, ~300M VND) — Tuổi Trẻ,
    18 Nov 2025 [S17]

Workaround being used: Borrowing from a second app to repay the first (loan stacking) [S16];
  concealing it from family [S14].
Unmet need (latent, not voiced up front): Limits sized to real capacity, a clear view of
  obligations, and protection from identity misuse. This is the "responsible" half of R-2.
```

### Cluster D — The bank operator (OFFICER)

```
Pain Point #12: No standard, trusted data for thin-file cases → default to collateral or decline
Segments: OFFICER (primary)
Frequency: High | Severity: High | Opportunity: 9 | Confidence: Confirmed (banker quotes + expert press + finco CEO + brief)
Category: Missing Feature / Workflow
Rubric: [R1 Diagnosis] [R3 Concept]

Evidence:
  - Banker: SMEs struggle "because evaluation data lacks standardized systems for assessment" —
    BVBank; SBV plans to pilot cash-flow data analysis that connects tax data first — VTV, 25 Sep 2026 [S38]
  - Finco CEO: national "trustworthiness" scoring from the Ministry of Public Security "giúp chấm
    điểm được cả những khách hàng chưa từng có lịch sử tín dụng" (helps score even customers with no
    credit history) — Lê Quốc Ninh, CEO Mcredit, VnEconomy, 18 Jun 2024 [S34]. This implies that
    without such data they cannot.
  - Expert: CIC is the only scoring system and does not cover people who never used banks [S33]
  - Brief: gig workers "frequently declined despite suspected repayment ability"; MSMEs declined
    "despite officers observing operational cash flows" [Brief p.8]

Workaround being used: Ask for collateral; decline; call employers and references; visit homes
  (manual appraisal practice [S49]; Home Credit field/home checks per forum [search-level, voz]).
Unmet need: Standardised, consented alternative-data evidence and a policy that lets the officer
  say "yes" on it defensibly.
Evidence gap: no first-hand quote from a Vietnamese credit officer describing a thin-file decision
  was found → OQ-03-03.
```

```
Pain Point #13: Manual, repetitive file assembly and verification is slow and costly
Segments: OFFICER (primary)
Frequency: High | Severity: High | Opportunity: 9 | Confidence: Confirmed (brief + bank executives' public statements + university curriculum describing manual appraisal)
Category: Workflow / Performance
Rubric: [R1 Diagnosis] [R2 Justification]

Evidence:
  - Brief: "centralised manual review", 380,000 VND per application "regardless of loan size",
    1.8–4.6 business days [Brief pp.5, 9]
  - Bank: VietinBank cut preparation of credit-appraisal files "from 4–5 days to about half a day"
    with ML — tincongnghe, May 2026 [S37]; ACB auto-approves ~60% of applications "based on pre-set
    criteria" in 5–10 minutes vs 10 days — 10 Sep 2025 [S36]
  - Practice: consumer-loan appraisal teaching material has officers verify information with
    employers and interview customers at their residences (paraphrase) — Duy Tân University [S49]
  - Competitor practice: Home Credit sends only lower-score cases to people, who contact "the
    client, their referenced contacts, or their employer before giving a final decision" [S46]

Workaround being used: Rule-based auto-approval for clear cases (ACB) [S36]; tiered referral (Home Credit) [S46].
Unmet need: Keep human judgment for the uncertain band only, with the evidence already assembled.
Evidence gap: no public breakdown of how officer time splits across document checks, CIC lookup,
  calls and write-up → OQ-03-03.
```

```
Pain Point #14: Identity and document fraud that document-based review cannot reliably catch
Segments: OFFICER (primary)
Frequency: Medium | Severity: High | Opportunity: 6 | Confidence: Partial — press/court reporting of 3 separate cases plus expert commentary and the brief. No fraud-rate statistic was found.
Category: Risk / Workflow
Rubric: [R1 Diagnosis] [R3 Concept]

Evidence:
  - Finco case: an agent forged 455 ID cards and 32 motorbike registrations and submitted 607 loan
    applications (45 approved, ~1.86bn VND) because he knew the finco's app "không kiểm tra được tính
    đúng đắn thông tin cá nhân, hình ảnh trên chứng minh nhân dân" (could not verify the personal
    information and photo on ID cards) — Tuổi Trẻ, 23 Aug 2021 [S31]
  - Bank insider case: a Vietcombank "chuyên viên thẩm định tín dụng cá nhân" (personal credit
    appraiser) fabricated 20 credit files for 10 customers (~48.8bn VND, 2021–2023) — Tuổi Trẻ,
    18 Apr 2025 [S32]
  - Biometric misuse: victims' biometrics were harvested through an e-wallet app to open loans in
    their names [S17]
  - Expert: fintech data stores are attractive targets for "fraudulent account creation" [S33]

Workaround being used: More documents and more manual checks, which raise cost and abandonment
  (the brief's "document friction") [Brief p.6].
Unmet need: Real-time identity and application-genuineness checks against authoritative sources
  (chip ID/NFC, device, behavioural signals) that are harder to forge than paper and don't rely on
  one insider.
```

### Cluster E — The merchant / partner (MERCHANT)

```
Pain Point #15: Sales lost when financing isn't instantly available to most shoppers
Segments: MERCHANT (primary)
Frequency: Medium | Severity: High | Opportunity: 6 | Confidence: Partial. Global independent data (Baymard, Stripe) plus Vietnam retailer data (2017) and vendor claims. No independent, recent Vietnam measurement.
Category: Performance / Revenue
Rubric: [R2 Justification] [R4 Assumptions]

Evidence:
  - Vietnam retailer: instalments were 26% of phone units but ~47–50% of revenue; 61% of phones
    priced 4.5–10M VND were bought on instalment (48% above 10M, 11% below 4.5M) — Thế Giới Di Động
    director Lê Huy Toàn, Thanh Niên, 18 Nov 2017 [S27] (dated)
  - Global experiment: an A/B test across 150,000+ checkout sessions found up to 14% higher revenue
    when BNPL was offered, with the largest conversion gains on $500–1,500 orders — Stripe, Jun 2024 [S26]
  - Global survey: 70.22% average cart abandonment; among non-browsing abandoners, 17% cite a
    long/complicated checkout, 10% a declined card, 9% too few payment methods — Baymard (US
    shoppers for reasons) [S25]
  - Vendor claim (low authority): Fundiin says merchants get "Tăng doanh thu đến 30%" and
    "Tăng giá trị đơn hàng trung bình đến 1.8 lần" (revenue up to +30%, AOV up to 1.8×) [S30]
  - Brief: customers "delay, abandon, or seek credit outside the formal banking system" [Brief p.4]

Workaround being used: Host several finco/BNPL partners at checkout; retailer-branded pay-later.
  Thế Giới Di Động said in Nov 2024 that its "mua trả chậm" would "thay thế hoàn toàn chính sách
  'mua trả góp'" (fully replace instalment purchase) [S29][S28].
Unmet need: A high approval rate among shoppers who could responsibly afford it, decided in-session,
  without sending the customer away.
```

```
Pain Point #16: In-store financing friction and loss of control over the offer
Segments: MERCHANT
Frequency: Low | Severity: Medium | Opportunity: 2 | Confidence: Hypothesis (retailer press only)
Category: Workflow / Integration
Rubric: [R3 Concept]

Evidence:
  - "Previously many lending units required household registration and utility bills, but now
    approximately 80% of applications don't need these procedures. Review time has decreased from
    3–4 hours to just 15–20 minutes" — retailer statement, Thanh Niên, 18 Nov 2017 [S27]
  - The largest electronics chain moved to its own 0%, no-fee pay-later format in Nov 2024 [S28][S29]
    (motive not stated with data; our inference that it is about control is unverified)

Unmet need (hypothesis): Simple integration, a predictable approval experience and co-branded
  control over terms. Needs merchant interviews → OQ-03-05.
```

---

## 2. Behavioural Signal Log

```
B1 — Behaviour: When formal credit is slow or needs paperwork, people take fast app credit or black credit instead of waiting
Signal: Speed and low documentation beat price at the moment of need. The competitor to beat is
  the illegal app as well as other banks. [R1 Diagnosis]
Segments: FTB, GIG, MSME
Sources: [S47] (police: borrowers "do not want to sign in loan procedures at banks"), [S48], [S14], [Brief p.4]
```

```
B2 — Behaviour: Gig workers accept platform-embedded loans repaid by daily wallet auto-deduction, even at 20–30%/yr
Signal: Platform income is already treated as underwritable by fintech partners, and
  auto-deduction is an accepted repayment rail. Access matters more than price. [R3 Concept]
Segments: GIG
Sources: [S1], [S6]
```

```
B3 — Behaviour: Mid-ticket consumer electronics are bought on instalment by default
Signal: The brief's 1.6M–90.1M VND range is exactly where instalment use is highest (61% in the
  4.5–10M phone band, 2017 data). Demand exists; the constraint is the decision. [R2 Justification]
Segments: SAL-C, SAL-N, FTB; MERCHANT
Sources: [S27], [S19] (87% of Vietnamese surveyed used an instalment plan in the last 12 months — Visa/Neurosensum, Sep 2022)
```

```
B4 — Behaviour: Strong preference for 0%, no-fee framing; credit cards are the preferred instalment rail among card-holders
Signal: Price transparency and 0%-type offers anchor expectations, so merchant subsidy (MDR) is
  probably the realistic pricing model at checkout. To be validated in Skill 09. [R4 Assumptions]
Segments: SAL-C (card holders), FTB
Sources: [S19] (drivers: "affordable monthly payment options", "no or low interest fees", "easy setup"), [S28], [S30]
```

```
B5 — Behaviour: Rapid BNPL adoption, especially among 25–35-year-olds
Signal: The market has learned in-journey, app-based credit. Branch-style flows now look out of
  step. [R1 Diagnosis]
Segments: FTB, SAL-N
Sources: [S20] (BNPL 17% in 2022 → 49% in 2025 among surveyed online shoppers — TGM Research via 2C2P), [S18] (BNPL 16% share of consumer credit)
```

```
B6 — Behaviour: People resist handing over contracts and payslips (31–33%) but will hand over an ID and phone number to a BNPL app
Signal: Friction comes from heavy document requests, not from data sharing as such. A minimal-input
  flow backed by consented data pulls is acceptable if trusted. [R3 Concept]
Segments: SAL-N, GIG, FTB
Sources: [S18], [S30], [S22]
```

```
B7 — Behaviour: Repeated retries at eKYC, then public complaint or abandonment; splitting transactions to avoid biometric checks
Signal: Every extra verification step leaks users; fallbacks are needed. [R2 Justification]
Segments: all
Sources: [S22], [S44], [S24]
```

```
B8 — Behaviour: Borrowers hide debts from family; non-borrowers listed as references get harassed
Signal: Shame and fear of third-party contact are strong. A lender whose conduct is trusted
  (bank-regulated, no third-party contact) has a differentiation angle (hypothesis). [R3 Concept]
Segments: FTB, GIG
Sources: [S14], [S11], [S12], [S13]
```

```
B9 — Behaviour: After one loan, customers get cross-sell calls from several lenders and block numbers
Signal: The data-sharing implicit in consumer finance is resented. Consent must be narrow and
  visible. [R4 Assumptions]
Segments: SAL-C, SAL-N
Sources: [S9], [S42]
```

```
B10 — Behaviour: Sellers need working capital within days, timed to stock opportunities
Signal: For MSMEs, timing is worth more than rate. A few days' delay means a lost sale. [R1 Diagnosis]
Segments: MSME
Sources: [S40], [S48]
```

```
B11 — Behaviour: Household businesses mix personal and business cash flow across transfers, QR and platform payouts
Signal: The "observable cash flow" in the brief is fragmented. CASA consolidation and the 2026
  bank-account registration rule for household businesses (from 5 Mar 2026) are pushing
  separation, which creates a data opportunity. [R3 Concept]
Segments: MSME
Sources: [S39], [S38], [Brief p.8]
```

```
B12 — Behaviour: Many borrowers don't understand the consequences of missed payments
Signal: 46% don't know late payment affects their credit rating. Disclosure and nudges are part
  of responsible design, not decoration. [R2 Justification]
Segments: FTB, SAL-N
Sources: [S18], [S21]
```

```
B13 — Behaviour: Platform work is a main income earned through long hours, with little formal protection
Signal: Income is regular in *activity* but volatile in *amount*. Scoring should weight tenure
  and consistency over any single month. [R3 Concept]
Segments: GIG
Sources: [S3], [S4], [S5]
```

```
B14 — Behaviour: Lenders that already decide in minutes use tiered routing (auto-approve clear cases, human review for the rest)
Signal: Proven operating pattern in Vietnam (Home Credit 2016; ACB 2025), which supports the
  "refer band" design in Skill 01 Pillar 2. [R2 Justification]
Segments: OFFICER
Sources: [S46], [S36], [S37]
```

---

## 3. Unmet Needs Matrix

Opportunity Score = Frequency × Severity (High = 3, Medium = 2, Low = 1). No invented importance/satisfaction figures.

| Unmet Need | Segments | Frequency | Severity | Current Workaround | Opportunity Score |
|-----------|---------|-----------|----------|-------------------|------------------|
| Decision inside the purchase/stock moment (minutes, in-journey) | All; MSME, MERCHANT most | High | High | Finco counter at store; fast/illegal apps; delay or abandon | **9** |
| Be assessed on the income they actually receive (platform payouts, sales, e-wallet, salary inflows) instead of payslips/contracts | GIG, MSME, SAL-N, FTB | High | High | Platform-embedded loans; SEasy; in-person pleading; informal credit | **9** |
| Officer: standardised, consented thin-file evidence and a defensible policy to approve on it | OFFICER | High | High | Collateral demand; decline; calls to employers/references | **9** |
| Officer: human effort only on the uncertain band, with the file pre-assembled | OFFICER | High | High | Rule-based auto-approval (ACB); tiered referral (Home Credit) | **9** |
| Business cash flow recognised without financial statements or collateral | MSME | High | High | Sales-ledger apps; platform loans; pledging a house | **9** |
| One all-in cost up front, no forced add-ons | FTB, GIG, SAL | High | Medium | Choose 0% offers; complain to regulators | **6** |
| Lawful collection, no third-party contact | FTB, all consumers | Medium | High | Truecaller; hide debt; police reports | **6** |
| Repayment and affordability matched to volatile daily/weekly income | GIG, MSME | Medium | High | Wallet auto-deduction; working longer hours | **6** |
| Guardrails against over-borrowing and identity misuse for first-timers | FTB | Medium | High | None (loan stacking) | **6** |
| Officer: real-time ID and application-genuineness assurance | OFFICER | Medium | High | More paper and more manual checks | **6** |
| Merchant: high, instant, responsible approval at checkout | MERCHANT | Medium | High | Multiple finco/BNPL partners; own pay-later | **6** |
| Know why declined and what would change it | FTB, SAL-N, GIG | Medium | Medium | Try another app; reapply | **4** |
| eKYC that works first time on own phone | All | Medium | Medium | Retry; branch visit; split transactions | **4** |
| Narrow, visible consent and no marketing reuse of data | SAL-C, SAL-N, FTB | Medium | Medium | Block numbers; refuse documents | **4** |
| Merchant: simple integration and control over the offer | MERCHANT | Low | Medium | Own pay-later scheme | **2** |

**Read-across for Skill 04 (not a conclusion, just a pointer):** the four 9-scores sit on one mechanism. Every file gets the same document-and-bureau manual review, so thin-file income evidence is never used (borrower side) and the officer re-assembles every case by hand (operator side). This matches the Skill 01 working hypothesis. Skill 04 must test it against the alternative that identity/fraud (#14) or pricing is the binding constraint. `[R1 Diagnosis]`

---

## 4. User Vocabulary Log

```
Users say: "trả góp" (instalment) / "mua trả chậm", "mua trước trả sau" (pay later / BNPL) — not "point-of-purchase lending" or "embedded credit"
Examples: "Mua điện thoại trả góp chiếm hơn 50%" [S27]; MWG: "lấy xài liền, tiền trả sau" ("take it and use it now, pay later") [S29]; Fundiin: "Mua trước trả sau 0%" [S22]
```

```
Users say: "được duyệt" / "không được duyệt", "bị từ chối" (approved / not approved, rejected) — not "credit decision", "decline"
Examples: "Bắt chụp chứng minh thư xong kh đc duyệt hồ sơ" [S22]; "duyệt hồ sơ online nó gọi điện hỏi … công việc" (they review online and phone to ask about your job) [S8]
```

```
Users say: "chứng minh thu nhập", "bảng lương", "sao kê", "hợp đồng lao động" (prove income, payslip, bank statement, labour contract) — not "income verification", "affordability assessment"
Examples: "Không có bảng lương, người làm nghề tự do chứng minh thu nhập thế nào để vay vốn?" [search-level headline]; SEasy "không cần chứng minh thu nhập" [S41]; 31% / 33% won't provide contract/payslip [S18]
```

```
Users say: "hồ sơ" (the file/dossier — the paperwork bundle) — not "application data"
Examples: "Cầm hồ sơ đi vay…" (taking the dossier to borrow); "Chụp ảnh cmnd rồi gửi chục lần không được" [S22]; "xét duyệt hồ sơ trong 5s" [S30]
```

```
Users say: "CIC", "nợ xấu", "check CIC", "lịch sử tín dụng" (bad debt, credit history) — not "bureau file", "thin file"
Examples: lender content "Vay có kiểm tra lịch sử tín dụng không: Hiểu đúng CIC để không bị từ chối" [search-level, MAFC]; "lịch sử tín dụng xấu là một trong những nguyên nhân chính…" [S50]
```

```
Users say: "tín dụng đen", "app đen", "vay nóng", "vay qua app" (black credit, black app, hot/quick loan, app loan) — not "informal/unregulated credit"
Examples: "Lãi chồng lãi từ app đen thành cái bẫy 'nuốt chửng' sinh viên" [S16]; "Cho vay qua app bủa vây giới trẻ" [S14]; "Ngại thủ tục, dân tìm đến 'tín dụng đen'" [S48]
```

```
Users say: "khủng bố", "gọi điện liên tục", "đòi nợ", "tham chiếu" (terrorised, called non-stop, debt-chasing, reference contact) — not "collections contact strategy"
Examples: "Kinh nghiệm bị FE gọi điện khủng bố đòi nợ nhầm" [S11]; "nó gọi liên tục" [S11]; "Bị khủng bố đòi nợ vì làm 'tham chiếu' vay tiền" [Tuổi Trẻ headline, search-level]
```

```
Users say: "phí ẩn", "ép mua bảo hiểm", "lãi chồng lãi" (hidden fees, forced insurance, interest on interest) — not "total cost of credit", "APR"
Examples: "'Ép' mua bảo hiểm: Không mua, chấm dứt khoản vay" [S45]; "Tôi cảm thấy … cực kỳ tức giận" [S45]; borrowed 9M, repaid ~14M [S10]
```

```
Users say: "tài xế công nghệ", "shipper", "chạy xe", "ế khách", "chạy sấp mặt" (tech driver, courier, riding, no customers, ride till you drop) — not "gig/platform worker", "income volatility"
Examples: "Giờ ế lắm…" [S1]; "chạy sấp mặt cũng không đủ tiền trả nợ" [S1]; "Tài xế xe công nghệ 'bào sức' kiếm 15 triệu đồng/tháng" [S4]
```

```
Users say: "chủ shop", "hộ kinh doanh", "vốn chưa xoay kịp", "nhập hàng" (shop owner, household business, capital not turned around, buying stock) — not "MSME working-capital need"
Examples: "vốn chưa xoay kịp. Chỉ chậm vài ngày là hàng hết" [S40]; "Ngân hàng giúp hộ kinh doanh minh bạch dòng tiền, rộng cửa vay vốn" [S39]
```

```
Users say: "sinh trắc học", "không nhận ra khuôn mặt", "quét NFC", "căn cước" (biometrics, face not recognised, NFC scan, chip ID) — not "eKYC", "liveness"
Examples: "Không nhận ra khuôn mặt. Bạn hãy xác thực lại nhé" [S44]; "Thông tin giấy tờ không hợp lệ" [S44]
```

**Copy implications (for Skills 13/15):** say "được duyệt trong X phút" (approved in X minutes), "không cần chứng minh thu nhập / bảng lương" (no income proof / payslip needed — only if the data-based path allows it), "tổng số tiền phải trả" (total you will repay) shown up front, and "không gọi người thân" (we never call your relatives) as an explicit trust promise. These are hypotheses for testing, not validated claims.

---

## 5. Source Summary

| Item | Value |
|---|---|
| Sources cited in the Source Index | 50, plus the case brief |
| Date range | Oct 2016 – Sep 2026. Most evidence is 2022–2026. Dated items (2016–2020) are flagged where used. |
| Geography | Vietnam: 45 sources. Benchmarks from outside Vietnam: Signicat (Europe), Baymard (US reasons), Stripe (global), CGAP/IDinsight (LMIC/global). |

**Breakdown by source type**

| Source type | Count | Examples |
|---|---|---|
| Vietnamese press (news reporting with named borrower/seller/banker voices) | 25 | Tuổi Trẻ, Thanh Niên, Tiền Phong, VTV, VnEconomy, SGGP, Công Thương, VIR, Vietnam News |
| Community forums | 4 | voz.vn ×3, tinhte.vn |
| App-store reviews | 2 | Fundiin iOS, Home Credit iOS |
| Surveys / research reports | 8 | Decision Lab × Home Credit (n=1,200); World Bank platform-worker survey (n≈5,000, via SGGP); Visa/Neurosensum; TGM via 2C2P; Vero × Kadence; Signicat; Baymard; Stripe |
| Academic studies | 2 | BJBS 2025; Tạp chí Kinh tế Tài chính 2026 |
| NGO / development research | 3 | CGAP, IDinsight, Fairwork |
| Regulator / official reporting | 2 | SBV inspection (via PLO), MOIT complaint statistics (via CafeF) |
| Vendor / lender / platform content (claims, low authority) | 4 | Fundiin merchant page, Shopee SEasy (via BigSeller), Home Credit blog, Duy Tân University curriculum |

**Coverage by segment:**
- **Strong:** GIG, FTB, OFFICER (operational and fraud side).
- **Adequate:** SAL-N, MSME. For MSME the evidence is mostly press and banker statements; direct online-seller voices are thin.
- **Thin:** SAL-C (few pains specific to them beyond speed, cross-sell and insurance) and MERCHANT (the Vietnam leg relies on 2017 retailer data and vendor claims).

**Limitations:**
1. Reddit (r/VietNam) could not be reached: the search tool doesn't index it and direct fetch is blocked. Facebook groups are not publicly fetchable.
2. Google Play review pages did not render. The Home Credit and Kredivo Play-store complaints seen in search snippets are **not** used as evidence.
3. Several quotes reached us through a page-summarising fetch. They are reproduced as extracted, and any that could not be confirmed as verbatim are marked *(paraphrase)* or "search-level".
4. No first-hand credit-officer testimony (see OQ-03-03).

---

## Open Items Raised by This Skill

| # | Item | Why it matters | Suggested validation / owner | Carry to |
|---|---|---|---|---|
| OQ-03-01 | HLB pilot funnel by segment is unknown: applications started vs. submitted vs. approved vs. disbursed, and where in the document step people drop out. | Needed to size the abandonment and in-moment effect claimed in R-2/R-3; external benchmarks (Signicat 68%, Baymard) are foreign. | State as an explicit assumption in the submission, or ask the case organisers. `[R4 Assumptions]` | Skill 04 / 10 / 22 |
| OQ-03-02 | Direct voices of Vietnamese **online sellers** declined for credit (Shopee/TikTok/Facebook) are thin. The MSME evidence is mainly press and bankers. | The MSME persona in Skill 05 risks being built on bank-side views. | 5–10 seller interviews or a short survey through HLB's Sổ Bán Hàng partner, or mark the persona "medium confidence". | Skill 05 / 22 |
| OQ-03-03 | No public first-hand account of a Vietnamese credit officer's time per file (document checks vs CIC lookup vs calls vs write-up), or of how they decide thin-file cases. | The 380,000 VND cost breakdown drives how much a tiered flow saves (links to OQ-07). | Ask HLB/organisers, or state an assumed cost split with a sensitivity range. `[R4 Assumptions]` | Skill 04 / 22 |
| OQ-03-04 | Willingness of gig workers and sellers to **consent** to banks reading platform-income, e-wallet or telco data has not been measured in Vietnam. Only general privacy concern (49.5%) and document reluctance (31–33%) are evidenced. | Alternative-data scoring (R-1) fails if consent uptake is low. | Literature review and Skill 02 (consent/PDP law); consider a consent-rate assumption with a range. | Lit review / Skill 13 / 22 |
| OQ-03-05 | Independent, recent Vietnamese data on checkout-financing conversion, AOV and abandonment is missing. Local figures are vendor claims (Fundiin) or 2017 retailer data; strong data is foreign (Stripe, Baymard). | Needed for the merchant-side business case (R-2) and the merchant persona decision (OQ-06). | Treat uplift as an assumption with the source stated; merchant interviews if time allows. | Skill 05 / 09 / 22 |
| OQ-03-06 | Responsible-lending guardrails for FTB and GIG (limit sizing, loan-stacking checks, cooling-off, collection conduct) are a user need, but the policy choice belongs to HLB/regulation. | Speed without guardrails recreates the harms users describe (Pains #8, #11). | `[DECISION REQUIRED]` — surface in Skill 22; check SBV rules in Skill 02. | Skill 02 / 22 |
| OQ-03-07 | Bureau-coverage figure is dated (CIC ">60% of adults", Apr 2022). Same issue as OQ-08 from Skill 01. | Sizes the no-credit-history problem. | Skill 02 to find a 2025–2026 CIC/PCB figure. | Skill 02 |
| OQ-03-08 | Hypothesis: HLB's bank brand and conduct ("no third-party calls, no forced insurance, all-in price") is a differentiator against fincos and apps. No direct evidence yet on bank-vs-finco trust for *borrowing* (Vero trust score is for online banking in general). | Shapes positioning and whether trust can offset slower speed. | Skill 05 persona evidence; mark as assumption if untested. | Skill 05 / 12 |
| OQ-03-09 | No Vietnam-specific eKYC/biometric failure or drop-off rate found. The evidence is anecdotal (Tuổi Trẻ 2024) plus Europe's Signicat. | R-3 claims "without document friction", so a baseline is needed for the KPI. | State an assumed baseline in Skill 10, or source it from an eKYC vendor or SBV. | Skill 10 / 22 |
| OQ-03-10 | Loan purpose for the **MSME** segment is unclear: in the brief's point-of-purchase product, are sellers financing personal purchases or business inputs (stock, equipment)? The research shows MSME pain is mostly working capital timed to stock. | Changes product fit, ticket size and the data used (sales flows vs personal inflows). | Ask the user / check the brief again. `[DECISION REQUIRED]` | Skill 04 / 22 |

---

## Source Index

| # | Source (publisher, title, date) | URL | Used for |
|---|---|---|---|
| Brief | Business Challenge 2026 Ideation Round Case (HLB Vietnam), distilled in `_requirements.md` | local file | Segment behaviour, baseline, requirements |
| S1 | Tuổi Trẻ, "Tài xế xe công nghệ ám ảnh với tiền trả góp", 4 Aug 2023 | https://tuoitre.vn/tai-xe-xe-cong-nghe-am-anh-voi-tien-tra-gop-2023080322454538.htm | GIG quotes, wallet-deducted loans, rates |
| S2 | Tuổi Trẻ, "Tài xế xe công nghệ: ám ảnh tiền góp!", 14 Apr 2020 | https://tuoitre.vn/tai-xe-xe-cong-nghe-am-anh-tien-gop-20200414105843052.htm | GIG income shock |
| S3 | SGGP, "Khoảng 20% lao động nền tảng số tham gia bảo hiểm xã hội" (World Bank survey), 22 Sep 2026 | https://www.sggp.org.vn/khoang-20-lao-dong-nen-tang-so-tham-gia-bao-hiem-xa-hoi-post872891.html | GIG main-income / social-protection stats |
| S4 | Thanh Niên, "Tài xế xe công nghệ 'bào sức' kiếm 15 triệu đồng/tháng…", 16 Sep 2026 | https://thanhnien.vn/tai-xe-xe-cong-nghe-bao-suc-kiem-15-trieu-dong-thang-mong-muon-lon-nhat-la-gi-185260916123849823.htm | GIG hours / income survey (n=141) |
| S5 | Fairwork, "Fairwork Vietnam Ratings 2023: Labour Standards in the Platform Economy" | https://fair.work/en/fw/publications/fairwork-vietnam-ratings-2023-labour-standards-in-the-platform-economy/ | GIG pay after costs unevidenced |
| S6 | CGAP, "Putting Gig Data to Work: Innovations in Expanding Credit Access", Jun 2023 | https://www.cgap.org/research/publication/putting-gig-data-to-work-innovations-in-expanding-credit-access | Gig income-documentation barrier; platform-data lending |
| S7 | IDinsight, "Digital gig work and financial inclusion: Examination of links, promises, and early evidence", 12 Dec 2023 | https://www.idinsight.org/publication/digital-gig-work-and-financial-inclusion-examination-of-links-promises-and-early-evidence/ | LMIC context: "less than a sixth" of sample report access to loan/insurance products (used as background only) |
| S8 | voz.vn, "Hỏi vay Homecredit cho freelancer" | https://voz.vn/t/hoi-vay-homecredit-cho-freelancer.1059161/ | Freelancer income proof, phone verification, cost |
| S9 | voz.vn, "Khuyên ae tránh xa thằng Home Credit", Jul 2022 | https://voz.vn/t/khuyen-ae-tranh-xa-thang-home-credit.576641/ | Cross-sell calls, data sharing |
| S10 | voz.vn, "Thòng lọng vay nhanh trả góp trực tuyến" | https://voz.vn/t/thong-long-vay-nhanh-tra-gop-truc-tuyen.1089609/ | All-in cost shock (9M → ~14M) |
| S11 | tinhte.vn, "Kinh nghiệm bị FE gọi điện khủng bố đòi nợ nhầm", 1 Feb 2020 | https://tinhte.vn/thread/kinh-nghiem-bi-fe-goi-dien-khung-bo-doi-no-nham.3076569/ | Collection harassment |
| S12 | Tuổi Trẻ / PLO, "FE Credit bị thanh tra vì nghi đe dọa người tiêu dùng", 14 May 2018 | https://tuoitre.vn/plo/fe-credit-bi-thanh-tra-vi-nghi-de-doa-nguoi-tieu-dung-post482210.html | Regulator inspection; reference harassment |
| S13 | CafeF, "Cho vay tiêu dùng bị phản ánh, khiếu nại nhiều nhất" (MOIT data), 1 May 2020 | https://cafef.vn/cho-vay-tieu-dung-bi-phan-anh-khieu-nai-nhieu-nhat-20200501152617947.chn | 21.8% complaint share |
| S14 | Tiền Phong, "Cho vay qua app bủa vây giới trẻ – Bài 1: Khốn đốn vay nợ qua iCloud", 23 Apr 2025 | https://tienphong.vn/cho-vay-qua-app-bua-vay-gioi-tre-bai-1-khon-don-vay-no-qua-icloud-post1735768.tpo | FTB quotes, reasons, iCloud threats |
| S15 | Tiền Phong, "Cho vay qua app bủa vây giới trẻ – Bài 2: Vượt qua sợ hãi", 23 Apr 2025 | https://tienphong.vn/cho-vay-qua-app-bua-vay-gioi-tre-bai-2-vuot-qua-so-hai-post1736059.tpo | FTB regret quotes; actual vs contract rate |
| S16 | Tuổi Trẻ, "Lãi chồng lãi từ app đen thành cái bẫy 'nuốt chửng' sinh viên", May 2026 | https://tuoitre.vn/lai-chong-lai-tu-app-den-thanh-cai-bay-nuot-chung-sinh-vien-20260502220952942.htm | Loan stacking, debt spiral |
| S17 | Tuổi Trẻ, "Sinh viên bỗng dưng thành con nợ của app vay tiền online", 18 Nov 2025 | https://tuoitre.vn/sinh-vien-bong-dung-thanh-con-no-cua-app-vay-tien-online-20251118145608691.htm | Biometric-harvesting loan fraud |
| S18 | Decision Lab (with Home Credit Vietnam), "New report highlights need for more accessible credit and better financial literacy" (fieldwork 1–7 Dec 2023, n=1,200) | https://www.decisionlab.co/blog/new-report-highlights-need-for-more-accessible-credit-and-better-financial-literacy | Document reluctance 31/33%; repayment strain; literacy gaps |
| S19 | Visa Vietnam, "Visa spearheads financial inclusion in Vietnam with Visa Installment Solutions" (Neurosensum study, Sep 2022) | https://www.visa.com.vn/en_VN/about-visa/newsroom/press-releases/visa-spearheads-financial-inclusion-in-vietnam-with-visa-installment-solutions.html | 87% used instalments; drivers of use |
| S20 | 2C2P, "Popular Payment Methods in Vietnam" (citing TGM Research 2025) | https://2c2p.com/articles/vietnam-payment-methods/ | BNPL adoption 17% → 49% |
| S21 | British Journal of Business Sciences, "Psychological Mechanisms and Debt Risk in BNPL Use: Evidence from University Students in Vietnam", 2025 | https://metaopensciences.com/index.php/bjbs/article/view/18 | FoMO → impulsive BNPL → debt risk |
| S22 | Apple App Store (VN), Fundiin — ratings & reviews | https://apps.apple.com/vn/app/fundiin-mua-tr%C6%B0%E1%BB%9Bc-tr%E1%BA%A3-sau-0/id1488771737?see-all=reviews&platform=iphone | Opaque rejection; eKYC retries |
| S23 | Apple App Store (VN), Home Credit Tài Chính Online — reviews | https://apps.apple.com/vn/app/home-credit-t%C3%A0i-ch%C3%ADnh-online/id1553761073 | Early-settlement fee surprise; denial |
| S24 | Signicat, "The Battle to Onboard 2022" press release (Sapio, n=7,600, Europe), 30 Mar 2022 | https://www.signicat.com/press-releases/the-battle-to-onboard-2022 | Abandonment benchmarks; data concern (foreign) |
| S25 | Baymard Institute, "Cart Abandonment Rate Statistics" | https://baymard.com/lists/cart-abandonment-rate | Abandonment rate and reasons (US reasons) |
| S26 | Stripe, "Testing the impact of buy now, pay later across 150,000+ checkout sessions", Jun 2024 | https://stripe.com/blog/testing-the-impact-of-buy-now-pay-later | Merchant revenue/conversion uplift (global) |
| S27 | Thanh Niên, "Mua điện thoại trả góp chiếm hơn 50%", 18 Nov 2017 | https://thanhnien.vn/mua-dien-thoai-tra-gop-chiem-hon-50-185711636.htm | Instalment share by price band; store review time (dated) |
| S28 | Thanh Niên, "Thế Giới Di Động lần đầu tiên giới thiệu chính sách mua trả chậm", 11 Nov 2024 | https://thanhnien.vn/the-gioi-di-dong-lan-dau-tien-gioi-thieu-chinh-sach-mua-tra-cham-185241111165807144.htm | 0%, no-hidden-fee pay-later |
| S29 | Thị trường Tài chính Tiền tệ, "…Thế Giới Di Động tung chiến lược mới thay thế 'mua trả góp' bằng trả chậm", Nov 2024 | https://thitruongtaichinhtiente.vn/sau-gia-re-qua-the-gioi-di-dong-tung-chien-luoc-moi-thay-the-mua-tra-gop-bang-tra-cham-63906.html | Retailer replacing instalment scheme |
| S30 | Sapo App Market, Fundiin listing (vendor claims) | https://apps.sapo.vn/fundiin | Merchant uplift claims; 5-second approval claim |
| S31 | Tuổi Trẻ, "Làm giả gần 500 thông tin khách hàng để lừa lấy bạc tỉ của công ty tài chính", 23 Aug 2021 | https://tuoitre.vn/lam-gia-gan-500-thong-tin-khach-hang-de-lua-lay-bac-ti-cua-cong-ty-tai-chinh-20210823153801143.htm | 607 forged applications; ID not verifiable |
| S32 | Tuổi Trẻ, "Làm giả hồ sơ vay để chiếm đoạt 48 tỉ đồng, nhân viên ngân hàng lãnh án tù chung thân", 18 Apr 2025 | https://tuoitre.vn/lam-gia-ho-so-vay-de-chiem-doat-48-ti-dong-nhan-vien-ngan-hang-lanh-an-tu-chung-than-20250418165228472.htm | Insider document fraud |
| S33 | Báo Tin Tức, "Thách thức khi sử dụng công nghệ số hỗ trợ chấm điểm tín dụng", 12 May 2024 | https://baotintuc.vn/tai-chinh-ngan-hang/thach-thuc-khi-su-dung-cong-nghe-so-ho-tro-cham-diem-tin-dung-20240512202018285.htm | CIC coverage gap; data-security risk |
| S34 | VnEconomy, "Thị trường tài chính tiêu dùng chờ cú hích từ chấm điểm tín dụng", 18 Jun 2024 | https://vneconomy.vn/thi-truong-tai-chinh-tieu-dung-cho-cu-hich-tu-cham-diem-tin-dung.htm | Finco CEOs on no-history customers |
| S35 | VnEconomy, "Độ phủ thông tin tín dụng Việt Nam đạt trên 48,6 triệu chủ thể, chiếm hơn 60% người trưởng thành", 7 Apr 2022 | https://vneconomy.vn/do-phu-thong-tin-tin-dung-viet-nam-dat-tren-48-6-trieu-chu-the-chiem-hon-60-nguoi-truong-thanh.htm | Bureau coverage (dated) |
| S36 | DNSE Senses (republishing VietnamFinance), "Duyệt hồ sơ 10 phút thay vì 10 ngày…", 10 Sep 2025 | https://www.dnse.com.vn/senses/tin-tuc/duyet-ho-so-10-phut-thay-vi-10-ngay-ai-cuop-viec-lam-cua-nhan-vien-ngan-hang-35125165 | ACB 60% auto-approval; 10 days → minutes |
| S37 | tincongnghe.net, "Một ngân hàng rút ngắn thời gian lập hồ sơ thẩm định từ 5 ngày xuống nửa ngày nhờ AI", May 2026 | https://www.tincongnghe.net/t-163224/mot-ngan-hang-rut-ngan-thoi-gian-lap-ho-so-tham-dinh-tu-5-ngay-xuong-nua-ngay-nho-ai.html | VietinBank appraisal-file preparation time |
| S38 | VTV, "Minh bạch thông tin, khơi thông vốn vay", 25 Sep 2026 | https://vtv.vn/minh-bach-thong-tin-khoi-thong-von-vay-100260924223805778.htm | SME loan-access stats; banker quotes; SBV cash-flow pilot |
| S39 | Tuổi Trẻ, "Ngân hàng giúp hộ kinh doanh minh bạch dòng tiền, rộng cửa vay vốn", 30 Mar 2026 | https://tuoitre.vn/ngan-hang-giup-ho-kinh-doanh-minh-bach-dong-tien-rong-cua-vay-von-20260330202644202.htm | Mixed cash flows; account-registration rule |
| S40 | Công Thương, "Tiếp sức hộ kinh doanh: Không để lỡ thời điểm kinh doanh vì thiếu vốn", 27 Jul 2026 | https://congthuong.vn/tiep-suc-ho-kinh-doanh-khong-de-lo-thoi-diem-kinh-doanh-vi-thieu-von-466907.html | Seller timing quote |
| S41 | BigSeller, "Tổng quan khoản vay SEasy Cho Vay Người Bán", 27 Oct 2025 (summarising Shopee seller-centre terms) | https://www.bigseller.com/blog/articleDetails/3931/seasy-cho-vay-nguoi-ban.htm | Platform seller-loan workaround |
| S42 | Tuổi Trẻ, "Người Việt tin vào ngân hàng số nhưng lo ngại lừa đảo" (Vero × Kadence), 4 Aug 2026 | https://tuoitre.vn/nguoi-viet-tin-vao-ngan-hang-so-nhung-lo-ngai-lua-dao-100260804164402705.htm | Trust and data-misuse concern |
| S43 | Tạp chí Kinh tế Tài chính, "Tác động của nhận thức bảo mật đến sự sẵn sàng chia sẻ thông tin trong ngân hàng mở: Bằng chứng từ sinh viên Hà Nội", 9 Jun 2026 | https://nghiencuu.tapchikinhtetaichinh.vn/tac-dong-cua-nhan-thuc-bao-mat-den-su-san-sang-chia-se-thong-tin-trong-ngan-hang-mo-bang-chung-tu-sinh-vien-ha-noi-158389.html | Privacy vs data sharing (n=417) |
| S44 | Tuổi Trẻ, "Cập nhật sinh trắc học xong app vẫn không nhận ra khuôn mặt để chuyển khoản", 3 Jul 2024 | https://tuoitre.vn/cap-nhat-sinh-trac-hoc-xong-app-van-khong-nhan-ra-khuon-mat-de-chuyen-khoan-20240703073736986.htm | eKYC/biometric failures |
| S45 | Tuổi Trẻ, "'Ép' mua bảo hiểm: Không mua, chấm dứt khoản vay", 13 Feb 2023 | https://tuoitre.vn/ep-mua-bao-hiem-khong-mua-cham-dut-khoan-vay-20230213083113418.htm | Forced insurance quotes |
| S46 | Vietnam Investment Review, "Home Credit advances with digital technology", 17 Oct 2016 | https://vir.com.vn/home-credit-advances-with-digital-technology-45277.html | POS decision in ~5 min; tiered human verification |
| S47 | Vietnam News, "Money borrowers face black credit traps on online lending apps", 8 Dec 2022 | https://vietnamnews.vn/society/1402303/money-borrowers-face-black-credit-traps-on-online-lending-apps.html | Why borrowers choose apps; victim profile |
| S48 | Kinh tế Chứng khoán, "Ngại thủ tục, dân tìm đến 'tín dụng đen'", 30 Jan 2016 | https://kinhtechungkhoan.vn/ngai-thu-tuc-dan-tim-den-tin-dung-den-1071653.html | Urgency/collateral/procedure → black credit (dated) |
| S49 | Duy Tân University (Faculty learning material), "Nội dung thẩm định cho vay tiêu dùng" | https://kkttc.duytan.edu.vn/goc-hoc-tap/noi-dung-tham-dinh-cho-vay-tieu-dung-ite8w | Manual appraisal steps (employer checks, home interviews) |
| S50 | Home PayLater (Home Credit) blog, "Bị từ chối mua điện thoại trả góp? Lý do và giải pháp", 3 Jun 2024 | https://www.homepaylater.vn/blog/mua-dien-thoai-tra-gop-bi-tu-choi/ | Generic rejection reasons; documents requested |

*Items marked "search-level" in the text (for example the "Không có bảng lương…" headline and the "Bị khủng bố đòi nợ vì làm 'tham chiếu'" headline) are search-result titles used only as vocabulary evidence. They are not counted toward any pain point's confidence level.*
