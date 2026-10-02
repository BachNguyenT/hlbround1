# Project Context — HLB Point-of-Purchase Credit Decisioning (working name)

Generated: 2026-09-28
Status: Active

---

## Elicitation Summary

### Product
**Name:** HLB Point-of-Purchase Credit Decisioning (working name — the final solution shape is decided in Skill 12)
**Type:** Other — a bank-internal credit decisioning capability owned and operated by Hong Leong Bank Vietnam, distributed B2B2C through merchant and partner checkouts (physical stores, e-commerce platforms, merchant apps). User confirmed this framing (recommended default, 2026-09-28).
**One-line description:** A capability that lets HLB Vietnam approve small-ticket instalment credit inside the purchase moment — for thin-file as well as bureau-visible applicants — at a cost per decision proportional to loan size, while holding risk quality, responsible-lending standards, fraud/identity controls and regulatory compliance.

### Primary Persona (Hypothesis)
**Who (user-confirmed persona set for Skill 05):**
1. Three borrower personas from the thin-file segments in Exhibit 1: a gig/platform worker, an online MSME merchant, and a first-time digitally active borrower.
2. One bank-side persona: HLB Vietnam credit-risk / underwriting lead (the internal operator and decision owner).
3. Optional: one merchant/partner persona (the checkout owner who embeds the offer).

**Context:** All drawn from the Business Challenge 2026 Ideation Round case brief (`Business Challenge 2026 _ Ideation Round Case _ AI in Finance and Banking.pdf`), distilled into `_requirements.md`.

### Problem Hypothesis
The brief bundles four coupled sub-problems:
1. **Thin-file creditworthiness** — bureau (CIC) + collateral assessment cannot score gig workers, online MSMEs and first-time borrowers even when they earn and transact digitally.
2. **Decision speed and unit cost** — manual centralised review costs 380,000 VND/application and takes 1.8–4.6 business days regardless of loan size; for small tickets this exceeds the loan's earnings and misses the purchase moment.
3. **Real-time identity and application-fraud verification** without document friction that drives abandonment.
4. **Targeting** — identifying which ecosystem customers are most likely to need, accept and responsibly use embedded credit.

User-confirmed approach: research all four; Skill 04 ranks them with evidence and names the core bottleneck. Working hypothesis (to be tested, not assumed): the manual, document-based review workflow is the tightly coupled decision point driving both (1) and (2).

### Target Market
**Geography:** Vietnam (HCMC and Hanoi presence), with other developing markets (India, Indonesia, Philippines, Kenya and similar) as analogues — stated in the brief ("considering the operating conditions of Vietnam and other developing markets").
**Segment/Niche:** All five inbound segments from Exhibit 2 (Salaried with credit 35.5%, Salaried no credit 20.5%, Gig/Platform 15%, Online MSME 14.5%, First-time borrowers 14.5%), weighted toward the three thin-file segments (44% of inbound). User-confirmed.

### Known Competitors
None named in the brief. Skill 02 researches the Vietnamese consumer-finance / BNPL / embedded-lending landscape (e.g. Home Credit, FE Credit, Fundiin, Kredivo, Atome, e-wallet pay-later products, bank BNPL programmes).

### Constraints
**Technical:** None mandated by the brief. AI permitted only where it shows clear incremental value versus the operational baseline; non-AI / hybrid options must stay in the solution space (user-confirmed).
**Regulatory:** Risk quality, responsible lending, and regulatory compliance are explicit requirements (Vietnam-specific regulation researched in Skill 02).
**Business:** Competition deliverable — the final submission is a one-page 1920×1080 (16:9) PDF scored against a rubric (Problem Diagnosis 20%, Intervention Justification 30%, Solution Concept 35%, Data & Assumptions 10%, Visualization 5%); brand assets: HLB logo, Noto Sans. This run is at full startup-product depth with findings tagged to rubric criteria; producing the one-pager itself is out of scope for this run. Deadline and team size not provided (optional; not asked further).
**Existing research:** Yes — the case brief itself (baseline operating parameters, segment table, application mix). Distilled in `_requirements.md`.

### Business Model Hypothesis
**Pricing:** Not a SaaS pricing question — HLB earns net interest margin and/or fees on instalment loans, potentially merchant discount/subsidy fees (researched in Skill 02; validated in Skill 09).
**Buyer vs. User:** HLB Vietnam builds and operates; borrowers apply and repay; merchants/partners host the offer and may co-fund. To be validated in Skill 05 / Skill 09.

