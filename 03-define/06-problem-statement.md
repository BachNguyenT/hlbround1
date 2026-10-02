# Problem Statement
**Product:** HLB Point-of-Purchase Credit Decisioning (working name) | **Skill:** 06 | **Status:** Done (2026-09-30)
**Depends on:** persona-research.md, problem-research.md | **Feeds into:** problem-solution-fit.md

> Rubric tags: `[R1]` Diagnosis · `[R2]` Justification · `[R3]` Concept · `[R4]` Assumptions. Upstream citations: `[Brief p.N]`, `[Skill0N §/S#]`, `[L#]` (literature review). New sources for this skill: `[P#]` (Source Index at end).
>
> **Solution direction note.** On 2026-09-30 the user specified the solution mechanism: a central AI credit-profile engine whose output every stage of a six-stage PoP journey uses (`_context.md` §Solution Direction, SD-1 to SD-7). A problem statement must not presuppose its solution, so the statements below are written to be answerable by that engine without naming it. They are also framed to cover the full customer lifecycle the user asked for, not only the approval moment.

---

## Pre-filled Context
- **Persona:** the recommended primary is **Anh Hùng, a gig/platform worker (GIG, 15.0% of inbound)**; the strongest secondary is **Chị Phụng, an online MSME seller (14.5%)**. The internal operator is **Chị Lan, the HLB credit-risk/underwriting lead** [Skill05]. For the Awareness and Retention stages, the "customer" is also any **existing HLB customer who has never used PoP credit**. The brief describes salaried account-holders as "most visible" through existing account data [Brief p.7].
- **Root cause (Skill 04 §4, High confidence on mechanism):** one undifferentiated, manual, document-and-bureau workflow is applied to every application, whatever the segment, ticket size or available signal. It was inherited unchanged from branch lending.
- **Problem candidates (Skill 04 §8):**
  1. Process/root-cause framing (recommended)
  2. Persona/inclusion framing
  3. Unit-economics framing

  All three are carried into the HMW set below.
- **Lifecycle gaps the user identified (2026-09-30):** HLB cannot yet:
  - find never-used, low-risk, high-potential customers to market to
  - tailor packages to a customer's financial profile
  - refine a customer's profile from repayment behaviour
  - recommend suitable PoP-purchasable products to repeat users
  - grow through loyal customers

  The brief supports the first gap directly: "identify which customers are most likely to need, accept, and responsibly use embedded credit, so that offers are targeted and relevant rather than broadcast to all" [Brief p.6]. The brief describes no mechanism for the second to fifth gaps. They are therefore recorded as **user-identified gaps** (see Assumption Map A11–A13), not as brief-evidenced facts.

---

## 1. HMW Statements

Method: IDEO's "How Might We" reframes insight statements as questions. Each question should be broad enough to allow several solutions but not so broad that it gives no direction [P1][P2].

**Five variations generated:**

| # | How Might We… | Primary source of insight |
|---|---|---|
| HMW-A | …give Vietnamese shoppers a **fair, right-sized credit offer at the moment they want to buy**, based on what HLB and its partners already know about them, and **keep improving that offer as they repay**? This includes shoppers without a bureau file. | Skill 04 core bottleneck, plus the user's lifecycle gaps |
| HMW-B | …decide a point-of-purchase application **within the checkout session**, for applicants whose income is visible in digital records but not in the bureau, **at a cost that fits a small ticket, without loosening risk quality**? | Skill 04 P1+P2 [Brief p.6 R-1, R-2] |
| HMW-C | …let a customer's **repayment behaviour continuously reshape** the limit, price and products they're offered? The aim is that good payers graduate while struggling payers are spotted and protected early. | User-identified gap (Decision/Repayment, Retention); Skill 03 Pain #10, #11 |
| HMW-D | …reach existing HLB customers who have **never used PoP credit but could use it responsibly**, with an offer they find relevant rather than a broadcast they resent? | [Brief p.6 R-4]; Skill 03 Pain #9 (cross-sell resentment), B9 |
| HMW-E | …let loyal customers **bring others into PoP lending** (friends or merchants) **without pushing debt onto friends or exposing anyone to "reference" harassment**? | User-identified gap (Advocacy); Skill 03 Pain #8 (harassment of reference contacts) |