### Additional Context
- User-confirmed scope for this run: **Discover phase (Skills 01–05) at full depth**, plus two additional deliverables:
  - `02-discover/literature-review.md` — standalone academic + industry literature review (alternative-data credit scoring, ML underwriting explainability/fairness, BNPL/embedded-credit outcomes, eKYC & application fraud, propensity targeting, responsible lending), Vietnam + developing-market analogues.
  - `04-design/12-solution-proposal.md` — a **preliminary** Skill 12-format solution-approaches document (3 scored options) grounded in Discover evidence only. Skills 06–11 remain pending; Skill 12 must be re-run once Define is complete.
- Findings in every deliverable are tagged to the competition rubric criteria they support (`[R1 Diagnosis]`, `[R2 Justification]`, `[R3 Concept]`, `[R4 Assumptions]`).
- The brief itself says "teams may make reasonable assumptions where information is unavailable, but all assumptions must be stated clearly" — consistent with the pipeline's zero-assumption mandate.

---

## Solution Direction (user-specified, 2026-09-30)

The user specified the solution mechanism after the Discover phase, and confirmed all recommended defaults for the resulting ambiguities. These decisions supersede the preliminary Skill 12 recommendation ("Lean Triage", non-AI), which is kept on record as a rejected or phase-alternative option.

**Mechanism.** A comprehensive AI-integrated platform built around one **central AI credit-profile engine** (working name set in Skill 12/13). The engine assesses bureau (CIC) and alternative data for each customer and produces an output that every stage of the point-of-purchase (PoP) customer journey uses.

| # | Decision | User-confirmed answer (recommended default) |
|---|---|---|
| SD-1 | Who the engine assesses | One engine with **two entry paths**. (a) Existing HLB customers are profiled proactively from bank-held data, which feeds Awareness and Retention targeting. (b) New or thin-file applicants are profiled at checkout from CIC plus consented partner data (platform income, e-wallet, telco, sales ledger). They are then onboarded to a current/savings (CASA) account so they become bank-observable over time, per the brief's own mitigation [Brief p.8]. |
| SD-2 | Journey stages | **Six stages:** Awareness → Consideration → Application & Instant Decision → Purchase & Repayment (the user's "decision" stage: continuous reassessment) → Retention → Advocacy. |
| SD-3 | How much is AI | **Hybrid engine:** machine-learning models, plus a policy and guardrail rule layer, plus a human "refer" band. Each AI component must state why it beats the baseline, as the brief requires [Brief pp.10–11]. Human oversight is required by Vietnam's AI Law for automated credit decisions [Skill02 RF-8]. |
| SD-4 | Engine output | A **Customer Credit Profile** with six fields: risk grade (A–E, probability-of-default band); affordability headroom (maximum safe monthly instalment, VND); evidence confidence (bureau / bank-observed / consented-partner / thin); propensity and need signals; identity and application-integrity score; decision tier (pre-approved / instant approve / refer / decline with reason and path). |
| SD-5 | Pipeline order | Run Skills 06–11 at full depth first. Then Skill 12 in abbreviated form (the user's approach is the Recommended Option), then Skill 13 (Solution Design), then Skill 15 (User Journey Map). Skill 14 (Technical Feasibility) is not run in this pass and is flagged as pending. |
| SD-6 | Advocacy ideas | (a) **Repayment-conditioned invite.** The invitee gets an instant eligibility check. The referrer is rewarded only after the invitee's on-time repayments, never for the borrowing itself, and no reference contacts are ever required. (b) **Refer-a-merchant.** (c) **"Good payer" credit passport**, as the bridge from Retention to Advocacy. |
| SD-7 | HLB data holdings | Only data evidenced in the brief or in sources is treated as confirmed: CASA and salary inflows, existing loans and cards, CIC, HLB Connect app, and the Sổ Bán Hàng sales ledger for SMEs [Brief pp.5–8; Skill01 S4]. Any other HLB data is labelled an assumption. |

**Pain-point scope for design (user instruction, 2026-09-30).** Features are designed first for the **user-suggested pain points**:
- **Awareness:** identify high-potential, low-risk customers who have never used PoP lending, for targeted marketing.
- **Consideration:** offer lending packages that differ by each customer's financial profile.
- **Purchase & Repayment:** continuously reassess and refine the profile from repayment history.
- **Retention:** recommend products with high PoP-purchase potential to suitable users.
- **Advocacy:** an innovative referral mechanism.

The **additional pain points proposed by Claude** (Skill 03 #5–#11 and #13–#16, mapped to stages on 2026-09-29) are held in a parked list, pending the user's assessment. The case-brief requirements R-1 to R-4 remain mandatory regardless. So the Application & Instant Decision stage is designed to the level R-2 and R-3 require (in-moment decision, real-time identity verification), and its finer pain-point features are parked.

**Tension on record (not a blocker).** Skill 04 §5 and the literature review found that a rules-based tiered flow captures much of the decision-stage value, and that AI's strongest evidenced edge is targeting (Awareness/Retention) and thin-file scoring precision. The design must therefore justify each AI component individually against the baseline, as rubric R2 requires [Brief p.11].

---

## Open Questions

| # | Question | Relevant Skill | Priority | Status |
|---|---------|---------------|----------|--------|
| OQ-01 | Which of the four bundled sub-problems is the core bottleneck with highest operational leverage? | Skill 04 → 06 | High | Open — research in Skill 04 |
| OQ-02 | Which alternative data sources can HLB lawfully access in Vietnam (telco, e-wallet, platform income, utility, e-commerce sales), under what consent model (PDP law, CIC rules, Open API rules)? | Skill 02, lit review | High | Open |
| OQ-03 | What approval rates, default rates, and loss rates apply per segment today? Brief gives only qualitative approval levels. | Skill 04 / 22 | High | Open — likely a stated assumption for the submission |
| OQ-04 | Pricing / revenue model per loan (interest rate, merchant subsidy/MDR, fees) and the break-even ticket size given 380,000 VND review cost | Skill 02 / 09 | High | Open |
| OQ-05 | Competition deadline, team size, and any preferred angle | Skill 08 / 21 | Low | Closed. User skipped this optional item (D-14); the PRD emphasises the rubric, AI track (user, 2026-09-30) |
| OQ-06 | Merchant/partner persona: include as a full persona in Skill 05 or keep optional? | Skill 05 | Low | Open — Skill 05 decides on evidence availability |

---

## Pipeline Status

| Stage | Skill | File | Status |
|-------|-------|------|--------|
| Foundation | 01 — Product Strategy | 01-foundation/01-product-strategy.md | Not Started |
| Discover | 02 — Market Research | 02-discover/02-market-research.md | Not Started |
| Discover | 03 — User Research | 02-discover/03-user-research.md | Not Started |
| Discover | 04 — Problem Research | 02-discover/04-problem-research.md | Not Started |
| Discover | 05 — Persona Research | 02-discover/05-persona-research.md | Not Started |
| Discover (extra) | Literature Review | 02-discover/literature-review.md | Not Started |
| Define | 06–11 | 03-define/ | Not Started (out of scope this run) |
| Design | 12 — Solution Proposal (preliminary) | 04-design/12-solution-proposal.md | Not Started |
| Design–Deliver | 13–23 | — | Not Started (out of scope this run) |

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-09-28 | Project initialised; user confirmed all recommended elicitation defaults | AI Pipeline |
| 2026-09-29 | Discover phase (Skills 01–05 and literature review) complete; preliminary Skill 12 written | AI Pipeline |
| 2026-10-02 | Added `07-deliver/creditpulse-solution-document.md` (+PDF): plain-language product document covering inputs, assessment, outputs and their use in each journey stage. Exported `13a-credit-assessment-engine.pdf` (Mermaid diagrams rendered) | AI Pipeline |
| 2026-10-01 | Added `04-design/13a-credit-assessment-engine.md`: the detailed core assessment-engine design (Skill 13 companion), linked from Skill 13 §1.1 and PRD §4.3. Final artifact renamed PRP → PRD (Product Requirements Documentation) in the pipeline and project | AI Pipeline |
| 2026-09-30 | Skills 06–21 complete; Skill 22 decisions D-01 to D-14 accepted at their recommended defaults by the user; PRD to emphasise the competition rubric (AI track) and research-backed strategic direction | AI Pipeline |
| 2026-09-30 | User specified the solution direction: central AI credit-profile engine across a six-stage PoP journey. Recommended defaults SD-1 to SD-7 confirmed. Pipeline continues with Skills 06–11, then 12 (abbreviated), 13 and 15. | AI Pipeline |