**Ranking (Skill 06 criteria: specificity, actionability, user-centricity, evidence strength):**

| # | Specificity | Actionability | User-centricity | Evidence strength | Rank |
|---|---|---|---|---|---|
| HMW-A | Medium-High: covers the whole journey but is anchored on "the moment they want to buy" | High | High | High: Skills 03–05 Confirmed pains for the in-moment core; lifecycle parts are user-identified | **1** |
| HMW-B | High | High | Medium: phrased partly from the bank's side (cost, risk) | High: Skill 04 Opportunity 9 for P1 and P2 | **2** |
| HMW-C | High | High | High | Medium: grounded in Pains #10/#11; the lifecycle mechanism itself is user-identified | **3** |
| HMW-D | High | High | Medium | Medium: the brief explicitly requires it (R-4); the positive-targeting upside is a hypothesis [Skill04 §6 P4] | 4 |
| HMW-E | Medium | Medium | High | Low-Medium: harms are Confirmed (Pain #8); no evidence yet that a safe referral design works | 5 |

**Top 3:**
- **HMW 1 (HMW-A):** How might we give Vietnamese shoppers a fair, right-sized credit offer at the moment they want to buy, based on what HLB and its partners already know about them, and keep improving that offer as they repay? This includes shoppers without a bureau file.
- **HMW 2 (HMW-B):** How might we decide a point-of-purchase application within the checkout session, for applicants whose income is visible in digital records but not in the bureau, at a cost that fits a small ticket, without loosening risk quality?
- **HMW 3 (HMW-C):** How might we let a customer's repayment behaviour continuously reshape the limit, price and products they're offered, so good payers graduate and struggling payers are protected early?

**Chosen HMW:** HMW 1 (HMW-A).

**Rationale:** It is the only framing that covers both what the case brief scores and what the user asked for.
- **Brief coverage.** "At the moment they want to buy", "without a bureau file" and "fair, right-sized" map directly onto the core bottleneck and requirements R-1 and R-2. "Fair and transparent cost" is also the customer need stated in the brief [Brief p.4].
- **User's lifecycle coverage.**
  - "Based on what HLB and its partners already know about them" covers Awareness and Consideration.
  - "Keep improving that offer as they repay" covers Purchase & Repayment and Retention.
- **Stays solution-neutral.** It does not name an AI engine, so the Skill 04 §5 non-AI test remains a live check on each component.

HMW 2 is retained as the **diagnostic core**. It is the sentence to lead with under rubric R1, "precisely isolate the core bottleneck" [Brief p.11]. HMW-D and HMW-E are not discarded. They become stage-level design questions in Skill 13 for Awareness and Advocacy respectively. `[R1]`

---

## 2. POV Statement

**Primary POV (borrower: GIG):**
> **Anh Hùng, a ride-hailing driver paid daily through a platform app, needs a way to get a right-sized instalment offer inside the checkout he is already standing in, because his income is recorded every day in platform and e-wallet records, yet the only review that decides his application reads payslips and bureau files he doesn't have, and it answers days after he has left the store.**

*Insight, the non-obvious part:* Anh Hùng is not unscoreable. He is **scored on the wrong evidence at the wrong time**. The signals exist daily. The brief states that platform income and e-wallet activity "exist as records within the bank, its partners, or third parties, but a document-based review does not draw on them" [Brief p.5]. Because of this, the "easiest yes" available to him today comes from a wallet-deducted platform loan at roughly 20–30%/yr or an app lender, not from a bank [Skill03 S1, B1, B2]. `[R1]`

**Secondary POV (operator), included because the product is B2B2C and the operator is the second user of every decision:**
> **Chị Lan, an HLB credit-risk/underwriting lead, needs a way to approve thin-file applicants against a policy she can defend and to spend her team's time only on the genuinely uncertain cases, because today every file, from a 1.6M VND first purchase to a 90.1M VND salaried loan, arrives as the same manual, document-and-CIC case costing 380,000 VND, and the cash flow she can see is not something policy lets her act on.**
> [Brief pp.5, 8–9; Skill03 Pain #12, #13; Skill05 Persona 4]

**Supporting POV for the lifecycle stages (existing customer, Awareness/Retention):**
> **An existing HLB account-holder who has never used PoP credit needs to hear about it only when it's relevant and affordable for them, because blanket cross-sell calls are already a leading irritant in Vietnamese consumer finance, and people are protective of how their financial data is used.**
> [Skill03 Pain #9, S9; S42: 49.5% of Vietnamese users see personal-data misuse as their biggest worry]

---

## 3. Lean UX Problem Statement

Template per Gothelf's Lean UX [P3]:

> **HLB Vietnam's point-of-purchase lending was designed to achieve** a commercially viable business line. That line should extend responsible, in-moment instalment credit across five customer segments, including young professionals, gig workers and online merchants, while maintaining risk quality, responsible-lending standards and regulatory compliance [Brief p.6].
>
> **We have observed that it is not meeting these goals:**
> - Every application passes through a branch-designed, centralised manual review costing 380,000 VND and taking 1.8–4.6 business days, regardless of loan size [Brief pp.5, 9].
> - The three thin-file segments (44.0% of inbound) have the lowest approval rates despite visible digital income [Brief pp.7–9].
> - Offers are not targeted to the customers most likely to need, accept and responsibly use them [Brief p.6].
> - The brief describes no mechanism that updates a customer's offer from how they repay.
>
> **This is causing** customers to "delay the purchase, abandon it, or seek credit outside the formal banking system" [Brief p.4]. Short-tenure tickets below roughly 5–17M VND lose money on the review cost alone [Skill02 §2A].
>
> **How might we improve** HLB's point-of-purchase lending **so that customers are more successful, based on these measurable criteria:**
> - in-session decision rate (baseline 0%)
> - blended cost per decision (baseline 380,000 VND)
> - thin-file approval rate at a MOB3 30+ delinquency ceiling HLB sets
> - repeat-use and on-time-repayment rates among PoP customers
>
> (Baselines from [Brief p.9]. Targets are set in Skill 10.) `[R3]`

---

## 4. Assumption Map

Every assumption embedded in the chosen problem statement (HMW 1 / POVs / Lean UX). Confidence = how sure we are it is true. Risk = consequence if wrong.

| # | Assumption | Confidence | Risk if Wrong | Validation Method |
|---|---|---|---|---|
| A1 | Digital signals of thin-file applicants (platform income, e-wallet, telco, utility, sales ledger) predict repayment in Vietnam well enough to decide on | **Medium.** Strong foreign evidence [L1][L4][L9]; no Vietnam-specific study (OQ-LR-01) | **High.** R-1 collapses | Back-test on HLB/partner data; otherwise run a limited pilot with a randomised approval corridor [Skill02 lit. M6] |
| A2 | Applicants will **consent** to share platform, e-wallet, telco or sales data | **Low–Medium.** 31–33% resist documents, yet people give ID and phone to BNPL apps [Skill03 B6]; 49.5% name data misuse as their top worry [Skill03 S42]; consent rate unmeasured (OQ-03-04) | **High.** Thin-file profiles stay thin | Measure the consent-rate funnel in the pilot; A/B test consent copy |
| A3 | "The moment they want to buy" means a decision in **minutes** | **High.** Competitors take 5 s to about 30 min [Skill02 F1]; a European benchmark shows applicants abandoning after about 19 min [Skill03 S24] | Medium | Pilot funnel timing by step (OQ-03-01) |
| A4 | Cost per decision **can** be made proportional to ticket size by automating clear cases | **Medium.** Mechanism evidenced (ACB ~60% auto [Skill03 S36]); HLB's cost breakdown and refer-band size are unknown (OQ-04-02, OQ-04-03) | **High.** R-2 unit economics fail | Obtain HLB cost breakdown; back-test the refer-band share |
| A5 | HLB holds enough data on **existing** customers to identify never-used, low-risk, high-potential PoP targets | **Medium.** The brief says salaried account-holders are "most visible" [Brief p.7]; other segments have low visibility until they apply [Brief pp.7–8] | Medium. Awareness targeting would reach mainly salaried customers | Data audit of HLB CASA/card/loan/HLB Connect holdings (SD-7) |
| A6 | Declines of visibly-earning applicants are mainly a **process** gap, not a deliberate risk-appetite choice | **Medium** (OQ-04-04) | **High.** The fix would also need a credit-policy decision | Ask HLB or the case organisers; state as an assumption in the submission |
| A7 | Targeted offers raise acceptance **without** raising harm | **Medium.** Uplift targeting is supported [L62]; framing effects cut both ways [L64]; cautions X5/X6 [lit. review] | **High.** Responsible-lending breach, reputational damage | Uplift test with a suitability filter; track harm guardrails (Skill 10) |
| A8 | Merchants/partners will embed an HLB offer at checkout | **Medium.** Competitors are widely embedded [Skill02 §1]; Vietnam merchant fees are not public (OQ-02-12) | **High.** No distribution | Merchant interviews; check partner terms via Sổ Bán Hàng / Sales Ledger |
| A9 | The AI Law's human-oversight requirement can be met with a human "refer" band **without** losing in-moment speed for most cases | **Medium** (OQ-02-11) | **High.** Compliance or speed conflict | Legal reading of Law 134/2025 and Decision 33/2026; design the refer band as asynchronous with a provisional offer (Skill 13) |
| A10 | Using bank-held financial data for **marketing/targeting** is lawful with purpose-specific consent | **Medium.** PDPL requires per-purpose consent and treats financial data as sensitive [Skill02 RF-7] | **High.** Awareness and Retention features unlawful as designed | Legal review; design a separate marketing-consent purpose (Skill 13) |
| A11 | Repayment behaviour on PoP loans materially improves profile accuracy over time (**user-identified gap**) | **Medium.** Mechanism is standard in credit (behavioural scoring); verification pending the research notes for Skill 07 | Low–Medium | Track model lift from repayment features across MOB cohorts |
| A12 | Repeat PoP users want product recommendations and will act on them (**user-identified gap**) | **Low.** No Vietnam evidence; cross-sell resentment is evidenced (Pain #9) | Medium. Recommendations become spam | Opt-in recommendations; measure click-through and complaint rate |
| A13 | A referral mechanism can grow the base without encouraging irresponsible borrowing (**user-identified gap**) | **Low.** No direct evidence; reference-contact harassment is a Confirmed harm (Pain #8) | Medium | Reward only after the invitee repays on time; no reference contacts (SD-6); monitor invitee delinquency |

**Highest-risk, lowest-confidence assumptions to validate first:** A2 (consent), A1 (predictive power in Vietnam), A6 (process vs. risk appetite), A10 (lawful marketing use). Carried to Skill 07 and, if unresolved, to Skill 22. `[R4]`

---

## Open Items Raised by This Skill

| # | Item | Carry to |
|---|---|---|
| OQ-06-01 | The lifecycle gaps (profile refinement from repayment, product recommendations, referral) are **user-identified**, not stated in the brief. The submission should frame them as extensions that serve the brief's commercial-viability goal [Brief p.6], not as brief requirements. | Skill 08 / 22 |
| OQ-06-02 | The primary persona is still a Skill 05 recommendation (GIG). Skill 08 must ratify it or override it. | Skill 08 |

---

## Source Index

| # | Source | URL | Used for |
|---|---|---|---|
| P1 | IDEO.org Design Kit, "How Might We" | https://www.designkit.org/methods/how-might-we.html | HMW method |
| P2 | Nielsen Norman Group, "Using 'How Might We' Questions" | https://www.nngroup.com/articles/how-might-we-questions/ | HMW breadth/specificity guidance |
| P3 | Jeff Gothelf, Lean UX problem-statement template (via Gothelf's blog / Lean UX excerpts) | https://jeffgothelf.com/blog/creating-okrs-with-lean-ux-canvas/ ; https://syjia.medium.com/lean-ux-excerpt-e5ecc2a3df43 | Lean UX statement format |
| — | Upstream: `_requirements.md` [Brief], `03-user-research.md` [Skill03], `04-problem-research.md` [Skill04], `05-persona-research.md` [Skill05], `02-market-research.md` [Skill02], `literature-review.md` [L#] | local | All substantive claims (see those files' Source Indexes) |
